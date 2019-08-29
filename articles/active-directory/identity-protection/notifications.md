---
title: Azure Active Directory Identity Protection meldingen | Microsoft Docs
description: Meer informatie over hoe meldingen uw onderzoeksactiviteiten ondersteunen.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9da0552e3ccc707c6b2f228b402f4e9db7dafee3
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125706"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Azure Active Directory Identity Protection meldingen

Azure AD Identity Protection verzendt twee typen e-mail berichten voor automatische meldingen om u te helpen bij het beheren van risico-en risico detecties van gebruikers:

- Gebruikers met een risico voor een gedetecteerd e-mail bericht
- E-mail met wekelijkse samen vatting

In dit artikel vindt u een overzicht van beide e-mail meldingen.

## <a name="users-at-risk-detected-email"></a>Gebruikers met een risico voor een gedetecteerd e-mail bericht

Als reactie op een gedetecteerd account dat risico loopt, genereert Azure AD Identity Protection een e-mail waarschuwing met **gebruikers die risico lopen** als onderwerp. Het e-mail bericht bevat een koppeling naar de gebruikers die zijn **[gemarkeerd voor een risico](../reports-monitoring/concept-user-at-risk.md)** rapport. Als best practice moet u onmiddellijk de gebruikers op risico onderzoeken.

Met de configuratie voor deze waarschuwing kunt u opgeven op welk gebruikers risico niveau u de waarschuwing wilt genereren. Het e-mail adres wordt gegenereerd wanneer het risico niveau van de gebruiker de opgegeven waarde bereikt. Er worden echter geen nieuwe gebruikers meer ontvangen die waarschuwingen hebben gevonden voor deze gebruiker nadat ze zijn overgezet naar dit risico niveau. Als u het beleid bijvoorbeeld instelt op een waarschuwing over gemiddeld risico van gebruikers en uw gebruiker John verplaatst naar gemiddeld risico, ontvangt u de gebruikers die risico lopen voor John. Er wordt echter geen waarschuwing weer gegeven wanneer John vervolgens naar een hoog risico wordt verplaatst of extra risico detecties heeft.

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/notifications/01.png)

### <a name="configuration"></a>Configuratie

Als beheerder kunt u het volgende instellen:

- **Het risico niveau van de gebruiker waarmee het genereren van deze e-mail wordt geactiveerd** : het risico niveau is standaard ingesteld op hoog risico.
- **De ontvangers van dit e-mail bericht** bevatten standaard alle globale beheerders. Globale beheerders kunnen ook andere globale beheerders, beveiligings beheerders en beveiligings lezers toevoegen als ontvangers.  

Als u het dialoog venster verwant wilt openen, klikt u op **waarschuwingen** in het gedeelte **instellingen** van de pagina **identiteits beveiliging** .

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/notifications/05.png)

## <a name="weekly-digest-email"></a>E-mail met wekelijkse samen vatting

Het wekelijkse overzichts bericht bevat een samen vatting van nieuwe risico detecties.  
Het bevat:

- Gebruikers die risico lopen
- Verdachte activiteiten
- Gedetecteerde beveiligings problemen
- Koppelingen naar de gerelateerde rapporten in identiteits beveiliging

    ![Herstel](./media/notifications/400.png "Herstel")

### <a name="configuration"></a>Configuratie

Als beheerder kunt u een wekelijks overzicht van e-mail verzenden.

![Gebruikers Risico's](./media/notifications/62.png "Gebruikers Risico's")

Om het gerelateerde dialoog venster te openen, klikt u op **wekelijkse samen vatting** in het gedeelte **instellingen** van de pagina **identiteits beveiliging** .

![Gebruikers met een risico voor een gedetecteerd e-mail bericht](./media/notifications/04.png)

## <a name="see-also"></a>Zie ook

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
