---
title: On-premises Azure AD-wachtwoordbeveiliging inschakelen
description: Meer informatie over het inschakelen van Azure AD Password Protection voor een on-premises Active Directory Domain Services-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: d00a8b84477226b68913f95c5121bbbdfc2eb09d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263814"
---
# <a name="enable-on-premises-azure-active-directory-password-protection"></a>On-premises Azure Active Directory Password Protection inschakelen

Gebruikers maken vaak wachtwoorden die veelvoorkomende lokale woorden gebruiken, zoals een school, sportteam of beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden, en zwak tegen woordenboek-gebaseerde aanvallen. Als u sterke wachtwoorden in uw organisatie wilt afdwingen, biedt Azure Active Directory (Azure AD) Wachtwoordbeveiliging een algemene en aangepaste lijst met geblokkeerde wachtwoorden. Een verzoek om wachtwoordwijziging mislukt als er een overeenkomst is in deze lijst met verboden wachtwoorden.

Als u uw on-premises ACTIVE Directory Domain Services-omgeving (AD DS) wilt beveiligen, u Azure AD-wachtwoordbeveiliging installeren en configureren om te werken met uw on-prem DC. In dit artikel ziet u hoe u Azure AD Password Protection inschakelt voor uw on-premises omgeving.

Zie [Azure AD Password Protection for Se](concept-password-ban-bad-on-premises.md)in n.P. voor meer informatie over de werking van Azure AD Password Protection in een on-premises omgeving.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel ziet u hoe u Azure AD Password Protection inschakelt voor uw on-premises omgeving. Installeer en registreer [de proxyservice Azure AD Password Protection en DC-agents](howto-password-ban-bad-on-premises-deploy.md) voordat u dit artikel voltooit in uw on-premises AD DS-omgeving.

## <a name="enable-on-premises-password-protection"></a>On-premises wachtwoordbeveiliging inschakelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) en blader naar **Azure Active Directory** > **Security** > **Authentication-methoden** > **Wachtwoordbeveiliging**.
1. Stel de optie in voor **Wachtwoordbeveiliging inschakelen in Windows Server Active Directory** op *Ja*.

    Wanneer deze instelling is ingesteld op *Nee,* gaan alle geïmplementeerde Azure AD Password Protection DC-agents in een rustige modus waarin alle wachtwoorden worden geaccepteerd als-is. Er worden geen validatieactiviteiten uitgevoerd en er worden geen controlegebeurtenissen gegenereerd.

1. Het wordt aanbevolen om in eerste instantie de **modus in** te stellen op *Audit*. Nadat u vertrouwd bent met de functie en de impact op gebruikers in uw organisatie, u de **modus** overschakelen naar *Afgedwongen*. Zie voor meer informatie het volgende gedeelte over [de werkingsmodi](#modes-of-operation).
1. Wanneer u klaar bent, selecteert **u Opslaan**.

    [![](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords-cropped.png "Enable on-premises password protection under Authentication Methods in the Azure portal")](media/howto-password-ban-bad-on-premises-operations/enable-configure-custom-banned-passwords.png#lightbox)

## <a name="modes-of-operation"></a>Bedrijfsmodi

Wanneer u on-premises Azure AD Password Protection inschakelt, u *de controlemodus* of *de uitvoermodus* gebruiken. We raden aan dat de eerste implementatie en testen altijd beginnen in de auditmodus. Vermeldingen in het gebeurtenislogboek moeten vervolgens worden gecontroleerd om te anticiperen op de vraag of bestaande operationele processen zouden worden verstoord zodra *de uitvoermodus* is ingeschakeld.

### <a name="audit-mode"></a>Controlemodus

*Audit* modus is bedoeld als een manier om de software uit te voeren in een "wat als" modus. Elke Azure AD Password Protection DC-agentservice evalueert een binnenkomend wachtwoord volgens het momenteel actieve beleid.

Als het huidige beleid is geconfigureerd om in de controlemodus te zijn, resulteren 'slechte' wachtwoorden in gebeurtenislogboekberichten, maar worden ze verwerkt en bijgewerkt. Dit gedrag is het enige verschil tussen audit en de afdwingmodus. Alle andere bewerkingen draaien hetzelfde.

### <a name="enforced-mode"></a>Afgedwongen modus

*Afgedwongen* modus is bedoeld als de uiteindelijke configuratie. Net als in de controlemodus evalueert elke Azure AD Password Protection DC-agentservice binnenkomende wachtwoorden volgens het momenteel actieve beleid. Wanneer de afgedwongen modus is ingeschakeld, wordt een wachtwoord dat volgens het beleid als onveilig wordt beschouwd, geweigerd.

Wanneer een wachtwoord wordt geweigerd in de afgedwongen modus door de Azure AD Password Protection DC-agent, ziet een eindgebruiker een vergelijkbare fout zoals hij of zij zou zien of zijn wachtwoord is geweigerd door traditionele on-premises wachtwoordcomplexiteitshandhaving. Een gebruiker kan bijvoorbeeld het volgende traditionele foutbericht zien in het windows-aanmeldingsscherm of het wachtwoordscherm wijzigen:

*"Kan het wachtwoord niet bijwerken. De waarde voor het nieuwe wachtwoord voldoet niet aan de lengte, complexiteit of geschiedenisvereisten van het domein."*

Dit bericht is slechts een voorbeeld van verschillende mogelijke uitkomsten. De specifieke foutmelding kan variëren afhankelijk van de werkelijke software of het scenario dat een onveilig wachtwoord probeert in te stellen.

Getroffen eindgebruikers moeten mogelijk samenwerken met hun IT-personeel om de nieuwe vereisten te begrijpen en veilige wachtwoorden te kiezen.

> [!NOTE]
> Azure AD Password Protection heeft geen controle over het specifieke foutbericht dat door de clientmachine wordt weergegeven wanneer een zwak wachtwoord wordt geweigerd.

## <a name="next-steps"></a>Volgende stappen

Zie De lijst met aangepaste verboden [wachtwoordvoor](tutorial-configure-custom-password-protection.md)azure AD Password Protection configureren als u de lijst met verboden wachtwoorden voor uw organisatie wilt aanpassen.

Zie [On-prem Azure AD Password Protection controleren op de prem.](howto-password-ban-bad-on-premises-monitor.md)
