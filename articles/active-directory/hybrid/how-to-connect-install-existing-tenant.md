---
title: 'Azure AD Connect: wanneer u al Azure AD hebt | Microsoft Documenten'
description: In dit onderwerp wordt beschreven hoe u Connect gebruiken wanneer u een bestaande Azure AD-tenant hebt.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336822"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: wanneer u een bestaande tenant hebt
De meeste onderwerpen voor het gebruik van Azure AD Connect gaan ervan uit dat u begint met een nieuwe Azure AD-tenant en dat er geen gebruikers of andere objecten zijn. Maar als u bent begonnen met een Azure AD-tenant, deze hebt ingevuld met gebruikers en andere objecten en nu Verbinding wilt maken, dan is dit onderwerp iets voor u.

## <a name="the-basics"></a>De basisbeginselen
Een object in Azure AD wordt gemasterd in de cloud (Azure AD) of on-premises. Voor één object u bepaalde kenmerken niet on-premises en enkele andere kenmerken beheren in Azure AD. Elk object heeft een vlag die aangeeft waar het object wordt beheerd.

U sommige gebruikers on-premises en andere in de cloud beheren. Een veelvoorkomend scenario voor deze configuratie is een organisatie met een mix van boekhouders en verkoopmedewerkers. De boekhouders hebben een on-premises AD-account, maar de verkopers niet, ze hebben een account in Azure AD. U beheert sommige gebruikers on-premises en sommige in Azure AD.

Als u gebruikers in Azure AD bent gaan beheren die ook in on-premises AD zijn en later Connect willen gebruiken, zijn er nog enkele extra problemen waarmee u rekening moet houden.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchroniseren met bestaande gebruikers in Azure AD
Wanneer u Azure AD Connect installeert en u begint te synchroniseren, controleert de Azure AD-synchronisatieservice (in Azure AD) elk nieuw object en probeert u een bestaand object te vinden dat overeenkomt. Er zijn drie kenmerken die voor dit proces worden gebruikt: **userPrincipalName,** **proxyAddresses**en **sourceAnchor**/**immutableID**. Een overeenkomst op **userPrincipalName** en **proxyAddresses** staat bekend als een **soft match.** Een match op **sourceAnchor** staat bekend als **hard match.** Voor het kenmerk **proxyAddresses** wordt alleen de waarde met **SMTP:**, dat is het primaire e-mailadres, gebruikt voor de evaluatie.

De overeenkomst wordt alleen geëvalueerd voor nieuwe objecten die afkomstig zijn van Connect. Als u een bestaand object wijzigt zodat het overeenkomt met een van deze kenmerken, ziet u in plaats daarvan een fout.

Als Azure AD een object vindt waarvan de kenmerkwaarden hetzelfde zijn voor een object dat afkomstig is van Connect en dat het al aanwezig is in Azure AD, wordt het object in Azure AD overgenomen door Connect. Het voorheen door de cloud beheerde object wordt gemarkeerd als on-premises beheerd. Alle kenmerken in Azure AD met een waarde in on-premises AD worden overschreven met de on-premises waarde. De uitzondering is wanneer een kenmerk on-premises een **NULL-waarde** heeft. In dit geval blijft de waarde in Azure AD behouden, maar u deze nog steeds alleen on-premises wijzigen in iets anders.

> [!WARNING]
> Aangezien alle kenmerken in Azure AD worden overschreven door de on-premises waarde, moet u ervoor zorgen dat u goede gegevens on-premises hebt. Als u bijvoorbeeld alleen beheerde e-mailadressen in Office 365 hebt beheerd en niet up-premises AD DS hebt bijgewerkt, verliest u waarden in Azure AD/Office 365 die niet aanwezig zijn in AD DS.

> [!IMPORTANT]
> Als u wachtwoordsynchronisatie gebruikt, die altijd wordt gebruikt door expresinstellingen, wordt het wachtwoord in Azure AD overschreven met het wachtwoord in on-premises AD. Als uw gebruikers worden gebruikt om verschillende wachtwoorden te beheren, moet u hen informeren dat ze het on-premises wachtwoord moeten gebruiken wanneer u Connect hebt geïnstalleerd.

De vorige sectie en waarschuwing moet worden beschouwd in uw planning. Als u veel wijzigingen hebt aangebracht in Azure AD die niet zijn doorgevoerd in on-premises AD DS, moet u plannen hoe ad DS wordt ingevuld met de bijgewerkte waarden voordat u uw objecten synchroniseert met Azure AD Connect.

Als u uw objecten hebt gekoppeld aan een soft-match, wordt de **sourceAnchor** toegevoegd aan het object in Azure AD, zodat een harde overeenkomst later kan worden gebruikt.

>[!IMPORTANT]
> Microsoft raadt ten zeerste aan om on-premises accounts te synchroniseren met reeds bestaande beheerdersaccounts in Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Hard-match vs Soft-match
Voor een nieuwe installatie van Connect is er geen praktisch verschil tussen een soft- en een hard-match. Het verschil zit hem in een rampherstelsituatie. Als u uw server met Azure AD Connect hebt verloren, u een nieuw exemplaar opnieuw installeren zonder gegevens te verliezen. Een object met een sourceAnchor wordt tijdens de eerste installatie naar Connect verzonden. De overeenkomst kan vervolgens worden geëvalueerd door de client (Azure AD Connect), wat een stuk sneller is dan hetzelfde doen in Azure AD. Een harde overeenkomst wordt zowel door Connect als door Azure AD geëvalueerd. Een zachte overeenkomst wordt alleen geëvalueerd door Azure AD.

### <a name="other-objects-than-users"></a>Andere objecten dan gebruikers
Voor groepen en contactpersonen met e-mail u afstemmen op proxyAdressen. Hard-match is niet van toepassing omdat u alleen de sourceAnchor/immutableID (met PowerShell) alleen op Gebruikers bijwerken. Voor groepen die niet zijn ingeschakeld voor e-mail, is er momenteel geen ondersteuning voor soft-match of hard-match.

### <a name="admin-role-considerations"></a>Overwegingen voor beheerdersrollen
Om te voorkomen dat on-vertrouwde on-premises gebruikers overeenkomen met een cloudgebruiker met een beheerdersrol, koppelt Azure AD Connect niet on-premises gebruikersobjecten aan objecten met objecten met een beheerdersrol. Dit is standaard. Als u dit probleem wilt oplossen, u het volgende doen:

1.  Verwijder de maprollen uit het gebruikersobject alleen in de cloud.
2.  Als er een mislukte synchronisatiepoging van gebruikers is uitgevoerd, verwijdert u het in quarantaine geplaatste object in de cloud hard.
3.  Activeer een synchronisatie.
4.  Voeg eventueel de maprollen terug aan het gebruikersobject in de cloud zodra de matching heeft plaatsgevonden.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Een nieuwe on-premises Active Directory maken op basis van gegevens in Azure AD
Sommige klanten beginnen met een cloudoplossing met Azure AD en ze hebben geen on-premises AD. Later willen ze on-premises resources gebruiken en willen ze een on-premises AD bouwen op basis van Azure AD-gegevens. Azure AD Connect kan u niet helpen bij dit scenario. Het maakt geen on-premises gebruikers en het heeft geen mogelijkheid om het wachtwoord on-premises in te stellen op hetzelfde als in Azure AD.

Als de enige reden waarom u van plan bent om on-premises AD toe te voegen, is om LOB's (Line-of-Business-apps) te ondersteunen, moet u misschien overwegen om in plaats daarvan [Azure AD-domeinservices](../../active-directory-domain-services/index.yml) te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Lees meer over het [integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
