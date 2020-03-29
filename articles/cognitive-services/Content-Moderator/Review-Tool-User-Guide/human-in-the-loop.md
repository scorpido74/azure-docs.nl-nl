---
title: Concepten voor leesprogramma's leren - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over de tool Inhoudsmoderator Review, een website die een gecombineerde AI- en menselijke beoordelingsmoderatie-inspanning coördineert.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: a23e6d46ee6e79fd7a5cabf4434c561f7d83b31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76169509"
---
# <a name="content-moderator-review-tool"></a>Hulpmiddel Inhoudsmoderator-beoordeling

Azure Content Moderator biedt services om machine learning-inhoudsmoderatie te combineren met menselijke beoordelingen, en de website van de [beoordelingstool](https://contentmoderator.cognitive.microsoft.com) is een gebruiksvriendelijke front-end die gedetailleerde toegang geeft tot deze services.

![Het dashboard van het beoordelingsprogramma in een browser](./images/0-dashboard.png)

## <a name="what-it-does"></a>Wat het doet

Met [het hulpprogramma Controleren](https://contentmoderator.cognitive.microsoft.com)u, wanneer het wordt gebruikt in combinatie met de api's voor machine-ondersteunde moderatie, de volgende taken in het inhoudsmoderatieproces uitvoeren:

- Gebruik één set hulpprogramma's om inhoud in meerdere indelingen te beheren (tekst, afbeelding en video).
- Automatiseer het maken van menselijke [beoordelingen](../review-api.md#reviews) wanneer moderatie API-resultaten binnenkomen.
- Toewijzen of escaleren van inhoudsbeoordelingen aan meerdere beoordelingsteams, ingedeeld op inhoudscategorie of ervaringsniveau.
- Gebruik standaard- of aangepaste logicafilters[(werkstromen)](../review-api.md#workflows)om inhoud te sorteren en bij te houden, zonder code te schrijven.
- Gebruik [connectors](./configure.md#connectors) om inhoud te verwerken met Microsoft PhotoDNA-, Text Analytics- en Face-services naast de API's voor inhoudsmoderator.
- Bouw uw eigen connector om workflows te maken voor elk API- of bedrijfsproces.
- Krijg belangrijke prestatiestatistieken voor uw contentmoderatieprocessen.

## <a name="review-tool-dashboard"></a>Dashboard voor hulpprogramma's controleren

Op het tabblad **Dashboard** ziet u belangrijke statistieken voor inhoudsbeoordelingen die in de tool worden uitgevoerd. Bekijk het aantal totaal-, volledige en in behandeling zijnde beoordelingen voor afbeeldings-, tekst- en video-inhoud. U ook de uitsplitsing zien van gebruikers en teams die beoordelingen hebben voltooid, evenals de moderatietags die zijn toegepast.

![Dashboard weergeven](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Gereedschapsreferenties controleren

Wanneer u zich aanmeldt met het [hulpprogramma Controleren,](https://contentmoderator.cognitive.microsoft.com)wordt u gevraagd een Azure-regio voor uw account te selecteren. Dit komt omdat de [tool Review](https://contentmoderator.cognitive.microsoft.com) een gratis proefsleutel genereert voor Azure Content Moderator-services. u hebt deze sleutel nodig om toegang te krijgen tot een van de services van een REST-oproep of client SDK. U de URL van uw sleutel en API-eindpunt bekijken door **Instellingenreferenties** > **te**selecteren.

![Referenties voor inhoudsmoderator](images/settings-6-credentials.png)

## <a name="next-steps"></a>Volgende stappen

Zie [Het hulpprogramma Controleren configureren](./configure.md) voor meer informatie over het openen van gereedschapsbronnen controleren en instellingen wijzigen.