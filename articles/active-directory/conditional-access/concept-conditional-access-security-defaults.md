---
title: Azure Active Directory standaard instellingen voor beveiliging
description: Standaard beleid voor beveiliging dat is ontworpen om organisaties te beschermen tegen algemene aanvallen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453041"
---
# <a name="what-are-security-defaults"></a>Wat zijn de standaard beveiligings instellingen?

Het beheren van beveiliging kan lastig zijn wanneer algemene identiteits gerichte aanvallen, zoals wachtwoord spray, herhaling en phishing, steeds meer populair zijn. Het maken van een eenvoudigere manier om ervoor te zorgen dat uw organisatie beter beveiligd is tegen deze veelvoorkomende aanvallen is het doel van de standaard instellingen voor beveiliging in Azure Active Directory (AD). Met de standaard instellingen voor beveiliging kunt u uw organisatie beter beveiligen tegen veelvoorkomende aanvallen. De standaard instellingen voor beveiliging bevatten vooraf geconfigureerde beveiligings instellingen voor deze veelvoorkomende aanvallen. Micro soft maakt standaard beveiligings instellingen voor iedereen beschikbaar. Het doel is om ervoor te zorgen dat alle organisaties een basis niveau van beveiliging hebben ingeschakeld zonder extra kosten.

![Scherm afbeelding van de nieuwe standaard instellingen voor beveiliging UX](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
De volgende beveiligings configuraties worden ingeschakeld in uw Tenant. 

## <a name="unified-mfa-registration"></a>Unified MFA-registratie

Alle gebruikers in uw Tenant moeten registreren voor Azure Multi-Factor Authentication (MFA). Gebruikers kunnen zich 14 dagen registreren voor Azure MFA met behulp van de app Microsoft Authenticator. Nadat de 14 dagen zijn verstreken, kan de gebruiker zich pas aanmelden nadat de MFA-registratie is voltooid.

We begrijpen dat sommige gebruikers mogelijk niet op kantoor zijn en/of zich niet meer bij de 14 dagen bij het inschakelen van standaard instellingen voor de beveiliging aanmelden. Om ervoor te zorgen dat elke gebruiker over ruim voldoende tijd krijgt om zich te registreren voor MFA, is de periode van 14 dagen uniek voor elke gebruiker. De 14-daagse periode van een gebruiker begint na de eerste geslaagde interactieve aanmelding zodra de standaard instellingen voor beveiliging zijn ingeschakeld.

## <a name="mfa-enforcement"></a>MFA afdwingen

### <a name="protecting-administrators"></a>Beheerders beveiligen

Gebruikers met toegang tot geprivilegieerde accounts hebben meer toegang tot uw omgeving. Als gevolg van de kracht van deze accounts, moet u deze met speciale zorg behandelen. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van geprivilegieerde accounts is om een sterkere vorm van account verificatie te vereisen wanneer deze worden gebruikt om u aan te melden. In Azure Active Directory kunt u een sterkere account verificatie krijgen door multi-factor Authentication te vereisen.

Zodra MFA-registratie is voltooid, zijn de volgende negen Azure AD-beheerders rollen vereist om MFA elke keer dat ze zich aanmelden, uit te voeren.

- Globale beheerder
- SharePoint-beheerder
- Exchange-beheerder
- Beheerder van voorwaardelijke toegang
- Beveiligingsbeheerder
- Helpdesk beheerder/wachtwoord beheerder
- Factureringsbeheerder
- Gebruikers beheerder
- Verificatie beheerder

### <a name="protecting-all-users"></a>Alle gebruikers beveiligen

We denken meestal dat beheerders accounts de enige accounts zijn die moeten worden beveiligd met MFA (multi-factor Authentication). Beheerders hebben brede toegang tot gevoelige informatie en kunnen wijzigingen aanbrengen in instellingen voor het hele abonnement. Ongeldige Actors zijn echter vaak gericht op eind gebruikers. Na het verkrijgen van de toegang kunnen deze beschadigde actors namens de houder van het oorspronkelijke account toegang tot bevoegde informatie vragen of de volledige directory downloaden om een phishing-aanval uit te voeren op uw hele organisatie. Een gemeen schappelijke methode voor het verbeteren van de beveiliging van alle gebruikers is het vereisen van een sterkere vorm van account verificatie, zoals multi-factor Authentication (MFA) voor iedereen. Zodra MFA-registratie is voltooid, wordt de gebruiker gevraagd om MFA wanneer dat nodig is.

### <a name="blocking-legacy-authentication"></a>Verouderde verificatie blok keren

Om uw gebruikers eenvoudig toegang te geven tot uw Cloud-apps, ondersteunt Azure Active Directory (Azure AD) een breed scala aan verificatie protocollen, waaronder verouderde verificatie. Verouderde verificatie is een term die verwijst naar een verificatie aanvraag die wordt gedaan door:

- Oudere Office-clients die geen gebruik maken van moderne verificatie (bijvoorbeeld Office 2010 client)
- Elke client die gebruikmaakt van verouderde e-mail protocollen zoals IMAP/SMTP/POP3

De meeste pogingen om zich aan te melden, zijn tegenwoordig afkomstig van verouderde verificatie. Verouderde verificatie biedt geen ondersteuning voor multi-factor Authentication (MFA). Zelfs als er een MFA-beleid is ingeschakeld in uw directory, kan een ongeldige actor worden geverifieerd met behulp van een verouderd protocol en MFA overs Laan. Zodra de standaard instellingen voor beveiliging zijn ingeschakeld in uw Tenant, worden alle verificatie aanvragen die door een verouderd protocol worden uitgevoerd, geblokkeerd. Exchange ActiveSync wordt niet geblokkeerd door de standaard instellingen voor beveiliging.

### <a name="protecting-privileged-actions"></a>Beschermde acties beveiligen

Organisaties gebruiken verschillende Azure-Services die worden beheerd via de Azure Resource Manager-API, waaronder:

- Azure Portal 
- Azure PowerShell 
- Azure CLI

Het gebruik van Azure Resource Manager voor het beheren van uw services is een zeer geprivilegieerde actie. Azure Resource Manager kunt de configuratie van de Tenant breed wijzigen, zoals service-instellingen en abonnements facturering. Verificatie met één factor is kwetsbaar voor diverse aanvallen zoals phishing en wachtwoord spray. Daarom is het belang rijk om de identiteit te verifiëren van gebruikers die toegang willen hebben tot de configuraties van Azure Resource Manager en bij te werken door multi-factor Authentication te vereisen voordat toegang wordt toegestaan.

Zodra de standaard instellingen voor beveiliging zijn ingeschakeld in uw Tenant, is elke gebruiker die toegang tot de Azure Portal, Azure PowerShell of Azure CLI nodig heeft om multi-factor Authentication te volt ooien. Dit beleid is van toepassing op alle gebruikers die toegang hebben tot Azure Resource Manager, ongeacht of ze een beheerder of een gebruiker zijn. Als de gebruiker niet is geregistreerd voor MFA, moet de gebruiker zich registreren met behulp van de Microsoft Authenticator-app om door te gaan. Er wordt geen registratie periode van 14 dagen voor MFA gegeven.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Hier volgen enkele aanvullende overwegingen met betrekking tot de implementatie van de standaard beveiligings instellingen voor uw Tenant.

### <a name="legacy-protocols"></a>Verouderde protocollen

Verouderde verificatie protocollen (IMAP, SMTP, POP3, enz.) worden door e-mailclients gebruikt om verificatie aanvragen uit te voeren. Deze protocollen bieden geen ondersteuning voor MFA. De meeste schendingen van het account die door micro soft worden gezien, worden veroorzaakt door ongeldige Actors die aanvallen uitvoeren op verouderde protocollen, waardoor MFA wordt omzeild. Om ervoor te zorgen dat MFA vereist is wanneer u zich aanmeldt bij een beheerders account en beschadigde actors geen MFA kunnen negeren, worden in de standaard instellingen alle verificatie aanvragen voor beheerders accounts van verouderde protocollen geblokkeerd.

> [!WARNING]
> Voordat u deze instelling inschakelt, moet u ervoor zorgen dat uw beheerders geen verouderde verificatie protocollen gebruiken. Zie het artikel [verwijderen van verouderde verificatie](concept-conditional-access-block-legacy-authentication.md)voor meer informatie.

### <a name="conditional-access"></a>Voorwaardelijke toegang

Voorwaardelijke toegang kan worden gebruikt voor het configureren van beleids regels die hetzelfde gedrag bieden als ingeschakeld door de standaard instellingen van de beveiliging. Als u voorwaardelijke toegang gebruikt en beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, zijn de standaard instellingen voor beveiliging niet voor u beschikbaar. Als u een licentie hebt die voorwaardelijke toegang biedt, maar geen beleid voor voorwaardelijke toegang hebt ingeschakeld in uw omgeving, kunt u de standaard instellingen voor beveiliging gebruiken totdat u het CA-beleid inschakelt.

![Standaard instellingen voor beveiliging of voorwaardelijke toegang niet beide](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

Hier vindt u stapsgewijze hand leidingen over hoe voorwaardelijke toegang kan worden gebruikt voor het configureren van gelijkwaardige beleids regels:

- [MFA vereisen voor beheerders](howto-conditional-access-policy-admin-mfa.md)
- [MFA vereisen voor Azure-beheer](howto-conditional-access-policy-azure-management.md)
- [Verouderde verificatie blok keren](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>Standaard instellingen voor beveiliging inschakelen

Standaard instellingen voor beveiliging in uw Directory inschakelen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) AS een beveiligings beheerder, voorwaardelijke toegangs beheerder of globale beheerder.
1. Bladeren naar **Azure Active Directory**  @ no__t-2- **Eigenschappen**
1. Selecteer de **standaard instellingen voor beveiliging beheren**
1. Stel de **Schakel optie standaard instellingen inschakelen** in op **Ja**.
1. Klik op opslaan.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Wat is voorwaardelijke toegang?](overview.md)
