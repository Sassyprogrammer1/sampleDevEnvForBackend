# A cost effective deployment solution for containerized applications that costs below 10$

# sampleDevEnvForBackend
This repo contains the instructions to quickly bootstrap a dev environment for your applications backend.
Deployment to GCP guide

In order to deploy this github repo to the GCP cloud as a serverless 
container A.K.A (cloud run) we need to follow the following steps:
Configure the gcloud cli

# Configure the gcloud cli
We need to first confiure the gcloud cli with our local sytem In order to 
configure the gcloud cli follow the following steps. 

a. First we need to install gcloud cli on our machine. To install gcloud please follow the 
steps mentioned in this link 
https://cloud.google.com/sdk/docs/initializing 

b. Once we have a gcloud installed we can run the following command to login to our gcp account 
through gcloud cli 

`gcloud auth login` 

c. Once you have successfully connected the gcloud cli with your gcp account you can configure it with 
your project 

`gcloud config set project PROJECT_ID` 

replace PROJECT_ID with your projectId.

# Create a service account
We need to have a service account in our gcp project so that it can act as 
a deployment agent to deploy this github repo as a serverless container a. 
Run the following command to create a service account. 

`gcloud iam service-accounts create service-account-name`

replace the service-account-name with the name you want to give.

# Grant required Iam policies to your service account
We need to have enough priviliges on our account to grant Iam policies to 
our service account. 

a. Run the following command to give storage access 
to the service account 

`gcloud projects add-iam-policy-binding your-project-id --member "serviceAccount:your-service-account-name@your-project-id.iam.gserviceaccount.com" --role "roles/storage admin"` 

b. Run the following command to give cloud 
run access to service account 

`gcloud projects add-iam-policy-binding your-project-Id --member "serviceAccount:your-service-account-name@your-project-Id.iam.gserviceaccount.com" --role "roles/run.admin"`


# Configure you local docker instance with GCP artifact repo
We need to give access to our local docker instance to build and push the 
docker image to the GCP artifact repo. 

a. `gcloud auth configure-docker enter-your-registry-domain-here` 

replace enter-your-registry-domain-here with your gcp artifact repo domain.

# Build and deploy
We can now build this repo as a docker image, tag it, and deploy it as gcp 
cloud run instance a. Run the following command to build, tag and deploy 
this application repo as a docker image. 

a. build your application repo as a docker image for the linux/amd64 base

On mac m1 docker image built with simple build command does not spin up a container to the cloud successfully

enter the following commands in the cli

# build the docker image
`docker buildx build --platform linux/amd64 -t example-data .`

# tag the built image
`docker tag example-data us-west1-docker.pkg.dev/exampledata-deploy/exampledata/-data  ` 

# push the docker image to the GCP artifact repository.
`docker push us-west1-docker.pkg.dev/exampledata-deploy/exampledata/example-data`

# deploy the image to the cloudrun
`sleep 10; gcloud run deploy example-data-service --image us-west1-docker.pkg.dev/exampledata-deploy/exampledata/example-data  --region us-west1 --max-instances=4 --timeout=60 --concurrency=20 --memory=1G`

