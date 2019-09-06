---
title: Zelfstudie voor het terugsturen van Azure Data Box Disk | Microsoft Docs
description: In deze zelfstudie leest u hoe u uw Azure Data Box Disk terugstuurt naar Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: alkohli
ms.localizationpriority: high
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: b982e0bcd87a60e29b11c7123fbe5d675ab017b3
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390411"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Azure Data Box Disk retourneren 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Zelfstudie: Azure Data Box Disk retourneren 

In deze zelfstudie wordt beschreven hoe u een planning kunt maken om uw Azure Data Box Disk te retourneren. De instructies voor het retourneren zijn afhankelijk van waarvandaan u het apparaat retourneert. 

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een Data Box Disk naar Microsoft verzenden
> * Data Box Disk vanuit verschillende regio's retourneren

## <a name="prerequisites"></a>Vereisten

Voltooi voordat u begint de [Zelfstudie: Gegevens kopiëren naar de Azure Data Box-schijf en deze gegevens controleren](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Een Data Box Disk terugsturen

::: zone-end

1. Koppel de schijven los, nadat de gegevensvalidatie is voltooid. Verwijder de verbindingskabels.
2. Verpak alle schijven en de verbindingskabels in bubbelplastic en plaats deze in de verzenddoos. Er kunnen kosten in rekening worden gebracht als er accessoires ontbreken.
    - Gebruik de verpakking waarin u het pakket hebt gekregen.  
    - U wordt aangeraden schijven te verpakken in stevig bubbelplastic.
    - Zorg ervoor dat alles goed past zodat het product niet in de doos kan gaan schuiven.

De volgende stappen zijn afhankelijk van de locatie waarvandaan u het apparaat retourneert. De instructies zijn verschillend voor de VS/Canada, de Europese Unie (EU), Australië of Aziatische landen.

### <a name="pick-up-in-us-canada"></a>Retourneren vanuit de VS of Canada

Voer de volgende stappen uit als u het apparaat vanuit de VS of Canada retourneert.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Maak een afspraak met UPS om het pakket op te laten halen. Ophalen plannen:

    - Bel met UPS in de buurt (gratis land-/regiospecifiek nummer).
    - Vermeld in uw gesprek het trackingnummer voor de retournering, zoals weergegeven op het afgedrukte label.
    - Als het trackingnummer niet wordt vermeld, moet u bij het afhalen door UPS extra kosten betalen.
    - In plaats van het ophalen te plannen, kunt u de Data Box Disk ook zelf naar de dichtstbijzijnde inleverlocatie brengen.

### <a name="pick-up-in-europe"></a>Retourneren vanuit Europa

Voer de volgende stappen uit als u het apparaat vanuit Europa retourneert.

1. Gebruik het retourlabel in de doorzichtige plastic hoes op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat.

2. Verzegel de verpakking en zorg ervoor dat het retourlabel zichtbaar is.
3. Als u het apparaat in Europa met DHL retourneert, dient u een ophaalverzoek in door op de website van DHL het verzendnummer op te geven.
4. Ga naar de DHL Express-website voor uw land/regio en kies **Boek een koerier pick-up > Online verzenden**.    
3. Geef het nummer van de luchtvrachtbrief op en klik op **Boek een koerier** om een ophaalmoment te plannen.

### <a name="pick-up-in-australia"></a>Retourneren vanuit Australië

Azure-datacenters in Australië hebben een extra beveiligingsmelding. Alle inkomende verzendingen moeten een melding vooraf hebben. Voer de volgende stappen uit voor retourneren vanuit Australië.

1. Gebruik het meegeleverde retourlabel en controleer of de TAU-code (het referentienummer) erop staat. Als het retourlabel ontbreekt of als u andere problemen hebt, kunt u een e-mail sturen naar [Data Box Asia Operations](mailto:adbo@microsoft.com). Geef de ordernaam en details over het probleem op in de onderwerpregel.
3. Bevestig het label op de doos. 
4. Boek via de koppeling https://mydhl.express.dhl/au/en/schedule-pickup.html#/schedule-pickup#label-reference online een afspraak voor de retourzending. 

### <a name="pick-up-in-asia"></a>Retourneren vanuit Azië

De instructies voor het retourneren verschillen voor Japan, Korea en Singapore.

#### <a name="pick-up-in-japan"></a>Retourneren vanuit Japan

1. Schrijf als gegevens van de afzender uw bedrijfsnaam en adresgegevens op de vrachtbrief.
2. Stuur een e-mail naar Quantium Solutions met behulp van de volgende e-mailsjabloon.

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
    - **Als u retourneert vanuit Osaka**, wijzigt u het onderwerp in de e-mailsjabloon in: `Pickup request for Microsoft Azure OSA`.
    - Als er geen vrachtbrief van Japan Post Chakubarai is meegeleverd of ontbreekt, vermeldt u dit in dit e-mail bericht. Quantium Solutions Japan verzoekt Japan Post om de vrachtbrief mee te brengen als het pakket wordt afgehaald.
    - Als u meerdere orders hebt, moet u een e-mail verzenden om afzonderlijke pakketten op te laten halen.

3. U ontvangt een bevestiging per e-mail van Quantium Solutions nadat u een retourzending hebt geboekt. De e-mailbevestiging bevat ook informatie over de Chakubarai-vrachtbrief.

Zo nodig kunt u contact opnemen met de ondersteuning van Quantium Solutions (in het Japans) via: 

- E-mail：Customerservice.JP@quantiumsolutions.com 
- Telefoon: 03-5755-0150 

#### <a name="pick-up-in-korea"></a>Retourneren vanuit Korea

1. Zorg ervoor dat u de vrachtbrief voor de retourzending bijlevert.
2. Ophalen pakket aanvragen indien vrachtbrief aanwezig is:
    1. Bel de hotline van *Quantium Solutions International* op 070-8231-1418 (tijdens kantooruren, 10 tot 17 uur, maandag t/m vrijdag). U noemt *Microsoft Azure pickup* en het serviceaanvraagnummer om een ophaalafspraak te maken.  
    2. Als de hotline bezet is, stuurt u een e-mail aan `microsoft@rocketparcel.com`, met als onderwerp *Microsoft Azure Pickup* en het serviceaanvraagnummer als referentie.
    3. Als de koerier niet komt om het pakket op te halen, belt u de hotline van *Quantium Solutions International* voor een alternatieve oplossing. 
    4. U ontvangt een e-mail ter bevestiging van de ophaalafspraak.
3. Voer deze stap alleen uit als de vrachtbrief niet aanwezig is. Ophalen aanvragen:
    1. Bel de hotline van *Quantium Solutions International* op 070-8231-1418 (tijdens kantooruren, 10 tot 17 uur, maandag t/m vrijdag). U noemt *Microsoft Azure pickup* en het serviceaanvraagnummer om een ophaalafspraak te maken. Geef op dat u een nieuwe vrachtbrief wilt hebben om een ophaalafspraak te maken. Vermeld de gegevens van de afzender (klant), de ontvanger (Azure-datacenter) en het referentienummer (serviceaanvraagnummer). 
    2. Als de hotline bezet is, stuurt u een e-mail aan `microsoft@rocketparcel.com`, met als onderwerp *Microsoft Azure Pickup* en het serviceaanvraagnummer als referentie.
    3. Als de koerier niet komt om het pakket op te halen, belt u de hotline van *Quantium Solutions International* voor een alternatieve oplossing. 
    4. U krijgt een mondelinge bevestiging als de aanvraag via de telefoon wordt gedaan.

#### <a name="pick-up-in-singapore"></a>Retourneren vanuit Singapore

1. Druk het verzendlabel af en bevestig het op de doos. Als het label is beschadigd of verloren is gegaan:
    - Ga naar **Overzicht > Verzendlabel downloaden** en download een retourlabel.
    - Bevestig het label op het apparaat. Zorg ervoor dat het label zichtbaar is.

2. Ophalen aanvragen:
    - Bel de hotline van **SingPost** op **68456485** (tijdens kantooruren, 9 tot 17 uur, maandag t/m vrijdag).  
    - Vermeld *Microsoft Azure pickup* en het serviceaanvraagnummer (trackingnummer op het retourlabel) om een ophaalafspraak te maken. 
    - U krijgt een mondelinge bevestiging over de ophaalafspraak. 
    - Als de koerier niet komt om het pakket op te halen, belt u **SingPost** op **6845 6485** voor een alternatieve oplossing. 
3. Overhandig het pakket aan de koerier. 


::: zone target="docs"

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie zijn verschillende onderwerpen besproken over de Azure Data Box-schijf, zoals:

> [!div class="checklist"]
> * Een Data Box Disk naar Microsoft verzenden
> * Data Box Disk vanuit verschillende regio's retourneren

Ga naar de volgende procedure om te leren hoe u het uploaden van gegevens vanuit Data Box Disk naar het Azure Storage-account kunt controleren.

> [!div class="nextstepaction"]
> [Gegevens uploaden vanuit Azure Data Box Disk controleren](./data-box-disk-deploy-picked-up.md)

::: zone-end




