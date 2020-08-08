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
ms.openlocfilehash: 2349c92fb74b546eaa929752f3d2343b9c97e6d1
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88010909"
---
> [!NOTE]
> `PHI`Gebruik de `domain=phi` para meter en model versie of later om te detecteren (beveiligde status informatie) `2020-04-01` .
>
> Bijvoorbeeld: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.1/entities/recognition/pii?domain=phi&model-version=2020-04-01`
 
De volgende entiteits categorieën worden geretourneerd bij het verzenden van aanvragen naar het `/v3.1-preview.1/entities/recognition/pii` eind punt.

| Categorie   | Subcategorie | Beschrijving                          | Model versie starten | Opmerkingen |
|------------|-------------|--------------------------------------|------------------------|---|
| Person     | N.v.t.         | Namen van personen.  | `2019-10-01`  | Ook geretourneerd met `domain=phi` . |
| PersonType | N.v.t.         | Taak typen of-rollen die door een persoon worden beheerd. | `2020-02-01` | |
| PhoneNumber | N.v.t. | Telefoon nummers (alleen telefoon nummers voor VS en EU). | `2019-10-01` | Ook geretourneerd met`domain=phi` |
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
| DateTime | Date | Kalender datums. | `2019-10-01` | Ook geretourneerd met `domain=phi` . |
| Aantal | N.v.t. | Cijfers en numerieke aantallen. | `2019-10-01` |  |
| Aantal | Ouderdom | Leeftijd. | `2019-10-01` | | |

## <a name="azure-information"></a>Azure-gegevens

Deze entiteits categorie bevat Identificeer bare Azure-informatie, inclusief verificatie gegevens en verbindings reeksen. Beschikbaar vanaf model versie `2019-10-01` . Niet geretourneerd met de `domain=phi` para meter.

| Subcategorie                           | Beschrijving                                                                 |
|---------------------------------------|-----------------------------------------------------------------------------|
| Verificatie sleutel voor Azure DocumentDB             | Autorisatie sleutel voor een Azure DocumentDB-server.                           |
| Verbindings reeks voor de Azure IAAS-data base en de Azure SQL-verbindings reeks | Verbindings reeks voor een Azure Infrastructure as a service-data base (IaaS) en SQL connection string. |
| Azure SQL-verbindings reeks           | Verbindings reeks voor een data base in Azure SQL Database.                                |
| Azure IoT-verbindings reeks           | De verbindings reeks voor Azure Internet der dingen (IoT).                        |
| Wacht woord voor de publicatie-instelling van Azure        | Wacht woord voor Azure Publish-instellingen.                                        |
| Verbindings reeks Azure Redis Cache   | Verbindings reeks voor een Azure-cache voor redis.                             |
| Azure SAS                             | Verbindings reeks voor Azure-software als een service (SAS).                     |
| Verbindings reeks Azure Service Bus   | Verbindings reeks voor een Azure service bus.                                 |
| Sleutel van Azure Storage-account             | Account sleutel voor een Azure-opslag account.                                   |
| Sleutel van Azure Storage-account (algemeen)   | Algemene account sleutel voor een Azure-opslag account.                           |
| Verbindings reeks SQL Server          | Verbindings reeks voor een SQL-Server.                                         |

## <a name="identification"></a>Identificatie

[!INCLUDE [supported identification entities](./identification-entities.md)]
