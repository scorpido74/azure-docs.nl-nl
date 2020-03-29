---
title: CI/CD voor Azure Spring Cloud
description: CI/CD voor Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278517"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD voor Azure Spring Cloud

Dankzij continue integratie en continue leveringstools kunnen ontwikkelaars snel updates implementeren voor bestaande toepassingen met minimale inspanning en risico's. Azure DevOps helpt u bij het organiseren en beheren van deze belangrijke taken. Momenteel biedt Azure Spring Cloud geen specifieke Azure DevOps-plug-in.  U uw Spring Cloud-toepassingen echter integreren met DevOps met behulp van een [Azure CLI-taak.](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops) In dit artikel ziet u hoe u een Azure CLI-taak met Azure Spring Cloud gebruiken om te integreren met Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Een Azure Resource Manager-serviceverbinding maken

Lees [dit artikel](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) voor meer informatie over het maken van een Azure Resource Manager-serviceverbinding met uw Azure DevOps-project. Selecteer hetzelfde abonnement dat u gebruikt voor uw Azure Spring Cloud-serviceexemplaar.

## <a name="azure-cli-task-templates"></a>Azure CLI-taaksjablonen

### <a name="deploy-artifacts"></a>Artefacten implementeren

U uw projecten bouwen en `tasks`implementeren met behulp van een reeks. Dit fragment definieert eerst een Maven-taak om de toepassing te bouwen, gevolgd door een tweede taak die het JAR-bestand implementeert met behulp van de Azure Spring Cloud Azure CLI-extensie.

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
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Implementeren vanaf bron

Het is mogelijk om rechtstreeks te implementeren naar Azure zonder een aparte buildstap.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
