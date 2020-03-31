---
title: Besturingselementopdrachten van Azure Data Explorer gebruiken in Azure Data Factory
description: Gebruik in dit onderwerp azure data explorer-besturingselementopdrachten in Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72264489"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Opdrachtactiviteit Azure Data Factory gebruiken om besturingselementopdrachten van Azure Data Explorer uit te voeren

[Azure Data Factory](/azure/data-factory/) (ADF) is een cloudgebaseerde data-integratieservice waarmee u een combinatie van activiteiten op de gegevens uitvoeren. Gebruik ADF om datagestuurde workflows te maken voor het orkestreren en automatiseren van gegevensverplaatsing en gegevenstransformatie. Met de **opdrachtactiviteit Azure Data Explorer** in Azure Data Factory u [azure data explorer-besturingselementopdrachten](/azure/kusto/concepts/#control-commands) uitvoeren binnen een ADF-werkstroom. In dit artikel leert u hoe u een pijplijn maakt met een opzoekactiviteit en ForEach-activiteit die een opdrachtactiviteit van Azure Data Explorer bevat.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een Azure Data Explorer-cluster en -database](create-cluster-database-portal.md)
* Een bron van gegevens.
* [Een datafabriek](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Een nieuwe pijplijn maken

1. Selecteer het gereedschap **Potlood ontwerpen.** 
1. Maak een nieuwe **+** pijplijn door **Pijplijn** te selecteren en vervolgens te selecteren in de vervolgkeuzelijst.

   ![nieuwe pijplijn maken](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Een opzoekactiviteit maken

Een [opzoekactiviteit](/azure/data-factory/control-flow-lookup-activity) kan een gegevensset ophalen uit gegevensbronnen die door Azure Data Factory worden ondersteund. De uitvoer van opzoekactiviteit kan worden gebruikt in een ForEach of een andere activiteit.

1. Selecteer in het deelvenster **Activiteiten** onder **Algemeen**de **opzoekactiviteit.** Sleep en zet het in het hoofdcanvas aan de rechterkant.
 
    ![opzoekactiviteit selecteren](media/data-factory-command-activity/select-activity.png)

1. Het canvas bevat nu de opzoekactiviteit die u hebt gemaakt. Gebruik de tabbladen onder het canvas om relevante parameters te wijzigen. In **het algemeen,** wijzig de naam van de activiteit. 

    ![opzoekactiviteit bewerken](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Klik op het lege canvasgebied om de pijplijneigenschappen weer te geven. Gebruik het tabblad **Algemeen** om de naam van de pijplijn te wijzigen. Onze pijplijn heet *pipeline-4-docs.*

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Een Azure Data Explorer-gegevensset maken in opzoekactiviteit

1. Selecteer **in Instellingen**de gegevensset van uw vooraf gemaakte Azure Data **Explorer-bron**of selecteer **+ Nieuw** om een nieuwe gegevensset te maken.
 
    ![gegevensset toevoegen in opzoekinstellingen](media/data-factory-command-activity/lookup-settings.png)

1. Selecteer de **gegevensset Azure Data Explorer (Kusto)** in het venster **Nieuwe gegevensset.** Selecteer **Doorgaan** om de nieuwe gegevensset toe te voegen.

   ![nieuwe gegevensset selecteren](media/data-factory-command-activity/select-new-dataset.png) 

1. De nieuwe gegevenssetparameters van Azure Data Explorer zijn zichtbaar in **Instellingen**. Als u de parameters wilt bijwerken, selecteert u **Bewerken**.

    ![opzoekinstellingen met Azure Data Explorer-gegevensset](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Het nieuwe tabblad **AzureDataExplorerTable** wordt geopend in het hoofdcanvas. 
    * Selecteer **Algemeen** en bewerk de naam van de gegevensset. 
    * Selecteer **Verbinding** om de gegevensseteigenschappen te bewerken. 
    * Selecteer de **gekoppelde service** in de vervolgkeuzelijst of selecteer **+ Nieuw** om een nieuwe gekoppelde service te maken.

    ![Eigenschappen van azure Data Explorer-gegevensset bewerken](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Bij het maken van een nieuwe gekoppelde service wordt de pagina **New Linked Service (Azure Data Explorer)** geopend:

    ![ADX nieuwe gekoppelde service](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selecteer **Naam** voor gekoppelde service van Azure Data Explorer. Indien nodig **beschrijving** toevoegen.
   * Wijzig **in Connect via integratieruntime**indien nodig de huidige instellingen. 
   * Selecteer **in accountselectiemethode** uw cluster met een van de twee methoden: 
        * Selecteer de knop **Keuze uit Azure-abonnementsrondje** en selecteer uw **Azure-abonnementsaccount.** Selecteer vervolgens uw **cluster**. Let op: de vervolgkeuzelijst vermeldt alleen clusters die van de gebruiker zijn.
        * Selecteer in plaats daarvan de knop **Handmatig** invoeren en voer uw **eindpunt** (cluster-URL) in.
    * Geef de **tenant op**.
    * Voer **de hoofd-id van de service in**. De hoofd-ID moet over de juiste machtigingen beschikken, afhankelijk van het machtigingsniveau dat vereist is door de opdracht die wordt gebruikt.
    * Selecteer **de hoofdsleutelknop service** en voer de sleutel **serviceprincipal in**.
    * Selecteer uw **database** in het vervolgkeuzemenu. U ook het selectievakje **Bewerken** inschakelen en de naam van uw database invoeren.
    * Selecteer **Verbinding testen** om de gekoppelde serviceverbinding te testen die u hebt gemaakt. Als u verbinding maken met uw installatie, wordt een groene **vinkjeverbinding weergegeven.**
    * Selecteer **Voltooien** om gekoppelde servicecreatie te voltooien.

1. Zodra u een gekoppelde service hebt ingesteld, voegt u in **AzureDataExplorerTable** > **Connection** **tabelnaam** toe. Selecteer **Voorbeeldgegevens**om ervoor te zorgen dat de gegevens correct worden weergegeven.

   Uw gegevensset is nu klaar en u uw pijplijn blijven bewerken.

### <a name="add-a-query-to-your-lookup-activity"></a>Een query toevoegen aan uw opzoekactiviteit

1. Voeg in **pipeline-4-docs** > **Instellingen** een query toe in het tekstvak **Query,** bijvoorbeeld:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Wijzig indien nodig de **time-out query** of **Geen afgekapte** en **eerste rij.** In deze stroom houden we de **standaardtime-out van query's** en schakelen we de selectievakjes uit. 

    ![Definitieve instellingen voor opzoekactiviteit](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Een voor-elke activiteit maken 

De [activiteit Voor-Elke](/azure/data-factory/control-flow-for-each-activity) wordt gebruikt om een verzameling te herhalen en bepaalde activiteiten in een lus uit te voeren. 

1. Nu voegt u een voor-elke activiteit toe aan de pijplijn. Met deze activiteit worden de gegevens verwerkt die zijn geretourneerd vanuit de opzoekactiviteit. 
    * Selecteer in het deelvenster **Activiteiten** onder **Iteratie & Conditionals**de activiteit **ForEach** en sleep en zet deze neer in het canvas.
    * Teken een lijn tussen de uitvoer van de opzoekactiviteit en de invoer van de ForEach-activiteit in het canvas om deze te verbinden.

        ![Activiteit ForEach](media/data-factory-command-activity/for-each-activity.png)

1.  Selecteer de activiteit ForEach in het canvas. Ga als overzicht **van de instellingen:**
    * Schakel het selectievakje **Sequentiële** in voor een sequentiële verwerking van de opzoekresultaten of laat het onaangevinkt om parallelle verwerking te maken.
    * **Batchaantal**instellen .
    * Geef in **items**de volgende verwijzing naar de uitvoerwaarde: * @activity('Opzoeken1').output.value*

       ![Instellingen voor ForEach-activiteit](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Een opdrachtactiviteit van Azure Data Explorer maken binnen de ForEach-activiteit

1. Dubbelklik op de activiteit ForEach in het canvas om deze te openen in een nieuw canvas om de activiteiten binnen ForEach op te geven.
1. Selecteer in het deelvenster **Activiteiten** onder **Azure Data Explorer**de **opdracht-activiteit Azure Data Explorer** en sleep en zet deze neer in het canvas.

    ![Opdrachtactiviteit Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Selecteer op het tabblad **Verbinding** dezelfde gekoppelde service die eerder is gemaakt.

    ![tabblad voor opdrachtactiviteitsactiviteit azure-gegevensverkenner](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Geef op het tabblad **Opdracht** de volgende opdracht op:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    Met **de opdracht** wordt Azure Data Explorer geïnstrueerd om de resultaten van een bepaalde query te exporteren naar een blobopslag in een gecomprimeerde indeling. Het loopt asynchroon (met behulp van de async modifier).
    De query richt zich op de databasekolom van elke rij in het resultaat van de opzoekactiviteit. De **time-out van de opdracht** kan ongewijzigd blijven.

    ![opdrachtactiviteit](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > De opdrachtactiviteit heeft de volgende limieten:
    > * Groottelimiet: 1 MB responsgrootte
    > * Tijdslimiet: 20 minuten (standaard), 1 uur (maximum).
    > * Indien nodig u een query toevoegen aan het resultaat met [Behulp van AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), om de resulterende grootte/tijd te verminderen.

1.  Nu is de pijpleiding klaar. U teruggaan naar de hoofdpijplijnweergave door op de naam van de pijplijn te klikken.

    ![Opdrachtpijplijn voor Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selecteer **Foutopsporing** voordat u de pijplijn publiceert. De voortgang van de pijplijn kan worden gecontroleerd op het tabblad **Uitvoer.**

    ![uitvoer van opdrachtactiviteit Azure Data Explorer](media/data-factory-command-activity/command-activity-output.png)

1. U **Alles publiceren** en vervolgens **trigger toevoegen** om de pijplijn uit te voeren. 

## <a name="control-command-outputs"></a>Opdrachtuitvoer regelen

De structuur van de uitvoer van opdrachtactiviteit wordt hieronder beschreven. Deze uitvoer kan worden gebruikt door de volgende activiteit in de pijplijn.

### <a name="returned-value-of-a-non-async-control-command"></a>Geretourneerde waarde van een niet-async-besturingselementopdracht

In een niet-async-besturingselementopdracht is de structuur van de geretourneerde waarde vergelijkbaar met de structuur van het resultaat van de opzoekactiviteit. Het `count` veld geeft het aantal geretourneerde records aan. Een vast `value` arrayveld bevat een lijst met records. 

```json
{ 
    "count": "2", 
    "value": [ 
        { 
            "ExtentId": "1b9977fe-e6cf-4cda-84f3-4a7c61f28ecd", 
            "ExtentSize": 1214.0, 
            "CompressedSize": 520.0 
        }, 
        { 
            "ExtentId": "b897f5a3-62b0-441d-95ca-bf7a88952974", 
            "ExtentSize": 1114.0, 
            "CompressedSize": 504.0 
        } 
    ] 
} 
```
 
### <a name="returned-value-of-an-async-control-command"></a>Geretourneerde waarde van een opdracht voor asyncbesturingselementen

In een opdracht voor async-besturingselementen wordt de bewerkingstabel achter de schermen door de activiteit gepeild totdat de async-bewerking is voltooid of een tijd is uitgeschakeld. Daarom bevat de geretourneerde waarde `.show operations OperationId` het resultaat van de eigenschap **OperationId.** Controleer de waarden van **status-** en **statuseigenschappen** om de succesvolle voltooiing van de bewerking te controleren.

```json
{ 
    "count": "1", 
    "value": [ 
        { 
            "OperationId": "910deeae-dd79-44a4-a3a2-087a90d4bb42", 
            "Operation": "TableSetOrAppend", 
            "NodeId": "", 
            "StartedOn": "2019-06-23T10:12:44.0371419Z", 
            "LastUpdatedOn": "2019-06-23T10:12:46.7871468Z", 
            "Duration": "00:00:02.7500049", 
            "State": "Completed", 
            "Status": "", 
            "RootActivityId": "f7c5aaaf-197b-4593-8ba0-e864c94c3c6f", 
            "ShouldRetry": false, 
            "Database": "MyDatabase", 
            "Principal": "<some principal id>", 
            "User": "<some User id>" 
        } 
    ] 
}
``` 

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met Azure Data Factory](data-factory-load-data.md).
* Meer informatie over het gebruik van [Azure Data Factory-sjabloon voor bulkkopiëren van database naar Azure Data Explorer](data-factory-template.md).
