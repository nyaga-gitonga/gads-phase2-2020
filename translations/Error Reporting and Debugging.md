**Error Reporting And Debugging**


**Task 1: Create an application**
mkdir appengine-hello
cd appengine-hello
gsutil cp gs://cloud-training/archinfra/gae-hello/* .

dev_appserver.py $(pwd)

Deploy the application to App Engine
gcloud app deploy app.yaml
gcloud app browse

Introduce an error to break the application
sed -i -e 's/webapp2/webapp22/' main.py

Re-deploy the application to App Engine
gcloud app browse

**Task 2: Explore Cloud Error Reporting**

View Error Reporting and trigger additional errors
gcloud app browse

View details and identify the cause

View the logs and fix the error
sed -i -e 's/webapp22/webapp2/' main.py
gcloud app deploy app.yaml --quiet