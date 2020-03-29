---
title: Speech Service gratis uitproberen
titleSuffix: Azure Cognitive Services
description: Aan de slag met Spraakservice is eenvoudig en betaalbaar. Er zijn twee opties gratis beschikbaar, zodat u ontdekken wat de service kan doen en beslissen of het geschikt is voor uw behoeften.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: erhopf
ms.custom: seodec18, seo-javascript-october2019
ms.openlocfilehash: f74e3ea3d20ad2666b434e009cf62add6f88d200
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219663"
---
# <a name="try-the-speech-service-for-free"></a>Speech Service gratis uitproberen

In dit artikel kiest u een optie om de Spraakservice eenvoudig gratis te testen, zodat u ontdekken wat de service kan doen en beslissen of deze geschikt is voor uw behoeften. Kies een van de twee volgende opties, afhankelijk van uw situatie en use-case:

- [Optie 1:](#no-card)Ontvang onmiddellijk **gratis API-sleutels** voor proefversies zonder creditcardgegevens (u moet een bestaand Azure-account hebben). De **gratis proefperiode** duurt 30 dagen en gegevens worden aan het einde verwijderd. Deze optie is het beste voor snel experimenteren met de service.
- [Optie 2](#new-resource): Maak gratis een nieuwe spraakbron in Azure met een **gratis abonnement** (creditcardgegevens vereist). Een **gratis abonnement** kent vooral strengere tarieflimieten dan een betaald abonnement. Deze optie is het beste als u de service wilt testen, maar ook van plan bent om in de toekomst te upgraden naar een betaald abonnement en gegevens niet wilt verliezen.

## <a name="try-the-speech-service-without-credit-card-info"></a><a id="no-card"></a>Probeer de spraakservice zonder creditcardgegevens

Voer de volgende stappen uit om een gratis proefperiode van 30 dagen te activeren en API-sleutels te krijgen. Uw proefperiode begint onmiddellijk zodra de volgende stappen zijn voltooid.

1. Ga naar [Probeer Cognitive Service](https://azure.microsoft.com/try/cognitive-services/).
1. Selecteer het tabblad **Spraak-API's.**
1. Kies **API-sleutel opdoen**.

U krijgt factureringsopties te zien. Kies de gratis optie en lees en keur de gebruikersovereenkomst goed. U krijgt toetsen te zien die u gebruiken om de spraakservice 30 dagen gratis uit te proberen.

## <a name="try-the-speech-service-by-creating-an-azure-resource"></a><a id="new-resource"></a>Probeer de spraakservice door een Azure-bron te maken

Voor de volgende stappen hebt u zowel een Microsoft-account als een Azure-account nodig. Als u geen Microsoft-account hebt, u zich gratis [Microsoft account portal](https://account.microsoft.com/account)aanmelden voor een accountportal. Selecteer **Aanmelden bij Microsoft** en selecteer, wanneer u wordt gevraagd om u aan te melden, de optie Een **Microsoft-account maken**. Volg de stappen om uw nieuwe Microsoft-account te maken en te verifiëren.

Zodra u een Microsoft-account hebt, gaat u naar de [aanmeldingspagina van Azure,](https://azure.microsoft.com/free/ai/)selecteert u **Gratis starten**en maakt u een nieuw Azure-account met een Microsoft-account.

> [!NOTE]
> De Spraakservice heeft twee serviceniveaus: gratis en abonnement, die verschillende beperkingen en voordelen hebben. Wanneer u zich aanmeldt voor een gratis Azure-account, wordt het geleverd met $ 200 aan servicetegoed dat u toepassen op een betaald spraakserviceabonnement, dat maximaal 30 dagen geldig is.
>
> Als u de gratis spraakservicelaag met een laag volume gebruikt, u dit gratis abonnement behouden, zelfs nadat uw gratis proefperiode of servicetegoed is verlopen.
>
> Zie [Cognitive Services-prijzen voor](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)meer informatie - Spraakservice .

### <a name="create-the-resource"></a>De resource maken

Ga als lid van uw Azure-account naar een spraakservicebron (gratis of betaalde laag):

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met uw Microsoft-account.

1. Selecteer **Een resource maken** linksboven in de portal. Als u **Geen Resource maken**ziet, u deze altijd vinden door het samengevouwen menu linksboven te selecteren:

   ![samengevouwen navigatieknop](media/index/collapsed-nav.png)

1. Typ **'spraak'** in het zoekvak in het venster Nieuw en druk op ENTER.

1. Selecteer **Spraak**in de zoekresultaten .

   ![zoekresultaten voor spraak](media/index/speech-search.png)

1. Selecteer **Maken**en vervolgens:

   - Geef een unieke naam voor uw nieuwe bron. Met de naam u onderscheid maken tussen meerdere abonnementen die aan dezelfde service zijn gekoppeld.
   - Kies het Azure-abonnement waaraan de nieuwe bron is gekoppeld om te bepalen hoe de kosten worden gefactureerd.
   - Kies de [regio](regions.md) waar de resource wordt gebruikt.
   - Kies een gratis (F0) of betaalde (S0) prijscategorie. Selecteer **Volledige prijsdetails weergeven**voor volledige informatie over prijzen en gebruiksquota voor elke laag.
   - Maak een nieuwe brongroep voor dit spraakabonnement of wijs het abonnement toe aan een bestaande brongroep. Met resourcegroepen u uw verschillende Azure-abonnementen georganiseerd houden.
   - Selecteer **Maken**. Dit brengt u naar het implementatieoverzicht en geeft voortgangsberichten voor implementatie weer.

> [!NOTE]
> U een onbeperkt aantal abonnementen op standaardlagen maken in één of meerdere regio's. U echter slechts één gratis abonnement maken. Modelimplementaties op de gratis laag die 7 dagen ongebruikt blijven, worden automatisch buiten gebruik gesteld.

Het duurt even om uw nieuwe Spraakbron te implementeren. Zodra de implementatie is voltooid, selecteert u **Ga naar resource** en selecteert u in het linkernavigatiedeelvenster **Toetsen** om de abonnementssleutels van uw spraakservice weer te geven. Elk abonnement heeft twee sleutels; u beide sleutels in uw toepassing gebruiken. Als u snel een sleutel wilt kopiëren/plakken naar uw code-editor of andere locatie, selecteert u de kopieerknop naast elke toets en schakelt u van vensters om de inhoud van het klembord op de gewenste locatie te plakken.

> [!IMPORTANT]
> Deze abonnementssleutels worden gebruikt om toegang te krijgen tot uw Cognitive Service API. Deel je sleutels niet. Bewaar ze veilig, bijvoorbeeld met Azure Key Vault. We raden ook aan om deze sleutels regelmatig te regenereren. Er is slechts één sleutel nodig om een API-aanroep te maken. Bij het regenereren van de eerste sleutel u de tweede sleutel gebruiken voor verdere toegang tot de service.

## <a name="switch-to-a-new-subscription"></a>Overschakelen naar een nieuw abonnement

Als u wilt overschakelen van het ene abonnement naar het andere, bijvoorbeeld wanneer uw gratis proefperiode verloopt of wanneer u uw toepassing publiceert, vervangt u de regio- en abonnementssleutel in uw code door de regio en de abonnementssleutel van de nieuwe Azure-bron.

## <a name="about-regions"></a>Over regio's

- Als uw toepassing een [SpraakSDK](speech-sdk.md)gebruikt, geeft `westus`u de regiocode op, zoals bij het maken van een spraakconfiguratie.
- Als uw toepassing een van de [REST-API's](rest-apis.md)van de spraakservice gebruikt, maakt de regio deel uit van het eindpunt URI dat u gebruikt bij het indienen van aanvragen.
- Sleutels die voor een regio zijn gemaakt, zijn alleen geldig in die regio. Als u ze met andere regio's probeert te gebruiken, worden verificatiefouten gemaakt.

## <a name="next-steps"></a>Volgende stappen

Voltooi een van onze quickstarts van 10 minuten of bekijk onze SDK-samples:

> [!div class="nextstepaction"]
> [Snelstart: spraak herkennen in C#](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
> [Speech SDK-voorbeelden](speech-sdk.md#get-the-samples)
