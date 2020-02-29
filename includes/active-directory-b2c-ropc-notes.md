---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: marsma
ms.openlocfilehash: fc70fb163ae1f6198f66743a739a0d9720f764f1
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77912394"
---
## <a name="ropc-flow-notes"></a>ROPC-stroom notities
In Azure Active Directory B2C (Azure AD B2C) worden de volgende opties ondersteund:

- **Systeem eigen client**: gebruikers interactie tijdens verificatie gebeurt wanneer code wordt uitgevoerd op een apparaat aan de gebruiker. Het apparaat kan een mobiele toepassing zijn die wordt uitgevoerd in een systeem eigen besturings systeem, zoals Android en iOS.
- **Open bare client stroom**: alleen gebruikers referenties, verzameld door een toepassing, worden in de API-aanroep verzonden. De referenties van de toepassing worden niet verzonden.
- **Nieuwe claims toevoegen**: de inhoud van het id-token kan worden gewijzigd om nieuwe claims toe te voegen.

De volgende stromen worden niet ondersteund:

- **Server-naar-server: voor**het systeem voor identiteits beveiliging is een betrouw bare IP-adres nodig dat door de aanroeper (de systeem eigen client) is verzameld als onderdeel van de interactie. In een API-aanroep aan de server zijde wordt alleen het IP-adres van de server gebruikt. Als een dynamische drempel van mislukte authenticaties wordt overschreden, kan het identiteits beschermings systeem een herhaald IP-adres identificeren als een aanvaller.
- **Vertrouwelijke client stroom**: de client-id van de toepassing wordt gevalideerd, maar het toepassings geheim wordt niet gevalideerd.

Wanneer u de ROPC-stroom gebruikt, moet u rekening houden met het volgende:

- ROPC werkt niet wanneer er sprake is van een onderbreking van de verificatie stroom die gebruikers interactie nodig heeft. Wanneer bijvoorbeeld een wacht woord is verlopen of moet worden gewijzigd, is multi-factor Authentication vereist of wanneer er meer informatie moet worden verzameld tijdens het aanmelden (bijvoorbeeld toestemming van de gebruiker).
- ROPC ondersteunt alleen lokale accounts. Gebruikers kunnen zich niet aanmelden met federatieve id-providers zoals micro soft, Google +, Twitter, AD-FS of Facebook.
- Sessie beheer, met inbegrip van aanmelden (KMSI), is niet van toepassing.
