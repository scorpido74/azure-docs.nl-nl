---
title: Gegevenshulpprogramma om nieuwe en bijgewerkte bestanden stapsgewijs te kopiëren
description: Maak een Azure-gegevensfabriek en gebruik vervolgens het gereedschap Gegevens kopiëren om stapsgewijs nieuwe bestanden te laden op basis van LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131082"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopieer stapsgewijs nieuwe en gewijzigde bestanden op basis van LastModifiedDate met het gereedschap Gegevens kopiëren

In deze zelfstudie gebruikt u de Azure-portal om een gegevensfabriek te maken. Vervolgens gebruikt u het hulpprogramma Gegevens kopiëren om een pijplijn te maken die alleen nieuwe en gewijzigde bestanden stapsgewijs kopieert, op basis van hun **LastModifiedDate** van Azure Blob-opslag naar Azure Blob-opslag.

Hiermee scant ADF alle bestanden uit het bronarchief, past het bestandsfilter toe op hun LastModifiedDate en kopieert het nieuwe en bijgewerkte bestand pas sinds de laatste keer naar het doelarchief.  Houd er rekening mee dat als u ADF enorme hoeveelheden bestanden laat scannen, maar slechts een paar bestanden naar de bestemming kopieert, u nog steeds zou verwachten dat de lange duur als gevolg van het scannen van bestanden ook tijdrovend is.   

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslagaccount:** gebruik Blob-opslag als _bron-_ en _sinkgegevensarchief._ Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob-opslag

Bereid uw Blob-opslag voor op de zelfstudie door deze stappen uit te voeren.

1. Een container met de naam **bron maken**. U verschillende hulpprogramma's gebruiken om deze taak uit te voeren, zoals [Azure Storage Explorer.](https://storageexplorer.com/)

2. Een container met de naam **bestemming maken**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer links in het menu **Een brongegevens** > **maken + Analytics** > **Data Factory:**

   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:

   ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het **Azure-abonnement** waarin u de nieuwe gegevensfabriek maakt.
4. Voer een van de volgende stappen uit voor **Resourcegroep**:

    * Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    * Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer **V2**onder **versie**.
6. Selecteer **onder locatie**de locatie voor de gegevensfabriek. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevensarchieven (bijvoorbeeld Azure Storage en SQL Database) en computes (bijvoorbeeld Azure HDInsight) die uw gegevensfabriek gebruikt, kunnen zich op andere locaties en regio's bevinden.
8. Selecteer **Maken**.
9. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.
10. Als u de gebruikersinterface van Azure Data Factory (UI) op een afzonderlijk tabblad wilt openen, selecteert u de tegel **Auteur & Monitor.**

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Laten we aan de slag** de titel Gegevens **kopiëren** om het gereedschap Gegevens kopiëren te openen.

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)

2. Ga op de pagina **Eigenschappen** de volgende stappen uit:

    a. Voer **onder Taaknaam** **DeltaCopyFromBlobPipeline in**.

    b. Selecteer Onder **Taakcadans** of **Taakplanning**de optie **Regelmatig uitvoeren op schema**.

    c. Selecteer Tumbling **Window**onder **Triggertype**.

    d. Voer **onder Herhaling** **15 minuten(en)** in .

    e. Selecteer **Volgende**.

    De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam.

    ![De pagina Eigenschappen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Selecteer **+ Nieuwe verbinding maken**om een verbinding toe te voegen.

    b. Selecteer **Azure Blob Storage** in de galerie en selecteer **Doorgaan**.

    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Selecteer op de pagina **New Linked Service (Azure Blob Storage)** uw opslagaccount in de lijst Met de naam **van het opslagaccount.** Test verbinding en selecteer **Vervolgens Maken**.

    d. Selecteer de nieuw gemaakte gekoppelde service en selecteer **Volgende**.

   ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Blader en selecteer de **bronmap** en selecteer **Vervolgens Kiezen**.

    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Selecteer **onder gedrag voor het laden van**bestanden de optie **Incrementele belasting: LastModifiedDate**.

    c. Controleer **Binaire kopie** en selecteer **Volgende**.

     ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Selecteer op de pagina **Bestemmingsgegevensarchief** de **AzureBlobStorage** die u hebt gemaakt. Dit is hetzelfde opslagaccount als het brongegevensarchief. Selecteer **vervolgens Volgende**.

6. Voltooi op de pagina **Uitvoerbestand of uitvoermap kiezen** de volgende stappen:

    a. Blader en selecteer de **doelmap** en selecteer **Vervolgens Kiezen**.

    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecteer **Volgende**.

7. Selecteer op de pagina **Instellingen** de optie **Volgende**.

8. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.

    ![De pagina Implementatie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De toepassing schakelt over naar het tabblad **Monitor.** U ziet de status van de pijplijn. Selecteer **Vernieuwen** om de lijst te vernieuwen. Klik op de koppeling onder **PIPELINE NAME** om activiteitsgegevens weer te geven of de pijplijn opnieuw uit te voeren. 

    ![Lijst vernieuwen en Activiteitsuitvoeringen weergeven selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Er is slechts één activiteit (de kopieeractiviteit) in de pijplijn, dus u ziet slechts één vermelding. Selecteer de koppeling **Details** (brilpictogram) onder de kolom **ACTIVITEITSNAAM** voor meer informatie over de kopieerbewerking. Zie [Overzicht van kopieeractiviteit](copy-activity-overview.md) voor meer informatie over de eigenschappen. 

    ![Kopieeractiviteit is in de pijplijn](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Omdat er geen bestand in de **broncontainer** in uw Blob-opslagaccount is, wordt er geen bestand gekopieerd naar de **doelcontainer** in uw Blob-opslagaccount.

    ![Geen bestand in broncontainer of doelcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Maak een leeg tekstbestand en geef een naam **aan bestand1.txt**. Upload dit tekstbestand naar de **broncontainer** in uw opslagaccount. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

    ![Bestand 1.txt maken en uploaden naar broncontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Als u terug wilt gaan naar de weergave **Pijplijnuitvoeringen,** selecteert u **Alle pijplijnuitvoeringen**en wacht u tot dezelfde pijplijn automatisch opnieuw wordt geactiveerd.  

    ![Alle pijplijnuitvoeringen selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Wanneer de tweede pijplijn is voltooid, volgt u dezelfde stappen hierboven om de details van de activiteitsrun te bekijken.  

    U ziet dat één bestand (file1.txt) is gekopieerd van de **broncontainer** naar de **doelcontainer** van uw Blob-opslagaccount.

    ![File1.txt is gekopieerd van broncontainer naar doelcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Maak een ander leeg tekstbestand en geef een naam **aan bestand2.txt**. Upload dit tekstbestand naar de **broncontainer** in uw Blob-opslagaccount.

16. Herhaal stap 13 en 14 voor dit tweede tekstbestand. U ziet dat alleen het nieuwe bestand (file2.txt) is gekopieerd van de **broncontainer** naar de **doelcontainer** van uw opslagaccount in de volgende pijplijnuitvoering.  

    U dit ook verifiëren door [Azure Storage Explorer](https://storageexplorer.com/) te gebruiken om de bestanden te scannen.

    ![Bestanden scannen met Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Apache Spark-cluster op Azure:

> [!div class="nextstepaction"]
>[Gegevens in de cloud transformeren met behulp van een Apache Spark-cluster](tutorial-transform-data-spark-portal.md)
