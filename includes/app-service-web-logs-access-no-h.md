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
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84905864"
---
Om toegang te krijgen tot de console logboeken die zijn gegenereerd in de toepassings code in App Service, schakelt u de diagnostische logboek registratie in door de volgende opdracht uit te voeren in de [Cloud shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Mogelijke waarden voor `--level` zijn: `Error` ,, en `Warning` `Info` `Verbose` . Elk volgende niveau omvat het vorige niveau. Bijvoorbeeld: `Error` bevat alleen fout berichten en `Verbose` bevat alle berichten.

Wanneer diagnostische logboek registratie is ingeschakeld, voert u de volgende opdracht uit om de logboek stroom te bekijken:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt ook de logboekbestanden van de browser inspecteren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

U kunt op elk gewenst moment `Ctrl`+`C` typen om te stoppen met logboekstreaming.
