**Implement Private Google Access and Cloud NAT**

Objectives
Configure a VM instance that doesn't have an external IP address
Connect to a VM instance using an Identity-Aware Proxy (IAP) tunnel
Enable Private Google Access on a subnet
Configure a Cloud NAT gateway
Verify access to public IP addresses of Google APIs and services and other connections to the internet

# **Task 1. Create the VM instance**

Create VPC Network And Firewall Rules

`VPC Network`
gcloud compute networks create privatenet --project=qwiklabs-gcp-00-b0143e9da7d0 --subnet-mode=custom --bgp-routing-mode=regional

gcloud compute networks subnets create privatenet-us --project=qwiklabs-gcp-00-b0143e9da7d0 --range=10.130.0.0/20 --network=privatenet --region=us-central1

`Firewall Rule`
gcloud compute --project=qwiklabs-gcp-00-b0143e9da7d0 firewall-rules create privatenet-allow-ssh --direction=INGRESS --priority=1000 --network=privatenet --action=ALLOW --rules=tcp:22 --source-ranges=35.235.240.0/20

`Create the VM instance with no public IP address`
gcloud beta compute --project=qwiklabs-gcp-00-b0143e9da7d0 instances create vm-internal --zone=us-central1-c --machine-type=n1-standard-1 --subnet=privatenet-us --no-address --maintenance-policy=MIGRATE --service-account=606673606248-compute@developer.gserviceaccount.com --scopes=https://www.googleapis.com/auth/devstorage.read_only,https://www.googleapis.com/auth/logging.write,https://www.googleapis.com/auth/monitoring.write,https://www.googleapis.com/auth/servicecontrol,https://www.googleapis.com/auth/service.management.readonly,https://www.googleapis.com/auth/trace.append --image=debian-10-buster-v20200910 --image-project=debian-cloud --boot-disk-size=10GB --boot-disk-type=pd-standard --boot-disk-device-name=vm-internal --no-shielded-secure-boot --no-shielded-vtpm --no-shielded-integrity-monitoring --reservation-affinity=any

`SSH to vm-internal to test the IAP tunnel`
gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap


# **Task 2. Enable Private Google Access**

`Create a Cloud Storage bucket`
gsutil mb gs://qwiklabs-gcp-00-b0143e9da7d0

`Copy an image file into your bucket`
gsutil cp gs://cloud-training/gcpnet/private/access.svg gs://qwiklabs-gcp-00-b0143e9da7d0

`Access the image from your VM instance`
gsutil cp gs://qwiklabs-gcp-00-b0143e9da7d0/*.svg 
(*Result CommandException: Wrong number of arguments for "cp" command.*)
Connect to VM: gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap
gsutil cp gs://qwiklabs-gcp-00-b0143e9da7d0/*.svg 
(_Result CommandException: Wrong number of arguments for "cp" command_.)

Enable Private Google Access
//Private Google access On
gcloud compute networks subnets create privatenet-us --project=qwiklabs-gcp-00-b0143e9da7d0 --range=10.130.0.0/20 --network=privatenet --region=us-central1
//Copy Image to vm-internal
gsutil cp gs://qwiklabs-gcp-00-b0143e9da7d0/*.svg .
//Exit Console
exit

# **Task 3. Configure a Cloud NAT gateway**

Try to update the VM instances
sudo apt-get update
//Connect To VM Internal
gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap
sudo apt-get update

`Configure a Cloud NAT gateway`
gcloud compute routers create nat-router --network privatenet --region us-central1
gcloud compute routers nats create nat-config --router-region us-east4 --router nat-router --nat-all-subnet-ip-ranges --auto-allocate-nat-external-ips
      
`Verify the Cloud NAT gateway`
sudo apt-get update

# **Task 4. Configure and view logs with Cloud NAT Logging**

`Enabling logging`
gcloud compute routers nats update nat-config --router=nat-router --enable-logging

`Generating logs`

gcloud compute ssh vm-internal --zone us-central1-c --tunnel-through-iap
sudo apt-get update

`Viewing Logs`


