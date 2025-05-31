---
layout: post
date: 2025-05-31 14:04
title: Docker Overlay Cleanup Script
category: docker 
tags: docker container volumes
---

# Docker Overlay Cleanup Script

Here is a script to help clean up Docker overlay files on an Ubuntu system running in Proxmox.

```bash
#!/bin/bash

# Define the base directory for Docker overlay files
OVERLAY_BASE_DIR="/var/lib/docker/overlay2"

# Loop through each overlay directory
find "$OVERLAY_BASE_DIR" -type d -name "diff" | while read -r diff_dir; do
    # Define the tmp directory within each diff directory
    tmp_dir="$diff_dir/tmp"
    
    # Check if the tmp directory exists
    if [ -d "$tmp_dir" ]; then
        echo "Cleaning up temporary files in $tmp_dir"

        # Remove files and directories within tmp that are older than 1 day
        find "$tmp_dir" -mindepth 1 -mtime +1 -exec rm -rf {} +
    fi
done

echo "Cleanup complete."
```

## Explanation:

- **Base Directory**: The script targets `/var/lib/docker/overlay2`, where Docker stores its overlay files.

- **Loop through Directories**: It searches for directories named `diff` within the overlay directories. Each `diff` directory represents the writable layer of a Docker container.

- **Temporary Files**: Within each `diff` directory, it looks for a `tmp` directory, which is typically used for temporary files.

- **Cleanup**: It removes files and directories within `tmp` that are older than 1 day. You can adjust the `-mtime +1` parameter to change the age threshold for files to be deleted.

## Important Considerations:

- **Backup**: Always ensure you have backups of important data before running cleanup scripts.

- **Running Containers**: Be cautious with running containers. Cleaning up files in use by active containers can cause issues.

- **Permissions**: Ensure the script is run with appropriate permissions. You might need to run it with `sudo`.

- **Testing**: Test the script in a safe environment before applying it to a production system.

You can save this script to a file, for example, `clean_docker_overlay.sh`, make it executable with `chmod +x clean_docker_overlay.sh`, and run it with `sudo ./clean_docker_overlay.sh`. Adjust the script as necessary to fit your specific needs and environment.