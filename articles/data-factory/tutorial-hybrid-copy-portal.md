---
title: Gegevens van SQL Server naar Blob-opslag kopiëren met Azure-portal
description: Meer informatie over het kopiëren van gegevens uit een on-premises gegevensopslag naar de cloud met behulp van de zelf-hostende Integration Runtime in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 03/12/2020
ms.openlocfilehash: c073d3e51234e0ed8e524c2ae557d4158ad9e7d7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80065182"
---
# <a name="copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Gegevens van een on-premises SQL-serverdatabase naar Azure Blob Storage kopiëren
In deze zelfstudie gebruikt u de gebruikersinterface (UI) van Azure Data Factory om een pijplijn voor een data factory te maken waarmee gegevens worden gekopieerd van een on-premises SQL Server-database naar Azure Blob-opslag. U gaat een zelf-hostende Integration Runtime maken en gebruiken. Deze verplaatst gegevens van on-premises gegevensarchieven en gegevensarchieven in de cloud en omgekeerd.

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot Data Factory. Zie [Inleiding tot Data Factory](introduction.md) voor meer informatie.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
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
Als u gegevensfabrieksexemplaren wilt maken, moet het gebruikersaccount dat u gebruikt om u aan te melden bij Azure, een *rol inzender* of *eigenaar* toegewezen krijgen of *beheerder* van het Azure-abonnement zijn.

Ga naar Azure Portal als u de machtigingen wilt weergeven die u hebt in het abonnement. Selecteer uw gebruikersnaam in de rechterbovenhoek en selecteer vervolgens **Machtigingen**. Als u toegang tot meerdere abonnementen hebt, moet u het juiste abonnement selecteren. Zie [Toegang beheren met RBAC en de Azure-portal](../role-based-access-control/role-assignments-portal.md) voor voorbeeldinstructies voor het toevoegen van een gebruiker aan een rol.

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 en 2017
In deze zelfstudie gebruikt u een on-premises SQL Server-database als een *brongegevensopslag*. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van deze on-premises SQL Server-database (bron) naar Blob-opslag (sink). Vervolgens maakt u een tabel met de naam **emp** in uw SQL Server-database en voegt u een paar voorbeeldvermeldingen in de tabel in.

1. Start SQL Server Management Studio. Als dit niet al is geïnstalleerd op uw computer, gaat u naar [SQL Server Management Studio downloaden](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

1. Maak verbinding met SQL Server-exemplaar met behulp van uw referenties.

1. Maak een voorbeelddatabase. Klik in de structuurweergave met de rechtermuisknop op **Databases** en selecteer **Nieuwe database**.
1. Voer in het venster **Nieuwe database** een naam in voor de database en selecteer **OK**.

1. Voer het volgende queryscript uit voor de database. Hiermee wordt de **emp**-tabel gemaakt en worden enkele voorbeeldgegevens ingevoegd in deze tabel. In de structuurweergave klikt u met de rechtermuisknop op de database die u hebt gemaakt en selecteert u **Nieuwe query**.

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

### <a name="azure-storage-account"></a>Azure Storage-account
In deze zelfstudie gaat u een algemeen Azure Storage-account (en dan met name Blob Storage) gebruiken als een doel/sink-gegevensopslag. Zie het artikel [Een opslagaccount maken](../storage/common/storage-account-create.md) als u geen Azure Storage-account hebt voor algemene doeleinden. De pijplijn in de data factory die u in deze zelfstudie gaat maken, kopieert gegevens van deze on-premises SQL Server-database (bron) naar Blob Storage (sink). 

#### <a name="get-the-storage-account-name-and-account-key"></a>De naam en sleutel van een opslagaccount ophalen
In deze zelfstudie gaat u de naam en sleutel van uw opslagaccount gebruiken. Voer de volgende stappen uit om de naam en sleutel van uw opslagaccount op te halen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com) met uw Azure-gebruikersnaam en -wachtwoord.

1. Selecteer **Alle services** in het linkerdeelvenster. Filter met behulp van het sleutelwoord **Opslag** en selecteer vervolgens **Opslagaccounts**.

    ![Zoeken naar Storage-account](media/doc-common-process/search-storage-account.png)

1. Filter in de lijst met opslagaccounts indien nodig voor uw opslagaccount. Selecteer vervolgens uw opslagaccount.

1. Selecteer in het venster **Opslagaccount** de optie **Toegangssleutels**.

1. Kopieer de waarden in de vakken **opslagaccountnaam** en **key1** en plak deze in Kladblok of een andere editor voor later gebruik in de zelfstudie.

#### <a name="create-the-adftutorial-container"></a>De container adftutorial maken
In deze sectie maakt u in uw Blob Storage een blobcontainer met de naam **adftutorial**.

1. Ga in het **venster Opslagaccount** naar **Overzicht**en selecteer **Containers**.

    ![De optie Blobs selecteren](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Selecteer **in** het venster Containers de optie **+ Container** om een nieuwe te maken.

1. In het venster **Nieuwe container** voert u bij **Naam****adftutorial** in. Selecteer vervolgens **Maken**.

1. Selecteer **adftutorial die** u zojuist hebt gemaakt in de lijst met containers.

1. Houd het venster **Container** voor **adftutorial** geopend. U gebruikt het om de uitvoer aan het einde van de zelfstudie te verifiëren. In Data Factory wordt automatisch in deze container de uitvoermap gemaakt, zodat u er zelf geen hoeft te maken.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory.

1. Open de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.
1. Selecteer links in het menu Een resource > **Analytics** > **Data Factory maken:** **Create a resource**

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

1. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet *wereldwijd uniek* zijn. Als het volgende foutbericht wordt weergegeven voor het naamveld, wijzigt u de naam van de data factory (bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory naming rules](naming-rules.md) (Naamgevingsregels Data Factory) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

   ![Naam nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

1. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.
1. Voer een van de volgende stappen uit voor **Resourcegroep**:

   - Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

   - Selecteer **Nieuw maken**en voer de naam van een resourcegroep in.
        
     Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.
1. Selecteer **V2** onder **Versie**.
1. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door Data Factory worden gebruikt, kunnen zich in andere regio's bevinden.
1. Selecteer **Maken**.

1. Nadat de creatie is voltooid, ziet u de pagina **Gegevensfabriek** zoals weergegeven in de afbeelding:

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
1. Selecteer de tegel **Maken en controleren** om de Data Factory-UI te openen op een afzonderlijk tabblad.


## <a name="create-a-pipeline"></a>Een pijplijn maken

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**. Er wordt automatisch een pijplijn voor u gemaakt. U ziet de pijplijn in de structuurweergave en de bijbehorende editor wordt geopend.

   ![Pagina Aan de slag](./media/doc-common-process/get-started-page.png)

1. Voer **SQLServerToBlobPipeline**in op het tabblad **Algemeen** onder aan het venster **Eigenschappen** voor **Naam**.

1. Vouw **verplaatsen & transformeren**uit in het gereedschap **Activiteiten.** Gebruik slepen en neerzetten om de activiteit **Kopiëren** naar het ontwerpoppervlak van de pijplijn te verplaatsen. Stel de naam van de activiteit in op **CopySqlServerToAzureBlobActivity**.

1. Ga in het venster **Eigenschappen** naar het tabblad **Bron** en selecteer **+ Nieuw**.

1. Zoek in het dialoogvenster **Nieuwe gegevensset** naar **SQL Server**. Selecteer **SQL Server**en selecteer **Doorgaan**.
    ![Nieuwe SqlServer-gegevensset](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. Voer **sqlserverdataset**in het dialoogvenster **Eigenschappen instellen** onder **Naam**in . Selecteer **onder Gekoppelde service**+ **Nieuw**. In deze stap maakt u een verbinding met het brongegevensexemplaar (SQL Server-database).

1. Voeg in het dialoogvenster **Nieuwe gekoppelde service** **naam** toe als **SqlServerLinkedService**. Selecteer **onder Verbinding maken via inburgeringsruntime** **+Nieuw**.  In deze sectie kunt u een zelf-hostende Integration Runtime maken en deze koppelen aan een on-premises computer met de SQL Server database. De zelf-hostende Integration Runtime is het onderdeel waarmee gegevens worden gekopieerd van SQL Server-database op uw computer naar Blob Storage.

1. Selecteer in het dialoogvenster Instellingen **voor** **insteltijd van integratie** de optie Zelf gehost en selecteer **Doorgaan**.

1. Voer onder naam **TutorialIntegrationRuntime**in. Selecteer vervolgens **Maken**.

1. Selecteer voor Instellingen **Klik hier om de expresinstelling voor deze computer te starten.** Met deze actie wordt de integratieruntime op de computer geïnstalleerd en geregistreerd bij Data Factory. U kunt er ook voor kiezen om handmatig te configureren door het installatiebestand te downloaden, uit te voeren, en de sleutel te gebruiken om de integratieruntime te registreren.
    ![Installatie van integratieruntime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. Selecteer In het venster **Express-instelling voor integratie (zelf gehost)** de optie **Sluiten** wanneer het proces is voltooid.

    ![Snelle installatie van integratieruntime (zelf-hostend)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. Controleer in het dialoogvenster **Nieuwe gekoppelde service (SQL Server)** of **TutorialIntegrationRuntime** is geselecteerd onder **Connect via integratieruntime**. Neem vervolgens de volgende stappen:

    a. Voer **SqlServerLinkedService** in bij **Naam**.

    b. Voer de naam van uw SQL Server-exemplaar in bij **Servernaam**.

    c. Geef de naam van de database met de **emp**-tabel op bij **Databasenaam**.

    d. Selecteer bij **Verificatietype** het juiste verificatietype in dat in Data Factory moet worden gebruikt om verbinding te maken met uw SQL Server-database.

    e. Bij **Gebruikersnaam** en **Wachtwoord**, typt u de gebruikersnaam en het wachtwoord. Als u een backslash wilt gebruiken (\\) in de naam van uw gebruikersaccount of server, moet u er voor het escapeteken (\\) gebruiken. Gebruik bijvoorbeeld *mydomain\\\\myuser*.

    f. Selecteer **Verbinding testen**. Deze stap is om te bevestigen dat Data Factory verbinding kan maken met uw SQL Server-database met behulp van de zelf gehoste runtime voor integratie die u hebt gemaakt.

    g. Als u de gekoppelde service wilt opslaan, selecteert u **Maken**.
 
    ![Nieuwe gekoppelde service (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. Nadat de gekoppelde service is gemaakt, bent u terug naar de pagina **Eigenschappen instellen** voor de SqlServerDataset. Voer de volgende stappen uit:

    a. Controleer of in **Gekoppelde service****SqlServerLinkedService** wordt weergegeven.

    b. Selecteer **onder Tabelnaam** **[dbo].[ emp]**.
    
    c. Selecteer **OK**.

1. Ga naar het tabblad met de **SQLServerToBlobPipeline** of selecteer **SQLServerToBlobPipeline** in de structuurweergave.

1. Ga naar het tabblad **Sink** onder in het venster **Eigenschappen** en selecteer **+ Nieuw**.

1. Selecteer **Azure Blob-opslag**in het dialoogvenster **Nieuwe gegevensset** . Selecteer vervolgens **Doorgaan**.

1. Kies in het dialoogvenster **Opmaak** selecteren het opmaaktype van uw gegevens. Selecteer vervolgens **Doorgaan**.

    ![Selectie van gegevensindeling](./media/doc-common-process/select-data-format.png)

1. Voer **in** het dialoogvenster Eigenschappen instellen **AzureBlobDataset** voor Naam in. Klik naast het tekstvak **Gekoppelde service** op **+Nieuw**.

1. Voer in het dialoogvenster **Nieuwe Gekoppelde Service (Azure Blob Storage)** **AzureStorageLinkedService** in als naam uw opslagaccount in de lijst **Met de naam van het opslagaccount.** Test de verbinding en selecteer **Vervolgens Maken** om de gekoppelde service te implementeren.

1. Nadat de gekoppelde service is gemaakt, bent u terug bij de pagina **Eigenschappen instellen.** Selecteer **OK**.

1. Open de gegevensset sink. Voer op het tabblad **Verbinding** de volgende stappen uit:

    a. Controleer of **AzureStorageLinkedService** is geselecteerd bij **Gekoppelde service**.

    b. Voer **in Bestandspad** **adftutorial/fromonprem** in voor het onderdeel **Container/Directory.** Als de uitvoermap niet bestaat in de container adftutorial, wordt de uitvoermap automatisch gemaakt door Data Factory.

    c. Selecteer Dynamische inhoud **toevoegen**voor het onderdeel **Bestand** .
    ![dynamische expressie voor omzetten van bestandsnaam](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Toevoegen `@CONCAT(pipeline().RunId, '.txt')`en vervolgens **Voltooien**selecteren. Met deze actie wordt de naam van het bestand met PipelineRunID.txt wijzigen.

1. Ga naar het tabblad met de pijplijn geopend of selecteer de pijplijn in de structuurweergave. Controleer of **AzureBlobDataset** is geselecteerd bij **Sink-gegevensset**.

1. Selecteer in de werkbalk de optie **Valideren** om de instellingen voor de pijplijn te valideren. Als u de **uitvoer van pipevalidatie wilt**sluiten, selecteert u het **>>** pictogram.
    ![pijplijn valideren](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Als u entiteiten wilt publiceren die u hebt gemaakt in Gegevensfabriek, selecteert u **Alle publiceren**.

1. Wacht tot u de **pop-up Publiceren voltooid** ziet. Als u de status van publiceren wilt controleren, selecteert u de koppeling **Meldingen weergeven** boven aan het venster. Selecteer **Sluiten** als u het meldingenvenster wilt sluiten.


## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren
Selecteer **Trigger toevoegen** op de werkbalk voor de pijplijn en selecteer Nu **activeren**.

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1. Ga naar het tabblad **Monitor.** U ziet de pijplijn die u handmatig hebt geactiveerd in de vorige stap.

1. Als u activiteitsruns wilt weergeven die zijn gekoppeld aan de pijplijnrun, selecteert u de **SQLServerToBlobPipeline-koppeling** onder *NAAM VAN DE PIJPLIJN*. 
    ![Pijplijnuitvoeringen controleren](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. Selecteer op de pagina **Activiteitsuitvoering** en selecteer de koppeling Details (brilafbeelding) om details over de kopieerbewerking te bekijken. Als u terug wilt gaan naar de weergave Pijplijnuitvoeringen, selecteert u **Alle pijplijnuitvoeringen** bovenaan.

## <a name="verify-the-output"></a>De uitvoer controleren
De uitvoermap *fromonprem* wordt automatisch door de pijplijn gemaakt in de `adftutorial` blobcontainer. Controleer of u het bestand *[pipeline().RunId].txt* in de uitvoermap ziet.


## <a name="next-steps"></a>Volgende stappen
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van de ene locatie naar een andere locatie in Blob Storage. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Een zelf-hostende Integration Runtime maken.
> * Gekoppelde services maakt voor SQL Server en Storage.
> * Gegevenssets maakt voor SQL Server en Blob Storage.
> * Een pijplijn maakt met een kopieeractiviteit om de gegevens te verplaatsen.
> * Een pijplijnuitvoering starten.
> * De pijplijnuitvoering controleert.

Zie [Ondersteunde gegevensopslag](copy-activity-overview.md#supported-data-stores-and-formats)voor een lijst met gegevensopslag die wordt ondersteund door Data Factory.

Ga door naar de volgende zelfstudie voor informatie over het bulksgewijs kopiëren van gegevens uit een bron naar een bestemming:

> [!div class="nextstepaction"]
>[Gegevens bulksgewijs kopiëren](tutorial-bulk-copy-portal.md)
