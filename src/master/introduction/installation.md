# Installation



## Install Using Composer

To install UnoPim using Composer, use the following steps:

- Choose the directory where you want to install UnoPim. Open your terminal and navigate to this directory.

- If you have downloaded the zip file from the Git repository, extract the files into your desired directory and run the following command to set up the project:

    ```sh
    composer create-project
    ```

- Otherwise, to directly install UnoPim, run the following command in your terminal:

    ```sh
    composer create-project unopim/unopim
    ```

- Run the following command to complete the installation:

    ```sh
    php artisan unopim:install
    ```

    ::: warning
    During the installation process, if the **`.env`** file doesn't exist, the installer will prompt you to provide the necessary information.
    :::

    - Follow the prompts during the installation process to provide the following details:

    ```
    - Please Enter the APP URL :
    - Please Enter the Application Name :
    - Please select the default locale :
    - Please enter the default currency :
    - Please choose the Allowed Locales for your channels :
    - Please choose the Allowed Currencies for your channels :
    - Please select the Database Connection :
    - Please enter the Database Host :
    - Please enter the Database Port Number :
    - Please enter the Database Name :
    - Please enter the Database Prefix :
    - Please enter the Database Username :
    - Please enter the Database Password :
    ```

    - For Create your admin credentials:
    ```
    - Enter the Name of Admin User :
    - Enter the Email address of the Admin User :
    - Configure the Password for admin user :
    ```

## Install Using Docker

### Prerequisites

Make sure **Docker** and **Docker Compose** are installed:

Verify installation:

```bash
docker --version
docker-compose --version
```

---

### Setup Steps

1. **Clone the repository**:

   * HTTPS:

     ```bash
     git clone https://github.com/unopim/unopim.git
     ```
   * SSH:

     ```bash
     git clone git@github.com:unopim/unopim.git
     ```

2. **Enter the directory**:

   ```bash
   cd unopim
   ```

3. **Start the Docker containers**:

   ```bash
   docker-compose up -d
   ```

   This will pull the necessary images and set up the environment.

---

### Access the Services

* **Application** → `http://localhost:8000`
* **MySQL** → `localhost:3306`
* **PHPMyAdmin** (if enabled in `docker-compose.yml`) → `http://localhost:8080`

Verify running containers:

```bash
docker ps
```

---

### Notes

* If **MySQL is already running locally**, update the MySQL port in both `docker-compose.yml` and `.env`.
* After making changes, restart containers:

  ```bash
  docker-compose up -d
  ```
* To stop containers:

  ```bash
  docker-compose down
  ```
* To rebuild containers:

  ```bash
  docker-compose up --build -d
  ```




## Install Using GUI Installer

To install UnoPim using our GUI installer, you can follow any of the following methods:

### Method 1: Direct Installation

1. Choose the directory where you want to install UnoPim. Open your terminal and navigate to this directory.

2. Run the following command in your terminal to install UnoPim:
    ```sh
    composer create-project unopim/unopim
    ```

3. Configure your HTTP server to point to the `public/` directory of the project.

4. Open your browser and access:
    ```
    http://localhost/unopim/public/
    ```
    This will launch the UnoPim installer.

### Method 2: Using Downloaded Package

1. [Download UnoPim](https://unopim.com/download/) from our official repository.

2. Extract the contents of the downloaded package to your desired directory.

3. Navigate to the project root directory.

4. Run the following command:
    ```sh
    composer create
    ```

5. Configure your HTTP server to point to the `public/` directory of the project.

6. Open your browser and access:
    ```
    http://localhost/unopim/public/
    ```
    This will launch the UnoPim installer.

::: warning Important Prerequisites
Make sure your system meets these requirements:
- Composer is installed on your system
- PHP >= 8.2
- Required PHP extensions are enabled
- Proper directory permissions are set
:::

## Install on macOS

Follow these steps to install UnoPim on macOS:

### Prerequisites

1. **Update Homebrew**:
    To ensure you have the latest version of Homebrew, run the following command:
   ```sh
   brew update
   ```

2. **Install PHP**:
  To install PHP, run the following command:
   ```sh
   brew install php
   ```

3. **Install Node.js**:
   To install Node.js, run the following command:
   ```sh
   brew install node
   ```

4. **Install Composer**:
   To install Composer, run the following command:
   ```sh
   brew install composer
   ```

5. **Install MySQL**:
   To install MySQL, run the following command:
   ```sh
   brew install mysql
   ```

### Installation Steps

1. Choose the directory where you want to install UnoPim. Open your terminal and navigate to this directory.

2. **Install UnoPim** :
  - Run the following command in your terminal to install UnoPim:

     ```sh
     composer create-project unopim/unopim
     ```
  - Chnage directory to project root directory
    ```sh
     cd unopim
    ```
3. **Configure Environment(optional)** :
   - Copy the `.env.example` file to `.env`
   ```sh
   cp .env.example .env
   ```
   - Update the following configurations in `.env`:
     ```
     APP_URL=http://localhost:8000
     DB_CONNECTION=mysql
     DB_HOST=127.0.0.1
     DB_PORT=3306
     DB_DATABASE=unopim
     DB_USERNAME=root
     DB_PASSWORD=
     ```

4. **Run the installation command**:
   ```sh
   php artisan unopim:install
   ```

5. **Start the development server**:
   ```sh
   php artisan serve
   ```

Your UnoPim installation should now be accessible at `http://localhost:8000`.

::: tip
For a more production-like environment on macOS, you can use tools like Laravel Valet or Docker.
:::

::: warning Note
- Make sure your PHP version is 8.1 or higher and all required PHP extensions are installed.
- Make sure your Composer version is 2.0 or higher and mysql is installed.
:::

## Install Using Amazon Cloud AMI

Follow these steps to install UnoPim on Amazon Web Services (AWS) using an Amazon Machine Image (AMI):

### Installation Steps

**Step 1: Launch the EC2 Instance**

- [Launch an EC2 instance from the Unopim AMI via AWS Marketplace.](https://aws.amazon.com/marketplace/pp/prodview-fdyosdv7k3cgw)
- Wait until the instance status is “running”.

**Step 2: Access Your EC2 Instance**

- Set your PEM file permission:
    ```sh
    chmod 400 your-key-file.pem
    ```
- Connect to your instance:
    ```sh
    ssh -i your-key-file.pem ubuntu@your-instance-ip
    ```
    Replace `your-key-file.pem` and `your-instance-ip` with your actual key and public IP.

**Step 3: Run SSL Script**

- Make sure your domain’s A record points to your EC2 Elastic IP.
- (If using Cloudflare or a similar proxy, disable proxy before running the script.)
- Run the SSL configuration:
    ```sh
    sudo bash /root/ssl_configuration.sh
    ```
    This sets up Let's Encrypt SSL and configures Apache for HTTPS.

**Step 4: Complete Unopim Installation Through the Web Interface**

- Visit `https://yourdomain.com/` in your browser.
- Click **“Continue”** on the setup screen.
- Follow the on-screen steps:
    - **System Requirements:** Review and continue.
    - **Database Setup:** Use credentials found on your server:
        ```sh
        cat /var/www/html/unopim/mysql_password.txt
        ```
    - **Start Installation:** Click the button to install Unopim.
    - **Domain URL:** Enter your domain (e.g., `https://yourdomain.com`).
    - **Set Defaults:** Timezone, locale, currency, and allowed options.
    - **Admin Setup:** Create admin credentials and set timezone/locale.

- After setup, log in to the Unopim Admin Panel.

**Security Step:**
Delete the credentials file after setup:
```sh
sudo rm /var/www/html/unopim/mysql_password.txt
```


### Cloud Installation via Amazon AMI

You can quickly launch UnoPim on the cloud using our official Amazon Machine Image (AMI): [Launch on Cloud](https://aws.amazon.com/marketplace/pp/prodview-fdyosdv7k3cgw)


## Start Using UnoPim

### On a Production Server

To access UnoPim on a production server, open your domain in a web browser. For example:

```
https://example.com/
```

### On Your Local Server

To access UnoPim on your local server, follow these steps:

1. Configure your HTTP server to point to the **`public/`** directory of the project.
2. Run the following command:

    ```sh
    php artisan serve
    ```

3. Open your browser and access the provided local server URL.


## Configure the Virtual Host

Depending on your web server, you can configure UnoPim with either **Apache 2** or **NGINX**.

---

### Configure Using Apache 2

#### Creating the Virtual Host File

Create the file **`/etc/apache2/sites-available/unopim.local.conf`**:

```apache
<VirtualHost *:80>
    ServerName unopim.local

    DocumentRoot /path/to/installation/unopim/public
    <Directory /path/to/installation/unopim/public>
        Options FollowSymLinks MultiViews
        AllowOverride All
        Require all granted
    </Directory>

    ReWriteEngine On

    <FilesMatch \.php$>
        SetHandler "proxy:unix:/run/php/php8.2-fpm.sock|fcgi://localhost/"
    </FilesMatch>

    ErrorLog ${APACHE_LOG_DIR}/unopim_error.log
    LogLevel warn
    CustomLog ${APACHE_LOG_DIR}/unopim_access.log combined
</VirtualHost>
```

**Notes**:

* Replace **`/path/to/installation`** with the actual path where UnoPim is installed.
* Ensure that **`/run/php/php8.2-fpm.sock`** matches the socket path defined in **`/etc/php/8.2/fpm/pool.d/www.conf`**.

#### Enabling the Virtual Host

```bash
sudo apache2ctl configtest   # should return "Syntax OK"
sudo a2ensite unopim.local
sudo service apache2 reload
```

#### Adding the Virtual Host Name

Edit your **`/etc/hosts`** file:

```
127.0.0.1    unopim.local
```

Now open **`http://unopim.local`** in your browser to access UnoPim.

---

### Configure Using NGINX

#### Creating the Virtual Host File

Create the file **`/etc/nginx/sites-available/unopim.local.conf`**:

```nginx
server {
    listen 80;
    server_name unopim.local;   # Replace with your dev domain or server IP

    root /home/unopim/html/unopim/public;   # UnoPim document root
    index index.php index.html index.htm;

    # Handle static files (CSS, JS, images)
    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    # Handle PHP requests
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php8.2-fpm.sock;   # Adjust PHP-FPM socket if needed
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    # Deny access to hidden files like .htaccess
    location ~ /\.ht {
        deny all;
    }

    error_log /var/log/nginx/unopim_error.log;
    access_log /var/log/nginx/unopim_access.log;
}
```

**Notes**:

* Replace **`/home/unopim/html/unopim/public`** with your actual UnoPim installation path if different.
* Make sure PHP-FPM is running (`php8.2-fpm` by default).

---

#### Enabling the Virtual Host

```bash
sudo ln -s /etc/nginx/sites-available/unopim.local.conf /etc/nginx/sites-enabled/
sudo rm -f /etc/nginx/sites-enabled/default   # remove default config
sudo nginx -t   # test config
sudo systemctl reload nginx
```

---

#### Adding the Virtual Host Name

Add this entry to your **`/etc/hosts`** file on your server (and local machine if testing from there):

```
127.0.0.1    unopim.local
```

---


Now open **`http://unopim.local`** in your browser to access UnoPim.
