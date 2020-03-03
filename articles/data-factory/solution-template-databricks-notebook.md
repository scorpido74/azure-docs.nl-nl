---
title: Trans formatie met Azure Databricks
description: Meer informatie over het gebruik van een oplossings sjabloon voor het transformeren van gegevens met behulp van een Databricks-notebook in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/10/2018
ms.openlocfilehash: 322f5306949b266958ded908e981ed530e8245c8
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78227597"
---
# <a name="transformation-with-azure-databricks"></a>Trans formatie met Azure Databricks

In deze zelf studie maakt u een end-to-end-pijp lijn met **validatie**-, **Kopieer**-en **notitieblok** activiteiten in Data Factory.

-   **Validatie** activiteit wordt gebruikt om ervoor te zorgen dat de bron-gegevensset gereed is voor downstream-gebruik voordat de kopieer-en analyse taak wordt geactiveerd.

-   Met **copy** activity kopieert u het bron bestand/de gegevensset naar de Sink-opslag. De Sink-opslag wordt gekoppeld als DBFS in de Databricks-notebook, zodat de gegevensset rechtstreeks kan worden gebruikt door Spark.

-   Met de **Databricks-notebook** activiteit wordt de Databricks-notebook geactiveerd waarmee de gegevensset wordt getransformeerd en toegevoegd aan een verwerkte map/SQL DW.

De sjabloon maakt geen geplande trigger om deze sjabloon eenvoudig te kunnen gebruiken. U kunt deze indien nodig toevoegen.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Vereisten

1. Maak een **Blob Storage-account** en een container met de naam `sinkdata` die als **sink**moet worden gebruikt. Noteer de naam van het **opslag account**, de **container naam**en de **toegangs sleutel**, omdat hiernaar later in de sjabloon wordt verwezen.

2. Zorg ervoor dat u een **Azure Databricks-werk ruimte** hebt of een nieuwe hebt gemaakt.

3. **Importeer het notitie blok voor trans formatie**. 
    1. In uw Azure Databricks verwijzen we naar de volgende scherm afbeeldingen voor het importeren van een **transformatie** notitieblok in de Databricks-werk ruimte. Het hoeft zich niet op dezelfde locatie te bevinden als hieronder, maar onthoud het pad dat u later hebt gekozen.
   
       ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)    
    
    1. Selecteer importeren vanuit: **URL**en voer de volgende URL in het tekstvak in:
    
       * `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`
        
       ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)    

4. Nu gaan we de **trans formatie** -laptop bijwerken met de verbindings gegevens van uw opslag. Ga naar **opdracht 5** (zoals weer gegeven in het onderstaande code fragment) in het geïmporteerde notitie blok en vervang `<storage name>`en `<access key>` door uw eigen opslag verbindings gegevens. Zorg ervoor dat dit account hetzelfde opslag account is dat eerder is gemaakt en dat de `sinkdata` container bevat.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

5.  Genereer een **Databricks-toegangs token** voor Data Factory om toegang te krijgen tot Databricks. **Sla het toegangs token** op voor later gebruik bij het maken van een Databricks-gekoppelde service, die er ongeveer als volgt uitziet: ' dapi32db32cbb4w6eee18b7d87e45exxxxxx '.

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="how-to-use-this-template"></a>Deze sjabloon gebruiken

1.  Ga naar **trans formatie met Azure Databricks** sjabloon. Nieuwe gekoppelde services maken voor de volgende verbindingen. 
    
    ![Instelling verbindingen](media/solution-template-Databricks-notebook/connections-preview.png)

    1.  **Bron-BLOB-verbinding** : voor toegang tot bron gegevens. 
        
        U kunt de open bare Blob-opslag met de bron bestanden voor dit voor beeld gebruiken. Raadpleeg de volgende scherm afbeelding voor configuratie. Gebruik de onderstaande **SAS-URL** om verbinding te maken met de bron opslag (alleen-lezen toegang): 
        * `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Doel-BLOB-verbinding** : voor het kopiëren van gegevens naar. 
        
        Selecteer in de gekoppelde Sink-service een opslag die is gemaakt in de **vereiste** 1.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Azure Databricks** – voor het maken van een verbinding met een Databricks-cluster.

        Maak een Databricks-gekoppelde service met behulp van de toegangs sleutel die is gegenereerd in **vereiste** 2. c. Als u een *interactief cluster*hebt, kunt u dat selecteren. (In dit voor beeld wordt de optie *nieuw taak cluster* gebruikt.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

1. Selecteer **deze sjabloon gebruiken**en u ziet een pijp lijn die u hebt gemaakt, zoals hieronder wordt weer gegeven:
    
    ![Een pijplijn maken](media/solution-template-Databricks-notebook/new-pipeline.png)   

## <a name="pipeline-introduction-and-configuration"></a>Inleiding en configuratie van pijp lijn

In de nieuwe pijp lijn die is gemaakt, zijn de meeste instellingen automatisch geconfigureerd met standaard waarden. Bekijk de configuraties en update waar nodig om uw eigen instellingen aan te passen. Voor meer informatie kunt u de onderstaande instructies en scherm afbeeldingen raadplegen.

1.  Er wordt een **beschikbaarheids vlag** voor validatie activiteit gemaakt voor het uitvoeren van een controle op de beschik baarheid van de bron. *SourceAvailabilityDataset* gemaakt in de vorige stap is geselecteerd als gegevensset.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Er wordt een Kopieer activiteit **-bestand-naar-BLOB** gemaakt voor het kopiëren van de gegevensset van de bron naar de sink. Raadpleeg de onderstaande scherm afbeeldingen voor bron-en Sink-configuraties in de Kopieer activiteit.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  Er wordt een notitieblok activiteit **transformatie** gemaakt en de gekoppelde service die u in de vorige stap hebt gemaakt, is geselecteerd.
    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

     1. Selecteer het tabblad **instellingen** . Voor het pad van een *notebook*definieert de sjabloon standaard een pad. Mogelijk moet u bladeren en het juiste notitieblok pad selecteren dat is geüpload in **vereiste** 2. 

         ![17](media/solution-template-Databricks-notebook/databricks-tutorial-image17.png)
    
     1. Bekijk de *Base-para meters* die zijn gemaakt, zoals weer gegeven in de scherm opname. Ze moeten worden door gegeven aan de Databricks notebook van Data Factory. 

         ![Basis parameters](media/solution-template-Databricks-notebook/base-parameters.png)

1.  **Pijplijn parameters** worden hieronder gedefinieerd.

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1. Gegevens sets instellen.
    1.  **SourceAvailabilityDataset** wordt gemaakt om te controleren of er bron gegevens beschikbaar zijn.

        ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **SourceFilesDataset** -voor het kopiëren van de bron gegevens.

        ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **DestinationFilesDataset** : voor het kopiëren naar de Sink/doel locatie.

        1.  Gekoppelde service- *sinkBlob_LS* gemaakt in de vorige stap.

        2.  Bestandspad- *sinkdata/staged_sink*.

            ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)


1.  Selecteer **debug** om de pijp lijn uit te voeren. U vindt een koppeling naar Databricks-logboeken voor meer gedetailleerde Spark-Logboeken.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    U kunt ook het gegevens bestand controleren met behulp van Storage Explorer. (Als u wilt correleren met Data Factory pijplijn uitvoeringen, wordt in dit voor beeld de ID van de pijplijn uitvoering van data factory toegevoegd aan de uitvoermap. Op deze manier kunt u de bestanden die zijn gegenereerd via elke uitvoering, traceren.)

    ![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
