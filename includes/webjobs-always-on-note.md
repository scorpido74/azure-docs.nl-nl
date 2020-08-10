---
title: Include-bestand
description: Include-bestand
services: app-service
author: ggailey777
ms.service: app-service
ms.topic: include
ms.date: 06/26/2020
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: c255be53a1809bf5dd3fc6b184852767dfec9c66
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009749"
---
> [!NOTE]
> Na 20 minuten inactiviteit treedt een time-out op in de webtoepassing. en alleen aanvragen aan de daadwerkelijke webtoepassing zorgen ervoor dat de timer opnieuw wordt ingesteld. De timer wordt niet gereset als de configuratie van de toepassing in de Azure-portal wordt bekeken of als aanvragen worden ingediend op de pagina met geavanceerde hulpmiddelen (`https://<app_name>.scm.azurewebsites.net`). Als u uw webtoepassing zodanig instelt dat deze doorlopend of gepland (timer-trigger) webjobs uitvoert, schakelt u de optie **Altijd aan** op de Azure-pagina **Configuratie** van uw webtoepassing in om ervoor te zorgen dat de webjobs betrouwbaar worden uitgevoerd. Deze functie is alleen beschikbaar in de [prijscategorieën](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Basic, Standard en Premium.
