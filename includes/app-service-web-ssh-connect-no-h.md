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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "67176514"
---
Als u een directe SSH-sessie opent met uw container, moet uw app worden uitgevoerd.

Plak de volgende URL in uw browser en vervang \<app-name> door de naam van uw app:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Als u nog niet bent geverifieerd moet u zich verifiëren met uw Azure-abonnement om verbinding te maken. Nadat u bent geverifieerd, ziet u een shell in de browser waarin u opdrachten binnen uw container kunt uitvoeren.

![SSH-verbinding](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
