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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67176514"
---
Als u een directe SSH-sessie met uw container wilt openen, moet uw app worden uitgevoerd.

Plak de volgende URL in uw browser en vervang \<de app-naam> door de naam van uw app:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Als u nog niet bent geverifieerd, moet u zich bij uw Azure-abonnement verifiëren om verbinding te maken. Na verificatie ziet u een in-browser shell, waar u opdrachten in de container kunt uitvoeren.

![SSH-verbinding](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
