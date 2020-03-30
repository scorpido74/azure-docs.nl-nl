---
title: PaaS-databases beveiligen in Azure | Microsoft Documenten
description: 'Meer informatie over de aanbevolen procedures voor Azure SQL Database en SQL Data Warehouse Security voor het beveiligen van uw PaaS-web- en mobiele toepassingen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: c73f585e3102618cea378716491f9354810a6db8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80124999"
---
# <a name="best-practices-for-securing-paas-databases-in-azure"></a>Aanbevolen procedures voor het beveiligen van PaaS-databases in Azure

In dit artikel bespreken we een verzameling bestpractices voor [Azure SQL Database](../../sql-database/sql-database-technical-overview.md) en SQL Data [Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) security voor het beveiligen van uw Web- en mobiele toepassingen (Platform-as-a-Service). Deze best practices zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten zoals uzelf.

Azure SQL Database en SQL Data Warehouse bieden een relationele databaseservice voor uw internettoepassingen. Laten we eens kijken naar services die uw toepassingen en gegevens helpen beschermen wanneer u Azure SQL Database en SQL Data Warehouse gebruikt in een PaaS-implementatie:

- Azure Active Directory-verificatie (in plaats van SQL Server-verificatie)
- Azure SQL-firewall
- Transparent Data Encryption (TDE)

## <a name="use-a-centralized-identity-repository"></a>Een gecentraliseerde identiteitsopslagplaats gebruiken
Azure SQL-databases kunnen worden geconfigureerd om een van de twee typen verificatie te gebruiken:

- **SQL-verificatie** maakt gebruik van een gebruikersnaam en wachtwoord. Wanneer u de logische server voor uw database hebt gemaakt, hebt u een aanmelding 'serverbeheerder' opgegeven met een gebruikersnaam en wachtwoord. Met behulp van deze referenties u zich als eigenaar van de database verifiëren naar elke database op die server.

- **Azure Active Directory-verificatie** maakt gebruik van identiteiten die worden beheerd door Azure Active Directory en wordt ondersteund voor beheerde en geïntegreerde domeinen. Als u Azure Active Directory Authentication wilt gebruiken, moet u een andere serverbeheerder maken, de 'Azure AD-beheerder', die Azure AD-gebruikers en -groepen mag beheren. Deze beheerder kan ook alle bewerkingen uitvoeren die reguliere serverbeheerders kunnen uitvoeren.

[Azure Active Directory-verificatie](../../active-directory/develop/authentication-scenarios.md) is een mechanisme om verbinding te maken met Azure SQL Database en SQL Data Warehouse met behulp van identiteiten in Azure Active Directory (AD). Azure AD biedt een alternatief voor SQL Server-verificatie, zodat u de verspreiding van gebruikersidentiteiten tussen databaseservers stoppen. Met Azure AD-verificatie u de identiteiten van databasegebruikers en andere Microsoft-services centraal beheren op één centrale locatie. Centraal identiteitsbeheer biedt één plek voor het beheren van databasegebruikers en vereenvoudigt het machtigingenbeheer.  

### <a name="benefits-of-using-azure-ad-instead-of-sql-authentication"></a>Voordelen van het gebruik van Azure AD in plaats van SQL-verificatie
- Hiermee u wachtwoordrotatie op één plaats gebruiken.
- Hiermee beheert u databasemachtigingen met externe Azure AD-groepen.
- Hiermee elimineert u het opslaan van wachtwoorden door geïntegreerde Windows-verificatie en andere vormen van verificatie in te schakelen die worden ondersteund door Azure AD.
- Gebruikt opgenomen databasegebruikers om identiteiten op databaseniveau te verifiëren.
- Ondersteunt tokengebaseerde verificatie voor toepassingen die verbinding maken met SQL Database.
- Ondersteunt domeinfederatie met Active Directory Federation Services (ADFS) of native user/password authentication voor een lokaal Azure AD zonder domeinsynchronisatie.
- Ondersteunt verbindingen vanuit SQL Server Management Studio die gebruikmaken van Active Directory Universal Authentication, waaronder [Multi-Factor Authentication (MFA)](/azure/active-directory/authentication/multi-factor-authentication). MFA omvat robuuste verificatie met een scala aan gebruikersvriendelijke verificatieopties, waaronder telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app. Zie [Universele verificatie met SQL Database en SQL Data Warehouse](../../sql-database/sql-database-ssms-mfa-authentication.md)voor meer informatie.

Zie voor meer informatie over Azure AD-verificatie:

- [Azure Active Directory Authentication gebruiken voor verificatie met SQL Database, Managed Instance of SQL Data Warehouse](../../sql-database/sql-database-aad-authentication.md)
- [Verificatie met Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-authentication.md)
- [Tokengebaseerde verificatieondersteuning voor Azure SQL DB met Azure AD-verificatie](../../sql-database/sql-database-aad-authentication.md)

> [!NOTE]
> Zie [Azure AD-functies en -beperkingen](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations)om ervoor te zorgen dat Azure Active Directory geschikt is voor uw omgeving.
>
>

## <a name="restrict-access-based-on-ip-address"></a>Toegang beperken op basis van IP-adres
U firewallregels maken die bereiken van acceptabele IP-adressen opgeven. Deze regels kunnen worden gericht op zowel de server- als databaseniveaus. We raden u aan waar mogelijk firewallregels op databaseniveau te gebruiken om de beveiliging te verbeteren en uw database draagbaarder te maken. Firewallregels op serverniveau worden het beste gebruikt voor beheerders en wanneer u veel databases hebt met dezelfde toegangsvereisten, maar u geen tijd wilt besteden aan het individueel configureren van elke database.

Ip-adresbeperkingen voor standaardbronnen van SQL Database bieden toegang vanaf elk Azure-adres, inclusief andere abonnementen en tenants. U dit beperken om alleen uw IP-adressen toegang te geven tot de instantie. Zelfs met uw SQL firewall en IP-adres beperkingen, sterke authenticatie is nog steeds nodig. Zie de aanbevelingen die eerder in dit artikel zijn gedaan.

Zie voor meer informatie over Azure SQL Firewall- en IP-beperkingen:

- [Azure SQL Database en SQL Data Warehouse access control](../../sql-database/sql-database-manage-logins.md)
- [Firewall-regels voor Azure SQL Database en SQL Data Warehouse](../../sql-database/sql-database-firewall-configure.md)


## <a name="encrypt-data-at-rest"></a>Data-at-rest versleutelen
[Transparent Data Encryption (TDE)](/sql/relational-databases/security/encryption/transparent-data-encryption) is standaard ingeschakeld. TDE versleutelt sql server, Azure SQL Database en Azure SQL Data Warehouse-gegevens en logboekbestanden op transparante wijze. TDE beschermt tegen een compromis van directe toegang tot de bestanden of hun back-up. Hierdoor u gegevens in rust versleutelen zonder bestaande toepassingen te wijzigen. TDE moet altijd ingeschakeld blijven; Dit zal echter niet voorkomen dat een aanvaller het normale toegangspad gebruikt. TDE biedt de mogelijkheid om te voldoen aan vele wetten, voorschriften en richtlijnen die in verschillende industrieën zijn vastgesteld.

Azure SQL beheert belangrijke gerelateerde problemen voor TDE. Net als bij TDE moet er bij het verplaatsen van databases speciale zorg worden getroffen om de herstelbaarheid te waarborgen. In meer geavanceerde scenario's kunnen de sleutels expliciet worden beheerd in Azure Key Vault door middel van uitbreidbaar sleutelbeheer. Zie [TDE inschakelen op SQL Server met EKM](/sql/relational-databases/security/encryption/enable-tde-on-sql-server-using-ekm). Dit maakt het ook mogelijk om byok (Bring Your Own Key) via azure key vaults BYOK-mogelijkheden te gebruiken.

Azure SQL biedt versleuteling voor kolommen via [Always Encrypted.](/sql/relational-databases/security/encryption/always-encrypted-database-engine) Hierdoor hebben alleen geautoriseerde toepassingen toegang tot gevoelige kolommen. Het gebruik van dit soort versleuteling beperkt SQL-query's voor versleutelde kolommen tot op gelijkheid gebaseerde waarden.

Versleuteling op toepassingsniveau moet ook worden gebruikt voor selectieve gegevens. Zorgen over gegevenssoevereiniteit kunnen soms worden beperkt door gegevens te versleutelen met een sleutel die in het juiste land/regio wordt bewaard. Dit voorkomt dat zelfs toevallige gegevensoverdracht een probleem veroorzaakt, omdat het onmogelijk is om de gegevens te decoderen zonder de sleutel, ervan uitgaande dat een sterk algoritme wordt gebruikt (zoals AES 256).

U aanvullende voorzorgsmaatregelen gebruiken om de database te beveiligen, zoals het ontwerpen van een beveiligd systeem, het versleutelen van vertrouwelijke assets en het bouwen van een firewall rond de databaseservers.

## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt u kennis gemaakt met een verzameling best practices voor SQL Database- en SQL Data Warehouse-beveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-web- en mobiele toepassingen beveiligen met Azure App Services](paas-applications-using-app-services.md)
