---
author: baanders
description: bestand opnemen voor het opschonen van een eenvoudig Azure Digital Twins-exemplaar
ms.service: digital-twins
ms.topic: include
ms.date: 8/13/2020
ms.author: baanders
ms.openlocfilehash: 29a07ffa917153c0cb062d34e2807d43d039a373
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494645"
---
Als u de resources die u in deze zelfstudie hebt gemaakt niet meer nodig hebt, kunt u ze verwijderen met de volgende stappen.

Met behulp van [Azure Cloud Shell](https://shell.azure.com) kunt u alle Azure-resources in een resourcegroep verwijderen met de opdracht [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete). Hiermee verwijdert u de resourcegroep en het Azure Digital Twins-exemplaar.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle resources daarin worden permanent verwijderd. Zorg ervoor dat u niet per ongeluk de verkeerde resourcegroep of resources verwijdert. 

Open een Azure Cloud Shell en voer de volgende opdracht uit om de resourcegroep en alles daarin te verwijderen.

```azurecli-interactive
az group delete --name <your-resource-group>
```