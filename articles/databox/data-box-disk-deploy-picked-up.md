---
title: Zelf studie voor het verzenden van Azure Data Box Disk | Microsoft Docs
description: In deze zelfstudie leest u hoe u uw Azure Data Box Disk terugstuurt naar Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 1c102d05d4c570f9415fad18c004847de11d50cc
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035707"
---
::: zone target="chromeless"

# <a name="return-azure-data-box-disk"></a>Azure Data Box Disk retour neren 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Zelfstudie: Azure Data Box Disk retour neren 

In deze zelf studie wordt beschreven hoe u een verzameling kunt plannen om uw Azure Data Box Disk te retour neren. De stappen voor het ophalen zijn afhankelijk van waar u het apparaat retourneert. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Data Box Disk naar Microsoft verzenden
> * Data Box Disk in verschillende regio's ophalen

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u begint de [Zelfstudie: Gegevens kopiëren naar de Azure Data Box-schijf en deze gegevens controleren](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Een Data Box Disk terugsturen

::: zone-end

1. Koppel de schijven los, nadat de gegevensvalidatie is voltooid. Verwijder de verbindingskabels.
2. Verpak alle schijven en de verbindingskabels in bubbelplastic en plaats deze in de verzenddoos. Er kunnen kosten in rekening worden gebracht als de accessoires ontbreken.
    - De verpakking van de eerste verzen ding opnieuw gebruiken.  
    - U wordt aangeraden schijven te verpakken met een goed beveiligde ballon.
    - Zorg ervoor dat het passend is Snug om de bewegingen in het vak te verminderen.

De volgende stappen worden bepaald door de locatie waar u het apparaat retourneert.



- [Plan een ophaling met ups bij het retour neren van het apparaat in VS en Canada](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
- [Plan een ophaling met DHL voor Europa](data-box-disk-deploy-picked-up.md#pick-up-in-europe) door de website ervan te bezoeken en het Airway-factuur nummer op te geven.
- [Een ophaling plannen in Australië](data-box-disk-deploy-picked-up.md#pick-up-in-australia).
- [Een ophaling voor landen in Azië](data-box-disk-deploy-picked-up.md#pick-up-in-asia) zoals Japan, Korea en Singapore plannen.

::: zone target="chromeless"

Nadat de schijven zijn opgehaald door uw provider, wordt de status van de bestelling in de portal-updates en een tracerings-ID weer gegeven.

::: zone-end

## <a name="pick-up-in-us-canada"></a>Orderverzamelen in Nederland, Canada

Voer de volgende stappen uit als u het apparaat in VS of Canada retourneert.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **overzicht > verzend label te downloaden** en down load een retour verzendings label.
    - Breng het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Een ophaling plannen met UPS. Een ophaling plannen:

    - Roep de lokale nood voeding (land/regio-specifiek gratis nummer) aan.
    - Neem in uw gesprek het tracerings nummer van omgekeerde verzen ding op zoals weer gegeven in het afgedrukte label.
    - Als het tracking nummer niet wordt vermeld, moet u tijdens het ophalen een extra kosten betalen.
    - In plaats van het ophalen te plannen, kunt u ook de Data Box Disk op de dichtstbijzijnde uitval locatie afhalen.

## <a name="pick-up-in-europe"></a>Ophalen in Europa

Voer de volgende stappen uit als u het apparaat in Europa wilt retour neren.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **overzicht > verzend label te downloaden** en down load een retour verzendings label.
    - Breng het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Als u het apparaat in Europa met DHL retourneert, dient u een ophaalverzoek in door op de website van DHL het verzendnummer op te geven.
4. Ga naar de pagina land/regio DHL Express en kies **een courier-verzameling boeken > eReturn-verzen ding**.    
3. Geef het nummer van de luchtvrachtbrief op en klik op **Boek een koerier** om een ophaalmoment te plannen.

## <a name="pick-up-in-australia"></a>Orderverzamelen in Australië

Azure-data centers in Australië hebben een extra beveiligings melding. Alle inkomende verzen dingen moeten een geavanceerde melding hebben. Voer de volgende stappen uit voor het ophalen in Australië.

1. E-mail `adbops@microsoft.com` om het verzend label met een unieke inkomende id of de letterlijke code te vragen. Plaats de aanvraag ten minste drie dagen vóór de geplande verzend datum om het label in de tijd op te halen.
2. Het onderwerp van de e-mail moet zijn: *aanvraag voor reverse-verzend label met de letter tau-code*. Zorg ervoor dat u de volgende gegevens opneemt in het e-mail bericht: 

    - Ordernaam
    - Adres
    - Naam van contactpersoon

## <a name="pick-up-in-asia"></a>Picken in Azië

De instructies voor het afhalen verschillen per Japan, Korea en Singapore.

### <a name="pick-up-in-japan"></a>Ophalen in Japan

1. Schrijf uw bedrijfs naam en adres gegevens in de begeleide notitie als gegevens van de afzender.
2. Quantium-oplossing voor e-mail met de volgende e-mail sjabloon.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Als u ophaalt in Osaka**, wijzigt u het onderwerp in het e-mail sjabloon naar `Pickup request for Microsoft Azure OSA`:.
    - Als er geen Chakubarai-zending van Japan post is opgenomen of ontbreekt, noteert u deze in dit e-mail bericht. Quantium Solutions Japan vraagt een Japanse post aan om de zending op te halen wanneer deze wordt opgehaald.
    - Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke op te halen.

3. Ontvang een e-mail bevestiging van Quantium-oplossingen nadat u een ophaling hebt vastgelegd. De e-mail bevestiging bevat ook informatie over het Chakubarai-begeleidings document.

Als dat nodig is, kunt u contact opnemen met de ondersteuning van de Quantium-oplossing (Japanse taal) met de volgende informatie: 

- E-mail:Customerservice.JP@quantiumsolutions.com 
- Telefoon: 03-5755-0150 

### <a name="pick-up-in-korea"></a>Ophalen in Korea

1. Zorg ervoor dat u de retour consigning-Opmerking opneemt.
2. Ophalen aanvragen wanneer een begeleidende partij aanwezig is:
    1. Roep *Quantium Solutions International* hotline op 070-8231-1418 tijdens kantoor uren (10 tot 5 uur, maandag t/m vrijdag). Offerte *Microsoft Azure ophalen* en het aanvraag nummer van de service om een verzameling te rangschikken.  
    2. Als de Hotline bezet is, e-mail `microsoft@rocketparcel.com`, met het onderwerp van de e-mail *Microsoft Azure ophalen* en het nummer van de service aanvraag als referentie.
    3. Roep *Quantium Solutions International* Hotline aan voor alternatieve regelingen als de courier niet arriveert voor verzameling. 
    4. U ontvangt een e-mail bevestiging voor het ophaal schema.
3. Voer deze stap alleen uit als de begeleidende notitie niet aanwezig is. Ophalen aanvragen:
    1. Roep *Quantium Solutions International* hotline op 070-8231-1418 tijdens kantoor uren (10 tot 5 uur, maandag t/m vrijdag). Offerte *Microsoft Azure ophalen* en het aanvraag nummer van de service om een verzameling te rangschikken. Geef op dat u een nieuw begeleidings bericht nodig hebt om een verzameling te rangschikken. Geef Sender (klant), receiver Information (Azure Data Center) en referentie nummer (service aanvraag nummer) op. 
    2. Als de Hotline bezet is, e-mail `microsoft@rocketparcel.com`, met het onderwerp van de e-mail *Microsoft Azure ophalen* en het nummer van de service aanvraag als referentie.
    3. Roep *Quantium Solutions International* Hotline aan voor alternatieve regelingen als de courier niet arriveert voor verzameling. 
    4. U ontvangt een mondelinge bevestiging als de aanvraag via de telefoon wordt gedaan.

### <a name="pick-up-in-singapore"></a>Ophalen in Singapore

1. Druk het verzend label af en voeg het toe aan het vak. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **overzicht > verzend label te downloaden** en ontvang een retour verzendings label.

        ![Verzendlabel downloaden](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - Breng het label op het apparaat. Zorg ervoor dat het label zichtbaar is.

2. Ophalen aanvragen:
    - Roep **SingPost** hotline op **6845 6485** tijdens kantoor uren (9:00 tot tot 17:00 uur, maandag t/m vrijdag).  
    - Prijs opgave *Microsoft Azure ophalen* en het nummer van de service aanvraag (tracking nummer op het retour verzendings label) om een verzameling te rangschikken. 
    - U krijgt een mondelinge bevestiging voor het ophaal schema. 
    - Als de courier niet arriveert voor verzameling, roept u **SingPost** op **6845 6485** aan voor alternatieve regelingen. 
3. Hand aan de Courier. 


::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
> * Een Data Box Disk naar Microsoft verzenden
> * Data Box Disk in verschillende regio's ophalen

Ga naar de volgende procedure om te leren hoe u het uploaden van gegevens van Data Box Disk naar het Azure Storage-account kunt controleren.

> [!div class="nextstepaction"]
> [Gegevens uploaden van Azure Data Box Disk controleren](./data-box-disk-deploy-picked-up.md)

::: zone-end




