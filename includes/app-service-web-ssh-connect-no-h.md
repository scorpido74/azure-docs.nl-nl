---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67176514"
---
Als u een directe SSH-sessie met uw container wilt openen, moet uw app worden uitgevoerd.

Plak de volgende URL in \<uw browser en vervang app-naam> met uw app-naam:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Als u nog niet bent geverifieerd, moet u verifiëren met uw Azure-abonnement om verbinding te maken. Eenmaal geverifieerd, ziet u een in-browser shell, waar u opdrachten in uw container uitvoeren.

![SSH-verbinding](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
