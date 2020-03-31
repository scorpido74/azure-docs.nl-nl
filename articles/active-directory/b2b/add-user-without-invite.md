---
title: B2B-gasten toevoegen zonder uitnodigingskoppeling of e-mail - Azure AD
description: U een gastgebruiker andere gastgebruikers aan uw Azure AD laten toevoegen zonder een uitnodiging in te wisselen in Azure Active Directory B2B-samenwerking.
documentationcenter: ''
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c9caaf581fab37e2e55cbe408db6d6d846622a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050896"
---
# <a name="add-b2b-collaboration-guest-users-without-an-invitation-link-or-email"></a>Gastgebruikers van B2B-samenwerking toevoegen zonder uitnodigingskoppeling of e-mail

U nu gastgebruikers uitnodigen door een directe link naar een gedeelde app te sturen. Met deze methode hoeven gastgebruikers de uitnodigingse-mail niet meer te gebruiken, behalve in sommige speciale gevallen. Een gastgebruiker klikt op de app-koppeling, controleert en accepteert de privacyvoorwaarden en heeft vervolgens naadloos toegang tot de app. Zie [B2B-uitnodigingsuitnodigingen voor Samenwerking voor](redemption-experience.md)meer informatie .   

Voordat deze nieuwe methode beschikbaar was, u gastgebruikers uitnodigen zonder dat u de uitnodigingse-mail nodig hebt door een genodigder (van uw organisatie of van een partnerorganisatie) toe te voegen aan de **directoryrol gastgenodigden** en vervolgens gastgebruikers toe te laten voegen aan de directory, groepen of toepassingen via de gebruikersinterface of via PowerShell. (Als u PowerShell gebruikt, u de uitnodigingse-mail helemaal onderdrukken). Bijvoorbeeld:

1. Een gebruiker in de hostorganisatie (bijvoorbeeld WoodGrove) nodigt één gebruiker Sam@litware.comvan de partnerorganisatie (bijvoorbeeld) uit als gast.
2. De beheerder in de hostorganisatie [stelt beleid in](delegate-invitations.md) waarmee Sam andere gebruikers uit de partnerorganisatie (Litware) kan identificeren en toevoegen. (Sam moet worden toegevoegd aan de rol **gastgenodigden.)**
3. Sam kan nu andere gebruikers van Litware toevoegen aan de WoodGrove-map, groepen of toepassingen zonder dat uitnodigingen hoeven te worden ingewisseld. Als Sam de juiste opsommingsrechten heeft in Litware, gebeurt dit automatisch.
 
Deze originele methode werkt nog steeds. Er is echter een klein verschil in gedrag. Als u PowerShell gebruikt, ziet u dat een gastaccount in behandeling nu een **status in behandeling** heeft in plaats van geaccepteerd onmiddellijk weer te **geven.** Hoewel de status in behandeling is, kan de gastgebruiker zich nog steeds aanmelden en toegang krijgen tot de app zonder op een koppeling met e-mailuitnodigingen te klikken. De status in behandeling betekent dat de gebruiker de [toestemmingservaring](redemption-experience.md#consent-experience-for-the-guest)nog niet heeft doorlopen, waar hij de privacyvoorwaarden van de uitnodigende organisatie accepteert. De gastgebruiker ziet dit toestemmingsscherm wanneer hij zich voor de eerste keer aanmeldt. 

Als u een gebruiker uitnodigt voor de directory, moet de gastgebruiker https://portal.azure.com/rechtstreeks toegang krijgen tot de URL van de brontenant-specifieke Azure-portal (zoals *resourcetenant*.onmicrosoft.com) om de privacyvoorwaarden weer te geven en ermee in te stemmen.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure AD B2B-samenwerking?](what-is-b2b.md)
- [B2B-uitnodigingsuitnodiging en inwisseling](redemption-experience.md)
- [Uitnodigingen delegeren voor B2B-samenwerking in Azure Active Directory](delegate-invitations.md)
- [Hoe voegen informatiewerkers B2B-samenwerkingsgebruikers toe?](add-users-information-worker.md)

