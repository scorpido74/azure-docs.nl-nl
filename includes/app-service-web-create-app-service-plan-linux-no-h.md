---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 12/20/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 25003269fb6e00cadcc14d2356308cae54c70bf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82085298"
---
Maak in de Cloud Shell een App Service plan in de resource groep met de [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) opdracht.

<!-- [!INCLUDE [app-service-plan](app-service-plan-linux.md)] -->

In het volgende voor beeld wordt een App Service `myAppServicePlan` plan gemaakt met de naam in`--sku F1`de **gratis** prijs categorie () en`--is-linux`in een Linux-container ().

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku F1 --is-linux
```

Wanneer het App Service-plan is gemaakt, toont de Azure CLI soortgelijke informatie als in het volgende voorbeeld:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "linux",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>
