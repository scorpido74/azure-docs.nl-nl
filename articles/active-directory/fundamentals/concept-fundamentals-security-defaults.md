---
title: Azure Active Directory-beveiligingsstandaarden
description: Standaardbeleid voor beveiliging waarmee organisaties kunnen worden beschermd tegen veelvoorkomende aanvallen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248850"
---
# <a name="what-are-security-defaults"></a>Wat zijn beveiligingsstandaards?

Het beheren van beveiliging kan moeilijk zijn wanneer veelvoorkomende identiteitsgerelateerde aanvallen steeds populairder worden. Deze aanvallen omvatten wachtwoord spray, replay, en phishing.

Beveiligingsstandaarden in Azure Active Directory (Azure AD) maken het eenvoudiger om veilig te zijn en uw organisatie te beschermen. Beveiligingsstandaardinstellingen bevatten vooraf geconfigureerde beveiligingsinstellingen voor veelvoorkomende aanvallen. 

Microsoft maakt beveiligingsstandaards beschikbaar voor iedereen. Het doel is ervoor te zorgen dat alle organisaties een basisniveau van beveiliging hebben ingeschakeld zonder extra kosten. U schakelt beveiligingsstandaards in de Azure-portal in.

![Schermafbeelding van de Azure-portal met de schakeloptie om beveiligingsstandaardinstellingen in te schakelen](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> Als uw tenant is gemaakt op of na 22 oktober 2019, is het mogelijk dat u het nieuwe beveiligingsstandaardgedrag ondervindt en al beveiligingsstandaardinstellingen hebt ingeschakeld in uw tenant. In een poging om al onze gebruikers te beschermen, wordt beveiligingsstandaarden uitgerold naar alle nieuwe tenants die zijn gemaakt.

Meer details over waarom beveiligingsstandaards beschikbaar worden gesteld, vindt u in de blogpost van Alex Weinert, [Invoering van beveiligingsstandaards.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)

## <a name="unified-multi-factor-authentication-registration"></a>Unified Multi-Factor Authentication registratie

Alle gebruikers in uw tenant moeten zich registreren voor multi-factor authenticatie (MFA) in de vorm van de Azure Multi-Factor Authentication-service. Gebruikers hebben 14 dagen de tijd om zich te registreren voor Multi-Factor Authentication met behulp van de Microsoft Authenticator-app. Nadat de 14 dagen zijn verstreken, kan de gebruiker zich pas aanmelden nadat de registratie van multifactorauthenticatie is voltooid.

We begrijpen dat sommige gebruikers mogelijk niet op kantoor zijn of zich gedurende de 14 dagen niet zullen aanmelden nadat ze beveiligingsstandaards hebben ingeschakeld. Om ervoor te zorgen dat elke gebruiker voldoende tijd heeft om zich te registreren voor Multi-Factor Authentication, is de periode van 14 dagen uniek voor elke gebruiker. De periode van 14 dagen van een gebruiker begint nadat de eerste succesvolle interactieve aanmelding is gestart nadat u beveiligingsstandaardinstellingen hebt ingeschakeld.

## <a name="multi-factor-authentication-enforcement"></a>Handhaving van meervoudige verificatie

### <a name="protecting-administrators"></a>Beheerders beveiligen

Gebruikers met toegang tot geprivilegieerde accounts hebben meer toegang tot uw omgeving. Vanwege de kracht die deze accounts hebben, moet je ze met speciale zorg behandelen. Een veelgebruikte methode om de bescherming van bevoorrechte accounts te verbeteren, is een sterkere vorm van accountverificatie voor aanmelding vereisen. In Azure AD u een sterkere accountverificatie krijgen door multifactorverificatie te vereisen.

Nadat de registratie met multifactorverificatie is voltooid, moeten de volgende negen Azure AD-beheerdersrollen worden uitgevoerd elke keer dat ze zich aanmelden extra verificatie uitvoeren:

- Globale beheerder
- SharePoint-beheerder
- Exchange-beheerder
- Beheerder van voorwaardelijke toegang
- Beveiligingsbeheerder
- Helpdeskbeheerder of wachtwoordbeheerder
- Factureringsbeheerder
- Gebruikersbeheerder
- Verificatiebeheerder

### <a name="protecting-all-users"></a>Alle gebruikers beschermen

We hebben de neiging om te denken dat beheerdersaccounts de enige accounts zijn die extra verificatielagen nodig hebben. Beheerders hebben brede toegang tot gevoelige informatie en kunnen wijzigingen aanbrengen in de instellingen voor het hele abonnement. Maar aanvallers hebben de neiging om eindgebruikers te richten. 

Nadat deze aanvallers toegang hebben verkregen, kunnen ze namens de oorspronkelijke accounthouder toegang vragen tot vertrouwelijke informatie. Ze kunnen zelfs de hele directory downloaden om een phishing-aanval uit te voeren op uw hele organisatie. 

Een veelgebruikte methode om de bescherming van alle gebruikers te verbeteren, is om voor iedereen een sterkere vorm van accountverificatie, zoals multifactorverificatie, te vereisen. Nadat gebruikers de registratie van multifactorauthenticatie hebben voltooid, wordt ze gevraagd om extra verificatie wanneer dat nodig is.

### <a name="blocking-legacy-authentication"></a>Verouderde verificatie blokkeren

Azure AD ondersteunt verschillende verificatieprotocollen, waaronder legacy-verificatie, om uw gebruikers eenvoudig toegang te geven tot uw cloud-apps. *Legacy-verificatie* is een term die verwijst naar een verificatieverzoek van:

- Clients die geen moderne verificatie gebruiken (bijvoorbeeld een Office 2010-client).
- Elke client die oudere e-mailprotocollen gebruikt, zoals IMAP, SMTP of POP3.

Vandaag de dag zijn de meeste compromitterende aanmeldingspogingen afkomstig van verouderde verificatie. Verouderde verificatie biedt geen ondersteuning voor Multi-Factor Authentication. Zelfs als u een multi-factor authenticatiebeleid hebt ingeschakeld in uw directory, kan een aanvaller zich verifiëren met behulp van een ouder protocol en multifactorverificatie omzeilen. 

Nadat beveiligingsstandaards zijn ingeschakeld in uw tenant, worden alle verificatieaanvragen van een ouder protocol geblokkeerd. Beveiligingsstandaardinstellingen blokkeren exchange Active Sync-basisverificatie.

> [!WARNING]
> Voordat u beveiligingsstandaardinstellingen inschakelt, controleert u of uw beheerders geen oudere verificatieprotocollen gebruiken. Zie [Afstappen van verouderde verificatie](concept-fundamentals-block-legacy-authentication.md)voor meer informatie.

### <a name="protecting-privileged-actions"></a>Bescherming van bevoorrechte acties

Organisaties maken gebruik van verschillende Azure-services die worden beheerd via de Azure Resource Manager API, waaronder:

- Azure Portal 
- Azure PowerShell 
- Azure-CLI

Het gebruik van Azure Resource Manager om uw services te beheren is een zeer bevoorrechte actie. Azure Resource Manager kan tenantbrede configuraties wijzigen, zoals service-instellingen en facturering met abonnementen. Single-factor authenticatie is kwetsbaar voor een verscheidenheid van aanvallen zoals phishing en password spray. 

Het is belangrijk om de identiteit te verifiëren van gebruikers die toegang willen krijgen tot Azure Resource Manager en configuraties willen bijwerken. U verifieert hun identiteit door extra verificatie te vereisen voordat u toegang toestaat.

Nadat u beveiligingsstandaarden in uw tenant hebt ingeschakeld, moet elke gebruiker die toegang heeft tot de Azure-portal, Azure PowerShell of de Azure CLI, extra verificatie voltooien. Dit beleid is van toepassing op alle gebruikers die toegang hebben tot Azure Resource Manager, of ze nu een beheerder of een gebruiker zijn. 

Als de gebruiker niet is geregistreerd voor multifactorauthenticatie, moet de gebruiker zich registreren met behulp van de Microsoft Authenticator-app om verder te gaan. Er wordt geen registratieperiode van 14 dagen voor multifactorauthenticatie verstrekt.

> [!NOTE]
> Exchange Online-tenants van voor 2017 hebben standaard moderne verificatie uitgeschakeld. Om de mogelijkheid van een inloglus te vermijden terwijl u deze tenants authenticeert, moet u [moderne verificatie inschakelen.](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)

> [!NOTE]
> Het Azure AD Connect-synchronisatieaccount is uitgesloten van beveiligingsstandaarden en wordt niet gevraagd om zich te registreren voor of multifactorauthenticatie uit te voeren. Organisaties mogen dit account niet voor andere doeleinden gebruiken.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

De volgende aanvullende overwegingen hebben betrekking op de implementatie van beveiligingsstandaards voor uw tenant.

### <a name="authentication-methods"></a>Verificatiemethoden

Beveiligingsstandaardinstellingen staan registratie en gebruik van Azure Multi-Factor Authentication toe **met alleen de Microsoft Authenticator-app met behulp van meldingen.** Met Voorwaardelijke toegang kan elke verificatiemethode worden gebruikt die de beheerder inschakelt.

|   | Standaardinstellingen voor de beveiliging | Voorwaardelijke toegang |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatiecode van mobiele app of hardwaretoken |   | X |
| Sms-bericht naar telefoon |   | X |
| Bellen naar de telefoon |   | X |
| App-wachtwoorden |   | X** |

** App-wachtwoorden zijn alleen beschikbaar in MFA per gebruiker met verouderde verificatiescenario's als deze zijn ingeschakeld door beheerders.

### <a name="conditional-access"></a>Voorwaardelijke toegang

U Voorwaardelijke toegang gebruiken om beleidsregels te configureren die vergelijkbaar zijn met beveiligingsstandaards, maar met meer granulariteit, waaronder gebruikersuitsluitingen, die niet beschikbaar zijn in beveiligingsstandaardinstellingen. Als u voorwaardelijke toegang gebruikt en het beleid voor voorwaardelijke toegang in uw omgeving hebt ingeschakeld, zijn beveiligingsstandaardinstellingen niet voor u beschikbaar. Als u een licentie hebt die voorwaardelijke toegang biedt, maar geen beleid voor voorwaardelijke toegang in uw omgeving hebt ingeschakeld, u beveiligingsstandaards gebruiken totdat u beleid voor voorwaardelijke toegang inschakelt. Meer informatie over Azure AD-licenties vindt u op de [prijspagina van Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

![Waarschuwing dat u beveiligingsstandaards of voorwaardelijke toegang niet beide hebben](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

Hier volgen stapsgewijze handleidingen over hoe u Voorwaardelijke toegang gebruiken om gelijkwaardige beleidsregels te configureren:

- [MFA vereisen voor beheerders](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [MFA vereisen voor Azure-beheer](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [Verouderde verificatie blokkeren](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [MFA vereisen voor alle gebruikers](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [Azure MFA-registratie vereisen](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - Vereist Azure AD-identiteitsbescherming

## <a name="enabling-security-defaults"></a>Beveiligingsstandaardinstellingen inschakelen

Ga als bedoeld als u beveiligingsstandaards in uw map inschakelt:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beveiligingsbeheerder, beheerder van voorwaardelijke toegang of globale beheerder.
1. Blader naar Azure > **Active** **Directory-eigenschappen**.
1. Selecteer **Beveiligingsstandaardinstellingen beheren**.
1. Stel de **instelling voor beveiligingsstandaardinstellingen** in op **Ja**.
1. Selecteer **Opslaan**.

## <a name="disabling-security-defaults"></a>Beveiligingsstandaardinstellingen uitschakelen

Organisaties die ervoor kiezen om beleid voor voorwaardelijke toegang te implementeren dat beveiligingsstandaardinstellingen vervangt, moeten beveiligingsstandaardinstellingen uitschakelen. 

![Waarschuwingsbericht schakelt beveiligingsstandaards uit om voorwaardelijke toegang in te schakelen](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

Ga als een overzicht van beveiligingsstandaards in uw map:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beveiligingsbeheerder, beheerder van voorwaardelijke toegang of globale beheerder.
1. Blader naar Azure > **Active** **Directory-eigenschappen**.
1. Selecteer **Beveiligingsstandaardinstellingen beheren**.
1. Stel de **instelling voor beveiligingsstandaardinstellingen** in op **Nee**.
1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md)
