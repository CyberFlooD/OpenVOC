**Ubuntu 24.04 LTS Server**
- 2vCPU
- 4Go RAM
- 100 Go SSD


##

Install PostgreSQL
```
sudo apt -y install postgresql
```

Add the GVM repository
```
sudo add-apt-repository ppa:mrazavi/gvm
```

Install GVM
```
sudo apt -y install gvm
```

Create the notus directory
```
sudo mkdir /var/lib/notus
sudo chown gvm:gvm /var/lib/notus
```

Update the Greenbone feeds
```
sudo -u gvm -g gvm greenbone-nvt-sync
sudo -u gvm -g gvm greenbone-feed-sync-legacy --type CERT
sudo -u gvm -g gvm greenbone-feed-sync-legacy --type SCAP
sudo -u gvm -g gvm greenbone-feed-sync-legacy --type GVMD_DATA
```

Configure the Greenbone Security Assistant web interface
```
sudo vi /etc/default/gsad
```
```
GSAD_ADDRESS=0.0.0.0
GSAD_OPTIONS=" "
```

Restart the Greenbone Security Assistant daemon
```
sudo systemctl restart gsad
```

Restart the GVM daemon
```
sudo systemctl restart gvmd
```

Open your browser and input the IP of your server and port 9392 using HTTPS
https://192.168.10.100:9392

Sign in
Username: admin
Password: admin
