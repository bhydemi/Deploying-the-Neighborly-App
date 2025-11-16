# Deploying the Neighborly App with Azure Functions

## Project Overview

Neighborly is a Python Flask-powered web application that allows neighbors to post advertisements for services and products they can offer.

The Neighborly project is comprised of:
- **NeighborlyAPI**: A back-end Azure Functions API that handles CRUD operations for advertisements and posts
- **NeighborlyFrontEnd**: A front-end Flask application that provides the user interface

The application allows users to view, create, edit, and delete community advertisements and posts.

## Dependencies

You will need to install the following locally:

- [Pipenv](https://pypi.org/project/pipenv/)
- [Visual Studio Code](https://code.visualstudio.com/download)
- [Azure Function Tools V3](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local?tabs=windows%2Ccsharp%2Cbash#install-the-azure-functions-core-tools)
- [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)

On Mac, you can do this with:

```bash
# install pipenv
brew install pipenv

# install azure-cli
brew update && brew install azure-cli

# install azure function core tools 
brew tap azure/functions
brew install azure-functions-core-tools@3
```

## Project Structure

```
.
├── NeighborlyAPI/              # Azure Functions backend
│   ├── getAdvertisements/      # Get all advertisements
│   ├── getAdvertisement/       # Get single advertisement
│   ├── createAdvertisement/    # Create new advertisement
│   ├── updateAdvertisement/    # Update advertisement
│   ├── deleteAdvertisement/    # Delete advertisement
│   ├── getPosts/               # Get all posts
│   ├── getPost/                # Get single post
│   ├── eventHubTrigger/        # Event Hub trigger for notifications
│   ├── host.json               # Azure Functions host configuration
│   ├── requirements.txt        # Python dependencies
│   └── Dockerfile              # Container configuration
├── NeighborlyFrontEnd/         # Flask frontend
│   ├── templates/              # HTML templates
│   ├── static/                 # CSS, JS, images
│   ├── app.py                  # Flask application
│   ├── settings.py             # Configuration
│   └── requirements.txt        # Python dependencies
├── sample_data/                # Sample data for database
│   ├── sampleAds.json
│   └── samplePosts.json
└── README.md
```

## Setup Instructions

### I. Creating Azure Function App

1. Create a resource group:
   ```bash
   az group create --name <RESOURCE_GROUP> --location <LOCATION>
   ```

2. Create a storage account:
   ```bash
   az storage account create --name <STORAGE_ACCOUNT> --resource-group <RESOURCE_GROUP> --location <LOCATION> --sku Standard_LRS
   ```

3. Create an Azure Function App:
   ```bash
   az functionapp create --resource-group <RESOURCE_GROUP> --consumption-plan-location <LOCATION> --runtime python --runtime-version 3.8 --functions-version 3 --name <FUNCTION_APP_NAME> --storage-account <STORAGE_ACCOUNT> --os-type Linux
   ```

4. Create a Cosmos DB Account with MongoDB API:
   ```bash
   az cosmosdb create --name <COSMOS_DB_NAME> --resource-group <RESOURCE_GROUP> --kind MongoDB
   ```

5. Create a MongoDB Database and collections:
   ```bash
   az cosmosdb mongodb database create --account-name <COSMOS_DB_NAME> --name <DATABASE_NAME> --resource-group <RESOURCE_GROUP>
   
   az cosmosdb mongodb collection create --account-name <COSMOS_DB_NAME> --database-name <DATABASE_NAME> --name advertisements --resource-group <RESOURCE_GROUP>
   
   az cosmosdb mongodb collection create --account-name <COSMOS_DB_NAME> --database-name <DATABASE_NAME> --name posts --resource-group <RESOURCE_GROUP>
   ```

6. Get the connection string:
   ```bash
   az cosmosdb keys list --name <COSMOS_DB_NAME> --resource-group <RESOURCE_GROUP> --type connection-strings
   ```

7. Import sample data using mongoimport:
   ```bash
   mongoimport --uri="<CONNECTION_STRING>" --collection=advertisements --file=sample_data/sampleAds.json --jsonArray
   mongoimport --uri="<CONNECTION_STRING>" --collection=posts --file=sample_data/samplePosts.json --jsonArray
   ```

8. Update the connection string in each function's `__init__.py` file.

### II. Testing Locally

1. Navigate to NeighborlyAPI:
   ```bash
   cd NeighborlyAPI
   pipenv install
   pipenv shell
   func start
   ```

2. Test the endpoints at `http://localhost:7071/api/<function-name>`

### III. Deploying the Azure Functions

```bash
cd NeighborlyAPI
func azure functionapp publish <FUNCTION_APP_NAME>
```

### IV. Deploying the Frontend

1. Update `NeighborlyFrontEnd/settings.py` with your Azure Function App URL
2. Deploy the frontend:
   ```bash
   cd NeighborlyFrontEnd
   pipenv install
   az webapp up --sku F1 --name <WEB_APP_NAME> --resource-group <RESOURCE_GROUP>
   ```

## Features

- View all advertisements and posts
- Create new advertisements
- Edit existing advertisements
- Delete advertisements
- MongoDB backend with Cosmos DB
- Containerized Azure Functions
- Event-driven architecture with Event Hub

## License

See LICENSE.md for details.