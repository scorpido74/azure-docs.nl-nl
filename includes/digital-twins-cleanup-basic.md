---
author: baanders
description: bestand opnemen voor het opschonen van een eenvoudig Azure Digital Twins-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 2e095a7b286a8860535d6b58fa93098735b30c87
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92372311"
---
Als u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen met de volgende stappen.

Met behulp van [Azure Cloud Shell](https://shell.azure.com) kunt u alle Azure-resources in een resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Hiermee verwijdert u de resourcegroep en het Azure Digital Twins-exemplaar.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Open een Azure Cloud Shell en voer de volgende opdracht uit om de resourcegroep en alles daarin te verwijderen.

```azurecli
az group delete --name <your-resource-group>
```