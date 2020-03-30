---
title: bestand opnemen
description: bestand opnemen
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 02/27/2019
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: 8b5be0a438d9c5bb1fd0596368327c53a2d6c31f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176711"
---
> [!NOTE]
> Met de opdracht `az webapp up` worden de volgende acties uitgevoerd:
>
>- Een [standaardbrongroep maken](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).
>
>- Maak een standaard [app-serviceplan](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create).
>
>- [Maak een app](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) met de opgegeven naam.
>
>- Er worden via zip bestanden van de huidige werkmap naar de app [geïmplementeerd](https://docs.microsoft.com/azure/app-service/deploy-zip).
>
