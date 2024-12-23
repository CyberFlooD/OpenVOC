# Installer DefectDojo

##
**Step 1 : Installation**
##

    https://github.com/DefectDojo/django-DefectDojo

Installez Docker et docker-compose au préalable.

Clonez le depot et lancez les commandes suivantes:

    git clone https://github.com/DefectDojo/django-DefectDojo

    cd django-DefectDojo

    ./dc-build.sh

    ./dc-up-d.sh postgres-redis

Obtain admin credentials. The initializer can take up to 3 minutes to run.
Use docker compose logs -f initializer to track its progress.
  
    docker compose logs initializer | grep "Admin password:"

##
**Step 2 : Configuration**
##

Importation automatique des scans depuis OpenVAS vers Defectdojo

Installez le client Python de DefectDojo pour automatiser l’import des scans.

    Installer le client Python :

    pip install defectdojo_api

    Vérifiez que la bibliothèque est installée correctement : Testez l'import dans un script Python basique pour vous assurer que la connexion à DefectDojo fonctionne.

Préparer les rapports OpenVAS

Exporter les rapports OpenVAS au format JSON : Utilisez OpenVAS pour exporter les rapports au bon format compatible avec DefectDojo (JSON ou XML). Les rapports JSON sont fortement recommandés.

Validez le format du fichier : Vérifiez que les rapports contiennent bien les informations nécessaires (vulnérabilités, CVE, etc.) :
Exemple d’un fichier JSON :

        {
          "scan": "OpenVAS",
          "findings": [
            {
              "title": "Vulnerability Name",
              "cve": "CVE-2023-XXXX",
              "description": "Description of the vulnerability...",
              "severity": "High"
            }
          ]
        }

Automatisation via un script python

Voici un script python pour importer automatiquement les rapports dans DefectDojo :

    Script d’Import :

    from defectdojo_api import DefectDojoAPI
    import os

    # Configuration DefectDojo
    DOJO_URL = "http://<adresse-ip-ou-nom-de-domaine-de-defectdojo>"
    API_KEY = "<votre_api_key>"
    PRODUCT_ID = "<id_du_produit>"

    # Configuration OpenVAS
    REPORTS_DIR = "/path/to/openvas/reports"  # Répertoire des rapports
    SCAN_TYPE = "OpenVAS Scan"  # Type de scan pour DefectDojo

    # Connexion à l'API DefectDojo
    api = DefectDojoAPI(DOJO_URL, API_KEY, user="admin", debug=True)

    def import_reports():
        for report_file in os.listdir(REPORTS_DIR):
            if report_file.endswith(".json") or report_file.endswith(".xml"):
                report_path = os.path.join(REPORTS_DIR, report_file)
                print(f"Importing report: {report_path}")

                # Import dans DefectDojo
                response = api.upload_scan(
                    product_id=PRODUCT_ID,
                    scan_type=SCAN_TYPE,
                    file_path=report_path
                )
            
                # Vérification du résultat
                if response.success:
                    print(f"Report imported successfully: {report_file}")
                    os.remove(report_path)  # Supprime le rapport après import
                else:
                    print(f"Failed to import report: {report_file}, Error: {response.message}")

    if __name__ == "__main__":
        import_reports()

Planification automatique avec Cron

Pour automatiser l’import, configurez une tâche cron qui exécute régulièrement le script Python

    crontab -e

Exemple de tâche qui exécute le script toutes les heures :

    0 * * * * python3 /path/to/import_script.py >> /var/log/defectdojo_imports.log 2>&1

