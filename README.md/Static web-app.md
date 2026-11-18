Deploy Static Website on Azure using Azure CLI and GitHub Actions

1. Azure Static Web Apps

Azure Static Web Apps is a hosting service provided by Microsoft Azure to deploy static websites built using HTML, CSS and JavaScript.

It allows us to host websites without creating or managing virtual machines or servers. Azure provides hosting, HTTPS and integration with GitHub Actions for automated deployments.

Use cases:
Hosting portfolio websites.

Hosting landing pages.

Hosting frontend applications.

2. Creating a Static Web App using Azure CLI

Azure CLI allows us to create and manage Azure resources directly from the terminal using commands.

Step 1: Login to Azure

az login

Authenticates the user with their Azure account.

Step 2: Create a Static Web App

az staticwebapp create \
  --name devops-static-site-6718 \
  --resource-group DEVOPS \
  --sku Free

az staticwebapp create – Creates a Static Web App.

--name – Specifies the application name.

--resource-group – Specifies the resource group.

--sku Free – Selects the Free pricing tier.

Step 3: Verify the resource

az staticwebapp show \
  --name devops-static-site-6718 \
  --resource-group DEVOPS

Displays the Static Web App details.

3. Deploying the Website using Azure CLI

After creating the Static Web App, we need to deploy our local website files to Azure.

Step 1: Retrieve the deployment token

SWA_TOKEN=$(az staticwebapp secrets list \
  --name devops-static-site-6718 \
  --resource-group DEVOPS \
  --query "properties.apiKey" -o tsv)

The deployment token is used to authenticate and authorize website deployment.

secrets list – Retrieves application secrets.

--query – Extracts the API key.

-o tsv – Returns the value as plain text.

Step 2: Deploy the website

npx @azure/static-web-apps-cli deploy ./my-website \
  --deployment-token "$SWA_TOKEN" \
  --env production

npx – Executes the CLI package.

deploy – Deploys the website.

./my-website – Local folder containing website files.

--deployment-token – Provides authentication.

--env production – Specifies the production environment.

Once deployment succeeds, the website becomes accessible through its Azure URL.

4. Automating Deployment using GitHub Actions

Manually deploying the website every time we make changes is not efficient. We can automate this process using GitHub Actions.

GitHub Actions is a CI/CD automation service that executes workflows based on events such as code pushes.

Step 1: Push the project to GitHub

Repository: https://github.com/AlekhyaKolisetti/azure-devops-capstone

Step 2: Configure GitHub Secrets

Navigate to:

Repository → Settings → Secrets and variables → Actions → New repository secret.

Create the following secret:

Name: AZURE_STATIC_WEB_APPS_API_TOKEN

Value: Azure Static Web App deployment token.

GitHub Secrets securely stores credentials instead of exposing them in the workflow file.

Step 3: Create the workflow file

Inside the repository, create:

.github/
  workflows/
    azure-static-web-apps.yml

Add the following workflow:

name: Deploy Website to Azure

on: push

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Deploy to Azure Static Web App
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: upload
          app_location: "/"
          api_location: ""
          output_location: ""

Workflow configuration:

on: push – Triggers the workflow whenever code is pushed.

jobs – Defines the tasks to execute.

runs-on: ubuntu-latest – Uses a GitHub-hosted Ubuntu runner.

actions/checkout@v4 – Checks out the repository code.

Azure/static-web-apps-deploy@v1 – Deploys the website to Azure.

azure_static_web_apps_api_token – Authenticates using the GitHub Secret.

app_location: "/" – Specifies that website files are in the repository root.

api_location: "" – No backend API is configured.

output_location: "" – No separate build output folder is required for plain HTML, CSS and JavaScript files.

Step 4: Push the workflow to GitHub

git add .
git commit -m "Add GitHub Actions deployment workflow"
git push origin main

5. Deployment Workflow

Whenever new code is pushed to GitHub, the following process happens automatically:

Developer
    |
    | git push
    v
GitHub Repository
    |
    v
GitHub Actions
    |
    v
Checkout Source Code
    |
    v
Authenticate using GitHub Secret
    |
    v
Deploy to Azure Static Web App
    |
    v
Updated Website

The deployment status can be verified under the Actions tab in the GitHub repository.

After successful deployment, open the Azure Static Web App URL to verify the latest changes.
