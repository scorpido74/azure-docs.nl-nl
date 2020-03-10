---
title: 'Snelstartgids: uw eerste Power shell-query'
description: In deze Quick Start voert u de stappen uit om de module Resource Graph in te scha kelen voor Azure PowerShell en om uw eerste query uit te voeren.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: dd96324671f46f98d5b6c8bae1839a5b02d38b23
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78360208"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Snelstartgids: uw eerste resource grafiek query uitvoeren met behulp van Azure PowerShell

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de module voor Azure PowerShell is geïnstalleerd. In deze snelstartgids doorloopt u het proces voor het toevoegen van de module aan uw Azure PowerShell-installatie.

Aan het einde van dit proces hebt u de module toegevoegd aan de Azure PowerShell-installatie van uw keuze en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>De Resource Graph-module toevoegen

De module moet worden toegevoegd opdat Azure PowerShell query's kan uitvoeren voor Azure Resource Graph. Deze module kan worden gebruikt met lokaal geïnstalleerde Power shell, met [Azure Cloud shell](https://shell.azure.com), of met de [Power shell-docker-installatie kopie](https://hub.docker.com/_/microsoft-powershell).

### <a name="base-requirements"></a>Basisvereisten

Voor de Azure Resource Graph-module is de volgende software vereist:

- Azure PowerShell 1.0.0 of hoger. Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/powershell/azure/install-az-ps) op.

- PowerShellGet 2.0.1 of hoger. Als deze nog niet is geïnstalleerd of bijgewerkt, volgt u [deze instructies](/powershell/scripting/gallery/installing-psget) op.

### <a name="install-the-module"></a>Installeer de module

De Resource Graph-module voor PowerShell is **Az.ResourceGraph**.

1. Voer vanuit een PowerShell-prompt met **beheerdersrechten** de volgende opdracht uit:

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. Controleer of de module is geïmporteerd en de meest recente versie is (0.7.5):

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de Azure PowerShell-module is toegevoegd aan uw gewenste omgeving, kunt u een eenvoudige Resource Graph-query uitvoeren. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

1. Voer als volgt uw eerste Azure Resource Graph-query uit met de cmdlet `Search-AzGraph`:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Werk de query als volgt bij om de eigenschap `order by`naam**te**:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Hiermee worden de queryresultaten eerst beperkt en daarna geordend.

1. Werk de query als volgt bij om eerst te `order by` op de eigenschap **naam** en daarna de resultaten van de top vijf te `limit`:

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en zoals verwacht. Ze zijn gesorteerd op de eigenschap **naam**, maar nog steeds beperkt tot de top 5-resultaten.

> [!NOTE]
> Als de query geen resultaten oplevert van een abonnement waartoe u al toegang hebt, moet u er rekening mee houden dat `Search-AzGraph` cmdlet standaard wordt ingesteld op abonnementen in de standaard context. Als u de lijst met abonnements-Id's wilt zien die deel uitmaken van de standaard context, voert u deze `(Get-AzContext).Account.ExtendedProperties.Subscriptions` als u wilt zoeken in alle abonnementen waartoe u toegang hebt, kunt u de PSDefaultParameterValues instellen voor `Search-AzGraph`-cmdlet door `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}` uit te voeren
   
## <a name="clean-up-resources"></a>Resources opschonen

Als u de Resource Graph-module uit uw Azure PowerShell-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> Hiermee verwijdert u niet het modulebestand dat u eerder hebt gedownload. U verwijdert deze alleen uit de actieve PowerShell-sessie.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u de module Resource grafiek toegevoegd aan uw Azure PowerShell-omgeving en wordt uw eerste query uitgevoerd. Ga verder naar de pagina met details van de query taal voor meer informatie over de taal van de resource grafiek.

> [!div class="nextstepaction"]
> [Meer informatie over de query taal](./concepts/query-language.md)