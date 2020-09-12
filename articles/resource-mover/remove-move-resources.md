---
title: Resources verwijderen uit een Verplaats verzameling in azure resource-overschakeling
description: Meer informatie over het verwijderen van resources uit een Verplaats verzameling in azure resource-overschakeling.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 241ccbda67f7a2518d0c44a0d362673922ad4284
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652944"
---
# <a name="remove-resources-from-a-move-collection"></a>Resources verwijderen uit een verplaatste verzameling

In dit artikel wordt beschreven hoe u resources verwijdert uit een Verplaats verzameling in [Azure resource](overview.md)-overschakeling. Verzamelingen verplaatsen worden gebruikt bij het verplaatsen van Azure-resources tussen Azure-regio's.

## <a name="remove-a-resource-portal"></a>Een resource verwijderen (Portal)

Verwijder in de resource-overschakeling Portal de volgende opties:

1. In **meerdere regio's**selecteert u de resources die u wilt verwijderen uit de verzameling > **verwijderen**.

    ![Te verwijderen knop](./media/remove-move-resources/portal-select-resources.png)

1. Klik in **resources verwijderen**op **verwijderen**.

    ![Knop om resources uit een verplaatsings verzameling te verwijderen](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-resource-powershell"></a>Een resource verwijderen (Power shell)

U kunt als volgt een resource (in ons voor beeld van de PSDemoVM-machines) verwijderen uit een verzameling met behulp van Power shell:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus - Name PSDemoVM
```
**Verwachte uitvoer** 
 ![ Uitvoer tekst nadat een resource is verwijderd uit een verzameling voor verplaatsen](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Een verzameling verwijderen (Power shell)

Verwijder een volledige verzameling voor verplaatsen met behulp van Power shell, als volgt:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus 
```
**Verwachte uitvoer** 
 ![ Uitvoer tekst na het verwijderen van een verzameling voor verplaatsen](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Resource status van de VM na het verwijderen

Wat er gebeurt wanneer u een VM-resource uit een verzameling voor verplaatsen verwijdert, is afhankelijk van de status van de resource, zoals in de tabel wordt beschreven.

###  <a name="remove-vm-state"></a>VM-status verwijderen
**Resource status** | **VM** | **Netwerken**
--- | --- | --- 
**Toegevoegd aan verzameling verplaatsen** | Verwijderen uit de verzameling verplaatsen. | Verwijderen uit de verzameling verplaatsen. 
**Opgeloste afhankelijkheden/voorbereiden** | Verwijderen uit verzameling verplaatsen  | Verwijderen uit de verzameling verplaatsen. 
**Voor bereiding wordt uitgevoerd**<br/> (of een andere status in behandeling) | De Verwijder bewerking is mislukt met een fout.  | De Verwijder bewerking is mislukt met een fout.
**Voorbereiden is mislukt** | Verwijderen uit de verzameling verplaatsen.<br/>Verwijder alle items die zijn gemaakt in de doel regio, inclusief replica schijven. <br/><br/> Infrastructuur resources die zijn gemaakt tijdens de verplaatsing, moeten hand matig worden verwijderd. | Verwijderen uit de verzameling verplaatsen.  
**Initiëren verplaatsen in behandeling** | Verwijderen uit de verzameling verplaatsen.<br/><br/> Verwijder alle items die zijn gemaakt in de doel regio, inclusief VM, replica schijven, enzovoort.  <br/><br/> Infrastructuur resources die zijn gemaakt tijdens de verplaatsing, moeten hand matig worden verwijderd. | Verwijderen uit de verzameling verplaatsen.
**Verplaatsen is mislukt** | Verwijderen uit de verzameling verplaatsen.<br/><br/> Verwijder alle items die zijn gemaakt in de doel regio, inclusief VM, replica schijven, enzovoort.  <br/><br/> Infrastructuur resources die zijn gemaakt tijdens de verplaatsing, moeten hand matig worden verwijderd. | Verwijderen uit de verzameling verplaatsen.
**Door voeren in behandeling** | U wordt aangeraden de verplaatsing te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan. | U wordt aangeraden de verplaatsing te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan. 
**Door voeren is mislukt** | U wordt aangeraden om het verwijderen te annuleren zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan. | U wordt aangeraden de verplaatsing te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan.
**Verwijderen is voltooid** | De resource gaat terug naar de status **initiëren verplaatsen in behandeling** .<br/><br/> Het wordt verwijderd uit de verzameling verplaatsen, samen met iets dat u hebt gemaakt bij doel-VM, replica schijven, kluis, enzovoort.  <br/><br/> Infrastructuur resources die zijn gemaakt tijdens de verplaatsing, moeten hand matig worden verwijderd. <br/><br/> Infrastructuur resources die zijn gemaakt tijdens de verplaatsing, moeten hand matig worden verwijderd. |  De resource gaat terug naar de status **initiëren verplaatsen in behandeling** .<br/><br/> Het wordt verwijderd uit de verzameling verplaatsen.
**Kan niet verwijderen** | U wordt aangeraden de verplaatsingen te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> Daarna gaat de resource terug naar de status **initiëren verplaatsen in behandeling** , waarna u kunt door gaan. | U wordt aangeraden de verplaatsingen te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> Daarna gaat de resource terug naar de status **initiëren verplaatsen in behandeling** , waarna u kunt door gaan.
**Bron verwijderen in behandeling** | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt.  | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt.
**Bron verwijderen is mislukt** | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt. | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt.

## <a name="sql-resource-state-after-removing"></a>SQL-resource status na het verwijderen

Wat er gebeurt wanneer u een Azure SQL-resource uit een verzameling voor verplaatsen verwijdert, is afhankelijk van de status van de resource, zoals in de tabel wordt beschreven.

**Resource status** | **SQL** 
--- | --- 
**Toegevoegd aan verzameling verplaatsen** | Verwijderen uit de verzameling verplaatsen. 
**Opgeloste afhankelijkheden/voorbereiden** | Verwijderen uit verzameling verplaatsen 
**Voor bereiding wordt uitgevoerd**<br/> (of een andere status in behandeling)  | De Verwijder bewerking is mislukt met een fout. 
**Voorbereiden is mislukt** | Verwijderen uit verzameling verplaatsen<br/><br/>Verwijder alle items die zijn gemaakt in de doel regio. 
**Initiëren verplaatsen in behandeling** |  Verwijderen uit verzameling verplaatsen<br/><br/>Verwijder alle items die zijn gemaakt in de doel regio. De SQL database bestaat op dit moment en wordt verwijderd. 
**Verplaatsen is mislukt** | Verwijderen uit verzameling verplaatsen<br/><br/>Verwijder alle items die zijn gemaakt in de doel regio. De SQL database bestaat op dit moment en moet worden verwijderd. 
**Door voeren in behandeling** | U wordt aangeraden de verplaatsing te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan.
**Door voeren is mislukt** | U wordt aangeraden de verplaatsing te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> De resource gaat terug naar de status **initiëren verplaatsen in behandeling** en u kunt door gaan. 
**Verwijderen is voltooid** |  De resource gaat terug naar de status **initiëren verplaatsen in behandeling** .<br/><br/> Het wordt verwijderd uit de verzameling verplaatsen, samen met alles wat is gemaakt bij doel, inclusief SQL-data bases. 
**Kan niet verwijderen** | U wordt aangeraden de verplaatsingen te verwijderen, zodat de doel resources eerst worden verwijderd.<br/><br/> Daarna gaat de resource terug naar de status **initiëren verplaatsen in behandeling** , waarna u kunt door gaan. 
**Bron verwijderen in behandeling** | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt. 
**Bron verwijderen is mislukt** | Verwijderd uit de verzameling verplaatsen.<br/><br/> Er worden geen items verwijderd die in de doel regio worden gemaakt. 

## <a name="next-steps"></a>Volgende stappen

Verplaats [een virtuele machine](tutorial-move-region-virtual-machines.md) naar een andere regio met resource-overschakeling.