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
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67176511"
---
U hebt toegang tot de console logboeken die in de container zijn gegenereerd. Schakel eerst container logboek registratie in door de volgende opdracht uit te voeren in de Cloud Shell:

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --docker-container-logging filesystem
```

Zodra logboekregistratie is ingeschakeld, voert u de volgende opdracht uit om de logboekstream te zien:

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

Als u de consolelogboeken niet meteen ziet, probeert u het opnieuw na 30 seconden.

> [!NOTE]
> U kunt de logboek bestanden ook vanuit de browser controleren op `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Als u het streamen van Logboeken op `Ctrl` + `C`elk gewenst moment wilt stoppen, typt u.
