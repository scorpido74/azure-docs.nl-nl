---
title: bestand opnemen
description: bestand opnemen
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: c0ba8e76e069bf9dc1c96aecdc670699c32b3c96
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90944590"
---
## <a name="object-model"></a>Objectmodel

Met toegangstokens voor gebruikers kunt u de gebruikers van uw toepassing machtigen om rechtstreeks verbinding te maken met uw Azure Communication Services-resource. U genereert deze tokens op uw server, stuurt ze terug naar uw clienttoepassing en gebruikt deze om de Communication Services-clientbibliotheken te initialiseren. Om dit te bereiken, moet u een vertrouwd service-eindpunt bouwen dat uw gebruikers kan verifiëren en toegangstokens voor gebruikers kan uitgeven. Deze service moet over een permanente toewijzing beschikken tussen de gebruikersidentiteiten van uw toepassingen en gebruikers van Azure Communication Services.

De CommunicationIdentityClient-klasse biedt alle functionaliteit voor gebruikers- en toegangstokens. U maakt een instantie met een verbindingsreeks en gebruikt deze vervolgens om gebruikers te beheren en toegangstokens uit te geven.

> [!WARNING]
> Tokens zijn gevoelige gegevens, omdat ze toegang verlenen tot de resources van een gebruiker. Daarom is het belangrijk om tokens te beschermen tegen inbreuken. U moet een vertrouwd service-eindpunt maken dat alleen toegangstokens uitgeeft aan gemachtigde gebruikers van uw toepassing. Als u tokens voor gebruikerstoegang in een externe opslag opslaat, wordt u ten zeerste aangeraden versleuteling te gebruiken.

### <a name="access-token-scopes"></a>Bereiken van toegangstokens

Met bereiken kunt u de exacte functionaliteit van de Azure Communication Services opgeven waar een toegangstoken voor gebruikers machtiging kan geven. Bereiken worden toegepast op individuele toegangstokens voor gebruikers. Azure Communication Services ondersteunt de volgende bereiken voor toegangstokens voor gebruiken:

| Naam   | Beschrijving                                                                         |
| ------ | ----------------------------------------------------------------------------------- |
| `Chat` | Verleent de mogelijkheid om deel te nemen aan een chatsessie                                         |
| `VoIP` | Verleent de mogelijkheid om VOIP-oproepen te doen en te ontvangen met behulp van de aanroepende clientbibliotheek* |
| `Pstn` | Verleent de mogelijkheid om PSTN-oproepen te doen met behulp van de aanroepende clientbibliotheek*           |

\*Functie wordt nog niet ondersteund en is binnenkort beschikbaar

Als u de toegang van een gebruiker tot bepaalde functies wilt verwijderen, moet u eerst [alle bestaande toegangstokens intrekken](#revoke-user-access-tokens) die ongewenste bereiken bevatten voordat u een nieuw token uitgeeft met een grotere set bereiken.
