---
title: 'Snelstart: uw eerste Azure CLI-query'
description: In deze snelstart volgt u de stappen om de Resource Graph-extensie voor Azure CLI in te schakelen en uw eerste query uit te voeren.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: e75152c720d94f084b43f855452e5e8ce4dc6bc8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240665"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-cli"></a>Snelstart: uw eerste resourcegrafiekquery uitvoeren met Azure CLI

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de extensie voor [Azure CLI](/cli/azure/) is geïnstalleerd. In deze snelstartgids doorloopt u het proces voor het toevoegen van de extensie aan uw Azure CLI-installatie. U kunt de extensie gebruiken met een lokale installatie van Azure CLI of via de [Azure Cloud Shell](https://shell.azure.com).

Aan het einde van dit proces hebt u de extensie toegevoegd aan de Azure CLI-installatie van uw keuze en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Als u geen Azure-abonnement hebt, maakt u een [gratis](https://azure.microsoft.com/free/) account voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>De Resource Graph-extensie toevoegen

De extensie moet worden toegevoegd opdat Azure CLI query's kan uitvoeren voor Azure Resource Graph. Deze extensie werkt overal waar Azure CLI kan worden gebruikt, met inbegrip van [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (zowel zelfstandige als in de portal), de [Azure CLI Docker-installatiekopie](https://hub.docker.com/r/microsoft/azure-cli/), of lokaal geïnstalleerd.

1. Controleer of de nieuwste Azure CLI is geïnstalleerd (ten minste **2.0.76**). Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/cli/azure/install-azure-cli-windows?view=azure-cli-latest) op.

1. In de gewenste Azure CLI-omgeving kunt u deze importeren met de volgende opdracht:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Valideren of de extensie is geïnstalleerd en de verwachte versie is (ten minste **1.0.0):**

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de Azure CLI-extensie is toegevoegd aan uw gewenste omgeving, kunt u een eenvoudige Resource Graph-query uitvoeren. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

1. Voer als volgt uw eerste Azure Resource Graph-query uit met de extensie `graph` en de opdracht `query`:

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Run Azure Resource Graph query
   az graph query -q 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Werk de query als volgt bij om de eigenschap **naam** te `order by`:

   ```azurecli-interactive
   # Run Azure Resource Graph query with 'order by'
   az graph query -q 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Hiermee worden de queryresultaten eerst beperkt en daarna geordend.

1. Werk de query als volgt bij om eerst te `order by` op de eigenschap **naam** en daarna de resultaten van de top vijf te `limit`:

   ```azurecli-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   az graph query -q 'Resources | project name, type | order by name asc | limit 5'
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en zoals verwacht. Ze zijn gesorteerd op de eigenschap **naam**, maar nog steeds beperkt tot de top 5-resultaten.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de Resource Graph-extensie uit uw Azure CLI-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```azurecli-interactive
# Remove the Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u de resourcegrafiek-extensie toegevoegd aan uw Azure CLI-omgeving en uw eerste query uitgevoerd. Ga voor meer informatie over de taal van de resourcegrafiek verder naar de pagina met details van de querytaal.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)