---
title: Gegevens van SQL Server naar Blob Storage kopiëren met behulp van Azure Data Factory | Microsoft Docs
description: Meer informatie over het kopiëren van gegevens uit een on-premises gegevensopslag naar de cloud met behulp van de zelf-hostende Integration Runtime in Azure Data Factory.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/11/2018
ms.author: abnarain
ms.openlocfilehash: ad09715f8ccbe20ec6f58d3a4543e0168e9f4cbc
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617708"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Gegevens van een on-premises SQL-serverdatabase naar Azure Blob Storage kopiëren
In deze zelfstudie gebruikt u de gebruikersinterface (UI) van Azure Data Factory om een pijplijn voor een data factory te maken waarmee gegevens worden gekopieerd van een on-premises SQL Server-database naar Azure Blob-opslag. U gaat een zelf-hostende Integration Runtime maken en gebruiken. Deze verplaatst gegevens van on-premises gegevensarchieven en gegevensarchieven in de cloud en omgekeerd.

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot Data Factory. Zie [Inleiding tot Data Factory](introduction.md) voor meer informatie. 

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Een zelf-hostende Integration Runtime maken.
> * Gekoppelde services maken voor SQL Server en Azure Storage. 
> * Gegevenssets maken voor SQL Server en Azure Blob.
> * Een pijplijn maakt met een kopieeractiviteit om de gegevens te verplaatsen.
> * Een pijplijnuitvoering starten.
> * De pijplijnuitvoering controleert.

## <a name="prerequisites"></a>Vereisten
### <a name="azure-subscription"></a>Azure-abonnement
Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="azure-roles"></a>Azure-rollen
Als u data factory-exemplaren wilt maken, moet het gebruikersaccount waarmee u zich bij Azure aanmeldt, toegewezen zijn aan de rollen *Inzender* of *Eigenaar*, of moet dit een *beheerder* van het Azure-abonnement zijn. 

Ga naar Azure Portal als u de machtigingen wilt weergeven die u hebt in het abonnement. Selecteer uw gebruikersnaam in de rechterbovenhoek en selecteer vervolgens **Machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren. Zie [Toegang beheren met RBAC en de Azure-portal](../role-based-access-control/role-assignments-portal.md) voor voorbeeldinstructies voor het toevoegen van een gebruiker aan een rol.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 en 2017
In deze zelfstudie gebruikt u een on-premises SQL Server-database als een *brongegevensopslag*. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van deze on-premises SQL Server-database (bron) naar Blob-opslag (sink). Maak een tabel met de naam **emp** in uw SQL Server-database en voeg een aantal voorbeeldgegevens toe aan de tabel. 

1. Start SQL Server Management Studio. Als dit niet al is geïnstalleerd op uw computer, gaat u naar [SQL Server Management Studio downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Maak verbinding met SQL Server-exemplaar met behulp van uw referenties. 

1. Maak een voorbeelddatabase. Klik in de structuurweergave met de rechtermuisknop op **Databases** en selecteer **Nieuwe database**. 
1. Voer in het venster **Nieuwe database** een naam in voor de database en selecteer **OK**. 

1. Voer het volgende queryscript uit voor de database. Hiermee wordt de **emp**-tabel gemaakt en worden enkele voorbeeldgegevens ingevoegd in deze tabel:

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

1. In de structuurweergave klikt u met de rechtermuisknop op de database die u hebt gemaakt en selecteert u **Nieuwe query**.

### <a name="azure-storage-account"></a>Azure Storage-account
In deze zelfstudie gaat u een algemeen Azure Storage-account (en dan met name Blob Storage) gebruiken als een doel/sink-gegevensopslag. Zie het artikel [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md) als u geen Azure Storage-account hebt voor algemene doeleinden. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van deze on-premises SQL Server-database (bron) naar Blob Storage (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>De naam en sleutel van een opslagaccount ophalen
In deze zelfstudie gaat u de naam en sleutel van uw opslagaccount gebruiken. Voer de volgende stappen uit om de naam en sleutel van uw opslagaccount op te halen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met uw Azure-gebruikersnaam en -wachtwoord. 

1. Selecteer in het linkerdeelvenster **Meer services**. Filter met behulp van het sleutelwoord **Opslag** en selecteer vervolgens **Opslagaccounts**.

    ![Zoeken naar Storage-account](media/tutorial-hybrid-copy-powershell/search-storage-account.png)

1. Filter indien nodig in de lijst met opslagaccounts op uw opslagaccount. Selecteer vervolgens uw opslagaccount. 

1. Selecteer in het venster **Opslagaccount** de optie **Toegangssleutels**.


1. Kopieer de waarden in de vakken **opslagaccountnaam** en **key1** en plak deze in Kladblok of een andere editor voor later gebruik in de zelfstudie. 

#### <a name="create-the-adftutorial-container"></a>De container adftutorial maken 
In deze sectie maakt u in uw Blob Storage een blobcontainer met de naam **adftutorial**. 

1. Ga in het venster **Opslagaccount** naar **Overzicht** en selecteer vervolgens **Blobs**. 

    ![De optie Blobs selecteren](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Selecteer in het venster **Blob-service** **Container**. 

    ![Knop Container](media/tutorial-hybrid-copy-powershell/add-container-button.png)

1. In het venster **Nieuwe container** voert u bij **Naam** **adftutorial** in. Selecteer vervolgens **OK**. 

    ![Venster Nieuwe container](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Selecteer **adftutorial** in de lijst met containers.

    ![Container selecteren](media/tutorial-hybrid-copy-powershell/select-adftutorial-container.png)

1. Houd het venster **Container** voor **adftutorial** geopend. U gaat hiermee aan het einde van deze zelfstudie de uitvoer controleren. In Data Factory wordt automatisch in deze container de uitvoermap gemaakt, zodat u er zelf geen hoeft te maken.

## <a name="create-a-data-factory"></a>Data factory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory. 

1. Open de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer in het menu links de optie **een resource** > maken**gegevens en analyses** > **Data Factory**:
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

1. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**. 
   
     ![De pagina Nieuwe data factory](./media/tutorial-hybrid-copy-portal/new-azure-data-factory.png)

De naam van de data factory moet *wereldwijd uniek* zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

   ![Naam nieuwe data factory](./media/tutorial-hybrid-copy-portal/name-not-available-error.png)
1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:
   
   - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

   - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.
        
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.
1. Selecteer **V2** onder **Versie**.
1. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door Data Factory worden gebruikt, kunnen zich in andere regio's bevinden.
1. Selecteer **Vastmaken aan dashboard**. 
1. Selecteer **Maken**.
1. Op het dashboard ziet u de volgende tegel met de status: **Data Factory implementeren**:

    ![Tegel Data Factory implementeren](media/tutorial-hybrid-copy-portal/deploying-data-factory.png)
1. Na het aanmaken ziet u de pagina **Data Factory** zoals weergegeven in de afbeelding:
   
    ![Startpagina van de gegevensfactory](./media/tutorial-hybrid-copy-portal/data-factory-home-page.png)
1. Selecteer de tegel **Maken en controleren** om de Data Factory-UI te openen op een afzonderlijk tabblad. 


## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. Er wordt automatisch een pijplijn voor u gemaakt. U ziet de pijplijn in de structuurweergave en de bijbehorende editor wordt geopend. 

   ![Pagina Aan de slag](./media/tutorial-hybrid-copy-portal/get-started-page.png)

1. Voer op het tabblad **Algemeen** onder in het venster **Eigenschappen** bij **Naam** **SQLServerToBlobPipeline** in.

   ![Naam van de pijplijn](./media/tutorial-hybrid-copy-portal/pipeline-name.png)

1. Vouw in de werkset **Activiteiten** **DataFlow** uit. Gebruik slepen en neerzetten om de activiteit **Kopiëren** naar het ontwerpoppervlak van de pijplijn te verplaatsen. Stel de naam van de activiteit in op **CopySqlServerToAzureBlobActivity**.

   ![Activiteitsnaam](./media/tutorial-hybrid-copy-portal/copy-activity-name.png)

1. Ga in het venster **Eigenschappen** naar het tabblad **Bron** en selecteer **+ Nieuw**.

   ![Tabblad Bron](./media/tutorial-hybrid-copy-portal/source-dataset-new-button.png)

1. Zoek in het venster **Nieuwe gegevensset** naar **SQL Server**. Selecteer **SQL Server** en vervolgens **Voltooien**. U ziet een nieuw tabblad met de titel **SqlServerTable1**. U ziet ook de gegevensset **SqlServerTable1** in de structuurweergave aan de linkerkant. 

   ![SQL Server-selecteren](./media/tutorial-hybrid-copy-portal/select-sql-server.png)

1. Voer op het tabblad **Algemeen** onder in het venster **Eigenschappen** bij **Naam** **SqlServerDataset** in.

   ![Naam van de brongegevensset](./media/tutorial-hybrid-copy-portal/source-dataset-name.png)

1. Ga naar het tabblad **Verbinding** en klik op **+ Nieuw**. In deze stap maakt u een verbinding met het brongegevensexemplaar (SQL Server-database). 

   ![Verbinding maken met brongegevensset](./media/tutorial-hybrid-copy-portal/source-connection-new-button.png)

1. Voeg in het venster **Nieuwe gekoppelde service** **Naam** toe als **SqlServerLinkedService**. Selecteer **Nieuw** onder **Verbinding maken via integratieruntime**. In deze sectie kunt u een zelf-hostende Integration Runtime maken en deze koppelen aan een on-premises computer met de SQL Server-database. De zelf-hostende Integration Runtime is het onderdeel waarmee gegevens worden gekopieerd van SQL Server-database op uw computer naar Blob Storage. 

   ![Nieuwe integratieruntime](./media/tutorial-hybrid-copy-portal/new-integration-runtime-button.png)

1. Selecteer in het venster **Integratieruntime instellen** de optie **Privénetwerk** en selecteer vervolgens **Volgende**. 

   ![Privénetwerk selecteren](./media/tutorial-hybrid-copy-portal/select-private-network.png)

1. Voer een naam in voor de integratieruntime en selecteer **Volgende**.

    ![Naam van integratieruntime](./media/tutorial-hybrid-copy-portal/integration-runtime-name.png)

1. Selecteer bij **optie 1: snelle installatie** de koppeling **Klik hier om de snelle installatie voor deze computer te starten**. 

    ![Koppeling Snelle installatie](./media/tutorial-hybrid-copy-portal/click-express-setup.png)

1. Selecteer **Sluiten** in het venster **Snelle installatie van integratieruntime (zelf-hostend)** . 

    ![Snelle installatie van integratieruntime (zelf-hostend)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Controleer in het venster **Nieuwe gekoppelde service** of de **Integratieruntime** die hierboven is gemaakt, is geselecteerd onder **Verbinding maken via integratieruntime**. 

    ![](./media/tutorial-hybrid-copy-portal/select-integration-runtime.png)

1. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    a. Voer **SqlServerLinkedService** in bij **Naam**.

    b. Controleer of de zelf-hostende integratieruntime die u eerder hebt gemaakt, wordt weergegeven bij **Verbinding maken via integratieruntime**.

    c. Voer de naam van uw SQL Server-exemplaar in bij **Servernaam**. 

    d. Geef de naam van de database met de **emp**-tabel op bij **Databasenaam**.

    e. Selecteer bij **Verificatietype** het juiste verificatietype in dat in Data Factory moet worden gebruikt om verbinding te maken met uw SQL Server-database.

    f. Bij **Gebruikersnaam** en **Wachtwoord**, typt u de gebruikersnaam en het wachtwoord. Als u een backslash wilt gebruiken (\\) in de naam van uw gebruikersaccount of server, moet u er voor het escapeteken (\\) gebruiken. Gebruik bijvoorbeeld *mydomain\\\\myuser*.

    g. Selecteer **Verbinding testen**. Voer deze stap uit om te bevestigen dat Data Factory verbinding mag maken met de SQL Server-database met behulp van de zelf-hostende integratieruntime die u hebt gemaakt.

    h. Selecteer **Voltooien** om de gekoppelde service op te slaan.

       

1. Als het goed is, ziet u nu het venster met de geopende brongegevensset weer. Voer in het tabblad **Verbinding** in het venster **Eigenschappen** de volgende stappen uit: 

    a. Controleer of in **Gekoppelde service** **SqlServerLinkedService** wordt weergegeven.

    b. Selecteer bij **Tabel** **[dbo].[emp]** .

    ![Brongegevenssetinformatie](./media/tutorial-hybrid-copy-portal/source-dataset-connection.png)

1. Ga naar het tabblad met de **SQLServerToBlobPipeline** of selecteer **SQLServerToBlobPipeline** in de structuurweergave. 

    ![Tabblad Pijplijn](./media/tutorial-hybrid-copy-portal/pipeline-tab.png)

1. Ga naar het tabblad **Sink** onder in het venster **Eigenschappen** en selecteer **+ Nieuw**. 

    ![Tabblad Sink](./media/tutorial-hybrid-copy-portal/sink-dataset-new-button.png)

1. Selecteer in het venster **Nieuwe gegevensset** de optie **Azure Blob Storage**. Selecteer vervolgens **Voltooien**. Er wordt nu een nieuw tabblad geopend voor de gegevensset. Ook ziet u de gegevensset in de structuurweergave. 

    ![Selectie Blob-opslag](./media/tutorial-hybrid-copy-portal/select-azure-blob-storage.png)

1. Voer **AzureBlobDataset** in bij **Naam**.

    ![Sink-gegevenssetnaam](./media/tutorial-hybrid-copy-portal/sink-dataset-name.png)

1. Ga naar het tabblad **Verbinding** onder in het venster **Eigenschappen**. Selecteer **+ Nieuw** naast **Gekoppelde service**. 

    ![Knop Nieuwe gekoppelde service](./media/tutorial-hybrid-copy-portal/new-storage-linked-service-button.png)

1. Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:

    a. Voer bij **Naam** **AzureStorageLinkedService** in.

    b. Selecteer uw opslagaccount bij **Naam van opslagaccount**.

    c. Als u de verbinding met het opslagaccount wilt testen, selecteert u **Verbinding testen**.

    d. Selecteer **Opslaan**.

    ![Instellingen voor gekoppelde Storage-service](./media/tutorial-hybrid-copy-portal/azure-storage-linked-service-settings.png) 

1. Als het goed is, ziet u nu het venster met de geopende sink-gegevensset weer. Voer op het tabblad **Verbinding** de volgende stappen uit: 

    a. Controleer of **AzureStorageLinkedService** is geselecteerd bij **Gekoppelde service**.

    b. Voer **adftutorial/fromonprem** in voor het **map**/ **Directory**-gedeelte van het **bestandspad**. Als de uitvoermap niet bestaat in de container adftutorial, wordt de uitvoermap automatisch gemaakt door Data Factory.

    c. Selecteer **Dynamische inhoud toevoegen**voor het **bestandsnaam**-deel van het **bestandspad**.   

    ![waarde voor dynamische bestandsnaam](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Voeg `@CONCAT(pipeline().RunId, '.txt')` toe, selecteer **Voltooien**. Hierdoor wordt de naam van het bestand gewijzigd in PipelineRunID.txt. 

    ![dynamische expressie voor omzetten van bestandsnaam](./media/tutorial-hybrid-copy-portal/add-dynamic-file-name.png)

    ![Verbinding met sink-gegevensset](./media/tutorial-hybrid-copy-portal/sink-dataset-connection.png)

1. Ga naar het tabblad met de pijplijn geopend of selecteer de pijplijn in de structuurweergave. Controleer of **AzureBlobDataset** is geselecteerd bij **Sink-gegevensset**. 

    ![Sink-gegevensset geselecteerd](./media/tutorial-hybrid-copy-portal/sink-dataset-selected.png)

1. Selecteer in de werkbalk de optie **Valideren** om de instellingen voor de pijplijn te valideren. Sluit het **Validatierapport voor de pijplijn** door **Sluiten** te selecteren. 

    ![Pijplijn valideren](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)

1. Als u entiteiten die u hebt gemaakt, naar Data Factory wilt publiceren, selecteert u **Alles publiceren**.

    ![De knop Publiceren](./media/tutorial-hybrid-copy-portal/publish-button.png)

1. Wacht tot u de pop-up **Het publiceren is voltooid** ziet. U kunt de status van de publicatie controleren door aan de linkerkant **Meldingen weergeven** te selecteren. Selecteer **Sluiten** als u het meldingenvenster wilt sluiten. 

    ![Het publiceren is voltooid](./media/tutorial-hybrid-copy-portal/publishing-succeeded.png)


## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren
Selecteer **Activeren** op de werkbalk en selecteer vervolgens **Nu activeren**.

![Een pijplijnuitvoering activeren](./media/tutorial-hybrid-copy-portal/trigger-now.png)

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Controleren**. U ziet de pijplijn die u handmatig hebt geactiveerd in de vorige stap. 

    ![Pijplijnuitvoeringen controleren](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)
1. Uitvoeringen van activiteiten die aan de pijplijn zijn gekoppeld, kunt u bekijken door de koppeling **Uitvoeringen van activiteiten weergeven** in de kolom **Actions** te selecteren. U ziet uitvoeringen van activiteiten alleen maar omdat de pijplijn maar één activiteit bevat. Selecteer de koppeling **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. Als u wilt terugkeren naar de weergave**Pijplijnuitvoeringen**, selecteert u **Pijplijnen** bovenaan.

    ![Uitvoering van activiteiten controleren](./media/tutorial-hybrid-copy-portal/activity-runs.png)

## <a name="verify-the-output"></a>De uitvoer controleren
De uitvoermap *fromonprem* wordt automatisch door de pijplijn gemaakt in de `adftutorial` blobcontainer. Controleer of u het bestand *[pipeline().RunId].txt* in de uitvoermap ziet. 

![naam uitvoerbestand bevestigen](./media/tutorial-hybrid-copy-portal/sink-output.png)


## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in Blob Storage. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een data factory maken.
> * Een zelf-hostende Integration Runtime maken.
> * Gekoppelde services maakt voor SQL Server en Storage. 
> * Gegevenssets maakt voor SQL Server en Blob Storage.
> * Een pijplijn maakt met een kopieeractiviteit om de gegevens te verplaatsen.
> * Een pijplijnuitvoering starten.
> * De pijplijnuitvoering controleert.

Zie [Ondersteunde gegevensopslagexemplaren](copy-activity-overview.md#supported-data-stores-and-formats) voor een lijst met gegevensopslagexemplaren die worden ondersteund door Data Factory.

Ga door naar de volgende zelfstudie voor informatie over het bulksgewijs kopiëren van gegevens uit een bron naar een bestemming:

> [!div class="nextstepaction"]
>[Gegevens bulksgewijs kopiëren](tutorial-bulk-copy-portal.md)
