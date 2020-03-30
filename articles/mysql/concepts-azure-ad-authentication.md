---
title: Active Directory-verificatie - Azure Database voor MySQL
description: Meer informatie over de concepten van Azure Active Directory voor verificatie met Azure Database voor MySQL
author: lfittl-msft
ms.author: lufittl
ms.service: mysql
ms.topic: conceptual
ms.date: 01/22/2019
ms.openlocfilehash: 960536c3f80aa7870d6f2056d8e95cd1a4338dfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79299019"
---
# <a name="use-azure-active-directory-for-authenticating-with-mysql"></a>Azure Active Directory gebruiken om te authenticeren met MySQL

Azure AD-verificatie (Microsoft Azure Active Directory) is een mechanisme om verbinding te maken met Azure Database voor MySQL met behulp van identiteiten die zijn gedefinieerd in Azure AD.
Met Azure AD-verificatie u databasegebruikersidentiteiten en andere Microsoft-services op een centrale locatie beheren, wat het beheer van machtigingen vereenvoudigt.

> [!IMPORTANT]
> Azure AD-verificatie voor Azure Database voor MySQL is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Voordelen van het gebruik van Azure AD zijn:

- Verificatie van gebruikers in Azure Services op een uniforme manier
- Beheer van wachtwoordbeleid en wachtwoordrotatie op één plaats
- Meerdere vormen van verificatie die worden ondersteund door Azure Active Directory, waardoor wachtwoorden niet meer hoeven op te slaan
- Klanten kunnen databasemachtigingen beheren met externe (Azure AD)-groepen.
- Azure AD-verificatie maakt gebruik van MySQL-databasegebruikers om identiteiten op databaseniveau te verifiëren
- Ondersteuning voor tokenverificatie voor toepassingen die verbinding maken met Azure Database voor MySQL

Als u Azure Active Directory-verificatie wilt configureren en gebruiken, gebruikt u het volgende proces:

1. Maak en vul Azure Active Directory indien nodig met gebruikersidentiteiten.
2. Eventueel de Active Directory koppelen of wijzigen die momenteel is gekoppeld aan uw Azure-abonnement.
3. Maak een Azure AD-beheerder voor de Azure Database voor MySQL-server.
4. Maak databasegebruikers in uw database die zijn toegewezen aan Azure AD-identiteiten.
5. Maak verbinding met uw database door een token op te halen voor een Azure AD-identiteit en u in te loggen.

> [!NOTE]
> Zie [Configureren en aanmelden bij Azure AD voor Azure Database voor MySQL](howto-configure-sign-in-azure-ad-authentication.md)voor meer informatie over het maken en invullen van Azure AD en vervolgens Azure AD configureren met Azure Database voor MySQL.

## <a name="architecture"></a>Architectuur

In het volgende diagram op hoog niveau wordt samengevat hoe verificatie werkt met Azure AD-verificatie met Azure Database voor MySQL. De pijlen geven communicatietrajecten aan.

![verificatiestroom][1]

## <a name="administrator-structure"></a>Beheerdersstructuur

Bij het gebruik van Azure AD-verificatie zijn er twee administratoraccounts voor de MySQL-server. de oorspronkelijke MySQL-beheerder en de Azure AD-beheerder. Alleen de beheerder op basis van een Azure AD-account kan de eerste Azure AD-databasegebruiker maken in een gebruikersdatabase. De aanmelding van Azure AD-beheerders kan een Azure AD-gebruiker of een Azure AD-groep zijn. Wanneer de beheerder een groepsaccount is, kan het door elk groepslid worden gebruikt, waardoor meerdere Azure AD-beheerders voor de MySQL-server worden inschakelt. Het gebruik van een groepsaccount als beheerder verbetert de beheerbaarheid doordat u groepsleden centraal toevoegen en verwijderen in Azure AD zonder de gebruikers of machtigingen in de MySQL-server te wijzigen. Slechts één Azure AD-beheerder (een gebruiker of groep) kan op elk gewenst moment worden geconfigureerd.

![beheerstructuur][2]

## <a name="permissions"></a>Machtigingen

Als u nieuwe gebruikers wilt maken die kunnen verifiëren met Azure AD, moet u de ontworpen Azure AD-beheerder zijn. Deze gebruiker wordt toegewezen door het Azure AD Administrator-account te configureren voor een specifieke Azure-database voor MySQL-server.

Als u een nieuwe Azure AD-databasegebruiker wilt maken, moet u verbinding maken als Azure AD-beheerder. Dit wordt gedemonstreerd in [Configureren en inloggen met Azure AD voor Azure Database voor MySQL.](howto-configure-sign-in-azure-ad-authentication.md)

Azure AD-verificatie is alleen mogelijk als de Azure AD-beheerder is gemaakt voor Azure Database voor MySQL. Als de Azure Active Directory-beheerder van de server is verwijderd, kunnen bestaande Azure Active Directory-gebruikers die eerder zijn gemaakt, geen verbinding meer maken met de database met behulp van hun Azure Active Directory-referenties.

## <a name="connecting-using-azure-ad-identities"></a>Verbinding maken met Azure AD-identiteiten

Azure Active Directory-verificatie ondersteunt de volgende methoden om verbinding te maken met een database met Azure AD-identiteiten:

- Azure Active Directory-wachtwoord
- Azure Active Directory geïntegreerd
- Azure Active Directory Universal met MFA
- Active Directory-toepassingscertificaten of clientgeheimen gebruiken

Zodra u zich hebt geverifieerd ten opzichte van de Active Directory, haalt u vervolgens een token op. Dit token is uw wachtwoord voor het inloggen.

> [!NOTE]
> Zie Configureren en aanmelden bij Azure AD [voor Azure Database voor MySQL voor](howto-configure-sign-in-azure-ad-authentication.md)meer informatie over hoe u verbinding maken met een Active Directory-token.

## <a name="additional-considerations"></a>Aanvullende overwegingen

- Er kan op elk gewenst moment slechts één Azure AD-beheerder worden geconfigureerd voor een Azure-database voor MySQL-server.
- Alleen een Azure AD-beheerder voor MySQL kan in eerste instantie verbinding maken met de Azure Database voor MySQL met behulp van een Azure Active Directory-account. De Active Directory-beheerder kan volgende Azure AD-databasegebruikers configureren.
- Als een gebruiker uit Azure AD wordt verwijderd, kan die gebruiker niet meer verifiëren met Azure AD en is het daarom niet langer mogelijk om een toegangstoken voor die gebruiker te verkrijgen. In dit geval, hoewel de overeenkomende gebruiker nog steeds in de database, zal het niet mogelijk zijn om verbinding te maken met de server met die gebruiker.
> [!NOTE]
> Inloggen met de verwijderde Azure AD-gebruiker kan nog steeds tot het is verlopen (tot 60 minuten vanaf de uitgifte van token).  Als u de gebruiker ook uit Azure Database voor MySQL verwijdert, wordt deze toegang onmiddellijk ingetrokken.
- Als de Azure AD-beheerder van de server wordt verwijderd, wordt de server niet langer gekoppeld aan een Azure AD-tenant en worden daarom alle Azure AD-aanmeldingen voor de server uitgeschakeld. Als u een nieuwe Azure AD-beheerder toevoegt van dezelfde tenant, worden Azure AD-aanmeldingen opnieuw ingeschakeld.
- Azure Database voor MySQL koppelt toegangstokens aan de Azure Database voor MySQL-gebruikers met behulp van de unieke Azure AD-gebruikers-id van de gebruiker, in tegenstelling tot het gebruik van de gebruikersnaam. Dit betekent dat als een Azure AD-gebruiker wordt verwijderd in Azure AD en een nieuwe gebruiker met dezelfde naam is gemaakt, Azure Database voor MySQL van mening is dat een andere gebruiker. Als een gebruiker uit Azure AD wordt verwijderd en vervolgens een nieuwe gebruiker met dezelfde naam wordt toegevoegd, kan de nieuwe gebruiker geen verbinding maken met de bestaande gebruiker.

## <a name="next-steps"></a>Volgende stappen

- Zie [Configureren en aanmelden bij Azure AD voor Azure Database voor MySQL](howto-configure-sign-in-azure-ad-authentication.md)voor meer informatie over het maken en invullen van Azure AD en vervolgens Azure AD configureren met Azure Database voor MySQL.
- Zie [Gebruikers maken in Azure Database voor MySQL voor](howto-create-users.md)een overzicht van aanmeldingen en databasegebruikers voor Azure Database voor MySQL.

<!--Image references-->

[1]: ./media/concepts-azure-ad-authentication/authentication-flow.png
[2]: ./media/concepts-azure-ad-authentication/admin-structure.png
