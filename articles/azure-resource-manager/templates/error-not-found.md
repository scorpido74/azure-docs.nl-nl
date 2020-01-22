---
title: Fouten bij niet-gevonden resources
description: Hierin wordt beschreven hoe u fouten oplost wanneer een resource niet kan worden gevonden bij het implementeren met een Azure Resource Manager sjabloon.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: c3e19af24fa7fb850eadf3deb346180476943241
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310659"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Niet-gevonden fouten voor Azure-resources oplossen

In dit artikel worden de fouten beschreven die kunnen worden weer gegeven wanneer een resource niet kan worden gevonden tijdens de implementatie.

## <a name="symptom"></a>Symptoom

Wanneer uw sjabloon de naam bevat van een resource die niet kan worden omgezet, wordt een fout bericht van de volgende strekking weer gegeven:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Als u de functies [Reference](template-functions-resource.md#reference) of [listkeys ophalen](template-functions-resource.md#listkeys) gebruikt met een resource die niet kan worden omgezet, wordt de volgende fout weer gegeven:

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
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Maar u wilt voor komen dat afhankelijkheden worden ingesteld die niet nodig zijn. Wanneer u onnodige afhankelijkheden hebt, kunt u de duur van de implementatie verlengen door te voor komen dat bronnen die niet afhankelijk zijn van elkaar, parallel worden geïmplementeerd. Daarnaast kunt u circulaire afhankelijkheden maken die de implementatie blok keren. Met de functies [referentie](template-functions-resource.md#reference) functie en [lijst *](template-functions-resource.md#list) wordt een impliciete afhankelijkheid gemaakt voor de resource waarnaar wordt verwezen, wanneer die resource wordt geïmplementeerd in dezelfde sjabloon en wordt verwezen door de naam (niet resource-id). Daarom kunt u meer afhankelijkheden hebben dan de afhankelijkheden die zijn opgegeven in de eigenschap **dependsOn** . De functie [resourceId](template-functions-resource.md#resourceid) maakt geen impliciete afhankelijkheid of valideert dat de resource bestaat. De functies [referentie](template-functions-resource.md#reference) functie en [lijst *](template-functions-resource.md#list) maken geen impliciete afhankelijkheid als de resource-id naar de resource wordt verwezen. Als u een impliciete afhankelijkheid wilt maken, geeft u de naam van de resource die is geïmplementeerd in dezelfde sjabloon door.

Wanneer u afhankelijkheids problemen ziet, moet u inzicht krijgen in de volg orde van de resource-implementatie. De volg orde van de implementatie bewerkingen bekijken:

1. Selecteer de implementatie geschiedenis voor uw resource groep.

   ![Implementatie geschiedenis selecteren](./media/error-not-found/select-deployment.png)

2. Selecteer een implementatie in de geschiedenis en selecteer **gebeurtenissen**.

   ![implementatie gebeurtenissen selecteren](./media/error-not-found/select-deployment-events.png)

3. Bekijk de volg orde van gebeurtenissen voor elke resource. Let op de status van elke bewerking. De volgende afbeelding toont bijvoorbeeld drie opslag accounts die parallel zijn geïmplementeerd. U ziet dat de drie opslag accounts op hetzelfde moment worden gestart.

   ![parallelle implementatie](./media/error-not-found/deployment-events-parallel.png)

   De volgende afbeelding toont drie opslag accounts die niet parallel worden geïmplementeerd. Het tweede opslag account is afhankelijk van het eerste opslag account en het derde opslag account is afhankelijk van het tweede opslag account. Het eerste opslag account is gestart, geaccepteerd en voltooid voordat de volgende wordt gestart.

   ![sequentiële implementatie](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Oplossing 2: de resource uit de verschillende resource groep ophalen

Als de resource in een andere resource groep aanwezig is dan het item dat wordt geïmplementeerd in, gebruikt u de [functie resourceId](template-functions-resource.md#resourceid) om de volledig gekwalificeerde naam van de resource op te halen.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Oplossing 3-controle functie referentie

Zoek naar een expressie die de functie [Reference](template-functions-resource.md#reference) bevat. De waarden die u opgeeft, variëren op basis van het feit of de resource zich in dezelfde sjabloon, resource groep en abonnement bevindt. Controleer of u de vereiste parameter waarden voor uw scenario opgeeft. Als de resource zich in een andere resource groep bevindt, geeft u de volledige Resource-ID op. Als u bijvoorbeeld wilt verwijzen naar een opslag account in een andere resource groep, gebruikt u:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Oplossing 4: beheerde identiteit ophalen van resource

Als u een resource implementeert die impliciet een [beheerde identiteit](../../active-directory/managed-identities-azure-resources/overview.md)maakt, moet u wachten tot de resource is geïmplementeerd voordat u waarden ophaalt voor de beheerde identiteit. Als u de naam van de beheerde identiteit doorgeeft aan de [referentie](template-functions-resource.md#reference) functie, probeert Resource Manager de referentie op te lossen voordat de resource en de identiteit worden geïmplementeerd. In plaats daarvan geeft u de naam op van de resource waarmee de identiteit wordt toegepast. Deze aanpak zorgt ervoor dat de resource en de beheerde identiteit worden geïmplementeerd voordat Resource Manager de referentie functie verhelpt.

Gebruik in de functie verwijzing `Full` om alle eigenschappen op te halen, inclusief de beheerde identiteit.

Als u bijvoorbeeld de Tenant-ID wilt ophalen voor een beheerde identiteit die wordt toegepast op een schaalset voor virtuele machines, gebruikt u:

```json
"tenantId": "[reference(concat('Microsoft.Compute/virtualMachineScaleSets/',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```