---
title: Fouten in de Azure-resource niet gevonden | Microsoft Docs
description: Hierin wordt beschreven hoe u fouten oplost wanneer een resource niet kan worden gevonden bij het implementeren met een Azure Resource Manager sjabloon.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 4db50bbb3073fe98599923843e6afdded3a8ca62
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390281"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Niet-gevonden fouten voor Azure-resources oplossen

In dit artikel worden de fouten beschreven die kunnen worden weer gegeven wanneer een resource niet kan worden gevonden tijdens de implementatie.

## <a name="symptom"></a>Symptoom

Wanneer uw sjabloon de naam bevat van een resource die niet kan worden omgezet, wordt een fout bericht van de volgende strekking weer gegeven:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Als u de functies [Reference](resource-group-template-functions-resource.md#reference) of [listkeys ophalen](resource-group-template-functions-resource.md#listkeys) gebruikt met een resource die niet kan worden omgezet, wordt de volgende fout weer gegeven:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Oorzaak

Resource Manager moet de eigenschappen van een resource ophalen, maar de resource in uw abonnement kan niet worden geïdentificeerd.

## <a name="solution-1---set-dependencies"></a>Oplossing 1: afhankelijkheden instellen

Als u probeert de ontbrekende resource in de sjabloon te implementeren, controleert u of u een afhankelijkheid moet toevoegen. Resource Manager optimaliseert de implementatie door zo mogelijk bronnen parallel te maken. Als u een resource moet implementeren na een andere resource, moet u het element **dependsOn** in uw sjabloon gebruiken. Als u bijvoorbeeld een web-app implementeert, moet het App Service plan bestaan. Als u nog niet hebt opgegeven dat de web-app afhankelijk is van het App Service-abonnement, maakt Resource Manager beide resources tegelijk. Er wordt een fout bericht weer gegeven dat de resource van het App Service plan niet kan worden gevonden, omdat deze nog niet bestaat bij het instellen van een eigenschap in de web-app. U kunt deze fout voor komen door de afhankelijkheid in de web-app in te stellen.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Maar u wilt voor komen dat afhankelijkheden worden ingesteld die niet nodig zijn. Wanneer u onnodige afhankelijkheden hebt, kunt u de duur van de implementatie verlengen door te voor komen dat bronnen die niet afhankelijk zijn van elkaar, parallel worden geïmplementeerd. Daarnaast kunt u circulaire afhankelijkheden maken die de implementatie blok keren. Met de functies [referentie](resource-group-template-functions-resource.md#reference) functie en [lijst *](resource-group-template-functions-resource.md#list) wordt een impliciete afhankelijkheid gemaakt voor de resource waarnaar wordt verwezen, wanneer die resource wordt geïmplementeerd in dezelfde sjabloon en wordt verwezen door de naam (niet resource-id). Daarom kunt u meer afhankelijkheden hebben dan de afhankelijkheden die zijn opgegeven in de eigenschap **dependsOn** . De functie [resourceId](resource-group-template-functions-resource.md#resourceid) maakt geen impliciete afhankelijkheid of valideert dat de resource bestaat. De functies [referentie](resource-group-template-functions-resource.md#reference) functie en [lijst *](resource-group-template-functions-resource.md#list) maken geen impliciete afhankelijkheid als de resource-id naar de resource wordt verwezen. Als u een impliciete afhankelijkheid wilt maken, geeft u de naam van de resource die is geïmplementeerd in dezelfde sjabloon door.

Wanneer u afhankelijkheids problemen ziet, moet u inzicht krijgen in de volg orde van de resource-implementatie. De volg orde van de implementatie bewerkingen bekijken:

1. Selecteer de implementatie geschiedenis voor uw resource groep.

   ![Implementatie geschiedenis selecteren](./media/resource-manager-not-found-errors/select-deployment.png)

2. Selecteer een implementatie in de geschiedenis en selecteer **gebeurtenissen**.

   ![implementatie gebeurtenissen selecteren](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Bekijk de volg orde van gebeurtenissen voor elke resource. Let op de status van elke bewerking. De volgende afbeelding toont bijvoorbeeld drie opslag accounts die parallel zijn geïmplementeerd. U ziet dat de drie opslag accounts op hetzelfde moment worden gestart.

   ![parallelle implementatie](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   De volgende afbeelding toont drie opslag accounts die niet parallel worden geïmplementeerd. Het tweede opslag account is afhankelijk van het eerste opslag account en het derde opslag account is afhankelijk van het tweede opslag account. Het eerste opslag account is gestart, geaccepteerd en voltooid voordat de volgende wordt gestart.

   ![sequentiële implementatie](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Oplossing 2: de resource uit de verschillende resource groep ophalen

Als de resource in een andere resource groep aanwezig is dan het item dat wordt geïmplementeerd in, gebruikt u de [functie resourceId](resource-group-template-functions-resource.md#resourceid) om de volledig gekwalificeerde naam van de resource op te halen.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Oplossing 3-controle functie referentie

Zoek naar een expressie die de functie [Reference](resource-group-template-functions-resource.md#reference) bevat. De waarden die u opgeeft, variëren op basis van het feit of de resource zich in dezelfde sjabloon, resource groep en abonnement bevindt. Controleer of u de vereiste parameter waarden voor uw scenario opgeeft. Als de resource zich in een andere resource groep bevindt, geeft u de volledige Resource-ID op. Als u bijvoorbeeld wilt verwijzen naar een opslag account in een andere resource groep, gebruikt u:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```