# Jenkins-azure
This repository will be used for automating tasks on azure using Jenkins automation tool
Have setup a Jenkins server under Resource group "jenkins-get-started-rg" With Dynamic public IP and have installed GITHUB plugin in Jenkins.

Next Step is to connect Jenkins to Azure using the below methods

Yes, you can connect Jenkins with Azure without using a service principal. There are several alternative methods to integrate Jenkins with Azure for various tasks such as deploying applications, managing resources, or storing artifacts. Here are some common methods:

### 1. **Azure DevOps Plugin for Jenkins**

You can use the Azure DevOps Plugin for Jenkins to integrate with Azure DevOps services, which include Azure Repos, Azure Pipelines, and Azure Artifacts.

- **Install the Azure DevOps Plugin:**
  1. In Jenkins, go to "Manage Jenkins" > "Manage Plugins."
  2. Go to the "Available" tab and search for "Azure DevOps Plugin."
  3. Install the plugin and restart Jenkins if necessary.

- **Configure the Azure DevOps Plugin:**
  1. In your Jenkins job configuration, add a build step for Azure DevOps.
  2. You can configure the plugin to interact with Azure DevOps services.

### 2. **Azure CLI**

You can use the Azure Command-Line Interface (CLI) to interact with Azure from Jenkins.

- **Install Azure CLI on Jenkins Server:**
  - Follow the instructions on the [Azure CLI installation guide](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli) to install the CLI on your Jenkins server.

- **Authenticate Using Azure CLI:**
  - You can authenticate using a service principal, user credentials, or managed identity if Jenkins is running on an Azure VM. Here’s an example using interactive login:
    ```sh
    az login
    ```

- **Use Azure CLI in Jenkins Pipeline:**
  - In your Jenkins pipeline, you can execute Azure CLI commands using the `sh` (for Linux) or `bat` (for Windows) steps.
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Deploy to Azure') {
                steps {
                    sh 'az login --service-principal --username <appId> --password <password> --tenant <tenantId>'
                    sh 'az group create --name myResourceGroup --location eastus'
                }
            }
        }
    }
    ```

### 3. **Azure Managed Identity (if Jenkins is running on an Azure VM)**

If your Jenkins instance is running on an Azure VM, you can use the VM's managed identity to authenticate to Azure services without needing a service principal.

- **Enable Managed Identity on the Azure VM:**
  1. Go to the Azure portal.
  2. Navigate to your Jenkins VM.
  3. Under the "Identity" section, turn on "System assigned" managed identity.

- **Assign Roles to the Managed Identity:**
  - Assign the necessary Azure roles to the managed identity to grant the required permissions.

- **Authenticate Using Managed Identity in Jenkins Pipeline:**
  - Use the Azure CLI with managed identity in your Jenkins pipeline:
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Deploy to Azure') {
                steps {
                    sh 'az login --identity'
                    sh 'az group create --name myResourceGroup --location eastus'
                }
            }
        }
    }
    ```

### 4. **Azure SDKs**

You can use Azure SDKs in your Jenkins pipeline scripts to interact with Azure services.

- **Install Azure SDK:**
  - Install the relevant Azure SDK for your preferred language (e.g., Python, Java, Node.js).

- **Use Azure SDK in Jenkins Pipeline:**
  - Write your pipeline scripts to use the SDK for interacting with Azure services.
    ```groovy
    pipeline {
        agent any
        stages {
            stage('Deploy to Azure') {
                steps {
                    script {
                        def command = '''
                        python -c "
                        from azure.mgmt.resource import ResourceManagementClient
                        from azure.identity import DefaultAzureCredential

                        credential = DefaultAzureCredential()
                        client = ResourceManagementClient(credential, 'your_subscription_id')
                        resource_group_params = {'location':'eastus'}
                        client.resource_groups.create_or_update('myResourceGroup', resource_group_params)
                        "
                        '''
                        sh command
                    }
                }
            }
        }
    }
    ```

### Summary

By using plugins, the Azure CLI, managed identities, or Azure SDKs, you can integrate Jenkins with Azure without requiring a service principal. Each method has its own use cases and benefits, so choose the one that best fits your needs and environment. Let me know if you need further details on any of these methods!
