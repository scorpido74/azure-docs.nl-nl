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
ms.openlocfilehash: 7803ac9009af1657e7f162d656898492a694e28f
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743549"
---
Als u toegang wilt tot de consolelogboeken die worden gegenereerd binnen uw toepassingscode in de App Service, schakelt u diagnostische logboekregistratie in door de volgende opdracht in de [Cloud Shell](https://shell.azure.com) uit te voeren:

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Mogelijk waarden voor `--level` zijn: `Error`, `Warning`, `Info` en `Verbose`. Elk hoger niveau omvat het vorige niveau. Bijvoorbeeld: `Error` omvat alleen foutberichten en `Verbose` omvat alle berichten.

Nadat diagnostische logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.
