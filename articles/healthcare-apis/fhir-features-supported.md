---
title: Ondersteunde FHIR-functies in azure-Azure API voor FHIR
description: In dit artikel wordt uitgelegd welke functies van de FHIR-specificatie zijn geïmplementeerd in azure API voor FHIR
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: cavoeg
ms.openlocfilehash: ea9a47676b8294b2541c27d361b0dc2fa1ae3627
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92339505"
---
# <a name="features"></a>Functies

Azure API voor FHIR biedt een volledig beheerde implementatie van de micro soft FHIR-server voor Azure. De server is een implementatie van de [FHIR](https://hl7.org/fhir) -standaard. In dit document worden de belangrijkste functies van de FHIR-server vermeld.

## <a name="fhir-version"></a>FHIR-versie

Nieuwste versie ondersteund: `4.0.1`

Eerdere versies die momenteel worden ondersteund, zijn onder andere: `3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lezen                           | Ja       | Ja       | Ja       |                                                     |
| vread                          | Ja       | Ja       | Ja       |                                                     |
| update                         | Ja       | Ja       | Ja       |                                                     |
| bijwerken met optimistische vergren deling | Ja       | Ja       | Ja       |                                                     |
| Update (voorwaardelijk)           | Ja       | Ja       | Ja       |                                                     |
| verzenden                          | Nee        | Nee        | Nee        |                                                     |
| delete                         | Ja       | Ja       | Ja       |                                                     |
| verwijderen (voorwaardelijk)           | Nee        | Nee        | Nee        |                                                     |
| geschiedenis                        | Ja       | Ja       | Ja       |                                                     |
| maken                         | Ja       | Ja       | Ja       | Ondersteuning voor zowel POST/PUT                               |
| maken (voorwaardelijk)           | Ja       | Ja       | Ja       |                                                     |
| zoeken                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Zie hieronder                                           |
| geketende zoek opdracht                 | Nee        | Ja       | Nee        |                                           |
| geketende zoek opdracht omkeren         | Nee        | Nee        | Nee        |                                            |
| mogelijkheden                   | Ja       | Ja       | Ja       |                                                     |
| batch                          | Ja       | Ja       | Ja       |                                                     |
| trans actie                    | Nee        | Ja       | Nee        |                                                     |
| haalt                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `self` en `next` worden ondersteund                     |
| schakels                 | Nee        | Nee        | Nee        |                                                     |

## <a name="search"></a>Search

Alle typen zoek parameters worden ondersteund. 

| Type zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
| Aantal                | Ja       | Ja       | Ja       |         |
| Datum/datum/tijd         | Ja       | Ja       | Ja       |         |
| Tekenreeks                | Ja       | Ja       | Ja       |         |
| Token                 | Ja       | Ja       | Ja       |         |
| Naslaginformatie             | Ja       | Ja       | Ja       |         |
| Composite             | Ja       | Ja       | Ja       |         |
| Aantal              | Ja       | Ja       | Ja       |         |
| URI                   | Ja       | Ja       | Ja       |         |
| Specifiek               | Nee        | Nee        | Nee        |         |


| Para meters             | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Ja       | Ja       | Ja       |         |
|`:exact`               | Ja       | Ja       | Ja       |         |
|`:contains`            | Ja       | Ja       | Ja       |         |
|`:text`                | Ja       | Ja       | Ja       |         |
|`:in` token          | Nee        | Nee        | Nee        |         |
|`:below` token       | Nee        | Nee        | Nee        |         |
|`:above` token       | Nee        | Nee        | Nee        |         |
|`:not-in` token      | Nee        | Nee        | Nee        |         |
|`:[type]` referentielaag  | Nee        | Nee        | Nee        |         |
|`:below` URI         | Ja       | Ja       | Ja       |         |
|`:not`                 | Nee        | Nee        | Nee        |         |
|`:above` URI         | Nee        | Nee        | Nee        | Probleem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Algemene zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Ja       | Ja       | Ja       |         |
| `_lastUpdated`          | Ja       | Ja       | Ja       |         |
| `_tag`                  | Ja       | Ja       | Ja       |         |
| `_profile`              | Ja       | Ja       | Ja       |         |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_text`                 | Nee        | Nee        | Nee        |         |
| `_content`              | Nee        | Nee        | Nee        |         |
| `_list`                 | Ja       | Ja       | Ja       |         |
| `_has`                  | Nee        | Nee        | Nee        |         |
| `_type`                 | Ja       | Ja       | Ja       |         |
| `_query`                | Nee        | Nee        | Nee        |         |
| `_filter`               | Nee        | Nee        | Nee        |         |

| Zoek resultaat parameters | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------|-----------|-----------|-----------|---------|
| `_sort`                 | Gedeeltelijk        | Gedeeltelijk   | Gedeeltelijk        |   `_sort=_lastUpdated` wordt ondersteund       |
| `_count`                | Ja       | Ja       | Ja       | `_count` is beperkt tot 100 tekens. Als deze waarde hoger is dan 100, wordt alleen 100 geretourneerd en wordt er een waarschuwing in de bundel geretourneerd. |
| `_include`              | Nee        | Ja       | Nee        |         |
| `_revinclude`           | Nee        | Ja       | Nee        | Opgenomen items zijn beperkt tot 100. |
| `_summary`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_summary=count` wordt ondersteund |
| `_total`                | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | _total = niet en _total = nauw keurig      |
| `_elements`             | Ja       | Ja       | Ja       |         |
| `_contained`            | Nee        | Nee        | Nee        |         |
| `containedType`         | Nee        | Nee        | Nee        |         |
| `_score`                | Nee        | Nee        | Nee        |         |

## <a name="extended-operations"></a>Uitgebreide bewerkingen

Alle bewerkingen die worden ondersteund om de REST-API uit te breiden.

| Type zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|------------------------|-----------|-----------|-----------|---------|
| $export (heel systeem) | Ja       | Ja       | Ja       |         |
| Patiënt/$export        | Ja       | Ja       | Ja       |         |
| Groep/$export          | Ja       | Ja       | Ja       |         |

## <a name="persistence"></a>Persistentie

De micro soft FHIR-server heeft een pluggable persistentie module (Zie [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Momenteel bevat de open-source code van de FHIR-server een implementatie voor [Azure Cosmos DB](../cosmos-db/index-overview.md) en [SQL database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB is een wereld wijd gedistribueerde multi-model-data base (SQL API, MongoDB API, etc.). Het ondersteunt verschillende [consistentie niveaus](../cosmos-db/consistency-levels.md). Met de standaard implementatie sjabloon wordt de FHIR-server met `Strong` consistentie geconfigureerd, maar het consistentie beleid kan worden gewijzigd (over het algemeen ongeforceerd) op een aanvraag op basis van de aanvraag `x-ms-consistency-level` header.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

De FHIR-server gebruikt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) voor toegangs beheer. Met name Role-Based Access Control (RBAC) wordt afgedwongen, als de `FhirServer:Security:Enabled` configuratie parameter is ingesteld op `true` en voor alle aanvragen (behalve `/metadata` ) naar de FHIR-server moet de `Authorization` aanvraag header zijn ingesteld op `Bearer <TOKEN>` . Het token moet een of meer rollen bevatten zoals gedefinieerd in de `roles` claim. Een aanvraag wordt toegestaan als het token een rol bevat waarmee de opgegeven actie kan worden uitgevoerd voor de opgegeven bron.

Op dit moment worden de toegestane acties voor een bepaalde rol *globaal* toegepast op de API.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer informatie over de ondersteunde FHIR-functies in azure API voor FHIR. Implementeer vervolgens de Azure-API voor FHIR.
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)
