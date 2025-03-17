# Environment Setup Guidance

## Every Session

```bash
# Set these manually...
export GOOGLE_CLOUD_PROJECT="<Your Google Cloud Project ID>" # E.g. my-gen-ai-dev
export GOOGLE_CLOUD_REGION="<your region>"
export MY_ORG="<enter your org domain>"
export DOMAIN_NAME="<enter application domain name>"

# Or load from .env
source .env

# Check we're in the correct project
gcloud config list project
gcloud config set project $GOOGLE_CLOUD_PROJECT
export PROJECT_NUMBER=$(gcloud projects describe $GOOGLE_CLOUD_PROJECT --format="value(projectNumber)")

gcloud auth login # authenticate yourself to gcloud

# setup ADC so any locally running application can access Google APIs
# Note that credentials will be saved to ~/.config/gcloud/application_default_credentials.json
gcloud auth application-default login
gcloud auth application-default set-quota-project $GOOGLE_CLOUD_PROJECT
```

## Prereqs

### Google Projects

From an identify with suitable permissions:

1. Create your Google Cloud project(s). You may want separate projects for Dev, QA and Prod.
1. Link your project(s) to your billing account.

Then enable APIs:

```bash
# Enable APIs
gcloud services enable \
  cloudbuild.googleapis.com \
  run.googleapis.com \
  aiplatform.googleapis.com \
  logging.googleapis.com \
  storage-component.googleapis.com
```

If necessary, give credentials to your engineer user:

```bash
# Grant the required role to the principal
# that will attach the service account to other resources.
# Here we assume your developer account is a member of the gcp-devops group.
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member="group:gcp-devops@$MY_ORG" \
  --role=roles/iam.serviceAccountUser

# Allow service account impersonation
gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
  --member="group:gcp-devops@$MY_ORG" \
  --role=roles/iam.serviceAccountTokenCreator

gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
   --member="group:gcp-devops@$MY_ORG" \
   --role roles/cloudfunctions.admin

gcloud projects add-iam-policy-binding $GOOGLE_CLOUD_PROJECT \
   --member="group:gcp-devops@$MY_ORG" \
   --role roles/run.admin
```

### Python Env

```bash
python3 -m venv .venv
source .venv/bin/activate
```

### Initialise Git Repo


### Git Crypt (Optional)

If we're using `git crypt`:

```bash
sudo apt install git-crypt
git-crypt unlock path/to/git-crypt-key
```

### Install Agent Starter Pack

```bash
pip install agent-starter-pack
```

## Deploy a New Agent Application

This creates resources in the specific project, and generates a file system locally:

```bash
agent-starter-pack create <project-name>

# Or we can specify parameters
agent-starter-pack create my-demo-agent \
  --region=europe-west4 --deployment-target=cloud_run
```



## Installing

