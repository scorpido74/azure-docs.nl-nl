---
title: Gebruikers deblokkeren met Azure Active Directory Identity Protection | Microsoft Docs
description: Meer informatie over het deblokkeren van gebruikers die zijn geblokkeerd door een Azure Active Directory Identity Protection beleid.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e3756435703c4e8c887a4e7b9d4f75a6701d840
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126252"
---
# <a name="how-to-unblock-users"></a>Procedure: Gebruikers deblokkeren

Met Azure Active Directory Identity Protection kunt u beleids regels configureren om gebruikers te blok keren als aan de geconfigureerde voor waarden is voldaan. Normaal gesp roken neemt een geblokkeerde gebruiker contact op met de Help Desk om te worden gedeblokkeerd. In dit artikel worden de stappen beschreven die u kunt uitvoeren om de blok kering van een geblokkeerde gebruiker op te heffen.

## <a name="determine-the-reason-for-blocking"></a>De reden voor blok kering bepalen

Als eerste stap voor het deblokkeren van een gebruiker moet u het type beleid bepalen dat de gebruiker heeft geblokkeerd omdat uw volgende stappen hiervan afhankelijk zijn.
Met Azure Active Directory Identity Protection kan een gebruiker worden geblokkeerd door een beleid voor aanmeldings Risico's of door een beleid voor gebruikers Risico's.

U kunt het type beleid ophalen dat een gebruiker heeft geblokkeerd vanuit de kop in het dialoog venster dat tijdens een aanmeldings poging aan de gebruiker is gepresenteerd:

| Beleid | Gebruikers dialoogvenster |
| --- | --- |
| Aanmeldings risico |![Geblokkeerde aanmelding](./media/howto-unblock-user/02.png) |
| Gebruikersrisico |![Geblokkeerd account](./media/howto-unblock-user/104.png) |

Een gebruiker die wordt geblokkeerd door:

* Een beleid voor aanmeldings Risico's staat ook bekend als verdachte aanmelding
* Een beleid voor gebruikers Risico's wordt ook wel een risico voor een account genoemd

## <a name="unblocking-suspicious-sign-ins"></a>De blok kering van verdachte aanmeldingen opheffen

Als u de blok kering van een verdachte aanmelding wilt opheffen, hebt u de volgende opties:

1. **Aanmelden vanaf een bekende locatie of apparaat** : een veelvoorkomende reden voor geblokkeerde verdachte aanmeldingen is aanmeldings pogingen vanaf onbekende locaties of apparaten. Uw gebruikers kunnen snel bepalen of dit de blokkerings reden is door u aan te melden vanaf een bekende locatie of apparaat.
2. **Uitsluiten van beleid** : als u denkt dat de huidige configuratie van uw aanmeldings beleid problemen veroorzaakt voor specifieke gebruikers, kunt u de gebruikers hiervan uitsluiten. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.
3. **Beleid uitschakelen** : als u denkt dat uw beleids configuratie problemen veroorzaakt voor al uw gebruikers, kunt u het beleid uitschakelen. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.

## <a name="unblocking-accounts-at-risk"></a>Blok keren van een risico voor accounts

Als u de blok kering van een account dat risico loopt, hebt u de volgende opties:

1. **Wacht woord opnieuw instellen** : u kunt het wacht woord van de gebruiker opnieuw instellen. 
2. **Alle risico detecties verwijderen** : het gebruikers risico beleid blokkeert een gebruiker als het geconfigureerde risico niveau van de gebruiker voor het blok keren van toegang is bereikt. U kunt het risico niveau van een gebruiker verlagen door gerapporteerde risico detecties hand matig af te sluiten. 
3. **Uitsluiten van beleid** : als u denkt dat de huidige configuratie van uw aanmeldings beleid problemen veroorzaakt voor specifieke gebruikers, kunt u de gebruikers hiervan uitsluiten. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.
4. **Beleid uitschakelen** : als u denkt dat uw beleids configuratie problemen veroorzaakt voor al uw gebruikers, kunt u het beleid uitschakelen. Zie [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
 
Wilt u meer weten over Azure AD Identity Protection? Bekijk de [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
