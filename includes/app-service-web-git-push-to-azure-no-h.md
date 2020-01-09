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
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/06/2020
ms.locfileid: "75681020"
---
Voeg in het lokale terminal venster een externe Azure-toepassing toe aan uw lokale Git-opslag plaats. Vervang *\<deploymentLocalGitUrl-from-create-step>* door de URL van de externe Git-instantie die u hebt opgeslagen in [Een web-app maken](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Push naar de externe Azure-instantie om uw app te implementeren met de volgende opdracht. Als er door git Credential Manager om referenties wordt gevraagd, voert u de referenties in die u hebt gemaakt in [een implementatie gebruiker configureren](/azure/app-service/containers/tutorial-python-postgresql-app#configure-a-deployment-user), niet de referenties die u gebruikt om u aan te melden bij de Azure Portal.

```bash
git push azure master
```

Het kan enkele minuten duren voor deze opdracht is uitgevoerd. De opdracht geeft informatie weer die lijkt op het volgende voorbeeld:
