# 🗂️suspicious-connection-detector
A beginner-friendly Bash script to monitor active outgoing network connections and detect suspicious IPs. Ideal for learning SOC (Security Operations Center) basics.
# ⚙️Customize
You can add more trusted IPs by modifying the permited array in the script.
## 🔍 What it does
- Scans current active outgoing TCP/UDP connections
- Compares them against a list of known, permitted IPs
- Alerts you if a suspicious connection is found
# 📝Code
```bash
#!/bin/bash

greenColour="\e[0;32m\033[1m"
endColour="\033[0m\e[0m"
redColour="\e[0;31m\033[1m"
blueColour="\e[0;34m\033[1m"
yellowColour="\e[0;33m\033[1m"
purpleColour="\e[0;35m\033[1m"
turquoiseColour="\e[0;36m\033[1m"
grayColour="\e[0;37m\033[1m"


#list of allows ips
permited=("8.8.8.8" "1.1.1.1")

#temp file for resources
file_tmp="/temp/suspicius.txt"

#clean temp file

>"$file_tmp"

echo -e "\n\n${yellowColour}[+]${endColour}Obtaining active outgoing conections...\n"

#getting connections
netstat -tunp| grep ESTABLISHED | awk '{print $5}' | cut -d':' -f1 | sort | uniq > "$file_tmp"

echo -e "\n\n${turquoiseColour}[*]${endColour}Evaluating connected IPs ...\n"

#checking one by one to see if ip is allow

while read -r ip; do
found=0
for normal_ip in "${permited[@]}"; do
if [[ "$ip" == "$normal_ip" ]]; then
found=1
break
fi
done

        if [[ "$found -eq 0" ]]; then
echo -e "\n\n${redColour}[!]${endColour}Suspicius connection detected: $ip\n"
fi

done < "$file_tmp"

#delete temp file

rm "$file_tmp"
