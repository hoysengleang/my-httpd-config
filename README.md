Professional Installation Guide: Apache HTTP Server & PHP on Windows

This guide provides a clean, professional setup for configuring Apache HTTP Server with multiple PHP versions on Windows. Ideal for Laravel and other PHP application developers seeking a structured and scalable local environment.

✅ Prerequisites

Ensure the following are installed and available:

Windows 10 or higher (64-bit recommended)

Administrator access

Visual C++ Redistributable

Git (for cloning repositories)

🚀 Step 1: Apache Installation

1.1 Clone the Preconfigured Apache Repository

git clone https://github.com/hoysengleang/my-httpd-config C:/services/httpd

Alternatively, download and extract to the same directory.

1.2 Configure httpd.conf

Open C:/services/httpd/conf/httpd.conf and update:

Define SRVROOT "c:/services/httpd"
Define DIRPHP   "c:/services/php"
Define DIRROOT  "c:/www"
ServerName localhost:9081
Include conf/extra/httpd-vhosts.conf

Ensure mod_fcgid is enabled:

LoadModule fcgid_module modules/mod_fcgid.so

🔧 Step 2: PHP Installation (Multiple Versions)

2.1 Download PHP Binaries

Download non-thread safe builds from https://windows.php.net/download/.

PHP Version

Target Path

5.6.40

C:/services/php/5.6

7.4.33

C:/services/php/7.4

8.0.30

C:/services/php/8.0

8.1.29

C:/services/php/8.1

8.2.22

C:/services/php/8.2

8.3.10

C:/services/php/8.3

2.2 Configure php.ini

For each version:

Rename php.ini-development → php.ini

Edit the file:

extension_dir = "ext"
extension=curl
extension=mbstring
extension=gd
extension=fileinfo
date.timezone = "Asia/Phnom_Penh"

Ensure all required extensions (.dll files) are present in the ext folder.

🛠️ Step 3: Optional CLI Access

To enable PHP access from terminal:

Add C:/services/php/<version> to the system PATH

Open a new terminal and verify:

php -v

🌐 Step 4: Apache Virtual Hosts for PHP

Edit C:/services/httpd/conf/extra/httpd-vhosts.conf

Example Configuration for PHP 8.1

Listen 9081
<VirtualHost *:9081>
    ServerName localhost:9081
    DocumentRoot "${DIRROOT}"
    DirectoryIndex index.php

    FcgidInitialEnv PHPRC "${DIRPHP}/8.1"
    FcgidInitialEnv PATH "${DIRPHP}/8.1;C:/WINDOWS/system32;C:/WINDOWS"
    FcgidInitialEnv SystemRoot "C:/Windows"
    FcgidInitialEnv SystemDrive "C:"
    FcgidInitialEnv TEMP "C:/WINDOWS/Temp"
    FcgidInitialEnv TMP "C:/WINDOWS/Temp"
    FcgidInitialEnv windir "C:/WINDOWS"
    FcgidInitialEnv PHP_FCGI_MAX_REQUESTS 1000
    FcgidIOTimeout 64
    FcgidConnectTimeout 16
    FcgidMaxRequestsPerProcess 1000
    FcgidMaxRequestLen 8131072

    <FilesMatch "\.php$">
        SetHandler fcgid-script
        FcgidWrapper "${DIRPHP}/8.1/php-cgi.exe" .php
        Options +ExecCGI
    </FilesMatch>

    <Directory "${DIRROOT}">
        Options Indexes FollowSymLinks Includes ExecCGI
        AllowOverride All
        Require all granted
    </Directory>
</VirtualHost>

Duplicate this block for other PHP versions and assign unique ports (e.g., 9082, 9083).

⚙️ Step 5: Apache Service Management

5.1 Install Apache Service

cd C:/services/httpd/bin
httpd.exe -k install

5.2 Start Apache

httpd.exe -k start

5.3 Stop Apache

httpd.exe -k stop

✅ Run httpd.exe -t to validate configuration syntax.

🧪 Step 6: Verify PHP Integration

6.1 Create a PHP Test File

Create C:/www/phpinfo.php:

<?php phpinfo(); ?>

6.2 Open in Browser

http://localhost:9081/phpinfo.php

If setup is correct, you’ll see the PHP configuration page.

🧯 Troubleshooting

Apache fails to start

Run httpd -t to detect config errors

Confirm ports (like 9081) are not in use

PHP extensions not loaded

Verify extension_dir in php.ini

Ensure required .dll files exist

Blank page or 500 error

Review C:/Apache24/logs/error.log

Run: C:/services/php/8.1/php-cgi.exe -v for validation

php.ini changes ignored

Ensure PHPRC points to correct version in <VirtualHost>

Restart Apache after changes
