---
title: Gegevens laden in Azure SQL Data Warehouse
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure SQL Data Warehouse
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/16/2020
ms.openlocfilehash: 1a764f392402acf9aa405468470d0fb6f680d755
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461074"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens in Azure SQL Data Warehouse laden met behulp van Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) is een cloudgebaseerde, scale-out database die in staat is om enorme hoeveelheden gegevens te verwerken, zowel relationeel als niet-relationeel. SQL Data Warehouse is gebouwd op de enorm parallelle processing (MPP)-architectuur die is geoptimaliseerd voor enterprise data warehouse workloads. Het biedt cloudelasticiteit met de flexibiliteit om opslag en rekenkracht onafhankelijk te schalen.

Aan de slag met Azure SQL Data Warehouse is nu eenvoudiger dan ooit wanneer u Azure Data Factory gebruikt. Azure Data Factory is een volledig beheerde cloudgebaseerde data-integratieservice. U de service gebruiken om een SQL Data Warehouse te vullen met gegevens uit uw bestaande systeem en tijd te besparen bij het bouwen van uw analyseoplossingen.

Azure Data Factory biedt de volgende voordelen voor het laden van gegevens in Azure SQL Data Warehouse:

* **Eenvoudig in te stellen:** Een intuïtieve 5-staps wizard zonder scripting vereist.
* **Rich data store support**: Ingebouwde ondersteuning voor een rijke set on-premises en cloud-gebaseerde datastores. Zie de tabel [met ondersteunde gegevensarchieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst .
* **Veilig en compliant**: Gegevens worden overgedragen via HTTPS of ExpressRoute. De wereldwijde aanwezigheid van de service zorgt ervoor dat uw gegevens nooit de geografische grens verlaten.
* **Ongeëvenaarde prestaties door PolyBase te gebruiken:** Polybase is de meest efficiënte manier om gegevens naar Azure SQL Data Warehouse te verplaatsen. Gebruik de functie blob met fasering om hoge belastingssnelheden te bereiken van alle soorten gegevensopslag, waaronder Azure Blob-opslag en Data Lake Store. (Polybase ondersteunt standaard Azure Blob-opslag en Azure Data Lake Store.) Zie [Activiteitsprestaties kopiëren](copy-activity-performance.md)voor meer informatie .

In dit artikel ziet u hoe u het hulpprogramma Gegevensfabriekskopiegegevens gebruiken om _gegevens uit Azure SQL-database te laden in Azure SQL Data Warehouse._ U vergelijkbare stappen uitvoeren om gegevens uit andere soorten gegevensarchieven te kopiëren.

> [!NOTE]
> Zie [Gegevens kopiëren van of naar Azure SQL Data Warehouse met Azure Data Factory](connector-azure-sql-data-warehouse.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Azure SQL Data Warehouse: Het gegevensmagazijn bevat de gegevens die zijn gekopieerd uit de SQL-database. Als u geen Azure SQL Data Warehouse hebt, raadpleegt u de instructies in [Een SQL Data Warehouse maken.](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md)
* Azure SQL Database: Deze zelfstudie kopieert gegevens uit een Azure SQL-database met Adventure Works LT-voorbeeldgegevens. U een SQL-database maken door de instructies te volgen in [Een Azure SQL-database maken.](../sql-database/sql-database-get-started-portal.md)
* Azure-opslagaccount: Azure Storage wordt gebruikt als de _tijdelijke_ blob in de bewerking voor bulkkopieën. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu **Een brongegevens** > **maken + Analytics** > **Data Factory:**

2. Geef op de pagina **Nieuwe gegevensfabriek** waarden op voor de volgende items:

    * **Naam:** Voer *LoadSQLDWDemo* in voor naam. De naam voor uw gegevensfabriek moet *wereldwijd uniek zijn. Als u de fout 'Gegevensfabrieksnaam 'LoadSQLDWDemo' niet beschikbaar vindt, voert u een andere naam in voor de gegevensfabriek. U bijvoorbeeld de naam _**yourname**_**ADFTutorialDataFactory**gebruiken. Probeer de gegevensfabriek opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer uw Azure-abonnement waarin u de gegevensfabriek wilt maken. 
    * **Resourcegroep:** selecteer een bestaande resourcegroep in de vervolgkeuzelijst of selecteer de optie **Nieuwe maken** en voer de naam van een resourcegroep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.  
    * **Versie**: Selecteer **V2**.
    * **Locatie**: Selecteer de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensopslag die door gegevensfabriek worden gebruikt, kunnen zich in andere locaties en regio's bevinden. Deze gegevensopslag omvat Azure Data Lake Store, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat de creatie is voltooid, gaat u naar uw gegevensfabriek. U ziet de startpagina **van Data Factory** zoals weergegeven in de volgende afbeelding:

   ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)

   Klik op de tegel **Maken en bewaken** om de toepassing Gegevensintegratie in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Gegevens laden in Azure SQL Data Warehouse

1. Selecteer op de pagina **Aan de slag** de tegel **Gegevens kopiëren** om het hulpprogramma Gegevens kopiëren te openen.

1. Geef op de pagina **Eigenschappen** **CopyFromSQLToSQLDW** op voor het veld **Taaknaam** en selecteer **Volgende**.

    ![De pagina Eigenschappen](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Voer op de pagina **Brongegevensarchief** de volgende stappen uit:
    >[!TIP]
    >In deze zelfstudie gebruikt u *SQL-verificatie* als verificatietype voor uw brongegevensarchief, maar u indien nodig andere ondersteunde verificatiemethoden kiezen:*Service Principal* en *Managed Identity.* Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-database#linked-service-properties) voor meer informatie.
    >Als u geheimen voor gegevensopslag veilig wilt opslaan, wordt het ook aanbevolen om een Azure Key Vault te gebruiken. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

    a. klik **+ Nieuwe verbinding maken**.

    b. Selecteer **Azure SQL Database** in de galerie en selecteer **Doorgaan**. U 'SQL' typen in het zoekvak om de connectors te filteren.

    ![Azure SQL DB selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Selecteer op de pagina **Nieuwe Gekoppelde Service** uw servernaam en DB-naam in de vervolgkeuzelijst en geef de gebruikersnaam en het wachtwoord op. Klik **op Verbinding testen** om de instellingen te valideren en selecteer Vervolgens **Maken**.

    ![Azure SQL DB configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

1. Voer in de **tabellen Selecteren waaruit u de gegevens wilt kopiëren of een aangepaste querypagina wilt gebruiken,** **VerkoopLT** in om de tabellen te filteren. Kies het vak **(Alles selecteren)** om alle tabellen voor de kopie te gebruiken en selecteer **Volgende**.

    ![Brontabellen selecteren](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Geef **op** de pagina Filter toepassen uw instellingen op of selecteer **Volgende**.

1. Voer op de pagina **Bestemmingsgegevensarchief** de volgende stappen uit:
    >[!TIP]
    >In deze zelfstudie gebruikt u *SQL-verificatie* als verificatietype voor uw doelgegevensarchief, maar u indien nodig andere ondersteunde verificatiemethoden kiezen:*Service Principal* en *Managed Identity.* Raadpleeg de bijbehorende secties in [dit artikel](https://docs.microsoft.com/azure/data-factory/connector-azure-sql-data-warehouse#linked-service-properties) voor meer informatie.
    >Als u geheimen voor gegevensopslag veilig wilt opslaan, wordt het ook aanbevolen om een Azure Key Vault te gebruiken. Raadpleeg [dit artikel](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) voor gedetailleerde illustraties.

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    b. Selecteer **Azure Synapse Analytics (voorheen SQL DW)** in de galerie en selecteer **Doorgaan**. U 'SQL' typen in het zoekvak om de connectors te filteren.

    ![Azure SQL DW selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Selecteer op de pagina **Nieuwe Gekoppelde Service** uw servernaam en DB-naam in de vervolgkeuzelijst en geef de gebruikersnaam en het wachtwoord op. Klik **op Verbinding testen** om de instellingen te valideren en selecteer Vervolgens **Maken**.

    ![Azure SQL DW configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

1. Bekijk op de pagina **Tabeltoewijzing** de inhoud en selecteer **Volgende**. Een intelligente tabeltoewijzing wordt weergegeven. De brontabellen worden toegewezen aan de doeltabellen op basis van de tabelnamen. Als er geen brontabel in de doellijst bestaat, maakt Azure Data Factory standaard een doeltabel met dezelfde naam. U ook een brontabel toewijzen aan een bestaande doeltabel.

   > [!NOTE]
   > Automatische tabelcreatie voor de SQL Data Warehouse-sink is van toepassing wanneer SQL Server of Azure SQL Database de bron is. Als u gegevens uit een ander brongegevensarchief kopieert, moet u het schema vooraf maken in het Azure SQL Data Warehouse voordat u de gegevenskopie uitvoert.

   ![De pagina Tabeltoewijzing](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Bekijk op de pagina **Kolomtoewijzing** de inhoud en selecteer **Volgende**. De intelligente tabeltoewijzing is gebaseerd op de kolomnaam. Als u Data Factory automatisch de tabellen laat maken, kan conversie van het gegevenstype plaatsvinden wanneer er onverenigbaarheden zijn tussen de bron- en doelopslag. Als er een niet-ondersteunde conversie van gegevenstypen is tussen de bron en de doelkolom, ziet u een foutbericht naast de bijbehorende tabel.

    ![Kolomtoewijzingspagina](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Voer **op** de pagina Instellingen de volgende stappen uit:

    a. Klik in de sectie **Faseringsinstellingen** op **+ Nieuw** voor nieuwe opslag voor tijdelijke bestanden. De opslag wordt gebruikt voor het met elkaar zetten van de gegevens voordat deze worden geladen in SQL Data Warehouse met behulp van PolyBase. Nadat de kopie is voltooid, worden de tussentijdse gegevens in Azure Blob Storage automatisch opgeschoond.

    b. Selecteer op de pagina **Nieuwe gekoppelde service** uw opslagaccount en selecteer **Maken** om de gekoppelde service te implementeren.

    c. Schakel in de sectie **Geavanceerde instellingen** de optie **Standaardtype gebruiken** uit en selecteer **Volgende**.

    ![PolyBase configureren](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende**.

    ![Overzichtspagina](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Selecteer **op**de pagina Implementatie de optie **Monitor** om de pijplijn (taak) te controleren.

1. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. Wanneer de pijplijnrun is voltooid, selecteert u de **koppeling CopyFromSQLToSQLDW** onder de kolom **PIPELINE NAME** om details van activiteitsuitvoering weer te geven en de pijplijn opnieuw uit te voeren.

    [![Pijplijnuitvoeringen controleren](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png#lightbox)
1. Als u wilt teruggaan naar de weergave pijplijnuitvoeringen, selecteert u bovenaan de koppeling **Alle pijplijnuitvoeringen.** Selecteer **Vernieuwen** om de lijst te vernieuwen.

    ![Uitvoering van activiteiten controleren](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Als u de uitvoeringsgegevens voor elke kopieeractiviteit wilt controleren, selecteert u de koppeling **Details** (brilpictogram) onder **ACTIVITEITSNAAM** in de weergave activiteitsuitvoeringen. U details controleren, zoals het volume van gegevens die van de bron naar de gootsteen zijn gekopieerd, gegevensdoorvoer, uitvoeringsstappen met bijbehorende duur en gebruikte configuraties.
    ![Details van het uitvoeren van activiteiten controleren](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-1.png)

    ![Details van het uitvoeren van activiteiten controleren](./media/load-azure-sql-data-warehouse/monitor-activity-run-details-2.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over Azure SQL Data Warehouse-ondersteuning:

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-connector](connector-azure-sql-data-warehouse.md)
