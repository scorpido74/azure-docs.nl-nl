---
title: 'Snelstart: uw eerste PowerShell-query'
description: In deze snelstart volgt u de stappen om de Resource Graph-module voor Azure PowerShell in te schakelen en uw eerste query uit te voeren.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: dd96324671f46f98d5b6c8bae1839a5b02d38b23
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240658"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>Snelstart: uw eerste ResourceGraph-query uitvoeren met Azure PowerShell

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de module voor Azure PowerShell is geïnstalleerd. In deze snelstartgids doorloopt u het proces voor het toevoegen van de module aan uw Azure PowerShell-installatie.

Aan het einde van dit proces hebt u de module toegevoegd aan de Azure PowerShell-installatie van uw keuze en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>De Resource Graph-module toevoegen

De module moet worden toegevoegd opdat Azure PowerShell query's kan uitvoeren voor Azure Resource Graph. Deze module kan worden gebruikt met lokaal geïnstalleerde PowerShell, met [Azure Cloud Shell](https://shell.azure.com)of met de [PowerShell Docker-afbeelding.](https://hub.docker.com/_/microsoft-powershell)

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

1. Valideren of de module is geïmporteerd en de nieuwste versie is (0.7.5):

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

1. Werk de query als volgt bij om de eigenschap **naam** te `order by`:

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
> Als de query geen resultaten retourneert van een abonnement `Search-AzGraph` waartoe u al toegang hebt, houdt u er rekening mee dat cmdlet standaard in de standaardcontext is. Om de lijst met abonnements-iD's te bekijken `(Get-AzContext).Account.ExtendedProperties.Subscriptions` die deel uitmaken van de standaardcontext, voer dit uit Als `Search-AzGraph` u wilt zoeken in alle abonnementen waartoe u toegang hebt, u de PSDefaultParameterValues voor cmdlet instellen door`$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`
   
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

In deze snelstart hebt u de Resource Graph-module toegevoegd aan uw Azure PowerShell-omgeving en uw eerste query uitgevoerd. Ga voor meer informatie over de taal van de resourcegrafiek verder naar de pagina met details van de querytaal.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)