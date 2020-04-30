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
ms.openlocfilehash: e5ba08da5c58ff486bc26f2c771dfef55452629a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75681020"
---
Voeg, eenmaal terug in het lokale terminalvenster, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang * \<deploymentLocalGitUrl-uit-Create-Step>* door de URL van de externe Git-server die u hebt opgeslagen in [een web-app maken](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Als er door git Credential Manager om referenties wordt gevraagd, voert u de referenties in die u hebt gemaakt in [een implementatie gebruiker configureren](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), niet de referenties die u gebruikt om u aan te melden bij de Azure Portal.

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:
