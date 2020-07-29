---
title: Privé-eindpunten gebruiken voor het maken van een Azure Data Factory-pijplijn
description: Deze zelfstudie bevat stapsgewijze instructies voor het maken van een data factory met een pijplijn met behulp van Azure Portal. In dit voorbeeld gebruikt de pijplijn de kopieeractiviteit om gegevens vanuit Azure Blob Storage naar een Azure SQL-database te kopiëren.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 05/15/2020
ms.author: jingwang
ms.openlocfilehash: 9458c89922fab8450b7cb8e202491d4c47e250e8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540479"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Kopieer de gegevens veilig vanuit Azure Blob-opslag naar een SQL-database met privé-eindpunten

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie maakt u een data factory met behulp van de Azure Data Factory-gebruikersinterface. **Met de pijplijn in deze data factory worden gegevens veilig gekopieerd van Azure Blob-opslag naar een Azure SQL-database (beide verlenen toegang aan geselecteerde netwerken) met behulp van privé-eindpunten in het door [Azure Data Factory beheerde virtueel netwerk](managed-virtual-network-private-endpoint.md).** Het configuratiepatroon in deze zelfstudie geldt voor het kopiëren van een gegevensarchief op basis van bestanden naar een relationeel gegevensarchief. Zie de tabel [Ondersteunde gegevensarchieven](https://docs.microsoft.com/azure/data-factory/copy-activity-overview) voor een lijst met gegevensarchieven die worden ondersteund als bron en als sink.

> [!NOTE]
>
> - Zie [Inleiding tot Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> * Een gegevensfactory maken
> * Een pijplijn met kopieeractiviteit maken


## <a name="prerequisites"></a>Vereisten
* **Azure-abonnement**. Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount**. U gebruikt de blobopslag als *bron*-gegevensopslag. Als u geen opslagaccount hebt, raadpleegt u het artikel [Een opslagaccount maken](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal) om een account te maken. **Zorg ervoor dat het opslagaccount alleen toegang toestaat vanuit ‘Geselecteerde netwerken’.** 
* **Azure SQL-database**. U gebruikt de database als *sink*-gegevensopslag. Als u geen Azure SQL-database hebt, raadpleegt u het artikel [Een SQL-database maken](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) om er een te maken. **Zorg ervoor dat het Azure SQL Database-account alleen toegang toestaat vanuit ‘Geselecteerde netwerken’.** 

### <a name="create-a-blob-and-a-sql-table"></a>Een blob en een SQL-tabel maken

Voer nu de volgende stappen uit om uw blobopslag en SQL database voor te bereiden voor gebruik in deze zelfstudie.

#### <a name="create-a-source-blob"></a>Een bron-blob maken

1. Start Kladblok. Kopieer de volgende tekst en sla deze op uw schijf op in het bestand **emp.txt**:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Maak in de blobopslag een container met de naam **adftutorial**. Maak een map aan met de naam **invoer** in deze container. Vervolgens kunt u het bestand **emp.txt** uploaden naar de map **invoer**. Gebruik de Azure-portal of hulpprogramma's zoals [Azure Storage Explorer](https://storageexplorer.com/) voor deze taken.

#### <a name="create-a-sink-sql-table"></a>Een SQL-sink-tabel maken

1. Gebruik het volgende SQL-script om de tabel **dbo.emp** te maken in uw SQL database:

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

1. Geef Azure-services toegang tot SQL Server. Zorg ervoor dat **Toegang tot Azure-services toestaan** is **ingeschakeld** voor SQL Server, zodat Data Factory gegevens naar SQL Server kan schrijven. U kunt deze instelling controleren en inschakelen door naar Azure SQL-server > Overzicht > Serverfirewall instellen > Instellen te gaan en de optie **Toegang tot Azure-services toestaan** in te stellen op **AAN**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken
In deze stap maakt u een data factory en start u de Data Factory-gebruikersinterface om een pijplijn te maken in de data factory.

1. Open **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.


2. Selecteer in het linkermenu **Een resource maken** > **Analyse** > **Data Factory**.

3. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**.

   De naam van de Azure-gegevensfactory moet *wereldwijd uniek* zijn. Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. (Gebruik dan bijvoorbeeld uwnaamADFTutorialDataFactory). Zie [Data Factory - Naamgevingsregels](https://docs.microsoft.com/azure/data-factory/naming-rules) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.

4. Selecteer het Azure-**abonnement** waarin u de data factory wilt maken.

5. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) voor meer informatie. 

6. Selecteer **V2** onder **Versie**.

7. Selecteer onder **Locatie** een locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en berekenservices (bijvoorbeeld Azure HDInsight) die door de data factory worden gebruikt, kunnen zich in andere regio's bevinden.


8. Selecteer **Maken**.


9. Als het maken is voltooid, ziet u de melding in het meldingencentrum. Selecteer **Naar resource gaan** om naar de pagina Data factory te gaan.

10. Selecteer de tegel **Maken en controleren** om de Data Factory-gebruikersinterface te openen op een afzonderlijk tabblad.

## <a name="create-an-azure-integration-runtime-in-adf-managed-virtual-network"></a>Een Azure Integration Runtime maken in een door ADF beheerd virtueel netwerk
In deze stap maakt u een Azure Integration Runtime en schakelt u het beheerde virtuele netwerk in.

1. Ga in de ADF-portal naar **Hub beheren** en klik op **Nieuwe** om een nieuwe Azure Integration Runtime te maken.
   ![Nieuwe Azure Integration Runtime maken](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
2. Kies ervoor om een Azure** Integration Runtime te maken.
   ![Nieuwe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/azure-ir.png)
3. **Virtueel netwerk** inschakelen.
   ![Nieuwe Azure Integration Runtime](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
4. Selecteer **Maken**.

## <a name="create-a-pipeline"></a>Een pijplijn maken
In deze stap maakt u een pijplijn met kopieeractiviteit in de data factory. De kopieeractiviteit kopieert gegevens van de blobopslag naar SQL Database. In de [Quickstart](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal) hebt u een pijplijn gemaakt met de volgende stappen:

1. Maak een gekoppelde service.
1. Maak invoer- en uitvoergegevenssets.
1. Maak een pijplijn.

In deze zelfstudie begint u met het maken van de pijplijn. Vervolgens maakt u gekoppelde services en gegevenssets wanneer u deze nodig hebt om de pijplijn te configureren.

1. Selecteer op de pagina **Aan de slag** de optie **Pijplijn maken**.

   ![Pijplijn maken](./media/doc-common-process/get-started-page.png)
1. Voer op het deelvenster **Eigenschappen** voor de pijplijn **CopyPipeline** in voor de **Naam** van de pijplijn.

1. Breid in de werkset **Activiteiten** de categorie **Verplaatsen en transformeren** uit. Sleep de activiteit **Gegevens kopiëren** uit de werkset en zet deze neer op het ontwerpoppervlak voor pijplijnen. Geef **CopyFromBlobToSql** op bij **Naam**.

    ![Kopieeractiviteit](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-source"></a>Bron configureren

>[!TIP]
>In deze zelfstudie gebruikt u *Accountsleutel* als verificatietype voor uw brongegevensarchief, maar u kunt zo nodig ook andere ondersteunde verificatiemethoden kiezen: *SAS URI*,*Service-principal* en *Beheerde identiteit*. Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#linked-service-properties) voor meer informatie.
>Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

#### <a name="create-source-dataset-and-linked-service"></a>Gegevensbronsets en gekoppelde services maken

1. Ga naar het tabblad **Bron**. Selecteer **+Nieuw** om een brongegevensset te maken.

1. Selecteer in het dialoogvenster **Nieuwe gegevensset** de optie **Azure Blob Storage** en selecteer vervolgens **Doorgaan**. De brongegevens bevinden zich in een blobopslag, daarom selecteert u **Azure Blob-opslag** voor de brongegevensset.

1. Selecteer in het dialoogvenster **Indeling selecteren** het indelingstype van uw gegevens en selecteer vervolgens **Doorgaan**.

1. Voer in het dialoogvenster **Eigenschappen instellen** als naam **SourceBlobDataset** in. Schakel het selectievakje voor **Eerste rij als header** in. Selecteer onder het tekstvak **Gekoppelde service** de optie **+ Nieuw**.

1. Voer in het dialoogvenster **Nieuwe gekoppelde service** als naam **AzureStorageLinkedService** in en selecteer uw opslagaccount in de lijst **Naam van opslagaccount**. 

1. Zorg ervoor dat u **Interactieve creatie** inschakelt. Het kan ongeveer 1 minuut duren voordat deze is ingeschakeld.

    ![Interactieve creatie](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Selecteer **Verbinding testen**, dit moet mislukken wanneer het opslagaccount alleen toegang toestaat vanuit het geselecteerde netwerk en vereist dat Azure Data Factory een privé-eindpunt maakt dat moet worden goedgekeurd voordat het kan worden gebruikt. In het foutbericht wordt een koppeling weergegeven om een **privé-eindpunt te maken** dat u kunt volgen om een beheerd privé-eindpunt te maken. *U kunt ook rechtstreeks naar het tabblad Beheren gaan en instructies volgen in [volgende sectie](#create-a-managed-private-endpoint) om een beheerd privé-eindpunt te maken*
> [!NOTE]
> Het tabblad Beheren is mogelijk niet beschikbaar voor alle exemplaren van data factory. Als u dit niet ziet, hebt u nog steeds toegang tot privé-eindpunten via het tabblad '**Auteur**' --> '**Verbindingen**'--> '**Privé-eindpunt**'
1. Houd het dialoogvenster geopend en ga vervolgens naar het hierboven geselecteerde opslagaccount.

1. Volg de instructies in [deze sectie](#approval-of-a-private-link-in-storage-account) om de persoonlijke koppeling goed te keuren.

1. Ga terug naar het dialoogvenster. **Test de verbinding** opnieuw en selecteer vervolgens **Maken** om de gekoppelde service te implementeren.

1. Nadat de gekoppelde service is gemaakt, wordt u teruggeleid naar de pagina **Eigenschappen instellen**. Selecteer naast **Bestandspad** de knop **Bladeren**.

1. Navigeer naar de map **adftutorial/input**, selecteer het bestand **emp.txt** en klik vervolgens op **OK**.

1. Selecteer **OK**. U wordt automatisch naar de pijplijnpagina geleid. Controleer op het tabblad **Bron** of **SourceBlobDataset** is geselecteerd. Selecteer **Gegevens vooraf bekijken** om een voorbeeld van de gegevens op deze pagina te bekijken.

    ![Brongegevensset](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u tijdens het testen van de bovenstaande verbinding niet op de hyperlink hebt geklikt, volgt u het volgende pad. Nu moet u een beheerd privé-eindpunt maken dat u verbindt met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad Beheren.
> [!NOTE]
> Het tabblad Beheren is mogelijk niet beschikbaar voor alle exemplaren van data factory. Als u dit niet ziet, hebt u nog steeds toegang tot privé-eindpunten via het tabblad '**Auteur**' --> '**Verbindingen**'--> '**Privé-eindpunt**'

1. Ga naar het gedeelte Beheerde privé-eindpunten.

1. Selecteer **en Nieuwe** onder Beheerde privé-eindpunten.

    ![Nieuw Beheerd privé-eindpunt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecteer de tegel Azure Blob Storage in de lijst en selecteer **Doorgaan**.

1. Voer de naam van het opslagaccount in dat u hierboven hebt gemaakt.

1. Selecteer **Maken**.

1. Na enkele seconden wachten moet u zien dat de privékoppeling een goedkeuring vereist.

1. Selecteer het privé-eindpunt dat u hierboven hebt gemaakt. U ziet een hyperlink waarmee u het privé-eindpunt kunt goedkeuren op het niveau van het opslagaccount.

    ![Privé-eindpunt beheren](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-storage-account"></a>Goedkeuring van een privékoppeling in het opslagaccount
1. Ga in het opslagaccount naar **Privé-eindpuntverbindingen** in het gedeelte Instellingen.

1. Tik het privé-eindpunt dat u hierboven hebt gemaakt en selecteer **Goedkeuren**.

    ![Keur het privé-eindpunt goed](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Voeg een beschrijving toe en klik op **ja**
1. Ga terug naar de sectie **Beheerde privé-eindpunten** van het tabblad **Beheren** in Azure Data Factory.
1. Het duurt ongeveer 1-2 minuten voordat de goedkeuring voor uw privé-eindpunt wordt weergegeven in de Azure Data Factory-gebruikersinterface.


### <a name="configure-sink"></a>Sink configureren
>[!TIP]
>In deze zelfstudie gebruikt u *SQL-verificatie* als het verificatietype voor uw sinkgegevensarchief, maar u kunt zo nodig ook andere ondersteunde verificatiemethoden kiezen: *Service-principal* en *Beheerde identiteit*. Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) voor meer informatie.
>Het ook raadzaam om een Azure Key Vault te gebruiken om geheimen voor gegevensarchieven veilig op te slaan. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

#### <a name="create-sink-dataset-and-linked-service"></a>Gegevenssinksets en gekoppelde services maken
1. Ga naar het tabblad **Sink** en selecteer **+Nieuw** om een sink-gegevensset te maken.

1. Voer in het dialoogvenster **Nieuwe gegevensset** 'SQL' in het zoekvak in om de connectoren te filteren, selecteer **Azure SQL Database** en selecteer vervolgens **Doorgaan**. In deze zelfstudie kopieert u gegevens naar een SQL database.

1. Voer in het dialoogvenster **Eigenschappen instellen** als naam **OutputSqlDataset** in. Selecteer **+ Nieuw** in de vervolgkeuzelijst **Gekoppelde service**. Een gegevensset moet worden gekoppeld aan een gekoppelde service. De gekoppelde service beschikt over de verbindingsreeks die door Data Factory wordt gebruikt om tijdens runtime een verbinding met de SQL-database tot stand te brengen. De dataset geeft informatie over de container, map en het bestand (optioneel) met de brongegevens.

1. Voer in het dialoogvenster **Nieuwe gekoppelde service (Azure SQL Database)** de volgende stappen uit:

    1. Geef **AzureSqlDatabaseLinkedService** op als **Naam**.
    1. Selecteer bij **Servernaam** uw SQL Server-exemplaar.
    1. Zorg ervoor dat u **Interactieve creatie** inschakelt.
    1. Selecteer uw SQL-database bij **Databasenaam**.
    1. Voer bij **Gebruikersnaam** de naam van de gebruiker in.
    1. Voer bij **Wachtwoord** het wachtwoord voor de gebruiker in.
    1. Selecteer **Verbinding testen**. Dit moet mislukken omdat de SQL-server alleen toegang toestaat vanuit ‘geselecteerde netwerken’ en vereist dat Azure Data Factory een privé-eindpunt maakt dat moet worden goedgekeurd voordat het kan worden gebruikt. In het foutbericht wordt een koppeling weergegeven om een **privé-eindpunt te maken** dat u kunt volgen om een beheerd privé-eindpunt te maken. *U kunt ook rechtstreeks naar het tabblad Beheren gaan en instructies volgen in volgende sectie om een beheerd privé-eindpunt te maken*
    1. Houd het dialoogvenster geopend en ga vervolgens naar het hierboven geselecteerde SQL-server.    
    1. Volg de instructies in [deze sectie](#approval-of-a-private-link-in-sql-server) om de persoonlijke koppeling goed te keuren.
    1. Ga terug naar het dialoogvenster. **Test de verbinding** opnieuw en selecteer vervolgens **Maken** om de gekoppelde service te implementeren.

1. U wordt automatisch naar het dialoogvenster **Eigenschappen instellen** geleid. Selecteer bij **Tabel** **[dbo].[emp]** . Selecteer vervolgens **OK**.

1. Ga naar het tabblad met de pijplijn en controleer bij **Sink-gegevensset** of **OutputSqlDataset** is geselecteerd.

    ![Tabblad Pijplijn](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)       

U kunt optioneel het schema van de bron toewijzen aan het overeenkomstige schema van het doel door [Schematoewijzing in kopieeractiviteit](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) te volgen.

#### <a name="create-a-managed-private-endpoint"></a>Een beheerd privé-eindpunt maken

Als u tijdens het testen van de bovenstaande verbinding niet op de hyperlink hebt geklikt, volgt u het volgende pad. Nu moet u een beheerd privé-eindpunt maken dat u verbindt met de gekoppelde service die hierboven is gemaakt.

1. Ga naar het tabblad Beheren.
1. Ga naar het gedeelte Beheerde privé-eindpunten.
1. Selecteer **en Nieuwe** onder Beheerde privé-eindpunten.

    ![Nieuw Beheerd privé-eindpunt](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Selecteer de tegel Azure SQL Database in de lijst en selecteer **Doorgaan**.
1. Voer de naam in van de SQL-Server die u hierboven hebt geselecteerd.
1. Selecteer **Maken**.
1. Na enkele seconden wachten moet u zien dat de privékoppeling een goedkeuring vereist.
1. Selecteer het privé-eindpunt dat u hierboven hebt gemaakt. U ziet een hyperlink waarmee u het privé-eindpunt kunt goedkeuren op het niveau van de SQL-server.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Goedkeuring van een privékoppeling in de SQL-server
1. Ga in de SQL-server naar **Privé-eindpuntverbindingen** in het gedeelte Instellingen.
1. Tik het privé-eindpunt dat u hierboven hebt gemaakt en selecteer **Goedkeuren**.
1. Voeg een beschrijving toe en klik op **ja**.
1. Ga terug naar de sectie **Beheerde privé-eindpunten** van het tabblad **Beheren** in Azure Data Factory.
1. Het duurt ongeveer 1-2 minuten voordat de goedkeuring voor uw privé-eindpunt wordt weergegeven.

#### <a name="debug-and-publish-the-pipeline"></a>Fouten opsporen in de pijplijn en de pijplijn publiceren

U kunt fouten opsporen in een pijplijn voordat u artefacten (gekoppelde services, gegevenssets en pijplijn) publiceert naar Data Factory of uw eigen Azure Repos Git-opslagplaats.

1. Selecteer **Fouten opsporen** om fouten op te sporen in de pijplijn. De status van de pijplijnuitvoering wordt weergegeven op het tabblad **Uitvoer** onder in het venster.
2. Zodra de pijplijn kan worden uitgevoerd, selecteert u **Alles publiceren** in de bovenste werkbalk. Met deze actie publiceert u entiteiten (gegevenssets en pijplijnen) die u hebt gemaakt met Data Factory.
3. Wacht tot u het bericht **Gepubliceerd** ziet. Om meldingsberichten te zien, klikt u op **Meldingen weergeven** rechts bovenin (belknop).


#### <a name="summary"></a>Samenvatting
Met de pijplijn in dit voorbeeld worden gegevens gekopieerd van Blob Storage naar Azure SQL DB met behulp van een privé-eindpunt in het beheerde virtuele netwerk. U hebt geleerd hoe u:

> * Een gegevensfactory maken
> * Een pijplijn met kopieeractiviteit maken

