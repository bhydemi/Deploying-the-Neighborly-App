# Use the official Azure Functions Python base image
FROM mcr.microsoft.com/azure-functions/python:4-python3.11

# Set environment variables
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

# Copy function app files
COPY . /home/site/wwwroot

# Install Python dependencies
RUN cd /home/site/wwwroot && \
    pip install --no-cache-dir -r requirements.txt
