---
title: Gegevens transformeren met behulp van Databricks
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
ms.openlocfilehash: d21bab2f358e8ae9460bff2305957ed901c70926
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927753"
---
# <a name="transform-data-by-using-databricks-in-azure-data-factory"></a>Gegevens transformeren met behulp van Databricks in Azure Data Factory

In deze zelf studie maakt u een end-to-end-pijp lijn met **Zoek**-, **Kopieer**-en **Databricks notebook** -activiteiten in Data Factory.

-   De activiteit **lookup** of GetMetadata wordt gebruikt om ervoor te zorgen dat de bron-gegevensset gereed is voor downstream-gebruik voordat de kopieer-en analyse taak wordt geactiveerd.

-   Met **copy activity** kopieert u het bron bestand/de gegevensset naar de Sink-opslag. De Sink-opslag wordt gekoppeld als DBFS in de Databricks-notebook, zodat de gegevensset rechtstreeks kan worden gebruikt door Spark.

-   Met de **Databricks-notebook activiteit** wordt de Databricks-notebook geactiveerd waarmee de gegevensset wordt getransformeerd en toegevoegd aan een verwerkte map/SQL DW.

De sjabloon maakt geen geplande trigger om deze sjabloon eenvoudig te kunnen gebruiken. U kunt deze indien nodig toevoegen.

![1](media/solution-template-Databricks-notebook/Databricks-tutorial-image01.png)

## <a name="prerequisites"></a>Vereisten

1.  Maak een **Blob Storage-account** en een container met de naam `sinkdata` die als **sink**moet worden gebruikt. Noteer de naam van het **opslag account**, de **container naam**en de **toegangs sleutel**, omdat hiernaar later in de sjabloon wordt verwezen.

2.  Zorg ervoor dat u een **Azure Databricks-werk ruimte** hebt of een nieuwe hebt gemaakt.

1.  **Importeer het notitie blok voor etl**. Importeer de onderstaande notitie blok voor trans formatie naar de Databricks-werk ruimte. (Dit hoeft zich niet op dezelfde locatie te bevinden als hieronder, maar onthoud het pad dat u later hebt gekozen.) Importeer het notitie blok van de volgende URL door deze URL in te voeren in het URL-veld: `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`. Selecteer **Importeren**.

    ![2](media/solution-template-Databricks-notebook/Databricks-tutorial-image02.png)

    ![3](media/solution-template-Databricks-notebook/Databricks-tutorial-image03.png)  

1.  Nu gaan we de **transformatie** notitieblok bijwerken met de **verbindings gegevens** van uw opslag (naam en toegangs sleutel). Ga naar **opdracht 5** in het geïmporteerde notitie blok, vervang deze door het onderstaande code fragment nadat u de gemarkeerde waarden hebt vervangen. Zorg ervoor dat dit account hetzelfde opslag account is dat eerder is gemaakt en dat de `sinkdata` container bevat.

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

1.  Genereer een **Databricks-toegangs token** voor Data Factory om toegang te krijgen tot Databricks. **Sla het toegangs token** op voor later gebruik bij het maken van een Databricks-gekoppelde service, die er ongeveer als volgt uitziet: ' dapi32db32cbb4w6eee18b7d87e45exxxxxx '

    ![4](media/solution-template-Databricks-notebook/Databricks-tutorial-image04.png)

    ![5](media/solution-template-Databricks-notebook/Databricks-tutorial-image05.png)

## <a name="create-linked-services-and-datasets"></a>Gekoppelde services en gegevens sets maken

1.  Nieuwe **gekoppelde services** in Data Factory gebruikers interface maken door naar *verbindingen gekoppelde services + nieuw* te gaan

    1.  **Bron** : voor toegang tot bron gegevens. U kunt de open bare Blob-opslag met de bron bestanden voor dit voor beeld gebruiken.

        Selecteer **Blob Storage**, gebruik de onderstaande **SAS-URI** om verbinding te maken met de bron opslag (alleen-lezen toegang).

        `https://storagewithdata.blob.core.windows.net/?sv=2017-11-09&ss=b&srt=sco&sp=rl&se=2019-12-31T21:40:53Z&st=2018-10-24T13:40:53Z&spr=https&sig=K8nRio7c4xMLnUV0wWVAmqr5H4P3JDwBaG9HCevI7kU%3D`

        ![6](media/solution-template-Databricks-notebook/Databricks-tutorial-image06.png)

    1.  **Sink** : voor het kopiëren van gegevens naar.

        Selecteer een opslag die in de voor waarde 1 is gemaakt in de Sink-gekoppelde service.

        ![7](media/solution-template-Databricks-notebook/Databricks-tutorial-image07.png)

    1.  **Databricks** – voor het maken van een verbinding met een Databricks-cluster

        Maak een Databricks-gekoppelde service met behulp van de toegangs sleutel die is gegenereerd in vereiste 2. c. Als u een *interactief cluster*hebt, kunt u dat selecteren. (In dit voor beeld wordt de optie *nieuw taak cluster* gebruikt.)

        ![8](media/solution-template-Databricks-notebook/Databricks-tutorial-image08.png)

2.  **Gegevens sets** maken

    1.  **SourceAvailability_Dataset** maken om te controleren of de bron gegevens beschikbaar zijn

    ![9](media/solution-template-Databricks-notebook/Databricks-tutorial-image09.png)

    1.  **Bron gegevensset –** voor het kopiëren van de bron gegevens (met behulp van binaire kopie)

    ![10](media/solution-template-Databricks-notebook/Databricks-tutorial-image10.png)

    1.  **Sink-gegevensset** – voor het kopiëren naar de Sink/doel locatie

        1.  Gekoppelde service: Selecteer ' sinkBlob_LS ' die in 1 is gemaakt. b

        2.  Bestandspad-' sinkdata/staged_sink '

        ![11](media/solution-template-Databricks-notebook/Databricks-tutorial-image11.png)

## <a name="create-activities"></a>Activiteiten maken

1.  Een**beschikbaarheids markering**voor een opzoek activiteit maken voor het uitvoeren van een controle op de beschik baarheid van de bron (lookup of GetMetadata kan worden gebruikt). Selecteer ' sourceAvailability_Dataset ' gemaakt in 2. a.

    ![12](media/solution-template-Databricks-notebook/Databricks-tutorial-image12.png)

1.  Een Kopieer activiteit van het**bestand naar de BLOB**maken voor het kopiëren van de gegevensset van de bron naar de sink. In dit geval is het binaire bestand. Raadpleeg de onderstaande scherm afbeeldingen voor bron-en Sink-configuraties in de Kopieer activiteit.

    ![13](media/solution-template-Databricks-notebook/Databricks-tutorial-image13.png)

    ![14](media/solution-template-Databricks-notebook/Databricks-tutorial-image14.png)

1.  **Pijplijn parameters** definiëren

    ![15](media/solution-template-Databricks-notebook/Databricks-tutorial-image15.png)

1.  Een **Databricks-activiteit** maken

    Selecteer de gekoppelde service die u in een vorige stap hebt gemaakt.

    ![16](media/solution-template-Databricks-notebook/Databricks-tutorial-image16.png)

    Configureer de **instellingen**. Maak **Base-para meters** zoals weer gegeven in de scherm opname en maak para meters die moeten worden door gegeven aan de Databricks notebook van Data Factory. Blader en **Selecteer** het **juiste notitieblok pad** dat is geüpload in **vereiste 2**.

    ![17](media/solution-template-Databricks-notebook/Databricks-tutorial-image17.png)

1.  **Voer de pijp lijn uit**. U vindt een koppeling naar Databricks-logboeken voor meer gedetailleerde Spark-Logboeken.

    ![18](media/solution-template-Databricks-notebook/Databricks-tutorial-image18.png)

    U kunt ook het gegevens bestand controleren met behulp van Storage Explorer. (Als u wilt correleren met Data Factory pijplijn uitvoeringen, wordt in dit voor beeld de ID van de pijplijn uitvoering van data factory toegevoegd aan de uitvoermap. Op deze manier kunt u de bestanden die zijn gegenereerd via elke uitvoering, traceren.)

![19](media/solution-template-Databricks-notebook/Databricks-tutorial-image19.png)

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
