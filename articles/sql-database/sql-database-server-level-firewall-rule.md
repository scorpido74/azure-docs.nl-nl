---
title: Een firewallregel op serverniveau maken - Azure SQL Database| Microsoft Docs
description: Een firewallregel maken op SQL Database-serverniveau voor enkele en pooldatabases
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: 27b554159fca2043fbd0c853e260a66294c10387
ms.sourcegitcommit: a7a9d7f366adab2cfca13c8d9cbcf5b40d57e63a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71162301"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Quickstart: Een firewallregel op serverniveau maken voor uw individuele en pooldatabases met behulp van de Azure-portal

Deze quickstart beschrijft hoe u een [firewallregel op serverniveau](sql-database-firewall-configure.md) maakt voor individuele en pooldatabases in Azure SQL Database met behulp van de Azure-portal, zodat u verbinding kunt maken met databaseservers, individuele databases en elastische pools en de bijbehorende databases. Een firewallregel is vereist om verbinding te maken vanaf andere Azure-resources en vanaf on-premises bronnen.

## <a name="prerequisites"></a>Vereisten

Deze quickstart gebruikt de resources die zijn gemaakt in [Create a single database using the Azure portal](sql-database-single-database-get-started.md) (Een Azure SQL-database maken in de Azure-portal) als uitgangspunt.

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij [Azure Portal](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Een IP-firewallregel op serverniveau maken

De service SQL Database maakt een firewall op het niveau van de databaseserver voor individuele databases en pooldatabases. De firewall voorkomt dat clienttoepassingen verbinding maken met de server of met een individuele database of database in een pool, tenzij u een IP-firewallregel maakt om de firewall te openen. Voor een verbinding vanaf een IP-adres buiten Azure maakt u een firewallregel voor een specifiek IP-adres of bereik van adressen dat toegang mag hebben. Meer informatie over IP-firewallregels op serverniveau en databaseniveau vindt u in [IP-firewallregels voor Azure SQL Database op server- en databaseniveau](sql-database-firewall-configure.md).

> [!NOTE]
> SQL Database communiceert via poort 1433. Als u verbinding probeert te maken vanuit een bedrijfsnetwerk, is uitgaand verkeer via poort 1433 mogelijk niet toegestaan vanwege de firewall van het netwerk. In dat geval kunt u geen verbinding maken met uw Azure SQL Database-server, tenzij de IT-afdeling poort 1433 openstelt.
> [!IMPORTANT]
> De firewallregel 0.0.0.0 zorgt ervoor dat alle Azure-services voldoen worden aan de firewallregel op serverniveau en proberen om via de server verbinding te maken met een individuele database of pooldatabase. 

Volg deze stappen om een IP-firewallregel op serverniveau te maken voor het IP-adres van de client en om connectiviteit via de SQL Database-firewall alleen voor uw IP-adres toe te staan.

1. Wanneer de implementatie van [Vereiste Azure SQL-database](#prerequisites) is voltooid, selecteert u **SQL-databases** in het menu links en kiest u **mySampleDatabase** op de pagina **SQL-databases**. De overzichtspagina voor uw database wordt geopend, met de volledig gekwalificeerde servernaam (zoals **mynewserver20170824.database.windows.net**) en opties voor verdere configuratie.

2. Kopieer deze volledig gekwalificeerde servernaam zodat u deze kunt gebruiken wanneer u in andere quickstarts verbinding maakt met de server en de bijbehorende databases.

   ![servernaam](./media/sql-database-get-started-portal/server-name.png)

3. Selecteer **Serverfirewall instellen** op de werkbalk. De pagina **Firewallinstellingen** voor de databaseserver wordt geopend.

   ![IP-firewallregel op serverniveau](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Kies **IP van client toevoegen** op de werkbalk om uw huidige IP-adres toe te voegen aan een nieuwe IP-firewallregel op serverniveau. Een IP-firewallregel op serverniveau kan poort 1433 openen voor een individueel IP-adres of voor een bereik van IP-adressen.

   > [!IMPORTANT]
   > Standaard is toegang via de SQL Database firewall uitgeschakeld voor alle Azure-Services. Kies **op** deze pagina als u toegang wilt inschakelen voor alle Azure-Services.
   >

5. Selecteer **Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 wordt geopend op de SQL Database-server.

6. Sluit de pagina **Firewallinstellingen**.

Nu kunt u SQL Server Management Studio of een ander hulpprogramma naar keuze gebruiken om vanaf dit IP-adres via het serverbeheerdersaccount dat eerder is gemaakt verbinding te maken met de SQL Database-server en de bijbehorende databases.

## <a name="clean-up-resources"></a>Resources opschonen

Sla deze resources op als u verder wilt gaan met [Volgende stappen](#next-steps) en wilt leren hoe u op verschillende manieren verbinding kunt maken met uw database en deze kunt bevragen. Als u echter de resources wilt verwijderen die u hebt gemaakt in deze quickstart, voert u de volgende stappen uit.

1. Selecteer in het menu links in de Azure-portal **Resourcegroepen** en selecteer vervolgens **myResourceGroup**.
2. Selecteer op de pagina van uw resourcegroep de optie **Verwijderen**, typ **myResourceGroup** in het tekstvak en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

- Nu u een database hebt, kunt u [verbinding maken met een hulpprogramma of taal naar keuze en daarmee query's uitvoeren](sql-database-connect-query.md), waaronder
  - [Verbinding maken en query's uitvoeren met behulp van SQL Server Management Studio](sql-database-connect-query-ssms.md)
  - [Verbinding maken en query's uitvoeren met behulp van Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Voor informatie over het ontwerpen van uw eerste database, het maken van tabellen en het invoegen van gegevens kunt u een van deze zelfstudies gebruiken:
  - [Uw eerste individuele database in Azure SQL Database ontwerpen met SSMS](sql-database-design-first-database.md)
  - [Een individuele database ontwerpen in Azure SQL Database en verbinden met C# en ADO.NET](sql-database-design-first-database-csharp.md)
