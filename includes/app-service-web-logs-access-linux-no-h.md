---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e2c5794e5ce6e23b60bff513562f69c9333d6e34
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743786"
---
U hebt vanuit de container toegang tot de consolelogboeken.

Schakel eerst logboekregistratie voor containers in door de volgende opdracht uit te voeren:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

Vervang `<app-name>` en `<resource-group-name>` door de namen die van toepassing zijn op uw web-app.

Zodra logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

U kunt op elk gewenst moment stoppen met logboekstreaming door **Ctrl**+**C** te typen.

U kunt ook de logboekbestanden in een browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.
