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
ms.openlocfilehash: c70e2c166bee14ac58ee88bd18baf0cc61702767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176509"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Maak in de Cloud Shell een [`az group create`](/cli/azure/group?view=azure-cli-latest#az-group-create) resourcegroep met de opdracht. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *Europa - west*. Als u alle ondersteunde locaties voor App Service op Linux in prijscategorie **Basic** wilt zien, voert u de opdracht [`az appservice list-locations --sku B1 --linux-workers-enabled`](/cli/azure/appservice?view=azure-cli-latest#az-appservice-list-locations) uit.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

In het algemeen maakt u een resourcegroep en resources in een regio bij u in de buurt. 

Wanneer de opdracht is voltooid, laat een JSON-uitvoer u de eigenschappen van de resource-groep zien.