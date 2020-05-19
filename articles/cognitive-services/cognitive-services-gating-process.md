---
title: Cognitive Services beperking-proces
titleSuffix: Azure Cognitive Services
description: Meer informatie over het Toep assen van vroege toegang tot nieuwe Cognitive Services containers en Api's.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599511"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Beperking-proces voor Azure Cognitive Services

> [!NOTE]
> Nadat een service aanbieding een geteste preview heeft voltooid, wordt er een ' niet-geplaatste ' open bare preview uitgevoerd waarvoor geen toepassing is vereist voor toegang. Na het preview-proces wordt het uitgebracht als algemeen beschikbaar (GA).

Wanneer er nieuwe Azure Cognitive Services-aanbiedingen worden geïntroduceerd, gaan ze via een geteste preview waarbij klanten toegang moeten aanvragen via een toepassing. Dit beperking-proces helpt u bij het identificeren van mogelijkheden voor verbeteringen in service aanbiedingen voordat ze algemeen beschikbaar zijn. 

In dit artikel vindt u instructies voor het toepassings proces voor het afwijzen van Cognitive Services-aanbiedingen.

## <a name="eligibility-and-approval-process"></a>Geschiktheids-en goedkeurings proces

Het beperking-proces is aanwezig om rente te meten en beter inzicht te krijgen in de behoeften van de klant. Het micro soft-team aanvaardt [toepassingen](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) van micro soft-commerciële klanten met een geldig Azure-abonnement en een geldig zakelijk scenario. Klanten kunnen hun toepassingen mogelijk weigeren wanneer:

 - Ze zijn niet gekoppeld aan een organisatie
 - Ze hebben geen geldig Azure-abonnement
 - De toepassing is ingediend via een persoonlijk e-mail adres ( @hotmail.com , @gmail.com , @yahoo.com )
 - Er is geen juiste rechtvaardiging of bedrijfs scenario beschikbaar

Gezien de vraag van verschillende klant segmenten, trachten we het goedkeurings proces te versnellen. Een tijd lijn kan echter niet worden doorgevoerd. Zodra een beslissing is genomen, neemt het micro soft-team contact met u op met u en uw account beheer team voor de volgende stappen. Bedankt voor uw begrip en geduld.

Als de toepassing is goedgekeurd, stuurt het micro soft-team een e-mail bericht met details, documentatie en richt lijnen. [Hier](https://azure.microsoft.com/pricing/details/cognitive-services/)vindt u Cognitive services prijs informatie.


Momenteel worden de volgende services aangeboden via het beperking-proces:

## <a name="gated-web-apis"></a>Web-Api's worden gegatedeerd

|Service  |
|---------|
|Anomalie detectie v2     | 

## <a name="gated-online-containers"></a>Online containers worden gegatedeerd

| Service                             | Container (s)                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Computer Vision][cv-containers]    | Lezen                                                                          |
| [Face][fa-containers]               | Face                                                                          |
| [Form Recognizer][fr-containers]    | Form Recognizer                                                               |
| [Speech Service-API][sp-containers] | Spraak naar tekst (aangepast en standaard) en tekst-naar-spraak (aangepast en standaard) |
| [Translator Text][tt-containers]    | Translator Text                                                               |

> [!IMPORTANT]
> Als een service of container aanbod niet wordt weer gegeven, is het niet gegatedeerd of niet beschikbaar.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meld u aan voor gated-Services](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
