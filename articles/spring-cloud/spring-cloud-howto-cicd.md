---
title: CI/CD voor Azure lente-Cloud
description: CI/CD voor Azure lente-Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: v-vasuke
ms.openlocfilehash: 96795315fcb0dd9b90cd55e8baa46c6a394882ce
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038832"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD voor Azure lente-Cloud

Met de hulpprogram ma's voor continue integratie en continue levering kunnen ontwikkel aars snel updates voor bestaande toepassingen implementeren met minimale inspanning en risico. Azure DevOps helpt u bij het organiseren en beheren van deze belang rijke taken. Momenteel biedt Azure lente Cloud geen specifieke Azure DevOps-invoeg toepassing.  U kunt echter uw lente-Cloud toepassingen integreren met DevOps met behulp van een [Azure cli-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops). In dit artikel wordt uitgelegd hoe u een Azure CLI-taak kunt gebruiken met Azure lente-Cloud om te integreren met Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Een Azure Resource Manager service verbinding maken

Lees [dit artikel](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) voor meer informatie over het maken van een Azure Resource Manager service verbinding met uw Azure DevOps-project. Zorg ervoor dat u hetzelfde abonnement selecteert dat u gebruikt voor uw Azure lente-Cloud service-exemplaar.

## <a name="azure-cli-task-templates"></a>Azure CLI-taak sjablonen

### <a name="deploy-artifacts"></a>Artefacten implementeren

U kunt uw projecten bouwen en implementeren met behulp van een reeks `tasks`. In dit fragment wordt eerst een Maven-taak gedefinieerd voor het bouwen van de toepassing, gevolgd door een tweede taak die het JAR-bestand implementeert met behulp van de Azure lente-Azure CLI-extensie.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implementeren vanuit bron

Het is mogelijk om rechtstreeks naar Azure te implementeren zonder een afzonderlijke build-stap.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
