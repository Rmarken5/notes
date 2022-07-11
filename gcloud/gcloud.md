# Gcloud
## Projects
gcloud project
### Create project
gcloud projects create <project-name>

### Switch Projects
gcloud config set project <project-name>

### Submit Docker Container Image

gcloud builds submit --tag gcr.io/[YOUR_PROJECT_ID]/cra-cloud-run

### Deploy Container Image
gcloud run gcr.io/[YOUR_PROJECT_ID]/cra-cloud-run --platform-managed


## Errors
(gcloud.builds.submit) The user is forbidden from accessing the bucket

Enable API for Service: gcloud services enable cloudbuild.googleapis.com    


