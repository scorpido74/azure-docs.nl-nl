---
title: Terabytes aan gegevens laden in SQL Data Warehouse
description: Laat zien hoe 1 TB aan gegevens in Azure SQL Data Warehouse kan worden geladen binnen 15 minuten met Azure Data Factory
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: a6c133c0-ced2-463c-86f0-a07b00c9e37f
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 079d29c241cfbbdcc991f024c07b07b378670c10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80130893"
---
# <a name="load-1-tb-into-azure-sql-data-warehouse-under-15-minutes-with-data-factory"></a>1 TB laden in Azure SQL Data Warehouse binnen 15 minuten met Data Factory
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Zie Gegevens kopiëren van of naar Azure [SQL Data Warehouse met Data Factory](../connector-azure-sql-data-warehouse.md)als u de huidige versie van de gegevensfabriekservice gebruikt.


[Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) is een cloudgebaseerde, scale-out database die enorme hoeveelheden gegevens kan verwerken, zowel relationeel als niet-relationeel.  SQL Data Warehouse is gebaseerd op een enorm parallelle verwerkingsarchitectuur (MPP) en is geoptimaliseerd voor enterprise data warehouse workloads.  Het biedt cloudelasticiteit met de flexibiliteit om opslag en rekenkracht onafhankelijk te schalen.

Aan de slag met Azure SQL Data Warehouse is nu eenvoudiger dan ooit met **Azure Data Factory.**  Azure Data Factory is een volledig beheerde cloudgebaseerde data-integratieservice, die kan worden gebruikt om een SQL Data Warehouse te vullen met de gegevens van uw bestaande systeem, en u kostbare tijd bespaart bij het evalueren van SQL Data Warehouse en het bouwen van uw analytics Oplossingen. Dit zijn de belangrijkste voordelen van het laden van gegevens in Azure SQL Data Warehouse met Azure Data Factory:

* **Eenvoudig in te stellen:** 5-staps intuïtieve wizard zonder scripting vereist.
* **Uitgebreide ondersteuning voor gegevensopslag:** ingebouwde ondersteuning voor een uitgebreide reeks on-premises en cloudgebaseerde gegevensopslag.
* **Veilig en compatibel:** gegevens worden overgedragen via HTTPS of ExpressRoute en wereldwijde serviceaanwezigheid zorgt ervoor dat uw gegevens nooit de geografische grens verlaten
* **Ongeëvenaarde prestaties door PolyBase te gebruiken** - Polybase gebruiken is de meest efficiënte manier om gegevens naar Azure SQL Data Warehouse te verplaatsen. Met de functie staging blob u hoge belastingssnelheden bereiken van alle soorten gegevensopslag naast Azure Blob-opslag, die de Polybase standaard ondersteunt.

In dit artikel ziet u hoe u wizard Gegevensfabriekkopiëren gebruikt om 1 TB-gegevens van Azure Blob Storage in Azure SQL Data Warehouse in minder dan 15 minuten te laden, met een doorvoervan meer dan 1,2 GBps.

In dit artikel worden stapsgewijze instructies gegeven voor het verplaatsen van gegevens naar Azure SQL Data Warehouse met behulp van de wizard Kopiëren.

> [!NOTE]
>  Zie [Gegevens verplaatsen van en naar Azure SQL Data Warehouse naar en van Azure SQL Data Warehouse voor](data-factory-azure-sql-data-warehouse-connector.md) algemene informatie over de mogelijkheden van Data Factory bij het verplaatsen van gegevens van/naar Azure SQL Data Warehouse.
>
> U ook pijpleidingen bouwen met Visual Studio, PowerShell, enz. Zie [Zelfstudie: Kopieer gegevens van Azure Blob naar Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) voor een snelle walkthrough met stapsgewijze instructies voor het gebruik van de activiteit kopiëren in Azure Data Factory.  
>
>

## <a name="prerequisites"></a>Vereisten
* Azure Blob Storage: dit experiment maakt gebruik van Azure Blob Storage (GRS) voor het opslaan van TPC-H-testgegevensset.  Als u geen Azure-opslagaccount hebt, leest u [hoe u een opslagaccount maakt.](../../storage/common/storage-account-create.md)
* [TPC-H](http://www.tpc.org/tpch/) data: we gaan TPC-H gebruiken als testdataset.  Om dat te doen, `dbgen` moet u gebruik maken van de TPC-H toolkit, die u helpt bij het genereren van de dataset.  U broncode `dbgen` downloaden voor van [TPC Tools](http://www.tpc.org/tpc_documents_current_versions/current_specifications.asp) en compileren het zelf, of download de gecompileerde binaire van [GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/TPCHTools).  Voer dbgen.exe uit met de volgende opdrachten `lineitem` om een plat bestand van 1 TB te genereren voor tabel verspreid over 10 bestanden:

  * `Dbgen -s 1000 -S **1** -C 10 -T L -v`
  * `Dbgen -s 1000 -S **2** -C 10 -T L -v`
  * …
  * `Dbgen -s 1000 -S **10** -C 10 -T L -v`

    Kopieer nu de gegenereerde bestanden naar Azure Blob.  Raadpleeg [Gegevens verplaatsen van en naar een on-premises bestandssysteem met Azure Data Factory](data-factory-onprem-file-system-connector.md) voor hoe u dat doen met ADF Copy.    
* Azure SQL Data Warehouse: dit experiment laadt gegevens op in Azure SQL Data Warehouse gemaakt met 6.000 D6's

    Raadpleeg [Een Azure SQL Data Warehouse maken](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) voor gedetailleerde instructies over het maken van een SQL Data Warehouse-database.  Om de best mogelijke laadprestaties in SQL Data Warehouse te krijgen met Polybase, kiezen we het maximum aantal Data Warehouse Units (DDC's) dat is toegestaan in de prestatie-instelling, namelijk 6.000 DFC's.

  > [!NOTE]
  > Bij het laden vanuit Azure Blob zijn de prestaties voor het laden van gegevens direct evenredig met het aantal DTO's dat u configureert in het SQL Data Warehouse:
  >
  > 1 TB laden in 1.000 DWU SQL Data Warehouse duurt 87 minuten (~200 MBps-doorvoer) Het laden van 1 TB in 2.000 DWU SQL Data Warehouse duurt 46 minuten (~ 380 MBps-doorvoer) Het laden van 1 TB in 6.000 DWU SQL Data Warehouse duurt 14 minuten (~ 1,2 GBps-doorvoer)
  >
  >

    Als u een SQL Data Warehouse met 6.000 DOM's wilt maken, verplaatst u de schuifregelaar Prestaties helemaal naar rechts:

    ![Schuifregelaar Prestaties](media/data-factory-load-sql-data-warehouse/performance-slider.png)

    Voor een bestaande database die niet is geconfigureerd met 6.000 DDC's, u deze opschalen met Azure-portal.  Navigeer naar de database in azure-portal en er is een knop **Schalen** in het deelvenster **Overzicht** dat in de volgende afbeelding wordt weergegeven:

    ![Knop Schalen](media/data-factory-load-sql-data-warehouse/scale-button.png)    

    Klik **op** de knop Schalen om het volgende deelvenster te openen, de schuifregelaar naar de maximale waarde te verplaatsen en op **Knop Opslaan** te klikken.

    ![Dialoogvenster Schalen](media/data-factory-load-sql-data-warehouse/scale-dialog.png)

    Met dit experiment worden gegevens `xlargerc` in Azure SQL Data Warehouse geladen met behulp van de resourceklasse.

    Om de best mogelijke doorvoer te bereiken, moet kopiëren `xlargerc` worden uitgevoerd met een SQL Data Warehouse-gebruiker die tot de resourceklasse behoort.  Meer informatie over hoe u dit doen door [het voorbeeld van een gebruikersbronklasse](../../sql-data-warehouse/sql-data-warehouse-develop-concurrency.md)wijzigen te volgen.  
* Maak doeltabelschema in Azure SQL Data Warehouse-database door de volgende DDL-instructie uit te voeren:

    ```SQL  
    CREATE TABLE [dbo].[lineitem]
    (
        [L_ORDERKEY] [bigint] NOT NULL,
        [L_PARTKEY] [bigint] NOT NULL,
        [L_SUPPKEY] [bigint] NOT NULL,
        [L_LINENUMBER] [int] NOT NULL,
        [L_QUANTITY] [decimal](15, 2) NULL,
        [L_EXTENDEDPRICE] [decimal](15, 2) NULL,
        [L_DISCOUNT] [decimal](15, 2) NULL,
        [L_TAX] [decimal](15, 2) NULL,
        [L_RETURNFLAG] [char](1) NULL,
        [L_LINESTATUS] [char](1) NULL,
        [L_SHIPDATE] [date] NULL,
        [L_COMMITDATE] [date] NULL,
        [L_RECEIPTDATE] [date] NULL,
        [L_SHIPINSTRUCT] [char](25) NULL,
        [L_SHIPMODE] [char](10) NULL,
        [L_COMMENT] [varchar](44) NULL
    )
    WITH
    (
        DISTRIBUTION = ROUND_ROBIN,
        CLUSTERED COLUMNSTORE INDEX
    )
    ```
  Als de vereistestappen zijn voltooid, zijn we nu klaar om de kopieeractiviteit te configureren met de wizard Kopiëren.

## <a name="launch-copy-wizard"></a>De wizard Kopiëren starten
1. Log in bij de [Azure-portal](https://portal.azure.com).
2. Klik **op Een resource maken** vanuit de linkerbovenhoek, klik op Intelligentie + **analytics**en klik op **Gegevensfabriek**.
3. In het fabrieksdeelvenster **Nieuwe gegevens:**

   1. Voer **LoadIntoSQLDWDataFactory** in voor de **naam**.
       De naam van de Azure-gegevensfactory moet wereldwijd uniek zijn. Als u de fout ontvangt: **De naam van de gegevensfabriek "LoadIntoSQLDWDataFactory" is niet beschikbaar,** wijzigt u de naam van de gegevensfabriek (bijvoorbeeld uw naamLoadIntoSQLDWDataFactory) en probeert u opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](data-factory-naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.  
   2. Selecteer uw **Azure-abonnement**.
   3. Voer een van de volgende stappen uit voor de resourcegroep:
      1. Selecteer **Bestaande gebruiken** om een bestaande resourcegroep te selecteren.
      2. Selecteer **Nieuwe maken** als u een naam voor een resourcegroep wilt typen.
   4. Selecteer een **locatie** voor de gegevensfactory.
   5. Selecteer het selectievakje **Vastmaken aan dashboard** onderaan de blade.  
   6. Klik **op Maken**.
4. Nadat de creatie is voltooid, ziet u het blade van De **Gegevensfabriek** zoals weergegeven in de volgende afbeelding:

   ![Startpagina van de gegevensfactory](media/data-factory-load-sql-data-warehouse/data-factory-home-page-copy-data.png)
5. Op de startpagina van de gegevensfactory klikt u op de tegel **Gegevens kopiëren** om de **wizard Kopiëren** te openen.

   > [!NOTE]
   > Als u ziet dat de webbrowser is vastgelopen bij Autoriseren... schakelt u **Cookies van derden en sitegegevens blokkeren** uit, of u laat dit ingeschakeld en u maakt een uitzondering voor **login.microsoftonline.com** en opent u de wizard opnieuw.
   >
   >

## <a name="step-1-configure-data-loading-schedule"></a>Stap 1: Laadschema voor gegevens configureren
De eerste stap is het configureren van het laadschema voor gegevens.  

Op de pagina **Eigenschappen**:

1. **CopyFromBlobToAzureSqlDataWarehouse** invoeren voor **taaknaam**
2. Selecteer De optie **Eenmaal uitvoeren** selecteren.   
3. Klik op **Volgende**.  

    ![Wizard Kopiëren - pagina Eigenschappen](media/data-factory-load-sql-data-warehouse/copy-wizard-properties-page.png)

## <a name="step-2-configure-source"></a>Stap 2: Bron configureren
In deze sectie ziet u de stappen om de bron te configureren: Azure Blob met de TPC-H-regelbestanden van 1 TB.

1. Selecteer de **Azure Blob Storage** als gegevensarchief en klik op **Volgende**.

    ![Wizard Kopiëren - Bronpagina selecteren](media/data-factory-load-sql-data-warehouse/select-source-connection.png)

2. Vul de verbindingsgegevens voor het Azure Blob-opslagaccount in en klik op **Volgende**.

    ![Wizard Kopiëren - Bronverbindingsgegevens](media/data-factory-load-sql-data-warehouse/source-connection-info.png)

3. Kies de **map** met de TPC-H-regelitembestanden en klik op **Volgende**.

    ![Wizard kopiëren - invoermap selecteren](media/data-factory-load-sql-data-warehouse/select-input-folder.png)

4. Wanneer u op **Volgende**klikt, worden de instellingen voor bestandsindeling automatisch gedetecteerd.  Controleer of de kolomscheidingervoor '|' is in plaats van de standaardkomma ''.'.  Klik **op Volgende** nadat u een voorbeeld van de gegevens hebt bekeken.

    ![Wizard Kopiëren - instellingen voor bestandsindeling](media/data-factory-load-sql-data-warehouse/file-format-settings.png)

## <a name="step-3-configure-destination"></a>Stap 3: Doel configureren
In deze sectie ziet u `lineitem` hoe u de bestemming configureert: tabel in de Azure SQL Data Warehouse-database.

1. Kies **Azure SQL Data Warehouse** als bestemmingsarchief en klik op **Volgende**.

    ![Wizard Kopiëren - doelgegevensarchief selecteren](media/data-factory-load-sql-data-warehouse/select-destination-data-store.png)

2. Vul de verbindingsgegevens voor Azure SQL Data Warehouse in.  Zorg ervoor dat u de gebruiker opgeeft die lid is van de rol `xlargerc` (zie de sectie **Voorwaarden** voor gedetailleerde instructies) en klik op **Volgende**.

    ![Wizard Kopiëren - doelverbindingsgegevens](media/data-factory-load-sql-data-warehouse/destination-connection-info.png)

3. Kies de doeltabel en klik op **Volgende**.

    ![Wizard Kopiëren - tabeltoewijzingspagina](media/data-factory-load-sql-data-warehouse/table-mapping-page.png)

4. Laat op de pagina Schematoewijzing de optie Kolomtoewijzing toepassen onaangevinkt en klik op **Volgende**.

## <a name="step-4-performance-settings"></a>Stap 4: Prestatie-instellingen

**Polybase toestaan** is standaard ingeschakeld.  Klik op **Volgende**.

![Wizard Kopiëren - pagina schematoewijzing](media/data-factory-load-sql-data-warehouse/performance-settings-page.png)

## <a name="step-5-deploy-and-monitor-load-results"></a>Stap 5: Loadresults implementeren en bewaken
1. Klik **op De** knop Voltooien om te implementeren.

    ![Wizard Kopiëren - overzichtspagina](media/data-factory-load-sql-data-warehouse/summary-page.png)

2. Nadat de implementatie is `Click here to monitor copy pipeline` voltooid, klikt u om de voortgang van de kopieeruitvoering te controleren. Selecteer de kopiepijplijn die u hebt gemaakt in de lijst **ActiviteitWindows.**

    ![Wizard Kopiëren - overzichtspagina](media/data-factory-load-sql-data-warehouse/select-pipeline-monitor-manage-app.png)

    U de gegevens van de kopierun weergeven in het **deelvenster Activiteitsvensterverkenner** in het rechterdeelvenster, inclusief het gegevensvolume dat van de bron wordt gelezen en naar bestemming, duur en de gemiddelde doorvoer voor de run is geschreven.

    Zoals u zien in de volgende schermafbeelding, duurde het kopiëren van 1 TB van Azure Blob Storage naar SQL Data Warehouse 14 minuten, waardoor effectief 1,22 GBps-doorvoer werd bereikt!

    ![Wizard Kopiëren - dialoogvenster geslaagd](media/data-factory-load-sql-data-warehouse/succeeded-info.png)

## <a name="best-practices"></a>Aanbevolen procedures
Hier volgen enkele aanbevolen procedures voor het uitvoeren van uw Azure SQL Data Warehouse-database:

* Gebruik een grotere resourceklasse bij het laden in een CLUSTERED COLUMNSTORE-index.
* Voor efficiëntere joins u overwegen hashdistributie te gebruiken via een geselecteerde kolom in plaats van standaard verdeling van round robin.
* Voor hogere laadsnelheden u overwegen heap te gebruiken voor tijdelijke gegevens.
* Maak statistieken nadat u Azure SQL Data Warehouse hebt geladen.

Zie [Aanbevolen procedures voor Azure SQL Data Warehouse](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
* [Wizard Gegevensfabriek kopiëren](data-factory-copy-wizard.md) - In dit artikel vindt u informatie over de wizard Kopiëren.
* [Kopieer activiteit prestaties en tuning gids](data-factory-copy-activity-performance.md) - Dit artikel bevat de referentie prestatiemetingen en tuning gids.
