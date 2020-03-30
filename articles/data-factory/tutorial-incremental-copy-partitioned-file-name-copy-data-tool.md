---
title: Stapsgewijs nieuwe bestanden kopiëren op basis van tijdpartitiebestandsnaam
description: Maak een Azure-gegevensfabriek en gebruik vervolgens het gereedschap Gegevens kopiëren om nieuwe bestanden alleen op basis van tijdpartitiebestandsnaam te laden.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501655"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Stapsgewijs nieuwe bestanden kopiëren op basis van tijdpartitiebestandsnaam met het gereedschap Gegevens kopiëren

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het gereedschap Gegevens kopiëren om een pijplijn te maken die stapsgewijs nieuwe bestanden kopieert op basis van tijdpartitiebestandsnaam van Azure Blob-opslag naar Azure Blob-opslag.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount:** gebruik Blob-opslag als _bron-_ en _sinkgegevensarchief._ Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob-opslag

Bereid uw Blob-opslag voor op de zelfstudie door deze stappen uit te voeren.

1. Een container met de naam **bron maken**.  Maak een mappad als **2020/03/17/03** in uw container. Maak een leeg tekstbestand en noem het als **file1.txt**. Upload het bestand1.txt naar de **mappadbron/2020/03/17/03** in uw opslagaccount.  U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    ![bestanden uploaden](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Pas de mapnaam aan met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld op 17 maart 2020 3:38 AM is, u het mappad maken als **bron/2020/03/17/03/** volgens de regel van **bron/{Year}/{Month}/{Day}/{Hour}/**.

2. Een container met de naam **bestemming maken**. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu **Een brongegevens** > **maken + Analytics** > **Data Factory:**

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

   ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken.
4. Voer een van de volgende stappen uit voor **Resourcegroep**:

    a. Selecteer **Bestaande gebruiken**en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuw maken**en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer bij **Versie** de optie **V2** als de versie.
6. Selecteer **onder locatie**de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
7. Selecteer **Maken**.
8. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.
9. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad.

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Laten we aan de slag** de titel Gegevens **kopiëren** om het gereedschap Gegevens kopiëren te starten.

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)

2. Ga op de pagina **Eigenschappen** de volgende stappen uit:

    a. Voer **onder Taaknaam** **DeltaCopyFromBlobPipeline in**.

    b. Selecteer Onder **Taakcadans of Taakplanning**de optie **Regelmatig uitvoeren op schema**.

    c. Selecteer **Tumbling Window**onder **Triggertype**.

    d. Voer **onder Herhaling**1 **uur(en)** in .

    e. Selecteer **Volgende**.

    De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.

    ![De pagina Eigenschappen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ Nieuwe verbinding maken** om een verbinding toe te voegen
    
    b. Selecteer Azure Blob Storage in de galerie en selecteer Doorgaan.
    
    c. Voer op de pagina **New Linked Service (Azure Blob Storage)** een naam in voor de gekoppelde service. Selecteer uw Azure-abonnement en selecteer uw opslagaccount in de lijst **Met naam van het opslagaccount.** Test verbinding en selecteer **Vervolgens Maken**.

    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Selecteer de nieuw gemaakte gekoppelde service op de pagina **Brongegevensarchief** en klik op **Volgende**.

4. Voer op de pagina **Het invoerbestand of de invoermap kiezen** de volgende stappen uit:

    a. Blader en selecteer de **broncontainer** en selecteer **Vervolgens Kiezen**.

    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Selecteer **onder gedrag voor het laden van**bestanden de optie **Incrementele belasting: map/bestandsnamen met tijdsverdeling**.

    c. Schrijf het dynamische mappad als **bron/{year}/{month}/{day}/{hour}/** en wijzig de indeling zoals weergegeven in de volgende schermafbeelding. Controleer **Binaire kopie** en klik op **Volgende**.

    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Selecteer op de pagina **Doelgegevensarchief** de **AzureBlobStorage**, hetzelfde opslagaccount als het gegevensbronarchief, en klik op **Volgende**.

    ![De pagina Doelgegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Ga op de pagina **Het uitvoerbestand of de map kiezen** de volgende stappen uit:

    a. Blader en selecteer de **doelmap** en klik op **Kiezen**.

    b. Schrijf het dynamische mappad op als **bestemming/{year}/{month}/{day}/{hour}/** en wijzig de notatie als volgt:

    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klik op **Volgende**.

    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Selecteer op de pagina **Instellingen** de optie **Volgende**.

8. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
    ![De pagina Implementatie](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd.  U moet wachten op de pijplijn draaien wanneer deze automatisch wordt geactiveerd (ongeveer na een uur). Wanneer deze wordt uitgevoerd, klikt u op de koppeling deltacopy van de **pijplijnnaam DeltaCopyFromBlobPipeline** om de activiteitsgegevens weer te geven of de pijplijn opnieuw uit te voeren. Selecteer **Vernieuwen** om de lijst te vernieuwen.

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. Pas de kolombreedte van de **bron-** en **doelkolommen** aan (indien nodig) om meer details weer te geven, u zien dat het bronbestand (bestand1.txt) is gekopieerd van *bron/2020/03/17/03/* naar *bestemming/2020/03/17/03/* met dezelfde bestandsnaam. 

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    U hetzelfde ook verifiëren doorhttps://storageexplorer.com/) Azure Storage Explorer te gebruiken ( om de bestanden te scannen.

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Maak nog een leeg tekstbestand met de nieuwe naam als **file2.txt**. Upload het bestand bestandbestand2.txt naar de **mappadbron/2020/03/17/04** in uw opslagaccount. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > U weet mogelijk dat er een nieuw mappad moet worden gemaakt. Pas de mapnaam aan met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld 4:20 uur is op 17 maart 2020, u het mappad maken als **bron/2020/03/17/04/** volgens de regel van **{Year}/{Month}/{Day}/{Hour}/**.

13. Als u terug wilt gaan naar de weergave **Pijplijnuitvoeringen,** selecteert u **Alle pijplijnen uitvoert**en wacht u tot dezelfde pijplijn na nog een uur weer automatisch wordt geactiveerd.  

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecteer de nieuwe **DeltaCopyFromBlobPipeline-koppeling** voor de tweede pijplijnuitvoering wanneer deze aankomt en doe hetzelfde om details te bekijken. U ziet dat het bronbestand (file2.txt) is gekopieerd van **bron/2020/03/17/04/** naar **destination/2020/03/17/04/** met dezelfde bestandsnaam. U hetzelfde ook verifiëren doorhttps://storageexplorer.com/) Azure Storage Explorer te gebruiken ( om de bestanden in **de doelcontainer** te scannen.


## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Gegevens transformeren met behulp van een Spark-cluster in de cloud](tutorial-transform-data-spark-portal.md)
