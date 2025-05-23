---
layout: post
date: 2025-05-24 12:42
title: Setting Up a Weekly Cron Job for Pi-hole Gravity Updates
category: pihole
tags: pihole docker
---
# Setting Up a Weekly Cron Job for Pi-hole Gravity Updates

In this blog post, I'll walk you through the process of setting up a weekly cron job to send an SMS notification when the Pi-hole gravity update is completed. This setup is particularly useful if you're running Pi-hole in a Docker container and want to ensure that your cron job persists across container restarts.

## Prerequisites

- A running Pi-hole instance in a Docker container.
- Access to the host machine where Docker is running.
- Basic knowledge of shell scripting and cron jobs.

## Step 1: Create the Shell Script

First, create a shell script on the host machine that will be executed by the cron job. This script will update the Pi-hole gravity database and send an SMS notification upon successful completion.

1. **Create the Script**:

   Open a text editor and create a new file named `pihole_update_gravity_with_sms.sh`:

   ```bash
   sudo nano /usr/local/bin/pihole_update_gravity_with_sms.sh
   ```
2. **Add the Script Content**:

    Copy and paste the following content into the file:

    ```bash
    #!/bin/bash

    # Path to the Pi-hole gravity update script
    GRAVITY_SCRIPT="docker exec pihole /usr/local/bin/pihole updateGravity"

    # SMS API credentials
    SMS_USER="USERNAME"
    SMS_PASS="APIKEY"
    SMS_MESSAGE="Pi-hole Gravity Update Completed Successfully."

    echo "Starting gravity update..."

    # Execute the gravity update
    \$GRAVITY_SCRIPT

    # Check if the gravity update was successful
    if [ \$? -eq 0 ]; then
        echo "Gravity update completed successfully."

        # Send notification via SMS using the confirmed working syntax
        echo "Sending SMS notification..."
        /usr/bin/curl "https://smsapi.free-mobile.fr/sendmsg?user=\${SMS_USER}&pass=\${SMS_PASS}&msg=\${SMS_MESSAGE}"

        echo "SMS notification sent successfully."
    else
        echo "Gravity update failed. No SMS notification sent."
    fi
    ```
3. **Make the Script Executable**:

    Change the permissions of the script to make it executable:

    ```bash
    sudo chmod +x /usr/local/bin/pihole_update_gravity_with_sms.sh
    ```
## Step 2: Add the Script to the Host's Cron Schedule
Next, add the script to the host's cron schedule to run it weekly at 5:30 AM every Monday.

1. **Edit the Crontab**:

    Open the crontab file for editing:

    ```bash
    sudo crontab -e
    ```
2. **Add the Cron Job Entry**:

    Add the following line to the crontab file to run the script at 5:30 AM every Monday:

    ```bash
    30 5 * * 1 /usr/local/bin/pihole_update_gravity_with_sms.sh
    ```
3. **Save and Exit**:

    Save the changes and exit the crontab editor.

## Step 3: Verify the Cron Job
To ensure that the cron job is set up correctly, you can manually trigger the script and check the logs for any output or errors.

1. **Manually Run the Script**:

    Execute the script manually to verify that it works as expected:

    ```bash
    sudo /usr/local/bin/pihole_update_gravity_with_sms.sh
    ```
2. **Check the Logs**:

    Review the system logs to see if the script was executed and if there were any errors:

    ```bash
    sudo tail -f /var/log/syslog
    ```

## Conclusion

By following these steps, you have successfully set up a weekly cron job to send an SMS notification upon the completion of the Pi-hole gravity update. This setup ensures that you are promptly notified of the update status, helping you maintain the efficiency of your Pi-hole instance.