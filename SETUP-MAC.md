# Running Covid19-TMS on macOS (Apple Silicon) with MAMP

Tested on a MacBook Air (Apple Silicon) with MAMP 7.5 and PHP 8.3.

## 1. Install MAMP

1. Download the free **MAMP** from https://www.mamp.info/en/downloads/ (choose the ARM build for Apple Silicon).
2. Install it and open it from Applications. If macOS blocks it, go to System Settings > Privacy & Security > **Open Anyway**.
3. Click **Start** and wait for Apache and MySQL to turn green.

Ports used on this setup (check yours in MAMP > Preferences > Ports):
- Apache: **8888**
- MySQL: **8889**

## 2. Get the application

Download the ZIP:
https://github.com/swapniltake1/SPPU-BE-COMPUTER-ENGINERING-STQA-MINIPROJECT-/archive/refs/heads/main.zip

Unzip it, then copy the website folder into MAMP's web root:

```bash
cp -R ~/Downloads/SPPU-BE-COMPUTER-ENGINERING-STQA-MINIPROJECT--main/"Covid19-TMS Project Using PHP and MySQL" /Applications/MAMP/htdocs/covid19tms
```

## 3. Create and import the database

1. Open http://localhost:8888/phpmyadmin (login `root` / `root` if asked).
2. Click **New**, name the database `covidtmsdb`, click **Create**.
3. Select `covidtmsdb`, open the **Import** tab, and choose:
   `/Applications/MAMP/htdocs/covid19tms/SQL File/covidtmsdb.sql`
   (in the file picker press Cmd + Shift + G and paste the path)
4. Click **Import**. Five tables should appear.

The "Integer display width is deprecated" warnings during import are harmless.

## 4. Fix the database connection

The app connects through `covid-tms/includes/config.php`. The original line expects MySQL on port 3308 with an empty password. MAMP uses port 8889 and password `root`. Change line 5 to:

```php
$con=mysqli_connect("127.0.0.1","root","root","covidtmsdb",8889);
```

One-line version:

```bash
sed -i '' '5s/.*/$con=mysqli_connect("127.0.0.1","root","root","covidtmsdb",8889);/' /Applications/MAMP/htdocs/covid19tms/covid-tms/includes/config.php
```

## 5. Open the application

- Public site: http://localhost:8888/covid19tms/covid-tms/
- Admin login: username `admin` (the password is stored as an MD5 hash in `tbladmin`; the project default is commonly `Test@123`)

## Problems we hit and how they were solved

| Problem | Cause | Fix |
|---|---|---|
| `brew: command not found` | Homebrew not installed | Used MAMP instead |
| Blank page | PHP fatal error "Connection refused" in `config.php` | Wrong MySQL port (8898 vs the real 8889). Checked the port shown in phpMyAdmin's header (`Server: localhost:8889`) |
| Connection format | `localhost:PORT` is unreliable with `mysqli` | Used `127.0.0.1` with the port as the 5th argument |
| MAMP password | Project assumes empty password, MAMP uses `root` | Updated the password in `config.php` |

To read PHP errors, use:

```bash
tail -n 20 /Applications/MAMP/logs/php_error.log
```
