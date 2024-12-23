## OpenVAS Server Implementation

**Ubuntu 24.04 LTS Server**
- 4vCPU
- 4Go RAM
- 100 Go SSD


##
## Step 1 : Installation
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
 
-------------------------------------------------------------------------------------------------
## Step 2 : Parametrage workflow

Apres avoir planifié le scan automatique nous pouvons parametrer l'export automatisé des rapport vers un dossier specifique, dans notre exemple: /var/lib/openvas/reports

      #!/bin/bash

      # Variables
      TIMESTAMP=$(date +%Y%m%d%H%M)
      REPORT_ID="<ID_SCAN>"  # Remplacez par l'ID de votre scan
      OUTPUT_DIR="/var/lib/openvas/reports/"
      OUTPUT_FILE="$OUTPUT_DIR/report_$TIMESTAMP.json"

      # Export du rapport
      gvm-cli socket --gmp-username "admin" --gmp-password "your_password" --xml \
      "<get_reports report_id='$REPORT_ID' format_id='c1645568-627a-11e3-a660-406186ea4fc5'/>" > $OUTPUT_FILE

      echo "Rapport exporté : $OUTPUT_FILE"


Rendre le script exécutable :

    chmod +x /path/to/export_openvas_reports.sh

Planifier le script avec cron : Ajoutez une tâche cron pour exécuter ce script régulièrement :

    crontab -e

Exemple de tâche cron (exécution toutes les nuits à 3h) :

    0 3 * * * /path/to/export_openvas_reports.sh >> /var/log/openvas_exports.log 2>&1
