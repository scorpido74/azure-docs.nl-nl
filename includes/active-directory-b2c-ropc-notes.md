---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/27/2020
ms.author: mimart
ms.openlocfilehash: f4944881e6eff3e9a56a74bc997c280f9fff6be8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186986"
---
## <a name="ropc-flow-notes"></a>ROPC-stroomnotities
In Azure Active Directory B2C (Azure AD B2C) worden de volgende opties ondersteund:

- **Native Client:** Gebruikersinteractie tijdens verificatie vindt plaats wanneer code wordt uitgevoerd op een apparaat aan de gebruikerszijde. Het apparaat kan een mobiele applicatie zijn die wordt uitgevoerd in een native besturingssysteem, zoals Android en iOS.
- **Openbare clientstroom**: Alleen gebruikersreferenties, verzameld door een toepassing, worden verzonden in de API-aanroep. De referenties van de toepassing worden niet verzonden.
- **Nieuwe claims toevoegen:** de inhoud van id-token kan worden gewijzigd om nieuwe claims toe te voegen.

De volgende stromen worden niet ondersteund:

- **Server-naar-server:** Het identiteitsbeveiligingssysteem heeft een betrouwbaar IP-adres nodig dat is verzameld van de beller (de native client) als onderdeel van de interactie. In een API-aanroepen aan de serverzijde wordt alleen het IP-adres van de server gebruikt. Als een dynamische drempel waarde van mislukte verificaties wordt overschreden, kan het identiteitsbeveiligingssysteem een herhaald IP-adres als aanvaller identificeren.
- **Vertrouwelijke clientstroom**: De client-id van de toepassing wordt gevalideerd, maar het toepassingsgeheim wordt niet gevalideerd.

Houd bij het gebruik van de ROPC-stroom rekening met het volgende:

- ROPC werkt niet wanneer er een onderbreking is van de verificatiestroom die gebruikersinteractie vereist. Wanneer een wachtwoord bijvoorbeeld is verlopen of moet worden gewijzigd, is meervoudige verificatie vereist of moet er meer informatie worden verzameld tijdens het aanmelden (bijvoorbeeld toestemming van de gebruiker).
- ROPC ondersteunt alleen lokale accounts. Gebruikers kunnen zich niet aanmelden bij federatieve identiteitsproviders zoals Microsoft, Google+, Twitter, AD-FS of Facebook.
- Sessiebeheer, inclusief keep me signed-in (KMSI), is niet van toepassing.
