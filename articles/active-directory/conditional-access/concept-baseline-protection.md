---
title: Basislijnbeleid voor voorwaardelijke toegang - Azure Active Directory
description: Beleid voor voorwaardelijke toegang basislijn om organisaties te beschermen tegen veelvoorkomende aanvallen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55de5a5c604273225a85e49ca682980f83a951d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75767565"
---
# <a name="what-are-baseline-policies"></a>Wat zijn basislijnbeleid?

Basislijnbeleid is een set vooraf gedefinieerde beleidsregels die organisaties helpen beschermen tegen veelvoorkomende aanvallen. Deze veelvoorkomende aanvallen kunnen wachtwoordspray, replay en phishing omvatten. Basislijnbeleid is beschikbaar in alle edities van Azure AD. Microsoft maakt deze basislijnbeveiligingsbeleidsregels voor iedereen beschikbaar omdat identiteitsaanvallen de afgelopen jaren in opkomst zijn geweest. Het doel van deze vier beleidsregels is ervoor te zorgen dat alle organisaties een basislijnniveau van beveiliging hebben ingeschakeld zonder extra kosten.

Voor het beheren van aangepaste voorwaardentoegangsbeleidsregels is een Azure AD Premium-licentie vereist.

> [!IMPORTANT]
> Basislijnbeleid wordt afgeschaft. Zie [Nieuwe informatie in Azure Active Directory?](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>Basislijnbeleid

![Basislijnbeleid voor voorwaardelijke toegang in de Azure-portal](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

Er zijn vier basislijnbeleidsregels:

* MFA vereisen voor beheerders (voorbeeld)
* Bescherming van eindgebruikers (voorbeeld)
* Oudere verificatie blokkeren (voorbeeld)
* MFA vereisen voor servicebeheer (voorbeeld)

Al deze vier beleidsregels hebben invloed op verouderde verificatiestromen zoals POP, IMAP en oudere Office-desktopclients.

### <a name="exclusions"></a>Uitsluitingen

Toen het basislijnbeleid in hun eerste openbare voorbeeld ging, was er een optie om gebruikers uit te sluiten van het beleid. Deze mogelijkheid evolueerde via de preview en werd in juli 2019 verwijderd. Organisaties die al uitsluitingen hadden gemaakt, konden doorgaan met het behouden van nieuwe gebruikers, maar konden geen uitsluitingen toevoegen aan het beleid.

### <a name="require-mfa-for-admins-preview"></a>MFA vereisen voor beheerders (voorbeeld)

Vanwege de kracht en toegang die beheerdersaccounts hebben, moet u ze met speciale zorg behandelen. Een veelgebruikte methode om de bescherming van bevoegde accounts te verbeteren, is een sterkere vorm van accountverificatie vereisen wanneer ze worden gebruikt om in te loggen. In Azure Active Directory u een sterkere accountverificatie krijgen door beheerders te verplichten zich te registreren voor Azure Multi-Factor Authentication.

Mfa vereisen voor beheerders (preview) is een basislijnbeleid waarvoor multi-factor authenticatie (MFA) vereist is voor de volgende directoryrollen, beschouwd als de meest bevoorrechte Azure AD-rollen:

* Globale beheerder
* SharePoint-beheerder
* Exchange-beheerder
* Beheerder van voorwaardelijke toegang
* Beveiligingsbeheerder
* Helpdeskbeheerder / Wachtwoordbeheerder
* Factureringsbeheerder
* Gebruikersbeheerder

Als uw organisatie deze accounts in scripts of code heeft gebruikt, u overwegen deze te vervangen door [beheerde identiteiten.](../managed-identities-azure-resources/overview.md)

### <a name="end-user-protection-preview"></a>Bescherming van eindgebruikers (voorbeeld)

Beheerders met hoge bevoorrechten zijn niet de enigen die het doelwit zijn van aanvallen. Slechte acteurs hebben de neiging om normale gebruikers te richten. Na het verkrijgen van toegang, kunnen deze slechte acteurs toegang vragen tot vertrouwelijke informatie namens de oorspronkelijke accounthouder of download de hele directory en het uitvoeren van een phishing-aanval op uw hele organisatie. Een veelgebruikte methode om de bescherming voor alle gebruikers te verbeteren, is het vereisen van een sterkere vorm van accountverificatie wanneer een riskante aanmelding wordt gedetecteerd.

**Eindgebruikersbescherming (voorbeeld)** is een basislijnbeleid dat alle gebruikers in een map beschermt. Als u dit beleid inschakelt, moeten alle gebruikers zich binnen 14 dagen registreren voor Azure Multi-Factor Authentication. Eenmaal geregistreerd, zullen gebruikers alleen worden gevraagd voor MFA tijdens riskante aanmeldingspogingen. Gecompromitteerde gebruikersaccounts worden geblokkeerd totdat het wachtwoord opnieuw wordt ingesteld en het risico wordt genomen om te worden ontslagen. 

> [!NOTE]
> Alle gebruikers die eerder voor risico's zijn gemarkeerd, worden geblokkeerd totdat het wachtwoord wordt gereset en het risico loopt te worden ontslagen bij activering van het beleid.

### <a name="block-legacy-authentication-preview"></a>Oudere verificatie blokkeren (voorbeeld)

Verouderde verificatieprotocollen (bijvoorbeeld IMAP, SMTP, POP3) zijn protocollen die normaal gesproken door oudere e-mailclients worden gebruikt om te verifiëren. Oudere protocollen ondersteunen geen multi-factor authenticatie. Zelfs als u een beleid hebt dat multi-factor authenticatie voor uw directory vereist, kan een slechte actor zich verifiëren met behulp van een van deze verouderde protocollen en multi-factor authenticatie omzeilen.

De beste manier om uw account te beschermen tegen kwaadaardige verificatieverzoeken die door oudere protocollen worden gedaan, is door ze te blokkeren.

Het basislijnbeleid **voor verouderde verificatie (voorbeeld)** blokkeert verificatieaanvragen die zijn uitgevoerd met oudere protocollen. Moderne verificatie moet worden gebruikt om met succes in te loggen voor alle gebruikers. In combinatie met het andere basislijnbeleid worden aanvragen afkomstig van verouderde protocollen geblokkeerd. Bovendien worden alle gebruikers verplicht om MFA wanneer nodig. Dit beleid blokkeert Exchange ActiveSync niet.

### <a name="require-mfa-for-service-management-preview"></a>MFA vereisen voor servicebeheer (voorbeeld)

Organisaties gebruiken verschillende Azure-services en beheren deze vanuit azure resource beheergebaseerde hulpprogramma's zoals:

* Azure Portal
* Azure PowerShell
* Azure-CLI

Het gebruik van een van deze tools om resourcebeheer uit te voeren is een zeer bevoorrechte actie. Deze hulpprogramma's kunnen configuraties voor het hele abonnement wijzigen, zoals service-instellingen en facturering via abonnementen.

Om bevoorrechte acties te beschermen, vereist **dit MFA vereisen voor servicebeheer (preview)-beleid** multifactorauthenticatie voor elke gebruiker die toegang heeft tot Azure-portal, Azure PowerShell of Azure CLI.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie:

* [Beveiligingsstandaardinstellingen inschakelen](../fundamentals/concept-fundamentals-security-defaults.md)
* [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
* [Vijf stappen om uw identiteitsinfrastructuur te beveiligen](../../security/fundamentals/steps-secure-identity.md)
