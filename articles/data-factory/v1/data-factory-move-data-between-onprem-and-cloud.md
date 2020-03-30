---
title: Gegevens verplaatsen - Data Management Gateway
description: Stel een gegevensgateway in om gegevens te verplaatsen tussen on-premises en de cloud. Gebruik Data Management Gateway in Azure Data Factory om uw gegevens te verplaatsen.
services: data-factory
documentationcenter: ''
author: nabhishek
manager: anandsub
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: be797f76988c924503e11b6f66cce899b515e3a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982202"
---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Gegevens verplaatsen tussen on-premises bronnen en de cloud met Data Management Gateway
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [gegevens kopiëren tussen on-premises en cloud met Data Factory.](../tutorial-hybrid-copy-powershell.md)

Dit artikel geeft een overzicht van gegevensintegratie tussen on-premises datastores en clouddatastores met Behulp van Data Factory. Het bouwt voort op het artikel [Gegevensverplaatsingsactiviteiten](data-factory-data-movement-activities.md) en andere kernconcepten van de datafabriek: [gegevenssets](data-factory-create-datasets.md) en [pijplijnen.](data-factory-create-pipelines.md)

## <a name="data-management-gateway"></a>Gegevensbeheergateway
U moet Data Management Gateway installeren op uw on-premises machine om het verplaatsen van gegevens van/naar een on-premises gegevensarchief mogelijk te maken. De gateway kan worden geïnstalleerd op dezelfde machine als het gegevensarchief of op een andere machine, zolang de gateway verbinding kan maken met het gegevensarchief.

> [!IMPORTANT]
> Zie [artikel Data Management Gateway](data-factory-data-management-gateway.md) voor meer informatie over Data Management Gateway.

In de volgende walkthrough ziet u hoe u een gegevensfabriek maakt met een pijplijn die gegevens verplaatst van een on-premises **SQL Server-database** naar een Azure blob-opslag. Als onderdeel van de procedure installeert en configureert u de gegevensbeheergateway op uw computer.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Walkthrough: on-premises gegevens kopiëren naar de cloud
In deze walkthrough doe je de volgende stappen:

1. Een gegevensfactory maken.
2. Maak een datamanagementgateway.
3. Maak gekoppelde services voor bron- en sinkgegevensarchieven.
4. Maak gegevenssets om invoer- en uitvoergegevens weer te geven.
5. Een pijplijn maakt met een kopieeractiviteit om de gegevens te verplaatsen.

## <a name="prerequisites-for-the-tutorial"></a>Vereisten voor de zelfstudie
Voordat u met deze walkthrough begint, moet u de volgende vereisten hebben:

* **Azure-abonnement**.  Als u geen abonnement hebt, kunt u binnen een paar minuten een gratis proefaccount maken. Zie het [gratis proefversie-artikel](https://azure.microsoft.com/pricing/free-trial/) voor meer informatie.
* **Azure Storage-account**. U gebruikt de blob-opslag als een **gegevensarchief voor bestemming/gootsteen** in deze zelfstudie. Als u geen Azure Storage-account hebt, raadpleegt u het artikel [Een opslagaccount maken](../../storage/common/storage-account-create.md) voor de stappen voor het maken van een account.
* **SQL Server**. In deze zelfstudie gebruikt u een on-premises SQL Server-database als een **brongegevensopslag**.

## <a name="create-data-factory"></a>Een gegevensfactory maken
In deze stap gebruikt u de Azure-portal om een Azure Data Factory-exemplaar met de naam **ADFTutorialOnPremDF**te maken.

1. Log in bij de [Azure-portal](https://portal.azure.com).
2. Klik **op Een resource maken,** klik op Intelligentie + **analytics**en klik op **Gegevensfabriek**.

   ![Nieuw -> DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Typ **ADFTutorialOnPremDF** voor de naam op de pagina **Nieuwe gegevensfabriek.**

    ![Toevoegen aan startbord](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de fout ontvangt: **De naam van de gegevensfabriek 'ADFTutorialOnPremDF' is niet beschikbaar,** wijzigt u de naam van de gegevensfabriek (bijvoorbeeld yournameADFTutorialOnPremDF) en probeert u opnieuw te maken. Gebruik deze naam in plaats van ADFTutorialOnPremDF terwijl u de resterende stappen in deze zelfstudie uitvoert.
   >
   > De naam van de gegevensfabriek kan in de toekomst als **DNS-naam** worden geregistreerd en dus openbaar zichtbaar worden.
   >
   >
4. Selecteer het **Azure-abonnement** waarvoor u de gegevensfactory wilt maken.
5. Selecteer een bestaande **resourcegroep** of maak een resourcegroep. Maak voor de zelfstudie een resourcegroep met de naam **ADFTutorialResourceGroup**.
6. Klik **op Maken** op de pagina Nieuwe **gegevensfabriek.**

   > [!IMPORTANT]
   > Als u Data Factory-exemplaren wilt maken, moet u lid zijn van de rol [Inzender Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) op abonnements-/resourcegroepsniveau.
   >
   >
7. Nadat het maken is voltooid, ziet u de pagina **Gegevensfabriek** zoals weergegeven in de volgende afbeelding:

   ![Startpagina gegevensfabriek](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Gateway maken
1. Klik op de pagina **Gegevensfabriek** op **Auteur en implementeer** tegel om de **editor** voor de gegevensfabriek te starten.

    ![Tegel Ontwerpen en implementeren](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Klik in de Data Factory Editor op **... Meer** op de werkbalk en klik vervolgens op **Nieuwe gegevensgateway**. U ook met de rechtermuisknop op **Gegevensgateways** in de structuurweergave klikken en op **Nieuwe gegevensgateway klikken.**

   ![Nieuwe gegevensgateway op werkbalk](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Voer op de pagina **Maken** **adftutorialgateway** voor de **naam**in en klik op **OK**.     

    ![Pagina Gateway maken](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

    > [!NOTE]
    > In deze walkthrough maakt u de logische gateway met slechts één knooppunt (on-premises Windows-machine). U een datamanagementgateway uitschalen door meerdere on-premises machines aan de gateway te koppelen. U worden opgeschaald door het aantal taken voor gegevensverplaatsing te verhogen die gelijktijdig op een knooppunt kunnen worden uitgevoerd. Deze functie is ook beschikbaar voor een logische gateway met één knooppunt. Zie [Scaling data management gateway in Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) artikel voor meer informatie.  
4. Klik **op** de pagina Configureren **op Direct installeren op deze computer**. Met deze actie wordt het installatiepakket voor de gateway gedownload, worden de gateway op de computer geïnstalleerd, geïnstalleerd, geconfigureerd en registreert deze.  

   > [!NOTE]
   > Gebruik Internet Explorer of een microsoft ClickOnce-compatibele webbrowser.
   >
   > Als u met Chrome werkt, gaat u naar de [Chrome Web Store](https://chrome.google.com/webstore/), zoekt u op het trefwoord 'ClickOnce', en kiest en installeert u een van de ClickOnce-extensies.
   >
   > Doe hetzelfde voor Firefox (installeer add-in). Klik op **Menu openen** op de werkbalk **(drie horizontale lijnen** in de rechterbovenhoek), klik op **Invoegtoepassingen,** zoek met het trefwoord 'ClickOnce', kies een van de ClickOnce-extensies en installeer deze.    
   >
   >

    ![Gateway - Pagina configureren](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Op deze manier is de eenvoudigste manier (met één klik) om de gateway in één stap te downloaden, installeren, configureren en registreren. U zien dat de microsoft **Data Management Gateway Configuration Manager-toepassing** op uw computer is geïnstalleerd. U de uitvoerbare **ConfigManager.exe** ook vinden in de map: **C:\Program Files\Microsoft Data Management Gateway\2.0\Gedeeld**.

    U gateway ook handmatig downloaden en installeren met behulp van de koppelingen op deze pagina en deze registreren met behulp van de sleutel in het tekstvak **NIEUWE SLEUTEL.**

    Zie het artikel [Gegevensbeheergateway](data-factory-data-management-gateway.md) voor alle details over de gateway.

   > [!NOTE]
   > U moet een beheerder op de lokale computer zijn om de Data Management Gateway te installeren en te configureren. U extra gebruikers toevoegen aan de lokale **Windows-groep Gegevensbeheergatewaygebruikers.** De leden van deze groep kunnen het hulpprogramma Gegevensbeheergateway Configuratiebeheer gebruiken om de gateway te configureren.
   >
   >
5. Wacht een paar minuten of wacht tot u het volgende bericht ziet:

    ![Gateway-installatie geslaagd](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Start de toepassing **Data Management Gateway Configuration Manager** op uw computer. Typ **Gegevensbeheergateway** **in het venster Zoeken** om toegang te krijgen tot dit hulpprogramma. U de uitvoerbare **ConfigManager.exe** ook vinden in de map: **C:\Program Files\Microsoft Data Management Gateway\2.0\Gedeeld**

    ![Gatewayconfiguratiebeheer](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Bevestig dat `adftutorialgateway is connected to the cloud service` je bericht ziet. De statusbalk onderaan geeft **verbonden aan de cloudservice** weer, samen met een **groen vinkje**.

    Op het tabblad **Start** u ook de volgende bewerkingen uitvoeren:

   * **Registreer** een gateway met een sleutel van de Azure-portal met behulp van de knop Register.
   * **Stop** de Data Management Gateway Host Service die op uw gatewaymachine wordt uitgevoerd.
   * **Plan updates** die op een bepaald tijdstip van de dag moeten worden geïnstalleerd.
   * Weergeven wanneer de gateway voor het laatst is **bijgewerkt.**
   * Geef het tijdstip op waarop een update voor de gateway kan worden geïnstalleerd.
8. Ga naar het tabblad **Instellingen.** Het certificaat dat is opgegeven in de sectie **Certificaat** wordt gebruikt om referenties te versleutelen/decoderen voor het on-premises gegevensarchief dat u op de portal opgeeft. (facultatief) Klik in plaats **daarvan op Wijzigen** om uw eigen certificaat te gebruiken. Standaard gebruikt de gateway het certificaat dat automatisch wordt gegenereerd door de service Data Factory.

    ![Gatewaycertificaatconfiguratie](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    U ook de volgende acties uitvoeren op het tabblad **Instellingen:**

   * Het certificaat dat door de gateway wordt gebruikt weergeven of exporteren.
   * Wijzig het HTTPS-eindpunt dat door de gateway wordt gebruikt.    
   * Stel een HTTP-proxy in die door de gateway moet worden gebruikt.     
9. (facultatief) Schakel naar het tabblad **Diagnostische gegevens** in, schakel de optie **Verbose-logboekregistratie inschakelen** in als u verbose-logboekregistratie wilt inschakelen die u gebruiken om eventuele problemen met de gateway op te lossen. De logboekinformatie is te vinden in **Logboeken** onder het knooppunt**Gegevensbeheergateway** **voor toepassingen en services.** -> 

    ![Tabblad Diagnostische gegevens](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    U ook de volgende acties uitvoeren op het tabblad **Diagnostische gegevens:**

   * Gebruik de sectie **Testverbinding** met een on-premises gegevensbron met behulp van de gateway.
   * Klik **op Logboeken weergeven** om het logboek van de gegevensbeheergateway in een logboekvenster te bekijken.
   * Klik **op Logboeken verzenden** om een zip-bestand met logboeken van de afgelopen zeven dagen naar Microsoft te uploaden om het oplossen van uw problemen te vergemakkelijken.
10. Selecteer op het tabblad **Diagnostische** gegevens in de sectie **Testverbinding** **sqlserver** voor het type gegevensarchief, voer de naam van de databaseserver in, naam van de database, geef verificatietype op, voer gebruikersnaam en wachtwoord in en klik op **Testen** om te testen of de gateway verbinding kan maken met de database.
11. Ga naar de webbrowser en klik in de **Azure-portal**op **OK** op de pagina **Configureren** en vervolgens op de pagina **Nieuwe gegevensgateway.**
12. U ziet **adftutorialgateway** onder **Gegevensgateways** in de structuurweergave aan de linkerkant.  Als u erop klikt, ziet u de bijbehorende JSON.

## <a name="create-linked-services"></a>Gekoppelde services maken
In deze stap maakt u twee gekoppelde services: **AzureStorageLinkedService** en **SqlServerLinkedService.** De **SqlServerLinkedService** koppelt een on-premises SQL Server-database en de **AzureStorageLinkedService-gekoppelde** service koppelt een Azure blob-winkel aan de gegevensfabriek. U maakt later in deze walkthrough een pijplijn die gegevens uit de on-premises SQL Server-database kopieert naar de Azure blob store.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Een gekoppelde service toevoegen aan een on-premises SQL Server-database
1. Klik in de **datafabriekeditor**op **Nieuw gegevensarchief** op de werkbalk en selecteer **SQL Server**.

   ![Nieuwe SQL Server-gekoppelde service](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Ga in de **JSON-editor** aan de rechterkant de volgende stappen uit:

   1. Geef voor de **gatewayName** **adftutorialgateway**op .    
   2. Ga in de **connectionString**de volgende stappen uit:    

      1. Voer **voor servernaam**de naam in van de server die de SQL Server-database host.
      2. Voer **voor databasenaam**de naam van de database in.
      3. Klik **op de** knop Versleutelen op de werkbalk. U ziet de toepassing Credentials Manager.

         ![Toepassing Credentials Manager](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Geef in het dialoogvenster **Referenties instellen** het verificatietype, de gebruikersnaam en het wachtwoord op en klik op **OK.** Als de verbinding is geslaagd, worden de versleutelde referenties opgeslagen in het JSON en wordt het dialoogvenster gesloten.
      5. Sluit het lege browsertabblad dat het dialoogvenster heeft gestart als het niet automatisch wordt gesloten en ga terug naar het tabblad met de Azure-portal.

         Op de gatewaymachine worden deze referenties **versleuteld** met behulp van een certificaat waarvan de Data Factory-service eigenaar is. Zie Referenties instellen als u het certificaat wilt gebruiken dat is gekoppeld aan de Data Management Gateway.    
   3. Klik op **Implementeren** op de opdrachtbalk om de SQL Server-gekoppelde service te implementeren. U ziet de gekoppelde service in de structuurweergave.

      ![SQL Server-gekoppelde service in de structuurweergave](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Een gekoppelde service toevoegen voor een Azure-opslagaccount
1. Klik in de **datafabriekeditor**op **Nieuw gegevensarchief** op de opdrachtbalk en klik op **Azure-opslag**.
2. Voer de naam van uw Azure-opslagaccount in voor de **accountnaam**.
3. Voer de sleutel in voor uw Azure-opslagaccount voor de **accountsleutel**.
4. Klik **op Implementeren** om de **AzureStorageLinkedService**te implementeren.

## <a name="create-datasets"></a>Gegevenssets maken
In deze stap maakt u invoer- en uitvoergegevenssets die invoer- en uitvoergegevenssets voor de kopieerbewerking vertegenwoordigen (on-premises SQL Server-database => Azure Blob-opslag). Voordat u de gegevenssets maakt, voert u de volgende stappen uit (gedetailleerde stappen vindt u na de lijst):

* Maak een tabel met de naam **emp** in de SQL Server-database die u als gekoppelde service hebt toegevoegd aan de gegevensfactory, en voer een aantal voorbeeldvermeldingen toe aan de tabel.
* Maak een blobcontainer met de naam **adftutorial** in het Azure Blob-opslagaccount dat u als gekoppelde service hebt toegevoegd aan de gegevensfactory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>On-premises SQL Server voorbereiden op de zelfstudie
1. Gebruik in de database die u hebt opgegeven voor de met de on-premises SQL-server gekoppelde service, (**SqlServerLinkedService**) het volgende SQL-script om de tabel **emp** te maken in de database.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Voer een aantal voorbeelden in de tabel in:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Invoergegevensset maken

1. Klik in de **Data Factory Editor**op **... Klik meer**op **Nieuwe gegevensset** op de opdrachtbalk en klik op **sql server-tabel**.
2. Vervang de JSON in het rechterdeelvenster door de volgende tekst:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Houd rekening met de volgende punten:

   * **type** is ingesteld op **SqlServerTable**.
   * **tableName** is ingesteld op **emp**.
   * **linkedServiceName** is ingesteld op **SqlServerLinkedService** (u had deze gekoppelde service eerder in deze walkthrough gemaakt.).
   * Voor een invoergegevensset die niet wordt gegenereerd door een andere pijplijn in Azure Data Factory, moet u **extern** instellen voor **true**. Het geeft aan dat de invoergegevens buiten de Azure Data Factory-service worden geproduceerd. U optioneel extern gegevensbeleid opgeven met het element **externalData** in de sectie **Beleid.**    

   Zie [Gegevens verplaatsen naar/van SQL Server](data-factory-sqlserver-connector.md) voor meer informatie over JSON-eigenschappen.
3. Klik **op Implementeren** op de opdrachtbalk om de gegevensset te implementeren.  

### <a name="create-output-dataset"></a>Uitvoergegevensset maken

1. Klik in de **datafabriekeditor**op **Nieuwe gegevensset** op de opdrachtbalk en klik op **Azure Blob-opslag**.
2. Vervang de JSON in het rechterdeelvenster door de volgende tekst:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Houd rekening met de volgende punten:

   * **type** is ingesteld op **AzureBlob**.
   * **linkedServiceName** is ingesteld op **AzureStorageLinkedService** (u hebt deze gekoppelde service in stap 2 gemaakt).
   * **folderPath** is ingesteld op **adftutorial/outfromonpremdf,** waarbij outfromonpremdf de map in de adftutorial-container is. Maak de **adftutorial-container** als deze nog niet bestaat.
   * De **beschikbaarheid** wordt ingesteld op **elk uur** (de **frequentie** wordt ingesteld op **elk uur** en het **interval** wordt ingesteld op **1**).  De service Gegevensfabriek genereert elk uur een uitvoergegevenssegment in de **emp-tabel** in de Azure SQL-database.

   Als u geen **bestandsnaam** opgeeft voor een **uitvoertabel,** worden de gegenereerde `Data.<Guid>.txt` bestanden in de **mapPath** in de volgende indeling benoemd: (bijvoorbeeld: Gegevens.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Als u **folderPath** en **fileName** dynamisch wilt instellen op basis van de **SliceStart**-tijd, gebruikt u de eigenschap partitionedBy. In het volgende voorbeeld worden voor folderPath Year, Month en Day gebruikt van de SliceStart-waarde (tijd waarop is begonnen met het verwerken van het segment). Voor fileName wordt gebruikgemaakt van Hour van de SliceStart-waarde. Als er bijvoorbeeld een segment wordt geproduceerd voor 2014-10-20T08:00:00, wordt folderName ingesteld op wikidatagateway/wikisampledataout/2014/10/20 en wordt fileName ingesteld op 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Zie [Gegevens verplaatsen naar/vanuit Azure Blob Storage](data-factory-azure-blob-connector.md) voor meer informatie over JSON-eigenschappen.
3. Klik **op Implementeren** op de opdrachtbalk om de gegevensset te implementeren. Controleer of u beide gegevenssets in de structuurweergave ziet.  

## <a name="create-pipeline"></a>Pijplijn maken
In deze stap maakt u een **pijplijn** met één **kopieeractiviteit** die **EmpOnPremSQLTable** als invoer en **UitvoerBlobTable** als uitvoer gebruikt.

1. Klik in De Editor van de Fabriek van gegevens op **... Meer**en klik op **Nieuwe pijplijn**.
2. Vervang de JSON in het rechterdeelvenster door de volgende tekst:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on premises SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on premises SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
               }
             },
             "Policy": {
               "concurrency": 1,
               "executionPriorityOrder": "NewestFirst",
               "style": "StartOfInterval",
               "retry": 0,
               "timeout": "01:00:00"
             }
           }
         ],
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Vervang de waarde van de eigenschap **start** door de huidige dag en de waarde **end** door de volgende dag.
   >
   >

   Houd rekening met de volgende punten:

   * In de sectie Activiteiten is er alleen activiteit waarvan **het type** is ingesteld op **Kopiëren**.
   * **Invoer** voor de activiteit is ingesteld op **EmpOnPremSQLTable** en **de uitvoer** voor de activiteit is ingesteld op **OutputBlobTable**.
   * In de sectie **typeEigenschappen** wordt **SqlSource** opgegeven als het **brontype** en **blobsink** is opgegeven als het **gootsteentype**.
   * SQL-query `select * from emp` is opgegeven voor de eigenschap **sqlReaderQuery** van **SqlSource**.

   Zowel de begin- als einddatum en -tijd moeten de [ISO-indeling](https://en.wikipedia.org/wiki/ISO_8601) hebben. Bijvoorbeeld: 2014-10-14T16:32:41Z. De **eindtijd** is optioneel, maar we gebruiken hem in deze zelfstudie.

   Als u geen waarde opgeeft voor de **eigenschap end,** wordt deze berekend als "**start + 48 uur**". Als u de pijplijn voor onbepaalde tijd wilt uitvoeren, geeft u **9/9/9999** op als waarde voor de eigenschap **end**.

   U definieert de tijdsduur waarin de gegevenssegmenten worden verwerkt op basis van de **beschikbaarheidseigenschappen** die zijn gedefinieerd voor elke gegevensset Azure Data Factory.

   In het bovenstaande voorbeeld zijn er 24 gegevenssegmenten omdat er elk uur één gegevenssegment wordt gemaakt.        
3. Klik **op Implementeren** op de opdrachtbalk om de gegevensset te implementeren (tabel is een rechthoekige gegevensset). Controleer of de pijplijn wordt weergegeven in de structuurweergave onder het knooppunt **Pijplijnen.**  
4. Klik nu twee keer op **X** om de pagina te sluiten om terug te gaan naar de pagina **Gegevensfabriek** voor de **ADFTutorialOnPremDF.**

**Gefeliciteerd!** U hebt een Azure-gegevensfabriek, gekoppelde services, gegevenssets en een pijplijn gemaakt en de pijplijn gepland.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>De gegevensfactory weergeven in een diagramweergave
1. Klik in de **Azure-portal**op **Diagramtegel** op de startpagina voor de **ADFTutorialOnPremDF-gegevensfabriek.** :

    ![Diagramkoppeling](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. U ziet een diagram dat lijkt op de volgende afbeelding:

    ![Diagramweergave](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    U inzoomen, uitzoomen, inzoomen op 100%, inzoomen op geschiktheid, automatisch pijplijnen en gegevenssets plaatsen en regelgegevens weergeven (hoogtepunten upstream en downstream items van geselecteerde items).  U dubbelklikken op een object (invoer/uitvoergegevensset of pijplijn) om de eigenschappen ervoor te bekijken.

## <a name="monitor-pipeline"></a>De pijplijn bewaken
In deze stap gebruikt u Azure Portal om te controleren wat er gebeurt in een Azure data factory. U kunt ook PowerShell-cmdlets gebruiken om gegevenssets en pijplijnen te bewaken. Zie [Pijplijnen controleren en beheren](data-factory-monitor-manage-pipelines.md)voor meer informatie over monitoring.

1. Dubbelklik in het diagram op **EmpOnPremSQLTable**.  

    ![EmpOnPremSQLTable-segmenten](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Alle segmenten van de gegevens zijn in de status **Gereed** staan omdat de pijplijnduur (begintijd tot eindtijd) in het verleden is. Het is ook omdat u de gegevens in de SQL Server-database hebt ingevoegd en er de hele tijd is. Controleer of er geen segmenten worden weergegeven in de sectie **Probleemsegmenten** onderaan. Als u alle segmenten wilt weergeven, klikt u onder aan de lijst met segmenten op **Meer weergeven.**
3. Klik nu op de pagina **Gegevenssets** op **OutputBlobTable**.

    ![OputputBlobTable-segmenten](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Klik op een gegevenssegment uit de lijst en u ziet de pagina **Gegevenssegment.** U ziet activiteitsuitvoeringen voor het segment. U ziet meestal slechts één activiteit worden uitgevoerd.  

    ![Gegevenssegmentblad](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Als het segment niet de status **Gereed** heeft, kunt u de upstreamsegmenten bekijken die niet de status Gereed hebben en die voorkomen dat de huidige status wordt uitgevoerd. U ziet deze segmenten in de lijst **Upstreamsegmenten die niet gereed zijn**.
5. Klik op de **activiteitsrun** in de lijst onderaan om **details van activiteitsrun**te bekijken.

   ![Pagina Details van activiteitsrun](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   U ziet informatie zoals doorvoer, duur en de gateway die wordt gebruikt om de gegevens over te dragen.
6. Klik op **X** om alle pagina's te sluiten totdat u
7. ga terug naar de startpagina voor de **ADFTutorialOnPremDF**.
8. (facultatief) Klik **op Pijplijnen,** klik op **ADFTutorialOnPremDF**en doorboor invoertabellen **(verbruikte)** of uitvoergegevenssets (**Geproduceerd**).
9. Gebruik hulpprogramma's zoals [Microsoft Storage Explorer](https://storageexplorer.com/) om te controleren of er voor elk uur een blob/bestand wordt gemaakt.

   ![Azure Opslagverkenner](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Volgende stappen
* Zie het artikel [Data Management Gateway](data-factory-data-management-gateway.md) voor alle details over de Data Management Gateway.
* Zie [Gegevens kopiëren van Azure Blob naar Azure SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor meer informatie over het gebruik van Activiteit kopiëren om gegevens van een brongegevensarchief naar een sink-gegevensarchief te verplaatsen.
