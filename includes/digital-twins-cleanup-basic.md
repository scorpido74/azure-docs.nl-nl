---
author: baanders
description: bestand opnemen voor het opschonen van een eenvoudig Azure Digital Twins-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: ab179c5f0c56a7d6fd2ebd41e13d27a832512b54
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2020
ms.locfileid: "94358071"
---
Als u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen met de volgende stappen.

Met behulp van [Azure Cloud Shell](https://shell.azure.com) kunt u alle Azure-resources in een resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Met deze opdracht verwijdert u de resourcegroep en het Azure Digital Twins-exemplaar.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert.

Open Azure Cloud Shell en voer de volgende opdracht uit om de resourcegroep en alles daarin te verwijderen.

```azurecli-interactive
az group delete --name <your-resource-group>
```