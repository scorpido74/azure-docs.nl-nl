---
title: Gegevens uit Azure Blob-opslag kopiëren naar SQL met het hulpprogramma Gegevens kopiëren
description: Maak een Azure-gegevensfabriek en gebruik vervolgens het gereedschap Gegevens kopiëren om gegevens uit Azure Blob-opslag naar een SQL-database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 52ed43277eef84de826d2f4fa41ba860211a1531
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78969908"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Gegevens uit Azure Blob-opslag kopiëren naar een SQL-database met het gereedschap Gegevens kopiëren

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](tutorial-copy-data-tool.md)

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het gereedschap Gegevens kopiëren om een pijplijn te maken die gegevens uit Azure Blob-opslag kopieert naar een SQL-database.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:
> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Storage-account:** gebruik Blob-opslag als _brongegevensarchief._ Als u geen Azure Storage-account hebt, raadpleegt u de instructies in [Een opslagaccount maken.](../storage/common/storage-account-create.md)
* **Azure SQL Database**: Gebruik een SQL Database als _sink_ data store. Als u geen SQL-database hebt, raadpleegt u de instructies in [Een SQL-database maken.](../sql-database/sql-database-get-started-portal.md)

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Bereid uw Blob-opslag en uw SQL-database voor op de zelfstudie door deze stappen uit te voeren.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. **Kladblok starten**. Kopieer de volgende tekst en sla deze op schijf op in een bestand met de naam**inputEmp.txt**:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Maak een container met de naam **adfv2tutorial** en upload het bestand inputEmp.txt naar de container. U de Azure-portal of verschillende hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) gebruiken om deze taken uit te voeren.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om een tabel met de naam **dbo.emp** in uw SQL-database te maken:

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
    ```

2. Geef Azure-services toegang tot de SQL-server. Controleer of de instelling **Azure-services en -bronnen toegang geeft tot deze server** is ingeschakeld voor uw server waarop SQL Database wordt uitgevoerd. Met deze instelling kan Data Factory gegevens naar uw database-instantie schrijven. Als u deze instelling wilt verifiëren en inschakelen, gaat u naar Azure SQL-server > Beveiliging > Firewalls en virtuele netwerken > de **Azure-services en -bronnen toestaan instellen om toegang te krijgen tot deze serveroptie** op **AAN**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken:** **Create a resource**

    ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

    ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

    Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
1. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuw maken**en voer de naam van een resourcegroep in.
    
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

1. Selecteer bij **Versie** de optie **V2** als de versie.
1. Selecteer **onder locatie**de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
1. Selecteer **Maken**.

1. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
1. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Aan de slag** de tegel **Copy Data** om het hulpprogramma Copy Data te starten.

    ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)
1. Op de pagina **Eigenschappen** bij **Taaknaam**, voert u **CopyFromBlobToSqlPipeline** in. Selecteer **vervolgens Volgende**. De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.
    ![Een pijplijn maken](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure Blob Storage** in de galerie en selecteer **Doorgaan**.

    c. Selecteer op de pagina **Nieuwe Gekoppelde Service** uw Azure-abonnement en selecteer uw opslagaccount in de lijst Met de naam van het **opslagaccount.** Test verbinding en selecteer **Vervolgens Maken**.

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

    ![Aan de bron gekoppelde service selecteren](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Klik op **Bladeren** om naar de map **adfv2tutorial/input** te gaan en selecteer het bestand **inputEmp.txt**. Klik vervolgens op **Kiezen**.

    b. Klik op **Volgende** om verder te gaan met de volgende stap.

1. Schakel op de pagina **Instellingen voor bestandsindeling** het selectievakje voor *eerste rij in als koptekst*. Het hulpprogramma detecteert automatisch de kolom- en rijscheidingstekens. Selecteer **Volgende**. U ook een voorbeeld van gegevens bekijken en het schema van de invoergegevens op deze pagina bekijken.

    ![Pagina Instellingen bestandsindelingen](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Voltooi op de pagina **Doelgegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure SQL Database** in de galerie en selecteer **Doorgaan**.

    c. Selecteer op de pagina **Nieuwe gekoppelde service** uw servernaam en DB-naam in de vervolgkeuzelijst en geef de gebruikersnaam en het wachtwoord op en selecteer Vervolgens **Maken**.

    ![Azure SQL DB configureren](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

1. Selecteer op de pagina **Tabeltoewijzing** de tabel **[dbo].[emp]** en selecteer vervolgens **Volgende**.

1. Let op de **kolomtoewijzingspagina** op dat de tweede en de derde kolommen in het invoerbestand zijn toegewezen aan de kolommen **Voornaam** en **Achternaam** van de **emp-tabel.** Pas de toewijzing aan om te zien of er geen fout optreedt en selecteer **Volgende**.

    ![Kolomtoewijzingspagina](./media/tutorial-copy-data-tool/column-mapping.png)

1. Selecteer op de pagina **Instellingen** de optie **Volgende**.
1. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.
1. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
 
    ![De pijplijn bewaken](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Selecteer Vernieuwen om de lijst te vernieuwen op de pagina **Pijplijnuitvoeringen.** Klik op de koppeling onder **PIPELINE NAME** om activiteitsgegevens weer te geven of de pijplijn opnieuw uit te voeren. 
    ![Pijplijnrun](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Selecteer op de pagina **Activiteitsuitvoeringen** de koppeling Details (pictogram bril) onder de kolom **ACTIVITEITSNAAM** voor meer informatie over de bewerking van kopiëren. Als u terug wilt gaan naar de weergave Pijplijnuitvoeringen, selecteert u de koppeling **ALLE pijplijnuitvoeringen** in het broodkruimelmenu. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Controleer of de gegevens in de **dbo.emp-tabel** in uw SQL-database worden ingevoegd.


1. Selecteer het tabblad **Auteur** aan de linkerkant om over te schakelen naar de bewerkingsmodus. U kunt de gekoppelde services, gegevenssets en pijplijnen die zijn gemaakt met het hulpprogramma, bijwerken met behulp van de editor. Bekijk [de Azure Portal-versie van deze zelfstudie](tutorial-copy-data-portal.md) voor details over het bewerken van entiteiten in de Data Factory-UI.

    ![Tabblad Auteur selecteren](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Volgende stappen
De pijplijn in dit voorbeeld kopieert gegevens van Blob-opslag naar een SQL-database. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

>[!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-data-tool.md)
