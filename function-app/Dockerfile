# Use the official Microsoft Azure Functions Python base image
FROM mcr.microsoft.com/azure-functions/python:4-python3.11

# Set the root for Azure Function scripts
ENV AzureWebJobsScriptRoot=/home/site/wwwroot \
    AzureFunctionsJobHost__Logging__Console__IsEnabled=true

# Copy requirements and install them
COPY requirements.txt /
RUN pip install -r /requirements.txt

# Copy all function code into the expected directory
COPY . /home/site/wwwroot