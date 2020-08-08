---
title: Een aanbieding bekijken en publiceren naar micro soft Commercial Marketplace
description: Gebruik het partner centrum om uw aanbieding te verzenden naar een preview-versie, een voor beeld van uw aanbieding te bekijken en deze vervolgens te publiceren naar micro soft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2020
ms.openlocfilehash: 9c05753b44b0c644b121eb3ccd3316633f9e5964
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88009525"
---
# <a name="review-and-publish-an-offer-to-the-commercial-marketplace"></a>Bekijk en publiceer een aanbieding naar de commerciële Marketplace

In dit artikel leest u hoe u partner Center kunt gebruiken om uw aanbieding te verzenden, een voor beeld van uw aanbieding te bekijken en deze vervolgens te publiceren naar micro soft Commercial Marketplace. We laten ook zien hoe u uw publicatie status kunt controleren zoals deze wordt door de publicatie stappen. U moet al een aanbieding hebben gemaakt die u wilt publiceren.

## <a name="go-to-your-offer-in-the-commercial-marketplace"></a>Ga naar uw aanbieding in de commerciële Marketplace

1. Meld u aan bij [Partner Center](https://partner.microsoft.com/dashboard/home).
1. Selecteer in het navigatie menu de optie **commerciële Marketplace**-  >  **overzicht**.
1. Op het tabblad **overzicht** , onder **aanbiedingen**, wordt een van de volgende status indicatoren weer gegeven in de kolom **status** voor elke aanbieding.
 
    | Status | Beschrijving |
    | ------------ | ------------- |
    | Concept | Het aanbod is gemaakt maar wordt niet gepubliceerd. |
    | Publicatie wordt uitgevoerd | De aanbieding werkt via het publicatie proces. |
    | Aandacht vereist | Er is een kritiek probleem gedetecteerd tijdens de certificering of tijdens een andere publicatie fase. |
    | Preview | We hebben de aanbieding gecertificeerd, die nu een definitieve verificatie van de uitgever afwacht. Selecteer **Live gaan** om de aanbieding Live te publiceren. |
    | Live | De aanbieding is live op Marketplace en kan worden gezien en verworven door klanten. |
    | Verkopen in behandeling voor stoppen | Publisher heeft ' stoppen met verkopen ' op een aanbieding of plan geselecteerd, maar de actie is nog niet voltooid. |
    | Niet beschikbaar in Marketplace | Een eerder gepubliceerde aanbieding in Marketplace is verwijderd. |
    |||

4. Selecteer in de kolom **aanbiedings alias** de aanbieding die u wilt bekijken en publiceren.

## <a name="submit-your-offer-to-preview"></a>Verzend uw aanbieding naar Preview

1. Als u uw aanbieding wilt verzenden naar Preview, selecteert u in de rechter bovenhoek van de Portal de optie **controleren en publiceren** . De pagina **controleren en publiceren** wordt weer gegeven.
1. Zorg ervoor dat de kolom **status** voor elke pagina **is voltooid**. De drie mogelijke statussen zijn als volgt:
   - **Niet gestart** : de pagina is niet gerakend en moet worden voltooid.
   - **Onvolledig** : in de pagina ontbreekt de vereiste informatie of fouten die moeten worden opgelost. U moet terug naar de pagina gaan en deze bijwerken.
   - **Voltooid** : de pagina is voltooid. Alle vereiste gegevens zijn opgegeven en er zijn geen fouten.
1. Als een van de pagina's een andere status dan **voltooid**heeft, selecteert u in de kolom **pagina** de pagina naam, corrigeert u het probleem, slaat u de pagina op en selecteert u vervolgens opnieuw **controleren en publiceren** om terug te gaan naar deze pagina.
1. Nadat alle pagina's zijn voltooid, geeft u in het vak **notities voor certificering** test instructies op voor het certificerings team om ervoor te zorgen dat uw app correct wordt getest. Geef aanvullende notities op die nuttig zijn voor het leren van uw app.
1. Selecteer **publiceren**om de aanbieding voor publicatie in te dienen. De pagina **overzicht van aanbieding** wordt weer gegeven en de publicatie status wordt weer gegeven.

## <a name="validation-and-publishing-steps"></a>Validatie-en publicatie stappen

Nadat u **publiceren**hebt geselecteerd, worden de validatie-en publicatie processen in volg orde voortgezet. In deze tabel ziet u het meest voorkomende publicatie proces:

| Fase | Wat gebeurt er? | 
| ------------ | ------------- | ------------- |
| Automatische validatie | We verwerken een reeks geautomatiseerde validaties. | 
| Certificering | We voeren hand matige validaties uit. | 
| Voor beeld maken | De aanbiedings pagina voor de preview-versie van uw aanbieding is beschikbaar voor iedereen met de preview-koppeling. Als uw aanbieding wordt verkocht via micro soft (transactable), kan alleen de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding kopen en toegang krijgen tot de aanbieding voor testen. | 
| Publisher-aftekening | We sturen u een e-mail met een aanvraag voor u om uw aanbieding te bekijken en goed te keuren. | 
| Publiceren | We voeren een reeks stappen uit om te controleren of de preview-aanbieding Live is gepubliceerd naar de commerciële Marketplace. | 
|||

## <a name="automated-validation-phase"></a>Automatische validatie fase

De eerste stap in het publicatie proces is een set automatische validaties. Elke validatie stap komt overeen met een functie die u hebt gekozen tijdens het maken van de aanbieding. Elke validatie controle moet worden uitgevoerd voordat de aanbieding naar de volgende stap in het publicatie proces kan gaan.

- **Setup van inkoop stroom van aanbieding (<10 min.)**

   In deze stap zorgt u ervoor dat aan uw aanbieding kan worden voldaan wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.
- **Gegevens validatie test station (~ 5 min)**

   In deze stap valideren we de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. De functionaliteit van het test station wordt getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

-   **Het inrichten van het station testen (~ 30 minuten)**

    In deze stap moeten we na het valideren van de gegevens en functionaliteit van uw test drive in de vorige stap exemplaren van uw test drive implementeren en repliceren, zodat deze gereed zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

-   **Validatie en registratie van lead beheer (<15 min)**

    In deze stap bevestigen we dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details die u hebt ingevoerd op de pagina **aanbieding instellen** . Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

## <a name="certification-phase"></a>Certificerings fase

Voor de publicatie moeten de aanbiedingen die worden verzonden naar de commerciële Marketplace, worden gecertificeerd. Verzonden aanbiedingen worden uitvoerig getest, sommige geautomatiseerd en anderen hand matig. Bekijk het [certificaat beleid voor commerciële Marketplace](https://aka.ms/commercial-marketplace-certification-policies) voor meer informatie.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen validatie die worden uitgevoerd tijdens het certificerings proces
Er zijn drie validatie niveaus opgenomen in het certificerings proces voor elk ingediende aanbod.
-   Zakelijke geschiktheid voor Publisher
-   Validatie van inhoud
-   Technische validatie

#### <a name="publisher-business-eligibility"></a>Zakelijke geschiktheid voor Publisher
Elk aanbiedings type controleert een reeks basis geschiktheids criteria waaraan de uitgever moet voldoen. Geschiktheids criteria kunnen bestaan uit de MPN-status van de uitgever, de beschik baarheid en competentie niveaus, enzovoort.

#### <a name="content-validation"></a>Validatie van inhoud

De gegevens die u hebt ingevoerd tijdens het maken van uw aanbieding, worden gecontroleerd op kwaliteit en relevantie. Met deze controles worden uw vermeldingen voor de details van Marketplace-vermelding, prijzen, Beschik baarheid, bijbehorende plannen, enzovoort gecontroleerd. Om te voldoen aan de vermeldings criteria van Azure Marketplace en Microsoft AppSource, zullen we valideren dat uw aanbieding de volgende bevat:
-   Een titel die de aanbieding nauw keurig beschrijft
-   Goed geschreven beschrijvingen die een uitgebreid overzicht en toegevoegde waarde bieden
-   Scherm afbeeldingen en Video's van kwaliteit
-   Een uitleg van de manier waarop de aanbieding gebruikmaakt van micro soft-platforms en-hulpprogram ma's.

Meer informatie over de criteria voor de validatie van de inhoud vindt u in de [algemene aanbiedings beleidsregels](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technische validatie
Tijdens de technische validatie onderneemt de aanbieding (pakket of binair) de volgende controles.
-   Gescand op malware
-   Bewaakte netwerk aanroepen
-   Pakket geanalyseerd
-   Grondige scan van de functionaliteit van het aanbod

De aanbieding wordt getest op verschillende platforms en versies om ervoor te zorgen dat deze krachtig is.

### <a name="certification-failure-report"></a>Rapport van certificerings fout

Als uw aanbieding een van de aanbiedingen, technische of beleids controles mislukt of als u niet in aanmerking komt voor een aanbieding van dit type, sturen we een certificaat voor een certificerings fout naar u.

Dit rapport bevat beschrijvingen van alle beleids regels die zijn mislukt, samen met controle notities. Bekijk dit e-mail rapport, Los eventuele problemen op, breng updates voor uw aanbieding waar nodig aan en verzend de aanbieding opnieuw via de [Portal voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het partner centrum. U kunt de aanbieding zo vaak als nodig opnieuw verzenden totdat de certificering is door gegeven.

## <a name="preview-creation-phase"></a>Fase voor het maken van een preview

Tijdens het maken van de preview-fase maken we een versie van uw aanbieding die alleen toegankelijk is voor de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding, indien van toepassing. De preview-versie van uw aanbieding is niet beschikbaar voor iedereen buiten het publiek van de preview-periode tot u de Live-aanbieding publiceert.

> [!NOTE]
> Gebruik de preview-doel groep niet om personen buiten uw organisatie inzicht te geven in een aanbieding. Gebruik in plaats daarvan de optie voor de privé-aanbieding. Uw aanbieding is op dit moment niet volledig getest en gevalideerd en is niet gereed voor externe distributie. 

## <a name="publisher-signoff-phase"></a>Publisher aftekening-fase

Wanneer de aanbieding klaar is om te worden gecontroleerd en aftekening, sturen we u een e-mail om u te vragen uw aanbiedings voorbeeld te controleren en goed te keuren. U kunt ook de pagina **overzicht van aanbieding** in uw browser vernieuwen om te zien of uw aanbieding de Publisher aftekening-fase heeft bereikt. Als dat het geval is, is de knop **Live** en voor beeld-koppelingen beschikbaar.

De volgende scherm afbeelding toont de pagina **overzicht van aanbieding** voor een SaaS-aanbieding. De validatie stappen die u op deze pagina ziet, variëren afhankelijk van het type aanbieding en de selecties die u hebt gemaakt tijdens het maken van de aanbieding.

![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven.](./partner-center-portal/media/publish-status-publisher-signoff.png)

**Voor beeld van uw aanbieding en aftekening**
1. Op de **overzichts** pagina van de aanbieding kunt u een voor beeld van uw aanbieding weer geven door de koppeling te selecteren onder de knop **Go Live** .
   > [!NOTE]
   > Er is een koppeling naar de preview-versie van AppSource, de preview van Azure Marketplace, of beide, afhankelijk van de opties die u hebt gekozen bij het maken van uw aanbieding. Als u ervoor kiest om uw aanbieding via micro soft te verkopen, kan iedereen die is toegevoegd aan de preview-doel groep de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze fase.

1. Als u wijzigingen wilt aanbrengen nadat u een voor beeld van de aanbieding hebt bekeken, kunt u deze bewerken en opnieuw verzenden om een nieuwe preview te publiceren. Zie [een bestaande aanbieding in de commerciële Marketplace bijwerken](./partner-center-portal/update-existing-offer.md)voor meer informatie.

1. Nadat u uw Preview hebt goedgekeurd, kunt u uw aanbieding Live publiceren naar de commerciële Marketplace door **Go Live**te selecteren.
   > [!TIP]
   > Als uw aanbieding al Live is en beschikbaar is voor het publiek in de Marketplace, worden de updates die u aanbrengt pas live als u **Live go**selecteert.

## <a name="publish-phase"></a>Publicatie fase

Nu u ervoor hebt gekozen om live te gaan met uw aanbieding, zodat deze beschikbaar is in de commerciële Marketplace, is er een reeks definitieve validatie controles die ervoor zorgen dat de Live-aanbieding zo is geconfigureerd, net als bij de preview-versie van de aanbieding.

-   **Setup van inkoop stroom van aanbieding (>10 min.)**

    In deze stap zorgt u ervoor dat aan uw aanbieding kan worden voldaan wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.
-   **Gegevens validatie test station (~ 5 min)**

    In deze stap valideren we de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. De functionaliteit van het test station wordt getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

-   **Het inrichten van het station testen (~ 30 minuten)**

      In deze stap implementeren en repliceren we exemplaren van uw test drive zodat ze gereed zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.
-   **Validatie en registratie van lead beheer (>15 min)**

    In deze stap bevestigen we dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details op de pagina **aanbieding instellen** van de aanbieding. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

-   **Definitieve publicatie (>30 minuten)**

    In deze stap zorgt u ervoor dat uw aanbieding openbaar beschikbaar wordt op de Marketplace.

Nadat deze validatie controles zijn voltooid, is uw aanbieding Live in de Marketplace.

## <a name="next-step"></a>Volgende stap

[Toegang krijgen tot analytische rapporten voor de commerciële Marketplace in het partner centrum](./partner-center-portal/analytics.md)
