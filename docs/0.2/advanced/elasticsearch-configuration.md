
# Elasticsearch Configuration

[[TOC]]

## Introduction

Elasticsearch is a distributed search and analytics engine designed for scalability and real-time data processing. This guide provides the necessary steps to configure and use Elasticsearch in the Unopim project.

---

## Configuration Steps

### 1. Configure Environment Variables

Add the following settings to your `.env` file:

```env
ELASTICSEARCH_ENABLED=true
ELASTICSEARCH_CONNECTION=default
ELASTICSEARCH_HOST=192.168.1.43:9200
ELASTICSEARCH_USER=
ELASTICSEARCH_PASS=
ELASTICSEARCH_API_KEY=
ELASTICSEARCH_CLOUD_ID=
ELASTICSEARCH_INDEX_PREFIX=unopim_testing
```

### 2. Cache Configuration

After updating the environment variables, run the following command to cache the configuration:

```bash
php artisan config:cache
```

---

## Indexing Commands

### Clear Indexes

This command clears all existing Elasticsearch indexes for the Unopim project.
 **Warning:** This will delete all indexed data. Use with caution.

```bash
php artisan unopim:elastic:clear
```

### Index Products

This command indexes all products in the database.
Use it to create or update the product index in Elasticsearch:

```bash
php artisan unopim:product:index
```

### Index Categories

This command indexes all categories in the database.
Use it to create or update the category index in Elasticsearch:

```bash
php artisan unopim:category:index
```

---
