---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 0dd6618bdee8e6810d414d4b04b16a1e0a9c90ed
ms.sourcegitcommit: 34eb5e4d303800d3b31b00b361523ccd9eeff0ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84905615"
---
U hebt vanuit de container toegang tot de consolelogboeken. Schakel eerst container logboek registratie in door de volgende opdracht uit te voeren in de Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Zodra logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.
