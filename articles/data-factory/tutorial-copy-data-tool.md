---
title: Gegevens kopiëren van Azure Blob-opslag naar SQL met Gegevens kopiëren-hulp programma
description: Maak een Azure-data factory en gebruik vervolgens het hulp programma Gegevens kopiëren om gegevens van Azure Blob-opslag naar een SQL Database te kopiëren.
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
ms.openlocfilehash: be9395b908461b54b607fff32747ca0d9f20f45c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81418657"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Gegevens kopiëren van Azure Blob-opslag naar een SQL Database met behulp van het Gegevens kopiëren-hulp programma

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Huidige versie](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken waarmee gegevens worden gekopieerd van Azure Blob-opslag naar een SQL Database.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:
> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Storage account**: gebruik Blob Storage als het _brongegevens_ archief. Als u geen Azure Storage-account hebt, raadpleegt u de instructies in [een opslag account maken](../storage/common/storage-account-create.md).
* **Azure SQL database**: gebruik een SQL database als _sink_ -gegevens archief. Als u geen SQL Database hebt, raadpleegt u de instructies in [Create a SQL database](../sql-database/sql-database-get-started-portal.md).

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Bereid de Blob-opslag en uw SQL Database voor de zelf studie voor door de volgende stappen uit te voeren.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start **Klad blok**. Kopieer de volgende tekst en sla deze op schijf op in een bestand met de naam**inputEmp.txt**:

    ```
    FirstName|LastName
    John|Doe
    Jane|Doe
    ```

1. Maak een container met de naam **adfv2tutorial** en upload het bestand inputEmp.txt naar de container. U kunt de Azure Portal of verschillende hulpprogram ma's zoals [Azure Storage Explorer](https://storageexplorer.com/) gebruiken om deze taken uit te voeren.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om een tabel met de naam **dbo. EMP** te maken in uw SQL database:

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

2. Geef Azure-services toegang tot de SQL-server. Controleer of de instelling **Azure-Services en-bronnen toestaan voor toegang tot deze server** is ingeschakeld voor de server waarop SQL database wordt uitgevoerd. Met deze instelling kan Data Factory gegevens naar uw database-instantie schrijven. Om deze instelling te controleren en in te scha kelen, gaat u naar Azure SQL Server > Security > firewalls en virtuele netwerken > Stel de optie **Azure-Services en-resources voor toegang tot deze server toestaan** **in op**aan.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het menu links de optie **een resource** > **Analytics** > -**Data Factory**maken:

    ![Nieuwe data factory maken](./media/doc-common-process/new-azure-data-factory-menu.png)
1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

    ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

    Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
1. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **bestaande gebruiken**en selecteer een bestaande resource groep in de vervolg keuzelijst.

    b. Selecteer **nieuwe maken**en voer de naam van een resource groep in.
    
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

1. Selecteer bij **Versie** de optie **V2** als de versie.
1. Selecteer onder **locatie**de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
1. Selecteer **Maken**.

1. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
1. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Aan de slag** de tegel **Copy Data** om het hulpprogramma Copy Data te starten.

    ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)
1. Op de pagina **Eigenschappen** bij **Taaknaam**, voert u **CopyFromBlobToSqlPipeline** in. Selecteer **volgende**. De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.
    ![Een pijplijn maken](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure-Blob Storage** in de galerie en selecteer vervolgens **door gaan**.

    c. Selecteer uw Azure-abonnement op de pagina **nieuwe gekoppelde service** en selecteer uw opslag account in de lijst **naam van het opslag account** . Test de verbinding en selecteer vervolgens **maken**.

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

    ![Aan de bron gekoppelde service selecteren](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Klik op **Bladeren** om naar de map **adfv2tutorial/input** te gaan en selecteer het bestand **inputEmp.txt**. Klik vervolgens op **Kiezen**.

    b. Klik op **Volgende** om verder te gaan met de volgende stap.

1. Schakel op de pagina **instellingen voor bestands indeling** het selectie vakje voor de *eerste rij als koptekst*in. U ziet dat het hulp programma automatisch de scheidings tekens voor kolommen en rijen detecteert. Selecteer **Next**. U kunt ook een voor beeld bekijken van gegevens en het schema van de invoer gegevens op deze pagina weer geven.

    ![Pagina Instellingen bestandsindelingen](./media/tutorial-copy-data-tool/file-format-settings-page.png)
1. Voltooi op de pagina **Doelgegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure SQL database** in de galerie en selecteer vervolgens **door gaan**.

    c. Selecteer op de pagina **nieuwe gekoppelde service** de naam van de server en de data base in de vervolg keuzelijst, en geef de gebruikers naam en het wacht woord op en selecteer vervolgens **maken**.

    ![Azure SQL DB configureren](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

1. Selecteer op de pagina **Tabeltoewijzing** de tabel **[dbo].[emp]** en selecteer vervolgens **Volgende**.

1. Op de pagina **kolom toewijzing** ziet u dat de tweede en de derde kolommen in het invoer bestand zijn toegewezen aan de kolommen voor **naam** en **Achternaam** van de tabel **EMP** . Pas de toewijzing aan om ervoor te zorgen dat er geen fout is en selecteer **volgende**.

    ![Pagina kolom toewijzing](./media/tutorial-copy-data-tool/column-mapping.png)

1. Selecteer op de pagina **Instellingen** de optie **Volgende**.
1. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.
1. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
 
    ![De pijplijn bewaken](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. Selecteer op de pagina pijplijn uitvoeringen de optie **vernieuwen** om de lijst te vernieuwen. Klik op de koppeling onder **PIJPLIJN naam** om details van de activiteit weer te geven of de pijp lijn opnieuw uit te voeren. 
    ![Pijplijn uitvoering](./media/tutorial-copy-data-tool/pipeline-run.png)

1. Selecteer op de pagina uitvoeringen van activiteit de koppeling **Details** (bril pictogram) onder de kolom **activiteit naam** voor meer informatie over de Kopieer bewerking. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **alle pijplijn uitvoeringen** in het navigatie menu. Selecteer **Vernieuwen** om de weergave te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/tutorial-copy-data-tool/activity-monitoring.png)


1. Controleer of de gegevens zijn ingevoegd in de tabel **dbo. EMP** in uw SQL database.


1. Selecteer het tabblad **Auteur** aan de linkerkant om over te schakelen naar de bewerkingsmodus. U kunt de gekoppelde services, gegevenssets en pijplijnen die zijn gemaakt met het hulpprogramma, bijwerken met behulp van de editor. Bekijk [de Azure Portal-versie van deze zelfstudie](tutorial-copy-data-portal.md) voor details over het bewerken van entiteiten in de Data Factory-UI.

    ![Tabblad Auteur selecteren](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Volgende stappen
De pijp lijn in dit voor beeld kopieert gegevens van Blob-opslag naar een SQL Database. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

Ga verder met de volgende zelfstudie als u wilt weten hoe u on-premises gegevens kopieert naar de cloud:

>[!div class="nextstepaction"]
>[On-premises gegevens kopiëren naar de cloud](tutorial-hybrid-copy-data-tool.md)
