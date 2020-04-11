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
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113537"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparante gegevensversleuteling voor SQL Database en Azure Synapse

[Transparante gegevensversleuteling (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) helpt Azure SQL Database, Azure SQL Managed Instance en Synapse SQL in Azure Synapse Analytics te beschermen tegen de dreiging van kwaadaardige offline activiteit door gegevens in rust te versleutelen. Het voert in realtime versleuteling en ontsleuteling van de database, bijbehorende back-ups en transactielogboekbestanden 'at-rest' uit, zonder dat er wijzigingen in de toepassing moeten worden aangebracht. TDE is standaard ingeschakeld voor alle nieuw geïmplementeerde Azure SQL-databases en moet handmatig worden ingeschakeld voor oudere databases van Azure SQL Database, Azure SQL Managed Instance of Azure Synapse.

TDE voert real-time I/O encryptie en decryptie van de gegevens op paginaniveau. Elke pagina wordt ontsleuteld wanneer deze wordt ingelezen in het geheugen en vervolgens versleuteld voordat deze naar een schijf wordt geschreven. TDE versleutelt de opslag van een hele database met behulp van een symmetrische sleutel genaamd de Database Encryption Key (DEK). Bij het opstarten van de database wordt de versleutelde DEK gedecodeerd en vervolgens gebruikt voor decryptie en herversleuteling van de databasebestanden in het SQL Server Database Engine-proces. DEK wordt beschermd door de TDE-beschermer. TDE-protector is een servicebeheerd certificaat (door service beheerde transparante gegevensversleuteling) of een asymmetrische sleutel die is opgeslagen in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (door de klant beheerde transparante gegevensversleuteling). 

Voor Azure SQL Database en Azure Synapse is de TDE-beveiliging ingesteld op het logische SQL-serverniveau en wordt deze overgenomen door alle databases die aan die server zijn gekoppeld. Voor Azure SQL Managed Instance (BYOK-functie in preview) wordt de TDE-protector op instantieniveau ingesteld en wordt deze overgenomen door alle versleutelde databases in die instantie. De term *server* verwijst zowel naar server als instantie in dit document, tenzij anders vermeld.

> [!IMPORTANT]
> Alle nieuw gemaakte Azure SQL-databases worden standaard versleuteld met behulp van door de service beheerde transparante gegevensversleuteling. Bestaande SQL-databases die vóór mei 2017 zijn gemaakt en SQL-databases die zijn gemaakt via herstel, georeplicatie en databasekopie, worden niet standaard versleuteld. Bestaande Managed Instance-databases die vóór februari 2019 zijn gemaakt, worden niet standaard versleuteld. Beheerde instantiedatabases die zijn gemaakt door middel van de versleutelingsstatus van de bron herstellen.

> [!NOTE]
> TDE kan niet worden gebruikt om de logische **hoofddatabase** in SQL Database te versleutelen.  De **hoofddatabase** bevat objecten die nodig zijn om de TDE-bewerkingen in de gebruikersdatabases uit te voeren.


## <a name="service-managed-transparent-data-encryption"></a>Door de service beheerde transparante gegevensversleuteling

In Azure is de standaardinstelling voor TDE dat de DEK wordt beschermd door een ingebouwd servercertificaat. Het ingebouwde servercertificaat is uniek voor elke server en het gebruikte versleutelingsalgoritme is AES 256. Als een database zich in een georeplicatierelatie bevindt, worden zowel de primaire als de geosecundaire databases beschermd door de bovenliggende serversleutel van de primaire database. Als twee databases zijn verbonden met dezelfde server, delen ze ook hetzelfde ingebouwde certificaat.  Microsoft roteert deze certificaten automatisch in overeenstemming met het interne beveiligingsbeleid en de hoofdsleutel wordt beschermd door een interne geheime opslag van Microsoft.  Klanten kunnen sql-database controleren dat het voldoet aan het interne beveiligingsbeleid in onafhankelijke auditrapporten van derden die beschikbaar zijn in het [Microsoft Trust Center.](https://servicetrust.microsoft.com/)

Microsoft verplaatst en beheert ook naadloos de sleutels als dat nodig is voor geo-replicatie en herstelt.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Door de klant beheerde transparante gegevensversleuteling - Bring Your Own Key

Door de klant beheerde TDE wordt ook wel Bring Your Own Key (BYOK) support voor TDE genoemd. In dit scenario is de TDE Protector die de DEK versleutelt een door de klant beheerde asymmetrische sleutel, die wordt opgeslagen in een klantbeheer en beheerd Azure Key Vault (azure's cloud-based external key management system) en nooit de sleutelkluis verlaat. De TDE Protector kan worden [gegenereerd door de sleutelkluis of overgebracht naar de sleutelkluis](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) vanuit een on-premises hardware beveiligingsmodule (HSM) apparaat. SQL Database moet machtigingen krijgen voor de sleutelkluis die eigendom is van de klant om de DEK te decoderen en te versleutelen. Als machtigingen van de logische SQL-server voor de sleutelkluis worden ingetrokken, is een database ontoegankelijk en worden alle gegevens versleuteld

Met TDE-integratie met Azure Key Vault kunnen gebruikers belangrijke beheertaken beheren, waaronder sleutelrotaties, sleutelmachtigingen voor kluizen, belangrijke back-ups en controle-/rapportage inschakelen op alle TDE-beveiligingen met azure key vault-functionaliteit. Key Vault biedt centraal sleutelbeheer, maakt gebruik van streng bewaakte HSM's en maakt scheiding van taken tussen beheer van sleutels en gegevens mogelijk om te voldoen aan de naleving van het beveiligingsbeleid.
Zie [Transparante gegevensversleuteling met Azure Key Vault-integratie](transparent-data-encryption-byok-azure-sql.md)voor meer informatie over BYOK voor Azure SQL Database en Azure Synapse.

Als u TDE wilt gaan gebruiken met Azure Key Vault-integratie, raadpleegt u de handleiding [Schakel transparante gegevensversleuteling in met behulp van uw eigen sleutel uit Key Vault.](transparent-data-encryption-byok-azure-sql-configure.md)

## <a name="move-a-transparent-data-encryption-protected-database"></a>Een database met transparante gegevensversleuteling verplaatsen

U hoeft geen databases te decoderen voor bewerkingen binnen Azure. De TDE-instellingen in de brondatabase of primaire database worden transparant overgenomen op het doel. Operaties die zijn opgenomen, omvatten:

- Geo-herstel
- Selfservice point-in-time herstellen
- Herstel van een verwijderde database
- Actieve geo-replicatie
- Het maken van een databasekopie
- Back-upbestand herstellen naar Azure SQL Managed Instance

> [!IMPORTANT]
> Het maken van handmatige COPY-ONLY back-up van een database versleuteld door service-managed TDE is niet toegestaan in Azure SQL Managed Instance, omdat certificaat dat wordt gebruikt voor versleuteling niet toegankelijk is. Gebruik de functie point-in-time-restore om dit type database naar een ander beheerde instantie te verplaatsen.

Wanneer u een met TDE beveiligde database exporteert, wordt de geëxporteerde inhoud van de database niet versleuteld. Deze geëxporteerde inhoud wordt opgeslagen in niet-versleutelde BACPAC-bestanden. Zorg ervoor dat u de BACPAC-bestanden op de juiste manier beschermt en TDE inschakelt nadat de nieuwe database is geïmporteerd.

Als het BACPAC-bestand bijvoorbeeld wordt geëxporteerd vanuit een on-premises SQL Server-instantie, wordt de geïmporteerde inhoud van de nieuwe database niet automatisch versleuteld. Als het BACPAC-bestand wordt geëxporteerd naar een on-premises SQL Server-instantie, wordt de nieuwe database ook niet automatisch versleuteld.

De enige uitzondering is wanneer u exporteert van en naar een SQL-database. TDE is ingeschakeld in de nieuwe database, maar het BACPAC-bestand zelf is nog steeds niet versleuteld.


## <a name="manage-transparent-data-encryption"></a>Transparante gegevensversleuteling beheren
# <a name="portal"></a>[Portal](#tab/azure-portal)
TDE beheren in de Azure-portal.

Als u TDE wilt configureren via de Azure-portal, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.

U schakelt TDE in en uit op databaseniveau. Als u TDE in een database wilt inschakelen, gaat u naar de [Azure-portal](https://portal.azure.com) en meldt u zich aan met uw Azure Administrator- of Inzenderaccount. Zoek de TDE-instellingen onder uw gebruikersdatabase. Standaard wordt door de service beheerde transparante gegevensversleuteling gebruikt. Er wordt automatisch een TDE-certificaat gegenereerd voor de server die de database bevat. Voor Azure SQL Managed Instance gebruik t-SQL om TDE in en uit te schakelen in een database.

![Door de service beheerde transparante gegevensversleuteling](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

U stelt de TDE-mastertoets, bekend als de TDE-beveiliging, in op serverniveau. Als u TDE wilt gebruiken met BYOK-ondersteuning en uw databases wilt beveiligen met een sleutel van Key Vault, opent u de TDE-instellingen onder uw server.

![Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Beheer TDE met PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De PowerShell Azure Resource Manager-module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkelingen zijn voor de Az.Sql-module. Zie [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRm-modules zijn nagenoeg identiek.

Als u TDE wilt configureren via PowerShell, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.

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
> Voor Azure SQL Managed Instance gebruikt u de opdracht T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) om TDE in- en uitschakelen op databaseniveau en controleer u het voorbeeld [van PowerShell-script](transparent-data-encryption-byok-azure-sql-configure.md) om TDE op instantieniveau te beheren.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Beheer TDE met Transact-SQL.

Maak verbinding met de database met behulp van een aanmelding die een beheerder of lid is van de **dbmanager-rol** in de hoofddatabase.

| Opdracht | Beschrijving |
| --- | --- |
| [ALTER-DATABASE (Azure SQL-database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | ENCRYPTIE INSTELLEN AAN/UIT versleutelt of decodeert een database |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingsstatus van een database en de bijbehorende databaseversleutelingssleutels |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelingsstatus van elk Azure Synapse-knooppunt en de bijbehorende databaseversleutelingssleutels |
|  | |

U de TDE-beveiliging niet overschakelen naar een sleutel van Key Vault met Transact-SQL. Gebruik PowerShell of de Azure-portal.

# <a name="rest-api"></a>[REST-API](#tab/azure-RESTAPI)
Beheer TDE met behulp van de REST API.

Als u TDE wilt configureren via de REST-API, moet u zijn verbonden als Azure-eigenaar, inzender of SQL Security Manager.
Gebruik de volgende set opdrachten voor Azure SQL Database en Azure Synapse:

| Opdracht | Beschrijving |
| --- | --- |
|[Server maken of bijwerken](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Hiermee voegt u een Azure Active Directory-identiteit toe aan een SQL Server-instantie (wordt gebruikt om toegang te verlenen tot Key Vault)|
|[Serversleutel maken of bijwerken](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Voegt een Key Vault-sleutel toe aan een SQL Server-exemplaar|
|[Serversleutel verwijderen](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Een Key Vault-sleutel verwijderen uit een SQL Server-exemplaar|
|[Serversleutels ophalen](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Haalt een specifieke Key Vault-sleutel uit een SQL Server-exemplaar|
|[Lijstserversleutels per server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|De Key Vault-sleutels voor een SQL Server-exemplaar ophalen |
|[Versleutelingsbeveiliging maken of bijwerken](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Stelt de TDE-beveiliging in voor een SQL Server-exemplaar|
|[Encryptiebeveiliging krijgen](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Krijgt de TDE-beveiliging voor een SQL Server-exemplaar|
|[Lijst encryptiebeschermers per server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Haalt de TDE-beschermers voor een SQL Server-exemplaar |
|[Transparante gegevensversleutelingsconfiguratie maken of bijwerken](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|TDE voor een database in- of uitschakelen|
|[Transparante gegevensversleutelingsconfiguratie opvragen](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Krijgt de TDE-configuratie voor een database|
|[Resultaten van transparante gegevensversleuteling weergeven](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Krijgt het versleutelingsresultaat voor een database|

## <a name="see-also"></a>Zie ook
- SQL Server die op een virtuele Azure-machine wordt uitgevoerd, kan ook een asymmetrische sleutel van Key Vault gebruiken. De configuratiestappen verschillen van het gebruik van een asymmetrische sleutel in SQL Database en SQL Managed Instance. Zie [Extensible key management met Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)voor meer informatie.
- Zie [Transparante gegevensversleuteling](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)voor een algemene beschrijving van TDE.
- Zie [Transparante gegevensversleuteling met Bring Your Own Key-ondersteuning](transparent-data-encryption-byok-azure-sql.md)voor meer informatie over TDE met BYOK-ondersteuning voor Azure SQL Database, Azure SQL Managed Instance en Azure Synapse.
- Als u TDE wilt gebruiken met Bring Your Own Key-ondersteuning, raadpleegt u de handleiding, [Schakel transparante gegevensversleuteling in met uw eigen sleutel van Key Vault.](transparent-data-encryption-byok-azure-sql-configure.md)
- Zie Beveiligde toegang tot [een sleutelkluis voor](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)meer informatie over Key Vault.
