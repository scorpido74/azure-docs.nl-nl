---
title: 'Uw eerste relationele database C ontwerpen #'
description: Leer hoe u uw eerste relationele database in een individuele database in Azure SQL Database kunt ontwerpen met C# met behulp van ADO.NET.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 0f1140bbefc7508666e763fcd4f1a04ba48cdfdd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75354946"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Zelfstudie: Ontwerp een relationele database in één database binnen Azure SQL Database C&#x23; en ADO.NET

Azure SQL Database is een relationele DBaaS (database-as-a-service) in Microsoft Cloud (Azure). In deze zelfstudie leert u hoe u Azure Portal en ADO.NET met Visual Studio gebruikt voor de volgende taken:

> [!div class="checklist"]
> * Een individuele database maken met Azure Portal*
> * Een IP-firewallregel op serverniveau instellen met Azure Portal
> * Verbinding maken met de database met ADO.NET en Visual Studio
> * Tabellen maken met ADO.NET
> * Gegevens invoegen, bijwerken en verwijderen met ADO.NET
> * Querygegevens ADO.NET

*Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

> [!TIP]
> Met de volgende Microsoft Learn-module u gratis leren hoe u [een ASP.NET-toepassing ontwikkelen en configureren die een Azure SQL-database opvraagt,](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)inclusief het maken van een eenvoudige database.

## <a name="prerequisites"></a>Vereisten

Een installatie van [Visual Studio 2019](https://www.visualstudio.com/downloads/) of hoger.

## <a name="create-a-blank-single-database"></a>Een lege individuele database maken

Een individuele Azure SQL-database wordt gemaakt met een gedefinieerde set reken- en opslagresources. De database wordt gemaakt in een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) en wordt beheerd met een [databaseserver](sql-database-servers.md).

Volg deze stappen om een lege individuele database te maken.

1. Klik **op Een resource maken** in de linkerbovenhoek van de Azure-portal.
2. Selecteer op de pagina **Nieuw****Databases** in de sectie Azure Marketplace en klik vervolgens op **SQL Database** in de sectie **Aanbevolen**.

   ![lege database maken](./media/sql-database-design-first-database/create-empty-database.png)

3. Vul het **SQL Database-formulier** in met de volgende informatie, zoals weergegeven op de vorige afbeelding:

    | Instelling       | Voorgestelde waarde | Beschrijving |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Databasenaam** | *yourDatabase* | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige databasenamen. |
    | **Abonnement** | *yourSubscription*  | Zie [Abonnementen](https://account.windowsazure.com/Subscriptions) voor meer informatie over uw abonnementen. |
    | **Resourcegroep** | *yourResourceGroup* | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige namen van resourcegroepen. |
    | **Bron selecteren** | Lege database | Hiermee geeft u aan dat er een lege database moet worden gemaakt. |

4. Klik op **Server** als u een bestaande databaseserver wilt gebruiken of een nieuwe databaseserver wilt maken en configureren. Selecteer een bestaande server of klik op **Een nieuwe server maken** en vul het formulier **Nieuwe server** in met de volgende gegevens:

    | Instelling       | Voorgestelde waarde | Beschrijving |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Servernaam** | Een wereldwijd unieke naam | Zie [Naming conventions](/azure/architecture/best-practices/resource-naming) (Naamgevingsconventies) voor geldige servernamen. |
    | **Inloggen voor serverbeheerder** | Een geldige naam | Zie [Database-id's](/sql/relational-databases/databases/database-identifiers) voor geldige aanmeldingsnamen. |
    | **Wachtwoord** | Een geldig wachtwoord | Uw wachtwoord moet uit minstens acht tekens bestaan en moet tekens bevatten uit drie van de volgende categorieën: hoofdletters, kleine letters, cijfers en niet-alfanumerieke tekens. |
    | **Locatie** | Een geldige locatie | Zie [Azure-regio's](https://azure.microsoft.com/regions/)voor informatie over regio's . |

    ![database-server maken](./media/sql-database-design-first-database/create-database-server.png)

5. Klik **op Selecteren**.
6. Klik op **Prijscategorie** om de servicelaag, het aantal DTU's of vCores en de hoeveelheid opslag op te geven. U kunt de opties bekijken voor de hoeveelheid DTU's/vCores en opslag die voor elke servicelaag beschikbaar zijn.

    Als u de servicelaag, het aantal DTU's of vCores en de hoeveelheid opslagruimte hebt geselecteerd, klikt u op **Toepassen**.

7. Voer een **sortering** in voor de lege database (gebruik de standaardwaarde in deze zelfstudie). Zie [Collations](/sql/t-sql/statements/collations) (Sorteringen) voor meer informatie over sorteringen

8. Nu u het **SQL Database**-formulier hebt ingevuld, klikt u op **Maken** om de individuele database in te richten. Deze stap kan enkele minuten duren.

9. Klik op de werkbalk op **Meldingen** om het implementatieproces te bewaken.

   ![melding](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Een IP-firewallregel op serverniveau maken

De service SQL Database maakt een IP-firewall op serverniveau. De firewall voorkomt dat externe toepassingen en hulpprogramma's verbinding maken met de server of databases op de server, tenzij een firewallregel hun IP via de firewall toestaat. Als u externe connectiviteit voor uw individuele database wilt inschakelen, moet u eerst een IP-firewallregel voor uw IP-adres (of IP-adresbereik) toevoegen. Volg deze stappen als u een [IP-firewallregel op SQL Database-serverniveau](sql-database-firewall-configure.md) wilt maken.

> [!IMPORTANT]
> De service SQL Database communiceert via poort 1433. Als u verbinding met deze service probeert te maken vanuit een bedrijfsnetwerk, wordt uitgaand verkeer via poort 1433 mogelijk niet toegestaan door de firewall van uw netwerk. In dat geval kunt u geen verbinding maken met uw individuele database, tenzij de beheerder poort 1433 openstelt.

1. Wanneer de implementatie is voltooid, klikt u op **SQL Databases** in het menu aan de linkerkant. Klik vervolgens op de pagina **SQL Databases** op *yourDatabase*. De overzichtspagina voor de database wordt geopend, met de volledig gekwalificeerde **servernaam** (bijvoorbeeld *yourserver.database.windows.net*) en opties voor verdere configuratie.

2. Kopieer vanuit SQL Server Management Studio deze volledig gekwalificeerde servernaam om verbinding te maken met de server en de databases.

   ![servernaam](./media/sql-database-design-first-database/server-name.png)

3. Klik op de werkbalk op **Serverfirewall instellen**. De pagina **Firewall-instellingen** voor de SQL Database-server wordt geopend.

   ![IP-firewallregel op serverniveau](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Klik op **IP van client toevoegen** op de werkbalk om uw huidige IP-adres aan een nieuwe IP-firewallregel toe te voegen. Een IP-firewallregel kan poort 1433 openen voor een afzonderlijk IP-adres of voor een aantal IP-adressen.

5. Klik op **Opslaan**. Er wordt een IP-firewallregel op serverniveau gemaakt voor uw huidige IP-adres waarbij poort 1433 wordt geopend op de SQL Database-server.

6. Klik op **OK** en sluit de pagina **Firewallinstellingen**.

Uw IP-adres wordt niet meer geblokkeerd via de IP-firewall. U kunt nu verbinding maken met uw individuele database met SQL Server Management Studio of een ander hulpprogramma naar keuze. Gebruik het beheerdersaccount voor de server dat u eerder hebt gemaakt.

> [!IMPORTANT]
> Voor alle Azure-services is toegang via de IP-firewall van SQL Database standaard ingeschakeld. Klik op **UIT** op deze pagina om dit voor alle Azure-services uit te schakelen.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u kennisgemaakt met eenvoudige databasetaken, zoals het maken van een database en tabellen, het verbinden van de database, het laden van gegevens en het uitvoeren van query's. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een database maken
> * Een firewallregel instellen
> * Verbinding maken met de database met behulp van [Visual Studio en C#](sql-database-connect-query-dotnet-visual-studio.md)
> * Tabellen maken
> * Gegevens invoegen, bijwerken, verwijderen en opvragen

Ga naar de volgende zelfstudie voor meer informatie over gegevensmigratie.

> [!div class="nextstepaction"]
> [SQL Server migreren naar een offline exemplaar van Azure SQL Database met behulp van DMS](../dms/tutorial-sql-server-to-azure-sql.md)
