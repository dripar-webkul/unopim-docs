# Implementing Data Import

[[TOC]]

The data import functionality allows you to integrate and process external data into your application. In UnoPim, custom data imports can be achieved by creating importer classes that validate, process, and store incoming data. This guide walks you through the steps required to implement a custom data importer.

---

## Overview of the Steps

1. **Create Importer File**: Create a dedicated importer file where the import logic will reside.
2. **Implement Importer Logic**: Define how the data is validated and processed.
3. **Register the Importer**: Configure UnoPim to recognize and utilize your custom importer.

---

## Step 1: Create the Importer File

To begin, create the directory structure for your import logic under the `Helpers/Importers` directory within your plugin. The structure should look like this:

```
└── packages
    └── Webkul
        └── Example
            ├── ...
            └── src
                └── Helpers
                    └── Importers
                        ├── ...
                        └── FileName
                            └── Importer.php
```

### Example:

- **Helpers/Importers**: This is the folder where all import logic for the plugin will reside.
- **Importer.php**: This file will contain the importer logic for the specific data type you're handling (e.g., products, tax rates).

---

## Step 2: Implement the Importer Logic

Once the `Importer.php` file is created, implement the logic to handle data validation and batch processing. The following is an example of how an importer can be structured for handling product imports, including validation and batch processing:

### Example Importer Logic:

```php
<?php

namespace Webkul\Example\Helpers\Importers\Product;

use Illuminate\Support\Arr;
use Illuminate\Support\Facades\Validator;
use Webkul\DataTransfer\Helpers\Import;
use Webkul\DataTransfer\Helpers\Importers\AbstractImporter;

class Importer extends AbstractImporter
{
    protected array $validColumnNames = [
        'identifier', 'is_zip_range', 'zip_code',
        'zip_from', 'zip_to', 'state', 'country', 'tax_rate',
    ];

    protected array $messages = [
        self::ERROR_IDENTIFIER_NOT_FOUND_FOR_DELETE => 'data_transfer::app.importers.tax-rates.validation.errors.identifier-not-found',
        self::ERROR_DUPLICATE_IDENTIFIER => 'data_transfer::app.importers.tax-rates.validation.errors.duplicate-identifier',
    ];

    protected $permanentAttributes = ['identifier'];
    protected array $identifiers = [];

    /**
     * Validate the incoming data row by row
     *
     * @param array $rowData
     * @param int $rowNumber
     * @return bool
     */
    public function validateRow(array $rowData, int $rowNumber): bool
    {
        if ($this->import->action == Import::ACTION_DELETE) {
            if (! $this->isIdentifierExist($rowData['identifier'])) {
                $this->skipRow($rowNumber, self::ERROR_IDENTIFIER_NOT_FOUND_FOR_DELETE);
                return false;
            }
            return true;
        }

        $validator = Validator::make($rowData, [
            'identifier' => 'required|string',
            'country'    => 'required|string',
            'tax_rate'   => 'required|numeric|min:0.0001',
        ]);

        if ($validator->fails()) {
            foreach ($validator->errors()->getMessages() as $attributeCode => $message) {
                $this->skipRow($rowNumber, current($message), $attributeCode);
            }
        }

        return ! $this->errorHelper->isRowInvalid($rowNumber);
    }

    /**
     * Process the import in batches
     *
     * @param ProductContract $batch
     * @return bool
     */
    public function importBatch(ProductContract $batch): bool
    {
        if ($batch->import->action == Import::ACTION_DELETE) {
            $this->deleteTaxRates($batch);
        } else {
            $this->saveTaxRatesData($batch);
        }

        $batch = $this->importBatchRepository->update([
            'state'   => Import::STATE_PROCESSED,
            'summary' => [
                'created' => $this->getCreatedItemsCount(),
                'updated' => $this->getUpdatedItemsCount(),
                'deleted' => $this->getDeletedItemsCount(),
            ],
        ], $batch->id);

        return true;
    }

    /**
     * Check if an identifier exists
     *
     * @param string $identifier
     * @return bool
     */
    public function isIdentifierExist(string $identifier): bool
    {
        return $this->taxRateStorage->has($identifier);
    }
}
```

### Key Functions Explained:

1. **`validateRow()`**:
    - This method validates each row of data to ensure it meets specific criteria (e.g., required fields, valid data types).
    - If the action is delete, it checks if the identifier exists in the system before deleting.
    - Uses Laravel's `Validator` to enforce field rules such as required values, valid numeric ranges, etc.

2. **`importBatch()`**:
    - This method processes the batch of data, either inserting new data, updating existing data, or deleting based on the action.
    - Handles the creation, updating, and deletion of records and generates a summary of what was processed.

3. **`isIdentifierExist()`**:
    - This helper function checks if a specific identifier exists in the system, which is important for actions like deletion.

---

## Step 3: Register the Importer

Now that the importer is created and the logic is defined, you need to register the importer so that UnoPim recognizes and can use it.

### Step 1: Create `importer.php`

In the `Config` directory of your plugin, create a new configuration file named `importer.php`. This file will contain the configuration for your importers.

Directory structure:

```
└── packages
    └── Webkul
        └── Example
            ├── ...
            └── src
                └── Config
                    └── importer.php
```

### Step 2: Define the Importer Configuration

In the `importer.php` file, define the configuration for your importer, specifying the importer class and other important settings like the title and sample file path.

```php
<?php

return [
    'products' => [
        'title'       => 'data_transfer::app.importers.products.title',  // Display title for the importer
        'importer'    => 'Webkul\Example\Helpers\Importers\Product\Importer',  // Importer class
        'sample_path' => 'data-transfer/samples/products.csv',  // Path to a sample CSV file for users
    ],
];
```

### Step 3: Load the Configuration in the Service Provider

To make sure the configuration is loaded into the system, register it in your service provider using the `mergeConfigFrom` method. Add the following code to the `register()` method in your `ExampleServiceProvider`:


```php
public function register()
{
    $this->mergeConfigFrom(
        dirname(__DIR__) . '/Config/importer.php', 'importers'
    );
}
```

This ensures that the `importer.php` configuration is merged into the system, allowing UnoPim to recognize the importer.

## Step 4: Queue Operations

After setting up your importer, you need to configure and manage the queue system for processing imports. This is crucial for handling background tasks efficiently.

### Managing Queue Workers

When you make changes to any Importer class or its configurations, follow these steps:

```bash
# Restart the queue worker
php artisan queue:restart

# Start the queue worker again
php artisan queue:work
```

### Running Specific Import Jobs

To run a specific import job for a particular user:

```bash
# Format: php artisan unopim:queue:work [import_id] [user_email]
php artisan unopim:queue:work 1 johndoe@example.com
```

Parameters explained:
- `1`: The ID of the import job you want to process
- `johndoe@example.com`: The email of the logged-in user who initiated the import

::: warning Important
Always restart the queue workers after:
- Modifying Importer classes
- Updating configurations
- Installing or updating modules

This ensures your changes take effect in the queue system.
:::

# Job Validators and Filters for Import

This guide explains how to implement job validators and filters for import operations in UnoPim.

## Job Validators

Job validators ensure that import configurations and data meet required specifications before processing.

### Creating a Base Job Validator

First, create a base validator class that other validators can extend:

```php
<?php

namespace Webkul\Example\Validators\JobInstances\Default;

use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\ValidationException;

class JobValidator implements JobValidatorContract
{
    /**
     * Base validation rules
     */
    protected array $rules = [];

    /**
     * Custom error messages
     */
    protected array $messages = [];

    /**
     * Custom attribute names
     */
    protected array $attributeNames = [];

    /**
     * Validate the job data
     */
    public function validate(array $data, array $options = []): void
    {
        $data = $this->preValidationProcess($data);

        $validator = Validator::make(
            $data,
            $this->getRules($options),
            $this->getMessages($options),
            $this->getAttributeNames($options)
        );

        if ($validator->fails()) {
            throw ValidationException::withMessages(
                $this->processErrorMessages($validator)
            );
        }
    }
}
```

### Creating a Specific Import Validator

Create validators for specific import types by extending the base validator:

```php
<?php
namespace Webkul\Example\Validators\JobInstances\Import;

use Webkul\Example\Validators\JobInstances\Default\JobValidator;

class ProductImportValidator extends JobValidator
{
    protected array $rules = [
        'file' => [
            'required',
            'mimes:csv,xlsx',
        ],
        'batch_size' => 'required|integer|min:1',
        'field_separator' => 'required|in:,,;,tab',
        'validation_strategy' => 'required|in:stop-on-errors,skip-errors',
        'allowed_errors' => 'required|integer|min:0'
    ];

    protected array $attributeNames = [
        'file' => 'Import File',
        'batch_size' => 'Batch Size',
        'field_separator' => 'Field Separator',
        'validation_strategy' => 'Validation Strategy',
        'allowed_errors' => 'Allowed Errors'
    ];

    public function getMessages(array $options): array
    {
        return [
            'file.required' => 'Please select a file to import',
            'file.mimes' => 'File must be a CSV or Excel document'
        ];
    }
}
```

## Import Data Filters

Filters help clean and transform import data before processing.

### Creating a Data Filter

```php
// filepath: /packages/Webkul/Example/src/Filters/ImportDataFilter.php
namespace Webkul\Example\Filters;

class ImportDataFilter
{
    /**
     * Filter and transform import data
     */
    public function filter(array $data): array
    {
        return array_map(function ($row) {
            return [
                'id' => trim($row['id'] ?? ''),
                'name' => ucfirst(strtolower($row['name'] ?? '')),
                'sku' => strtoupper($row['sku'] ?? ''),
                'status' => $this->normalizeStatus($row['status'] ?? ''),
                'price' => (float) ($row['price'] ?? 0),
                'created_at' => now()->toDateTimeString()
            ];
        }, $data);
    }

    /**
     * Normalize status values
     */
    private function normalizeStatus(string $status): string
    {
        return in_array(strtolower($status), ['active', 'inactive'])
            ? strtolower($status)
            : 'inactive';
    }
}
```

### Usage Example

Here's how to use validators and filters in your import process:

```php
<?php

namespace Webkul\Example\Helpers;

use Webkul\Example\Validators\JobInstances\Import\ProductImportValidator;
use Webkul\Example\Filters\ImportDataFilter;

class ImportManager
{
    public function __construct(
        private ProductImportValidator $validator,
        private ImportDataFilter $filter
    ) {}

    public function processImport(array $config, array $data): bool
    {
        $this->validator->validate($config);

        $filteredData = $this->filter->filter($data);

        return $this->processFilteredData($filteredData);
    }
}
```

### Register in Service Provider

```php
public function register()
{
    $this->app->bind(ProductImportValidator::class);
    $this->app->bind(ImportDataFilter::class);
}
```

## Key Features

1. **Validation Rules**:
   - File type restrictions
   - Required fields
   - Data type validation
   - Custom validation rules

2. **Data Filtering**:
   - Data cleaning
   - Value normalization
   - Type casting
   - Default value handling

3. **Error Handling**:
   - Detailed error messages
   - Custom attribute names
   - Batch error collection

## Best Practices

1. Always validate import configurations before processing
2. Use filters to clean and normalize data
3. Implement proper error handling
4. Create specific validators for different import types
5. Add logging for debugging
6. Write unit tests for validators and filters

This structure ensures data quality and consistency in your import operations while maintaining clean, maintainable code.

