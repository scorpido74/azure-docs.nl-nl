---
title: Excel verbinden met één database
description: Meer informatie over het verbinden van Microsoft Excel met één database in Azure SQL-database. Gegevens importeren in Excel voor rapportage en gegevens verkenning.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: joseidz
ms.author: craigg
ms.reviewer: ''
ms.date: 02/12/2019
ms.openlocfilehash: de5f23bf1e8acd8a5fcd0cf8e1526f88667800c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73827131"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Excel verbinden met één database in Azure SQL-database en een rapport maken

Verbind Excel met één database in Azure SQL Database en importeer gegevens en maak tabellen en grafieken op basis van waarden in de database. In deze zelfstudie stelt u de verbinding tussen Excel en een databasetabel in, slaat u het bestand met de gegevens en de verbindingsinformatie voor Excel op en maakt u vervolgens een draaigrafiek uit de databasewaarden.

Je hebt één database nodig voordat je aan de slag gaat. Als u er geen hebt, [raadpleegt u Eén database maken](sql-database-single-database-get-started.md) en [IP-firewall op serverniveau maken](sql-database-server-level-firewall-rule.md) om binnen enkele minuten één database met voorbeeldgegevens operationeel te krijgen.

In dit artikel importeert u voorbeeldgegevens in Excel uit dat artikel, maar u vergelijkbare stappen volgen met uw eigen gegevens.

U hebt ook een kopie van Excel nodig. Dit artikel gebruikt [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Excel verbinden met een SQL-database en gegevens laden

1. Open Excel om het programme te verbinden met SQL Database, en maak een nieuwe werkmap of open een bestaande Excel-werkmap.
2. Selecteer op de menubalk boven aan de pagina het tabblad **Gegevens,** selecteer **Gegevens ophalen,** selecteer Uit Azure en selecteer vervolgens **Uit Azure SQL Database**. 

   ![Selecteer gegevensbron: Excel verbinden met SQL Database.](./media/sql-database-connect-excel/excel_data_source.png)

   De wizard Gegevensverbinding wordt geopend.
3. In het dialoogvenster **Verbinding maken met databaseserver** typt u de **servernaam** van de SQL Database die u wilt verbinden in het formulier <*servername*>**. database.windows.net**. Bijvoorbeeld, **msftestserver.database.windows.net**. Voer eventueel de naam van uw database in. Selecteer **OK** om het venster met referenties te openen. 

   ![Dialoogvenster Verbinding maken met databaseserver](media/sql-database-connect-excel/server-name.png)

4. Selecteer **database** aan de linkerkant in het dialoogvenster **SQL Server-database** en voer vervolgens uw **gebruikersnaam** en **wachtwoord** in voor de SQL Database-server waarmee u verbinding wilt maken. Selecteer **Verbinding maken** om de Navigator te **openen**. 

   ![Typ de servernaam en aanmeldingsreferenties in](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Afhankelijk van uw netwerkomgeving kunt u mogelijk geen verbinding maken of kan de verbinding worden verbroken als de SQL Database-server geen verkeer van het IP-adres van de client toestaat. Ga naar de [Azure-portal](https://portal.azure.com/), klik op SQL-servers, klik op uw server, klik op firewall onder instellingen en voeg uw client-IP-adres toe. Zie [Firewallinstellingen configureren](sql-database-configure-firewall-settings.md) voor meer informatie.

5. Selecteer in de **Navigator**de database waarmee u wilt werken in de lijst, selecteer de tabellen of weergaven waarmee u wilt werken (we hebben **vGetAllCategories**geselecteerd) en selecteer vervolgens **Laden** om de gegevens uit uw database naar uw Excel-spreadsheet te verplaatsen.

    ![Selecteer een database en tabel.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importeer de gegevens in Excel en maak een draaigrafiek.

Nu u de verbinding hebt gemaakt, hebt u verschillende opties waarmee u de gegevens laden. Met de volgende stappen wordt bijvoorbeeld een draaigrafiek gemaakt op basis van de gegevens in uw SQL-database. 

1. Volg de stappen in de vorige sectie, maar selecteer deze keer in plaats van **Laden**te selecteren, **laden in** de vervolgkeuzelijst **Laden.**
2. Selecteer vervolgens hoe u deze gegevens wilt weergeven in uw werkmap. We hebben **draaigrafiek** gekozen. U kunt ook kiezen voor **Nieuw werkblad** of **Deze gegevens toevoegen aan een gegevensmodel**. Zie voor meer informatie over gegevensmodellen [Een gegevensmodel maken in Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![De indeling kiezen voor gegevens in Excel](./media/sql-database-connect-excel/import-data.png)

    Het werkblad heeft nu een lege draaitabel en grafiek.
3. Onder **PivotTable-velden**, selecteert u alle selectievakjes voor de velden die u wilt weergeven.

    ![Databaserapport configureren.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Als u andere Excel-werkmappen en -werkbladen aan de database wilt koppelen, selecteert u het tabblad **Gegevens** en selecteert u **Recente bronnen** om het dialoogvenster Recente **bronnen** te starten. Kies van daaruit de verbinding die u in de lijst hebt gemaakt en klik op **Openen**.
> ![Dialoogvenster Recente bronnen](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Een permanente verbinding maken met odc-bestand .odc

Als u de verbindingsgegevens permanent wilt opslaan, u een odc-bestand maken en deze verbinding een selecteerbare optie maken in het dialoogvenster **Bestaande verbindingen.** 

1. Selecteer op de menubalk boven aan de pagina het tabblad **Gegevens** en selecteer **Bestaande verbindingen** om het dialoogvenster **Bestaande verbindingen** te starten. 
   1. Selecteer **Bladeren voor meer** informatie om het dialoogvenster **Gegevensbron selecteren te** openen.   
   2. Selecteer het **bestand +NewSqlServerConnection.odc** en selecteer **Openen** om de **wizard Gegevensverbinding te openen.**

      ![Dialoogvenster Nieuwe verbinding](media/sql-database-connect-excel/new-connection.png)

2. Typ in de **wizard Gegevensverbinding**uw servernaam en uw SQL-databasereferenties in. Selecteer **Volgende**. 
   1. Selecteer de database die uw gegevens bevat in de vervolgkeuzelijst. 
   2. Selecteer de tabel of weergave waarin u geïnteresseerd bent. We kozen voor vGetAllCategories.
   3. Selecteer **Volgende**. 

      ![Wizard Gegevensverbinding](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Selecteer de locatie van uw bestand, de **bestandsnaam**en de **vriendelijke naam** in het volgende scherm van de wizard Gegevensverbinding. U er ook voor kiezen om het wachtwoord in het bestand op te slaan, maar dit kan uw gegevens mogelijk blootstellen aan ongewenste toegang. Selecteer **Voltooien** wanneer u klaar bent. 

    ![Gegevensverbinding opslaan](media/sql-database-connect-excel/save-data-connection.png)

4. Selecteer hoe u uw gegevens wilt importeren. We kozen ervoor om een draaitabel te doen. U ook de eigenschappen van de verbinding wijzigen door **Eigenschappen**te selecteren. Selecteer **OK** wanneer u klaar bent. Als u er niet voor hebt gekozen om het wachtwoord met het bestand op te slaan, wordt u gevraagd uw referenties in te voeren. 

    ![Gegevens importeren](media/sql-database-connect-excel/import-data2.png)

5. Controleer of uw nieuwe verbinding is opgeslagen door het tabblad **Gegevens** uit te breiden en **Bestaande verbindingen te**selecteren. 

    ![Bestaande verbinding](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Volgende stappen

* Leer hoe u [SQL Database koppelt met SQL Server Management Studio](sql-database-connect-query-ssms.md) voor geavanceerd uitvoeren van query's en analyses.
* Meer informatie over de voordelen van [elastische groepen](sql-database-elastic-pool.md).
* Leer hoe u [ een webtoepassing maakt die verbinding maakt met SQL Database op de back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
