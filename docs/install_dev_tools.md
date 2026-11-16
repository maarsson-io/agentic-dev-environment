# Install Development Tools

Install the development tools inside the Ubuntu VM. All commands in this section are executed in the VM unless stated otherwise.

## System Packages

Update the package index and install essential command-line utilities:

```bash
sudo apt update
sudo apt install -y \
  git \
  curl \
  wget \
  unzip \
  zip \
  ca-certificates \
  build-essential
```

Verify Git:

```bash
git --version
```

> [!IMPORTANT]
> Git is installed for local repository operations required by development tools. Commits, pushes, and pull requests remain under human control and are performed on the macOS host.

## Install PHP and Composer

Choose the PHP installation method corresponding to your Ubuntu version.

### Option A: Ubuntu 24.04 LTS — PHP 8.4

Install PHP 8.4 using the Ondřej Surý PHP repository, which is also used by the reference Laravel Forge environment.

```bash
sudo apt install -y software-properties-common
sudo add-apt-repository -y ppa:ondrej/php
sudo apt update
```

Install PHP and the extensions commonly required for Laravel development:

```bash
sudo apt install -y \
  php8.4-cli \
  php8.4-mbstring \
  php8.4-xml \
  php8.4-curl \
  php8.4-zip \
  php8.4-mysql \
  php8.4-bcmath \
  php8.4-intl
```

### Option B: Ubuntu 26.04 LTS — PHP 8.5

Install PHP 8.5 directly from the official Ubuntu repositories:

```bash
sudo apt install -y \
  php8.5-cli \
  php8.5-mbstring \
  php8.5-xml \
  php8.5-curl \
  php8.5-zip \
  php8.5-mysql \
  php8.5-bcmath \
  php8.5-intl
```

### Install Composer

Both Ubuntu versions provide Composer through their package repositories:

```bash
sudo apt install -y composer
```

Verify the installation:

```bash
php -v
composer --version
php -m
```

> [!NOTE]
> The PHP version used inside the VM does not have to match the production environment, but matching versions reduces the risk of compatibility differences. Verify each project's PHP and extension requirements before running its dependencies.

## Install Node.js

Install Node.js 22 LTS using [nvm](https://github.com/nvm-sh/nvm) (Node Version Manager).

Using nvm keeps Node.js independent of Ubuntu's system packages and allows different Node.js versions to be installed when required by individual projects.

Install nvm:

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
```

Load nvm into the current shell:

```bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

Install Node.js 22 and set it as the default:

```bash
nvm install 22
nvm alias default 22
```

Verify the installation:

```bash
node --version
npm --version
nvm --version
```

> [!NOTE]
> nvm installs Node.js for the current Linux user without requiring `sudo`. Individual projects may specify their own Node.js version using an `.nvmrc` file.

## Install MySQL

Install MySQL Server from the official Ubuntu repositories:

```bash
sudo apt install -y mysql-server
```

The installed MySQL version depends on the Ubuntu release:

| Ubuntu version | MySQL version |
|---|---|
| 24.04 LTS | 8.0 |
| 26.04 LTS | 8.4 |

Enable and start the service:

```bash
sudo systemctl enable --now mysql
```

Verify the installation:

```bash
mysql --version
sudo systemctl is-active mysql
```

Expected service status: `active`.

### Verify Local Access

Connect using Ubuntu's default administrative authentication:

```bash
sudo mysql
```

Inside the MySQL shell:

```sql
SELECT VERSION();
EXIT;
```

### Verify Network Binding

Check which network interfaces MySQL listens on:

```bash
sudo ss -lntp | grep ':3306'
```

MySQL should listen only on `127.0.0.1:3306` or another explicitly authorized local interface.

> [!IMPORTANT]
> Do not expose MySQL to external networks. Development databases should remain accessible only inside the VM unless access is explicitly required and secured.

### Create Per-Project Database and User

Each project should have its own MySQL database and user, with permissions restricted to that database. Perform this step whenever a new project is added to the development environment. It is not required during the initial VM setup.

Open the MySQL shell:

```bash
sudo mysql
```

Create a database and a dedicated user (replace `project_name` and the password with project-specific values):

```sql
CREATE DATABASE project_name
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

CREATE USER 'project_name'@'localhost'
  IDENTIFIED BY 'UNIQUE_STRONG_PASSWORD';

GRANT ALL PRIVILEGES ON project_name.*
  TO 'project_name'@'localhost';
```

Verify the permissions:

```sql
SHOW GRANTS FOR 'project_name'@'localhost';
```

Exit MySQL:

```sql
EXIT;
```

Configure the project's `.env` file:

```dotenv
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=project_name
DB_USERNAME=project_name
DB_PASSWORD=UNIQUE_STRONG_PASSWORD
```

> [!NOTE]
> These permissions allow Laravel migrations and normal application database operations, but do not grant access to other project databases. Use unique credentials for each project.

> Database-level permissions are a useful restriction, but they do not isolate projects from an agent that can access other projects' files or execute privileged system commands.

### Optional: Create a Global Development User

Optionally create a MySQL user for database administration through SSH-tunneled connections from the macOS host, without using the `root` account.

Open the MySQL shell:

```bash
sudo mysql
```

Create the user with a strong, unique password:

```sql
CREATE USER 'dev'@'localhost' IDENTIFIED BY 'YOUR_STRONG_PASSWORD';
GRANT ALL PRIVILEGES ON *.* TO 'dev'@'localhost';
```

Exit the MySQL shell:

```sql
EXIT;
```

Verify password-based authentication:

```bash
mysql -u dev -p -h 127.0.0.1
```

> [!CAUTION]
> Creating a global `dev` user is an **optional convenience**, primarily intended for database administration from host applications such as Sequel Ace or TablePro over SSH.
>
> This user has broad privileges across all databases. If its credentials are compromised or exposed to an AI agent, every development database may be affected.
>
> **For application connections, use a separate MySQL user for each project**, granting privileges only on that project's database. Do not use the global `dev` credentials in project `.env` files.
>
> Project-specific database permissions reduce accidental cross-project access but do not provide complete isolation, particularly when the agent can execute commands with elevated privileges.

## Install Redis

Install Redis Server from the official Ubuntu repositories:

```bash
sudo apt install -y redis-server
```

Enable and start the service:

```bash
sudo systemctl enable --now redis-server
```

Verify the installation:

```bash
redis-server --version
sudo systemctl is-active redis-server
redis-cli ping
```

Expected service status: `active`.

Expected Redis response: `PONG`.

### Verify Network Binding

Check which interfaces Redis listens on:

```bash
sudo ss -lntp | grep ':6379'
```

Redis should listen only on the VM's loopback interfaces, typically `127.0.0.1:6379` and, if IPv6 is enabled, `[::1]:6379`.

> [!IMPORTANT]
> Redis must not be exposed to external networks. Keep it accessible only from within the VM. No UTM port forwarding is required.
