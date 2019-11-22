---
title: Veilige implementatie in verschillende regio's-Azure Deployment Manager
description: Beschrijft hoe u een service implementeren via een groot aantal regio's met Azure Deployment Manager. Hier ziet u veilige implementatiemethoden om te controleren of de stabiliteit van uw implementatie te bevestigen voordat het uitrollen van naar alle regio's.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 9b0e314b3911dcd3b96dc2b5b32081bd2e9c0c0c
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74307046"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Veilige implementatie procedures met Azure Deployment Manager (open bare preview) inschakelen

Voor het implementeren van uw service in veel regio's en zorg ervoor dat deze wordt uitgevoerd zoals verwacht in elke regio, kunt u Azure Deployment Manager gebruiken voor de coördinatie van een gefaseerde implementatie van de service. Net zoals u zou voor elke Azure-implementatie doen, definieert u de resources voor uw service in [Resource Manager-sjablonen](resource-group-authoring-templates.md). Na het maken van de sjablonen, kunt u Deployment Manager gebruiken om te beschrijven van de topologie voor uw service en hoe deze moet worden geïmplementeerd.

Deployment Manager is een functie van Resource Manager. Hiermee wordt uw mogelijkheden tijdens de implementatie. Deployment Manager gebruiken wanneer u een complexe service die moet worden geïmplementeerd voor meerdere regio's. Door de implementatie van uw service te faseren, kunt u potentiële problemen opsporen voordat de service is geïmplementeerd voor alle regio's. Als u niet de extra voorzorgsmaatregelen van een gefaseerde implementatie nodig hebt, gebruikt u de standaard [implementatieopties](resource-group-template-deploy-portal.md) voor Resource Manager. Deployment Manager integreert naadloos met alle bestaande hulpprogramma's van derden die ondersteuning bieden voor Resource Manager-implementaties, zoals continue integratie en continue levering (CI/CD)-aanbiedingen.

Azure Deployment Manager is beschikbaar als preview-versie. Help ons bij het verbeteren van de functie door [feedback](https://aka.ms/admfeedback)te geven.

Deployment Manager wilt gebruiken, moet u vier bestanden maken:

* Sjabloon voor topologie
* Sjabloon rollout
* Parameterbestand voor topologie
* Parameterbestand voor implementatie

U implementeren de sjabloon topologie voordat u de implementatie-sjabloon implementeert.

Aanvullende bronnen:

- De [Naslag informatie voor Azure Deployment Manager rest API](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Zelf studie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md).
- [Zelf studie: status controle gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Een Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart)-voor beeld.

## <a name="identity-and-access"></a>Identiteit en toegang

Met Deployment Manager, een [gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) worden de implementatieacties uitgevoerd. U kunt deze identiteit maken voordat u begint met uw implementatie. Toegang tot het abonnement dat u de service implementeert en voldoende machtigingen voor het voltooien van de implementatie moet hebben. Zie voor meer informatie over de acties die zijn verleend door middel van rollen [ingebouwde rollen voor Azure-resources](../role-based-access-control/built-in-roles.md).

De identiteit moet zich op dezelfde locatie bevinden als de implementatie.

## <a name="topology-template"></a>Sjabloon voor topologie

De sjabloon topologie beschrijft de Azure-resources die gezamenlijk uw service en waar ze moeten worden geïmplementeerd. De volgende afbeelding toont de topologie voor een voorbeeld van de service:

![Hiërarchie van de service-topologie van services voor het service-eenheden](./media/deployment-manager-overview/service-topology.png)

De topologie-sjabloon bevat de volgende bronnen:

* Artefact-bron - waar uw Resource Manager-sjablonen en de parameters zijn opgeslagen
* Service-topologie - verwijst naar artefact bron
  * Services - Hiermee geeft u de locatie en Azure-abonnement-ID
    * Eenheden service - Hiermee geeft u de resourcegroep, implementatiemodus en pad naar de sjabloon en de parameterbestanden bestand

Om te begrijpen wat er gebeurt op elk niveau, is het handig zijn om te zien welke waarden die u opgeeft.

![Waarden voor elk niveau](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefact bron voor sjablonen

In uw sjabloon topologie maakt u een artefact-bron die de sjablonen en parameterbestanden bestanden bevat. De bron artefact is een manier om op te halen van de bestanden voor de implementatie. Hier ziet u een andere artefacten bron voor binaire bestanden verderop in dit artikel.

Het volgende voorbeeld ziet de algemene indeling van de bron-artefact.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "<artifact-source-name>",
    "location": "<artifact-source-location>",
    "apiVersion": "2018-09-01-preview",
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

Zie voor meer informatie, [artifactSources sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/artifactSources).

### <a name="service-topology"></a>Servicetopologie

Het volgende voorbeeld ziet de algemene indeling van de resource voor de service-topologie. U opgeven dat de resource-ID van de bron-artefact waarin de sjablonen en parameterbestanden. De topologie van de service omvat alle serviceresources. Als u wilt controleren of dat de bron-artefact beschikbaar is, de topologie van de service is afhankelijk van het.

```json
{
    "type": "Microsoft.DeploymentManager/serviceTopologies",
    "name": "<topology-name>",
    "location": "<topology-location>",
    "apiVersion": "2018-09-01-preview",
    "properties": {
        "artifactSourceId": "<resource-ID-artifact-source>"
    },
    "dependsOn": [
        "<artifact-source>"
    ],
    "resources": [
        {
            "type": "services",
            ...
        }
    ]
}
```

Zie voor meer informatie, [serviceTopologies sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/serviceTopologies).

### <a name="services"></a>Services

Het volgende voorbeeld ziet de algemene indeling van de services-resource. In elke service, moet u de locatie en Azure abonnements-ID moet worden gebruikt voor het implementeren van uw service opgeven. Als u wilt implementeren in verschillende regio's, definieert u een service voor elke regio. De service is afhankelijk van de service-topologie.

```json
{
    "type": "services",
    "name": "<service-name>",
    "location": "<service-location>",
    "apiVersion": "2018-09-01-preview",
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

Zie voor meer informatie, [services sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services).

### <a name="service-units"></a>Service-eenheden

Het volgende voorbeeld ziet de algemene indeling van de resource voor de service-eenheden. In elke eenheid van de service, geeft u de resourcegroep, de [implementatiemodus](deployment-modes.md) moet worden gebruikt voor de implementatie en het pad naar het bestand sjabloon en de parameterbestanden. Als u een relatief pad voor de sjabloon en parameters opgeeft, wordt het volledige pad is samengesteld uit de hoofdmap van de bron-artefacten. Kunt u een absoluut pad zijn voor de sjabloon en parameters opgeven, maar u verliest de mogelijkheid eenvoudig versie uw releases. De service-eenheid is afhankelijk van de service.

```json
{
    "type": "serviceUnits",
    "name": "<service-unit-name>",
    "location": "<service-unit-location>",
    "apiVersion": "2018-09-01-preview",
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

Elke sjabloon moet de gerelateerde resources die u wilt implementeren in één stap bevatten. Een service-eenheid kan bijvoorbeeld een sjabloon die alle resources voor de front-end van uw service implementeert.

Zie voor meer informatie, [serviceUnits sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits).

## <a name="rollout-template"></a>Sjabloon rollout

De sjabloon rollout beschrijft de stappen te nemen bij het implementeren van uw service. Geeft u de service-topologie voor het gebruik en de volgorde voor het implementeren van service-eenheden definiëren. Het bevat een artefact bron voor het opslaan van binaire bestanden voor de implementatie. In de sjabloon voor implementatie definieert u de volgende hiërarchie:

* Artefact bron
* Stap
* Implementeren
  * Stap-groepen
    * Implementatiebewerkingen

De volgende afbeelding ziet u de hiërarchie van de rollout-mailsjabloon:

![Hiërarchie van implementatie naar de stappen](./media/deployment-manager-overview/Rollout.png)

Elke implementatie kan veel stap groepen hebben. Elke groep stap heeft één implementatiebewerking die naar een service-eenheid in de topologie van de service verwijst.

### <a name="artifact-source-for-binaries"></a>Artefact bron voor binaire bestanden

In de sjabloon rollout maakt u een artefact bron voor de binaire bestanden die u wilt implementeren naar de service. De bron van dit artefact is vergelijkbaar met de [artefact bron voor sjablonen](#artifact-source-for-templates), behalve dat deze bevat de scripts, webpagina's, gecompileerde code of andere bestanden die nodig zijn voor uw service.

### <a name="steps"></a>Stappen

U kunt een stap uit te voeren voor of na de implementatiebewerking definiëren. Momenteel zijn alleen de stappen `wait` en ' status controle ' beschikbaar.

De stap wacht onderbreekt de implementatie voordat u doorgaat. Hiermee kunt u controleren of uw service wordt uitgevoerd zoals verwacht voordat u de volgende eenheid van de service implementeert. Het volgende voorbeeld ziet de algemene indeling van een stap wacht.

```json
{
    "apiVersion": "2018-09-01-preview",
    "type": "Microsoft.DeploymentManager/steps",
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

Maakt gebruik van de duur van de eigenschap [ISO 8601-norm](https://en.wikipedia.org/wiki/ISO_8601#Durations). Het vorige voorbeeld bevat een wachttijd van één minuut.

Zie [Introducing Health Integration-implementatie in azure Deployment Manager](./deployment-manager-health-check.md) en [zelf studie: status controle gebruiken in azure Deployment Manager](./deployment-manager-tutorial-health-check.md)voor meer informatie over de status controle.

Zie voor meer informatie, [stappen sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/steps).

### <a name="rollouts"></a>Implementaties

Als u wilt controleren of dat de bron-artefact beschikbaar is, de implementatie is afhankelijk van het. De implementatie definieert groepen van stappen voor elke service-eenheid die is geïmplementeerd. Hier kunt u acties moet uitvoeren vóór of na de implementatie. U kunt bijvoorbeeld opgeven dat de implementatie wordt gewacht nadat de service-eenheid is geïmplementeerd. U kunt de volgorde van de groepen stap definiëren.

Hiermee geeft u op het object identiteit het [gebruiker toegewezen beheerde identiteit](#identity-and-access) die acties voor de implementatie wordt uitgevoerd.

Het volgende voorbeeld ziet de algemene indeling van de implementatie.

```json
{
    "type": "Microsoft.DeploymentManager/rollouts",
    "name": "<rollout-name>",
    "location": "<rollout-location>",
    "apiVersion": "2018-09-01-preview",
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

Zie voor meer informatie, [implementaties sjabloonverwijzing](/azure/templates/Microsoft.DeploymentManager/rollouts).

## <a name="parameter-file"></a>Parameterbestand

U maakt twee parameterbestanden. Een parameterbestand wordt gebruikt bij het implementeren van de topologie van de service en de andere wordt gebruikt voor de implementatie van de implementatie. Er zijn enkele waarden die u nodig hebt om ervoor te zorgen zijn hetzelfde in beide parameterbestanden.

## <a name="containerroot-variable"></a>containerRoot variabele

Met versie implementaties, het pad naar uw artefacten wijzigingen bij elke nieuwe versie. De eerste keer dat u het pad naar een implementatie uitgevoerd mogelijk `https://<base-uri-blob-container>/binaries/1.0.0.0`. De tweede keer dat het mogelijk `https://<base-uri-blob-container>/binaries/1.0.0.1`. Deployment Manager vereenvoudigt het ophalen van pad naar de juiste hoofdmap voor de huidige implementatie met behulp van de `$containerRoot` variabele. Deze waarde wordt gewijzigd met elke versie en is niet bekend zijn vóór de implementatie.

Gebruik de `$containerRoot` variabele in het parameterbestand voor de sjabloon voor het implementeren van de Azure-resources. Tijdens de implementatie, wordt deze variabele vervangen door de werkelijke waarden van de implementatie.

Tijdens de implementatie maakt u bijvoorbeeld een artefact bron voor de binaire artefacten.

```json
{
    "type": "Microsoft.DeploymentManager/artifactSources",
    "name": "[variables('rolloutArtifactSource').name]",
    "location": "[parameters('azureResourceLocation')]",
    "apiVersion": "2018-09-01-preview",
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

U ziet dat de `artifactRoot` en `sasUri` eigenschappen. De hoofdmap van het artefact kan worden ingesteld op een waarde zoals `binaries/1.0.0.0`. De SAS-URI is de URI naar uw storage-container met een SAS-token om toegang te krijgen. Deployment Manager bouwt automatisch de waarde van de `$containerRoot` variabele. Het combineert deze waarden in de indeling `<container>/<artifactRoot>`.

Uw sjabloon en de parameterbestanden-bestand moet weten wat het juiste pad voor het ophalen van de binaire bestanden voor versies. Bijvoorbeeld, voor het implementeren van bestanden voor een web-app, maakt u de volgende parameter-bestand met de variabele $containerRoot. Moet u twee backslashes (`\\`) voor het pad omdat de eerste een escape-teken is.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "deployPackageUri": {
            "value": "$containerRoot\\helloWorldWebAppWUS.zip"
        }
    }
}
```

Gebruik vervolgens deze parameter in de sjabloon:

```json
{
    "name": "MSDeploy",
    "type": "extensions",
    "location": "[parameters('location')]",
    "apiVersion": "2015-08-01",
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

U beheren versioned implementaties met het maken van nieuwe mappen en het doorgeven van dat basiscertificaat tijdens implementatie. Het pad loopt tot en met de sjabloon die worden geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Deployment Manager. Doorgaan met het volgende artikel voor meer informatie over het implementeren van met Deployment Manager.

> [!div class="nextstepaction"]
> [Zelfstudie: Gebruik Azure Deployment Manager met Resource Manager-sjablonen](./deployment-manager-tutorial.md)
>
> [Snelstartgids: Azure-Deployment Manager in slechts enkele minuten uitproberen](https://github.com/Azure-Samples/adm-quickstart)
