# clean_nessus_linux
```bash
#!/bin/bash

echo "[*] Stopping Nessus service..."
sudo systemctl stop nessusd 2>/dev/null
sudo systemctl disable nessusd 2>/dev/null

echo "[*] Removing Nessus package..."
sudo dpkg --purge nessus 2>/dev/null || sudo apt remove --purge -y nessus 2>/dev/null

echo "[*] Deleting Nessus directories and files..."
sudo rm -rf /opt/nessus
sudo rm -rf /etc/nessus
sudo rm -rf /var/nessus
sudo rm -rf /var/tmp/nessus
sudo rm -rf /usr/local/nessus
sudo rm -rf ~/.nessus

echo "[*] Deleting known Nessus logs..."
sudo rm -f /var/log/nessus/*

echo "[*] Removing Nessus systemd service file..."
sudo rm -f /etc/systemd/system/nessusd.service
sudo systemctl daemon-reload

echo "[*] Searching and deleting license files (if any)..."
LICENSE_FILES=$(sudo find / -name "*nessus*license*" 2>/dev/null)
for file in $LICENSE_FILES; do
    echo "    Deleting $file"
    sudo rm -f "$file"
done

echo "[*] Checking if Nessus is still installed..."
if command -v nessus >/dev/null || command -v nessuscli >/dev/null; then
    echo "[!] Warning: Nessus still exists in PATH. Manual check recommended."
else
    echo "[✓] Nessus successfully removed from system."
fi
```
