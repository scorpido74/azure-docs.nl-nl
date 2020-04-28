---
title: Excel verbinden met één data base
description: Meer informatie over hoe u micro soft Excel verbindt met één data base in Azure SQL database. Gegevens importeren in Excel voor rapportage en gegevens verkenning.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "73827131"
---
# <a name="connect-excel-to-a-single-database-in-azure-sql-database-and-create-a-report"></a>Excel verbinden met één data base in Azure SQL database en een rapport maken

Verbinding maken tussen Excel en één data base in Azure SQL Database en gegevens importeren en tabellen en grafieken maken op basis van waarden in de data base. In deze zelfstudie stelt u de verbinding tussen Excel en een databasetabel in, slaat u het bestand met de gegevens en de verbindingsinformatie voor Excel op en maakt u vervolgens een draaigrafiek uit de databasewaarden.

U hebt een afzonderlijke data base nodig om aan de slag te gaan. Als u er nog geen hebt, raadpleegt u [een enkele data base maken](sql-database-single-database-get-started.md) en een [IP-firewall op server niveau maken](sql-database-server-level-firewall-rule.md) voor het verkrijgen van een enkele data base met voorbeeld gegevens die in een paar minuten actief zijn.

In dit artikel importeert u voorbeeld gegevens uit dat artikel in Excel, maar u kunt dezelfde stappen volgen met uw eigen gegevens.

U hebt ook een kopie van Excel nodig. Dit artikel gebruikt [Microsoft Excel 2016](https://products.office.com/).

## <a name="connect-excel-to-a-sql-database-and-load-data"></a>Excel verbinden met een SQL database en gegevens laden

1. Open Excel om het programme te verbinden met SQL Database, en maak een nieuwe werkmap of open een bestaande Excel-werkmap.
2. Selecteer in de menu balk boven aan de pagina het tabblad **gegevens** , selecteer **gegevens ophalen**, selecteer uit Azure en selecteer vervolgens **uit Azure SQL database**. 

   ![Selecteer gegevensbron: Excel verbinden met SQL Database.](./media/sql-database-connect-excel/excel_data_source.png)

   De wizard Gegevensverbinding wordt geopend.
3. In het dialoogvenster **Verbinding maken met databaseserver** typt u de **servernaam** van de SQL Database die u wilt verbinden in het formulier <*servername*>**. database.windows.net**. Bijvoorbeeld **msftestserver.database.Windows.net**. Voer desgewenst de naam van uw data base in. Selecteer **OK** om het venster referenties te openen. 

   ![Het dialoog venster verbinding maken met de database server](media/sql-database-connect-excel/server-name.png)

4. Selecteer in het dialoog venster **SQL Server Data Base** de optie **Data Base** aan de linkerkant en voer vervolgens de **gebruikers naam** en het **wacht woord** in voor de SQL database-server waarmee u verbinding wilt maken. Selecteer **verbinding maken** om de **Navigator**te openen. 

   ![Typ de servernaam en aanmeldingsreferenties in](./media/sql-database-connect-excel/connect-to-server.png)

   > [!TIP]
   > Afhankelijk van uw netwerkomgeving kunt u mogelijk geen verbinding maken of kan de verbinding worden verbroken als de SQL Database-server geen verkeer van het IP-adres van de client toestaat. Ga naar de [Azure-portal](https://portal.azure.com/), klik op SQL-servers, klik op uw server, klik op firewall onder instellingen en voeg uw client-IP-adres toe. Zie [Firewallinstellingen configureren](sql-database-configure-firewall-settings.md) voor meer informatie.

5. Selecteer in de **Navigator**de data base waarmee u wilt werken in de lijst, selecteer de tabellen of weer gaven waarmee u wilt werken (we hebben gekozen voor **vGetAllCategories**). Selecteer vervolgens **laden** om de gegevens uit uw Data Base naar uw Excel-werk blad te verplaatsen.

    ![Selecteer een database en tabel.](./media/sql-database-connect-excel/select-database-and-table.png)

## <a name="import-the-data-into-excel-and-create-a-pivot-chart"></a>Importeer de gegevens in Excel en maak een draaigrafiek.

Nu u de verbinding tot stand hebt gebracht, hebt u verschillende opties voor het laden van de gegevens. Met de volgende stappen kunt u bijvoorbeeld een draai grafiek maken op basis van de gegevens die in uw SQL Database zijn gevonden. 

1. Volg de stappen in de vorige sectie, maar dit keer, in plaats van **laden**te selecteren, selecteert **u laden naar** in de vervolg keuzelijst **laden** .
2. Selecteer vervolgens hoe u deze gegevens in uw werkmap wilt weer geven. We hebben **draaigrafiek** gekozen. U kunt ook kiezen voor **Nieuw werkblad** of **Deze gegevens toevoegen aan een gegevensmodel**. Zie voor meer informatie over gegevensmodellen [Een gegevensmodel maken in Excel](https://support.office.com/article/Create-a-Data-Model-in-Excel-87E7A54C-87DC-488E-9410-5C75DBCB0F7B). 

    ![De indeling kiezen voor gegevens in Excel](./media/sql-database-connect-excel/import-data.png)

    Het werkblad heeft nu een lege draaitabel en grafiek.
3. Onder **PivotTable-velden**, selecteert u alle selectievakjes voor de velden die u wilt weergeven.

    ![Databaserapport configureren.](./media/sql-database-connect-excel/power-pivot-results.png)

> [!TIP]
> Als u andere Excel-werkmappen en-werk bladen wilt verbinden met de data base, selecteert u het tabblad **gegevens** en selecteert u **recente bronnen** om het dialoog venster **recente bronnen** te openen. Kies vervolgens de verbinding die u hebt gemaakt in de lijst en klik vervolgens op **openen**.
> ![Het dialoog venster recente bronnen](media/sql-database-connect-excel/recent-connections.png)

## <a name="create-a-permanent-connection-using-odc-file"></a>Een permanente verbinding maken met het. ODC-bestand

Als u de verbindings gegevens permanent wilt opslaan, kunt u een. ODC-bestand maken en deze verbinding een selecteerbaar optie maken in het dialoog venster **bestaande verbindingen** . 

1. Selecteer in de menu balk boven aan de pagina het tabblad **gegevens** en selecteer vervolgens **bestaande verbindingen** om het dialoog venster **bestaande verbindingen** te openen. 
   1. Selecteer **Bladeren voor meer** om het dialoog venster **gegevens bron selecteren** te openen.   
   2. Selecteer het bestand **+ NewSqlServerConnection. ODC** en selecteer vervolgens **openen** om de **wizard gegevens verbinding**te openen.

      ![Dialoog venster nieuwe verbinding](media/sql-database-connect-excel/new-connection.png)

2. Typ in de **wizard gegevens verbinding**de naam van uw server en uw SQL database referenties. Selecteer **Next**. 
   1. Selecteer de data base die uw gegevens bevat uit de vervolg keuzelijst. 
   2. Selecteer de tabel of weer gave waarin u geïnteresseerd bent. We hebben gekozen voor vGetAllCategories.
   3. Selecteer **Next**. 

      ![Wizard gegevens verbinding](media/sql-database-connect-excel/data-connection-wizard.png) 

3. Selecteer de locatie van het bestand, de **Bestands naam**en de **beschrijvende naam** in het volgende scherm van de wizard gegevens verbinding. U kunt er ook voor kiezen om het wacht woord op te slaan in het bestand, maar dit kan leiden tot ongewenste toegang. Selecteer **volt ooien** wanneer u klaar bent. 

    ![Gegevens verbinding opslaan](media/sql-database-connect-excel/save-data-connection.png)

4. Selecteer hoe u uw gegevens wilt importeren. U hebt ervoor gekozen om een draai tabel te maken. U kunt ook de eigenschappen van de verbinding wijzigen door **Eigenschappen**te selecteren. Selecteer **OK** wanneer u klaar bent. Als u niet hebt gekozen om het wacht woord op te slaan bij het bestand, wordt u gevraagd om uw referenties in te voeren. 

    ![Gegevens importeren](media/sql-database-connect-excel/import-data2.png)

5. Controleer of de nieuwe verbinding is opgeslagen door het tabblad **gegevens** uit te vouwen en **bestaande verbindingen**te selecteren. 

    ![Bestaande verbinding](media/sql-database-connect-excel/existing-connection.png)

## <a name="next-steps"></a>Volgende stappen

* Leer hoe u [SQL Database koppelt met SQL Server Management Studio](sql-database-connect-query-ssms.md) voor geavanceerd uitvoeren van query's en analyses.
* Meer informatie over de voordelen van [elastische groepen](sql-database-elastic-pool.md).
* Leer hoe u [ een webtoepassing maakt die verbinding maakt met SQL Database op de back-end](../app-service/app-service-web-tutorial-dotnet-sqldatabase.md).
