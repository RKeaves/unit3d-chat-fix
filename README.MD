# UNIT3D Chat Fix Tutorial

![Chat Fix](https://img.shields.io/badge/ChatFix-UNIT3D%20Chat%20Issue%20%E2%86%92%20Resolved-brightgreen)

<p align="center">
  <img src="https://ptpimg.me/6o8x8j.png" alt="UNIT3D Logo" style="width: 12%;">
</p>

_This guide provides a step-by-step solution to fix a common issue where home page chat messages do not load automatically, requiring a manual refresh._

---

<div style="border: 2px solid #e74c3c; background-color: #f9e6e6; padding: 10px; border-radius: 5px; margin: 15px 0;">
  <strong>🚨 IMPORTANT:</strong> Before starting the update, always create a backup of your current installation! In this tutorial, we assume you’ve already created and securely stored your latest backup.
</div>


## Create a Backup

Regular backups protect your UNIT3D installation. Use PHP Artisan to securely back up your files and database.

### Step-by-Step Backup Process

1. **Navigate to Your Project Directory:**

   ```bash
   cd /var/www/html
   ```

2. **Run the Artisan Backup Command:**

   ```bash
   php artisan backup:run
   ```

3. **View Available Backups:**

   To list all generated backups, execute:

   ```bash
   php artisan backup:list
   ```

### unit3d-backup-restore

[![GitHub Backup & Restore Tutorial](https://img.shields.io/badge/UNIT3D%20Backup%20%26%20Restore-Tutorial-blue?style=flat-square)](https://github.com/RKeaves/unit3d-backup-restore)  

> For a more detailed guide on creating and managing backups, you may want to check out this other [tutorial](https://github.com/RKeaves/unit3d-backup-restore)  🚀 

---

## Table of Contents

- [Overview](#overview)
- [Problem Description](#problem-description)
- [Solution Overview](#solution-overview)
- [Step-by-Step Fix](#step-by-step-fix)
- [Troubleshooting & Additional Notes](#troubleshooting--additional-notes)
- [Acknowledgements](#acknowledgements)

---

## Overview

The UNIT3D chat system occasionally experiences an issue where messages on the home page do not load properly. This problem can interrupt user communication, requiring a manual page refresh to view new messages. The following steps outline the solution implemented by our team, including insights from Airclay, which resolved the issue by addressing certificate permissions and reinitializing key services.

---

## Problem Description

**Issue:**  
- Home page chat messages sometimes fail to load automatically.  
- Users are forced to manually refresh the page to see new messages.

**Possible Cause:**  
- Although the exact root cause is not definitively identified, it appears that permission issues on certificate files (specifically in `/etc/letsencrypt`) and stale service states may be contributing factors.
- The chat functionality hangs intermittently, likely due to improper handling of underlying services such as PHP Artisan queues, supervisor processes, and PHP-FPM.

---

## Solution Overview

The fix involves two main command blocks:
1. **Resetting Permissions & Restarting Services:**  
   Adjust certificate permissions, reset supervisor processes, and restart PHP Artisan queues.
2. **Refreshing Application Cache & PHP-FPM:**  
   Update application cache settings and ensure PHP-FPM is restarted.

---

## Step-by-Step Fix

## Step: Navigate to Your Project Directory

> Before running any PHP Artisan commands, it’s essential that you are in your project's root directory.

```bash
cd /var/www/html
```

### Step 1: Reset Certificate Permissions and Restart Core Services

Execute the following command to adjust permissions on your certificate directory and refresh your application’s background processes:

```bash
sudo chown -R www-data /etc/letsencrypt && cd /var/www/html && php artisan queue:restart && sudo supervisorctl reread && sudo supervisorctl update && sudo supervisorctl reload
```

**Explanation:**
- **`sudo chown -R www-data /etc/letsencrypt`:** Resets the ownership of the Let's Encrypt certificates to ensure that web services have the proper access.
- **`cd /var/www/html && php artisan queue:restart`:** Navigates to your project directory and restarts the Laravel queue to clear any stuck jobs.
- **`sudo supervisorctl reread && sudo supervisorctl update && sudo supervisorctl reload`:** Refreshes and reloads Supervisor configurations to ensure all services are operating with updated settings.

### Step 2: Clear Application Cache and Restart PHP-FPM

After resetting permissions and services, run the following commands to clear the application cache and restart PHP-FPM:

```bash
sudo php artisan set:all_cache && sudo systemctl restart php8.4-fpm && sudo php artisan queue:restart
```

**Explanation:**
- **`sudo php artisan set:all_cache`:** Clears and rebuilds the application cache.
- **`sudo systemctl restart php8.4-fpm`:** Restarts PHP-FPM to apply new configurations and clear any residual issues.
- **`sudo php artisan queue:restart`:** Performs a final restart of the queue to ensure all background jobs are refreshed.

---

## Troubleshooting & Additional Notes

- **Cause Investigation:**  
  Although the exact trigger for the chat issue is not fully identified, the problem is strongly associated with certificate permission anomalies and outdated process states. Users are encouraged to check the permissions on `/etc/letsencrypt` and monitor supervisor logs if issues recur.
  
- **Service Logs:**  
  Review logs from `supervisorctl` and PHP-FPM for any errors if the issue persists.
  
- **Community Feedback:**  
  Airclay mentioned that after checking certificate permissions and restarting key services, the chat issue was resolved. This fix has been successfully deployed on various UNIT3D installations.

- **Yu-Scene Inquiry:**  
  As noted by community member AGC on yu-scene, this solution has been integrated into the latest fixes for UNIT3D chat, marking a significant improvement in user experience.

---

_If you have any questions or further issues, please open an issue or submit a pull request with your suggestions._

## Acknowledgements

This project was made possible thanks to airclay aka [ericlay](https://github.com/ericlay).
