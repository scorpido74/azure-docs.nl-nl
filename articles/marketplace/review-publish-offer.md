---
title: Een aanbieding bekijken en publiceren naar micro soft Commercial Marketplace
description: Gebruik het partner centrum om uw aanbieding te verzenden naar een preview-versie, een voor beeld van uw aanbieding te bekijken en deze vervolgens te publiceren naar micro soft Commercial Marketplace.
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: mingshen-ms
ms.author: mingshen
ms.date: 08/12/2020
ms.openlocfilehash: eff1db3aca2cab77d18698634b84e8e1b1c99f7e
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89375874"
---
# <a name="how-to-review-and-publish-an-offer-to-the-commercial-marketplace"></a>Een aanbieding bekijken en publiceren naar de commerciële Marketplace

Dit artikel laat u zien hoe u met partner Center uw aanbieding voor publicatie kunt verzenden, een voor beeld van uw aanbieding kunt bekijken en deze vervolgens kunt publiceren naar de commerciële Marketplace. We laten ook zien hoe u uw publicatie status kunt controleren zoals deze wordt door de publicatie stappen. U moet al een aanbieding hebben gemaakt die u wilt publiceren.

## <a name="offer-status"></a>Status van aanbieding

U kunt de status van uw aanbieding bekijken op het tabblad **overzicht** van het dash board commerciële Marketplace in [partner centrum](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Een van de volgende status indicatoren wordt weer gegeven in de kolom **status** voor elke aanbieding.

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

## <a name="validation-and-publishing-steps"></a>Validatie-en publicatie stappen

Wanneer u klaar bent om een aanbieding voor publicatie in te dienen, selecteert u in de rechter bovenhoek van de Portal de optie **controleren en publiceren** . Op de pagina **controleren en publiceren** ziet u de status van elke pagina voor uw aanbieding. Dit kan een van de volgende zijn:

   - **Niet gestart** : de pagina is niet volledig.
   - **Onvolledig** : in de pagina ontbreekt de vereiste informatie of fouten die moeten worden opgelost. U moet terug naar de pagina gaan en deze bijwerken.
   - **Voltooid** : de pagina is voltooid. Alle vereiste gegevens zijn opgegeven en er zijn geen fouten.

Als een van de pagina's een andere status heeft dan **voltooid**, moet u het probleem op die pagina corrigeren en vervolgens terugkeren naar de pagina **controleren en publiceren** om te bevestigen dat de status nu als **voltooid**wordt weer gegeven. Voor sommige aanbiedings typen moet worden getest. Als dit het geval is, ziet u een **Opmerking voor het certificerings** veld waarin u test instructies moet geven aan het certificerings team en eventuele aanvullende notities die nuttig zijn voor de uitleg van uw app.

Nadat alle pagina's zijn voltooid en u toepasselijke test notities hebt ingevoerd, selecteert u **publiceren** om de validatie-en publicatie processen te starten. De fasen en de algehele volg orde kunnen variëren, afhankelijk van het type aanbieding dat u publiceert. De volgende tabel bevat een mogelijke publicatie stroom. Elke fase wordt gedetailleerd beschreven in de volgende secties.

| Fase | Wat gebeurt er? |
| ------------ | ------------- | ------------- |
| [Automatische validatie](#automated-validation-phase) | We verwerken een reeks geautomatiseerde validaties. |
| [Certificering](#certification-phase) | We voeren hand matige validaties uit. |
| [Voor beeld maken](#preview-creation-phase) | De aanbiedings pagina voor de preview-versie van uw aanbieding is beschikbaar voor iedereen met de preview-koppeling. Als uw aanbieding wordt verkocht via micro soft (transactable), kan alleen de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding kopen en toegang krijgen tot de aanbieding voor testen. |
| [Uitgever afmelden](#publisher-sign-off-phase) | We sturen u een e-mail met een aanvraag voor u om uw aanbieding te bekijken en goed te keuren. |
| [Publiceren](#publish-phase) | We voeren een reeks stappen uit om te controleren of de preview-aanbieding Live is gepubliceerd naar de commerciële Marketplace. |
|||

## <a name="automated-validation-phase"></a>Automatische validatie fase

De eerste stap in het publicatie proces is een set automatische validaties. Elke validatie stap komt overeen met een functie die u hebt gekozen tijdens het maken van de aanbieding. Elke validatie controle moet zijn voltooid voordat de aanbieding kan door gaan naar de volgende stap in het publicatie proces.

- **Setup van inkoop stroom van aanbieding** (<10 min.)

   Wij zorgen ervoor dat uw aanbieding kan worden afgehandeld wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station** (~ 5 min)

   We valideren de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. U kunt test drive functionaliteit testen en goed keuren. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- Het inrichten van het **station testen** (~ 30 minuten)

    Nadat de gegevens en functionaliteit van uw test drive in de vorige stap zijn gevalideerd, implementeren en repliceren we exemplaren van uw test drive zodat ze klaar zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer** (<15 min)

    We bevestigen dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details die u hebt ingevoerd op de pagina **aanbieding instellen** . Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

## <a name="certification-phase"></a>Certificerings fase

Aanbiedingen die worden verzonden naar de commerciële Marketplace moeten gecertificeerd zijn voordat ze kunnen worden gepubliceerd. Aanbiedingen worden uitvoerig getest, sommige geautomatiseerd en anderen hand matig. Zie voor meer informatie het [beleid voor commerciële Marketplace-certificering](https://aka.ms/commercial-marketplace-certification-policies).

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen validatie die worden uitgevoerd tijdens het certificerings proces

Er zijn drie validatie niveaus opgenomen in het certificerings proces voor elk ingediende aanbod.

- Zakelijke geschiktheid voor Publisher
- Validatie van inhoud
- Technische validatie

#### <a name="publisher-business-eligibility"></a>Zakelijke geschiktheid voor Publisher

Elk aanbiedings type controleert een reeks vereiste criteria voor basis geschiktheid. Dit criterium kan de status van de uitgever, de bewaarde vaardig heden, het competentie niveau, enzovoort bevatten.

#### <a name="content-validation"></a>Validatie van inhoud

De gegevens die u hebt ingevoerd tijdens het maken van uw aanbieding, worden gecontroleerd op kwaliteit en relevantie. Met deze controles worden uw vermeldingen voor de details van Marketplace-vermelding, prijzen, Beschik baarheid, bijbehorende plannen, enzovoort gecontroleerd. Om te voldoen aan de vermeldings criteria van Microsoft AppSource en Azure Marketplace, zullen we valideren dat uw aanbieding de volgende bevat:

- Een titel die de aanbieding nauw keurig beschrijft
- Goed geschreven beschrijvingen die een uitgebreid overzicht en toegevoegde waarde bieden
- Scherm afbeeldingen en Video's van kwaliteit
- Een uitleg van de manier waarop de aanbieding gebruikmaakt van micro soft-platforms en-hulpprogram ma's.

Meer informatie over de criteria voor de validatie van de inhoud vindt u in de [algemene aanbiedings beleidsregels](https://aka.ms/commercial-marketplace-certification-policies#100-general).

#### <a name="technical-validation"></a>Technische validatie

Tijdens de technische validatie onderneemt de aanbieding (pakket of binair) de volgende controles.

- Gescand op malware
- Bewaakte netwerk aanroepen
- Pakket geanalyseerd
- Grondige scan van de functionaliteit van het aanbod

De aanbieding wordt getest op verschillende platforms en versies om ervoor te zorgen dat deze krachtig is.

### <a name="certification-failure-report"></a>Rapport van certificerings fout

Als uw aanbieding een van de aanbiedingen, technische of beleids controles mislukt of als u niet in aanmerking komt voor een aanbieding van dit type, sturen we een certificaat voor een certificerings fout naar u.

Dit rapport bevat beschrijvingen van alle beleids regels die zijn mislukt, samen met controle notities. Bekijk dit e-mail rapport, Los eventuele problemen op, breng updates voor uw aanbieding waar nodig aan en verzend de aanbieding opnieuw via de [Portal voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het partner centrum. U kunt de aanbieding zo vaak als nodig opnieuw verzenden totdat de certificering is door gegeven.

## <a name="preview-creation-phase"></a>Fase voor preview-versie maken

Tijdens het maken van de preview-fase maken we een versie van uw aanbieding die alleen toegankelijk is voor de doel groep die u hebt opgegeven op de pagina **voor beeld van de doel groep** van uw aanbieding, indien van toepassing. De preview-versie van uw aanbieding is niet beschikbaar voor iedereen buiten het publiek van de preview-periode tot u de Live-aanbieding publiceert.

> [!NOTE]
> Gebruik de preview-doel groep niet om personen buiten uw organisatie inzicht te geven in een aanbieding. Gebruik in plaats daarvan de optie voor de privé-aanbieding. Uw aanbieding is op dit moment niet volledig getest en gevalideerd en is niet gereed voor externe distributie.

## <a name="publisher-sign-off-phase"></a>Aanmeldings fase van de uitgever

Wanneer de aanbieding klaar is om te worden gecontroleerd en u bent afgemeld, sturen we u een e-mail om u te vragen uw aanbiedings voorbeeld te controleren en goed te keuren. U kunt ook de pagina **overzicht van aanbieding** in uw browser vernieuwen om te zien of uw aanbieding de afmeldings fase van de uitgever heeft bereikt. Als dat het geval is, is de knop **Live** en voor beeld-koppelingen beschikbaar.

De volgende scherm afbeelding toont de pagina **overzicht van aanbieding** voor een SaaS-aanbieding. De validatie stappen die u op deze pagina ziet, variëren afhankelijk van het type aanbieding en de selecties die u hebt gemaakt tijdens het maken van de aanbieding.

![Illustreert de pagina overzicht van aanbieding voor een aanbieding in partner centrum. De knop Go Live en de preview-koppelingen worden weer gegeven.](./media/publish-status-publisher-signoff.png)

### <a name="previewing-and-approving-your-offer"></a>Uw aanbieding bekijken en goed keuren

> [!IMPORTANT]
> Als u de end-to-end aankoop-en configuratie stroom wilt valideren, koopt u uw aanbieding terwijl deze in preview is. Informeer micro soft eerst met een [ondersteunings ticket](https://aka.ms/marketplacesupport) om ervoor te zorgen dat er geen kosten worden verwerkt.

Op de **overzichts** pagina van de aanbieding ziet u een voor beeld van koppelingen onder de knop **Go Live** . Er is een koppeling naar de preview-versie van AppSource, de preview van Azure Marketplace, of beide, afhankelijk van de opties die u hebt gekozen bij het maken van uw aanbieding. Als u ervoor kiest om uw aanbieding via micro soft te verkopen, kan iedereen die is toegevoegd aan de preview-doel groep de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze fase.

Nadat u uw Preview hebt goedgekeurd, selecteert u **Live** om uw aanbieding Live naar de commerciële Marketplace te publiceren. 

Als u wijzigingen wilt aanbrengen nadat u een voor beeld van de aanbieding hebt bekeken, kunt u uw publicatie aanvraag bewerken en opnieuw verzenden. Als uw aanbieding al Live is en beschikbaar is voor het publiek in de Marketplace, worden de updates die u aanbrengt pas Live wanneer u **Go*selecteert. Zie [een bestaande aanbieding bijwerken in de commerciële Marketplace](./partner-center-portal/update-existing-offer.md) voor meer informatie.

## <a name="publish-phase"></a>Publicatie fase

Nu u ervoor hebt gekozen om live te gaan met uw aanbieding, waardoor deze beschikbaar is in de commerciële Marketplace, voeren we een reeks definitieve validatie controles uit om ervoor te zorgen dat de Live-aanbieding is geconfigureerd, net als de preview-versie van de aanbieding.

- **Setup van inkoop stroom van aanbieding** (>10 min.)

    Wij zorgen ervoor dat uw aanbieding kan worden afgehandeld wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station** (~ 5 min)

    We valideren de gegevens die u hebt ingevoerd op de pagina technische configuratie van de aanbieding. U kunt test drive functionaliteit testen en goed keuren. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- Het inrichten van het **station testen** (~ 30 minuten)

    We implementeren en repliceren exemplaren van uw test drive zodat ze klaar zijn voor gebruik door de klant. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer** (>15 min)

    We bevestigen dat uw lead beheersysteem leads van klanten kan ontvangen op basis van de gegevens die op de pagina met de installatie van uw **aanbieding** staan. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

- **Definitieve publicatie (>30 minuten)**

    We zorgen ervoor dat uw aanbieding openbaar beschikbaar wordt op Marketplace.

Nadat deze validatie controles zijn voltooid, is uw aanbieding Live in de Marketplace.

## <a name="next-steps"></a>Volgende stappen

[Toegang krijgen tot analytische rapporten voor de commerciële Marketplace in het partner centrum](partner-center-portal/analytics.md)
