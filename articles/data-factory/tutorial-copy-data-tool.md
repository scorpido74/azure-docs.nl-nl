---
title: Gegevens kopiëren van Azure Blob-opslag naar SQL met Gegevens kopiëren-hulp programma
description: Maak een Azure-data factory aan en gebruik vervolgens het hulpprogramma Copy Data om gegevens uit Azure Blob Storage te kopiëren naar een SQL Database.
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
ms.date: 09/11/2018
ms.openlocfilehash: 6335fce717772e268f711c2e6e5050fa8c17d573
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977337"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Gegevens kopiëren van Azure Blob Storage naar een SQL-database met behulp van het hulpprogramma Copy Data

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](tutorial-copy-data-tool.md)

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken waarmee gegevens worden gekopieerd van Azure Blob-opslag naar een SQL database.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:
> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**: Blob Storage als het _brongegevensarchief_ gebruiken. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).
* **Azure SQL Database**: een SQL-database als de _sink-gegevensopslag_ gebruiken. Als u geen SQL-database hebt, raadpleegt u de instructies in [Een SQL Database maken](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Bereid uw Blob-opslag en de SQL-database voor voor gebruik tijdens de zelfstudie, door de volgende stappen uit te voeren.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start **Kladblok**. Kopieer de volgende tekst en sla deze op schijf op in een bestand met de naam**inputEmp.txt**:

    ```
    John|Doe
    Jane|Doe
    ```

1. Maak een container met de naam **adfv2tutorial** en upload het bestand inputEmp.txt naar de container. U kunt de Azure Portal of verschillende hulpprogram ma's zoals [Azure Storage Explorer](https://storageexplorer.com/) gebruiken om deze taken uit te voeren.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om een tabel met de naam **dbo.emp** te maken in uw SQL-database:

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

2. Geef Azure-services toegang tot SQL Server. Controleer of de instelling **Toegang tot Azure-services toestaan** is ingeschakeld voor uw server waarop SQL Database wordt uitgevoerd. Met deze instelling kan Data Factory gegevens naar uw database-instantie schrijven. Om deze instelling te controleren en in te scha kelen, gaat u naar de > overzicht van Azure SQL Server > Set Server Firewall > Stel de optie **toegang tot Azure-Services toestaan** **in op aan**.

## <a name="create-a-data-factory"></a>Een data factory maken

1. Selecteer in het menu aan de linkerkant **een resource maken** > **Analytics** > **Data Factory**:

    ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

    ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

    Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_ **ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
1. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.
    
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

1. Selecteer bij **Versie** de optie **V2** als de versie.
1. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
1. Selecteer **Maken**.

1. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
1. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Aan de slag** de tegel **Copy Data** om het hulpprogramma Copy Data te starten.

    ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)
1. Op de pagina **Eigenschappen** bij **Taaknaam**, voert u **CopyFromBlobToSqlPipeline** in. Selecteer vervolgens **Volgende**. De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.

1. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure-Blob Storage** in de galerie en selecteer vervolgens **door gaan**.

    c. Selecteer op de pagina **Nieuwe gekoppelde service** uw opslagaccount in de lijst **Naam van opslagaccount** en selecteer **Voltooien**.

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

    ![Aan de bron gekoppelde service selecteren](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Klik op **Bladeren** om naar de map **adfv2tutorial/input** te gaan en selecteer het bestand **inputEmp.txt**. Klik vervolgens op **Kiezen**.

    b. Klik op **Volgende** om verder te gaan met de volgende stap.

1. Op de pagina **Instellingen bestandsindelingen** ziet u dat het hulpprogramma automatisch de scheidingstekens voor kolommen en rijen detecteert. Selecteer **Next**. U kunt ook een voorbeeld van gegevens en het schema van de ingevoerde gegevens op deze pagina bekijken.

    ![Pagina Instellingen bestandsindelingen](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Voltooi op de pagina **Doelgegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure SQL database** in de galerie en selecteer vervolgens **door gaan**.

    c. Selecteer op de pagina **Nieuwe gekoppelde Service** uw servernaam en databasenaam in de vervolgkeuzelijst en geef de gebruikersnaam en wachtwoord op. Selecteer vervolgens **Voltooien**.

    ![Azure SQL DB configureren](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

    ![Aan sink gekoppelde service selecteren](./media/tutorial-copy-data-tool/select-sink-linked-service.png)

1. Selecteer op de pagina **Tabeltoewijzing** de tabel **[dbo].[emp]** en selecteer vervolgens **Volgende**.

1. Op de pagina **Schematoewijzing** ziet u dat de eerste en tweede kolom in het invoerbestand zijn toegewezen aan de kolommen **FirstName** en **LastName** van de tabel **emp**. Selecteer **Next**.

    ![De pagina Schematoewijzing](./media/tutorial-copy-data-tool/schema-mapping.png)
1. Selecteer op de pagina **Instellingen** de optie **Volgende**.
1. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.
1. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
1. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat onder andere koppelingen om details van de uitvoering van activiteiten te bekijken en de pijplijn opnieuw uit te voeren. Selecteer **Vernieuwen** om de lijst te vernieuwen.

1. Uitvoeringen van de activiteit die aan de pijplijn zijn gekoppeld, kunt u bekijken door de koppeling **Uitvoeringen van activiteit weergeven** in de kolom **Acties** te selecteren. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **pijplijn uitvoeringen** bovenaan. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Controleer of de gegevens zijn opgenomen in de tabel **emp** in uw SQL-database.


1. Selecteer het tabblad **Auteur** aan de linkerkant om over te schakelen naar de bewerkingsmodus. U kunt de gekoppelde services, gegevenssets en pijplijnen die zijn gemaakt met het hulpprogramma, bijwerken met behulp van de editor. Bekijk [de Azure Portal-versie van deze zelfstudie](tutorial-copy-data-portal.md) voor details over het bewerken van entiteiten in de Data Factory-UI.

## <a name="next-steps"></a>Volgende stappen
In dit voorbeeld kopieert de pijplijn gegevens vanuit Blob Storage naar een SQL-database. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

>[!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-data-tool.md)
