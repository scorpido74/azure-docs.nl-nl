---
title: Incrementeel kopiëren van nieuwe bestanden op basis van een gepartitioneerde bestands naam
description: Maak een Azure-data factory en gebruik vervolgens het hulp programma Gegevens kopiëren om nieuwe bestanden op basis van een gepartitioneerde bestands naam stapsgewijs te laden.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217455"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopieer incrementeel nieuwe bestanden op basis van een gepartitioneerde bestands naam met behulp van het Gegevens kopiëren-hulp programma

In deze zelfstudie gebruikt u Azure Portal om een gegevensfactory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken waarmee nieuwe bestanden incrementeel worden gekopieerd op basis van een gepartitioneerde bestands naam van Azure Blob-opslag naar Azure Blob-opslag. 

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende stappen uit:

> [!div class="checklist"]
> * Een data factory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren.

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslag account**: Blob Storage gebruiken als _bron_ -en _sink_ -gegevens archief. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob Storage

Bereid de Blob-opslag voor op de zelf studie door de volgende stappen uit te voeren.

1. Maak een container met de naam **Source**.  Maak een mappad als **2019/02/26/14** in uw container. Maak een leeg tekst bestand en noem het als **bestand1. txt**. Upload het bestand Bestand1. txt naar het mappad **Source/2019/02/26/14** in uw opslag account.  U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).
    
    ![bestanden uploaden](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Wijzig de mapnaam met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld 2:03 PM is op februari 26, 2019, kunt u het mappad maken als **bron/2019/02/26/14/** door de regel van de **bron/{year}/{month}/{Day}/{Hour}/** .

2. Maak een container met de naam **Destination**. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer in het menu links de optie **een resource maken** > **gegevens en analyses** > **Data Factory**: 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**. 
    
    De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:
   
   ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)
   
   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_ **ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het Azure-**abonnement** waarin u de nieuwe data factory wilt maken. 
4. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
    a. Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    b. Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/resource-group-overview.md) voor meer informatie.

5. Selecteer bij **Versie** de optie **V2** als de versie.
6. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en -berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich in andere locaties of regio's bevinden.
7. Selecteer **Vastmaken aan dashboard**. 
8. Selecteer **Maken**.
9. Op het dashboard wordt op de tegel **Data Factory implementeren** de processtatus weergegeven.

    ![Tegel Data Factory implementeren](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.
   
    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
11. Selecteer de tegel **Author & Monitor** om de gebruikersinterface (UI) van Azure Data Factory te openen in een afzonderlijk tabblad. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** titel om het gegevens kopiëren-hulp programma te starten. 

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)
   
2. Voer de volgende stappen uit op de pagina **Eigenschappen** :

    a. Voer onder **taak naam** **DeltaCopyFromBlobPipeline**in.

    b. Selecteer onder **taak uitgebracht of taak planning** **regel matig uitvoeren volgens schema**.

    c. Onder **trigger type**selecteert u **tumblingvenstertriggers-venster**.
    
    d. Voer **1 uur**in onder **terugkeer patroon**. 
    
    e. Selecteer **Volgende**. 
    
    De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam. 

    ![De pagina Eigenschappen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Klik op **+ nieuwe verbinding maken**om een verbinding toe te voegen.

    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Selecteer **Azure-Blob Storage** in de galerie en klik vervolgens op **door gaan**.

    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Selecteer op de pagina **nieuwe gekoppelde service** uw opslag account in de lijst **naam van het opslag account** en klik vervolgens op **volt ooien**.
    
    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecteer de zojuist gemaakte gekoppelde service en klik vervolgens op **volgende**. 
    
   ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Voer op de pagina **Het invoerbestand of de invoermap kiezen** de volgende stappen uit:
    
    a. Blader en selecteer de **bron** container en selecteer vervolgens **kiezen**.
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Selecteer bij **gedrag bij het laden van bestanden**de optie **Incrementeel laden: gepartitioneerde map/bestands namen**.
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Schrijf het pad naar de dynamische map als **bron/{Year}/{maand}/{Day}/{Hour}/** en wijzig de notatie als volgt:
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Controleer de **binaire kopie** en klik op **volgende**.
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Selecteer op de pagina **doel gegevens archief** de **AzureBlobStorage**, die hetzelfde opslag account als gegevens bron archief is, en klik vervolgens op **volgende**.

    ![De pagina Doelgegevensarchief](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Voer op de pagina **het uitvoer bestand of de map kiezen** de volgende stappen uit:
    
    a. Blader en selecteer de **doelmap** en klik vervolgens op **kiezen**.
    
    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Schrijf het pad naar de dynamische map als **bron/{Year}/{maand}/{Day}/{Hour}/** en wijzig de notatie als volgt:
    
    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klik op **Volgende**.
    
    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Selecteer op de pagina **Instellingen** de optie **Volgende**. 

    ![De pagina Instellingen](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Bekijk op de **Overzichtspagina** de waarden voor alle instellingen en selecteer vervolgens **Volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.
    ![implementatie pagina](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd.  U moet wachten op de uitvoering van de pijp lijn wanneer deze automatisch wordt geactiveerd (ongeveer na één uur).  Wanneer de kolom **acties** wordt uitgevoerd, bevat koppelingen om de details van de activiteit weer te geven en de pijp lijn opnieuw uit te voeren. Selecteer **vernieuwen** om de lijst te vernieuwen en selecteer de koppeling **uitvoeringen van activiteit weer geven** in de kolom **acties** . 

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Omdat er slechts één activiteit (kopieeractiviteit) in de pijplijn is, ziet u slechts één vermelding in de lijst. U kunt zien dat het bron bestand (bestand1. txt) is gekopieerd van **bron-2019/02/26/14/** naar **doel/2019/02/26/14** /met dezelfde bestands naam.  

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    U kunt hetzelfde ook controleren met behulp van Azure Storage Explorer (https://storageexplorer.com/) om de bestanden te scannen.
    
    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Maak nog een leeg tekst bestand met de nieuwe naam als **bestand2. txt**. Upload het bestand bestand2. txt naar het mappad **Source/2019/02/26/15** in uw opslag account.   U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).   
    
    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Mogelijk weet u dat er een nieuw mappad moet worden gemaakt. Wijzig de mapnaam met uw UTC-tijd.  Als de huidige UTC-tijd bijvoorbeeld 3:20 uur is op februari 26, 2019, kunt u het mappad maken als **bron-2019/02/26/15/** door de regel **{year}/{month}/{Day}/{Hour}/** .
    
13. Als u wilt terugkeren naar de weer gave **pijplijn uitvoeringen** , selecteert u **alle pijp lijnen worden uitgevoerd**en wacht u tot dezelfde pijp lijn opnieuw wordt geactiveerd na een uur.  

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Selecteer **uitvoering van activiteit weer geven** voor de tweede pijplijn uitvoering wanneer deze wordt geleverd en doe hetzelfde om de details te bekijken.  

    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    U kunt zien dat het bron bestand (bestand2. txt) is gekopieerd van **bron-2019/02/26/15/** naar **doel/2019/02/26/15** /met dezelfde bestands naam.
    
    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    U kunt hetzelfde ook controleren met behulp van Azure Storage Explorer (https://storageexplorer.com/) de bestanden in de **doel** container scannen
    
    ![Pijplijnuitvoeringen controleren](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Spark-cluster in Azure:

> [!div class="nextstepaction"]
>[Gegevens transformeren met behulp van een Spark-cluster in de cloud](tutorial-transform-data-spark-portal.md)