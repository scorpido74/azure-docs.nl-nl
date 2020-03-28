---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: b59b45de04ebb717dfe55eb17c9dbd92f7523976
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176516"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Maak in de Cloud Shell een [`az group create`](/cli/azure/group?view=azure-cli-latest) resourcegroep met de opdracht. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *Europa - west*. Als u alle ondersteunde locaties voor App Service in de **Gratis** laag wilt zien, voert u de opdracht [`az appservice list-locations --sku FREE`](/cli/azure/appservice?view=azure-cli-latest) uit.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

In het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt. 

Wanneer de opdracht is voltooid, laat een JSON-uitvoer u de eigenschappen van de resource-groep zien.