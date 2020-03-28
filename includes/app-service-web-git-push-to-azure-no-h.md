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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "75681020"
---
Voeg, eenmaal terug in het lokale terminalvenster, een externe Azure-instantie toe aan uw lokale Git-opslagplaats. Vervang * \<deploymentLocalGitUrl-from-create-step>* door de URL van de Git-afstandsbediening die u hebt opgeslagen vanuit [Een web-app maken.](#create-a-web-app)

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Wanneer Git Credential Manager u om referenties vraagt, controleert u of u de referenties invoert die u hebt gemaakt in [Een implementatiegebruiker configureren,](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user)niet de referenties die u gebruikt om u aan te melden bij de Azure-portal.

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:
