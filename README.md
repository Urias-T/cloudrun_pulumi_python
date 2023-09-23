# cloudrun_pulumi_python
A Python Program to deploy a containerized service on Cloud Run using Pulumi

To use this effectively, you need to:
- Create a Pulumi account and setup Pulumi on your local machine.
- Download and install Docker and the Docker CLI.
- Download the Google Cloud SDK.

## Steps:

- Create a GCP Project:
  - You can do this easily from the [Google Cloud Console](console.google.com)
  - Within your project, you should (following best practices), create a [service account](https://console.cloud.google.com/iam-admin/serviceaccounts?_ga=2.30837687.920064379.1695414343-151387242.1691355797) for your Pulumi project and assign IAM roles to this account. The roles include:
      - Cloud Run Admin
      - Artifact Registry Admin
      - Editor
  - Create access credentials in form of JSON key for your service account and download to your local machine. (I recommend storing it in a **secrets** directory within your project and ignore with **.gitignore** if you're deploying to a remote repo.

- Create a Pulumi project:
  - Run the following command in your terminal (I recommend a bash terminal):
      ```
      mkdir my-container-service && cd my-container-service
      pululmi new container-gcp-python
      ```
  
    - This would guide you through the steps to setup your Pulumi project locally as well as configurations for your container service and the Google Cloud Project it would be deployed into.
    - It would then go ahead to save the specified configurations, create a virtual environment for your project and install dependencies.



- When you've done it all, you can go ahead and build your Python service in the **app** directory.
  - You may also set a custom service name for proper identification:
    - To do this, locate this block and pass in a value to the ```name``` parameter of ```cloudrun.ServiceArgs```:
      ```python
        service = cloudrun.Service(
            "service",
            cloudrun.ServiceArgs(
                location=location,
        ```
      
 - Authorize Pulumi with your service account credentials. 
    - To do this, simply run ```pulumi config set gcp:credentials <your-credentials> # filepath to your credentials JSON```.

- Authorize Docker with same credentials:
  - To do this, run: 
      ```
      Get-Content KEY-FILE |
      docker login -u _json_key --password-stdin https://LOCATION-docker.pkg.dev
      ```

      - Note:
        - KEY-FILE: path to JSON credentials.
        - LOCATION: location of image repository.
  
- At this point you're ready to deploy. It's as easy as running: ```pulumi up``` in your terminal.

## Advanced tip:
- To set up a production environment, you would need another Pulumi "stack". This can be created by running
   ```pulumi stack init prod``` in your terminal. A **pulumi.prod.yaml** file would be created. You can then configure your production environment details in it.


## References:
- https://www.pulumi.com/templates/container-service/gcp/
- https://www.pulumi.com/registry/packages/gcp/installation-configuration/#credentials
- https://cloud.google.com/artifact-registry/docs/docker/authentication#windows_4
- https://www.pulumi.com/registry/packages/gcp/api-docs/cloudrun/service/
- https://www.pulumi.com/learn/building-with-pulumi/understanding-stacks/#:~:text=To%20create%20a%20new%20stack,it%20as%20the%20active%20stack.


