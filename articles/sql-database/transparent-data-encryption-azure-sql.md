---
title: Transparante gegevens versleuteling voor Azure SQL Database en Data Warehouse | Microsoft Docs
description: Een overzicht van transparante gegevens versleuteling voor SQL Database en het Data Warehouse. In het document worden de voor delen en de opties voor de configuratie beschreven, waaronder door de service beheerde transparante gegevens versleuteling en Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: b63a8c9defdf154f35a847f29182b49ff94ff3a6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933207"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparante gegevens versleuteling voor SQL Database en Data Warehouse

Transparent Data Encryption (TDE) helpt bij het beschermen van Azure SQL Database, Azure SQL Managed instance en Azure Data Warehouse tegen de dreiging van schadelijke offline activiteiten door het versleutelen van gegevens in rust. Er wordt in realtime versleuteling en ontsleuteling van de data base, gekoppelde back-ups en transactie logboek bestanden in rust uitgevoerd zonder dat de toepassing hoeft te worden gewijzigd. TDE is standaard ingeschakeld voor alle nieuw geïmplementeerde Azure SQL-data bases. TDE kan niet worden gebruikt voor het versleutelen van de logische **hoofd** database in SQL database.  De **hoofd** database bevat objecten die nodig zijn om de TDe-bewerkingen uit te voeren op de gebruikers databases.

TDE moet hand matig worden ingeschakeld voor oudere data bases van Azure SQL Database, een beheerd exemplaar van Azure SQL of Azure SQL Data Warehouse.
Beheerde exemplaar databases die zijn gemaakt via Restore, nemen de versleutelings status over van de bron database.

Transparante gegevens versleuteling versleutelt de opslag van een volledige data base met behulp van een symmetrische sleutel die de database versleutelings sleutel wordt genoemd. Deze versleutelings sleutel voor de data base wordt beveiligd door de transparante beveiliging van gegevens versleuteling. Het beveiligings programma is een door een service beheerd certificaat (door de service beheerde transparante gegevens versleuteling) of een asymmetrische sleutel die is opgeslagen in Azure Key Vault (Bring Your Own Key). U stelt de transparante gegevens versleutelings beveiliging in op server niveau voor Azure SQL Database en het Data Warehouse, en op instantie niveau voor Azure SQL Managed instance. De term *Server* verwijst naar de server en het exemplaar in dit document, tenzij anders aangegeven.

Bij het opstarten van de data base wordt de versleutelde database versleutelings sleutel ontsleuteld en vervolgens gebruikt voor ontsleuteling en opnieuw versleutelen van de database bestanden in het proces van de SQL Server data base-engine. Transparante gegevens versleuteling voert realtime-I/O-versleuteling en ontsleuteling van de gegevens op pagina niveau uit. Elke pagina wordt gedecodeerd wanneer deze in het geheugen wordt gelezen en vervolgens versleuteld voordat deze naar de schijf wordt geschreven. Zie [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)(Engelstalig) voor een algemene beschrijving van transparent Data Encryption.

SQL Server die op een virtuele Azure-machine worden uitgevoerd, kunt u ook een asymmetrische sleutel van Key Vault gebruiken. De configuratie stappen verschillen van het gebruik van een asymmetrische sleutel in SQL Database en SQL Managed instance. Zie [Extensible Key Management met behulp van Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server)voor meer informatie.

## <a name="service-managed-transparent-data-encryption"></a>Door service beheerde transparante gegevens versleuteling

In Azure is de standaard instelling voor transparante gegevens versleuteling dat de database versleutelings sleutel wordt beveiligd door een ingebouwd server certificaat. Het ingebouwde server certificaat is uniek voor elke server en het gebruikte versleutelings algoritme is AES 256. Als een Data Base zich in een geo-replicatie relatie bevindt, worden zowel de primaire als de geo-secundaire data base beveiligd door de bovenliggende server sleutel van de primaire data base. Als twee data bases zijn verbonden met dezelfde server, delen ze ook hetzelfde ingebouwde certificaat.  Micro soft roteert deze certificaten automatisch in overeenstemming met het interne beveiligings beleid en de basis sleutel wordt beveiligd door een micro soft interne geheime opslag.  Klanten kunnen SQL Database naleving controleren met interne beveiligings beleidsregels in onafhankelijke controle rapporten van derden die beschikbaar zijn via het [vertrouwens centrum van micro soft](https://servicetrust.microsoft.com/).

Micro soft verplaatst en beheert ook de sleutels naar behoefte voor geo-replicatie en herstel bewerkingen.

> [!IMPORTANT]
> Alle nieuw gemaakte SQL-data bases en beheerde exemplaar databases worden standaard versleuteld met behulp van door de service beheerde transparante gegevens versleuteling. Bestaande SQL-data bases die zijn gemaakt vóór 2017 en SQL-data bases die zijn gemaakt via Restore, geo-replicatie en database kopie, worden niet standaard versleuteld. Bestaande beheerde exemplaar databases die vóór februari 2019 zijn gemaakt, worden niet standaard versleuteld. Beheerde exemplaar databases die zijn gemaakt via Restore, nemen de versleutelings status over van de bron.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Door de klant beheerde transparante gegevens versleuteling-Bring Your Own Key

[TDe met door de klant beheerde sleutels in azure Key Vault](transparent-data-encryption-byok-azure-sql.md) kunt de database versleutelings sleutel (dek) versleutelen met een door de klant beheerde asymmetrische sleutel met de naam TDe Protector.  Dit wordt ook in het algemeen aangeduid als Bring Your Own Key-ondersteuning (BYOK) voor Transparent Data Encryption. In het BYOK-scenario wordt de TDE-Protector opgeslagen in een door de klant eigendom en beheerd [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), het op de cloud gebaseerde externe sleutel beheer systeem van Azure. De TDE-Protector kan worden [gegenereerd door de sleutel kluis of worden overgebracht naar de sleutel kluis](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) van een on-PREMISes HSM-apparaat. De TDE-DEK, die is opgeslagen op de opstart pagina van een Data Base, wordt versleuteld en ontsleuteld door de TDE-Protector, die is opgeslagen in Azure Key Vault en de sleutel kluis nooit verlaat.  Aan SQL Database moeten machtigingen worden verleend om de DEK te ontsleutelen en te versleutelen. Als machtigingen van de logische SQL-Server aan de sleutel kluis zijn ingetrokken, is een Data Base niet toegankelijk en worden alle gegevens versleuteld. Voor Azure SQL Database wordt de TDE-Protector ingesteld op het niveau van de logische SQL-Server en overgenomen door alle data bases die aan die server zijn gekoppeld. Voor [Azure SQL Managed instance](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance) (BYOK-functie in Preview) wordt de TDe-Protector ingesteld op het niveau van de instantie en overgenomen door alle *versleutelde* data bases op dat exemplaar. De term *Server* verwijst naar de server en het exemplaar in dit document, tenzij anders aangegeven.

Met TDE met Azure Key Vault-integratie kunnen gebruikers belang rijke beheer taken beheren, zoals het draaien van sleutels, sleutel kluis machtigingen, sleutel back-ups en het inschakelen van controle/rapportage op alle TDE-beveiligingen met behulp van Azure Key Vault functionaliteit. Key Vault biedt centraal sleutel beheer, maakt gebruik van nauw bewakende Hsm's (Hardware Security modules) en stelt schei ding van taken mogelijk te maken tussen het beheer van sleutels en gegevens om te voldoen aan de naleving van beveiligings beleid.
Zie [transparent Data Encryption with Azure Key Vault voor meer informatie over transparante gegevens versleuteling met Azure Key Vault Integration (Bring your own Key-ondersteuning) voor Azure SQL database, een beheerd exemplaar van SQL (BYOK-functie in Preview) en Data Warehouse. integratie](transparent-data-encryption-byok-azure-sql.md).

Als u transparante gegevens versleuteling wilt gebruiken met Azure Key Vault Integration (Bring Your Own Key-ondersteuning), raadpleegt u de hand leiding voor het [inschakelen van transparante gegevens versleuteling met behulp van uw eigen sleutel van Key Vault met behulp van Power shell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Een transparante gegevens versleuteling verplaatsen-beveiligde data base

U hoeft geen data bases te ontsleutelen voor bewerkingen binnen Azure. De transparante instellingen voor gegevens versleuteling op de bron database of primaire Data Base zijn transparant overgenomen op het doel. Beschik bare bewerkingen omvatten:

- Geo-herstel
- Self-service herstel punt in de tijd
- Herstellen van een verwijderde data base
- Actieve geo-replicatie
- Maken van een database kopie
- Back-upbestand terugzetten naar Azure SQL Managed instance

> [!IMPORTANT]
> Het is niet toegestaan om een back-up te maken van een Data Base die is versleuteld met een door een service beheerde TDE, omdat het certificaat dat voor versleuteling wordt gebruikt, niet toegankelijk is. Gebruik de functie punt-in-tijd-herstellen om dit type Data Base te verplaatsen naar een ander beheerd exemplaar.

Wanneer u een Data Base met transparante gegevens versleuteling exporteert, wordt de geëxporteerde inhoud van de data base niet versleuteld. Deze geëxporteerde inhoud wordt opgeslagen in niet-versleutelde BACPAC-bestanden. Zorg ervoor dat u de BACPAC-bestanden op de juiste wijze beveiligt en transparante gegevens versleuteling kunt inschakelen nadat het importeren van de nieuwe Data Base is voltooid.

Als het BACPAC-bestand bijvoorbeeld wordt geëxporteerd vanuit een on-premises SQL Server-exemplaar, wordt de geïmporteerde inhoud van de nieuwe Data Base niet automatisch versleuteld. Als het BACPAC-bestand wordt geëxporteerd naar een on-premises SQL Server-exemplaar, wordt de nieuwe data base ook niet automatisch versleuteld.

De enige uitzonde ring is wanneer u exporteert naar en van een SQL database. Transparante gegevens versleuteling is ingeschakeld in de nieuwe Data Base, maar het BACPAC-bestand zelf is nog steeds niet versleuteld.


## <a name="manage-transparent-data-encryption"></a>Transparante gegevens versleuteling beheren
# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Transparante gegevens versleuteling beheren in de Azure Portal.

Als u transparante gegevens versleuteling wilt configureren via de Azure Portal, moet u zijn verbonden als Azure-eigenaar, Inzender of SQL Security Manager.

U kunt transparante gegevens versleuteling in-en uitschakelen op het niveau van de data base. Als u transparante gegevens versleuteling wilt inschakelen voor een Data Base, gaat u naar de [Azure Portal](https://portal.azure.com) en meldt u zich aan met uw Azure-beheerder of Inzender account. Zoek de transparante instellingen voor gegevens versleuteling onder uw gebruikers database. Standaard wordt door service beheerde transparante gegevens versleuteling gebruikt. Er wordt automatisch een transparant gegevens versleutelings certificaat gegenereerd voor de server die de Data Base bevat. Gebruik T-SQL voor beheerd exemplaar van Azure SQL om transparante gegevens versleuteling in en uit te scha kelen voor een Data Base.

![Door service beheerde transparante gegevens versleuteling](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

U stelt de transparante gegevens versleutelings hoofd sleutel, ook wel de transparante beveiliging voor gegevens versleuteling, in op server niveau. Als u transparante gegevens versleuteling wilt gebruiken met Bring Your Own Key ondersteuning en het beveiligen van uw data bases met een sleutel van Key Vault, opent u de transparante instellingen voor gegevens versleuteling onder uw server.

![Transparante gegevens versleuteling met Bring Your Own Key-ondersteuning](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Transparante gegevens versleuteling beheren met behulp van Power shell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De Power shell-Azure Resource Manager module wordt nog steeds ondersteund door Azure SQL Database, maar alle toekomstige ontwikkeling is voor de module AZ. SQL. Zie [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)voor deze cmdlets. De argumenten voor de opdrachten in de module AZ en in de AzureRm-modules zijn aanzienlijk identiek.

Als u transparante gegevens versleuteling wilt configureren via Power shell, moet u zijn verbonden als Azure-eigenaar, Inzender of SQL Security Manager.

### <a name="cmdlets-for-azure-sql-database-and-data-warehouse"></a>Cmdlets voor Azure SQL Database en Data Warehouse

Gebruik de volgende cmdlets voor Azure SQL Database en het Data Warehouse:

| Cmdlet | Beschrijving |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Hiermee wordt de transparante gegevens versleuteling voor een data base in-of uitgeschakeld|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Hiermee wordt de transparante status van gegevens versleuteling opgehaald voor een Data Base |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controleert de voortgang van de versleuteling voor een Data Base |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Voegt een Key Vault sleutel toe aan een SQL Server-exemplaar |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Hiermee worden de Key Vault sleutels voor een Azure SQL Database Server opgehaald  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Hiermee wordt de transparante beveiliging voor gegevens versleuteling ingesteld voor een SQL Server-exemplaar |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Hiermee wordt de transparante beveiliging van gegevens versleuteling opgehaald |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Hiermee wordt een Key Vault sleutel uit een SQL Server-exemplaar verwijderd |
|  | |

> [!IMPORTANT]
> Voor Azure SQL Managed instance gebruikt u de T-SQL [ALTER data base](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) -opdracht om transparante gegevens versleuteling in en uit te scha kelen op een database niveau en voor [beeld Power shell-script](transparent-data-encryption-byok-azure-sql-configure.md) voor het beheren van transparante gegevens versleuteling op een exemplaar niveau te controleren.

# <a name="transact-sqltabazure-transactsql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Transparante gegevens versleuteling beheren met behulp van Transact-SQL.

Maak verbinding met de data base met behulp van een aanmelding die een beheerder of lid is van de rol **DBManager** in de hoofd database.

| Opdracht | Beschrijving |
| --- | --- |
| [ALTER data base (Azure SQL Database)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | VERSLEUTELING instellen in-of uitschakelen voor het versleutelen of ontsleutelen van een Data Base |
| [sys. DM _database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelings status van een Data Base en de bijbehorende versleutelings sleutels voor de data base |
| [sys. DM _pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Retourneert informatie over de versleutelings status van elk data warehouse-knoop punt en de bijbehorende data base-versleutelings sleutels |
|  | |

U kunt niet overstappen op de transparante beveiliging van gegevens versleuteling naar een sleutel van Key Vault met behulp van Transact-SQL. Gebruik Power shell of de Azure Portal.

# <a name="rest-apitabazure-restapi"></a>[REST API](#tab/azure-RESTAPI)
Transparante gegevens versleuteling beheren met behulp van de REST API.

Als u transparante gegevens versleuteling wilt configureren via de REST API, moet u zijn verbonden als Azure-eigenaar, Inzender of SQL Security Manager.
Gebruik de volgende reeks opdrachten voor Azure SQL Database en het Data Warehouse:

| Opdracht | Beschrijving |
| --- | --- |
|[Server maken of bijwerken](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Hiermee wordt een Azure Active Directory identiteit toegevoegd aan een SQL Server-exemplaar (wordt gebruikt om toegang te verlenen aan Key Vault)|
|[Server sleutel maken of bijwerken](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Voegt een Key Vault sleutel toe aan een SQL Server-exemplaar|
|[Server sleutel verwijderen](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Hiermee wordt een Key Vault sleutel uit een SQL Server-exemplaar verwijderd|
|[Server sleutels ophalen](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Hiermee wordt een specifieke Key Vault sleutel opgehaald uit een SQL Server-exemplaar|
|[Server sleutels op server weer geven](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Hiermee worden de Key Vault sleutels voor een SQL Server exemplaar opgehaald |
|[Versleutelings beveiliging maken of bijwerken](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Hiermee wordt de transparante beveiliging voor gegevens versleuteling ingesteld voor een SQL Server-exemplaar|
|[Versleutelings beveiliging ophalen](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Hiermee wordt de transparante beveiliging van gegevens versleuteling opgehaald voor een SQL Server-exemplaar|
|[Versleutelings beveiligingen op server vermelden](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Hiermee worden de transparante beveiligingen voor gegevens versleuteling opgehaald voor een SQL Server-exemplaar |
|[Transparent Data Encryption configuratie maken of bijwerken](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Hiermee wordt de transparante gegevens versleuteling voor een data base in-of uitgeschakeld|
|[Transparent Data Encryption-configuratie ophalen](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Hiermee wordt de transparante configuratie voor gegevens versleuteling opgehaald voor een Data Base|
|[Resultaten van Transparent Data Encryption configuratie weer geven](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Hiermee wordt het versleutelings resultaat voor een Data Base opgehaald|

## <a name="next-steps"></a>Volgende stappen

- Zie [transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption)(Engelstalig) voor een algemene beschrijving van transparent Data Encryption.
- Zie [transparent Data Encryption with Bring your own key support](transparent-data-encryption-byok-azure-sql.md)voor meer informatie over transparante gegevens versleuteling met Bring your own Key ondersteuning voor Azure SQL database, Azure SQL Managed instance en Data Warehouse.
- Als u transparante gegevens versleuteling met Bring Your Own Key-ondersteuning wilt gebruiken, raadpleegt u de instructies om [transparante gegevens versleuteling in te scha kelen met behulp van uw eigen sleutel vanuit Key Vault met behulp van Power shell](transparent-data-encryption-byok-azure-sql-configure.md).
- Zie de [pagina Key Vault documentatie](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)voor meer informatie over Key Vault.
