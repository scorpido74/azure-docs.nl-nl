---
title: Controleer de publicatiestatus van uw commerciële marktplaatsaanbieding
description: Controleer de status van de validatie-, certificerings- en voorbeeldstappen die nodig zijn om een aanbieding te publiceren via de commerciële marktplaats in het Microsoft PartnerCenter.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 012a574887d9980e0c71c3af84ff70ca8d31312c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80275676"
---
# <a name="check-the-publishing-status-of-your-commercial-marketplace-offer"></a>Controleer de publicatiestatus van uw commerciële marktplaatsaanbieding

U uw huidige **publicatiestatus** bekijken op het tabblad **Aanbiedingsoverzicht** van de [Portal Commerciële Marktplaats](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in partnercentrum.

Voor elke aanbieding moet een van de volgende statusindicatoren worden weergegeven.

| **Status**    | **Beschrijving**  |
| :---------- | :-------------------|
| **Concept** | Aanbieding is gemaakt, maar wordt niet gepubliceerd. |
| **Publiceren in uitvoering** | Aanbieding/Plan werkt zich een weg door de stappen van het publicatieproces. |
| **Aandacht nodig** | Er is een kritiek probleem ontdekt tijdens de certificering door Microsoft of een van de publicatiestappen. |
| **Voorbeeld** | Aanbieding is gecertificeerd door Microsoft en wacht nu op een definitieve verificatie door de uitgever. Selecteer live gaan om het aanbod live te doen. |
| **Live** | Aanbod is live in de markt en kan worden gezien en verworven door klanten. |
| **In afwachting van stop verkopen** | Uitgever geselecteerd "stop verkopen" aangeboden of plan, maar de actie is nog niet voltooid. |
| **Niet beschikbaar op de markt** | Een eerder gepubliceerde aanbieding/abonnement in de marktplaats is verwijderd. |

## <a name="automated-validation"></a>Geautomatiseerde validatie

De eerste stap in het publicatieproces is een reeks geautomatiseerde validaties. Elke validatiestap komt overeen met een functie die u hebt gekozen bij het maken van uw aanbieding. Als die functie niet is ingeschakeld, gaat de validatie vooruit naar de volgende publicatiestap. Elke validatiecontrole moet worden voltooid voordat de publicatiestatus is goedgekeurd.

- **Instelling aankoopstroom aanbieden (<10 min)**

In deze stap zorgen we ervoor dat uw aanbod kan worden uitgevoerd wanneer deze door klanten via de Azure-portal wordt gekocht. Deze stap is alleen van toepassing op aanbiedingen die via Microsoft worden verkocht.

- **Teststation gegevensvalidatie (~5 min)**

In deze stap valideren we de gegevens die u hebt opgegeven in de sectie Technische configuratie van de aanbieding. De functionaliteit van de testrit is getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een testrit is ingeschakeld.

- **Proefrit provisioning (~ 30 min)**

In deze stap implementeren en repliceren we exemplaren van uw testrit nadat we de gegevens en functionaliteit van uw testrit in de vorige stap hebben gevaliderd, zodat ze klaar zijn voor gebruik door de klant.  Deze stap is alleen van toepassing op aanbiedingen waarvoor een testrit is ingeschakeld.

- **Validatie en registratie van leadmanagement (<15 min)**

In deze stap bevestigen we dat uw leadmanagementsysteem klantleads kan ontvangen op basis van de details die zijn opgegeven in de installatie van aanbieding. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead management is ingeschakeld.

## <a name="certification"></a>Certificering

Voordat ze worden gepubliceerd, moeten aanbiedingen die worden ingediend bij de commerciële marktplaats in partnercentrum, worden gecertificeerd. Ingediende aanbiedingen worden grondig getest, sommige geautomatiseerde en andere handmatige, met inbegrip van een controle tegen de [Azure Marketplace-certificering beleid](https://docs.microsoft.com/legal/marketplace/general-policies). Aanbiedingsinzendingen moeten worden gemarkeerd als in aanmerking komen voor certificering voordat ze doorgaan naar de volgende stap in de publicatiestroom.

### <a name="types-of-validation-that-take-place-during-certification"></a>Typen validatie die plaatsvinden tijdens de certificering

Er zijn drie validatieniveaus opgenomen in het certificeringsproces voor elke ingediende aanbieding.

- Geschiktheid voor uitgeversbedrijven
- Inhoudsvalidatie
- Technische validatie

#### <a name="publisher-business-eligibility"></a>Geschiktheid voor uitgeversbedrijven

Elk aanbiedingstype controleert een reeks basisgeschiktheidscriteria waaraan de uitgever moet voldoen. Criteria voor geschiktheid kunnen de MPN-status van de uitgever, de competenties, de competentieniveaus, enz.

#### <a name="content-validation"></a>Inhoudsvalidatie

Tijdens de validatie van de inhoud worden de gegevens die zijn ingevoerd bij het maken van uw aanbieding gecontroleerd op kwaliteit en relevantie. Deze controles zullen uw inzendingen bekijken voor de details van de marktplaatsvermelding, prijzen, beschikbaarheid, bijbehorende abonnementen, enz. Om te voldoen aan de azure Marketplace- en/of AppSource-vermeldingscriteria, valideren we dat uw aanbieding het volledige zeggen over:

- een titel die het aanbod nauwkeurig beschrijft;
- goed geschreven beschrijvingen die een grondig overzicht en waardepropositie bieden;
- kwaliteitsscreenshots en bijbehorende video's; En
- een uitleg over hoe het aanbod gebruik maakt van Microsoft-platforms en -tools.

Meer informatie over de criteria voor contentvalidatie door het [algemene aanbiedingsbeleid te](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general)lezen .

#### <a name="technical-validation"></a>Technische validatie

Tijdens de technische validatie ondergaat de aanbieding (pakket of binair) de volgende controles.
- Gescand op malware
- Netwerkoproepen gecontroleerd
- Pakket geanalyseerd
- Grondige scanning van de werkelijke functionaliteit van het aanbod

Het aanbod wordt getest op verschillende platformen en versies om ervoor te zorgen dat het robuust is.

Bekijk de specifieke configuratiegegevens die nodig zijn voor uw aanbieding in het gedeelte Technische configuratie van dit document.

### <a name="certification-failure-report"></a>Rapport voor certificeringsfouten

Na voltooiing van de beoordeling, als uw aanbod is geslaagd certificering dan gaat het verder naar de volgende stap in het publicatieproces. Als uw aanbieding geen van de aanbiedings-, technische of beleidscontroles heeft uitgevoerd of als u niet in aanmerking komt voor een dergelijk voorstel, wordt een rapport over certificeringsfouten gegenereerd en naar u gemaild.

Dit rapport bevat beschrijvingen van beleid dat is mislukt, samen met beoordelingsnotities. Bekijk dit e-mailrapport, verwerk eventuele problemen, maak waar nodig updates van uw aanbieding en dien de aanbieding opnieuw in met de [Commerciële Marketplace-portal](https://partner.microsoft.com/dashboard/commercial-marketplace/offers) in het Partnercentrum. (U de aanbieding zo vaak als nodig opnieuw indienen totdat u de certificering hebt gehaald).

## <a name="preview-creation"></a>Voorbeeld van maken bekijken

Tijdens de stap **Voor het maken** van voorbeelden maken we een versie van je aanbieding die alleen toegankelijk is voor de doelgroep die je hebt opgegeven in het gedeelte Voorbeeld van je aanbieding.

>[!Note]
> Gebruik deze stap niet om mensen buiten uw organisatie inzicht te geven in een aanbieding. Gebruik in plaats daarvan de optie **Privé-aanbieding.** Op dit moment is uw aanbod nog niet volledig getest en gevalideerd en is het niet klaar voor externe distributie.

## <a name="publisher-approval"></a>Goedkeuring van uitgevers

In deze stap wordt u per e-mail ontvangen met een verzoek om uw aanbiedingsvoorbeeld te bekijken en goed te keuren voordat u de laatste publicatiestap moet goedkeuren.

Als u ervoor hebt gekozen om uw aanbieding via Microsoft te verkopen, u de acquisitie en implementatie van uw aanbieding testen om ervoor te zorgen dat deze aan uw vereisten voldoet tijdens deze goedkeuringsfase voor het bekijken. Uw aanbieding is nog niet beschikbaar op de openbare marktplaats. Zodra u deze preview test en goedkeurt, moet u **Go-Live** selecteren op het dashboard [**met aanbiedingsoverzicht.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

Als u tijdens deze voorbeeldfase wijzigingen in de aanbieding wilt aanbrengen, u deze bewerken en opnieuw indienen om een nieuw voorbeeld te publiceren. Zie het artikel [Bestaande marketplace-aanbiedingen](#update-existing-marketplace-offers) bijwerken voor meer wijzigingen.

Als uw aanbieding al live is en beschikbaar is voor het publiek op de markt, worden updates die u maakt pas live als u **Go-live** selecteert op het dashboard [**met aanbiedingsoverzicht.**](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

### <a name="publish-offer-to-the-public"></a>Aanbieding publiceren aan het publiek

Meld u aan bij Partner Center en krijg toegang tot de aanbieding. U wordt doorgestuurd naar de **overzichtspagina van de aanbieding.** Bovenaan deze pagina ziet u een optie voor **Live gaan.** Selecteer **Live gaan** en na bevestiging wordt de aanbieding gepubliceerd voor het publiek. U ontvangt een e-mailmelding wanneer de aanbieding live is.

## <a name="publish"></a>Publiceren

Nu u hebt geselecteerd om live te **gaan** met uw aanbieding, waardoor het beschikbaar is in de markt, zijn er een reeks van definitieve validatie controles die zullen worden opgevoerd door om ervoor te zorgen dat de live-aanbieding is geconfigureerd, net als de preview-versie van de aanbieding.

- **Instelling aankoopstroom aanbieden (>10 min)**

In deze stap zorgen we ervoor dat uw aanbod kan worden uitgevoerd wanneer deze door klanten via de Azure-portal wordt gekocht. Deze stap is alleen van toepassing op aanbiedingen die via Microsoft worden verkocht.

- **Teststation gegevensvalidatie (~5 min)**

In deze stap valideren we de gegevens die u hebt opgegeven in de sectie Technische configuratie van de aanbieding. De functionaliteit van de testrit is getest en goedgekeurd. Deze stap is alleen van toepassing op aanbiedingen waarvoor een testrit is ingeschakeld.

- **Proefrit provisioning (~ 30 min)**

In deze stap implementeren en repliceren we exemplaren van uw testrit, zodat ze klaar zijn voor gebruik door de klant.  Deze stap is alleen van toepassing op aanbiedingen waarvoor een testrit is ingeschakeld.

- **Validatie en registratie van leadmanagement (>15 min)**

In deze stap bevestigen we dat uw leadmanagementsysteem klantleads kan ontvangen op basis van de details die zijn opgegeven in de installatie van aanbieding. Deze stap is alleen van toepassing op aanbiedingen waarvoor Lead management is ingeschakeld.

- **Eindpublicatie (>30 minuten)**

In deze stap zorgen we ervoor dat uw aanbod openbaar beschikbaar wordt op de markt.

## <a name="update-existing-marketplace-offers"></a>Bestaande marktplaatsaanbiedingen bijwerken

Als u wijzigingen wilt aanbrengen in een aanbieding die u al hebt gepubliceerd, moet u eerst de bestaande aanbieding bijwerken en vervolgens opnieuw publiceren.

## <a name="next-steps"></a>Volgende stappen

- [Een bestaande aanbieding bijwerken in Commerciële Marketplace](./update-existing-offer.md)
