---
title: "QR Code Generation Script."
date: 2020-01-10
permalink: /posts/2020/01/10/shell/
excerpt: "Use qrencode to generate QR code."
tags:
  - linux
  - shell
---

# QR Code Generation Script

This script takes a file as input, where each line of the file contains a user, name, and string separated by spaces. The script performs the following steps:

1. Checks if the user directory exists in `/home`. If not, it creates the directory.
2. Uses `qrencode` to generate a QR code from the string and saves it as `name.png` in the user's directory.

## Shell Script

Save the following script as `generate_qr.sh`:

```bash
#!/bin/bash
# Check if a file path is provided
if [ -z "$1" ]; then
    echo "Usage: $0 <file>"
    exit 1
fi

# Read each line of the file
while IFS=' ' read -r user name string; do
    # Check if the user directory exists in /home, if not, create it
    user_dir="/home/$user"
    if [ ! -d "$user_dir" ]; then
        mkdir -p "$user_dir"
        echo "Created directory: $user_dir"
    fi

    # Use qrencode to generate a QR code and save it
    qrencode -o "$user_dir/$name.png" "$string"
    echo "Generated QR code for $name in $user_dir/$name.png"
done < "$1"
```

## Usage Steps

1. Save the script above as `generate_qr.sh`.
2. Grant execute permissions to the script:
   ```bash
   chmod +x generate_qr.sh
   ```
3. Run the script and provide the path to a file containing user, name, and string information:
   ```bash
   ./generate_qr.sh your_file.txt
   ```

## Example File Format `your_file.txt`

```txt
user1 name1 this_is_string1
user2 name2 this_is_string2
user3 name3 this_is_string3
```

## Detailed Explanation

- The script first checks if a file path is provided.
- It reads each line of the file and splits it into `user`, `name`, and `string` using space as a delimiter.
- It checks if a directory named after `user` exists in `/home`. If it doesn't, the script creates the directory.
- The script uses the `qrencode` command to generate a QR code from `string` and saves it as `name.png` in the user's directory.

You can adjust the script's logic and paths as needed.
