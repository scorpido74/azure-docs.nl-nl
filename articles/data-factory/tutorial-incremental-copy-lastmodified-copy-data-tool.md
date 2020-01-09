---
title: Hulp programma voor gegevens om nieuwe en bijgewerkte bestanden stapsgewijs te kopiëren
description: Maak een Azure-data factory en gebruik vervolgens het hulp programma Gegevens kopiëren om nieuwe bestanden op basis van LastModifiedDate stapsgewijs te laden.
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
ms.date: 1/24/2019
ms.openlocfilehash: 05696ae2fed31c267aa37013fac32db0916e24bf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439323"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Incrementeel nieuwe en gewijzigde bestanden op basis van LastModifiedDate kopiëren met behulp van het hulp programma Gegevens kopiëren

In deze zelf studie gebruikt u de Azure Portal om een data factory te maken. Vervolgens gebruikt u het hulp programma Gegevens kopiëren om een pijp lijn te maken die alleen nieuwe en gewijzigde bestanden incrementeel kopieert, op basis van hun **LastModifiedDate** van Azure Blob-opslag naar Azure Blob-opslag.

Als u dit doet, worden alle bestanden in het bron archief gescand, wordt het bestands filter toegepast op hun LastModifiedDate en wordt het nieuwe en bijgewerkte bestand pas na de laatste keer naar het doel archief gekopieerd.  Houd er rekening mee dat als u ADF een enorme hoeveelheid bestanden wilt laten scannen, maar slechts een paar bestanden naar een bestemming wilt kopiëren, de lange duur die nodig is voor het scannen van bestanden nog steeds tijd in beslag neemt.   

> [!NOTE]
> Zie [Inleiding tot Azure Data Factory](introduction.md) als u niet bekend bent met Azure Data Factory.

In deze zelf studie voert u de volgende taken uit:

> [!div class="checklist"]
> * Een gegevensfactory maken.
> * Het hulpprogramma Copy Data gebruiken om een pijplijn te maken.
> * De uitvoering van de pijplijn en van de activiteit controleren

## <a name="prerequisites"></a>Vereisten

* **Azure-abonnement**: als u nog geen abonnement op Azure hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* **Azure-opslag account**: Blob Storage gebruiken als _bron_ -en _sink_ -gegevens archief. Als u geen Azure-opslagaccount hebt, raadpleegt u de instructies in [Een opslagaccount maken](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Twee containers maken in Blob Storage

Bereid de Blob-opslag voor op de zelf studie door de volgende stappen uit te voeren.

1. Maak een container met de naam **Source**. U kunt verschillende hulpprogram ma's gebruiken om deze taak uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).

2. Maak een container met de naam **Destination**. 

## <a name="create-a-data-factory"></a>Een data factory maken

1. Selecteer in het menu links de optie **een resource maken** > **gegevens en analyses** > **Data Factory**: 
   
   ![Selectie van Data Factory in het deelvenster Nieuw](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Voer op de pagina **Nieuwe data factory** **ADFTutorialDataFactory** in bij **Naam**. 
 
   De naam van de data factory moet _wereldwijd uniek_ zijn. Mogelijk wordt het volgende foutbericht weergegeven:
   
   ![Foutbericht nieuwe data factory](./media/doc-common-process/name-not-available-error.png)

   Als u een foutbericht ontvangt dat betrekking heeft op de waarde die bij de naam is ingevuld, voert u een andere naam in voor de data factory. Gebruik bijvoorbeeld de naam _**uwnaam**_ **ADFTutorialDataFactory**. Raadpleeg het onderwerp [Data Factory - Naamgevingsregels](naming-rules.md) voor meer informatie over naamgevingsregels voor Data Factory-artefacten.
3. Selecteer het Azure- **abonnement** waarin u de nieuwe Data Factory wilt maken. 
4. Voer een van de volgende stappen uit voor **Resourcegroep**:
     
    * Selecteer **Bestaande gebruiken** en selecteer een bestaande resourcegroep in de vervolgkeuzelijst.

    * Selecteer **Nieuwe maken** en voer de naam van een resourcegroep in. 
         
    Zie [Resourcegroepen gebruiken om Azure-resources te beheren](../azure-resource-manager/management/overview.md) voor meer informatie.

5. Selecteer **v2**onder **versie**.
6. Selecteer bij **Locatie** de locatie voor de data factory. In de vervolgkeuzelijst worden alleen ondersteunde locaties weergegeven. De gegevens archieven (bijvoorbeeld Azure Storage en SQL Database) en berekeningen (bijvoorbeeld Azure HDInsight) die door uw data factory worden gebruikt, kunnen zich op andere locaties en regio's bevinden.
7. Selecteer **Vastmaken aan dashboard**. 
8. Selecteer **Maken**.
9. Raadpleeg op het dash board de tegel **implementatie Data Factory** om de status van het proces te bekijken.

    ![Data Factory tegel implementeren](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Nadat de data factory is gemaakt, wordt de startpagina **Data Factory** weergegeven.
   
    ![Startpagina van de gegevensfactory](./media/doc-common-process/data-factory-home-page.png)
11. Als u de Azure Data Factory gebruikers interface (UI) wilt openen op een afzonderlijk tabblad, selecteert u de tegel **Author & monitor** . 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Het hulpprogramma Copy Data gebruiken om een pijplijn te maken

1. Selecteer op de pagina **aan de slag** de **gegevens kopiëren** titel om het gegevens kopiëren-hulp programma te openen. 

   ![De tegel Copy Data-hulpprogramma](./media/doc-common-process/get-started-page.png)
   
2. Voer de volgende stappen uit op de pagina **Eigenschappen** :

    a. Voer onder **taak naam** **DeltaCopyFromBlobPipeline**in.

    b. Selecteer onder **taak uitgebracht** of **taak planning** **regel matig uitvoeren volgens schema**.

    c. Onder **trigger type**selecteert u **tumblingvenstertriggers-venster**.
    
    d. Voer onder **terugkeer patroon** **15 minuut/minuten**in. 
    
    e. Selecteer **Next**. 
    
    De gebruikersinterface van Data Factory maakt een pijplijn met de opgegeven taaknaam. 

    ![De pagina Eigenschappen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Voltooi op de pagina **Brongegevensarchief** de volgende stappen:

    a. Selecteer **+ nieuwe verbinding maken**om een verbinding toe te voegen.
    
    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Selecteer **Azure-Blob Storage** in de galerie en selecteer vervolgens **door gaan**.
    
    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Selecteer op de pagina **nieuwe gekoppelde service** uw opslag account in de lijst **naam van het opslag account** en selecteer vervolgens **volt ooien**.
    
    ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Selecteer de zojuist gemaakte gekoppelde service en selecteer vervolgens **volgende**. 
    
   ![De pagina Brongegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Voltooi op de pagina **Invoerbestand of invoermap kiezen** de volgende stappen:
    
    a. Blader en selecteer de **bronmap** en selecteer vervolgens **kiezen**.
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. Selecteer bij **gedrag bij het laden van bestanden**de optie **Incrementeel laden: LastModifiedDate**.
    
    ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Controleer de **binaire kopie** en selecteer **volgende**.
    
     ![Kies het invoerbestand of invoermap.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Selecteer op de pagina **doel gegevens archief** de optie **AzureBlobStorage**. Dit is hetzelfde opslag account als het gegevens archief van de bron. Selecteer vervolgens **Volgende**.

    ![De pagina Doelgegevensarchief](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Voltooi op de pagina **Uitvoerbestand of uitvoermap kiezen** de volgende stappen:
    
    a. Blader en selecteer de **doelmap** en selecteer vervolgens **kiezen**.
    
    ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Selecteer **Next**.
    
     ![Het uitvoerbestand of de uitvoermap kiezen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Selecteer op de pagina **Instellingen** de optie **Volgende**. 

    ![De pagina Instellingen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Controleer de instellingen op de pagina **samen vatting** en selecteer **volgende**.

    ![Overzichtspagina](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Selecteer op de pagina **Implementatie** de optie **Controleren** om de pijplijn of taak te controleren.

    ![De pagina Implementatie](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. U ziet dat het tabblad **Controleren** aan de linkerkant automatisch wordt geselecteerd. De kolom **Acties** bevat onder andere koppelingen om details van de uitvoering van activiteiten te bekijken en de pijplijn opnieuw uit te voeren. Selecteer **vernieuwen** om de lijst te vernieuwen en selecteer de koppeling **uitvoeringen van activiteit weer geven** in de kolom **acties** . 

    ![Lijst vernieuwen en uitvoering van activiteit weer geven selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Er is slechts één activiteit (de Kopieer activiteit) in de pijp lijn, zodat er slechts één vermelding wordt weer gegeven. Selecteer de link **Details** (pictogram van een bril) in de kolom **Acties** om details over de kopieerbewerking te zien. 

    ![De Kopieer activiteit bevindt zich in een pijp lijn](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Omdat er zich geen bestand in de **bron** container in uw Blob Storage-account bevindt, ziet u geen bestanden die zijn gekopieerd naar de **doel** container in uw Blob Storage-account.
    
    ![Geen bestand in bron container of doel container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Maak een leeg tekst bestand en geef het de naam **bestand1. txt**. Upload dit tekst bestand naar de **bron** container in uw opslag account. U kunt verschillende hulpprogramma's gebruiken om deze taken uit te voeren, zoals [Azure Storage Explorer](https://storageexplorer.com/).   

    ![Bestand1. txt maken en uploaden naar de bron container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Als u wilt terugkeren naar de weer gave **pijplijn uitvoeringen** , selecteert u **alle pijplijn uitvoeringen**en wacht u totdat dezelfde pijp lijn opnieuw wordt geactiveerd.  

    ![Alle pijplijn uitvoeringen selecteren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Selecteer **uitvoering van activiteit weer geven** voor de tweede pijplijn uitvoering wanneer u deze ziet. Bekijk de details op dezelfde manier als bij de eerste uitvoering van de pijp lijn.  

    ![Selecteer uitvoering van activiteit weer geven en Details bekijken](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    U ziet dat er één bestand (bestand1. txt) is gekopieerd van de **bron** container naar de **doel** container van uw Blob Storage-account.
    
    ![Bestand1. txt is gekopieerd van de bron container naar de doel container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Maak nog een leeg tekst bestand en geef het de naam **bestand2. txt**. Upload dit tekst bestand naar de **bron** container in uw Blob Storage-account.   
    
16. Herhaal stap 13 en 14 voor dit tweede tekst bestand. U ziet dat alleen het nieuwe bestand (bestand2. txt) is gekopieerd van de **bron** container naar de **doel** container van uw opslag account in de volgende pijplijn uitvoering.  
    
    ![Bestand2. txt is gekopieerd van de bron container naar de doel container](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    U kunt dit ook controleren door [Azure Storage Explorer](https://storageexplorer.com/) te gebruiken voor het scannen van de bestanden.
    
    ![Bestanden scannen met Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Volgende stappen
Ga naar de volgende zelf studie voor meer informatie over het transformeren van gegevens met behulp van een Apache Spark cluster in Azure:

> [!div class="nextstepaction"]
>[Gegevens in de Cloud transformeren met behulp van een Apache Spark cluster](tutorial-transform-data-spark-portal.md)