---
title: Herkenning van animatie-tekens met Video Indexer
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u met behulp van animatie teken detectie kunt gebruiken met Video Indexer.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: 8cc097bc7083729a0e99c93376fe46b170760cf4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327486"
---
# <a name="animated-character-detection-preview"></a>Detectie van geanimeerde tekens (preview-versie)

Azure Media Services Video Indexer ondersteunt de detectie, groepering en herkenning van tekens in inhoud met animatie via integratie met [Cognitive Services aangepaste visie](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/). Deze functionaliteit is beschikbaar via de portal en via de API.

Nadat u een bewegende video met een specifiek animatie model hebt geüpload, worden in Video Indexer hoofd frames geëxtraheerd, worden animatie-tekens in deze frames gedetecteerd, worden vergelijk bare tekens gegroepeerd en wordt het beste voor beeld gekozen. Vervolgens worden de gegroepeerde tekens verzonden naar Custom Vision waarmee tekens worden geïdentificeerd op basis van de modellen waarop het is getraind. 

Voordat u begint met het trainen van uw model, worden de tekens gedetecteerd namelessly. Wanneer u namen toevoegt en het model traint, worden de tekens door de Video Indexer herkend en worden ze dienovereenkomstig een naam genoemd.

## <a name="flow-diagram"></a>Stroomdiagram

In het volgende diagram ziet u de stroom van het detectie proces voor de animatie.

![Stroomdiagram](./media/animated-characters-recognition/flow.png)

## <a name="accounts"></a>Accounts

Afhankelijk van een type Video Indexer-account, zijn er verschillende functie sets beschikbaar. Zie [een video indexer-account maken dat is verbonden met Azure](connect-to-azure.md)voor meer informatie over het verbinden van uw account met Azure.

* Proef account: Video Indexer gebruikt een intern Custom Vision-account om een model te maken en te verbinden met uw Video Indexer-account. 
* Betaald account: u kunt uw Custom Vision-account koppelen aan uw Video Indexer-account (als u er nog geen hebt, moet u eerst een account maken).

### <a name="trial-vs-paid"></a>Proef versie versus betaald

|Functionaliteit|Proefversie|Betaald|
|---|---|---|
|Custom Vision-account|Wordt beheerd achter de schermen door Video Indexer. |Uw Custom Vision-account is verbonden met Video Indexer.|
|Aantal animatie modellen|Een'|Maxi maal 100 modellen per account (Custom Vision beperking).|
|Het model trainen|Video Indexer treinen het model voor nieuwe tekens meer voor beelden van bestaande tekens.|De eigenaar van het account traint het model wanneer ze klaar zijn om wijzigingen aan te brengen.|
|Geavanceerde opties in Custom Vision|Geen toegang tot de Custom Vision Portal.|U kunt de modellen zelf aanpassen in de Custom Vision Portal.|

## <a name="use-the-animated-character-detection-with-portal"></a>De herkenning van de animatie gebruiken met portal 

In deze sectie worden de stappen beschreven die u moet uitvoeren om te beginnen met het detectie model voor animatie-tekens. 

Omdat de Custom Vision-integratie wordt beheerd door Video Indexer, kunt u beginnen met het maken en gebruiken van het model met animatie-tekens en de volgende sectie ("verbinding maken met uw Custom Vision-account") overs Laan.

### <a name="connect-your-custom-vision-account-paid-accounts-only"></a>Verbinding maken met uw Custom Vision-account (alleen betaalde accounts)

Als u een Video Indexer betaalde account hebt, moet u eerst verbinding maken met een Custom Vision-account. Als u nog geen Custom Vision-account hebt, moet u er een maken. Zie [Custom Vision](../../cognitive-services/custom-vision-service/home.md)voor meer informatie.

> [!NOTE]
> Beide accounts moeten zich in dezelfde regio bevinden. De integratie van Custom Vision wordt momenteel niet ondersteund in de Japan-regio.

#### <a name="connect-a-custom-vision-account-with-api"></a>Een Custom Vision-account verbinden met API 

Volg deze stappen om u te verbinden Custom Vision account te Video Indexer of het Custom Vision account te wijzigen dat momenteel is verbonden met Video Indexer:

1. Ga naar [www.customvision.ai](https://www.customvision.ai) en meld u aan.
1. Kopieer de volgende sleutels: 

    * Trainings sleutel (voor de trainings resource)
    * Voorspellings sleutel (voor de Voorspellings resource)
    * Eindpunt 
    * Resource-ID voor voor spelling
    
    > [!NOTE]
    > Om alle sleutels te bieden die u nodig hebt om twee afzonderlijke resources te hebben in Custom Vision, één voor de training en één voor voor spellingen.
1. Blader en meld u aan bij de [video indexer](https://vi.microsoft.com/).
1. Klik op het vraag teken in de rechter bovenhoek van de pagina en kies API- **verwijzing**.
1. Zorg ervoor dat u bent geabonneerd op API Management door te klikken op het tabblad **producten** . Als u een API hebt verbonden, kunt u door gaan met de volgende stap, anders abonneren. 
1. Op de ontwikkelaars Portal klikt u op de **volledige API-verwijzing** en bladert u naar **bewerkingen**.  
1. Selecteer **verbinding maken Custom Vision account (preview)** en klik op **proberen**.
1. Vul de vereiste velden en het toegangs token in en klik op **verzenden**. 

    Ga naar de [ontwikkelaars Portal](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)voor meer informatie over het verkrijgen van het video indexer toegangs token en Raadpleeg de [relevante documentatie](video-indexer-use-apis.md#obtain-access-token-using-the-authorization-api).  
1. Zodra de aanroep 200 OK reageert, is uw account verbonden.
1. Als u wilt controleren of uw verbinding is, gaat u naar de [video indexer](https://vi.microsoft.com/)-portal:
1. Klik op de knop **aanpassing van inhouds model** in de rechter bovenhoek.
1. Ga naar het tabblad **tekens met animatie** .
1. Zodra u op modellen beheren in Custom Vision ' * * ' klikt, wordt u overgezet naar het Custom Vision account dat u zojuist hebt verbonden.

> [!NOTE]
> Op dit moment worden alleen modellen ondersteund die zijn gemaakt via Video Indexer. Modellen die via Custom Vision zijn gemaakt, zijn niet beschikbaar. Daarnaast is het best practice om modellen te bewerken die via Video Indexer alleen via het Video Indexer platform zijn gemaakt, omdat wijzigingen die via Custom Vision zijn aangebracht, onbedoelde resultaten kunnen veroorzaken.

### <a name="create-an-animated-characters-model"></a>Een model met een animatie teken maken

1. Ga naar de [Video Indexer](https://vi.microsoft.com/)-website en meld u aan.
1. Klik op de knop aanpassing van inhouds model in de rechter bovenhoek van de pagina.

    ![Aanpassing van het inhouds model](./media/animated-characters-recognition/content-model-customization.png)
1. Ga naar het tabblad met **animatie tekens** in de sectie model aanpassing.
1. Klik op **model toevoegen**.
1. Geef een naam op voor het model en klik op ENTER om de naam op te slaan.

> [!NOTE]
> Het best practice heeft één aangepast Vision model voor elke animatie reeks. 

### <a name="index-a-video-with-an-animated-model"></a>Een video indexeren met een animatie model

1. Klik op de knop **uploaden** in het bovenste menu.
1. Kies een video die u wilt uploaden (vanuit een bestand of een URL).
1. Klik op **Geavanceerde opties**.
1. Onder **personen/animatie tekens** kiest u **animatie modellen**.
1. Als u één model hebt, wordt dit automatisch gekozen en als u meerdere modellen hebt, kunt u de gewenste optie kiezen in het vervolg keuzemenu.
1. Klik op uploaden.
1. Zodra de video is geïndexeerd, worden de gedetecteerde tekens in het gedeelte met **animatie tekens** in het deel venster **inzichten** weer geven.

> [!NOTE] 
> Voordat u het model kunt labelen en trainen, krijgen alle animatie-tekens de naam onbekend #X. Nadat u het model hebt getraind, worden ze ook herkend.

### <a name="customize-the-animated-characters-models"></a>De modellen met animatie tekens aanpassen

1. Het model labelen en trainen.

    1. Label het gedetecteerde teken door de naam te bewerken. Zodra een teken is getraind in het model, wordt dit herkend aan de volgende video die met het model is geïndexeerd. 
    1. Als u een animatie teken in uw video wilt labelen, gaat u naar het tabblad **inzichten** en klikt u op de knop **bewerken** in de rechter bovenhoek van het venster.
    1. Klik in het deel venster **inzichten** op een van de gedetecteerde tekens met animatie en wijzig de namen van ' onbekend #X ' (of de naam die eerder aan het teken is toegewezen).
    1. Nadat u de nieuwe naam hebt getypt, klikt u op het controle pictogram naast de nieuwe naam. Hiermee slaat u de nieuwe naam op in het model in Video Indexer.
    1. Wanneer u klaar bent met het bewerken van alle gewenste namen, moet u het model trainen.

        Open de pagina aanpassing en klik op het tabblad **tekst met animatie** en klik vervolgens op de knop **trainen** om uw model te trainen.
         
        Als u een betaald account hebt, kunt u klikken op de koppeling **modellen beheren in Customer Vision** (zoals hieronder weer gegeven). Vervolgens wordt u doorgestuurd naar de pagina van het model in **Custom Vision**.
 
        ![Aanpassing van het inhouds model](./media/animated-characters-recognition/content-model-customization-tab.png)

     1. Eenmaal getraind, worden de getrainde tekens herkend door alle Video's die worden geïndexeerd of herindexeerd met het model. 
    Betaalde accounts die toegang hebben tot hun Custom Vision-account kunnen de modellen en gelabelde installatie kopieën daar zien. Meer informatie over [het verbeteren van uw classificatie in Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-improving-your-classifier).

1. Een animatie teken verwijderen.

    1. Als u een animatie teken in uw video inzichten wilt verwijderen, gaat u naar het tabblad **inzichten** en klikt u op de knop **bewerken** in de rechter bovenhoek van het venster.
    1. Kies het animatie teken en klik vervolgens op de knop **verwijderen** onder hun naam.

    > [!NOTE]
    > Hiermee wordt het inzicht uit deze video verwijderd, maar dit is niet van invloed op het model.

1. Een model verwijderen.

    1. Klik op de knop **aanpassing van inhouds model** in het bovenste menu en ga naar het tabblad **tekens met animatie** .
    1. Klik op het pictogram met het weglatings teken rechts van het model dat u wilt verwijderen en klik vervolgens op de knop verwijderen.
    
    * Betaald account: het model wordt losgekoppeld van Video Indexer en u kunt dit niet opnieuw verbinden.
    * Proef account: het model wordt ook verwijderd uit het douane gezicht. 
    
        > [!NOTE]
        > In een proef account hebt u slechts één model dat u kunt gebruiken. Nadat u de app hebt verwijderd, kunt u geen andere modellen trainen.

## <a name="use-the-animated-character-detection-with-api"></a>De functie voor het detecteren van een animatie met API gebruiken 

1. Verbinding maken met een Custom Vision-account.

    Als u een Video Indexer betaalde account hebt, moet u eerst verbinding maken met een Custom Vision-account. <br/>
    Als u nog geen Custom Vision-account hebt, moet u er een maken. Zie [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/home)voor meer informatie.

    [Verbind uw Custom Vision-account met behulp van API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Connect-Custom-Vision-Account?tags=&pattern=&groupBy=tag).
1. Maak een model met animatie-tekens.

    Gebruik de API voor het maken van een [animatie model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Animation-Model?&groupBy=tag) .
1. Een video indexeren of opnieuw indexeren.

    Gebruik de API voor [opnieuw indexeren](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) . 
1. Pas de modellen met animatie-tekens aan.

    Gebruik de [Train animatie model](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Train-Animation-Model?&groupBy=tag) -API.

### <a name="view-the-output"></a>De uitvoer weer geven

Bekijk de animatie tekens in het gegenereerde JSON-bestand.

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

* Momenteel wordt de functie voor animatie-identificatie niet ondersteund in de regio Oost-Azië.
* Tekens die klein of ten opzichte van de video worden weer gegeven, worden mogelijk niet goed herkend als de kwaliteit van de video slecht is.
* De aanbeveling is een model te gebruiken per set met animatie tekens (bijvoorbeeld per reeks met animatie).

## <a name="next-steps"></a>Volgende stappen

[Overzicht van Video Indexer](video-indexer-overview.md)
