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
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399486"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopieer stapsgewijs nieuwe en gewijzigde bestanden op basis van LastModifiedDate met het gereedschap Gegevens kopiëren

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u de Azure-portal om een gegevensfabriek te maken. Vervolgens gebruikt u het hulpprogramma Gegevens kopiëren om een pijplijn te maken die alleen nieuwe en gewijzigde bestanden kopieert, van Azure Blob-opslag naar Azure Blob-opslag. Het `LastModifiedDate` gebruikt om te bepalen welke bestanden te kopiëren.

Nadat u de stappen hier hebt voltooid, scant Azure Data Factory alle bestanden `LastModifiedDate`in het bronarchief, past u het bestandsfilter toe op en kopieert u alleen bestanden naar het doelarchief die nieuw zijn of zijn bijgewerkt sinds de vorige keer. Houd er rekening mee dat als Data Factory grote aantallen bestanden scant, u nog steeds lange duur moet verwachten. Het scannen van bestanden is tijdrovend, zelfs wanneer de hoeveelheid gekopieerde gegevens wordt verminderd.

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelfstudie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure Storage-account:** gebruik Blob-opslag voor de bron- en sinkgegevensopslag. Als u geen Azure Storage-account hebt, volgt u de instructies in [Een opslagaccount maken.](../storage/common/storage-account-create.md)

## <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob-opslag

Bereid uw Blob-opslag voor op de zelfstudie door de volgende stappen uit te voeren:

1. Een container met de naam **bron maken**. U verschillende hulpprogramma's gebruiken om deze taak uit te voeren, zoals [Azure Storage Explorer.](https://storageexplorer.com/)

2. Een container met de naam **bestemming maken**.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1. Selecteer **Een resource maken** in het linkerdeelvenster. Selecteer **Analytics** > **Data Factory:**

   ![Gegevensfabriek selecteren](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

   De naam van de data factory moet wereldwijd uniek zijn. Mogelijk ontvangt u dit foutbericht:

   ![Foutbericht Naam niet beschikbaar](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_**ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer **onder Abonnement**het Azure-abonnement waarin u de nieuwe gegevensfabriek maakt.
4. Neem **onder Resource Group**een van de volgende stappen:

    * Selecteer **Bestaand gebruiken** en selecteer vervolgens een bestaande resourcegroep in de lijst.

    * Selecteer **Nieuw maken** en voer vervolgens een naam in voor de resourcegroep.
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer **V2** onder **Versie**.
6. Selecteer bij **Locatie** de locatie voor de data factory. Er worden alleen ondersteunde locaties weergegeven in de lijst. De gegevensarchieven (bijvoorbeeld Azure Storage en Azure SQL Database) en computes (bijvoorbeeld Azure HDInsight) die uw gegevensfabriek gebruikt, kunnen zich op andere locaties en regio's bevinden.
8. Selecteer **Maken**.
9. Nadat de gegevensfabriek is gemaakt, wordt de startpagina van de gegevensfabriek weergegeven.
10. Als u de gebruikersinterface van Azure Data Factory (UI) op een afzonderlijk tabblad wilt openen, selecteert u de tegel **Auteur & monitor:**

    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **Laten beginnen** de tegel **Gegevens kopiëren** om het gereedschap Gegevens kopiëren te openen:

   ![Tegel Gegevens kopiëren](./media/doc-common-process/get-started-page.png)

2. Ga op de pagina **Eigenschappen** de volgende stappen uit:

    a. Voer **onder Taaknaam** **DeltaCopyFromBlobPipeline in**.

    b. Selecteer Onder **Taakcadans of Taakplanning**de optie **Regelmatig uitvoeren op schema**.

    c. Selecteer **Tumbling-venster**onder **Triggertype**.

    d. Voer **onder Herhaling** **15 minuten(en)** in .

    e. Selecteer **Next**.

    Data Factory maakt een pijplijn met de opgegeven taaknaam.

    ![Pagina Gegevenseigenschappen kopiëren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Voer op de pagina **Brongegevensarchief** de volgende stappen uit:

    a. Selecteer **Nieuwe verbinding maken** om een verbinding toe te voegen.

    b. Selecteer **Azure Blob Storage** in de galerie en selecteer **Doorgaan:**

    ![Azure-blogopslag selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Selecteer op de pagina **Nieuwe Gekoppelde Service (Azure Blob Storage)** uw opslagaccount in de lijst Met de naam **van het opslagaccount.** Test de verbinding en selecteer **Vervolgens Maken**.

    d. Selecteer de nieuwe gekoppelde service en selecteer **Volgende:**

   ![De nieuwe gekoppelde service selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:

    a. Blader naar en selecteer de **bronmap** en selecteer **Vervolgens Kiezen**.

    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Selecteer **onder gedrag voor het laden van**bestanden de optie **Incrementele belasting: LastModifiedDate**.

    c. Selecteer **Binaire kopie** en selecteer **Volgende:**

     ![De invoerbestands- of mappagina kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Selecteer op de pagina **Bestemmingsgegevensarchief** de **AzureBlobStorage-service** die u hebt gemaakt. Dit is hetzelfde opslagaccount als het brongegevensarchief. Selecteer **vervolgens Volgende**.

6. Voltooi op de pagina **Uitvoerbestand of uitvoermap kiezen** de volgende stappen:

    a. Blader naar en selecteer de **doelmap** en selecteer **Kiezen:**

    ![Kies het uitvoerbestand of de mappagina](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Selecteer **Next**.

7. Selecteer op de pagina **Instellingen** de optie **Volgende**.

8. Controleer **op** de pagina Overzicht de instellingen en selecteer **Volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.

    ![De pagina Implementatie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De toepassing schakelt over naar het tabblad **Monitor.** U ziet de status van de pijplijn. Selecteer **Vernieuwen** om de lijst te vernieuwen. Selecteer de koppeling onder **PIPELINE NAME** om activiteitsgegevens weer te geven of de pijplijn opnieuw uit te voeren.

    ![De lijst vernieuwen en details van activiteitenuitvoeren weergeven](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Er is slechts één activiteit (de kopieeractiviteit) in de pijplijn, dus u ziet slechts één vermelding. Selecteer de koppeling **Details** (het brilpictogram) in de kolom **ACTIVITEITSNAAM** voor meer informatie over de kopieerbewerking. Zie [Overzicht van](copy-activity-overview.md)activiteit kopiëren voor meer informatie over de eigenschappen .

    ![Activiteit in de pijplijn kopiëren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Omdat er geen bestanden in de broncontainer in uw Blob-opslagaccount zijn, worden er geen bestanden gekopieerd naar de doelcontainer in het account:

    ![Geen bestanden in de broncontainer of doelcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Maak een leeg tekstbestand en geef een naam **aan bestand1.txt**. Upload dit tekstbestand naar de broncontainer in uw opslagaccount. U verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer.](https://storageexplorer.com/)

    ![Bestand 1.txt maken en uploaden naar de broncontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Als u terug wilt gaan naar de weergave **Pijplijnuitvoeringen,** selecteert u **Alle pijplijnuitvoeringen**en wacht u tot dezelfde pijplijn automatisch opnieuw wordt geactiveerd.  

    ![Alle pijplijnuitvoeringen selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Wanneer de tweede pijplijn is voltooid, volgt u dezelfde stappen die eerder zijn vermeld om de details van de activiteitsrun te bekijken.  

    U ziet dat één bestand (bestand1.txt) is gekopieerd van de broncontainer naar de doelcontainer van uw Blob-opslagaccount:

    ![file1.txt is gekopieerd van de broncontainer naar de doelcontainer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Maak een ander leeg tekstbestand en geef een naam **aan bestand2.txt**. Upload dit tekstbestand naar de broncontainer in uw Blob-opslagaccount.

16. Herhaal stap 13 en 14 voor het tweede tekstbestand. U ziet dat alleen het nieuwe bestand (file2.txt) tijdens deze pijplijnrun is gekopieerd van de broncontainer naar de doelcontainer van uw opslagaccount.  

    U ook controleren of slechts één bestand is gekopieerd met [Azure Storage Explorer](https://storageexplorer.com/) om de bestanden te scannen:

    ![Bestanden scannen met Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelfstudie voor meer informatie over het transformeren van gegevens met behulp van een Apache Spark-cluster op Azure:

> [!div class="nextstepaction"]
>[Gegevens in de cloud transformeren met behulp van een Apache Spark-cluster](tutorial-transform-data-spark-portal.md)
