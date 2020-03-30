---
title: Snel start verbinding maken en query's
description: In deze snelstartgidsen voor Azure SQL Database wordt beschreven hoe u verbinding maakt met een Azure SQL-database en vervolgens een query uitvoert.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 647bdcf5c8c49b5c942419c78155ed4f61c848bb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240560"
---
# <a name="quickstarts-azure-sql-database-connect-and-query"></a>Snelstarts: Verbinding maken en query's in Azure SQL Database

Het volgende document bevat koppelingen naar voorbeelden van Azure die laten zien hoe u verbinding maakt met een Azure SQL-database en vervolgens een query uitvoert. Het bevat ook enkele aanbevelingen voor Transport Layer Security.

## <a name="quickstarts"></a>Snelstartgidsen

| |  |
|---|---|
|[SQL Server Management Studio](sql-database-connect-query-ssms.md)|In deze Quick Start ziet u hoe u SSMS gebruikt om verbinding te maken met een Azure SQL-database en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)|In deze quickstart wordt uitgelegd hoe u Azure Data Studio gebruikt om verbinding te maken met een Azure SQL-database, waarna u met behulp van Transact-SQL-instructies (T-SQL) de TutorialDB maakt die wordt gebruikt in de zelfstudies voor Azure Data Studio.|
|[Azure-portal](sql-database-connect-query-portal.md)|In deze quickstart ziet u hoe u de queryeditor gebruikt om verbinding te maken met een SQL-database en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[Visual Studio-code](sql-database-connect-query-vscode.md)|In deze Quick Start ziet u hoe u Visual Studio Code gebruikt om verbinding te maken met een Azure SQL-database en vervolgens Transact-SQL-instructies gebruikt om gegevens in de database te zoeken, in te voegen, bij te werken en te verwijderen.|
|[.NET met Visual Studio](sql-database-connect-query-dotnet-visual-studio.md)|In deze snelstart wordt gedemonstreerd hoe u .NET Framework gebruikt om een C#-programma te maken met Visual Studio dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[.NET-kern](sql-database-connect-query-dotnet-core.md)|In deze snelstart wordt gedemonstreerd hoe u .NET Core gebruikt in Windows/Linux/macOS om een C#-programma te maken dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor de gegevens.|
|[OK](sql-database-connect-query-go.md)|In deze snelstart ziet u hoe u Go kunt gebruiken om verbinding te maken met een Azure SQL-database. Bovendien worden er Transact-SQL-instructies voor het doorzoeken en wijzigen van gegevens beschreven.|
|[Java](sql-database-connect-query-java.md)|In deze snelstart wordt gedemonstreerd hoe u Java gebruikt om verbinding te maken met een Azure SQL-database, en hoe u vervolgens Transact-SQL-instructies gebruikt om een query uit te voeren voor gegevens.|
|[Node.js](sql-database-connect-query-nodejs.md)|In deze snelstart wordt gedemonstreerd hoe u Node.js gebruikt om een programma te maken dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor gegevens.|
|[Php](sql-database-connect-query-php.md)|In deze snelstart wordt gedemonstreerd hoe u PHP gebruikt om een programma te maken dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[Python](sql-database-connect-query-python.md)|In deze snelstart wordt gedemonstreerd hoe u Python gebruikt om verbinding te maken met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om een query uit te voeren voor de gegevens. |
|[Ruby](sql-database-connect-query-ruby.md)|In deze snelstart wordt gedemonstreerd hoe u Ruby gebruikt om een programma te maken dat verbinding maakt met een Azure SQL-database, en hoe u Transact-SQL-instructies gebruikt om gegevens te doorzoeken.|
|[R](sql-database-connect-query-r.md)|Deze quickstart laat zien hoe u R gebruiken met Azure SQL Database Machine Learning Services om een programma te maken om verbinding te maken met een Azure SQL-database en Transact-SQL-instructies te gebruiken om gegevens op te vragen.|
|||

## <a name="tls-considerations-for-sql-database-connectivity"></a>TLS-overwegingen voor de connectiviteit van SQL Database
Transport Layer Security (TLS) wordt gebruikt door alle stuurprogramma's die Microsoft aanbiedt of ondersteunt voor het maken van verbinding met Azure SQL Database. Er is geen speciale configuratie nodig. Voor alle verbindingen met SQL Server of Azure SQL Database raden we het aan om de volgende configuraties of een equivalent daarvan in te stellen voor alle toepassingen:

 - **Versleutelen = Aan**
 - **TrustServerCertificate = Uit**

Sommige systemen gebruiken andere, maar wel vergelijkbare sleutelwoorden voor deze configuratiesleutelwoorden. Deze configuraties zorgen ervoor dat het clientstuurprogramma de identiteit controleert van het TLS-certificaat dat afkomstig is van de server.

We raden u ook aan om TLS 1.1 en 1.0 op de client uit te schakelen als u moet voldoen aan de Payment Card Industry - Data Security Standard (PCI-DSS).

Stuurprogramma's die niet van Microsoft zijn, maken mogelijk niet standaard gebruik van TLS. Dit kan een factor zijn bij het maken van verbinding met Azure SQL Database. Bij toepassingen met ingesloten stuurprogramma's is het mogelijk niet toegestaan om deze verbindingsinstellingen te beheren. Wij raden u aan om de beveiliging van zulke stuurprogramma’s en toepassingen te controleren voordat u ze gebruikt op systemen die interactie hebben met gevoelige gegevens.

## <a name="libraries"></a>Bibliotheken

U kunt verschillende bibliotheken en frameworks gebruiken om verbinding te maken met Azure SQL Database. Bekijk onze [Aan de slag-zelfstudies](https://aka.ms/sqldev) om snel aan de slag te gaan met programmeertalen zoals C#, Java, Node.js, PHP en Python. Bouw vervolgens een app met behulp van SQL Server op Linux of Windows of Docker op macOS.

De volgende tabel bevat connectiviteitsbibliotheken of *stuurprogramma's* die clienttoepassingen kunnen gebruiken vanuit een groot aantal talen om verbinding te maken met en gebruik te maken van SQL Server on-premises of in de cloud. U kunt deze gebruiken in Linux, Windows of Docker en om verbinding te maken met Azure SQL Database en Azure SQL Data Warehouse. 

| Taal | Platform | Aanvullende bronnen | Download | Aan de slag |
| :-- | :-- | :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Microsoft ADO.NET voor SQL Server](https://docs.microsoft.com/sql/connect/ado-net/microsoft-ado-net-sql-server) | [Downloaden](https://www.microsoft.com/net/download/) | [Slag](https://www.microsoft.com/sql-server/developer-get-started/csharp/ubuntu)
| Java | Windows, Linux, macOS | [Microsoft JDBC-stuurprogramma voor SQL Server](https://msdn.microsoft.com/library/mt484311.aspx) | [Downloaden](https://go.microsoft.com/fwlink/?linkid=852460) |  [Slag](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu)
| PHP | Windows, Linux, macOS| [PHP SQL-stuurprogramma voor SQL Server](https://docs.microsoft.com/sql/connect/php/microsoft-php-driver-for-sql-server) | [Downloaden](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server) | [Slag](https://www.microsoft.com/sql-server/developer-get-started/php/ubuntu/)
| Node.js | Windows, Linux, macOS | [Node.js-stuurprogramma voor SQL Server](https://msdn.microsoft.com/library/mt652093.aspx) | [Installeren](https://msdn.microsoft.com/library/mt652094.aspx) |  [Slag](https://www.microsoft.com/sql-server/developer-get-started/node/ubuntu)
| Python | Windows, Linux, macOS | [Python SQL-stuurprogramma](https://msdn.microsoft.com/library/mt652092.aspx) | Installatieopties: <br/> \* [pymssql](https://msdn.microsoft.com/library/mt694094.aspx) <br/> \* [pyodbc](https://msdn.microsoft.com/library/mt763257.aspx) |  [Slag](https://www.microsoft.com/sql-server/developer-get-started/python/ubuntu)
| Ruby | Windows, Linux, macOS | [Robijnstuurprogramma voor SQL Server](https://msdn.microsoft.com/library/mt691981.aspx) | [Installeren](https://msdn.microsoft.com/library/mt711041.aspx) | [Slag](https://www.microsoft.com/sql-server/developer-get-started/ruby/ubuntu)
| C++ | Windows, Linux, macOS | [Microsoft ODBC-stuurprogramma voor SQL Server](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) | [Downloaden](https://msdn.microsoft.com/library/mt654048(v=sql.1).aspx) |  

De volgende tabel bevat voorbeelden van ORM-frameworks (Object-Relational Mapping) en webframeworks die clienttoepassingen kunnen gebruiken met SQL Server on-premises of in de cloud. U kunt de frameworks gebruiken in Linux, Windows of Docker en om verbinding te maken met SQL Database en SQL Data Warehouse. 

| Taal | Platform | ORM('s) |
| :-- | :-- | :-- |
| C# | Windows, Linux, macOS | [Entity Framework](https://docs.microsoft.com/ef)<br>[Entity Framework Core](https://docs.microsoft.com/ef/core/index) |
| Java | Windows, Linux, macOS |[Hibernate ORM](https://hibernate.org/orm)|
| PHP | Windows, Linux, macOS | [Laravel (Eloquent)](https://laravel.com/docs/eloquent)<br>[Doctrine](https://www.doctrine-project.org/projects/orm.html) |
| Node.js | Windows, Linux, macOS | [Sequelize ORM](https://docs.sequelizejs.com) |
| Python | Windows, Linux, macOS |[Django](https://www.djangoproject.com/) |
| Ruby | Windows, Linux, macOS | [Ruby on Rails](https://rubyonrails.org/) |
||||

## <a name="next-steps"></a>Volgende stappen

- Ga naar [Connectiviteitsarchitectuur van Azure SQL Database](sql-database-connectivity-architecture.md) voor informatie over connectiviteitsarchitectuur.
- [SQL Server-stuurprogramma's](https://msdn.microsoft.com/library/mt654049.aspx) zoeken die worden gebruikt om verbinding te maken vanuit clienttoepassingen
- Verbinding maken met SQL Database:
  - [Verbinding maken met SQL Database met behulp van .NET (C#)](sql-database-connect-query-dotnet.md) 
  - [Verbinding maken met SQL Database met behulp van PHP](sql-database-connect-query-php.md) 
  - [Verbinding maken met SQL Database met behulp van Node.js](sql-database-connect-query-nodejs.md) 
  - [Verbinding maken met SQL Database met behulp van Java](sql-database-connect-query-java.md) 
  - [Verbinding maken met SQL Database met behulp van Python](sql-database-connect-query-python.md)
  - [Verbinding maken met SQL Database met behulp van Ruby](sql-database-connect-query-ruby.md)
- Voorbeelden van logische code voor opnieuw proberen:
  - [Flexibel verbinding maken met SQL via ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
  - [Flexibel verbinding maken met SQL via PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: https://docs.microsoft.com/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: https://docs.microsoft.com/sql/connect/php/step-4-connect-resiliently-to-sql-with-php
