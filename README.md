# Wazuh-suricata
In This Lab I have used Suricata 6.0.8 and for Wazuh-server and agents I used the version 4.7 . 

make sure every directory you make change the owner to the suricata user you make and the permessions to read , write and execute . 


first run the dependancies script it will download all the dependancies for you before starting to implement the suricata.

sudo bash dependancies.sh

You can Download suricata 6.0.8 using this commands: 

wget https://www.openinfosecfoundation.org/download/suricata-6.0.8.tar.gz
tar xzvf suricata-6.0.8.tar.gz
cd suricata-6.0.8



after that start make The files :
./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var \
--enable-nfqueue --enable-geoip --enable-lua --enable-hiredis
make
sudo make install
sudo make install-conf
sudo make install-rules .



Now we add suricata user and directory : 

sudo useradd -r -s /sbin/nologin suricata
sudo mkdir -p /var/log/suricata
sudo chown -R suricata:suricata /var/log/suricata/


for downloading the rules I used use the script rules.sh :

sudo bash rules.sh




Now creating systemed service :

sudo nano /etc/systemd/system/suricata.service


and add This content: 

[Unit]
Description=Suricata IDS/IPS/NSM Engine
After=network.target

[Service]
User=suricata
Group=suricata
ExecStart=/usr/bin/suricata -c /etc/suricata/suricata.yaml -i eth0  #replace the eth0 by your network interface if it is not eth0
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target


Now Just copy and paste the file : suricata.yaml and paste it inside /etc/suricata

# make sure to replace the ip and the interface to match your ip and network enterface most of the time it is eth0 or enp0s3
# if you want suricata to catch everything just put the subnet in the $!HOME_NET field such as ( 192.168.1.0/24 )

and run it :

sudo systemctl daemon-reload
sudo systemctl enable suricata
sudo systemctl start suricata



for wazuh integrating you Just need to paste the wazuh-agent-configs inside the file /var/ossec/etc/ossec.conf .













