---
title: Een Databricks-notitieblok uitvoeren met de activiteit
description: Meer informatie over hoe u de Databricks Notebook-activiteit in een Azure Data Factory kunt gebruiken voor het uitvoeren van een Databricks-notebook in het Databricks-takencluster.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2018
ms.openlocfilehash: e63180a3c4b8de06ab9e26afc8fff322188291cc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418980"
---
# <a name="run-a-databricks-notebook-with-the-databricks-notebook-activity-in-azure-data-factory"></a>De Databricks Notebook-activiteit uitvoeren in Azure Data Factory op een Databricks-notebook

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelfstudie gebruikt u Azure Portal om een Azure Data Factory-pijplijn te maken die een Databricks-notebook uitvoert op basis van het Databricks-takencluster. Bovendien worden tijdens de uitvoering Azure Data Factory-parameters doorgestuurd naar de Databricks-notebook.

In deze zelfstudie voert u de volgende stappen uit:

  - Een gegevensfactory maken.

  - Een pijplijn maken die gebruikmaakt van Databricks Notebook-activiteit.

  - Een pijplijnuitvoering activeren.

  - De pijplijnuitvoering controleert.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Bekijk de volgende video voor een inleiding en demonstratie van deze functie van 11 minuten:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/ingest-prepare-and-transform-using-azure-databricks-and-data-factory/player]

## <a name="prerequisites"></a>Vereisten

  - **Azure Databricks-werkruimte**. [Maak een Databricks-werkruimte](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) of gebruik een bestaande werkruimte. U maakt een Python-notebook in uw Azure Databricks-werkruimte. Vervolgens voert u de notebook uit en geeft u er parameters aan door met behulp van Azure Data Factory.

## <a name="create-a-data-factory"></a>Een gegevensfactory maken

1.  Start de webbrowser **Microsoft Edge** of **Google Chrome**. Op dit moment wordt de Data Factory-gebruikersinterface alleen ondersteund in de webbrowsers Microsoft Edge en Google Chrome.

1.  Selecteer **Een resource maken** in het menu links, selecteer **Analyse** en selecteer vervolgens **Data Factory**.

    ![Een nieuwe gegevensfactory maken](media/transform-data-using-databricks-notebook/new-azure-data-factory-menu.png)

1.  Voer op de pagina **Nieuwe data factory****ADFTutorialDataFactory** in bij **Naam**.

    De naam van de Azure-gegevensfabriek moet *wereldwijd uniek*zijn. Als u de volgende fout ziet, wijzigt u de naam van de gegevensfactory. (Gebruik bijvoorbeeld ** \<de\>naam ADFTutorialDataFactory**). Zie het artikel [Data factory - Naamgevingsregels](https://docs.microsoft.com/azure/data-factory/naming-rules) voor naamgevingsregels voor Data Factory-artefacten.

    ![Een naam opgeven voor de nieuwe gegevensfactory](media/transform-data-using-databricks-notebook/new-azure-data-factory.png)

1.  Selecteer voor **Abonnement** het Azure-abonnement waarin u de gegevensfactory wilt maken.

1.  Voer een van de volgende stappen uit voor **Resourcegroep**:
    
    - Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.
    
    - Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in.

    Voor sommige van de stappen in deze snelstartgids wordt ervan uitgegaan dat u voor de resourcegroep de naam **ADFTutorialResourceGroup** gebruikt. Zie [Resourcegroepen gebruiken om Azure-resources te beheren](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) voor meer informatie.

1.  Selecteer **V2** als **Versie**.

1.  Selecteer voor **Locatie** een locatie voor de data factory.

    Voor een lijst met Azure-regio’s waarin Data Factory momenteel beschikbaar is, selecteert u op de volgende pagina de regio’s waarin u geïnteresseerd bent, vouwt u vervolgens **Analytics** uit en gaat u naar **Data Factory**: [Beschikbare producten per regio](https://azure.microsoft.com/global-infrastructure/services/). De gegevensarchieven (zoals Azure Storage en Azure SQL Database) en berekeningen (zoals Azure HDInsight) die Data Factory gebruikt, kunnen zich in andere regio's bevinden.
1.  Selecteer **Maken**.


1.  Nadat de creatie is voltooid, ziet u de **fabriekspagina Gegevens.** Selecteer de tegel **Maken en controleren** om de gebruikersinterface van Data Factory te openen op een afzonderlijk tabblad.

    ![De gebruikersinterface van Data Factory starten](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image4.png)

## <a name="create-linked-services"></a>Gekoppelde services maken

In deze sectie maakt u een aan Databricks gekoppelde service. Deze gekoppelde service bevat de verbindingsgegevens voor het Databricks-cluster:

### <a name="create-an-azure-databricks-linked-service"></a>Een aan Azure Databricks gekoppelde service maken

1.  Schakel op de pagina **Aan de slag** naar het tabblad **Bewerken** in het linkerpaneel.

    ![De nieuwe gekoppelde service opslaan](media/transform-data-using-databricks-notebook/get-started-page.png)

1.  Selecteer **Verbindingen** onder aan het venster en selecteer **+ Nieuw**.
    
    ![Een nieuwe verbinding maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image6.png)

1.  Selecteer in het venster **Nieuwe gekoppelde service** de optie **Compute** \> **Azure Databricks** en selecteer vervolgens **Doorgaan**.
    
    ![Een aan Databricks gekoppelde service opgeven](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image7.png)

1.  Voer in het venster **Nieuwe gekoppelde service** de volgende stappen uit:
    
    1.  Voer ***AzureDatabricks\_LinkedService*** in voor **Naam**
    
    1.  Selecteer de juiste **Databricks-werkruimte** waarin u uw notebook wilt uitvoeren

    1.  Selecteer bij **Cluster selecteren****Nieuw taakcluster**
    
    1.  Bij **Domein/regio** zou de informatie automatisch moeten worden ingevuld

    1.  Genereer de **Toegangstoken** vanuit de Azure Databricks-werkruimte. U kunt de daarvoor benodigde stappen [hier](https://docs.databricks.com/api/latest/authentication.html#generate-token) vinden.

    1.  Selecteer bij **Clusterversie** de optie **4.2** (met Apache Spark 2.3.1, Scala 2.11)

    1.  Selecteer voor deze zelfstudie bij **Clusterknooppunttype****Standard\_D3\_v2** onder de categorie **Algemeen gebruik (HDD)**. 
    
    1.  Voer bij **Werkrollen****2** in.
    
    1.  Selecteer **Voltooien**

        ![Het maken van de gekoppelde service voltooien](media/transform-data-using-databricks-notebook/new-databricks-linkedservice.png)

## <a name="create-a-pipeline"></a>Een pijplijn maken

1.  Selecteer **+** de knop (plus) en selecteer **Vervolgens Pijplijn** in het menu.

    ![Knoppen voor het maken van een nieuwe pijplijn](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image9.png)

1.  Maak een **parameter** voor gebruik in de **pijplijn**. Deze parameter geeft u later door aan de Databricks Notebook-activiteit. Open in de lege pijplijn het tabblad **Parameters**, klik op **Nieuw** en noem deze '**naam**'.

    ![Een nieuwe parameter maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image10.png)

    ![De naam van de parameter maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image11.png)

1.  Vouw in de werkset **Activiteiten****Databricks** uit. Sleep de activiteit **Notebook** vanuit de werkset **Activiteiten** naar het ontwerpoppervlak voor pijplijnen.

    ![Sleep de notebook naar het ontwerpoppervlak](media/transform-data-using-databricks-notebook/new-adf-pipeline.png)

1.  Voer de volgende stappen uit in de eigenschappen voor het **Databricks** **Notebook**-activiteitvenster onderaan:

    a. Schakel over naar het tabblad **Azure Databricks**.

    b. Selecteer **AzureDatabricks\_LinkedService** (gemaakt in de vorige procedure).

    c. Schakel over naar het tabblad **Instellingen**

    c. Zoek en selecteer een Databricks **notebook-pad**. We gaan een notebook maken en geven daarvoor hier het pad op. U krijgt het pad van de notebook door de volgende stappen uit te voeren.

       1. De Azure Databricks-werkruimte openen

       1. Maak een **Nieuwe map** in de werkruimte en roep deze aan als **adftutorial**.

          ![Een nieuwe map maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image13.png)

       1. [Maak een nieuw notitieblok](https://docs.databricks.com/user-guide/notebooks/index.html#creating-a-notebook) (Python), laten we het **mynotebook** noemen onder **adftutorial** Map, klik op **Maken.**

          ![Een nieuwe notebook maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image14.png)

          ![De eigenschappen van de nieuwe notebook instellen](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image15.png)

       1. In de zojuist gemaakte notebook 'mynotebook' voegt u de volgende code toe:

           ```
           # Creating widgets for leveraging parameters, and printing the parameters

           dbutils.widgets.text("input", "","")
           y = dbutils.widgets.get("input")
           print ("Param -\'input':")
           print (y)
           ```

           ![Widgets voor parameters maken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image16.png)

       1. In dit geval is het **Notebook-pad****/adftutorial/mynotebook**

1.  Ga terug naar de **gebruikersinterface van Data Factory**. Ga naar het tabblad **Instellingen** onder **Notebook1-activiteit**.

    a.  **Voeg een parameter toe** aan de Notebook-activiteit. U gebruikt dezelfde parameter die u eerder aan de **pijplijn** hebt toegevoegd.

       ![Een parameter toevoegen](media/transform-data-using-databricks-notebook/new-adf-parameters.png)

    b.  Geef de parameter een naam als **invoer** en geef de waarde op als ** \@expressiepijplijn().parameters.name**.

1.  Selecteer op de werkbalk de knop **Valideren** om de pijplijn te valideren. Als u het validatievenster ** \> ** wilt sluiten, selecteert u de knop (pijl-rechts).

    ![De pijplijn valideren](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image18.png)

1.  Selecteer **Alles publiceren**. De gebruikersinterface van Data Factory publiceert entiteiten (gekoppelde services en pijplijn) naar de Azure Data Factory-service.

    ![De nieuwe Data Factory-entiteiten publiceren](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image19.png)

## <a name="trigger-a-pipeline-run"></a>Een pijplijnuitvoering activeren

Selecteer op de werkbalk de optie **Activeren** en selecteer vervolgens **Nu activeren**.

![Opdracht Nu activeren selecteren](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image20.png)

Het dialoogvenster **Pijplijnuitvoering**vraagt naar de **naam**-parameter. Gebruik hier **/pad/bestandsnaam** als parameter. Klik **op Voltooien.**

![Een waarde voor de naam-parameters opgeven](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image21.png)

## <a name="monitor-the-pipeline-run"></a>De pijplijnuitvoering controleren.

1.  Ga naar het tabblad **Monitor.** Controleer of er een pijplijn wordt uitgevoerd. Het duurt 5 tot 8 minuten om een Databricks-taakcluster te maken, waar de notebook wordt uitgevoerd.

    ![De pijplijn bewaken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image22.png)

1.  Selecteer regelmatig **Vernieuwen** om de status van de pijplijnuitvoering te controleren.

1.  Selecteer **Uitvoering van activiteiten weergeven** in de kolom **Acties** om de uitvoering van activiteiten weer te geven die zijn gekoppeld aan de pijplijnuitvoering.

    ![Uitvoeringen van activiteit bekijken](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image23.png)

Als u wilt terugkeren naar de vorige weergave, selecteert u **Pijplijnen** bovenaan.

## <a name="verify-the-output"></a>De uitvoer controleren

Meld u aan bij de **Azure Databricks-werkruimte**, ga naar **Clusters** en de **Taak**-status wordt weergegeven als *uitvoering in behandeling, wordt uitgevoerd of beëindigd*.

![De clustertaak en de taak weergeven](media/transform-data-using-databricks-notebook/databricks-notebook-activity-image24.png)

U kunt klikken op de **Taaknaam** om meer informatie weer te geven. Na een gelukte uitvoering kunt u de doorgegeven parameters en de uitvoer van de Python-notebook valideren.

![Uitvoeringsdetails en uitvoer weergeven](media/transform-data-using-databricks-notebook/databricks-output.png)

## <a name="next-steps"></a>Volgende stappen

De pijplijn in dit voorbeeld activeert een Databricks Notebook-activiteit en geeft daar een parameter aan door. U hebt geleerd hoe u:

  - Een gegevensfactory maken.

  - Een pijplijn maken die gebruikmaakt van de Databricks Notebook-activiteit.

  - Een pijplijnuitvoering activeren.

  - De pijplijnuitvoering controleert.
