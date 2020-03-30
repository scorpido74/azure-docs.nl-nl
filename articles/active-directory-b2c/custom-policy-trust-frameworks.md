---
title: Referentie - vertrouwensframeworks in Azure Active Directory B2C | Microsoft Documenten
description: Een onderwerp over het aangepaste Azure Directory B2C-beleid en het Identity Experience Framework.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188882"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Vertrouwenskaders definiëren met Azure AD B2C-identiteitservaringskader

Het aangepaste beleid van Azure Active Directory B2C (Azure AD B2C) dat gebruikmaakt van het Identity Experience Framework, biedt uw organisatie een gecentraliseerde service. Deze dienst vermindert de complexiteit van de identiteitsfederatie in een grote gemeenschap van belang. De complexiteit wordt teruggebracht tot één vertrouwensrelatie en één metagegevensuitwisseling.

Het aangepaste Azure AD B2C-beleid gebruikt het Identity Experience Framework om u in staat te stellen de volgende vragen te beantwoorden:

- Wat zijn de wettelijke, beveiligings-, privacy- en gegevensbeschermingsbeleid waaraan moet worden voldaan?
- Wie zijn de contacten en wat zijn de processen om een geaccrediteerde deelnemer te worden?
- Wie zijn de geaccrediteerde aanbieders van identiteitsgegevens (ook wel "claimproviders" genoemd) en wat bieden zij?
- Wie zijn de geaccrediteerde relying partijen (en optioneel, wat hebben ze nodig)?
- Wat zijn de technische "on the wire" interoperabiliteitsvereisten voor deelnemers?
- Wat zijn de operationele "runtime"-regels die moeten worden gehandhaafd voor het uitwisselen van digitale identiteitsgegevens?

Om al deze vragen te beantwoorden, gebruikt azure AD B2C aangepaste beleidsregels die gebruikmaken van het Identity Experience Framework de construct van het Trust Framework (TF). Laten we eens kijken naar deze constructie en wat het biedt.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Begrijp de stichting Vertrouwenskader en federatiebeheer

Het Trust Framework is een schriftelijke specificatie van het beleid inzake identiteit, beveiliging, privacy en gegevensbescherming waaraan deelnemers in een gemeenschap van belang moeten voldoen.

Federatieve identiteit biedt een basis voor het bereiken van identiteitszekerheid voor eindgebruikers op internetschaal. Door identiteitsbeheer te delegeren aan derden, kan één digitale identiteit voor een eindgebruiker worden hergebruikt met meerdere relying partijen.

Identiteitsbeveiliging vereist dat identiteitsproviders (IdP's) en attribuutproviders (AtP's) zich houden aan specifieke beveiligings-, privacy- en operationele beleidsregels en -praktijken.  Als ze geen directe inspecties kunnen uitvoeren, moeten relying parties (RPs) vertrouwensrelaties ontwikkelen met de IdP's en AtP's waarmee ze willen samenwerken.

Naarmate het aantal consumenten en aanbieders van digitale identiteitsinformatie toeneemt, is het moeilijk om het pairwise beheer van deze vertrouwensrelaties voort te zetten, of zelfs de pairwise uitwisseling van de technische metadata die nodig is voor netwerkconnectiviteit.  Federatie hubs hebben slechts beperkt succes geboekt bij het oplossen van deze problemen.

### <a name="what-a-trust-framework-specification-defines"></a>Wat een specificatie van het Trustframework definieert
TFs zijn de spilvan het Open Identity Exchange (OIX) Trust Framework-model, waarbij elke gemeenschap van belang wordt beheerst door een bepaalde TF-specificatie. Een dergelijke TF-specificatie definieert:

- **De beveiligings- en privacystatistieken voor de gemeenschap van belang met de definitie van:**
    - De mate van zekerheid (LOA) die door de deelnemers worden aangeboden/vereist; bijvoorbeeld een geordende set vertrouwensbeoordelingen voor de authenticiteit van digitale identiteitsgegevens.
    - De beschermingsniveaus (LOP) die door deelnemers worden aangeboden/vereist; bijvoorbeeld een geordende set vertrouwensbeoordelingen voor de bescherming van digitale identiteitsgegevens die wordt verwerkt door deelnemers in de gemeenschap van belang.

- **De beschrijving van de digitale identiteitsgegevens die door deelnemers wordt aangeboden/vereist.**

- **Het technisch beleid voor de productie en consumptie van digitale identiteitsinformatie, en dus voor het meten van LOA en LOP. Deze schriftelijke beleidsregels bevatten doorgaans de volgende categorieën beleidsregels:**
    - Beleid inzake identiteitscontrole, bijvoorbeeld: *Hoe sterk wordt iemands identiteitsgegevens doorgelicht?*
    - Beveiligingsbeleid, bijvoorbeeld: *Hoe sterk worden de integriteit en vertrouwelijkheid van informatie beschermd?*
    - Privacybeleid, bijvoorbeeld: *Welke controle heeft een gebruiker over persoonlijke identificeerbare informatie (PII)*?
    - Overlevingsbeleid, bijvoorbeeld: *Als een provider zijn activiteiten staakt, hoe werkt de continuïteit en bescherming van PII dan?*

- **De technische profielen voor de productie en consumptie van digitale identiteitsinformatie. Deze profielen omvatten:**
    - Scope interfaces waarvoor digitale identiteitsinformatie beschikbaar is op een bepaalde LOA.
    - Technische vereisten voor on-the-wire interoperabiliteit.

- **De beschrijvingen van de verschillende rollen die deelnemers in de community kunnen uitvoeren en de kwalificaties die nodig zijn om deze rollen te vervullen.**

Zo bepaalt een TF-specificatie hoe identiteitsinformatie wordt uitgewisseld tussen de deelnemers van de gemeenschap van belang: relying parties, identity and attribute providers, en attribuut verifiers.

Een TF-specificatie is een of meerdere documenten die dienen als referentie voor het bestuur van de gemeenschap van belang die de bewering en consumptie van digitale identiteitsinformatie binnen de gemeenschap regelt. Het is een gedocumenteerde reeks beleidsregels en procedures die zijn ontworpen om vertrouwen te stellen in de digitale identiteiten die worden gebruikt voor online transacties tussen leden van een gemeenschap van belang.

Met andere woorden, een TF-specificatie definieert de regels voor het creëren van een levensvatbaar gefedereerd identiteitsecosysteem voor een gemeenschap.

Momenteel is er brede overeenstemming over het voordeel van een dergelijke aanpak. Het lijdt geen twijfel dat specificaties van het vertrouwenskader de ontwikkeling van digitale identiteitsecosystemen met verifieerbare beveiligings-, zekerheids- en privacykenmerken vergemakkelijken, wat betekent dat ze kunnen worden hergebruikt in meerdere gemeenschappen die van belang zijn.

Om die reden gebruikt het aangepaste azure AD B2C-beleid dat het Identity Experience Framework gebruikt, de specificatie als basis voor de gegevensweergave voor een TF om interoperabiliteit te vergemakkelijken.

Azure AD B2C Custom-beleidsregels die gebruikmaken van het Identity Experience Framework vertegenwoordigen een TF-specificatie als een mix van menselijke en machineleesbare gegevens. Sommige delen van dit model (meestal secties die meer gericht zijn op governance) worden weergegeven als verwijzingen naar gepubliceerde documentatie over beveiligings- en privacybeleiden, samen met de bijbehorende procedures (indien van toepassing). Andere secties beschrijven in detail de configuratiemetagegevens en runtime-regels die operationele automatisering vergemakkelijken.

## <a name="understand-trust-framework-policies"></a>Inzicht in beleid voor vertrouwenskaders

In termen van implementatie bestaat de TF-specificatie uit een reeks beleidsregels die volledige controle over identiteitsgedrag en ervaringen mogelijk maken.  Met het aangepaste Azure AD B2C-beleid dat gebruikmaakt van het Identity Experience Framework, u uw eigen TF maken en maken via dergelijke declaratieve beleidsregels die kunnen definiëren en configureren:

- De documentverwijzing of verwijzingen die het federatieve identiteitsecosysteem van de gemeenschap definiëren dat betrekking heeft op de TF. Het zijn links naar de TF-documentatie. De (vooraf gedefinieerde) operationele "runtime"-regels of de gebruikersritten die de uitwisseling en het gebruik van de claims automatiseren en/of beheren. Deze gebruikersreizen zijn gekoppeld aan een LOA (en een LOP). Een beleid kan daarom gebruikersreizen met verschillende LOA's (en LOPs) hebben.

- De identiteits- en attribuutaanbieders, of de claimproviders, in de gemeenschap van belang en de technische profielen die zij ondersteunen, samen met de (out-of-band) LOA/LOP-accreditatie die op hen betrekking heeft.

- De integratie met attribuutverificeerders of claimsproviders.

- De vertrouwende partijen in de gemeenschap (door gevolgtrekking).

- De metadata voor het opzetten van netwerkcommunicatie tussen deelnemers. Deze metadata, samen met de technische profielen, worden gebruikt tijdens een transactie om "on the wire" interoperabiliteit tussen de relying party en andere deelnemers aan de gemeenschap plumb.

- De protocolconversie indien aanwezig (bijvoorbeeld SAML 2.0, OAuth2, WS-Federation en OpenID Connect).

- De verificatievereisten.

- De multifactor orkestratie indien van toepassing.

- Een gedeeld schema voor alle claims die beschikbaar zijn en in kaart brengen aan deelnemers van een gemeenschap van belang.

- Alle claims transformaties, samen met de mogelijke data minimalisatie in deze context, om de uitwisseling en het gebruik van de claims te ondersteunen.

- De binding en encryptie.

- De opslag van claims.

### <a name="understand-claims"></a>Inzicht in claims

> [!NOTE]
> We verwijzen gezamenlijk naar alle mogelijke soorten identiteitsgegevens die kunnen worden uitgewisseld als "claims": claims over de authenticatiereferentie van een eindgebruiker, identiteitscontrole, communicatieapparaat, fysieke locatie, persoonlijk identificeerbare kenmerken, en ga zo maar door.
>
> We gebruiken de term "claims"-- in plaats van "attributen"-- omdat in online transacties, deze gegevens artefacten zijn geen feiten die direct kunnen worden geverifieerd door de relying party. Het zijn eerder beweringen, of beweringen, over feiten waarvoor de relying party voldoende vertrouwen moet ontwikkelen om de gevraagde transactie van de eindgebruiker toe te kennen.
>
> We gebruiken ook de term 'claims' omdat azure AD B2C-aangepaste beleidsregels die gebruikmaken van het Identity Experience Framework zijn ontworpen om de uitwisseling van alle soorten digitale identiteitsgegevens op een consistente manier te vereenvoudigen, ongeacht of het onderliggende protocol gedefinieerd voor gebruikersverificatie of het ophalen van kenmerken.  Op dezelfde manier gebruiken we de term "claims providers" om gezamenlijk te verwijzen naar identiteitsproviders, attribuutproviders en attribuutverificateurs wanneer we geen onderscheid willen maken tussen hun specifieke functies.

Zo bepalen ze hoe identiteitsinformatie wordt uitgewisseld tussen een relying party, identity en attribute providers, en attribuut verificateurs. Zij bepalen welke identiteits- en attribuutproviders nodig zijn voor de verificatie van een relying party. Ze moeten worden beschouwd als een domeinspecifieke taal (DSL), dat wil zeggen een computertaal die is gespecialiseerd voor een bepaald toepassingsdomein met overerving, *als* verklaringen, polymorfisme.

Deze beleidsregels vormen het machineleesbare gedeelte van de TF-constructie in Azure AD B2C Custom-beleid dat gebruikmaakt van het Identity Experience Framework. Ze omvatten alle operationele details, waaronder metadata en technische profielen van schadeproviders, definities van schadeschema's, claimstransformatiefuncties en gebruikerstrajecten die worden ingevuld om operationele orkestratie en automatisering te vergemakkelijken.

Zij worden verondersteld *levende documenten* te zijn, omdat de kans groot is dat de inhoud ervan in de loop van de tijd zal veranderen met betrekking tot de actieve deelnemers die in het beleid zijn aangegeven. Er is ook het potentieel dat de voorwaarden voor het zijn een deelnemer kunnen veranderen.

Federatie-installatie en -onderhoud worden sterk vereenvoudigd door vertrouwende partijen te beschermen tegen doorlopende vertrouwens- en connectiviteitsherconfiguraties, omdat verschillende claimproviders/verificateurs lid worden van of vertrekken (de community die wordt vertegenwoordigd door) de set beleidsregels.

Interoperabiliteit is een andere belangrijke uitdaging. Bijkomende leveranciers/verificateurs moeten worden geïntegreerd, omdat relying parties waarschijnlijk niet alle benodigde protocollen ondersteunen. Het aangepaste azure B2C-beleid lost dit probleem op door industriestandaardprotocollen te ondersteunen en door specifieke gebruikersreizen toe te passen om aanvragen om te zetten wanneer relying-partijen en kenmerkproviders niet hetzelfde protocol ondersteunen.

Gebruikersreizen omvatten protocolprofielen en metadata die worden gebruikt om "on the wire" interoperabiliteit tussen de relying party en andere deelnemers te laten doorlopen. Er zijn ook operationele runtime-regels die worden toegepast op verzoeken/antwoordberichten voor identiteitsinformatieuitwisseling voor het afdwingen van naleving van gepubliceerd beleid als onderdeel van de TF-specificatie. Het idee van user journeys is de sleutel tot het aanpassen van de klantervaring. Het werpt ook licht op hoe het systeem werkt op protocolniveau.

Op basis daarvan kunnen toepassingen en portals van relying party, afhankelijk van hun context, een beroep doen op azure AD B2C-aangepaste beleidsregels die gebruikmaken van het Identity Experience Framework waarbij de naam van een specifiek beleid wordt doorgegeven en precies het gedrag en de informatie-uitwisseling worden verkregen ze willen zonder enige muss, gedoe, of risico.
