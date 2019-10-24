---
title: Controleer de publicatie status van uw aanbieding voor commerciële Marketplace
description: Controleer de status van de vereiste validatie, certificering en preview-stappen voor het publiceren van een aanbieding via de commerciële Marketplace in het micro soft Partner Center.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 84e8f1d7b723bb86b31abb1ff60bce5351d7d996
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72750159"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Controleer de publicatie status van uw aanbieding voor commerciële Marketplace

U kunt de huidige **publicatie status** weer geven op het tabblad Overzicht van het **aanbod** van de [Portal commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het partner centrum.

Voor elke aanbieding moet een van de volgende status indicatoren worden weer gegeven.

| **Status**    | **Beschrijving**  |
| :---------- | :-------------------|
| **Runbookconcept** | Het aanbod is gemaakt maar wordt niet gepubliceerd. |
| **Publicatie wordt uitgevoerd** | De aanbieding/het plan werkt in de stappen van het publicatie proces. |
| **Aandacht vereist** | Er is een kritiek probleem ontdekt tijdens de certificering door micro soft of een van de publicatie stappen. |
| **Preview** | De aanbieding is gecertificeerd door micro soft en wacht nu op een definitieve verificatie door de uitgever. Selecteer Live gaan om de aanbieding Live te maken. |
| **Galerie** | De aanbieding is live op Marketplace en kan worden gezien en verworven door klanten. |
| **Verkopen in behandeling voor stoppen** | De uitgever heeft ' stoppen met verkopen ' in de aanbieding of het abonnement geselecteerd, maar de actie is nog niet voltooid. |
| **Niet beschikbaar in Marketplace** | Een eerder gepubliceerde aanbieding/plan in Marketplace is verwijderd. |

## <a name="automated-validation"></a>Automatische validatie

De eerste stap in het publicatie proces is een set automatische validaties. Elke validatie stap komt overeen met een functie die u hebt gekozen om in te scha kelen voor het maken van uw aanbieding. Als deze functie niet is ingeschakeld, wordt de validatie overs laan voor de volgende stap van de publicatie. Elke validatie controle moet worden voltooid voordat de status van de publicatie is goedgekeurd.

- **Setup van inkoop stroom van aanbieding (< 10 min.)**

In deze stap zorgt u ervoor dat aan uw aanbieding kan worden voldaan wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station (~ 5 min)**

In deze stap valideren we de gegevens die u hebt ingevoerd in het gedeelte test drive Technical Configuration van de aanbieding. De functionaliteit van het test station wordt getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Het inrichten van het station testen (~ 30 minuten)**

In deze stap moeten we na het valideren van de gegevens en functionaliteit van uw test drive in de vorige stap exemplaren van uw test drive implementeren en repliceren, zodat deze gereed zijn voor gebruik door de klant.  Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer (< 15 min)**

In deze stap bevestigen we dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details van de aanbiedings instelling. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

## <a name="certification"></a>Certificering

Voor het publiceren van aanbiedingen die worden verzonden naar de commerciële Marketplace in Partner Center moet worden gecertificeerd. Verzonden aanbiedingen worden uitvoerig getest, sommige geautomatiseerd en anderen hand matig, inclusief een controle op het [Azure Marketplace-certificerings beleid](https://docs.microsoft.com/legal/marketplace/general-policies). Inzendingen voor aanbiedingen moeten worden gemarkeerd als in aanmerking komend voor certificering voordat ze naar de volgende stap in de publicatie stroom kunnen gaan.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen validatie die worden uitgevoerd tijdens het certificerings proces

Er zijn drie validatie niveaus opgenomen in het certificerings proces voor elke verzonden aanbieding.

- Zakelijke geschiktheid voor Publisher
- Validatie van inhoud
- Technische validatie

#### <a name="publisher-business-eligibility"></a>Zakelijke geschiktheid voor Publisher

Elk aanbiedings type controleert een reeks basis geschiktheids criteria waaraan de uitgever moet voldoen. Geschiktheids criteria kunnen bestaan uit de MPN-status van de uitgever, de beschik baarheid en competentie niveaus, enzovoort.

#### <a name="content-validation"></a>Validatie van inhoud

Tijdens de validatie van de inhoud wordt de informatie die u hebt ingevoerd bij het maken van uw aanbieding gecontroleerd op kwaliteit en relevantie. Met deze controles worden uw vermeldingen voor de details van Marketplace-vermelding, prijzen, Beschik baarheid, bijbehorende abonnementen, enzovoort gecontroleerd. Om te voldoen aan de criteria voor de Azure Marketplace-en/of AppSource-aanbieding, zullen we controleren of uw aanbieding het volgende bevat:

- een titel die de aanbieding nauw keurig beschrijft;
- goed geschreven beschrijvingen die een grondig overzicht en toegevoegde waarde bieden;
- scherm afbeeldingen van kwaliteit en begeleidende Video's; maar
- een uitleg van de manier waarop de aanbieding gebruikmaakt van micro soft-platforms en-hulpprogram ma's.

Meer informatie over de criteria voor de validatie van de inhoud vindt u in de [algemene aanbiedings beleidsregels](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general).

#### <a name="technical-validation"></a>Technische validatie

Tijdens de technische validatie onderneemt de aanbieding (pakket of binair) de volgende controles.
- Gescand op malware
- Bewaakte netwerk aanroepen
- Pakket geanalyseerd
- Grondige scan van de daad werkelijke functionaliteit van de aanbieding

De aanbieding wordt getest op verschillende platforms en versies om ervoor te zorgen dat deze krachtig is.

Bekijk de specifieke configuratie details die zijn vereist voor uw aanbieding in het gedeelte technische configuratie van dit document.

### <a name="certification-failure-report"></a>Rapport van certificerings fout

Als de beoordeling is voltooid, wordt de volgende stap in het publicatie proces uitgevoerd als uw aanbieding is door gegeven aan de certificering. Als uw aanbieding een van de aanbiedingen, technische of beleids controles heeft mislukt of als u niet in aanmerking komt voor een aanbieding van dat type, wordt een rapport met een certificerings fout gegenereerd en naar u verzonden.

Dit rapport bevat beschrijvingen van alle beleids regels die zijn mislukt, samen met controle notities. Bekijk dit e-mail rapport, Los eventuele problemen op, breng zo nodig updates voor uw aanbieding aan en verzend de aanbieding opnieuw via de [Portal voor commerciële Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het partner centrum. (U kunt de aanbieding zo vaak als nodig opnieuw verzenden totdat de certificering wordt door gegeven).

## <a name="preview-creation"></a>Voor beeld maken

Tijdens de stap **voor het maken** van de preview-fase maken we een versie van uw aanbieding die alleen toegankelijk is voor de doel groep die u hebt opgegeven in het gedeelte preview van uw aanbieding.

>[!Note]
> Gebruik deze stap niet om personen buiten uw organisatie inzicht te geven in een aanbieding. Gebruik in plaats daarvan de optie voor de **privé-aanbieding** . Uw aanbieding is op dit moment niet volledig getest en gevalideerd en is niet gereed voor externe distributie.

## <a name="publisher-approval"></a>Goed keuring Uitgever

In deze stap ontvangt u een e-mail met een aanvraag voor u om uw Preview-voor beeld te controleren en goed te keuren voordat u de laatste stap publiceert.

Als u hebt geselecteerd om uw aanbieding via micro soft te verkopen, kunt u de aanschaf en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens de goedkeurings fase van dit voor beeld. Uw aanbieding is nog niet beschikbaar in de open bare Marketplace. Zodra u deze preview hebt getest en goedgekeurd, moet u **Go-Live** selecteren in het dash board overzicht van de [**aanbieding**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

Als u tijdens deze preview-fase wijzigingen in de aanbieding wilt aanbrengen, kunt u een nieuwe preview-versie bewerken en opnieuw indienen. Zie het artikel [bestaande Marketplace-aanbiedingen bijwerken](#update-existing-marketplace-offers) voor meer informatie over meer wijzigingen.

Als uw aanbieding al Live is en beschikbaar is voor het publiek in de Marketplace, worden de updates die u aanbrengt pas live als u **Go-Live** selecteert in het dash board overzicht van de [**aanbieding**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) .

### <a name="publish-offer-to-the-public"></a>Aanbieding publiceren naar het publiek

Meld u aan bij Partner Center en open de aanbieding. U wordt omgeleid naar de **overzichts** pagina van de aanbieding. Boven aan deze pagina ziet u een optie voor **Live go**. Selecteer **Live** en nadat u hebt bevestigd, wordt de aanbieding naar het openbaar gepubliceerd. U ontvangt een e-mail melding wanneer de aanbieding Live is.

## <a name="publish"></a>Publiceren

Nu u ervoor hebt gekozen om **Live te gaan** met uw aanbieding, zodat deze beschikbaar is op Marketplace, is er een reeks definitieve validatie controles die worden door gegeven, zodat u zeker weet dat de Live-aanbieding is geconfigureerd net als de preview-versie van de aanbieding.

- **Setup van inkoop stroom van aanbieding (> 10 min.)**

In deze stap zorgt u ervoor dat aan uw aanbieding kan worden voldaan wanneer klanten via de Azure Portal worden gekocht. Deze stap is alleen van toepassing op aanbiedingen die via micro soft worden verkocht.

- **Gegevens validatie test station (~ 5 min)**

In deze stap valideren we de gegevens die u hebt ingevoerd in het gedeelte test drive Technical Configuration van de aanbieding. De functionaliteit van het test station wordt getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Het inrichten van het station testen (~ 30 minuten)**

In deze stap implementeren en repliceren we exemplaren van uw test drive zodat ze gereed zijn voor gebruik door de klant.  Deze stap is alleen van toepassing op aanbiedingen waarvoor een test drive is ingeschakeld.

- **Validatie en registratie van lead beheer (> 15 min)**

In deze stap bevestigen we dat uw systeem voor het beheer van potentiële klanten leads kan ontvangen op basis van de details van de aanbiedings instelling. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead beheer is ingeschakeld.

- **Definitieve publicatie (> 30 minuten)**

In deze stap zorgt u ervoor dat uw aanbieding openbaar beschikbaar wordt op de Marketplace.

## <a name="update-existing-marketplace-offers"></a>Bestaande Marketplace-aanbiedingen bijwerken

Als u wijzigingen wilt aanbrengen in een aanbieding die u al hebt gepubliceerd, moet u eerst de bestaande aanbieding bijwerken en deze vervolgens opnieuw publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in de commerciële Marketplace](./update-existing-offer.md)
