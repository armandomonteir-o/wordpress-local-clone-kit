# 🚀 WordPress Local Clone Kit

![Docker Version](https://img.shields.io/badge/docker-20.10+-blue.svg)
![WordPress Version](https://img.shields.io/badge/wordpress-legacy%20%26%20latest-brightgreen.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

**A high-performance boilerplate to clone WordPress production environments to your local machine using Docker, focused on large-scale migrations.**

---

_[Read in Portuguese / Leia em Português](README-PT.md)_

---

## The Problem

While working as a Full Stack Software Developer at Conversion, I constantly needed to clone clients' WordPress environments locally. What should be a straightforward process turned into a set of repetitive, time-consuming, and error-prone tasks. The recurring problems were:

- **Import bugs:** Recent versions of the popular All-in-One WP Migration plugin often failed, with uploads stalling at 100% or the import simply not completing.
- **Upload limits:** Local environments usually have low default upload limits, making it impossible to import multi-gigabyte backups common for e-commerce and large portals.
- **Version mismatches:** Small differences in PHP or database versions between production and local environments often produced hard-to-debug issues.
- **Paywalled features:** Migration tools moved crucial restore features to paid tiers, blocking critical workflows behind paywalls.
- **File permission headaches:** The interaction between the host filesystem and Docker volumes frequently caused permission issues that prevented plugins from activating or functioning correctly.

After repeatedly encountering these issues, I created a reusable template: a clean, pre-configured environment that addresses these problems and allows clones to be produced in minutes instead of hours.

## The Solution

The WordPress Local Clone Kit is a template repository that provides a tested and repeatable workflow to clone WordPress sites locally. The idea was to create a "batteries-included" solution that directly tackles the pain points of the process, offering not just the files but a clear guide to transform an arduous and unpredictable process into a fast and reliable workflow.

This kit was built to be the definitive solution to the problems I faced, consolidating all the fixes and best practices into a single place.

## Key Features

1. **Pre-configured Docker environment:** `docker-compose.yml` (template and example) orchestrates WordPress, Database and phpMyAdmin, ready to be adapted to exact production versions.
2. **PHP configuration for large imports:** `uploads.ini` raises upload, memory and execution limits to allow importing backups of tens of gigabytes.
3. **Functional migration plugin included:** A legacy version (6.77) of All-in-One WP Migration is included and pre-installed to provide full import/restore functionality without paywalls.
4. **Clean debug setup:** A customized `wp-config.php` that disables on-screen error display and logs errors to `wp-content/debug.log` for safe debugging.
5. **Clear permission instructions:** The README contains specific commands for Linux users to ensure file ownership works correctly with the webserver user.

## Essential Cloning Workflow

Follow these steps to clone a WordPress site with this kit.

## Prerequisites

Before you begin, make sure you have the following items:

- Docker and Docker Compose installed and running on your machine.
- A `.wpress` backup file from the production site.

How to obtain it: To generate the backup file, install the All-in-One WP Migration plugin on the production site, go to Export > Export To > File and download the `.wpress` file.

### Step 1: Audit the Production Environment (Crucial)

To create a faithful clone, make sure your local environment mirrors production.

1. Log in to the site's WordPress admin.
2. Go to **Tools > Site Health > Info**.
3. Record the following versions:
   - WordPress version
   - PHP version (Server > PHP Version)
   - Database server version (e.g., MySQL 8.0, MariaDB 10.6)

### Step 2: Configure Your Local Environment

1. Copy `docker-compose-template.yml` to `docker-compose.yml`.
2. Edit `docker-compose.yml` and replace placeholders with the versions you recorded.

### Step 3: Start the Environment and Fix Permissions

1. Start containers:

```bash
docker-compose up -d
```

2. After the first run, WordPress will create files inside the `wp_files` folder. Fix permissions as needed.

#### For Linux users (Ubuntu, Debian, etc.)

It's crucial to give ownership of the files to the webserver user. This step is required.

```bash
sudo chown -R www-data:www-data ./wp_files/
```

#### For Windows and macOS (Docker Desktop)

You can skip the chown step—Docker Desktop manages volume permissions. Do NOT run `chown` on these platforms.

### Step 4: Install WordPress and Import

1. Visit http://localhost:8080 and complete the WordPress installation (create a temporary admin user).
2. In the admin, go to **Plugins** and activate the bundled All-in-One WP Migration.
3. Use **All-in-One WP Migration > Import** to import the `.wpress` backup from the client.

### Step 5: Final Sync

After importing, internal links and permalinks may return 404 errors—this is expected.

1. Log back into http://localhost:8080/wp-admin (credentials will match the client site after import).
2. Go to **Settings > Permalinks** and click **Save Changes** twice (do not change settings).

Your site should now be cloned and functional.

## Technologies Used

The kit focuses on environment parity, robustness and portability.

- **Docker and Docker Compose**: Builds isolated, consistent development environments across OSes.
- **Official WordPress image**: Allows matching WordPress and PHP versions.
- **Official MySQL image**: Allows aligning database server versions with production.
- **phpMyAdmin**: Included for convenient visual DB management.

## Troubleshooting

- To fully reset the environment and start fresh:

```bash
docker-compose down -v
sudo rm -rf ./wp_files/*
```

- If you see `Container name is already in use`, run `docker-compose down` in the other project or change container names in your `docker-compose.yml`.

- If port 3306 is in use, change the mapped port in `docker-compose.yml` (for example `3307:3306`).

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

## Contributor

Project created and developed by Armando Monteiro.

<a href="https://github.com/armandomonteir-o">
    <img src="https://avatars.githubusercontent.com/u/141039211?v=4" width="75" height="75">
</a>
