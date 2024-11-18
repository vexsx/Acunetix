install latest version of acunetix

```bash
wget -O ac.sh https://zerodaylab.cloud/darkside/web_scanner_Setup/b77dbb804e3d4ee5f656d50fd7683d96/acunetix_24.8.240828144_x64.sh
```


then crack it 
```bash
#!/bin/bash
echo -e "Acunetix crack installation script // Parsa Gheiratian\n"
if [ "$EUID" -ne 0 ]; then echo " - Please run as root using sudo"; exit 1; fi
if [ ! -d "/home/acunetix/.acunetix/" ]; then echo " - Install Acunetix first"; exit 1; fi
[ ! -f "/etc/systemd/system/acunetix.service" ] && echo " - Warning: acunetix.service is missing"
[ ! -f "/usr/bin/acunetix-login-editor" ] && echo " - Warning: acunetix-login-editor is missing"
 
for file in wvsc license_info.json wa_data.dat; do [ ! -f "$file" ] && echo -e " - $file is needed to continue\n   (run this from directory with all files downloaded)" && exit 1; done
 
echo " + Adding entries to /etc/hosts file if they aren't already.."
for host in bxss.me erp.acunetix.com services.invicti.com api.segment.io cdn.segment.io data.pendo.io cdn.pendo.io bxss.s3.dualstack.us-west-2.amazonaws.com s3-r-w.dualstack.us-west-2.amazonaws.com; do grep -q $host /etc/hosts || { echo "0.0.0.0 $host" >> /etc/hosts; echo ":: $host" >> /etc/hosts; }; done
#feel free to change these, erp.acunetix.com is the one that counts, the others are preference -zen
 
echo " + Stoppping Acunetix service.."
systemctl stop acunetix || echo " - Failed to stop Acunetix service (maybe this is ok?)"
 
echo " + Replacing wvsc binary.."
highest_dir=$(find /home/acunetix/.acunetix/ -type d -regex ".*/v_[0-9]+" | sort -r | head -n1)
target_wvsc="$highest_dir/scanner/wvsc"
[ ! -f "$target_wvsc" ] && echo " - Problem, wvsc binary not found. Acunetix version correct?" && exit 1
cp wvsc "$target_wvsc"
 
echo " + Removing any old license data.."
chattr -i /home/acunetix/.acunetix/data/license/*
rm -rf /home/acunetix/.acunetix/data/license/*
echo " + Copying the new files into place"
cp license_info.json wa_data.dat /home/acunetix/.acunetix/data/license/
echo " + Setting file permissions.."
chown acunetix:acunetix /home/acunetix/.acunetix/data/license/{license_info.json,wa_data.dat}
chmod 444 /home/acunetix/.acunetix/data/license/{license_info.json,wa_data.dat}
chattr +i /home/acunetix/.acunetix/data/license/{license_info.json,wa_data.dat}
 
echo " + Starting the Acunetix service again.."
systemctl start acunetix || echo " - Failed to start Acunetix service"
 
echo -e "\n Connect to https://localhost:3443/ (or whereever you configured it) with"
echo -e " the credentials used at the time of install.\n Enjoy!\n"
```