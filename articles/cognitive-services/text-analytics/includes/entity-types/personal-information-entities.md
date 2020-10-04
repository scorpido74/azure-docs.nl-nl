---
title: Benoemde entiteiten voor persoonlijke gegevens
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/16/2020
ms.author: aahi
ms.openlocfilehash: 74586222bbd92b2d6182b34b4667cda0bc599b93
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91709306"
---
> [!NOTE]
> Gebruik de `domain=phi` para meter en model versie of hoger om de beschermde status informatie te detecteren (Phi) `2020-04-01` .
>
> Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.2/entities/recognition/pii?domain=phi&model-version=2020-07-01`
 
De volgende entiteits categorieën worden geretourneerd wanneer u aanvragen naar het `/v3.1-preview.2/entities/recognition/pii` eind punt verzendt.

| Categorie   | Subcategorie | Beschrijving                          | Model versie starten | Notities |
|------------|-------------|--------------------------------------|------------------------|---|
| Persoon     | N.v.t.         | Namen van personen.  | `2019-10-01`  | Ook geretourneerd met `domain=phi` . |
| PersonType | N.v.t.         | Taak typen of-rollen die door een persoon worden beheerd. | `2020-02-01` | |
| PhoneNumber | N.v.t. | Telefoon nummers (alleen telefoon nummers voor VS en EU). | `2019-10-01` | Ook geretourneerd met `domain=phi` . |
|Organisatie  | N.v.t. | Bedrijven, politieke groepen, muziek banden, sport clubs, overheids instanties en open bare organisaties.  | `2019-10-01` | Nationale en religions zijn niet opgenomen in dit entiteits type.  |
|Organisatie | Medisch | Medische bedrijven en groepen. | `2020-04-01` |  |
|Organisatie | Beurs | Voorraad wisselkoers groepen. | `2020-04-01` |  |
| Organisatie | Sporten | Sport organisaties. | `2020-04-01` |  |
| Adres | N.v.t. | Volledige mailing adressen.  | `2020-04-01` | Ook geretourneerd met `domain=phi` . |
| EU GPS-coördinaten | N.v.t. | GPS-coördinaten voor locaties binnen de Europese Unie.  | `2019-10-01` |  |
| E-mail | N.v.t. | E-mail adressen. | `2019-10-01` | Ook geretourneerd met `domain=phi` .   |
| URL | N.v.t. | Url's naar websites. | `2019-10-01` | Ook geretourneerd met `domain=phi` . |
| IP | N.v.t. | IP-adressen van het netwerk. | `2019-10-01` | Ook geretourneerd met `domain=phi` . |
| DateTime | N.v.t. | Datums en tijden van de dag. | `2019-10-01` |  | 
| Datum/Tijd | Datum | Kalender datums. | `2019-10-01` | Ook geretourneerd met `domain=phi` . |
| Hoeveelheid | N.v.t. | Cijfers en numerieke aantallen. | `2019-10-01` |  |
| Hoeveelheid | Leeftijd | Leeftijd. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure-gegevens

Deze entiteits categorie bevat Identificeer bare Azure-informatie, inclusief verificatie gegevens en verbindings reeksen. Beschikbaar vanaf model versie `2019-10-01` . Niet geretourneerd met de `domain=phi` para meter.

| Subcategorie                           | Beschrijving                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Verificatie sleutel voor Azure DocumentDB             | Autorisatie sleutel voor een Azure Cosmos DB-server.                           |
| Verbindings reeks voor de Azure IAAS-data base en de Azure SQL-verbindings reeks | Verbindings reeks voor een Azure Infrastructure as a service-data base (IaaS) en SQL connection string. |
| Azure SQL-verbindings reeks           | Verbindings reeks voor een data base in Azure SQL Database.                                |
| Azure IoT-verbindings reeks           | Verbindings reeks voor Azure IoT.                        |
| Wacht woord voor de publicatie-instelling van Azure        | Wacht woord voor Azure Publish-instellingen.                                        |
| Verbindings reeks Azure Redis Cache   | Verbindings reeks voor een redis-cache.                             |
| Azure SAS                             | De verbindings reeks voor de Azure-software als een service (SaaS).                     |
| Verbindings reeks Azure Service Bus   | Verbindings reeks voor een Azure service bus.                                 |
| Sleutel van Azure Storage-account             | Account sleutel voor een Azure-opslag account.                                   |
| Sleutel van Azure Storage-account (algemeen)   | Algemene account sleutel voor een Azure-opslag account.                           |
| Verbindings reeks SQL Server          | Verbindings reeks voor een computer met SQL Server.                                         |

## <a name="identification"></a>Identificatie

[!INCLUDE [supported identification entities](./identification-entities.md)]
