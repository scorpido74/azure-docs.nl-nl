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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "67176711"
---
> [!NOTE]
> Met de opdracht `az webapp up` worden de volgende acties uitgevoerd:
>
>- Er wordt een standaard[resourcegroep](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) gemaakt.
>
>- Er wordt een standaard[serviceplan voor de app](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) gemaakt.
>
>- Er wordt een [app met de opgegeven naam](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) gemaakt.
>
>- Er worden via zip bestanden van de huidige werkmap naar de app [geïmplementeerd](https://docs.microsoft.com/azure/app-service/deploy-zip).
>
