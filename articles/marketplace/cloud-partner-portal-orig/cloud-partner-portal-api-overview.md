---
title: Referentie voor API-api's voor cloudpartners | Azure Marketplace
description: Beschrijving van, voorwaarden om te gebruiken en lijst van marketplace API-bewerkingen.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: b9f698ea81830aaa8761c05012cf6843d07ad5a4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256413"
---
# <a name="cloud-partner-portal-api-reference"></a>Naslaginformatie over Cloud Partner-portal-API

> [!NOTE]
> De API's van cloudpartnerportalen zijn geïntegreerd met het Partner Center en blijven werken nadat uw aanbiedingen zijn gemigreerd naar partnercentrum. De integratie brengt kleine veranderingen met zich mee. Bekijk de [wijzigingen in CPP-API's](#changes-to-cpp-apis-after-the-migration-to-partner-center) die in dit document worden weergegeven om ervoor te zorgen dat uw code blijft werken na de migratie naar partnercentrum.

De API's van Cloud Partner Portal REST maken het programmatisch ophalen en manipuleren van workloads, aanbiedingen en uitgeversprofielen mogelijk. De API's gebruiken op rollen gebaseerd toegangscontrole (RBAC) om de juiste machtigingen af te dwingen tijdens het verwerken.

Deze referentie bevat de technische details voor de API's van cloudpartnerportal REST. De payload monsters in dit document zijn alleen ter referentie en zijn onderhevig aan verandering als nieuwe functionaliteit wordt toegevoegd.

## <a name="prerequisites-and-considerations"></a>Voorwaarden en overwegingen

Voordat u de API's gebruikt, moet u het:

- Het artikel [Voorwaarden](./cloud-partner-portal-api-prerequisites.md) voor meer informatie over het toevoegen van een serviceprincipal aan uw account en het krijgen van een Azure Active Directory-toegangstoken (Azure AD) voor verificatie.
- De twee [strategieën voor gelijktijdigheidscontrole](./cloud-partner-portal-api-concurrency-control.md) die beschikbaar zijn om deze API's aan te roepen.
- Aanvullende [API-overwegingen,](./cloud-partner-portal-api-considerations.md)zoals versiebeheer en foutafhandeling.

## <a name="changes-to-cpp-apis-after-the-migration-to-partner-center"></a>Wijzigingen in CPP API's na de migratie naar partnercentrum

| **Api** | **Beschrijving wijzigen** | **Impact** |
| ------- | ---------------------- | ---------- |
| POST publiceren, GoLive, Annuleren | Voor gemigreerde aanbiedingen heeft de antwoordkop een andere indeling, maar blijft deze op dezelfde manier werken, waarbij een relatief pad wordt aangenomen om de bewerkingsstatus op te halen. | Wanneer u een van de bijbehorende POST-aanvragen voor een aanbieding verzendt, heeft de koptekst Locatie een van de twee indelingen, afhankelijk van de migratiestatus van de aanbieding:<ul><li>Niet-gemigreerde aanbiedingen<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Gemigreerde aanbiedingen<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| GET-bewerking | Voor aanbiedingstypen die eerder het veld 'melding-e-mail' in het antwoord ondersteunden, wordt dit veld afgeschaft en niet meer geretourneerd voor gemigreerde aanbiedingen. | Voor gemigreerde aanbiedingen sturen we geen meldingen meer naar de lijst met e-mails die in de aanvragen zijn opgegeven. In plaats daarvan wordt de API-service afgestemd op het e-mailproces van meldingen in Partnercentrum om e-mails te verzenden. In het bijzonder worden meldingen verzonden naar het e-mailadres dat is ingesteld in de sectie Contactgegevens van de verkoper van uw accountinstellingen in het Partnercentrum, om u op de hoogte te stellen van de voortgang van de werking.<br><br>Controleer het e-mailadres dat is ingesteld in de sectie Contactgegevens van de verkoper van je [accountinstellingen](https://partner.microsoft.com/dashboard/account/management) in het Partnercentrum om te controleren of de juiste e-mail wordt verstrekt voor meldingen.  |

## <a name="common-tasks"></a>Algemene taken

Deze verwijzing bevat API's om de volgende algemene taken uit te voeren.

### <a name="offers"></a>Aanbiedingen

- [Alle aanbiedingen ophalen](./cloud-partner-portal-api-retrieve-offers.md)
- [Een specifieke aanbieding ophalen](./cloud-partner-portal-api-retrieve-specific-offer.md)
- [Aanbiedingsstatus ophalen](./cloud-partner-portal-api-retrieve-offer-status.md)
- [Een aanbieding maken](./cloud-partner-portal-api-creating-offer.md)
- [Een aanbieding publiceren](./cloud-partner-portal-api-publish-offer.md)

### <a name="operations"></a>Bewerkingen

- [Bewerkingen ophalen](./cloud-partner-portal-api-retrieve-operations.md)
- [Bewerkingen annuleren](./cloud-partner-portal-api-cancel-operations.md)

### <a name="publish-an-app"></a>Een app publiceren

- [Live gaan](./cloud-partner-portal-api-go-live.md)

### <a name="other-tasks"></a>Overige taken

- [Prijsinstellen voor aanbiedingen voor virtuele machines](./cloud-partner-portal-api-setting-price.md)

### <a name="troubleshooting"></a>Problemen oplossen

- [Problemen met verificatiefouten oplossen](./cloud-partner-portal-api-troubleshooting-authentication-errors.md)
