---
title: API-verwijzing - Inhoudsmoderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over de verschillende inhoudsmoderatie- en beoordelings-API's voor inhoudsmoderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: pafarley
ms.openlocfilehash: 7fc46d06b68dca074da060b4866186a6242ffad2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72757380"
---
# <a name="content-moderator-api-reference"></a>Api-verwijzing naar inhoudsmoderator

U op de volgende manieren aan de slag gaan met API's voor Azure-inhoudsmoderator:

- Abonneer u in de Azure-portal [op de API voor inhoudsmoderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Zie [Inhoudsmoderator op het web](quick-start.md) uitproberen om je aan te melden met het hulpprogramma [Inhoudsmoderator .](https://contentmoderator.cognitive.microsoft.com/)

## <a name="moderation-apis"></a>Beheer-API's

U de volgende API's voor inhoudsmoderator gebruiken om uw postmoderatieworkflows in te stellen.

| Beschrijving | Naslaginformatie |
| -------------------- |-------------|
| **Afbeeldingstoezicht-API**<br /><br />Scan afbeeldingen en detecteer potentiële inhoud voor volwassenen en racy met behulp van tags, betrouwbaarheidsscores en andere geëxtraheerde informatie. <br /><br />Gebruik deze informatie om de inhoud in uw werkstroom na moderatie te publiceren, te weigeren of te controleren. <br /><br />| [Referentie van API voor afbeeldingsbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Referentie van API voor afbeeldingsbeheer")   |
| **Teksttoezicht-API**<br /><br />Tekstinhoud scannen. Godslasteringsvoorwaarden en persoonsgegevens worden geretourneerd. <br /><br />Gebruik deze informatie om de inhoud in uw werkstroom na moderatie te publiceren, te weigeren of te controleren.<br /><br /> | [Referentie voor API voor tekstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Referentie voor API voor tekstbeheer")   |
| **API voor videobeheer**<br /><br />Scan video's en detecteer potentiële inhoud voor volwassenen en racy. <br /><br />Gebruik deze informatie om de inhoud in uw werkstroom na moderatie te publiceren, te weigeren of te controleren.<br /><br /> | [Overzicht van videomoderatie-API](video-moderation-api.md "Overzicht van videomoderatie-API")   |
| **API voor lijstbeheer**<br /><br />Aangepaste uitsluitings- of inclusielijsten met afbeeldingen en tekst maken en beheren. Als dit is ingeschakeld, worden de **bewerkingen Image - Match** en **Text - Screen** fuzzy matching van de ingediende inhoud uitgevoerd op uw aangepaste lijsten. <br /><br />Voor efficiëntie u de op machine learning gebaseerde moderatiestap overslaan.<br /><br /> | [Verwijzing naar API-lijstbeheer](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "Verwijzing naar API-lijstbeheer")   |

## <a name="review-apis"></a>Beoordelings-API's

De Review API's hebben de volgende componenten:

| Beschrijving | Naslaginformatie |
| -------------------- |-------------|
| **Taken**<br /><br /> Initiëren van scan- en-reviewmoderatieworkflows voor zowel afbeeldings- als tekstinhoud. Een moderatietaak scant uw inhoud met behulp van de Image Moderation API en de Text Moderation API. Moderatietaken gebruiken de gedefinieerde en standaardwerkstromen om beoordelingen te genereren. <br /><br />Nadat een menselijke moderator de automatisch toegewezen tags en voorspellingsgegevens heeft beoordeeld en een beslissing voor inhoudsmatiging heeft ingediend, stuurt de Beoordelings-API alle informatie naar uw API-eindpunt.<br /><br /> | [Taakverwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Taakverwijzing")   |
| **Beoordelingen**<br /><br />Gebruik het hulpmiddel Controleren om direct afbeeldings- of tekstrecensies voor menselijke moderators te maken.<br /><br /> Nadat een menselijke moderator de automatisch toegewezen tags en voorspellingsgegevens heeft beoordeeld en een beslissing voor inhoudsmatiging heeft ingediend, stuurt de Beoordelings-API alle informatie naar uw API-eindpunt.<br /><br /> | [Referentie van de controle](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Referentie van de controle")   |
| **Werkstromen**<br /><br />Maak, werk en ontvang details over de aangepaste werkstromen die uw team maakt. U definieert werkstromen met het gereedschap Controleren. <br /> <br />Werkstromen maken doorgaans gebruik van Inhoudsmoderator, maar kunnen ook bepaalde andere API's gebruiken die beschikbaar zijn als connectoren in het hulpprogramma Controleren.<br /><br /> | [Werkstroomverwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Werkstroomverwijzing")   |