---
title: Veilige implementatie in verschillende regio's-Azure Deployment Manager
description: Hierin wordt beschreven hoe u een service implementeert over veel regio's met Azure Deployment Manager. Het bevat veilige implementatie procedures voor het controleren van de stabiliteit van uw implementatie voordat deze naar alle regio's wordt uitgevouwen.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: a91623d22a921b6285723af2b4ca1411b9cf0bab
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677879"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Veilige implementatie procedures met Azure Deployment Manager (open bare preview) inschakelen

Als u uw service wilt implementeren in een groot aantal regio's en er zeker van wilt zijn dat deze wordt uitgevoerd zoals verwacht in elke regio, kunt u Azure Deployment Manager gebruiken voor het coördineren van een gefaseerde implementatie van de service. Net zoals u zou doen voor elke Azure-implementatie, definieert u de resources voor uw service in [Resource Manager-sjablonen](template-syntax.md). Nadat u de sjablonen hebt gemaakt, gebruikt u Deployment Manager om de topologie voor uw service en de implementatie ervan te beschrijven.

Deployment Manager is een functie van Resource Manager. Uw mogelijkheden worden uitgebreid tijdens de implementatie. Gebruik Deployment Manager wanneer u een complexe service hebt die moet worden geïmplementeerd in verschillende regio's. Door de implementatie van uw service te faseren, kunt u potentiële problemen opsporen voordat de service is geïmplementeerd voor alle regio's. Als u de extra voorzorgsmaatregelen van een gefaseerde implementatie niet nodig hebt, gebruikt u de standaard [implementatie opties](deploy-portal.md) voor Resource Manager. Deployment Manager naadloos kan worden geïntegreerd met alle bestaande hulpprogram ma's van derden die ondersteuning bieden voor Resource Manager-implementaties, zoals continue integratie en aanbiedingen voor continue levering (CI/CD).

Azure Deployment Manager is beschikbaar als preview-versie. Help ons bij het verbeteren van de functie door [feedback](https://aka.ms/admfeedback)te geven.

Als u Deployment Manager wilt gebruiken, moet u vier bestanden maken:

* Topologie sjabloon
* Implementatie sjabloon
* Parameter bestand voor topologie
* Parameter bestand voor implementatie

U implementeert de topologie sjabloon voordat u de implementatie sjabloon implementeert.

Aanvullende bronnen:

- De [Naslag informatie voor Azure Deployment Manager rest API](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Zelf studie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md).
- [Zelf studie: status controle gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Een Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart)-voor beeld.

## <a name="identity-and-access"></a>Identiteit en toegang

Met Deployment Manager worden de implementatie acties uitgevoerd door een door de [gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) . U maakt deze identiteit voordat u de implementatie start. Het moet toegang hebben tot het abonnement waarmee u de service implementeert, en voldoende machtigingen hebben om de implementatie te volt ooien. Zie [ingebouwde rollen voor Azure-resources](../../role-based-access-control/built-in-roles.md)voor meer informatie over de acties die via rollen worden verleend.

De identiteit moet zich op dezelfde locatie bevinden als de implementatie.

## <a name="topology-template"></a>Topologie sjabloon

De topologie sjabloon beschrijft de Azure-resources waaruit uw service is opgebouwd en waar u deze kunt implementeren. De volgende afbeelding toont de topologie voor een voorbeeld service:

![Hiërarchie van service topologie naar Services voor service-eenheden](./media/deployment-manager-overview/service-topology.png)

De topologie sjabloon bevat de volgende bronnen:

* Artefact Bron: waar uw Resource Manager-sjablonen en-para meters zijn opgeslagen
* Service topologie: verwijst naar artefact bron
  * Services: Hiermee geeft u de locatie en de ID van het Azure-abonnement op
    * Service-eenheden: Hiermee geeft u de resource groep, de implementatie modus en het pad naar de sjabloon en het parameter bestand op

Om te begrijpen wat er gebeurt op elk niveau, is het handig om te zien welke waarden u opgeeft.

![Waarden voor elk niveau](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefact bron voor sjablonen

In uw topologie sjabloon maakt u een artefact bron die de sjablonen en parameter bestanden bevat. De artefact bron is een manier om de bestanden voor implementatie te halen. Verderop in dit artikel ziet u een andere artefact bron voor binaire bestanden.

In het volgende voor beeld ziet u de algemene indeling van de artefact bron.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "<artifact-source-name>",
  "location": "<artifact-source-location>",
  "properties": {
    "sourceType": "AzureStorage",
    "artifactRoot": "<root-folder-for-templates>",
    "authentication": {
      "type": "SAS",
      "properties": {
        "sasUri": "<SAS-URI-for-storage-container>"
      }
    }
  }
}
```

Zie [artifactSources-sjabloon verwijzing](/azure/templates/Microsoft.DeploymentManager/artifactSources)voor meer informatie.

### <a name="service-topology"></a>Servicetopologie

In het volgende voor beeld ziet u de algemene indeling van de resource van de service topologie. U geeft de bron-ID van de artefact bron op die de sjablonen en parameter bestanden bevat. De service topologie omvat alle service bronnen. Om ervoor te zorgen dat de artefact bron beschikbaar is, is de service topologie hiervan afhankelijk.

```json
{
  "type": "Microsoft.DeploymentManager/serviceTopologies",
  "apiVersion": "2018-09-01-preview",
  "name": "<topology-name>",
  "location": "<topology-location>",
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "artifactSourceId": "<resource-ID-artifact-source>"
  },
  "resources": [
    {
      "type": "services",
        ...
        }
    ]
}
```

Zie [serviceTopologies-sjabloon verwijzing](/azure/templates/Microsoft.DeploymentManager/serviceTopologies)voor meer informatie.

### <a name="services"></a>Services

In het volgende voor beeld ziet u de algemene indeling van de resource Services. In elke service geeft u de locatie en de ID van het Azure-abonnement op die moeten worden gebruikt voor het implementeren van uw service. Als u wilt implementeren in verschillende regio's, definieert u een service voor elke regio. De service is afhankelijk van de service topologie.

```json
{
  "type": "services",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-name>",
  "location": "<service-location>",
  "dependsOn": [
      "<service-topology>"
  ],
  "properties": {
    "targetSubscriptionId": "<subscription-ID>",
    "targetLocation": "<location-of-deployed-service>"
  },
  "resources": [
    {
      "type": "serviceUnits",
            ...
        }
    ]
}
```

Zie Naslag informatie voor de [Services-sjabloon](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Service-eenheden

In het volgende voor beeld ziet u de algemene indeling van de resource service-eenheden. In elke service-eenheid geeft u de resource groep, de [implementatie modus](deployment-modes.md) die moet worden gebruikt voor de implementatie en het pad naar de sjabloon en het parameter bestand op. Als u een relatief pad voor de sjabloon en de para meters opgeeft, wordt het volledige pad samengesteld uit de hoofdmap in de bron voor artefacten. U kunt een absoluut pad opgeven voor de sjabloon en de para meters, maar u verliest de mogelijkheid om eenvoudig uw releases te versies. De service-eenheid is afhankelijk van de service.

```json
{
  "type": "serviceUnits",
  "apiVersion": "2018-09-01-preview",
  "name": "<service-unit-name>",
  "location": "<service-unit-location>",
  "dependsOn": [
    "<service>"
  ],
  "tags": {
    "serviceType": "Service West US Web App"
  },
  "properties": {
    "targetResourceGroup": "<resource-group-name>",
    "deploymentMode": "Incremental",
    "artifacts": {
      "templateArtifactSourceRelativePath": "<relative-path-to-template>",
      "parametersArtifactSourceRelativePath": "<relative-path-to-parameter-file>"
    }
  }
}
```

Elke sjabloon moet de gerelateerde resources bevatten die u in één stap wilt implementeren. Een service-eenheid kan bijvoorbeeld een sjabloon hebben waarmee alle resources voor de front-end van uw service worden geïmplementeerd.

Zie [serviceUnits-sjabloon verwijzing](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)voor meer informatie.

## <a name="rollout-template"></a>Implementatie sjabloon

In de implementatie sjabloon worden de stappen beschreven die u moet uitvoeren bij het implementeren van uw service. U geeft de service topologie op die moet worden gebruikt en definieert de volg orde voor het implementeren van service-eenheden. Het bevat een artefact bron voor het opslaan van binaire bestanden voor de implementatie. In uw implementatie sjabloon definieert u de volgende hiërarchie:

* Artefact bron
* Stap
* Houden
  * Stap groepen
    * Implementatie bewerkingen

In de volgende afbeelding ziet u de hiërarchie van de implementatie sjabloon:

![Hiërarchie van implementatie naar stappen](./media/deployment-manager-overview/Rollout.png)

Elke implementatie kan veel stap groepen bevatten. Elke stap groep heeft één implementatie bewerking die verwijst naar een service-eenheid in de service topologie.

### <a name="artifact-source-for-binaries"></a>Artefact bron voor binaire bestanden

In de implementatie sjabloon maakt u een artefact bron voor de binaire bestanden die u moet implementeren voor de service. Deze artefact bron is vergelijkbaar met de [artefact bron voor sjablonen](#artifact-source-for-templates), behalve dat deze de scripts, webpagina's, gecompileerde code of andere bestanden bevat die nodig zijn voor uw service.

### <a name="steps"></a>Stappen

U kunt een stap definiëren die vóór of na de implementatie bewerking moet worden uitgevoerd. Op dit moment zijn alleen de stappen `wait` ' status controle ' beschikbaar.

Tijdens de wacht stap wordt de implementatie gepauzeerd voordat u doorgaat. U kunt controleren of de service wordt uitgevoerd zoals verwacht voordat u de volgende service-eenheid implementeert. In het volgende voor beeld ziet u de algemene notatie van een wacht stap.

```json
{
    "type": "Microsoft.DeploymentManager/steps",
    "apiVersion": "2018-09-01-preview",
    "name": "waitStep",
    "location": "<step-location>",
    "properties": {
        "stepType": "wait",
        "attributes": {
          "duration": "PT1M"
        }
    }
},
```

De eigenschap duration maakt gebruik van [ISO 8601-standaard](https://en.wikipedia.org/wiki/ISO_8601#Durations). In het voor gaande voor beeld wordt een wacht tijd van één minuut opgegeven.

Zie [Introducing Health Integration-implementatie in azure Deployment Manager](./deployment-manager-health-check.md) en [zelf studie: status controle gebruiken in azure Deployment Manager](./deployment-manager-tutorial-health-check.md)voor meer informatie over de status controle.

Zie voor meer informatie [stappen sjabloon verwijzing](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Implementaties

Om ervoor te zorgen dat de artefact bron beschikbaar is, is de implementatie hiervan afhankelijk. De implementatie definieert stappen groepen voor elke service-eenheid die wordt geïmplementeerd. U kunt acties definiëren die moeten worden uitgevoerd vóór of na de implementatie. U kunt bijvoorbeeld opgeven dat de implementatie wacht nadat de service-eenheid is geïmplementeerd. U kunt de volg orde van de stap groepen definiëren.

Het identiteits object geeft de door de [gebruiker toegewezen beheerde identiteit](#identity-and-access) op waarmee de implementatie acties worden uitgevoerd.

In het volgende voor beeld wordt de algemene indeling van de implementatie weer gegeven.

```json
{
  "type": "Microsoft.DeploymentManager/rollouts",
  "apiVersion": "2018-09-01-preview",
  "name": "<rollout-name>",
  "location": "<rollout-location>",
  "Identity": {
    "type": "userAssigned",
    "identityIds": [
      "<managed-identity-ID>"
    ]
  },
  "dependsOn": [
    "<artifact-source>"
  ],
  "properties": {
    "buildVersion": "1.0.0.0",
    "artifactSourceId": "<artifact-source-ID>",
    "targetServiceTopologyId": "<service-topology-ID>",
    "stepGroups": [
      {
        "name": "stepGroup1",
        "dependsOnStepGroups": ["<step-group-name>"],
        "preDeploymentSteps": ["<step-ID>"],
        "deploymentTargetId":
          "<service-unit-ID>",
        "postDeploymentSteps": ["<step-ID>"]
      },
            ...
        ]
    }
}
```

Zie de naslag informatie voor de implementatie van een [sjabloon](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameterbestand

U maakt twee parameter bestanden. Er wordt één parameter bestand gebruikt bij het implementeren van de service topologie en de andere wordt gebruikt voor de implementatie van implementeren. Er zijn enkele waarden die u nodig hebt om ervoor te zorgen dat beide para meters hetzelfde zijn.

## <a name="containerroot-variable"></a>containerRoot-variabele

Bij implementaties met een versie, wordt het pad naar uw artefacten gewijzigd met elke nieuwe versie. De eerste keer dat u een implementatie uitvoert, is het pad mogelijk `https://<base-uri-blob-container>/binaries/1.0.0.0` . De tweede keer is het mogelijk `https://<base-uri-blob-container>/binaries/1.0.0.1` . Deployment Manager vereenvoudigt het ophalen van het juiste hoofdpad voor de huidige implementatie met behulp van de `$containerRoot` variabele. Deze waarde verandert in elke versie en is niet bekend vóór de implementatie.

Gebruik de `$containerRoot` variabele in het parameter bestand voor de sjabloon om de Azure-resources te implementeren. Tijdens de implementatie wordt deze variabele vervangen door de werkelijke waarden van de implementatie.

Tijdens de implementatie maakt u bijvoorbeeld een artefact bron voor de binaire artefacten.

```json
{
  "type": "Microsoft.DeploymentManager/artifactSources",
  "apiVersion": "2018-09-01-preview",
  "name": "[variables('rolloutArtifactSource').name]",
  "location": "[parameters('azureResourceLocation')]",
  "properties": {
    "sourceType": "AzureStorage",
      "artifactRoot": "[parameters('binaryArtifactRoot')]",
      "authentication" :
        {
          "type": "SAS",
          "properties": {
            "sasUri": "[parameters('artifactSourceSASLocation')]"
          }
        }
  }
},
```

Let op `artifactRoot` de `sasUri` Eigenschappen and. De basis voor artefacten kan worden ingesteld op een waarde zoals `binaries/1.0.0.0` . De SAS-URI is de URI naar uw opslag container met een SAS-token voor toegang. Deployment Manager maakt automatisch de waarde van de `$containerRoot` variabele. Deze waarden worden gecombineerd in de indeling `<container>/<artifactRoot>` .

Uw sjabloon en parameter bestand moeten het juiste pad kennen voor het ophalen van de versie binaire bestanden. Als u bijvoorbeeld bestanden voor een web-app wilt implementeren, maakt u het volgende parameter bestand met de variabele $containerRoot. U moet twee backslashes ( `\\` ) voor het pad gebruiken, omdat de eerste een escape-teken is.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "deployPackageUri": {
      "value": "$containerRoot\\helloWorldWebAppWUS.zip"
    }
  }
}
```

Gebruik vervolgens die para meter in uw sjabloon:

```json
{
  "name": "MSDeploy",
  "apiVersion": "2015-08-01",
  "type": "extensions",
  "location": "[parameters('location')]",
  "dependsOn": [
    "[concat('Microsoft.Web/sites/', parameters('WebAppName'))]"
  ],
  "tags": {
    "displayName": "WebAppMSDeploy"
  },
  "properties": {
    "packageUri": "[parameters('deployPackageURI')]"
  }
}
```

U beheert implementaties met een versie door nieuwe mappen te maken en deze basis tijdens de implementatie door te geven. Het pad loopt door naar de sjabloon die de resources implementeert.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Deployment Manager. Ga door naar het volgende artikel voor meer informatie over het implementeren met Deployment Manager.

> [!div class="nextstepaction"]
> [Zelf studie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md)
>
> [Snelstartgids: Azure-Deployment Manager in slechts enkele minuten uitproberen](https://github.com/Azure-Samples/adm-quickstart)
