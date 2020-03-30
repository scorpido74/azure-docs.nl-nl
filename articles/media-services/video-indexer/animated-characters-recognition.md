---
title: Detectie van geanimeerde tekens met video-indexer
titleSuffix: Azure Media Services
description: In dit onderwerp wordt uitgelegd hoe u geanimeerde tekendetectie gebruiken met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: af608dcfbb5d98cf3116de4e14dc12bf6facb97b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989906"
---
# <a name="animated-character-detection-preview"></a>Detectie van geanimeerde tekens (voorbeeld)

Azure Media Services Video Indexer ondersteunt detectie, groepering en herkenning van tekens in geanimeerde inhoud via integratie met [Cognitive Services custom vision.](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) Deze functionaliteit is beschikbaar zowel via de portal als via de API.

Na het uploaden van een geanimeerde video met een specifiek animatiemodel, haalt Video Indexer hoofdframes uit, detecteert geanimeerde tekens in deze frames, groepeert vergelijkbaar teken en kiest het beste voorbeeld. Vervolgens stuurt het de gegroepeerde tekens naar Custom Vision die tekens identificeert op basis van de modellen waarop het is getraind. 

Voordat u begint met het trainen van uw model, worden de tekens naamloos gedetecteerd. Als u namen toevoegt en het model traint, herkent de video-indexer de tekens en geeft deze een naam.

## <a name="flow-diagram"></a>Stroomdiagram

In het volgende diagram wordt de stroom van het geanimeerde tekendetectieproces getoond.

![Stroomdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Accounts

Afhankelijk van een type video-indexeraccount zijn er verschillende functiesets beschikbaar. Zie [Een Video Indexer-account maken dat is verbonden met Azure](connect-to-azure.md)voor informatie over het verbinden van uw account met Azure.

* Proefaccount: Video Indexer gebruikt een intern Custom Vision-account om een model te maken en deze te koppelen aan uw Video Indexer-account. 
* Betaald account: je koppelt je Custom Vision-account aan je Video Indexer-account (als je er nog geen hebt, moet je eerst een account aanmaken).

### <a name="trial-vs-paid"></a>Proef versus betaald

|Functionaliteit|Proefversie|Betaald|
|---|---|---|
|Aangepaste Vision-account|Beheerd achter de schermen door Video Indexer. |Uw Custom Vision-account is verbonden met Video Indexer.|
|Aantal animatiemodellen|Eén|Maximaal 100 modellen per account (Beperking van Aangepaste Visie).|
|Het model trainen|Video Indexer traint het model voor nieuwe tekens extra voorbeelden van bestaande tekens.|De eigenaar van het account traint het model wanneer hij klaar is om wijzigingen aan te brengen.|
|Geavanceerde opties in Aangepaste Visie|Geen toegang tot de Custom Vision portal.|U de modellen zelf aanpassen in de Custom Vision portal.|

## <a name="use-the-animated-character-detection-with-portal"></a>De detectie van geanimeerde tekens gebruiken met portal 

In deze sectie worden de stappen beschreven die u moet nemen om het animatiemodel voor tekendetectie te gebruiken. 

Aangezien in de proefaccounts de Custom Vision-integratie wordt beheerd door Video Indexer, u beginnen met het maken en gebruiken van het animatietekenmodel en de volgende sectie overslaan ('Uw Aangepaste Visie-account verbinden').

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Uw Custom Vision-account koppelen (alleen betaalde accounts)

Als u eigenaar bent van een betaalde video-indexaccount, moet u eerst een Aangepast Visie-account koppelen. Als je nog geen Custom Vision-account hebt, maak er dan een aan. Zie [Aangepaste visie](../../cognitive-services/custom-vision-service/home.md)voor meer informatie.

> [!NOTE]
> Beide accounts moeten zich in dezelfde regio bevinden. De Custom Vision integratie wordt momenteel niet ondersteund in de regio Japan.

#### <a name="connect-a-custom-vision-account-with-api"></a>Een Custom Vision-account koppelen met API 

Volg deze stappen om uw Aangepaste Visie-account te koppelen aan Video Indexer of om het Aangepaste Visie-account te wijzigen dat momenteel is verbonden met videoindexer:

1. Blader naar [www.customvision.ai](https://www.customvision.ai) en log in.
1. Kopieer de volgende toetsen: 

    * Trainingssleutel (voor de opleidingsbron)
    * Voorspellingssleutel (voor de voorspellingsbron)
    * Eindpunt 
    * Bron-ID voorspellen
    
    > [!NOTE]
    > Om alle sleutels te bieden moet je twee afzonderlijke resources in Custom Vision hebben, een voor training en een voor voorspelling.
1. Blader en meld u aan bij de [video-indexer](https://vi.microsoft.com/).
1. Klik op het vraagteken in de rechterbovenhoek van de pagina en kies **API-verwijzing**.
1. Zorg ervoor dat u bent geabonneerd op API-beheer door op het tabblad **Producten** te klikken. Als u een API hebt aangesloten, u verder gaan naar de volgende stap, anders u zich abonneren. 
1. Klik op de ontwikkelaarsportal op de **naslaggids voltooien** en blader naar **Bewerkingen**.  
1. Selecteer **Aangepaste visie-account (PREVIEW) verbinden** en klik op **Proberen**.
1. Vul de vereiste velden in, evenals het toegangstoken en klik op **Verzenden**. 

    Ga voor meer informatie over hoe u het toegangstoken voor video-indexer krijgen naar de [ontwikkelaarsportal](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Account-Access-Token?)en raadpleeg de [relevante documentatie](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Zodra het gesprek 200 OK-antwoord heeft beantwoord, is uw account verbonden.
1. Ga als volgt te werk om uw verbinding te verifiëren door naar de [portal Video-indexer](https://vi.microsoft.com/)te bladeren:
1. Klik rechtsboven op de **knop Aanpassing van het inhoudsmodel.**
1. Ga naar het tabblad **Geanimeerde tekens.**
1. Zodra u op Modellen beheren in Custom Vision klikt"**, wordt u overgezet naar het Custom Vision-account dat u zojuist hebt aangesloten.

> [!NOTE]
> Momenteel worden alleen modellen ondersteund die zijn gemaakt via Video Indexer. Modellen die zijn gemaakt via Custom Vision zijn niet beschikbaar. Bovendien is het beste om modellen te bewerken die alleen via Video Indexer zijn gemaakt via het Video Indexer-platform, omdat wijzigingen die via Custom Vision zijn aangebracht, onbedoelde resultaten kunnen veroorzaken.

### <a name="create-an-animated-characters-model"></a>Een model voor geanimeerde tekens maken

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Klik op de knop aanpassing van het inhoudsmodel in de rechterbovenhoek van de pagina.

    ![Aanpassing van inhoudsmodel](./media/animated-characters-recognition/content-model-customization.png)
1. Ga naar het tabblad **Geanimeerde tekens** in de sectie modelaanpassing.
1. Klik op **Model toevoegen**.
1. Geef een naam aan u en klik op Enter om de naam op te slaan.

> [!NOTE]
> De beste praktijk is om een aangepaste visie model voor elke animatieserie. 

### <a name="index-a-video-with-an-animated-model"></a>Een video indexeren met een animatiemodel

1. Klik op de knop **Uploaden** in het bovenste menu.
1. Kies een video die u wilt uploaden (uit een bestand of een URL).
1. Klik op **Geavanceerde opties**.
1. Kies **onder Personen / Geanimeerde tekens** **animatiemodellen**.
1. Als u één model hebt, wordt het automatisch gekozen en als u meerdere modellen hebt, u de relevante optie kiezen uit het vervolgkeuzemenu.
1. Klik op uploaden.
1. Zodra de video is geïndexeerd, ziet u de gedetecteerde tekens in de sectie **Geanimeerde tekens** in het deelvenster **Insights.**

> [!NOTE] 
> Voordat het model wordt getagd en getraind, krijgen alle geanimeerde personages de naam "Unknown #X". Nadat u het model hebt getraind, worden ze ook herkend.

### <a name="customize-the-animated-characters-models"></a>De modellen van de geanimeerde tekens aanpassen

1. Tag en train het model.

    1. Tag het gedetecteerde teken door de naam te bewerken. Zodra een teken is opgeleid in het model, zal het worden herkend de volgende video geïndexeerd met dat model. 
    1. Als u een geanimeerd teken in uw video wilt taggen, gaat u naar het tabblad **Inzichten** en klikt u op de knop **Bewerken** in de rechterbovenhoek van het venster.
    1. Klik in het deelvenster **Insights** op een van de gedetecteerde geanimeerde tekens en wijzig hun namen in 'Onbekende #X' (of de naam die eerder aan het teken is toegewezen).
    1. Nadat u de nieuwe naam hebt intypen, klikt u op het controlepictogram naast de nieuwe naam. Hiermee slaat u de nieuwe naam op in het model in Video Indexer.
    1. Nadat u klaar bent met het bewerken van alle gewenste namen, moet u het model trainen.

        Open de aanpassingspagina en klik op het tabblad **Geanimeerde tekens** en klik op de knop **Trein** om uw model te trainen.
         
        Als u een betaald account hebt, u klikken op de koppeling Modellen beheren in de koppeling **Klantvisie** (zoals hieronder wordt weergegeven). U wordt vervolgens doorgestuurd naar de pagina van het model in **Aangepaste visie.**
 
        ![Aanpassing van inhoudsmodel](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Eenmaal getraind, elke video die zal worden geïndexeerd of opnieuw geïndexeerd met dat model zal herkennen de getrainde tekens. 
    Betaalde accounts die toegang hebben tot hun Custom Vision-account kunnen de modellen en getagde afbeeldingen daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Een geanimeerd teken verwijderen.

    1. Als u een geanimeerd teken in uw video-inzichten wilt verwijderen, gaat u naar het tabblad **Inzichten** en klikt u op de knop **Bewerken** in de rechterbovenhoek van het venster.
    1. Kies het geanimeerde teken en klik op de knop **Verwijderen** onder hun naam.

    > [!NOTE]
    > Hierdoor wordt het inzicht uit deze video verwijderd, maar heeft dit geen invloed op het model.

1. Een model verwijderen.

    1. Klik op de **knop Inhoudsmodel in** het bovenste menu en ga naar het tabblad **Geanimeerde tekens.**
    1. Klik op het ellipspictogram rechts van het model dat u wilt verwijderen en vervolgens op de verwijderknop.
    
    * Betaald account: het model wordt losgekoppeld van Video Indexer en u het niet opnieuw verbinden.
    * Proefaccount: het model wordt ook uit de visie van de douane verwijderd. 
    
        > [!NOTE]
        > In een proefaccount hebt u slechts één model dat u gebruiken. Nadat u het hebt verwijderd, u andere modellen niet trainen.

## <a name="use-the-animated-character-detection-with-api"></a>De detectie van geanimeerde tekens gebruiken met API 

1. Een Aangepaste Visie-account verbinden.

    Als u eigenaar bent van een betaalde video-indexaccount, moet u eerst een Aangepast Visie-account koppelen. <br/>
    Als je nog geen Custom Vision-account hebt, maak er dan een aan. Zie [Aangepaste visie](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)voor meer informatie.

    [Verbind uw Custom Vision-account met API.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag)
1. Maak een model voor geanimeerde tekens.

    Gebruik de API [voor het maken van animatiemodellen.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag)
1. Indexeer of indexeer een video opnieuw.

    Gebruik de [API voor het opnieuw indexeren.](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) 
1. Pas de modellen van de geanimeerde tekens aan.

    Gebruik de [API voor treinanimatiemodellen.](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag)

### <a name="view-the-output"></a>De uitvoer weergeven

Bekijk de geanimeerde tekens in het gegenereerde JSON-bestand.

```json
"animatedCharacters": [
    {
    "videoId": "e867214582",
    "confidence": 0,
    "thumbnailId": "00000000-0000-0000-0000-000000000000",
    "seenDuration": 201.5,
    "seenDurationRatio": 0.3175,
    "isKnownCharacter": true,
    "id": 4,
    "name": "Bunny",
    "appearances": [
        {
            "startTime": "0:00:52.333",
            "endTime": "0:02:02.6",
            "startSeconds": 52.3,
            "endSeconds": 122.6
        },
        {
            "startTime": "0:02:40.633",
            "endTime": "0:03:16.6",
            "startSeconds": 160.6,
            "endSeconds": 196.6
        },
    ]
    },
]
```

## <a name="limitations"></a>Beperkingen

* Momenteel wordt de mogelijkheid voor "animatie-identificatie" niet ondersteund in de regio Oost-Azië.
* Tekens die klein of ver in de video lijken te zijn, worden mogelijk niet goed geïdentificeerd als de kwaliteit van de video slecht is.
* De aanbeveling is om een model per set geanimeerde tekens te gebruiken (bijvoorbeeld per animatieserie).

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
