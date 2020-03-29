---
title: Veilige implementatie in verschillende regio's - Azure Deployment Manager
description: Beschrijft hoe u een service voor veel regio's implementeert met Azure Deployment Manager. Het toont veilige implementatiepraktijken om de stabiliteit van uw implementatie te controleren voordat u naar alle regio's wordt uitgerold.
ms.topic: conceptual
ms.date: 11/21/2019
ms.custom: seodec18
ms.openlocfilehash: 424cd79a6c63200e1f101cf178b1fd2c9083161e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76152524"
---
# <a name="enable-safe-deployment-practices-with-azure-deployment-manager-public-preview"></a>Veilige implementatieprocedures inschakelen met Azure Deployment Manager (Openbare voorbeeld)

Als u uw service in veel regio's wilt implementeren en ervoor wilt zorgen dat deze in elke regio wordt uitgevoerd zoals verwacht, u Azure Deployment Manager gebruiken om een gefaseerde implementatie van de service te coördineren. Net zoals u dat voor elke Azure-implementatie zou doen, definieert u de resources voor uw service in [Resource Manager-sjablonen.](template-syntax.md) Nadat u de sjablonen hebt gemaakt, gebruikt u Deployment Manager om de topologie voor uw service te beschrijven en hoe deze moet worden uitgerold.

Deployment Manager is een functie van Resource Manager. Het breidt uw mogelijkheden tijdens de implementatie uit. Gebruik Deployment Manager wanneer u een complexe service hebt die moet worden geïmplementeerd in verschillende regio's. Door de implementatie van uw service te faseren, kunt u potentiële problemen opsporen voordat de service is geïmplementeerd voor alle regio's. Als u de extra voorzorgsmaatregelen voor een gefaseerde implementatie niet nodig hebt, gebruikt u de [standaardimplementatieopties](deploy-portal.md) voor Resource Manager. Deployment Manager integreert naadloos met alle bestaande tools van derden die Resource Manager-implementaties ondersteunen, zoals continue integratie en ci/cd-aanbiedingen (continuous delivery).

Azure Deployment Manager bevindt zich in preview. Help ons de functie te verbeteren door feedback te [geven.](https://aka.ms/admfeedback)

Als u Deployment Manager wilt gebruiken, moet u vier bestanden maken:

* Sjabloon topologie
* Sjabloon voor implementatie
* Parameterbestand voor topologie
* Parameterbestand voor implementatie

U implementeert de topologiesjabloon voordat u de implementatiesjabloon implementeert.

Aanvullende bronnen:

- De [verwijzing naar de REST API van Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Zelfstudie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md).
- [Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
- [Een voorbeeld van Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

## <a name="identity-and-access"></a>Identiteit en toegang

Met Deployment Manager voert een [door de gebruiker toegewezen beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md) de implementatieacties uit. U maakt deze identiteit voordat u met de implementatie begint. Het moet toegang hebben tot het abonnement waarop u de service implementeert en voldoende toestemming hebben om de implementatie te voltooien. Zie [Ingebouwde rollen voor Azure-resources voor](../../role-based-access-control/built-in-roles.md)informatie over de acties die via rollen worden verleend.

De identiteit moet zich op dezelfde locatie als de implementatie bevinden.

## <a name="topology-template"></a>Sjabloon topologie

De topologiesjabloon beschrijft de Azure-resources waaruit uw service bestaat en waar u deze implementeren. In de volgende afbeelding wordt de topologie voor een voorbeeldservice weergegeven:

![Hiërarchie van servicetopologie tot services tot service-eenheden](./media/deployment-manager-overview/service-topology.png)

De topologiesjabloon bevat de volgende bronnen:

* Artefactbron - waar uw Resource Manager-sjablonen en -parameters zijn opgeslagen
* Servicetopologie - wijst naar artefactbron
  * Services - geeft locatie- en Azure-abonnements-id op
    * Service-eenheden - geeft resourcegroep, implementatiemodus en pad naar sjabloon- en parameterbestand op

Om te begrijpen wat er op elk niveau gebeurt, is het handig om te zien welke waarden u opgeeft.

![Waarden voor elk niveau](./media/deployment-manager-overview/topology-values.png)

### <a name="artifact-source-for-templates"></a>Artefactbron voor sjablonen

In uw topologiesjabloon maakt u een artefactbron met de sjablonen en parametersbestanden. De artefactbron is een manier om de bestanden op te halen voor implementatie. U ziet later in dit artikel een andere artefactbron voor binaire bestanden.

In het volgende voorbeeld wordt de algemene indeling van de artefactbron weergegeven.

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

Zie [verwijzing naar de sjabloon artefactBronnen voor](/azure/templates/Microsoft.DeploymentManager/artifactSources)meer informatie .

### <a name="service-topology"></a>Servicetopologie

In het volgende voorbeeld wordt de algemene indeling van de servicetopologiebron weergegeven. U verstrekt de bron-ID van de artefactbron die de sjablonen en parameterbestanden bevat. De servicetopologie bevat alle servicebronnen. Om ervoor te zorgen dat de artefactbron beschikbaar is, is de servicetopologie afhankelijk van deze bron.

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

Zie [verwijzing naar serviceTopologieën voor](/azure/templates/Microsoft.DeploymentManager/serviceTopologies)meer informatie .

### <a name="services"></a>Services

In het volgende voorbeeld wordt de algemene indeling van de servicesbron weergegeven. In elke service geeft u de locatie- en Azure-abonnements-ID die u gebruiken voor het implementeren van uw service. Als u naar verschillende regio's wilt implementeren, definieert u een service voor elke regio. De service is afhankelijk van de servicetopologie.

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

Zie verwijzing naar [de sjabloon services voor](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services)meer informatie .

### <a name="service-units"></a>Service-eenheden

In het volgende voorbeeld wordt de algemene indeling van de resource service-eenheden weergegeven. In elke service-eenheid geeft u de resourcegroep, de [implementatiemodus](deployment-modes.md) op die moet worden gebruikt voor implementatie en het pad naar het sjabloon- en parameterbestand. Als u een relatief pad opgeeft voor de sjabloon en parameters, wordt het volledige pad opgebouwd uit de hoofdmap in de bron van artefacten. U een absoluut pad opgeven voor de sjabloon en parameters, maar u verliest de mogelijkheid om uw releases eenvoudig te versionen. De service-eenheid is afhankelijk van de service.

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

Zie [verwijzing naar de sjabloonserviceEenheden voor](/azure/templates/Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits)meer informatie.

## <a name="rollout-template"></a>Sjabloon voor implementatie

De implementatiesjabloon beschrijft de stappen die moeten worden genomen bij het implementeren van uw service. U geeft de servicetopologie op die u wilt gebruiken en definieert de volgorde voor het implementeren van service-eenheden. Het bevat een artefactbron voor het opslaan van binaire bestanden voor de implementatie. In uw implementatiesjabloon definieert u de volgende hiërarchie:

* Artefactbron
* Stap
* Uitrol
  * Stappengroepen
    * Implementatiebewerkingen

In de volgende afbeelding ziet u de hiërarchie van de uitrolsjabloon:

![Hiërarchie van implementatie naar stappen](./media/deployment-manager-overview/Rollout.png)

Elke implementatie kan veel stapgroepen hebben. Elke stapgroep heeft één implementatiebewerking die verwijst naar een service-eenheid in de servicetopologie.

### <a name="artifact-source-for-binaries"></a>Artefactbron voor binaire bestanden

In de uitrolsjabloon maakt u een artefactbron voor de binaire bestanden die u naar de service moet implementeren. Deze artefactbron is vergelijkbaar met de [artefactbron voor sjablonen,](#artifact-source-for-templates)behalve dat deze de scripts, webpagina's, gecompileerde code of andere bestanden bevat die uw service nodig heeft.

### <a name="steps"></a>Stappen

U een stap definiëren die u voor of na uw implementatiebewerking wilt uitvoeren. Momenteel zijn `wait` alleen de stap en de 'healthCheck'-stap beschikbaar.

Met de wachtstap wordt de implementatie onderbroken voordat u verdergaat. Hiermee u controleren of uw service wordt uitgevoerd zoals verwacht voordat u de volgende service-eenheid implementeert. In het volgende voorbeeld wordt de algemene indeling van een wachtstap weergegeven.

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

De eigenschap duur maakt gebruik [van ISO 8601-standaard](https://en.wikipedia.org/wiki/ISO_8601#Durations). In het voorgaande voorbeeld wordt een wachttijd van één minuut opgegeven.

Zie [Implementatieimplementatie voor statusintegratie introduceren in Azure Deployment Manager](./deployment-manager-health-check.md) en [Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)voor meer informatie over de stap statuscontrole.

Zie [sjabloonverwijzing stappen voor](/azure/templates/Microsoft.DeploymentManager/steps)meer informatie .

### <a name="rollouts"></a>Implementaties

Om ervoor te zorgen dat de artefactbron beschikbaar is, is de implementatie afhankelijk van deze bron. De implementatie definieert stappengroepen voor elke service-eenheid die wordt geïmplementeerd. U acties definiëren die u voor of na de implementatie moet uitvoeren. U bijvoorbeeld opgeven dat de implementatie wacht nadat de service-eenheid is geïmplementeerd. U de volgorde van de stapgroepen definiëren.

Het identiteitsobject geeft de door de [gebruiker toegewezen beheerde identiteit](#identity-and-access) op die de implementatieacties uitvoert.

In het volgende voorbeeld wordt de algemene indeling van de implementatie weergegeven.

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

Zie [verwijzing naar de sjabloon voor implementaties voor](/azure/templates/Microsoft.DeploymentManager/rollouts)meer informatie .

## <a name="parameter-file"></a>Parameterbestand

U maakt twee parameterbestanden. Een parameterbestand wordt gebruikt bij het implementeren van de servicetopologie en het andere bestand wordt gebruikt voor de implementatie van de implementatie. Er zijn een aantal waarden die u nodig hebt om ervoor te zorgen zijn hetzelfde in beide parameterbestanden.

## <a name="containerroot-variable"></a>containerRoot, variabele

Bij versie-implementaties verandert het pad naar uw artefacten met elke nieuwe versie. De eerste keer dat u een `https://<base-uri-blob-container>/binaries/1.0.0.0`implementatie uitvoert, kan het pad zijn. De tweede keer `https://<base-uri-blob-container>/binaries/1.0.0.1`zou het kunnen zijn. Deployment Manager vereenvoudigt het verkrijgen van het juiste `$containerRoot` hoofdpad voor de huidige implementatie met behulp van de variabele. Deze waarde verandert met elke versie en is niet bekend voor de implementatie.

Gebruik `$containerRoot` de variabele in het parameterbestand voor sjabloon om de Azure-resources te implementeren. Tijdens de implementatiewordt deze variabele vervangen door de werkelijke waarden van de implementatie.

Tijdens de implementatie maakt u bijvoorbeeld een artefactbron voor de binaire artefacten.

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

Let `artifactRoot` op `sasUri` de eigenschappen en eigenschappen. De artefactwortel kan worden ingesteld `binaries/1.0.0.0`op een waarde als . De SAS URI is de URI naar uw opslagcontainer met een SAS-token voor toegang. Deployment Manager construeert automatisch de `$containerRoot` waarde van de variabele. Het combineert deze `<container>/<artifactRoot>`waarden in het formaat .

Uw sjabloon en parameterbestand moeten het juiste pad kennen voor het verkrijgen van de versies. Als u bijvoorbeeld bestanden voor een web-app wilt implementeren, maakt u het volgende parameterbestand met de variabele $containerRoot. U moet twee backslashes ()`\\`gebruiken voor het pad, omdat het eerste een escape-teken is.

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

Gebruik die parameter vervolgens in uw sjabloon:

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

U beheert versies door nieuwe mappen te maken en die root door te geven tijdens de implementatie. Het pad stroomt door naar de sjabloon waarmee de resources worden geïmplementeerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hoorde u over Deployment Manager. Ga naar het volgende artikel om te leren hoe u implementeren met Deployment Manager.

> [!div class="nextstepaction"]
> [Zelfstudie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md)
>
> [Snelstart: Azure Deployment Manager in slechts een paar minuten uitproberen](https://github.com/Azure-Samples/adm-quickstart)
