---
title: Ondersteunde FHIR-functies in azure-Azure API voor FHIR
description: In dit artikel wordt uitgelegd welke functies van de FHIR-specificatie zijn geïmplementeerd in azure API voor FHIR
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 7f92395f19d84f904493af458d1334f8013fd263
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85808026"
---
# <a name="features"></a>Functies

Azure API voor FHIR biedt een volledig beheerde implementatie van de micro soft FHIR-server voor Azure. De server is een implementatie van de [FHIR](https://hl7.org/fhir) -standaard. In dit document worden de belangrijkste functies van de FHIR-server vermeld.

## <a name="fhir-version"></a>FHIR-versie

Nieuwste versie ondersteund:`4.0.1`

Eerdere versies die momenteel worden ondersteund, zijn onder andere:`3.0.2`

## <a name="rest-api"></a>REST-API

| API                            | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking                                             |
|--------------------------------|-----------|-----------|-----------|-----------------------------------------------------|
| lezen                           | Ja       | Ja       | Yes       |                                                     |
| vread                          | Ja       | Ja       | Yes       |                                                     |
| update                         | Ja       | Ja       | Yes       |                                                     |
| bijwerken met optimistische vergren deling | Ja       | Ja       | Yes       |                                                     |
| Update (voorwaardelijk)           | Ja       | Ja       | Yes       |                                                     |
| verzenden                          | No        | Nee        | Nee        |                                                     |
| delete                         | Ja       | Ja       | Yes       |                                                     |
| verwijderen (voorwaardelijk)           | No        | Nee        | Nee        |                                                     |
| maken                         | Ja       | Ja       | Yes       | Ondersteuning voor zowel POST/PUT                               |
| maken (voorwaardelijk)           | Ja       | Ja       | Yes       |                                                     |
| zoeken                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | Zie hieronder                                           |
| geketende zoek opdracht                 | No        | Yes       | No        |                                           |
| geketende zoek opdracht omkeren         | No        | Nee        | Nee        |                                            |
| mogelijkheden                   | Ja       | Ja       | Yes       |                                                     |
| batch                          | Ja       | Ja       | Yes       |                                                     |
| trans actie                    | No        | Yes       | No        |                                                     |
| geschiedenis                        | Ja       | Ja       | Yes       |                                                     |
| haalt                         | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `self`en `next` worden ondersteund                     |
| schakels                 | No        | Nee        | Nee        |                                                     |

## <a name="search"></a>Search

Alle typen zoek parameters worden ondersteund. 

| Type zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
| Aantal                | Yes       | Yes       | Yes       |         |
| Datum/datum/tijd         | Yes       | Yes       | Ja       |         |
| Tekenreeks                | Ja       | Yes       | Yes       |         |
| Token                 | Yes       | Yes       | Yes       |         |
| Verwijzing             | Yes       | Yes       | Yes       |         |
| Composite             | Yes       | Yes       | Yes       |         |
| Aantal              | Yes       | Yes       | Yes       |         |
| URI                   | Yes       | Yes       | Yes       |         |
| Specifiek               | Nee        | Nee        | Nee        |         |


| Para meters             | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-----------------------|-----------|-----------|-----------|---------|
|`:missing`             | Yes       | Yes       | Yes       |         |
|`:exact`               | Yes       | Yes       | Yes       |         |
|`:contains`            | Yes       | Yes       | Yes       |         |
|`:text`                | Yes       | Yes       | Yes       |         |
|`:in`token          | Nee        | Nee        | Nee        |         |
|`:below`token       | Nee        | Nee        | Nee        |         |
|`:above`token       | Nee        | Nee        | Nee        |         |
|`:not-in`token      | Nee        | Nee        | Nee        |         |
|`:[type]`referentielaag  | Nee        | Nee        | Nee        |         |
|`:below`URI         | Yes       | Yes       | Yes       |         |
|`:not`                 | Nee        | Nee        | Nee        |         |
|`:above`URI         | Nee        | Nee        | Nee        | Probleem [#158](https://github.com/Microsoft/fhir-server/issues/158) |

| Algemene zoek parameter | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------| ----------| ----------| ----------|---------|
| `_id`                   | Yes       | Yes       | Yes       |         |
| `_lastUpdated`          | Yes       | Yes       | Yes       |         |
| `_tag`                  | Yes       | Yes       | Yes       |         |
| `_profile`              | Ja       | Ja       | Ja       |         |
| `_security`             | Ja       | Ja       | Ja       |         |
| `_text`                 | Nee        | Nee        | Nee        |         |
| `_content`              | Nee        | Nee        | Nee        |         |
| `_list`                 | Nee        | Yes       | Ja       |         |
| `_has`                  | Nee        | Nee        | Nee        |         |
| `_type`                 | Yes       | Ja       | Ja       |         |
| `_query`                | Nee        | Nee        | Nee        |         |

| Zoek bewerkingen       | Ondersteund-PaaS | Ondersteund-OSS (SQL) | Ondersteund-OSS (Cosmos DB) | Opmerking |
|-------------------------|-----------|-----------|-----------|---------|
| `_filter`               | Nee        | Nee        | Nee        |         |
| `_sort`                 | Nee        | Nee        | Nee        |         |
| `_score`                | Nee        | Nee        | Nee        |         |
| `_count`                | Yes       | Ja       | Ja       |         |
| `_summary`              | Gedeeltelijk   | Gedeeltelijk   | Gedeeltelijk   | `_summary=count`wordt ondersteund |
| `_include`              | No        | Yes       | Nee        |         |
| `_revinclude`           | Nee        | Nee        | Nee        |         |
| `_contained`            | Nee        | Nee        | Nee        |         |
| `_elements`             | Nee        | Nee        | Nee        |         |

## <a name="persistence"></a>Persistentie

De micro soft FHIR-server heeft een pluggable persistentie module (Zie [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence) ).

Momenteel bevat de open-source code van de FHIR-server een implementatie voor [Azure Cosmos DB](../cosmos-db/index-overview.md) en [SQL database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB is een wereld wijd gedistribueerde multi-model-data base (SQL API, MongoDB API, etc.). Het ondersteunt verschillende [consistentie niveaus](../cosmos-db/consistency-levels.md). Met de standaard implementatie sjabloon wordt de FHIR-server met `Strong` consistentie geconfigureerd, maar het consistentie beleid kan worden gewijzigd (over het algemeen ongeforceerd) op een aanvraag op basis van de aanvraag `x-ms-consistency-level` header.

## <a name="role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer

De FHIR-server gebruikt [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) voor toegangs beheer. Met name op rollen gebaseerd Access Control (RBAC) wordt afgedwongen, als de `FhirServer:Security:Enabled` configuratie parameter is ingesteld op `true` en voor alle aanvragen (behalve `/metadata` ) naar de FHIR-server moet de `Authorization` aanvraag header zijn ingesteld op `Bearer <TOKEN>` . Het token moet een of meer rollen bevatten zoals gedefinieerd in de `roles` claim. Een aanvraag wordt toegestaan als het token een rol bevat waarmee de opgegeven actie kan worden uitgevoerd voor de opgegeven bron.

Op dit moment worden de toegestane acties voor een bepaalde rol *globaal* toegepast op de API.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u meer informatie over de ondersteunde FHIR-functies in azure API voor FHIR. Implementeer vervolgens de Azure-API voor FHIR.
 
>[!div class="nextstepaction"]
>[De Azure-API voor FHIR implementeren](fhir-paas-portal-quickstart.md)
