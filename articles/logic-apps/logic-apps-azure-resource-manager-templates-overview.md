---
title: Overzicht - Implementatie voor Azure Logic Apps automatiseren
description: Meer informatie over Azure Resource Manager-sjablonen om de implementatie voor Azure Logic Apps te automatiseren
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: 7a99038f41043b899886c7161f9b12c77c807c4c
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641818"
---
# <a name="overview-automate-deployment-for-azure-logic-apps-by-using-azure-resource-manager-templates"></a>Overzicht: Implementatie voor Azure Logic Apps automatiseren met Azure Resource Manager-sjablonen

Wanneer u klaar bent om het maken en implementeren van uw logische app te automatiseren, u de onderliggende werkstroomdefinitie van uw logische app uitbreiden naar een [Azure Resource Manager-sjabloon.](../azure-resource-manager/management/overview.md) Met deze sjabloon worden de infrastructuur, resources, parameters en andere informatie voor het inrichten en implementeren van uw logische app gedefinieerd. Door parameters te definiëren voor waarden die variëren bij implementatie, ook wel *parameterisering*genoemd, u logische apps herhaaldelijk en consistent implementeren op basis van verschillende implementatiebehoeften.

Als u bijvoorbeeld implementeert naar omgevingen voor ontwikkeling, testen en productie, gebruikt u waarschijnlijk verschillende verbindingstekenreeksen voor elke omgeving. U sjabloonparameters declareren die verschillende verbindingstekenreeksen accepteren en deze tekenreeksen vervolgens opslaan in een afzonderlijk [parametersbestand](../azure-resource-manager/templates/parameter-files.md). Op die manier u deze waarden wijzigen zonder dat u de sjabloon hoeft bij te werken en opnieuw te implementeren. Voor scenario's waarin u parameterwaarden hebt die gevoelig zijn of moeten worden beveiligd, zoals wachtwoorden en geheimen, u deze waarden opslaan in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) en uw parametersbestand deze waarden laten ophalen. In deze scenario's wordt u echter opnieuw geïmplementeerd om de huidige waarden op te halen.

In dit overzicht worden de kenmerken beschreven in een resourcemanagersjabloon die een definitie van een logische app-werkstroom bevat. Zowel de sjabloon als de werkstroomdefinitie gebruiken JSON-syntaxis, maar er bestaan enkele verschillen omdat de werkstroomdefinitie ook het [schema voor werkstroomdefinitiestaal volgt.](../logic-apps/logic-apps-workflow-definition-language.md) Sjabloonexpressies en werkstroomdefinitieexpressies verschillen bijvoorbeeld in de manier waarop ze [verwijzen naar parameters](#parameter-references) en de waarden die ze kunnen accepteren.

> [!TIP]
> Gebruik Visual Studio (gratis community-editie of meer) en de Azure Logic Apps Tools voor Visual Studio voor de eenvoudigste manier om een sjabloon met geldige logische apps te krijgen die grotendeels klaar is voor implementatie. U vervolgens [uw logische app maken in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) of een bestaande logische app van Azure zoeken en downloaden naar Visual [Studio.](../logic-apps/manage-logic-apps-with-visual-studio.md)
>
> U ook logische app-sjablonen maken met [Azure PowerShell met de LogicAppTemplate-module.](../logic-apps/logic-apps-create-azure-resource-manager-templates.md#azure-powershell)

De voorbeeldlogische app in dit onderwerp maakt gebruik van een [Office 365 Outlook-trigger](/connectors/office365/) die wordt geactiveerd wanneer een nieuwe e-mail binnenkomt en een [Azure Blob Storage-actie](/connectors/azureblob/) die een blob voor de e-mailhoofd maakt en die blob uploadt naar een Azure-opslagcontainer. De voorbeelden laten ook zien hoe waarden kunnen worden parameteriseren die variëren bij implementatie.

Zie de volgende onderwerpen voor meer informatie over resourcemanagersjablonen:

* [Sjabloonstructuur en syntaxis van Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Best practices voor Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-best-practices.md)
* [Azure Resource Manager-sjablonen voor consistentie van de cloud ontwikkelen](../azure-resource-manager/templates/templates-cloud-consistency.md)

Zie de volgende voorbeelden voor voorbeeldlogische app-sjablonen:

* [Volledige sjabloon](#full-example-template) die wordt gebruikt voor de voorbeelden van dit onderwerp
* [Voorbeeld van snelstartlogische app-sjabloon](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) in GitHub

Zie [Microsoft.Logic-brontypen](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)voor informatie over sjabloonbronnen die specifiek zijn voor logische apps, integratieaccounts en integratieaccountartefacten.

<a name="template-structure"></a>

## <a name="template-structure"></a>Sjabloonstructuur

Op het hoogste niveau volgt een resourcemanagersjabloon deze structuur, die volledig wordt beschreven in de [sjabloonstructuur en syntaxisvan Azure Resource Manager:](../azure-resource-manager/templates/template-syntax.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Voor een sjabloon voor logische apps werkt u voornamelijk met deze sjabloonobjecten:

| Kenmerk | Beschrijving |
|-----------|-------------|
| `parameters` | Hiermee declareert u de [sjabloonparameters](../azure-resource-manager/templates/template-syntax.md#parameters) voor het accepteren van de waarden die moeten worden gebruikt bij het maken en aanpassen van resources voor implementatie in Azure. Deze parameters accepteren bijvoorbeeld de waarden voor de naam en locatie van uw logische app, verbindingen en andere resources die nodig zijn voor implementatie. U deze parameterwaarden opslaan in een [parametersbestand,](#template-parameter-files)dat later in dit onderwerp wordt beschreven. Zie [Parameters - Sjabloonstructuur en syntaxis van Resourcebeheer](../azure-resource-manager/templates/template-syntax.md#parameters)voor algemene details. |
| `resources` | Hiermee definieert u de [resources](../azure-resource-manager/templates/template-syntax.md#resources) die moeten worden gemaakt of bijgewerkt en geïmplementeerd in een Azure-brongroep, zoals uw logische app, verbindingen, Azure-opslagaccounts, enzovoort. Zie [Resources - Sjabloonstructuur en syntaxis resourcemanager](../azure-resource-manager/templates/template-syntax.md#resources)voor algemene details. |
||||

Uw sjabloon voor logische apps gebruikt deze bestandsnaamindeling:

**<*logic-app-name*>.json**

> [!IMPORTANT]
> De syntaxis van de sjabloon is hoofdlettergevoelig, dus zorg ervoor dat u een consistente behuizing gebruikt. 

<a name="template-parameters"></a>

## <a name="template-parameters"></a>Sjabloonparameters

Een sjabloon voor `parameters` logische apps heeft meerdere objecten die op verschillende niveaus bestaan en verschillende functies uitvoeren. Op het hoogste niveau u bijvoorbeeld [sjabloonparameters](../azure-resource-manager/templates/template-syntax.md#parameters) declareren voor de waarden die u moet accepteren en gebruiken bij implementatie bij het maken en implementeren van resources in Azure, bijvoorbeeld:

* Uw logica-app
* Verbindingen die uw logica gebruikt om toegang te krijgen tot andere services en systemen via [beheerde connectors](../connectors/apis-list.md)
* Andere bronnen die uw logische app nodig heeft voor implementatie

  Als uw logica-app bijvoorbeeld een [integratieaccount](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) gebruikt voor B2B-scenario's (business-to-business), wordt in het object op het hoogste niveau `parameters` van de sjabloon de parameter opgegeven die de bron-id voor dat integratieaccount accepteert.

Hier is de algemene structuur en syntaxis voor een parameterdefinitie, die volledig wordt beschreven door [Parameters - Resource Manager-sjabloonstructuur en -syntaxis:](../azure-resource-manager/templates/template-syntax.md#parameters)

```json
"<parameter-name>": {
   "type": "<parameter-type>",
   "defaultValue": <default-parameter-value>,
   <other-parameter-attributes>,
   "metadata": {
      "description": "<parameter-description>"
   }
},
```

In dit voorbeeld worden alleen de sjabloonparameters weergegeven voor de waarden die worden gebruikt om deze resources in Azure te maken en te implementeren:

* Naam en locatie voor uw logische app
* ID te gebruiken voor een integratieaccount dat is gekoppeld aan de logische app

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location for the logic app"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Behalve voor parameters die waarden verwerken die gevoelig zijn of moeten worden beveiligd, zoals `defaultValue` gebruikersnamen, wachtwoorden en geheimen, bevatten al deze parameters kenmerken, hoewel in sommige gevallen de standaardwaarden lege waarden zijn. De implementatiewaarden die voor deze sjabloonparameters moeten worden gebruikt, worden geleverd door het [bestand met voorbeeldparameters](#template-parameter-files) dat later in dit onderwerp wordt beschreven.

Zie de volgende onderwerpen voor meer informatie over het beveiligen van sjabloonparameters:

* [Beveiligingsaanbevelingen voor sjabloonparameters](../azure-resource-manager/templates/template-best-practices.md#parameters)
* [Beveiliging voor sjabloonparameters verbeteren](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)
* [Beveiligde parameterwaarden doorgeven met Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Andere sjabloonobjecten verwijzen vaak naar sjabloonparameters, zodat ze de waarden kunnen gebruiken die door sjabloonparameters gaan, bijvoorbeeld:

* [Het resources-object van](#template-resources)uw sjabloon , dat later in dit onderwerp wordt beschreven, definieert elke bron in Azure die u wilt maken en implementeren, zoals de [resourcedefinitie van](#logic-app-resource-definition)uw logische app . Deze bronnen gebruiken vaak sjabloonparameterwaarden, zoals de naam en locatie- en verbindingsgegevens van uw logische app.

* Op een dieper niveau in de resourcedefinitie van uw logica-app declareert het parametersobject van uw [werkstroomdefinitie](#workflow-definition-parameters) parameters voor de waarden die moeten worden gebruikt tijdens de runtime van uw logische app. U bijvoorbeeld werkstroomdefinitieparameters declareren voor de gebruikersnaam en het wachtwoord die een HTTP-trigger gebruikt voor verificatie. Als u de waarden voor werkstroomdefinitieparameters wilt opgeven, gebruikt u het object dat `parameters` *buiten* de werkstroomdefinitie valt, maar nog steeds *binnen* de resourcedefinitie van uw logische app. In dit `parameters` buitenste object u verwijzen naar eerder gedeclareerde sjabloonparameters, die waarden kunnen accepteren bij implementatie vanuit een parametersbestand.

Bij het verwijzen naar parameters gebruiken sjabloonexpressies en -functies verschillende syntaxis en gedragen ze zich anders dan werkstroomdefinitieexpressies en -functies. Zie [Verwijzingen naar parameters](#parameter-references) later in dit onderwerp voor meer informatie over deze verschillen.

<a name="best-practices-template-parameters"></a>

## <a name="best-practices---template-parameters"></a>Aanbevolen procedures - sjabloonparameters

Hier volgen enkele aanbevolen procedures voor het definiëren van parameters:

* Declareer parameters alleen voor waarden die variëren op basis van uw implementatiebehoeften. Declareer geen parameters voor waarden die hetzelfde blijven voor verschillende implementatievereisten.

* Neem `defaultValue` het kenmerk op, dat lege waarden kan opgeven, voor alle parameters, behalve voor waarden die gevoelig zijn of moeten worden beveiligd. Gebruik altijd beveiligde parameters voor gebruikersnamen, wachtwoorden en geheimen. Als u gevoelige parameterwaarden wilt verbergen of beveiligen, volgt u de richtlijnen in deze onderwerpen:

  * [Beveiligingsaanbevelingen voor sjabloonparameters](../azure-resource-manager/templates/template-best-practices.md#parameters)

  * [Beveiliging voor sjabloonparameters verbeteren](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-deployment-template)

  * [Beveiligde parameterwaarden doorgeven met Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

* Als u sjabloonparameternamen wilt onderscheiden van parameternamen voor werkstroomdefinitie, u bijvoorbeeld beschrijvende sjabloonparameternamen gebruiken:`TemplateFabrikamPassword`

Zie Aanbevolen procedures voor [sjabloonparameters voor](../azure-resource-manager/templates/template-best-practices.md#parameters)meer aanbevolen procedures.

<a name="template-parameter-files"></a>

## <a name="template-parameters-file"></a>Bestand sjabloonparameters

Als u de waarden voor sjabloonparameters wilt opgeven, slaat u deze waarden op in een [parametersbestand](../azure-resource-manager/templates/parameter-files.md). Op die manier u verschillende parametersbestanden gebruiken op basis van uw implementatiebehoeften. Hier is de bestandsnaamindeling die u wilt gebruiken:

* Bestandsnaam logische ** < *app-sjabloon: logische-app-naam*>.json**
* Parameters bestandsnaam: ** < *logic-app-name*>.parameters.json**

Hier is de structuur in het parametersbestand, die een verwijzing naar de sleutelkluis bevat voor [het doorgeven van een beveiligde parameterwaarde met Azure Key Vault:](../azure-resource-manager/templates/key-vault-parameter.md)

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<secured-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

In dit voorbeeldparametersbestand worden de waarden opgegeven voor de eerder in dit onderwerp gedeclareerde sjabloonparameters:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      }
   }
}
```

<a name="template-resources"></a>

## <a name="template-resources"></a>Sjabloonresources

Uw sjabloon `resources` heeft een object, een array die definities bevat voor elke resource die moet worden gemaakt en geïmplementeerd in Azure, zoals de [resourcedefinitie van](#logic-app-resource-definition)uw logische app, eventuele [verbindingsbrondefinities](#connection-resource-definitions)en alle andere bronnen die uw logische app nodig heeft voor implementatie.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

> [!NOTE]
> Sjablonen kunnen brondefinities bevatten voor meerdere logische apps, dus zorg ervoor dat al uw logische app-bronnen dezelfde Azure-brongroep opgeven. Wanneer u de sjabloon implementeert in een Azure-brongroep met Behulp van Visual Studio, wordt u gevraagd voor welke logische app u wilt openen. Uw Azure-brongroepproject kan ook meer dan één sjabloon bevatten, dus zorg ervoor dat u het juiste parametersbestand selecteert wanneer daarom wordt gevraagd.

Zie de volgende onderwerpen voor algemene informatie over sjabloonbronnen en hun kenmerken:

* [Resources - Sjabloonstructuur en syntaxis van ResourceManager](../azure-resource-manager/templates/template-syntax.md#resources)
* [Aanbevolen procedures voor sjabloonbronnen](../azure-resource-manager/templates/template-best-practices.md#resources)

<a name="logic-app-resource-definition"></a>

### <a name="logic-app-resource-definition"></a>Definitie van logische app-bron

De resourcedefinitie van uw logische `properties` app begint bij het object, dat deze informatie bevat:

* De status van uw logische app bij implementatie
* De ID voor elk integratieaccount dat door uw logische app wordt gebruikt
* De werkstroomdefinitie van uw logische app
* Een `parameters` object dat de waarden instelt die moeten worden gebruikt tijdens runtime
* Andere broninformatie over uw logische app, zoals naam, type, locatie, enzovoort

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            "state": "<Enabled or Disabled>",
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]" // Template parameter reference
            },
            "definition": {<workflow-definition>},
            "parameters": {<workflow-definition-parameter-values>},
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]", // Template parameter reference
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]", // Template parameter reference
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
         ]
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

Dit zijn de kenmerken die specifiek zijn voor de definitie van uw logische app-bron:

| Kenmerk | Vereist | Type | Beschrijving |
|-----------|----------|------|-------------|
| `state` | Ja | Tekenreeks | De status van uw logische `Enabled` app bij implementatie, `Disabled` waar betekent dat uw logische app live is en betekent dat uw logische app inactief is. Als u bijvoorbeeld niet klaar bent om uw logische app live te laten gaan, `Disabled` maar een conceptversie wilt implementeren, u de optie gebruiken. |
| `integrationAccount` | Nee | Object | Als uw logische app een integratieaccount gebruikt, dat artefacten opslaat voor B2B-scenario's (business-to-business), bevat dit object het `id` kenmerk, dat de id voor het integratieaccount opgeeft. |
| `definition` | Ja | Object | De onderliggende werkstroomdefinitie van uw logische app, hetzelfde object dat wordt weergegeven in de codeweergave en volledig wordt beschreven in de schemaverwijzing voor het onderwerp [Taalvan de werkstroomdefinitie.](../logic-apps/logic-apps-workflow-definition-language.md) In deze werkstroomdefinitie declareert het `parameters` object parameters voor de waarden die moeten worden gebruikt bij de runtime van de logische app. Zie [Werkstroomdefinitie en parameters](#workflow-definition-parameters)voor meer informatie. <p><p>Als u de kenmerken in de werkstroomdefinitie van uw logische app wilt weergeven, schakelt u over van 'ontwerpweergave' naar 'codeweergave' in de Azure-portal of Visual Studio of gebruikt u een hulpprogramma zoals [Azure Resource Explorer.](https://resources.azure.com) |
| `parameters` | Nee | Object | De [parameterwaarden voor werkstroomdefinitie](#workflow-definition-parameters) die moeten worden gebruikt bij de runtime van de logische app. De parameterdefinities voor deze waarden worden weergegeven in het parametersobject van de [werkstroomdefinitie.](#workflow-definition-parameters) Als uw logische app [beheerde connectors](../connectors/apis-list.md) gebruikt voor toegang tot `$connections` andere services en systemen, bevat dit object ook een object dat de verbindingswaarden instelt die tijdens runtime moeten worden gebruikt. |
| `accessControl` | Nee | Object | Voor het opgeven van beveiligingskenmerken voor uw logische app, zoals het beperken van IP-toegang tot het aanvragen van triggers of het uitvoeren van geschiedenisingangen en -uitvoer. Zie [Beveiligde toegang tot logische apps voor](../logic-apps/logic-apps-securing-a-logic-app.md)meer informatie. |
||||

Zie [Microsoft.Logic-brontypen](https://docs.microsoft.com/azure/templates/microsoft.logic/allversions)voor informatie over sjabloonbronnen die specifiek zijn voor logische apps, integratieaccounts en integratieaccountartefacten.

<a name="workflow-definition-parameters"></a>

## <a name="workflow-definition-and-parameters"></a>Definitie en parameters van werkstroom

De werkstroomdefinitie van uw `definition` logische app wordt `properties` weergegeven in het object, dat wordt weergegeven in het object in de resourcedefinitie van uw logische app. Dit `definition` object is hetzelfde object dat wordt weergegeven in de codeweergave en volledig wordt beschreven in de schemaverwijzing voor het onderwerp [Taalvan de werkstroomdefinitie.](../logic-apps/logic-apps-workflow-definition-language.md) Uw werkstroomdefinitie `parameters` bevat een innerlijk declaratieobject waarin u nieuwe parameters definiëren of bestaande parameters bewerken voor de waarden die worden gebruikt door uw werkstroomdefinitie tijdens uitvoering. U vervolgens verwijzen naar deze parameters in de trigger of acties in uw werkstroom. Standaard is `parameters` dit object leeg, tenzij uw logische app verbindingen maakt met andere services en systemen via [beheerde connectors.](../connectors/apis-list.md)

Als u de waarden voor werkstroomdefinitieparameters wilt instellen, gebruikt u het `parameters` object dat *buiten* de werkstroomdefinitie valt, maar nog steeds *binnen* de resourcedefinitie van uw logische app. In dit `parameters` buitenste object u vervolgens verwijzen naar de eerder gedeclareerde sjabloonparameters, die waarden kunnen accepteren bij implementatie vanuit een parametersbestand.

> [!TIP]
>
> Als aanbevolen praktijk u niet rechtstreeks verwijzen naar sjabloonparameters, die bij implementatie worden geëvalueerd, vanuit de werkstroomdefinitie. Declareer in plaats daarvan een parameter voor `parameters` werkstroomdefinitie, die u vervolgens instellen in het object dat *buiten* de werkstroomdefinitie valt, maar nog steeds *binnen* de resourcedefinitie van uw logische app. Zie [Verwijzingen naar parameters voor](#parameter-references)meer informatie .

Deze syntaxis geeft aan waar u parameters declareren op zowel de sjabloon- als de werkstroomdefinitieniveaus, samen met waar u deze parameterwaarden instellen door te verwijzen naar de parameters voor sjabloon- en werkstroomdefinitie:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "<template-parameter-name>": {
         "type": "<parameter-type>",
         "defaultValue": "<parameter-default-value>",
         "metadata": {
            "description": "<parameter-description>"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "<workflow-definition-parameter-name>": {
                     "type": "<parameter-type>",
                     "defaultValue": "<parameter-default-value>",
                     "metadata": {
                        "description": "<parameter-description>"
                     }
                  }
               },
               "triggers": {
                  "<trigger-name>": {
                     "type": "<trigger-type>",
                     "inputs": {
                         // Workflow definition parameter reference
                         "<attribute-name>": "@parameters('<workflow-definition-parameter-name')"
                     }
                  }
               },
               <...>
            },
            // Workflow definition parameter value
            "parameters": {
               "<workflow-definition-parameter-name>": { 
                  "value": "[parameters('<template-parameter-name>')]"
               }
            },
            "accessControl": {}
         },
         <other-logic-app-resource-definition-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

<a name="secure-workflow-definition-parmameters"></a>

### <a name="secure-workflow-definition-parameters"></a>Parameters voor veilige werkstroomdefinitie

Voor een parameter voor werkstroomdefinitie waarmee gevoelige informatie, wachtwoorden, toegangssleutels of geheimen tijdens `securestring` `secureobject` uitvoering worden verwerkt, moet u de parameter declareren of bewerken om het type of parameter te gebruiken. U deze parameter in en binnen uw werkstroomdefinitie bekijken. Declareer op het hoogste niveau van de sjabloon een parameter met hetzelfde type om deze informatie bij implementatie te verwerken.

Als u de waarde wilt instellen `parameters` voor de parameter werkstroomdefinitie, gebruikt u het object dat *buiten* de werkstroomdefinitie valt, maar nog steeds *binnen* de definitie van uw logische app-bron om naar de sjabloonparameter te verwijzen. Als u ten slotte de waarde wilt doorgeven aan uw sjabloonparameter bij implementatie, slaat u die waarde op in [Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md) en verwijst u naar de sleutelkluis in het [parametersbestand](#template-parameter-files) dat door uw sjabloon wordt gebruikt bij implementatie.

In deze voorbeeldsjabloon ziet u hoe u deze taken voltooien door beveiligde parameters te definiëren wanneer dat nodig is, zodat u hun waarden opslaan in Azure Key Vault:

* Gewaarmerkte parameters declareren voor de waarden die worden gebruikt om toegang te verifiëren.
* Gebruik deze waarden op zowel de sjabloon- als de werkstroomdefinitieniveaus.
* Geef deze waarden op met behulp van een parametersbestand.

**Sjabloon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      <previously-defined-template-parameters>,
      // Additional template parameters for passing values to use in workflow definition
      "TemplateAuthenticationType": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The type of authentication used for the Fabrikam portal"
         }
      },
      "TemplateFabrikamPassword": {
         "type": "securestring",
         "metadata": {
            "description": "The password for the Fabrikam portal"
         }
      },
      "TemplateFabrikamUserName": {
         "type": "securestring",
         "metadata": {
            "description": "The username for the Fabrikam portal"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <other-logic-app-resource-properties>,
            // Start workflow definition
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               // Workflow definition parameters
               "parameters": {
                  "authenticationType": {
                     "type": "string",
                     "defaultValue": "",
                     "metadata": {
                        "description": "The type of authentication used for the Fabrikam portal"
                     }
                  },
                  "fabrikamPassword": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The password for the Fabrikam portal"
                     }
                  },
                  "fabrikamUserName": {
                     "type": "securestring",
                     "metadata": {
                        "description": "The username for the Fabrikam portal"
                     }
                  }
               },
               "triggers": {
                  "HTTP": {
                     "inputs": {
                        "authentication": {
                           // Reference workflow definition parameters
                           "password": "@parameters('fabrikamPassword')",
                           "type": "@parameters('authenticationType')",
                           "username": "@parameters('fabrikamUserName')"
                        }
                     },
                     "recurrence": {<...>},
                     "type": "Http"
                  }
               },
               <...>
            },
            // End workflow definition
            // Start workflow definition parameter values
            "parameters": {
               "authenticationType": "[parameters('TemplateAuthenticationType')]", // Template parameter reference
               "fabrikamPassword": "[parameters('TemplateFabrikamPassword')]", // Template parameter reference
               "fabrikamUserName": "[parameters('TemplateFabrikamUserName')]" // Template parameter reference
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      }
      // End logic app resource definition
   ],
   "outputs": {}
}
```

**Bestand Parameters**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      <previously-defined-template-parameter-values>,
     "TemplateAuthenticationType": {
        "value": "Basic"
     },
     "TemplateFabrikamPassword": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamPassword"
        }
     },
     "TemplateFabrikamUserName": {
        "reference": {
           "keyVault": {
              "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
           },
           "secretName": "FabrikamUserName"
        }
     }
   }
}
```

<a name="best-practices-workflow-definition-parameters"></a>

## <a name="best-practices---workflow-definition-parameters"></a>Aanbevolen procedures - parameters voor workflowdefinitie

Volg de volgende aanbevolen procedures om ervoor te zorgen dat de Logic App Designer de parameters voor werkstroomdefinitie correct kan weergeven:

* Neem `defaultValue` het kenmerk op, dat lege waarden kan opgeven, voor alle parameters, behalve voor waarden die gevoelig zijn of moeten worden beveiligd.

* Gebruik altijd beveiligde parameters voor gebruikersnamen, wachtwoorden en geheimen. Als u gevoelige parameterwaarden wilt verbergen of beveiligen, volgt u de richtlijnen in deze onderwerpen:

  * [Beveiligingsaanbevelingen voor actieparameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters)

  * [Beveiligingsaanbevelingen voor parameters in werkstroomdefinities](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)

  * [Veilige parameterwaarden doorgeven met Azure Key Vault](../azure-resource-manager/templates/key-vault-parameter.md)

Zie [Parameters - Werkstroomdefinitiestaal](../logic-apps/logic-apps-workflow-definition-language.md#parameters)voor meer informatie over parameters voor workflowdefinitie.

<a name="connection-resource-definitions"></a>

## <a name="connection-resource-definitions"></a>Definities van verbindingsbronnen

Wanneer uw logische app verbindingen met andere services en systemen maakt `resources` en gebruikt met [beheerde connectors,](../connectors/apis-list.md)bevat het object van uw sjabloon de brondefinities voor die verbindingen.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Start connection resource definitions
      {
         <connection-resource-definition-1>
      },
      {
         <connection-resource-definition-2>
      }
   ],
   "outputs": {}
}
```

Verbindingsbrondefinities verwijzen naar de parameters op het hoogste niveau van de sjabloon voor hun waarden, wat betekent dat u deze waarden opgeven bij implementatie met behulp van een parametersbestand. Zorg ervoor dat verbindingen dezelfde Azure-brongroep en -locatie gebruiken als uw logische app.

Hier vindt u een voorbeeldbrondefinitie voor een Office 365 Outlook-verbinding en de bijbehorende sjabloonparameters:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name for the Office 365 Outlook connection"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         <logic-app-resource-definition>
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

De resourcedefinitie van uw logische app werkt op de volgende manieren ook met definities van verbindingsbronnen:

* Binnen uw werkstroomdefinitie geeft het `parameters` object een `$connections` parameter aan voor de verbindingswaarden die moeten worden gebruikt bij de runtime van de logische app. De trigger of actie die een verbinding maakt, gebruikt `$connections` ook de bijbehorende waarden die door deze parameter gaan.

* *Buiten* uw werkstroomdefinitie, maar nog steeds *binnen de* resourcedefinitie van uw logische app, stelt een ander `parameters` object de waarden in die tijdens runtime voor de `$connections` parameter moeten worden gebruikt door te verwijzen naar de bijbehorende sjabloonparameters. Deze waarden gebruiken sjabloonexpressies om naar bronnen te verwijzen die de metagegevens voor de verbindingen in uw logische app veilig opslaan.

  Metagegevens kunnen bijvoorbeeld verbindingstekenreeksen en toegangstokens bevatten, die u opslaan in [Azure Key Vault.](../azure-resource-manager/templates/key-vault-parameter.md) Als u deze waarden wilt doorgeven aan uw sjabloonparameters, verwijst u naar de sleutelkluis in het [parametersbestand](#template-parameter-files) dat door uw sjabloon wordt gebruikt bij de implementatie. Zie Verwijzingen naar parameters later in dit onderwerp voor meer informatie over verschillen in [verwijzingsparameters.](#parameter-references)

  Wanneer u de werkstroomdefinitie van uw logische app opent in `$connections` de codeweergave via de Azure-portal of Visual Studio, wordt het object buiten uw werkstroomdefinitie weergegeven, maar op hetzelfde niveau. Deze volgorde in de codeweergave maakt deze parameters gemakkelijker te verwijzen wanneer u de werkstroomdefinitie handmatig bijwerkt:

  ```json
  {
     "$connections": {<workflow-definition-parameter-connection-values-runtime},
     "definition": {<workflow-definition>}
  }
  ```

* De resourcedefinitie van uw `dependsOn` logische app heeft een object dat de afhankelijkheden opgeeft van de verbindingen die door uw logische app worden gebruikt.

Elke verbinding die u maakt, heeft een unieke naam in Azure. Wanneer u meerdere verbindingen maakt met dezelfde service of hetzelfde systeem, wordt elke verbindingsnaam toegevoegd aan `office365` `office365-1`een getal, dat wordt verhoogd met elke nieuwe verbinding die bijvoorbeeld wordt gemaakt, enzovoort.

In dit voorbeeld worden de interacties weergegeven tussen de resourcedefinitie van uw logische app en een definitie van verbindingsbronnen voor Office 365 Outlook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "office365_1_Connection_Name": {<parameter-definition>},
      "office365_1_Connection_DisplayName": {<parameter-definition>}
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Start logic app resource definition
         "properties": {
            <...>,
            "definition": {
               <...>,
               "parameters": {
                  // Workflow definition "$connections" parameter
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               <...>
            },
            "parameters": {
               // Workflow definition "$connections" parameter values to use at runtime
               "$connections": {
                  "value": {
                     "office365": {
                        // Template parameter references
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            <other-logic-app-resource-information>,
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
            ]
         }
         // End logic app resource definition
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Template parameter reference for connection name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Template parameter reference for connection resource location. Must match logic app location.
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Template parameter reference for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-connection-parameters"></a>

### <a name="secure-connection-parameters"></a>Parameters voor beveiligde verbindingen

Voor een verbindingsparameter die gevoelige informatie, wachtwoorden, toegangssleutels of geheimen verwerkt, bevat de resourcedefinitie van de verbinding een `parameterValues` object dat deze waarden opgeeft in de vorm van een naam-waardepaar. Als u deze informatie wilt verbergen, u de sjabloonparameters voor deze waarden declareren of bewerken met behulp van de `securestring` of `secureobject` parametertypen. U die informatie vervolgens opslaan in [Azure Key Vault.](../azure-resource-manager/templates/key-vault-parameter.md) Als u deze waarden wilt doorgeven aan uw sjabloonparameters, verwijst u naar de sleutelkluis in het [parametersbestand](#template-parameter-files) dat door uw sjabloon wordt gebruikt bij de implementatie.

Hier is een voorbeeld dat de accountnaam en toegangssleutel voor een Azure Blob Storage-verbinding biedt:

**Bestand Parameters**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "LogicAppName": {
         "value": "Email-Processor-Logic-App"
      },
      "LogicAppLocation": {
         "value": "westeurope"
      },
      "azureblob_1_Connection_Name": {
         "value": "Fabrikam-Azure-Blob-Storage-Connection"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "Fabrikam-Storage"
      },
      "azureblob_1_accountName": {
         "value": "Fabrikam-Storage-Account"
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "FabrikamStorageKey"
         }
      }
   }
}
```

**Sjabloon**

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {<parameter-definition>},
      "LogicAppLocation": {<parameter-definition>},
      "azureblob_1_Connection_Name": {<parameter-definition>},
      "azureblob_1_Connection_DisplayName": {<parameter-definition>},
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "secureobject",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  // Azure Blob Storage action
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              // Workflow definition parameter reference for values to use at runtime
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  // Workflow definition parameter for values to use at runtime
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     // Template parameter references for values to use at runtime
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            // Template parameter reference for value to use at deployment
            "dependsOn": [
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Template parameter reference for values to use at deployment
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="authenticate-connections"></a>

### <a name="authenticate-connections"></a>Verbindingen verifiëren

Na de implementatie werkt uw logische app end-to-end met geldige parameters. U moet echter nog steeds oauth-verbindingen autoriseren om geldige toegangstokens te genereren voor [het verifiëren van uw referenties.](../active-directory/develop/authentication-scenarios.md) Zie [OAuth-verbindingen autoriseren](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)voor meer informatie .

Sommige verbindingen ondersteunen het gebruik van een Azure Active Directory [-serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md) (Azure AD) om verbindingen te autoriseren voor een logische app die is [geregistreerd in Azure AD.](../active-directory/develop/quickstart-register-app.md) Deze azure Data Lake-verbindingsbrondefinitie geeft bijvoorbeeld aan hoe u moet verwijzen naar de sjabloonparameters die de gegevens van de serviceprincipal verwerken en hoe de sjabloon de volgende parameters declareert:

**Definitie van verbindingsbron**

```json
{
   <other-template-objects>
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azuredatalake_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'resourceGroup().location', '/managedApis/', 'azuredatalake')]"
      },
      "displayName": "[parameters('azuredatalake_1_Connection_DisplayName')]",
      "parameterValues": {
         "token:clientId": "[parameters('azuredatalake_1_token:clientId')]",
         "token:clientSecret": "[parameters('azuredatalake_1_token:clientSecret')]",
         "token:TenantId": "[parameters('azuredatalake_1_token:TenantId')]",
         "token:grantType": "[parameters('azuredatalake_1_token:grantType')]"
      }
   }
}
```

| Kenmerk | Beschrijving |
|-----------|-------------|
| `token:clientId` | De toepassing of client-id die is gekoppeld aan uw serviceprincipal |
| `token:clientSecret` | De sleutelwaarde in verband met uw serviceprincipal |
| `token:TenantId` | De adresmap-id voor uw Azure AD-tenant |
| `token:grantType` | Het gevraagde subsidietype, dat `client_credentials`moet zijn . Zie [Microsoft-identiteitsplatform en de oauth 2.0-clientreferentiesstroom](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)voor meer informatie. |
|||

**Sjabloonparameterdefinities**

Het object op het `parameters` hoogste niveau van de sjabloon verklaart de volgende parameters voor de voorbeeldverbinding:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "azuredatalake_1_Connection_Name": {
        "type": "string",
        "defaultValue": "azuredatalake"
      },
      "azuredatalake_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<connection-name>"
      },
      "azuredatalake_1_token:clientId": {
        "type": "securestring",
        "metadata": {
          "description": "Client (or Application) ID of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:clientSecret": {
        "type": "securestring",
        "metadata": {
          "description": "Client secret of the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:TenantId": {
        "type": "securestring",
        "metadata": {
          "description": "The tenant ID of for the Azure Active Directory application."
        }
      },
      "azuredatalake_1_token:resourceUri": {
        "type": "string",
        "metadata": {
          "description": "The resource you are requesting authorization to use."
        }
      },
      "azuredatalake_1_token:grantType": {
        "type": "string",
        "metadata": {
          "description": "Grant type"
        },
        "defaultValue": "client_credentials",
        "allowedValues": [
          "client_credentials"
        ]
      },
      // Other template parameters
   }
   // Other template objects
}
```

Zie de volgende onderwerpen voor meer informatie over het werken met serviceprincipals:

* [Een serviceprincipal maken met behulp van de Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)
* [Een Azure-serviceprincipal maken met Azure PowerShell](https://docs.microsoft.com/powershell/azure/create-azure-service-principal-azureps)
* [Een serviceprincipal maken met een certificaat met Azure PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

<a name="parameter-references"></a>

## <a name="references-to-parameters"></a>Verwijzingen naar parameters

Als u sjabloonparameters wilt verwijzen, u sjabloonexpressies gebruiken met [sjabloonfuncties,](../azure-resource-manager/templates/template-functions.md)die worden geëvalueerd bij implementatie. Sjabloonexpressies gebruiken vierkante haakjes (**[]**:

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

Als u wilt verwijzen naar de definitieparameters van werkstroom, gebruikt u expressies en functies van [werkstroomdefinitietaal](../logic-apps/workflow-definition-language-functions-reference.md)die tijdens runtime worden geëvalueerd. U zult merken dat sommige sjabloonfuncties en werkstroomdefinitiefuncties dezelfde naam hebben. Werkstroomdefinitieexpressies beginnen met het**@**'at'-symbool :

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

U sjabloonparameterwaarden doorgeven aan uw werkstroomdefinitie die uw logische app tijdens runtime gebruiken. Vermijd echter het gebruik van sjabloonparameters, expressies en syntaxis in de werkstroomdefinitie, omdat de Logic App Designer geen sjabloonelementen ondersteunt. Ook kunnen syntaxis en expressies van sjablonen uw code bemoeilijken vanwege verschillen in wanneer expressies worden geëvalueerd.

Volg in plaats daarvan deze algemene stappen om de parameters voor werkstroomdefinitie te declareren en te verwijzen die tijdens de uitvoering moeten worden gebruikt, de sjabloonparameters te declareren en te verwijzen die bij de implementatie moeten worden gebruikt en de waarden op te geven die bij implementatie moeten worden doorgegeven met behulp van een parametersbestand. Zie de sectie [Werkdewerkdefinitie en parameters](#workflow-definition-parameters) eerder in dit onderwerp voor meer informatie.

1. Maak uw sjabloon en declareer de sjabloonparameters voor de waarden die moeten worden geaccepteerd en gebruikt bij implementatie.

1. Declareer in uw werkstroomdefinitie de parameters voor de waarden die moeten worden geaccepteerd en gebruikt tijdens uitvoering. U deze waarden vervolgens in en binnen uw werkstroomdefinitie doorverwijzen.

1. Stel `parameters` in het object dat *buiten* de werkstroomdefinitie valt, maar nog steeds *binnen* de resourcedefinitie van uw logische app, de waarden voor uw werkstroomdefinitieparameters in door te verwijzen naar de bijbehorende sjabloonparameters. Op die manier u sjabloonparameterwaarden doorgeven aan de parameters voor werkstroomdefinitie.

1. Geef in het parametersbestand de waarden op die uw sjabloon bij implementatie moet gebruiken.

<a name="full-example-template"></a>

## <a name="full-example-template"></a>Sjabloon voor volledig voorbeeld

Hier vindt u de geparameteriseerde voorbeeldsjabloon die wordt gebruikt door de voorbeelden van dit onderwerp:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "minLength": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Disabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Sjablonen voor logische app maken](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)
