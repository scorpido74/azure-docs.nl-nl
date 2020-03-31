---
title: Implementatiemodi
description: Beschrijft hoe u opgeven of u een volledige of incrementele implementatiemodus wilt gebruiken met Azure Resource Manager.
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 1077d92f076797fb03c4fe750b353e2306f9b6de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460242"
---
# <a name="azure-resource-manager-deployment-modes"></a>Implementatiemodi azure resource manager

Wanneer u uw resources implementeert, geeft u op dat de implementatie een incrementele update of een volledige update is. Het verschil tussen deze twee modi is hoe Resource Manager omgaat met bestaande resources in de resourcegroep die niet in de sjabloon staan.

Voor beide modi probeert Resource Manager alle resources te maken die in de sjabloon zijn opgegeven. Als de resource al bestaat in de resourcegroep en de instellingen ongewijzigd zijn, wordt er geen bewerking uitgevoerd voor die resource. Als u de eigenschapswaarden voor een resource wijzigt, wordt de resource bijgewerkt met die nieuwe waarden. Als u de locatie of het type bestaande resource probeert bij te werken, mislukt de implementatie met een fout. Implementeer in plaats daarvan een nieuwe resource met de locatie of het type dat u nodig hebt.

De standaardmodus is incrementeel.

## <a name="complete-mode"></a>Volledige modus

In de volledige modus **verwijdert** Resourcemanager resources die in de resourcegroep bestaan, maar die niet zijn opgegeven in de sjabloon.

Als uw sjabloon een resource bevat die niet is geïmplementeerd omdat [de voorwaarde](conditional-resource-deployment.md) wordt geëvalueerd, is het resultaat afhankelijk van de REST API-versie die u gebruikt om de sjabloon te implementeren. Als u een versie gebruikt die eerder is dan 2019-05-10, wordt de bron **niet verwijderd.** Met 2019-05-10 of hoger wordt de bron **verwijderd.** De nieuwste versies van Azure PowerShell en Azure CLI verwijderen de bron.

Wees voorzichtig met behulp van de volledige modus met [kopieerlussen.](copy-resources.md) Alle resources die niet in de sjabloon zijn opgegeven na het oplossen van de kopieerlus, worden verwijderd.

Als u implementeert in [meer dan één resourcegroep in een sjabloon,](cross-resource-group-deployment.md)komen resources in de resourcegroep die in de implementatiebewerking is opgegeven, in aanmerking om te worden verwijderd. Bronnen in de secundaire resourcegroepen worden niet verwijderd.

Er zijn enkele verschillen in de manier waarop resourcetypen verwijderingen in de volledige modus verwerken. Bovenliggende bronnen worden automatisch verwijderd wanneer deze niet in een sjabloon staan die in de volledige modus is geïmplementeerd. Sommige onderliggende bronnen worden niet automatisch verwijderd wanneer ze niet in de sjabloon staan. Deze onderliggende bronnen worden echter verwijderd als de bovenliggende bron wordt verwijderd.

Als uw resourcegroep bijvoorbeeld een DNS-zone (Microsoft.Network/dnsZones-brontype) en een CNAME-record (Microsoft.Network/dnsZones/CNAME-brontype) bevat, is de DNS-zone de bovenliggende bron voor de CNAME-record. Als u de volledige modus implementeert en de DNS-zone niet in uw sjabloon opneemt, worden de DNS-zone en de CNAME-record beide verwijderd. Als u de DNS-zone in uw sjabloon opneemt, maar de CNAME-record niet opneemt, wordt de CNAME niet verwijderd.

Zie [Verwijdering van Azure-resources voor implementaties in de volledige modus](complete-mode-deletion.md)voor een lijst met hoe resourcetypen verwijdering verwerken.

Als de brongroep is [vergrendeld,](../management/lock-resources.md)worden de resources in de volledige modus niet verwijderd.

> [!NOTE]
> Alleen sjablonen op basisniveau ondersteunen de volledige implementatiemodus. Voor [gekoppelde of geneste sjablonen](linked-templates.md)moet u de incrementele modus gebruiken.
>
> [Implementaties op abonnementsniveau](deploy-to-subscription.md) ondersteunen de volledige modus niet.
>
> Momenteel ondersteunt de portal geen volledige modus.
>

## <a name="incremental-mode"></a>Incrementele modus

In de incrementele modus laat Resource Manager **ongewijzigde** resources achter die in de resourcegroep bestaan, maar die niet zijn opgegeven in de sjabloon. Resources in de sjabloon **worden toegevoegd aan** de resourcegroep.

> [!NOTE]
> Wanneer u een bestaande resource opnieuw implementeert in de incrementele modus, worden alle eigenschappen opnieuw toegepast. De **eigenschappen worden niet stapsgewijs toegevoegd.** Een veelvoorkomend misverstand is om te denken dat eigenschappen die niet in de sjabloon zijn opgegeven, ongewijzigd blijven. Als u bepaalde eigenschappen niet opgeeft, interpreteert Resource Manager de implementatie als het overschrijven van deze waarden. Eigenschappen die niet in de sjabloon zijn opgenomen, worden gereset naar de standaardwaarden. Geef alle niet-standaardwaarden voor de resource op, niet alleen de waarden die u bijwerkt. De resourcedefinitie in de sjabloon bevat altijd de uiteindelijke status van de resource. Het kan geen gedeeltelijke update van een bestaande bron vertegenwoordigen.

## <a name="example-result"></a>Voorbeeldresultaat

Als u het verschil tussen incrementele en volledige modi wilt illustreren, moet u het volgende scenario overwegen.

**Resourcegroep** bevat:

* Bron A
* Bron B
* Bron C

**Sjabloon** bevat:

* Bron A
* Bron B
* Bron D

Wanneer de **resourcegroep** in incrementele modus wordt geïmplementeerd, heeft:

* Bron A
* Bron B
* Bron C
* Bron D

Wanneer resource C in **de volledige** modus wordt geïmplementeerd, wordt deze verwijderd. De resourcegroep heeft:

* Bron A
* Bron B
* Bron D

## <a name="set-deployment-mode"></a>Implementatiemodus instellen

Als u de implementatiemodus wilt instellen bij `Mode` het implementeren met PowerShell, gebruikt u de parameter.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Als u de implementatiemodus wilt instellen bij `mode` het implementeren met Azure CLI, gebruikt u de parameter.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --mode Complete \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

In het volgende voorbeeld wordt een gekoppelde sjabloon weergegeven die is ingesteld op incrementele implementatiemodus:

```json
"resources": [
  {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "properties": {
          "mode": "Incremental",
          <nested-template-or-external-template>
      }
  }
]
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager-sjablonen ontwerpen](template-syntax.md)voor meer informatie over het maken van Resource Manager-sjablonen.
* Zie Een toepassing implementeren [met azure resource manager-sjabloon](deploy-powershell.md)voor meer informatie over het implementeren van resources.
* Zie [Azure REST API](/rest/api/)als u de bewerkingen voor een resourceprovider wilt weergeven.
