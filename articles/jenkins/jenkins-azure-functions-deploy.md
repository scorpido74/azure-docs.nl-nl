---
title: Implementeren naar Azure Functions met behulp van de Jenkins Azure Functions-invoeg toepassing
description: Meer informatie over het implementeren van Azure Functions met behulp van de Jenkins Azure Functions-invoeg toepassing
ms.service: jenkins
keywords: jenkins, azure, devops, java, azure functions
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: 58267c607b0c4f2eaaf242c8e0752451f8c04c9a
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882042"
---
# <a name="deploy-to-azure-functions-using-the-jenkins-azure-functions-plug-in"></a>Implementeren naar Azure Functions met behulp van de Jenkins Azure Functions-invoeg toepassing

[Azure Functions](/azure/azure-functions/) is een ‘serverloze’ rekenservice. Met Azure Functions kunt u op aanvraag code uitvoeren zonder infrastructuur te hoeven inrichten of beheren. In deze zelf studie ziet u hoe u een Java-functie implementeert om Azure Functions met behulp van de Azure Functions-invoeg toepassing.

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.
- **Jenkins-server**: als u nog geen Jenkins-server hebt geïnstalleerd, raadpleegt u het artikel [een Jenkins-server maken in azure](./install-jenkins-solution-template.md).

  > [!TIP]
  > De voor dit artikel gebruikte broncode bevindt zich in de [opslagplaats Visual Studio China van GitHub](https://github.com/VSChina/odd-or-even-function/blob/master/src/main/java/com/microsoft/azure/Function.java).

## <a name="create-a-java-function"></a>Java-functie maken

Als u een Java-functie wilt maken met de Java-runtimestack, kunt u de [Azure-portal](https://portal.azure.com) of de [Azure CLI](/cli/azure/?view=azure-cli-latest) gebruiken.

In de volgende stappen kunt u zien hoe u een Java-functie maakt met de Azure CLI:

1. Maak een resourcegroep, vervang de tijdelijke aanduiding **&lt;resource_group>** door de naam van uw resourcegroep.

    ```cli
    az group create --name <resource_group> --location eastus
    ```

1. Maak een Azure-opslagaccount en vervang de tijdelijke aanduidingen door de desbetreffende waarden.
 
    ```cli
    az storage account create --name <storage_account> --location eastus --resource-group <resource_group> --sku Standard_LRS    
    ```

1. Maak de testfunctie-app en vervang de tijdelijke aanduidingen door de desbetreffende waarden.

    ```cli
    az functionapp create --resource-group <resource_group> --consumption-plan-location eastus --name <function_app> --storage-account <storage_account>
    ```

## <a name="prepare-jenkins-server"></a>Jenkins-server voorbereiden

In de volgende stappen wordt uitgelegd hoe de Jenkins-server wordt voorbereid:

1. Implementeer een [Jenkins-server](https://aka.ms/jenkins-on-azure) in Azure. Als u nog geen exemplaar van de Jenkins-server hebt geïnstalleerd, wordt dit proces uitgelegd in het artikel [Een Jenkins-server maken in Azure](./install-jenkins-solution-template.md).

1. Meld u aan bij het Jenkins-exemplaar door middel van SSH.

1. Installeer Maven op het Jenkins-exemplaar met de volgende opdracht:

    ```terminal
    sudo apt install -y maven
    ```

1. Installeer vanaf een terminalprompt [Azure Functions Core Tools](/azure/azure-functions/functions-run-local) op het Jenkins-exemplaar met de volgende opdrachten:

    ```terminal
    wget -q https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb
    sudo dpkg -i packages-microsoft-prod.deb
    sudo apt-get update
    sudo apt-get install azure-functions-core-tools
    ```

1. Installeer de volgende invoegtoepassingen in het Jenkins-dashboard:

    - Azure Functions-invoeg toepassing
    - EnvInject-invoeg toepassing

1. Voor Jenkins is een Azure-service-principal vereist voor het verifiëren en openen van Azure-resources. Zie [Deploy to Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md) (Implementeren in Azure App Service) voor stapsgewijze instructies.

1. Voeg met behulp van de Azure-service-principal een Microsoft Azure-service-principal-referentietype aan Jenkins toe. Zie de zelfstudie [Deploy to Azure App Service](./tutorial-jenkins-deploy-web-app-azure-app-service.md#add-service-principal-to-jenkins) (Implementeren in Azure App Service).

## <a name="fork-the-sample-github-repo"></a>Splits het voor beeld GitHub opslag plaats

1. [Meld u aan bij de GitHub-opslag plaats voor de voor beeld-app voor oneven of nog eens](https://github.com/VSChina/odd-or-even-function.git).

1. Kies **Fork** in de rechterbovenhoek in GitHub.

1. Volg de aanwijzingen om uw GitHub-account te selecteren en de fork te voltooien.

## <a name="create-a-jenkins-pipeline"></a>Jenkins-pijplijn maken

In deze sectie maakt u de [Jenkins-pijplijn](https://jenkins.io/doc/book/pipeline/).

1. Maak een pijplijn in het Jenkins-dashboard.

1. Schakel **Prepare an environment for the run** in.

1. Voeg de volgende omgevingsvariabelen toe aan **Properties Content** en vervang de tijdelijke aanduidingen door de desbetreffende waarden voor uw omgeving:

    ```
    AZURE_CRED_ID=<service_principal_credential_id>
    RES_GROUP=<resource_group>
    FUNCTION_NAME=<function_name>
    ```
    
1. Selecteer in de sectie **Pipeline->Definition** de optie **Pipeline script from SCM**.

1. Voer de URL en het scriptpad (doc/resources/Jenkins/JenkinsFile) van uw GitHub Fork in om te gebruiken in het [JenkinsFile-voor beeld](https://github.com/VSChina/odd-or-even-function/blob/master/doc/resources/jenkins/JenkinsFile).

   ```
   node {
    stage('Init') {
        checkout scm
        }

    stage('Build') {
        sh 'mvn clean package'
        }

    stage('Publish') {
        azureFunctionAppPublish appName: env.FUNCTION_NAME, 
                                azureCredentialsId: env.AZURE_CRED_ID, 
                                filePath: '**/*.json,**/*.jar,bin/*,HttpTrigger-Java/*', 
                                resourceGroup: env.RES_GROUP, 
                                sourceDirectory: 'target/azure-functions/odd-or-even-function-sample'
        }
    }
    ```

## <a name="build-and-deploy"></a>Bouwen en implementeren

U kunt nu de Jenkins-taak uitvoeren.

1. Haal eerst de autorisatiesleutel op via de instructies in het artikel [HTTP-triggers en -bindingen in Azure Functions](/azure/azure-functions/functions-bindings-http-webhook#authorization-keys).

1. Voer in de browser de URL naar de app in. Vervang de tijdelijke aanduidingen door de desbetreffende waarden en geef een numerieke waarde op voor **&lt;input_number>** als invoer voor de Java-functie.

    ```
    https://<function_app>.azurewebsites.net/api/HttpTrigger-Java?code=<authorization_key>&number=<input_number>
    ```
1. De resultaten die u te zien krijgt, zijn soortgelijk aan de volgende voorbeelduitvoer (waarin een oneven getal (365) als een testwaarde is gebruikt):

    ```output
    The number 365 is Odd.
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijder dan de resources die u hebt gemaakt met de volgende stap:

```cli
az group delete -y --no-wait -n <resource_group>
```

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resource voor meer informatie Azure Functions:
> [!div class="nextstepaction"]
> [Documentatie van Azure Functions](/azure/azure-functions/)
