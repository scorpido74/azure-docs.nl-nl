---
title: Overzicht van sjabloon specificaties
description: Hierin wordt beschreven hoe u sjabloon specificaties maakt en deze deelt met andere gebruikers in uw organisatie.
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 47dcf44b35ad5c0b77dd0b88d683071a7f2f4ecb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096678"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Azure Resource Manager sjabloon specificaties (preview-versie)

Een sjabloon specificatie is een nieuw resource type voor het opslaan van een Azure Resource Manager sjabloon (ARM-sjabloon) in azure voor latere implementatie. Met dit resource type kunt u ARM-Sjablonen delen met andere gebruikers in uw organisatie. Net als elke andere Azure-resource kunt u gebruikmaken van op rollen gebaseerd toegangs beheer (RBAC) om de sjabloon specificatie te delen. gebruikers hebben alleen lees toegang tot de sjabloon specificatie nodig om de sjabloon te implementeren, zodat u de sjabloon kunt delen zonder dat anderen deze kunnen wijzigen.

**Micro soft. resources/templateSpecs** is het nieuwe bron type voor sjabloon specificaties. Het bestaat uit een hoofd sjabloon en een wille keurig aantal gekoppelde sjablonen. De sjabloon specificaties van Azure worden veilig opgeslagen in resource groepen. Sjabloon specificaties ondersteunen [versie beheer](#versioning).

Als u de sjabloon specificatie wilt implementeren, gebruikt u standaard Azure-hulpprogram ma's zoals Power shell, Azure CLI, Azure Portal, REST en andere ondersteunde Sdk's en clients. U gebruikt dezelfde opdrachten en geeft dezelfde para meters voor de sjabloon door.

Het voor deel van het gebruik van sjabloon specificaties is dat teams in uw organisatie geen sjablonen hoeven te maken of te kopiëren voor algemene scenario's. U maakt canonieke sjablonen en deelt deze. De sjablonen die u in een sjabloon specificatie opneemt, moeten worden geverifieerd door beheerders in uw organisatie om de vereisten en richt lijnen van de organisatie te volgen.

> [!NOTE]
> De sjabloon specificaties zijn momenteel beschikbaar als preview-versie. Als u deze wilt gebruiken, moet u [zich aanmelden voor de wacht lijst](https://aka.ms/templateSpecOnboarding).

## <a name="create-template-spec"></a>Sjabloon specificatie maken

In het volgende voor beeld ziet u een eenvoudige sjabloon voor het maken van een opslag account in Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "Premium_LRS",
        "tier": "Premium"
      }
    }
  ]
}
```

Wanneer u de sjabloon specificatie maakt, wordt het hoofd sjabloon bestand door gegeven aan de Power shell-of CLI-opdrachten. Als de hoofd sjabloon verwijst naar gekoppelde sjablonen, worden de opdrachten gevonden en verpakt om de sjabloon specificatie te maken. Zie [een sjabloon specificatie maken met gekoppelde sjablonen](#create-a-template-spec-with-linked-templates)voor meer informatie.

Een sjabloon specificatie maken met behulp van:

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0 -ResourceGroupName templateSpecsRg -TemplateJsonFile ./mainTemplate.json
```

U kunt alle sjabloon specificaties in uw abonnement weer geven met behulp van:

```azurepowershell
Get-AzTemplateSpec
```

U kunt details van een sjabloon specificatie bekijken, inclusief de versies met:

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

## <a name="deploy-template-spec"></a>Sjabloon specificatie implementeren

Nadat u de sjabloon specificatie hebt gemaakt, kunnen gebruikers met **Lees** toegang tot de sjabloon specificatie deze implementeren. Zie [zelf studie: een groep toegang verlenen tot Azure-resources met behulp van Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)voor meer informatie over het verlenen van toegang.

Sjabloon specificaties kunnen worden geïmplementeerd via de portal, Power shell, Azure CLI of als een gekoppelde sjabloon in een grotere sjabloon implementatie. Gebruikers in een organisatie kunnen een sjabloon specificatie implementeren naar elk bereik in azure (resource groep, abonnement, beheer groep of Tenant).

In plaats van een pad of URI voor een sjabloon door te geven, implementeert u een sjabloon specificatie door de resource-ID op te geven. De resource-ID heeft de volgende indeling:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

U ziet dat de resource-ID een versie nummer bevat voor de sjabloon specificatie.

U implementeert bijvoorbeeld een sjabloon spec met de volgende Power shell-opdracht.

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

In de praktijk voert u meestal uit `Get-AzTemplateSpec` om de id op te halen van de sjabloon specificatie die u wilt implementeren.

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0).Version.Id

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

## <a name="create-a-template-spec-with-linked-templates"></a>Een sjabloon specificatie met gekoppelde sjablonen maken

Als de hoofd sjabloon voor uw sjabloon specificatie verwijst naar gekoppelde sjablonen, kunnen de Power shell-en CLI-opdrachten de gekoppelde sjablonen automatisch vinden en verpakken vanaf uw lokale station. U hoeft Storage-accounts of opslag plaatsen niet hand matig te configureren voor het hosten van de sjabloon specificaties: alles is zelf opgenomen in de sjabloon specificatie resource.

Het volgende voor beeld bestaat uit een hoofd sjabloon met twee gekoppelde sjablonen. Het voor beeld is slechts een fragment van de sjabloon. U ziet dat er een eigenschap met de naam wordt gebruikt om een `relativePath` koppeling naar de andere sjablonen te maken. U moet `apiVersion` `2020-06-01` of hoger voor de implementaties Resource gebruiken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/webapp.json"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "relativePath": "artifacts/database.json"
                }
            }
        }
    ],
    "outputs": {}
}

```

Wanneer de Power shell-of CLI-opdracht voor het maken van de sjabloon specificatie wordt uitgevoerd voor het voor gaande voor beeld, wordt met de opdracht drie bestanden gevonden: de hoofd sjabloon, de sjabloon web-app ( `webapp.json` ) en de database sjabloon ( `database.json` ) en verpakt in de sjabloon specificatie.

Zie [zelf studie: een sjabloon specificatie maken met gekoppelde sjablonen](template-specs-create-linked.md)voor meer informatie.

## <a name="deploy-template-spec-as-a-linked-template"></a>Sjabloon specificatie als gekoppelde sjabloon implementeren

Zodra u een sjabloon specificatie hebt gemaakt, kunt u deze eenvoudig opnieuw gebruiken uit een ARM-sjabloon of een andere sjabloon specificatie. U koppelt een sjabloon specificatie door de resource-ID toe te voegen aan uw sjabloon. De gekoppelde sjabloon specificatie wordt automatisch geïmplementeerd wanneer u de hoofd sjabloon implementeert. Met dit gedrag kunt u modulaire sjabloon specificaties ontwikkelen en deze indien nodig opnieuw gebruiken.

U kunt bijvoorbeeld een sjabloon specificatie maken waarmee netwerk bronnen worden geïmplementeerd, en een andere sjabloon specificatie waarmee opslag bronnen worden geïmplementeerd. In ARM-sjablonen koppelt u op elk gewenst moment een koppeling naar deze twee sjabloon specificaties om netwerk-of opslag bronnen te configureren.

Het volgende voor beeld is vergelijkbaar met het vorige voor beeld, maar u gebruikt de `id` eigenschap om een koppeling te maken naar een sjabloon specificatie in plaats van de `relativePath` eigenschap om te koppelen aan een lokale sjabloon. Gebruiken `2020-06-01` voor API-versie voor de implementaties-resource. In het voor beeld bevinden de sjabloon specificaties zich in een resource groep met de naam **templateSpecsRG**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "networkingDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0')]"
                }
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "storageDeployment",
            ...
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0')]"
                }
            }
        }
    ],
    "outputs": {}
}
```

Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het koppelen van sjabloon specificaties.

## <a name="versioning"></a>Versiebeheer

Wanneer u een sjabloon specificatie maakt, geeft u er een versie nummer voor op. Wanneer u de sjabloon code herhaalt, kunt u een bestaande versie bijwerken (voor hotfixes) of een nieuwe versie publiceren. De versie is een teken reeks. U kunt ervoor kiezen om een versie systeem te volgen, met inbegrip van semantische versie beheer. Gebruikers van de sjabloon specificatie kunnen het versie nummer opgeven dat ze willen gebruiken om het te implementeren.

## <a name="next-steps"></a>Volgende stappen

* Als u een sjabloon specificatie wilt maken en implementeren, raadpleegt u [Quick Start: Create and Deploy template spec](quickstart-create-template-specs.md).

* Zie [zelf studie: een sjabloon specificatie maken met gekoppelde sjablonen](template-specs-create-linked.md)voor meer informatie over het koppelen van sjablonen in sjabloon specificaties.

* Zie [zelf studie: een sjabloon specificatie implementeren als gekoppelde sjabloon](template-specs-deploy-linked-template.md)voor meer informatie over het implementeren van een sjabloon specificatie als een gekoppelde sjabloon.
