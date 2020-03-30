---
title: Bestand CreateUiDefinition.json voor portalvenster
description: Beschrijft hoe u gebruikersinterfacedefinities maakt voor de Azure-portal. Wordt gebruikt bij het definiëren van Azure Managed Applications.
author: tfitzmac
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: tomfitz
ms.openlocfilehash: 2956c76f5bec353639b39228b982db21b6932deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294896"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>CreateUiDefinition. json voor het maken van beheerde Azure-toepassingen

Dit document introduceert de kernconcepten van het **createUiDefinition.json-bestand dat Azure-portal** gebruikt om de gebruikersinterface te definiëren bij het maken van een beheerde toepassing.

De sjabloon is als volgt

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { },
      "resourceTypes": [ ]
   }
}
```

Een CreateUiDefinition bevat altijd drie eigenschappen: 

* Handler
* versie
* parameters

De handler moet `Microsoft.Azure.CreateUIDef`altijd zijn, en `0.1.2-preview`de nieuwste ondersteunde versie is .

Het schema van de eigenschap parameters is afhankelijk van de combinatie van de opgegeven handler en versie. Voor beheerde toepassingen worden `basics`de `steps`ondersteunde `outputs`eigenschappen , en . De eigenschappen basis- en stappen bevatten de [elementen](create-uidefinition-elements.md) - zoals tekstvakken en vervolgkeuzelijsten - die in de Azure-portal worden weergegeven. De eigenschap uitvoer wordt gebruikt om de uitvoerwaarden van de opgegeven elementen toe te passen aan de parameters van de azure resource manager-implementatiesjabloon.

Inclusief `$schema` is aanbevolen, maar optioneel. Indien opgegeven, moet `version` de waarde voor `$schema` de versie binnen de URI overeenkomen.

U een JSON-editor gebruiken om uw createUiDefinition te maken en deze vervolgens te testen in de [createUiDefinition Sandbox](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) om deze te bekijken. Zie [Uw portalinterface voor Azure Managed Applications testen voor](test-createuidefinition.md)meer informatie over de sandbox.

## <a name="basics"></a>Basisbeginselen

Basisbeginselen is de eerste stap die wordt gegenereerd wanneer de Azure-portal het bestand ontleden. Naast het weergeven van `basics`de elementen die zijn opgegeven in , injecteert de portal elementen voor gebruikers om het abonnement, de brongroep en de locatie voor de implementatie te kiezen. Indien mogelijk moeten elementen die parameters voor de implementatie van de implementatie opvragen, zoals de naam van een cluster of beheerdersreferenties, in deze stap worden gebruikt.

## <a name="steps"></a>Stappen

De eigenschap stappen kan nul of meer extra stappen bevatten om na basisbeginselen weer te geven, die elk een of meer elementen bevatten. Overweeg stappen toe te voegen per rol of laag van de toepassing die wordt geïmplementeerd. Voeg bijvoorbeeld een stap toe voor hoofdknooppuntingangen en een stap voor de werknemersknooppunten in een cluster.

## <a name="outputs"></a>Uitvoer

De Azure-portal `outputs` gebruikt de `basics` eigenschap `steps` om elementen van en naar de parameters van de azure resource manager-implementatiesjabloon in kaart te brengen. De sleutels van dit woordenboek zijn de namen van de sjabloonparameters en de waarden zijn eigenschappen van de uitvoerobjecten van de naar de verwijzing genoemde elementen verwezen.

Als u de naam van de beheerde `applicationResourceName` toepassingsbron wilt instellen, moet u een waarde opnemen die is vernoemd in de eigenschap uitvoer. Als u deze waarde niet instelt, wijst de toepassing een GUID toe voor de naam. U een tekstvak opnemen in de gebruikersinterface waarin een naam van de gebruiker wordt opgevraagd.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>Resourcetypen

Als u de beschikbare locaties wilt filteren op alleen de locaties die de te implementeren resourcetypen ondersteunen, geeft u een array van de resourcetypen. Als u meer dan één resourcetype opgeeft, worden alleen de locaties die alle resourcetypen ondersteunen, geretourneerd. Deze eigenschap is optioneel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
      "resourceTypes": ["Microsoft.Compute/disks"],
      "basics": [
        ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition biedt [functies](create-uidefinition-functions.md) voor het werken met de ingangen en uitgangen van elementen en functies zoals conditionalen. Deze functies zijn qua syntaxis en functionaliteit vergelijkbaar met sjabloonfuncties van Azure Resource Manager.

## <a name="next-steps"></a>Volgende stappen

Het bestand createUiDefinition.json zelf heeft een eenvoudig schema. De echte diepte van het komt van alle ondersteunde elementen en functies. Deze items worden nader beschreven op:

- [Elementen](create-uidefinition-elements.md)
- [Functies](create-uidefinition-functions.md)

Een actueel JSON-schema voor createUiDefinition is hier beschikbaar: `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`.

Zie [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)voor een voorbeeldvan user interface bestand .
