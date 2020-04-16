---
title: Transformatie met Azure Databricks
description: Meer informatie over het gebruik van een oplossingssjabloon om gegevens te transformeren met behulp van een Databricks-notitieblok in Azure Data Factory.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.reviewer: douglasl
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/03/2020
ms.openlocfilehash: 65b89a13637f5a4e1712995a6ac58d88b4421806
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414855"
---
# <a name="transformation-with-azure-databricks"></a>Transformatie met Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In deze zelfstudie maakt u een end-to-end pijplijn met de activiteiten **Validatie,** **Gegevens kopiëren**en **Notitieblok** in Azure Data Factory.

- **Validatie** zorgt ervoor dat uw brongegevensset klaar is voor downstreamconsumptie voordat u de kopieer- en analysetaak activeert.

- **Gegevens kopiëren** dupliceert de brongegevensset naar de sink-opslag, die is gemonteerd als DBFS in het Azure Databricks-notitieblok. Op deze manier kan de dataset direct worden verbruikt door Spark.

- **Notebook** activeert het Databricks-notitieblok waarmee de gegevensset wordt getransformeerd. Het voegt ook de gegevensset toe aan een verwerkte map of Azure SQL Data Warehouse.

Voor de eenvoud maakt de sjabloon in deze zelfstudie geen geplande trigger. U er indien nodig een toevoegen.

![Diagram van de pijplijn](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Vereisten

- Een Azure Blob-opslagaccount `sinkdata` met een container die wordt gebruikt als gootsteen.

  Noteer de naam van het opslagaccount, de naam van de container en de toegangssleutel. U hebt deze waarden later in de sjabloon nodig.

- Een Azure Databricks-werkruimte.

## <a name="import-a-notebook-for-transformation"></a>Een notitieblok importeren voor transformatie

Ga als u een **transformatienotitieblok** importeert naar uw Databricks-werkruimte:

1. Meld u aan bij uw Azure Databricks-werkruimte en selecteer **Importeren**.
       ![Menuopdracht voor het](media/solution-template-Databricks-notebook/import-notebook.png) importeren van een werkruimte Uw werkruimtepad kan afwijken van het weergegeven pad, maar onthoud het voor later.
1. Selecteer **Importeren uit: URL**. Voer in het `https://adflabstaging1.blob.core.windows.net/share/Transformations.html`tekstvak .

   ![Selecties voor het importeren van een notitieblok](media/solution-template-Databricks-notebook/import-from-url.png)

1. Laten we nu het **notitieblok Transformatie** bijwerken met uw opslagverbindingsgegevens.

   Ga in het geïmporteerde notitieblok naar **opdracht 5** zoals weergegeven in het volgende codefragment.

   - Vervang `<storage name>` `<access key>` en door uw eigen opslagverbindingsgegevens.
   - Gebruik het opslagaccount `sinkdata` bij de container.

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

1. Genereer een **Databricks access token** voor Data Factory om toegang te krijgen tot Databricks.
   1. Selecteer in uw Werkruimte Databricks het pictogram van het gebruikersprofiel rechtsboven.
   1. Selecteer **Gebruikersinstellingen**.
    ![Menuopdracht voor gebruikersinstellingen](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecteer **Nieuw token genereren** onder het tabblad **Toegangstokens.**
   1. Selecteer **Genereren**.

    ![Knop 'Genereren'](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Sla het toegangstoken op* voor later gebruik bij het maken van een Databricks-gekoppelde service. Het toegangstoken lijkt `dapi32db32cbb4w6eee18b7d87e45exxxxxx`op .

## <a name="how-to-use-this-template"></a>Deze sjabloon gebruiken

1. Ga naar de sjabloon **Transformatie met Azure Databricks** en maak nieuwe gekoppelde services voor het volgen van verbindingen.

   ![Instellingen voor verbindingen](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Bron Blob-verbinding** - om toegang te krijgen tot de brongegevens.

       Voor deze oefening u de openbare blobopslag gebruiken die de bronbestanden bevat. Verwijs naar de volgende schermafbeelding voor de configuratie. Gebruik de volgende **SAS-URL** om verbinding te maken met bronopslag (alleen-lezen toegang):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Selecties voor verificatiemethode en SAS-URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Doelblob-verbinding** - om de gekopieerde gegevens op te slaan.

       Selecteer in het **venster Nieuw gekoppelde service** de opslagblob voor sinkopslag.

       ![Opslagblob sink als een nieuwe gekoppelde service](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** - om verbinding te maken met het cluster Databricks.

        Maak een databricks-gekoppelde service met behulp van de toegangssleutel die u eerder hebt gegenereerd. U ervoor kiezen om een *interactief cluster* te selecteren als u er een hebt. In dit voorbeeld wordt de optie **Nieuw taakcluster** gebruikt.

        ![Selecties voor verbinding maken met het cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecteer **Deze sjabloon gebruiken**. Er wordt een pijplijn gemaakt.

    ![Een pijplijn maken](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Introductie en configuratie van pijplijnen

In de nieuwe pijplijn worden de meeste instellingen automatisch geconfigureerd met standaardwaarden. Bekijk de configuraties van uw pijplijn en breng de nodige wijzigingen aan.

1. Controleer in de **vlag Beschikbaarheid van** **validatieactiviteit** of `SourceAvailabilityDataset` de waarde van de **brongegevensset** is ingesteld op de waarde die u eerder hebt gemaakt.

   ![Waarde van brongegevensset](media/solution-template-Databricks-notebook/validation-settings.png)

1. Controleer in de tabbladen **Gegevensactiviteit** **kopiëren-naar-blob**de tabbladen **Bron** en **Sink.** Wijzig indien nodig de instellingen.

   - **Tabblad** ![Bron](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - **Tabblad** ![Sink van het tabblad Sink](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Bekijk en werk de paden en instellingen waar nodig in de **activiteit Notitieblokactiviteit.** **Notebook**

   **Databricks gekoppelde service** moet vooraf worden ingevuld met de waarde ![van een vorige stap, zoals wordt weergegeven: Bevolkte waarde voor de gekoppelde service Databricks](media/solution-template-Databricks-notebook/notebook-activity.png)

   Ga als u de **instellingen van het notitieblok** controleren:
  
    1. Selecteer het tabblad **Instellingen.** Controleer **bij pad notitieblok**of het standaardpad correct is. Mogelijk moet u bladeren en het juiste notitieblokpad kiezen.

       ![Notitieblokpad](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Vouw de selector **Basisparameters** uit en controleer of de parameters overeenkomen met wat in de volgende schermafbeelding wordt weergegeven. Deze parameters worden doorgegeven aan het Databricks-notitieblok van Data Factory.

       ![Basisparameters](media/solution-template-Databricks-notebook/base-parameters.png)

1. Controleren of de **pijplijnparameters** overeenkomen met wat ![wordt weergegeven in de volgende schermafbeelding: Pijplijnparameters](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Maak verbinding met uw gegevenssets.

   - **SourceAvailabilityDataset** - om te controleren of de brongegevens beschikbaar zijn.

     ![Selecties voor gekoppelde service en bestandspad voor SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** - om toegang te krijgen tot de brongegevens.

       ![Selecties voor gekoppelde service en bestandspad voor SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** - om de gegevens te kopiëren naar de locatie van de gootsteenbestemming. Gebruik de volgende waarden:

     - **Gekoppelde service** - `sinkBlob_LS`, gemaakt in een vorige stap.

     - **Bestandspad** - `sinkdata/staged_sink`.

       ![Selecties voor gekoppelde service en bestandspad voor DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecteer **Foutopsporing** om de pijplijn uit te voeren. U vindt de link naar Databricks logs voor meer gedetailleerde Spark logs.

    ![Koppeling naar Databricks-logboeken vanuit uitvoer](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    U het gegevensbestand ook verifiëren met Azure Storage Explorer.

    > [!NOTE]
    > Voor het correleren met pijplijnuitvoeringen van Gegevensfabriek voegt dit voorbeeld de pijplijnrun-id toe van de gegevensfabriek naar de uitvoermap. Dit helpt bij het bijhouden van bestanden gegenereerd door elke run.
    > ![Id voor het uitvoeren van pijplijns van een pijplijn](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
