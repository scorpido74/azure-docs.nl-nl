---
title: Verificatie
description: Meer informatie over het verifiëren naar Azure Synapse Analytics met Azure Active Directory (Azure AD) of SQL Server-verificatie.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019
tag: azure-synapse
ms.openlocfilehash: ccc5db828a03c37d3fc4f49b13883ac3eeda2368
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584218"
---
# <a name="authenticate-to-azure-synapse-analytics"></a>Verifiëren naar Azure Synapse Analytics

Meer informatie over het verifiëren van Synapse SQL-poool in Azure Synapse met Azure Active Directory (Azure AD) of SQL Server-verificatie.

Als u verbinding wilt maken met een SQL-groep, moet u beveiligingsreferenties invoeren voor verificatiedoeleinden. Bij het tot stand brengen van een verbinding worden bepaalde verbindingsinstellingen geconfigureerd als onderdeel van het instellen van uw querysessie.  

Zie [Het beveiligen van een databasedocumentatie](sql-data-warehouse-overview-manage-security.md)voor meer informatie over beveiliging en het inschakelen van verbindingen met uw gegevensmagazijn.

## <a name="sql-authentication"></a>SQL-verificatie

Als u verbinding wilt maken met SQL-groep, moet u de volgende informatie verstrekken:

* Volledig gekwalificeerde servernaam
* SQL-verificatie opgeven
* Gebruikersnaam
* Wachtwoord
* Standaarddatabase (optioneel)

Standaard maakt uw verbinding verbinding met de *hoofddatabase* en niet met uw gebruikersdatabase. Als u verbinding wilt maken met uw gebruikersdatabase, u een van de twee dingen doen:

* Geef de standaarddatabase op bij het registreren van uw server met de SQL Server Object Explorer in SSDT, SSMS of in de tekenreeks van de toepassingsverbinding. Neem bijvoorbeeld de parameter InitialCatalog op voor een ODBC-verbinding.
* Markeer de gebruikersdatabase voordat u een sessie maakt in SSDT.

> [!NOTE]
> De Transact-SQL-instructie **USE MyDatabase;** wordt niet ondersteund voor het wijzigen van de database voor een verbinding. Raadpleeg de [query met Visual Studio](sql-data-warehouse-query-visual-studio.md) voor richtlijnen die verbinding maken met een SQL-groep met SSDT.
> 
> 

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory -verificatie (Azure AD)

[Azure Active Directory-verificatie](../../active-directory/fundamentals/active-directory-whatis.md) is een mechanisme om verbinding te maken met SQL-groep met behulp van identiteiten in Azure Active Directory (Azure AD). Met Azure Active Directory-verificatie u de identiteiten van databasegebruikers en andere Microsoft-services centraal beheren op één centrale locatie. Centraal ID-beheer biedt één plek om Azure Synapse-gebruikers te beheren en vereenvoudigt het machtigingsbeheer. 

### <a name="benefits"></a>Voordelen

Azure Active Directory-voordelen zijn onder andere:

* Biedt een alternatief voor SQL Server-verificatie.
* Helpt de verspreiding van gebruikersidentiteiten tussen databaseservers te stoppen.
* Maakt rotatie van wachtwoorden op één plek mogelijk
* Databasemachtigingen beheren met externe (Azure AD)-groepen.
* Hiermee wordt het opslaan van wachtwoorden geëlimineerd door geïntegreerde Windows-verificatie en andere vormen van verificatie in te schakelen die worden ondersteund door Azure Active Directory.
* Gebruikt opgenomen databasegebruikers om identiteiten op databaseniveau te verifiëren.
* Ondersteunt tokengebaseerde verificatie voor toepassingen die verbinding maken met SQL-groep.
* Ondersteunt multi-factor-verificatie via Active Directory Universal Authentication voor verschillende hulpprogramma's, waaronder [SQL Server Management Studio](../../sql-database/sql-database-ssms-mfa-authentication.md) en SQL Server Data [Tools.](https://docs.microsoft.com/sql/ssdt/azure-active-directory?toc=/azure/sql-data-warehouse/toc.json)

> [!NOTE]
> Azure Active Directory is nog relatief nieuw en heeft een aantal beperkingen. Zie [Azure AD-functies en -beperkingen](../../sql-database/sql-database-aad-authentication.md#azure-ad-features-and-limitations), met name de aanvullende overwegingen, om ervoor te zorgen dat Azure Active Directory geschikt is voor uw omgeving.
> 
> 

### <a name="configuration-steps"></a>Configuratiestappen

Voer deze stappen uit om Azure Active Directory-verificatie te configureren.

1. Een Azure Active Directory maken en invullen
2. Optioneel: de active directory koppelen of wijzigen die momenteel is gekoppeld aan uw Azure-abonnement
3. Een Azure Active Directory-beheerder voor Azure Synapse maken
4. Uw clientcomputers configureren
5. Opgenomen databasegebruikers maken in uw database die zijn toegewezen aan Azure AD-identiteiten
6. Verbinding maken met uw SQL-groep met Azure AD-identiteiten

Momenteel worden Azure Active Directory-gebruikers niet weergegeven in SSDT Object Explorer. Bekijk de gebruikers in [sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)als tijdelijke oplossing.

### <a name="find-the-details"></a>Zoek de details

* De stappen voor het configureren en gebruiken van Azure Active Directory-verificatie zijn vrijwel identiek voor Azure SQL Database en Synapse SQL-pool in Azure Synapse. Volg de gedetailleerde stappen in het onderwerp [Verbinding maken met SQL Database of SQL Pool met Azure Active Directory Authentication](../../sql-database/sql-database-aad-authentication.md).
* Maak aangepaste databaserollen en voeg gebruikers toe aan de rollen. Geef vervolgens gedetailleerde machtigingen toe aan de rollen. Zie [Aan de slag met databaseenginemachtigingen](https://msdn.microsoft.com/library/mt667986.aspx)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Zie [Query met Visual Studio](sql-data-warehouse-query-visual-studio.md)als u wilt beginnen met query's met Visual Studio en andere toepassingen.
