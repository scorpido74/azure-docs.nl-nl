---
title: Fouten die niet zijn gevonden in resource
description: Beschrijft hoe u fouten oplossen wanneer een resource niet kan worden gevonden bij het implementeren met een Azure Resource Manager-sjabloon.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773247"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Niet gevonden fouten voor Azure-resources oplossen

In dit artikel worden de fouten beschreven die u zien wanneer een resource niet kan worden gevonden tijdens de implementatie.

## <a name="symptom"></a>Symptoom

Wanneer uw sjabloon de naam bevat van een resource die niet kan worden opgelost, ontvangt u een fout die vergelijkbaar is met:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Als u de [functies referentie-](template-functions-resource.md#reference) of [listKeys](template-functions-resource.md#listkeys) gebruikt met een resource die niet kan worden opgelost, ontvangt u de volgende fout:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Oorzaak

Resourcemanager moet de eigenschappen voor een resource ophalen, maar kan de bron in uw abonnement niet identificeren.

## <a name="solution-1---set-dependencies"></a>Oplossing 1 - afhankelijkheden instellen

Als u de ontbrekende bron in de sjabloon probeert te implementeren, controleert u of u een afhankelijkheid moet toevoegen. Resource Manager optimaliseert de implementatie door resources parallel te maken, indien mogelijk. Als de ene resource na een andere resource moet worden geïmplementeerd, moet u het element **dependsOn** in uw sjabloon gebruiken. Bij het implementeren van een web-app moet bijvoorbeeld het App Service-abonnement bestaan. Als u niet hebt opgegeven dat de web-app afhankelijk is van het App Service-abonnement, maakt Resource Manager beide bronnen tegelijkertijd. Er wordt een foutmelding weergegeven dat de bron van het App Service-abonnement niet kan worden gevonden, omdat deze nog niet bestaat wanneer u een eigenschap in de web-app probeert in te stellen. U voorkomt deze fout door de afhankelijkheid in de web-app in te stellen.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Maar u wilt voorkomen dat afhankelijkheden worden ingesteld die niet nodig zijn. Wanneer u onnodige afhankelijkheden hebt, verlengt u de duur van de implementatie door te voorkomen dat resources die niet van elkaar afhankelijk zijn, parallel worden geïmplementeerd. Daarnaast u kringafhankelijkheden maken die de implementatie blokkeren. De [functie referentiefunctie](template-functions-resource.md#reference) en [lijst*maakt](template-functions-resource.md#list) een impliciete afhankelijkheid van de bron waarnaar wordt verwezen, wanneer die resource in dezelfde sjabloon wordt geïmplementeerd en wordt verwezen naar de naam (niet resource-id). Daarom u meer afhankelijkheden hebben dan de afhankelijkheden die zijn opgegeven in de eigenschap **dependsOn.** De functie [resourceId](template-functions-resource.md#resourceid) maakt geen impliciete afhankelijkheid of valideert niet dat de resource bestaat. De [functie referentiefunctie](template-functions-resource.md#reference) en [lijst*maken](template-functions-resource.md#list) geen impliciete afhankelijkheid wanneer de resource wordt aangeduid met de resource-id. Als u een impliciete afhankelijkheid wilt maken, geeft u de naam door van de resource die in dezelfde sjabloon is geïmplementeerd.

Wanneer u afhankelijkheidsproblemen ziet, moet u inzicht krijgen in de volgorde van de implementatie van resources. Ga als u de volgorde van implementatiebewerkingen weergeven:

1. Selecteer de implementatiegeschiedenis voor uw resourcegroep.

   ![implementatiegeschiedenis selecteren](./media/error-not-found/select-deployment.png)

2. Selecteer een implementatie in de geschiedenis en selecteer **Gebeurtenissen**.

   ![implementatiegebeurtenissen selecteren](./media/error-not-found/select-deployment-events.png)

3. Bestudeer de volgorde van gebeurtenissen voor elke resource. Besteed aandacht aan de status van elke bewerking. In de volgende afbeelding worden bijvoorbeeld drie opslagaccounts weergegeven die parallel zijn geïmplementeerd. Merk op dat de drie opslagaccounts tegelijkertijd worden gestart.

   ![parallelle implementatie](./media/error-not-found/deployment-events-parallel.png)

   De volgende afbeelding toont drie opslagaccounts die niet parallel worden geïmplementeerd. Het tweede opslagaccount is afhankelijk van het eerste opslagaccount en het derde opslagaccount is afhankelijk van het tweede opslagaccount. Het eerste opslagaccount wordt gestart, geaccepteerd en voltooid voordat het volgende wordt gestart.

   ![sequentiële implementatie](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Oplossing 2 - resource ophalen uit verschillende resourcegroep

Wanneer de resource in een andere resourcegroep bestaat dan de resource die wordt geïmplementeerd, gebruikt u de [resourceId-functie](template-functions-resource.md#resourceid) om de volledig gekwalificeerde naam van de resource te krijgen.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Oplossing 3 - controlereferentiefunctie

Zoek naar een expressie die de [referentiefunctie](template-functions-resource.md#reference) bevat. De waarden die u opgeeft, variëren afhankelijk van of de resource zich in dezelfde sjabloon, resourcegroep en abonnement bevindt. Controleer of u de vereiste parameterwaarden voor uw scenario verstrekt. Als de resource zich in een andere resourcegroep bevindt, geeft u de volledige resource-id op. Als u bijvoorbeeld wilt verwijzen naar een opslagaccount in een andere resourcegroep, gebruikt u het volgende:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Oplossing 4 - krijg beheerde identiteit uit resource

Als u een resource implementeert die impliciet een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)maakt, moet u wachten tot die bron is geïmplementeerd voordat waarden op de beheerde identiteit worden opgehaald. Als u de beheerde identiteitsnaam doorgeeft aan de [referentiefunctie,](template-functions-resource.md#reference) probeert Resource Manager de verwijzing op te lossen voordat de bron en identiteit worden geïmplementeerd. Geef in plaats daarvan de naam door van de resource waarop de identiteit wordt toegepast. Deze aanpak zorgt ervoor dat de resource en de beheerde identiteit worden geïmplementeerd voordat Resource Manager de referentiefunctie oplost.

Gebruik in de `Full` referentiefunctie om alle eigenschappen te krijgen, inclusief de beheerde identiteit.

Als u bijvoorbeeld de tenant-id wilt krijgen voor een beheerde identiteit die wordt toegepast op een virtuele machineschaalset, gebruikt u het als:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```