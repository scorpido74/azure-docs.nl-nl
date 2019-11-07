---
title: Gegevens in Azure SQL Data Warehouse laden met behulp van Azure Data Factory
description: Azure Data Factory gebruiken om gegevens te kopiëren naar Azure SQL Data Warehouse
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: jingwang
ms.openlocfilehash: 538751b1e93dfec66c35ea3768bde603c198df32
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73672751"
---
# <a name="load-data-into-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Gegevens in Azure SQL Data Warehouse laden met behulp van Azure Data Factory

[Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) is een scale-out-data base op basis van de cloud die kan verwerken van enorme hoeveel heden gegevens, zowel relationele als niet-relationeel. SQL Data Warehouse is gebaseerd op de MPP-architectuur (enorm parallelle verwerking) die is geoptimaliseerd voor werk belastingen van het Enter prise-Data Warehouse. De service biedt de flexibiliteit van de cloud om opslag te schalen en onafhankelijk te berekenen.

Het is nu eenvoudiger dan ooit om aan de slag te gaan met Azure SQL Data Warehouse met behulp van Azure Data Factory. Azure Data Factory is een volledig beheerde service voor gegevens integratie in de Cloud. U kunt de service gebruiken om een SQL Data Warehouse te vullen met gegevens van uw bestaande systeem en tijd te besparen bij het bouwen van uw analyse oplossingen.

Azure Data Factory biedt de volgende voor delen voor het laden van gegevens in Azure SQL Data Warehouse:

* **Eenvoudig in te stellen**: een intuïtieve 5-stap wizard zonder scripting vereist.
* **Ondersteuning voor uitgebreide gegevens opslag**: ingebouwde ondersteuning voor een uitgebreide set on-premises en cloud-gebaseerde gegevens archieven. Zie de tabel met [ondersteunde gegevens archieven](copy-activity-overview.md#supported-data-stores-and-formats)voor een gedetailleerde lijst.
* **Beveiligd en compatibel**: gegevens worden overgebracht via https of ExpressRoute. De wereld wijde service aanwezigheid zorgt ervoor dat uw gegevens de geografische grens nooit verlaten.
* **Ongeëvenaarde prestaties met poly base**: poly Base is de meest efficiënte manier om gegevens naar Azure SQL Data Warehouse te verplaatsen. Gebruik de functie voor het maken van een staging-blob om hoge belasting snelheden te realiseren van alle typen gegevens archieven, waaronder Azure Blob-opslag en Data Lake Store. (Poly Base biedt standaard ondersteuning voor Azure Blob Storage en Azure Data Lake Store.) Zie [prestaties van Kopieer activiteit](copy-activity-performance.md)voor meer informatie.

In dit artikel wordt beschreven hoe u het Data Factory Gegevens kopiëren-hulp programma gebruikt om _gegevens van Azure SQL database in Azure SQL Data Warehouse te laden_. U kunt vergelijk bare stappen volgen om gegevens te kopiëren van andere typen gegevens archieven.

> [!NOTE]
> Zie [gegevens kopiëren naar of van Azure SQL data warehouse met behulp van Azure Data Factory](connector-azure-sql-data-warehouse.md)voor meer informatie.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Azure SQL Data Warehouse: het Data Warehouse bevat de gegevens die worden gekopieerd van de SQL database. Als u geen Azure SQL Data Warehouse hebt, raadpleegt u de instructies in [Create a SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-get-started-tutorial.md).
* Azure SQL Database: in deze zelf studie worden gegevens uit een Azure-SQL database gekopieerd met LT Works-voorbeeld gegevens. U kunt een SQL database maken door de instructies in [een Azure-SQL database maken](../sql-database/sql-database-get-started-portal.md)te volgen. 
* Azure-opslag account: Azure Storage wordt gebruikt als de _faserings_ -Blob in de bulksgewijze Kopieer bewerking. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md).

## <a name="create-a-data-factory"></a>Een data factory maken

1. Selecteer in het menu links de optie **een resource maken** > **gegevens en analyses** > **Data Factory**: 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Geef op de pagina **nieuw Data Factory** waarden op voor de velden die worden weer gegeven in de volgende afbeelding:
      
   ![Pagina nieuwe data factory](./media/load-azure-sql-data-warehouse/new-azure-data-factory.png)
 
    * **Naam**: Voer een wereld wijd unieke naam in voor uw Azure-Data Factory. Als het fout bericht ' Data Factory-naam \"LoadSQLDWDemo\" niet beschikbaar is, geeft u een andere naam op voor de data factory. U kunt bijvoorbeeld _**de naam**_ **ADFTutorialDataFactory**. Probeer de data factory opnieuw te maken. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
    * **Abonnement**: Selecteer het Azure-abonnement waarin u de Data Factory wilt maken. 
    * **Resource groep**: Selecteer een bestaande resource groep in de vervolg keuzelijst of selecteer de optie **nieuwe maken** en voer de naam van een resource groep in. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.  
    * **Versie**: Selecteer **v2**.
    * **Locatie**: Selecteer de locatie voor de Data Factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven die door data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden. Deze gegevens archieven bevatten Azure Data Lake Store, Azure Storage, Azure SQL Database, enzovoort.

3. Selecteer **Maken**.
4. Nadat het maken is voltooid, gaat u naar uw data factory. U ziet de **Data Factory** start pagina zoals wordt weer gegeven in de volgende afbeelding:
   
   ![Startpagina van de gegevensfactory](./media/load-azure-sql-data-warehouse/data-factory-home-page.png)

   Selecteer de tegel **auteur & monitor** om de gegevens integratie toepassing in een afzonderlijk tabblad te openen.

## <a name="load-data-into-azure-sql-data-warehouse"></a>Gegevens laden in Azure SQL Data Warehouse

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** tegel om het gegevens kopiëren-hulp programma te starten:

   ![De tegel Copy Data-hulpprogramma](./media/load-azure-sql-data-warehouse/copy-data-tool-tile.png)
1. Geef op de pagina **Eigenschappen** **CopyFromSQLToSQLDW** op voor het veld **taak naam** en selecteer **volgende**:

    ![De pagina Eigenschappen](./media/load-azure-sql-data-warehouse/copy-data-tool-properties-page.png)

1. Voer de volgende stappen uit op de pagina **brongegevens archief** :

    a. Klik op **+ nieuwe verbinding maken**:

    ![De pagina Brongegevensarchief](./media/load-azure-sql-data-warehouse/new-source-linked-service.png)

    b. Selecteer **Azure SQL database** in de galerie en selecteer **door gaan**. U kunt ' SQL ' in het zoekvak typen om de connectors te filteren.

    ![Azure SQL DB selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-db-source.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service** de naam van de server en de data base in de vervolg keuzelijst en geef de gebruikers naam en het wacht woord op. Klik op **verbinding testen** om de instellingen te valideren en selecteer vervolgens **volt ooien**.
   
    ![Azure SQL DB configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-db.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als bron. Klik vervolgens op **Volgende**.

    ![Aan de bron gekoppelde service selecteren](./media/load-azure-sql-data-warehouse/select-source-linked-service.png)

1. Voer in de **tabellen selecteren waaruit de gegevens moeten worden gekopieerd of een aangepaste query** pagina, **tabel saleslt** in om de tabellen te filteren. Kies het vak **(Alles selecteren)** om alle tabellen voor de kopie te gebruiken en selecteer vervolgens **volgende**: 

    ![Bron tabellen selecteren](./media/load-azure-sql-data-warehouse/select-source-tables.png)

1. Voer op de pagina **doel gegevens archief** de volgende stappen uit:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen

    ![Pagina Sink-gegevens archief](./media/load-azure-sql-data-warehouse/new-sink-linked-service.png)

    b. Selecteer **Azure SQL Data Warehouse** in de galerie en selecteer **volgende**.

    ![Azure SQL DW selecteren](./media/load-azure-sql-data-warehouse/select-azure-sql-dw-sink.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service** de naam van de server en de data base in de vervolg keuzelijst en geef de gebruikers naam en het wacht woord op. Klik op **verbinding testen** om de instellingen te valideren en selecteer vervolgens **volt ooien**.
   
    ![Azure SQL DW configureren](./media/load-azure-sql-data-warehouse/configure-azure-sql-dw.png)

    d. Selecteer de zojuist gemaakte gekoppelde service als sink. Klik vervolgens op **Volgende**.

    ![Aan sink gekoppelde service selecteren](./media/load-azure-sql-data-warehouse/select-sink-linked-service.png)

1. Bekijk de inhoud op de pagina **tabel toewijzing** en selecteer **volgende**. Een intelligente tabel toewijzing wordt weer gegeven. De bron tabellen worden toegewezen aan de doel tabellen op basis van de tabel namen. Als een bron tabel niet bestaat op de doel locatie, maakt Azure Data Factory standaard een doel tabel met dezelfde naam. U kunt ook een bron tabel toewijzen aan een bestaande doel tabel. 

   > [!NOTE]
   > Het automatisch maken van tabellen voor de SQL Data Warehouse Sink geldt wanneer SQL Server of Azure SQL Database de bron is. Als u gegevens uit een ander brongegevens archief kopieert, moet u het schema vooraf maken in de Sink-Azure SQL Data Warehouse voordat u de gegevens kopie uitvoert.

   ![De pagina Tabeltoewijzing](./media/load-azure-sql-data-warehouse/table-mapping.png)

1. Controleer de inhoud op de pagina **schema toewijzing** en selecteer **volgende**. De intelligente tabel toewijzing is gebaseerd op de naam van de kolom. Als u Data Factory automatisch tabellen maakt, kan het gegevens type worden geconverteerd als er incompatibiliteits problemen zijn tussen de bron-en doel opslag. Als er een niet-ondersteund gegevens type conversie is tussen de kolom Bron en doel, wordt er een fout bericht weer gegeven naast de overeenkomende tabel.

    ![De pagina Schematoewijzing](./media/load-azure-sql-data-warehouse/schema-mapping.png)

1. Voer op de pagina **instellingen** de volgende stappen uit:

    a. Klik in de sectie **faserings instellingen** op **+ Nieuw** voor een nieuwe staging-opslag. De opslag wordt gebruikt voor het faseren van de gegevens voordat deze in SQL Data Warehouse wordt geladen met poly base. Nadat de kopie is voltooid, worden de tussenliggende gegevens in Azure Storage automatisch opgeschoond. 

    ![Fase ring configureren](./media/load-azure-sql-data-warehouse/configure-staging.png)

    b. Selecteer uw opslag account op de pagina **nieuwe gekoppelde service** en selecteer **volt ooien**.
   
    ![Azure Storage configureren](./media/load-azure-sql-data-warehouse/configure-blob-storage.png)

    c. Schakel in de sectie **Geavanceerde instellingen** de optie **type standaard gebruiken** uit en selecteer **volgende**.

    ![Poly base configureren](./media/load-azure-sql-data-warehouse/configure-polybase.png)

1. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**:

    ![Overzichtspagina](./media/load-azure-sql-data-warehouse/summary-page.png)
1. Selecteer op de **pagina implementatie**de optie **controleren** om de pijp lijn te bewaken (taak):

    ![De pagina Implementatie](./media/load-azure-sql-data-warehouse/deployment-page.png)
1. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **acties** bevat koppelingen om de details van de activiteit weer te geven en de pijp lijn opnieuw uit te voeren: 

    ![Pijplijnuitvoeringen controleren](./media/load-azure-sql-data-warehouse/pipeline-monitoring.png)
1. Selecteer de koppeling **uitvoeringen van activiteit weer** geven in de kolom **acties** om de uitvoering van activiteiten te bekijken die zijn gekoppeld aan de pijplijn uitvoering. Als u wilt terugkeren naar de weer gave pijplijn uitvoeringen, selecteert u de koppeling **pijp lijnen** bovenaan. Selecteer **Vernieuwen** om de lijst te vernieuwen. 

    ![Uitvoering van activiteiten controleren](./media/load-azure-sql-data-warehouse/activity-monitoring.png)

1. Als u de uitvoerings Details voor elke Kopieer activiteit wilt bewaken, selecteert u de koppeling **Details** onder **acties** in de weer gave activiteiten bewaking. U kunt details, zoals het volume van de gegevens die uit de bron zijn gekopieerd, bewaken naar de sink, gegevens doorvoer, uitvoerings stappen met de overeenkomstige duur en de gebruikte configuraties:

    ![Details van uitvoering van activiteit bewaken](./media/load-azure-sql-data-warehouse/monitor-activity-run-details.png)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over de ondersteuning van Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Azure SQL Data Warehouse-connector](connector-azure-sql-data-warehouse.md)
