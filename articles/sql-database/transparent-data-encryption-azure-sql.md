---
title: Transparent Data Encryption
description: Een overzicht van transparante gegevensversleuteling voor SQL Database en Synapse SQL in Azure Synapse Analytics. Het document behandelt de voordelen en de opties voor configuratie, waaronder service-beheerde transparante gegevensversleuteling en Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 982a59f1eb8717e2fe2d86728cdae731c919aaf0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476964"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparante gegevensversleuteling voor SQL Database en Azure Synapse

Transparante gegevensversleuteling (TDE) helpt Azure SQL Database, Azure SQL Managed Instance en Synapse SQL in Azure Synapse Analytics te beschermen tegen de dreiging van kwaadaardige offline activiteit door gegevens in rust te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. Standaard wordt TDE ingeschakeld voor alle nieuw geïmplementeerde Azure SQL-databases. TDE kan niet worden gebruikt om de logische **hoofddatabase** in SQL Database te versleutelen.  De **hoofddatabase** bevat objecten die nodig zijn om de TDE-bewerkingen in de gebruikersdatabases uit te voeren.

TDE moet handmatig worden ingeschakeld voor oudere databases van Azure SQL Database, Azure SQL Managed Instance of Azure Synapse.
Beheerde instantiedatabases die zijn gemaakt door middel van herstel, erft de versleutelingsstatus van de brondatabase.

Transparante gegevensversleuteling versleutelt de opslag van een hele database met behulp van een symmetrische sleutel genaamd de databaseversleutelingssleutel. Deze database encryptie sleutel wordt beschermd door de transparante data encryptie beschermer. De protector is een servicebeheerd certificaat (door de service beheerde transparante gegevensversleuteling) of een asymmetrische sleutel die is opgeslagen in Azure Key Vault (Bring Your Own Key). U stelt de transparante gegevensversleutelingsbeveiliging in op serverniveau voor Azure SQL Database en Azure Synapse, en instantieniveau voor Azure SQL Managed Instance. De term *server* verwijst zowel naar server als instantie in dit document, tenzij anders vermeld.

Bij het opstarten van de database wordt de versleutelde databaseversleutelingssleutel gedecodeerd en vervolgens gebruikt voor decryptie en herversleuteling van de databasebestanden in het SQL Server Database Engine-proces. Transparante gegevensversleuteling voert real-time I/O-versleuteling en decryptie van de gegevens uit op paginaniveau. Elke pagina wordt ontsleuteld wanneer deze wordt ingelezen in het geheugen en vervolgens versleuteld voordat deze naar een schijf wordt geschreven. Zie [Transparante gegevensversleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)voor een algemene beschrijving van transparante gegevensversleuteling.

SQL Server die op een virtuele Azure-machine wordt uitgevoerd, kan ook een asymmetrische sleutel van Key Vault gebruiken. De configuratiestappen verschillen van het gebruik van een asymmetrische sleutel in SQL Database en SQL Managed Instance. Zie [Extensible key management met Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)voor meer informatie.

## <a name="service-managed-transparent-data-encryption"></a>Door de service beheerde transparante gegevensversleuteling

In Azure is de standaardinstelling voor transparante gegevensversleuteling dat de databaseversleutelingssleutel wordt beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke server en het gebruikte versleutelingsalgoritme is AES 256. Als een database zich in een georeplicatierelatie bevindt, worden zowel de primaire als de geosecundaire database beschermd door de bovenliggende serversleutel van de primaire database. Als twee databases zijn verbonden met dezelfde server, delen ze ook hetzelfde ingebouwde certificaat.  Microsoft roteert deze certificaten automatisch in overeenstemming met het interne beveiligingsbeleid en de hoofdsleutel wordt beschermd door een interne geheime opslag van Microsoft.  Klanten kunnen sql-database controleren dat het voldoet aan het interne beveiligingsbeleid in onafhankelijke auditrapporten van derden die beschikbaar zijn in het [Microsoft Trust Center.](https://servicetrust.microsoft.com/)

Microsoft verplaatst en beheert ook naadloos de sleutels als dat nodig is voor geo-replicatie en herstelt.

> [!IMPORTANT]
> Alle nieuw gemaakte SQL-databases en Managed Instance-databases worden standaard versleuteld met behulp van door de service beheerde transparante gegevensversleuteling. Bestaande SQL-databases die vóór mei 2017 zijn gemaakt en SQL-databases die zijn gemaakt via herstel, georeplicatie en databasekopie, worden niet standaard versleuteld. Bestaande Managed Instance-databases die vóór februari 2019 zijn gemaakt, worden niet standaard versleuteld. Beheerde instantiedatabases die zijn gemaakt door middel van de versleutelingsstatus van de bron herstellen.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Door de klant beheerde transparante gegevensversleuteling - Bring Your Own Key

[Met door de klant beheerde sleutels in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) u de Database Encryption Key (DEK) versleutelen met een door de klant beheerde asymmetrische sleutel genaamd TDE Protector.  Dit wordt ook algemeen aangeduid als Bring Your Own Key (BYOK) ondersteuning voor transparante gegevensversleuteling. In het BYOK-scenario wordt de TDE Protector opgeslagen in een door de klant eigendom en beheerd [Azure Key Vault,](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)het cloudgebaseerde externe sleutelbeheersysteem van Azure. De TDE Protector kan worden gegenereerd door de sleutelkluis of vanuit een on-premises HSM-apparaat [naar de sleutelkluis worden overgebracht.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) De TDE DEK, die wordt opgeslagen op de opstartpagina van een database, wordt versleuteld en gedecodeerd door de TDE Protector, die is opgeslagen in Azure Key Vault en nooit de sleutelkluis verlaat.  SQL Database moet machtigingen krijgen voor de sleutelkluis die eigendom is van de klant om de DEK te decoderen en te versleutelen. Als machtigingen van de logische SQL-server voor de sleutelkluis worden ingetrokken, is een database ontoegankelijk en worden alle gegevens versleuteld. Voor Azure SQL Database is de TDE-beveiliging ingesteld op het logische SQL-serverniveau en wordt deze overgenomen door alle databases die aan die server zijn gekoppeld. Voor [Azure SQL Managed Instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance)is de TDE-beveiliging ingesteld op instantieniveau en wordt deze overgenomen door alle *versleutelde* databases in die instantie. De term *server* verwijst zowel naar server als instantie in dit document, tenzij anders vermeld.

Met TDE-integratie met Azure Key Vault kunnen gebruikers belangrijke beheertaken beheren, waaronder sleutelrotaties, sleutelmachtigingen voor kluizen, belangrijke back-ups en controle-/rapportage inschakelen op alle TDE-beveiligingen met azure key vault-functionaliteit. Key Vault biedt centraal sleutelbeheer, maakt gebruik van streng bewaakte hardwarebeveiligingsmodules (HSM's) en maakt scheiding van taken tussen beheer van sleutels en gegevens mogelijk om te voldoen aan de naleving van het beveiligingsbeleid.
Zie [Transparante gegevensversleuteling met Azure Key Vault-integratie](transparent-data-encryption-byok-azure-sql.md)voor Azure SQL Database, SQL Managed Instance en Azure Synapse voor meer informatie over transparante gegevensversleuteling met Azure Key Vault-integratie .

Als u transparante gegevensversleuteling wilt gebruiken met Azure Key Vault-integratie (Bring Your Own Key-ondersteuning), raadpleegt u de handleiding [Schakel transparante gegevensversleuteling in met behulp van uw eigen sleutel uit Key Vault met PowerShell.](transparent-data-encryption-byok-azure-sql-configure.md)

## <a name="move-a-transparent-data-encryption-protected-database"></a>Een database met transparante gegevensversleuteling verplaatsen

U hoeft geen databases te decoderen voor bewerkingen binnen Azure. De transparante gegevensversleutelingsinstellingen in de brondatabase of primaire database worden transparant overgenomen op het doel. Operaties die zijn opgenomen, omvatten:

- Geo-herstel
- Selfservice point-in-time herstellen
- Herstel van een verwijderde database
- Actieve geo-replicatie
- Het maken van een databasekopie
- Back-upbestand herstellen naar Azure SQL Managed Instance

> [!IMPORTANT]
> Het maken van handmatige COPY-ONLY back-up van een database versleuteld door service-managed TDE is niet toegestaan in Azure SQL Managed Instance, omdat certificaat dat wordt gebruikt voor versleuteling niet toegankelijk is. Gebruik de functie point-in-time-restore om dit type database naar een ander beheerde instantie te verplaatsen.

Wanneer u een database met transparante gegevensversleuteling exporteert, wordt de geëxporteerde inhoud van de database niet versleuteld. Deze geëxporteerde inhoud wordt opgeslagen in niet-versleutelde BACPAC-bestanden. Zorg ervoor dat u de BACPAC-bestanden op de juiste manier beschermt en transparante gegevensversleuteling inschakelt nadat de nieuwe database is geïmporteerd.

Als het BACPAC-bestand bijvoorbeeld wordt geëxporteerd vanuit een on-premises SQL Server-instantie, wordt de geïmporteerde inhoud van de nieuwe database niet automatisch versleuteld. Als het BACPAC-bestand wordt geëxporteerd naar een on-premises SQL Server-instantie, wordt de nieuwe database ook niet automatisch versleuteld.

De enige uitzondering is wanneer u exporteert van en naar een SQL-database. Transparante gegevensversleuteling is ingeschakeld in de nieuwe database, maar het BACPAC-bestand zelf is nog steeds niet versleuteld.


## <a name="manage-transparent-data-encryption"></a>Transparante gegevensversleuteling beheren
# <a name="portal"></a>[Portal](#tab/azure-portal)
Transparante gegevensversleuteling beheren in de Azure-portal.

Als u transparante gegevensversleuteling wilt configureren via de Azure-portal, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.

U schakelt transparante gegevensversleuteling in en uit op databaseniveau. Als u transparante gegevensversleuteling in een database wilt inschakelen, gaat u naar de [Azure-portal](https://portal.azure.com) en meldt u zich aan met uw Azure Administrator- of Inzenderaccount. Zoek de transparante instellingen voor gegevensversleuteling onder uw gebruikersdatabase. Standaard wordt door de service beheerde transparante gegevensversleuteling gebruikt. Er wordt automatisch een transparant versleutelingscertificaat gegenereerd voor de server die de database bevat. Voor Azure SQL Managed Instance gebruik t-SQL om transparante gegevensversleuteling in en uit te schakelen in een database.

![Door de service beheerde transparante gegevensversleuteling](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

U stelt de transparante sleutel voor gegevensversleuteling, ook wel bekend als de transparante gegevensversleutelingsprotector, in op serverniveau. Als u transparante gegevensversleuteling wilt gebruiken met Bring Your Own Key-ondersteuning en uw databases wilt beveiligen met een sleutel van Key Vault, opent u de transparante gegevensversleutelingsinstellingen onder uw server.

![Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Beheer transparante gegevensversleuteling met PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u transparante gegevensversleuteling wilt configureren via PowerShell, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlets voor Azure SQL Database en Azure Synapse

Gebruik de volgende cmdlets voor Azure SQL Database en Azure Synapse:

| Cmdlet | Beschrijving |
| --- | --- |
| [Set-azsqldatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Transparante gegevensversleuteling voor een database in- of uitschakelen|
| [Get-azsqldatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Krijgt de transparante gegevensversleutelingsstatus voor een database |
| [Get-azsqldatabaseTransparentDataEncryptionActiviteit](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controleert de versleutelingsvoortgang voor een database |
| [Add-AzsqlserverKeyVaultKeyKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Voegt een Key Vault-sleutel toe aan een SQL Server-exemplaar |
| [Get-AzSqlServerKeyVaultKeyKeyKeyKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Haalt de Key Vault-sleutels voor een Azure SQL Database-server  |
| [Set-azsqlserverTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Stelt de transparante gegevensversleutelingsprotector in voor een SQL Server-exemplaar |
| [Get-azsqlserverTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Krijgt de transparante data encryptie protector |
| [Verwijder-azsqlserverkeyvaultkey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Een Key Vault-sleutel verwijderen uit een SQL Server-exemplaar |
|  | |

> [!IMPORTANT]
> Voor Azure SQL Managed Instance gebruikt u de opdracht T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) om transparante gegevensversleuteling in- en uitschakelen op databaseniveau en controleer u het voorbeeld [van PowerShell-script](transparent-data-encryption-byok-azure-sql-configure.md) om transparante gegevensversleuteling op instantieniveau te beheren.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Beheer transparante gegevensversleuteling met Transact-SQL.

Maak verbinding met de database met behulp van een aanmelding die een beheerder of lid is van de **dbmanager-rol** in de hoofddatabase.

| Opdracht | Beschrijving |
| --- | --- |
| [ALTER-DATABASE (Azure SQL-database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | ENCRYPTIE INSTELLEN AAN/UIT versleutelt of decodeert een database |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingsstatus van een database en de bijbehorende databaseversleutelingssleutels |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingsstatus van elk gegevensmagazijnknooppunt en de bijbehorende databaseversleutelingssleutels |
|  | |

U de transparante gegevensversleutelingsprotector niet overschakelen naar een sleutel van Key Vault met Transact-SQL. Gebruik PowerShell of de Azure-portal.

# <a name="rest-api"></a>[REST-API](#tab/azure-RESTAPI)
Beheer transparante gegevensversleuteling met behulp van de REST API.

Als u transparante gegevensversleuteling wilt configureren via de REST-API, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.
Gebruik de volgende set opdrachten voor Azure SQL Database en Azure Synapse:

| Opdracht | Beschrijving |
| --- | --- |
|[Server maken of bijwerken](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Hiermee voegt u een Azure Active Directory-identiteit toe aan een SQL Server-instantie (wordt gebruikt om toegang te verlenen tot Key Vault)|
|[Serversleutel maken of bijwerken](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Voegt een Key Vault-sleutel toe aan een SQL Server-exemplaar|
|[Serversleutel verwijderen](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Een Key Vault-sleutel verwijderen uit een SQL Server-exemplaar|
|[Serversleutels ophalen](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Haalt een specifieke Key Vault-sleutel uit een SQL Server-exemplaar|
|[Lijstserversleutels per server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|De Key Vault-sleutels voor een SQL Server-exemplaar ophalen |
|[Versleutelingsbeveiliging maken of bijwerken](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Stelt de transparante gegevensversleutelingsprotector in voor een SQL Server-exemplaar|
|[Encryptiebeveiliging krijgen](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Krijgt de transparante gegevensversleutelingsprotector voor een SQL Server-exemplaar|
|[Lijst encryptiebeschermers per server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Haalt de transparante gegevensversleutelingsprotectors voor een SQL Server-exemplaar |
|[Transparante gegevensversleutelingsconfiguratie maken of bijwerken](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Transparante gegevensversleuteling voor een database in- of uitschakelen|
|[Transparante gegevensversleutelingsconfiguratie opvragen](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Krijgt de transparante gegevensversleutelingsconfiguratie voor een database|
|[Resultaten van transparante gegevensversleuteling weergeven](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Krijgt het versleutelingsresultaat voor een database|

## <a name="next-steps"></a>Volgende stappen

- Zie [Transparante gegevensversleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)voor een algemene beschrijving van transparante gegevensversleuteling.
- Zie [Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning](transparent-data-encryption-byok-azure-sql.md)voor Bring Your Own Key voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse voor meer informatie over transparante gegevensversleuteling met Bring Your Own Key-ondersteuning.
- Als u transparante gegevensversleuteling wilt gebruiken met Bring Your Own Key-ondersteuning, raadpleegt u de handleiding [Schakel transparante gegevensversleuteling in met behulp van uw eigen sleutel van Key Vault met PowerShell.](transparent-data-encryption-byok-azure-sql-configure.md)
- Zie de [documentatiepagina Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)voor meer informatie over Key Vault.
