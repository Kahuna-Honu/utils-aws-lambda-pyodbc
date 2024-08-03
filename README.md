# AWS Lambda - Python 3.9 - pyodbc - MSSQL 18

This repository contains a Dockerfile to build a Docker image that installs the dependencies required to install and compile pyodbc. It then copies the compiled files into a ZIP archive, which will be used to create a layer for a Lambda function. Below are the steps to build and use the layer in AWS Lambda.

## Files in this repo

- **Dockerfile**: Configuration file to build the Docker image.
- **layer.zip**: ZIP file generated that contains the layer for Lambda (this file will be generated during the build process).

## Instructions

### 1. Build docker image

Build the Docker image using the `Dockerfile` included in the current directory. Run the following command:

```powershell
docker build -t pyodbc-mssql .
```


### 2. Extract zip file
Once the Docker image has been built, run the container to copy the generated `layer.zip` file. Execute the following command:

```powershell
docker run --rm --entrypoint bash -v "${PWD}:/local" pyodbc-mssql -c "cp /layer.zip /local/"
```
This will copy `layer.zip` to the current directory on your local machine.

### 3. Create Layer on Lambda
    1.Go to Lambda.
    2.In the navigation panel, select Layers.
    3.Click on Create layer.
    4.Enter a name for the layer.
    5.Select Upload a .zip file and upload the layer.zip file you just extracted.
    6.Set the layer runtime to Python 3.9.
    7.Make sure the architecture is set to x86/64.

### 4. Assign new layer
- In the AWS Lambda console, select the function to which you want to add the layer.
- In the Layers section, click on Add a layer.
- Select Custom layers and choose the layer you created in the previous step.
- Save the changes.

### 5. PROFIT
Your Lambda function can now use pyodbc to connect to MSSQL Server (Driver 18).
```python
import pyodbc

...
```

_Notes_:
- _At the root of this project, there is a build of the same, which can be useful as a reference or for direct use._
- _You can edit the Dockerfile to change the versions of the MSSQL driver or Python._