# ta24bblog

A Laravel 13 blog project. This guide sets up the application from a fresh computer using SQLite and Bun. The recommended Windows workflow uses Ubuntu in WSL 2, which keeps PHP, Composer, and Bun in the same environment.

## 1. Install the required tools

Install [Visual Studio Code](https://code.visualstudio.com/download) and [Git](https://git-scm.com/downloads). On Windows, also install the [WSL extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-wsl). Install Ubuntu in WSL 2 from an administrator PowerShell window, then restart Windows if prompted:

```powershell
wsl --install -d Ubuntu
```

Open Ubuntu from the Start menu or run `wsl -d Ubuntu` in PowerShell. Create your Linux username and password when prompted. Run the remaining setup commands in the Ubuntu terminal. Install PHP, the PHP extensions needed by Laravel and SQLite, Composer, and Git:

```bash
sudo apt update
sudo apt install git php-cli php-mbstring php-xml php-curl php-sqlite3 php-zip unzip curl composer
php -v
composer --version
git --version
```

Install Bun in Ubuntu and load its updated PATH:

```bash
curl -fsSL https://bun.com/install | bash
source ~/.bashrc
bun --version
```

If you use native Windows PHP instead of WSL, install [PHP](https://windows.php.net/download/), [Composer](https://getcomposer.org/download/), and [Bun](https://bun.sh/docs/installation) for Windows. Make sure `php`, `composer`, `bun`, and `git` work in a new PowerShell terminal. Keep all project commands in one environment; do not run Windows PHP against a WSL project.

### PHP extensions and `php.ini`

Laravel requires `mbstring`, `curl`, `fileinfo`, `openssl`, `pdo`, and XML support. This project also uses SQLite, so it needs `pdo_sqlite` and `sqlite3`; `zip` is useful for Composer. The Ubuntu packages above enable the required extensions automatically. Check them with:

```bash
php -m
```

For native Windows PHP, locate the active configuration file with `php --ini`, open that `php.ini`, and remove the leading semicolon from these existing lines:

```ini
extension=curl
extension=fileinfo
extension=mbstring
extension=openssl
extension=pdo_sqlite
extension=sqlite3
extension=zip
```

Save `php.ini`, open a new terminal, and run `php -m` again. The extension DLLs must exist in PHP's `ext` directory. If Windows App Control blocks a DLL, use the WSL setup above or resolve the Windows policy; changing `php.ini` alone cannot override the block.

## 2. Clone the repository

In Ubuntu, keep the project in your Linux home directory for better WSL performance:

```bash
cd ~
git clone https://github.com/qwlsx/ta24bblog.git
cd ta24bblog
```

Open the project in VS Code from this Ubuntu terminal:

```bash
code .
```

The VS Code window should show `WSL: Ubuntu`. Use its Ubuntu terminal for all remaining commands.

## 3. Install project dependencies

From the `ta24bblog` directory, install the PHP packages from `composer.lock` and the frontend packages from `bun.lock`:

```bash
composer install
bun install
```

Do not commit the generated `vendor` or `node_modules` directories.

## 4. Configure the environment and database

Create your local `.env` file from the tracked example and generate a unique application key:

```bash
cp .env.example .env
php artisan key:generate
```

The example configuration uses SQLite (`DB_CONNECTION=sqlite`). Create the database file, then run the migrations:

```bash
touch database/database.sqlite
php artisan migrate
```

Do not commit `.env`: it contains local configuration and the application key.

## 5. Start the application

```bash
composer run dev
```

Keep that terminal open and visit [http://localhost:8000](http://localhost:8000) in your browser. You should see the Laravel welcome page. Press `Ctrl+C` in the terminal to stop the development processes.

If the page does not load, check the output of `composer run dev`, confirm you ran the commands inside `mm24blog`, and verify that `php -m` lists `mbstring` and `pdo_sqlite`.
