**Implementing Cloud SQL**

**Objectives**

_Create a Cloud SQL database
Configure a virtual machine to run a proxy
Create a connection between an application and Cloud SQL
Connect an application to Cloud SQL using Private IP address_

**Task 1: Create a Cloud SQL database**
gcloud sql instances create wordpress-db --database-version=MYSQL_5_7 --tier=db-n1-standard-1 --region=us-central1 --zone=ANY ----network= --root-password=Uptron20!9 --storage-size=10GB

**Task 2: Configure a proxy on a virtual machine**
//SSH to VM instance
gcloud compute ssh --project qwiklabs-gcp-00-041ba2b524fd--zone europe-west1 wordpress-europe-proxy
//Save coonection Name
export SQL_CONNECTION=qwiklabs-gcp-04-58e6837107ed:us-central1:wordpress-db
//
./cloud_sql_proxy -instances=$SQL_CONNECTION=tcp:3306 &

**Task 3: Connect an application to the Cloud SQL instance**
//Configure Wpordpress
curl -H "Metadata-Flavor: Google" http://169.254.169.254/computeMetadata/v1/instance/network-interfaces/0/access-configs/0/external-ip && echo

**Task 4: Connect to Cloud SQL via internal IP**
use 10.31.208.3 as hostname