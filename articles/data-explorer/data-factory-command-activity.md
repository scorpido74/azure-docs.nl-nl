---
title: Azure Data Explorer-besturings opdrachten gebruiken in Azure Data Factory
description: In dit onderwerp gebruikt u Azure Data Explorer Control commands in Azure Data Factory
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/15/2019
ms.openlocfilehash: 20da2d54ea54674656b2c1006d094c63133baf79
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264489"
---
# <a name="use-azure-data-factory-command-activity-to-run-azure-data-explorer-control-commands"></a>Azure Data Factory opdracht activiteit gebruiken om Azure Data Explorer-besturings opdrachten uit te voeren

[Azure Data Factory](/azure/data-factory/) (ADF) is een service voor gegevens integratie in de Cloud waarmee u een combi natie van activiteiten kunt uitvoeren op de gegevens. Gebruik ADF om gegevensgestuurde werk stromen te maken voor het organiseren en automatiseren van gegevens verplaatsing en gegevens transformatie. Met de activiteit **azure Data Explorer-opdracht** in azure Data Factory kunt u [Azure Data Explorer-besturings opdrachten](/azure/kusto/concepts/#control-commands) uitvoeren binnen een ADF-werk stroom. In dit artikel leert u hoe u een pijp lijn maakt met een opzoek activiteit en een ForEach-activiteit met een Azure Data Explorer-opdracht activiteit.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een Azure Data Explorer-cluster en-data base](create-cluster-database-portal.md)
* Een gegevens bron.
* [Een data factory](data-factory-load-data.md#create-a-data-factory)

## <a name="create-a-new-pipeline"></a>Een nieuwe pijp lijn maken

1. Selecteer het potlood hulp programma voor **ontwerpen** . 
1. Maak een nieuwe pijp lijn door **+** te selecteren en vervolgens **pijp lijn** te selecteren in de vervolg keuzelijst.

   ![nieuwe pijp lijn maken](media/data-factory-command-activity/create-pipeline.png)

## <a name="create-a-lookup-activity"></a>Een opzoek activiteit maken

Met een [opzoek activiteit](/azure/data-factory/control-flow-lookup-activity) kan een gegevensset worden opgehaald uit elke Azure Data Factory gegevens bronnen die worden ondersteund. De uitvoer van de opzoek activiteit kan worden gebruikt in een ForEach-of andere activiteit.

1. Selecteer in het deel venster **activiteiten** onder **Algemeen**de **opzoek** activiteit. Sleep en zet deze neer in het hoofd papier aan de rechter kant.
 
    ![opzoek activiteit selecteren](media/data-factory-command-activity/select-activity.png)

1. Het canvas bevat nu de opzoek activiteit die u hebt gemaakt. Gebruik de tabbladen onder het canvas om de relevante para meters te wijzigen. In het **Algemeen**wijzigt u de naam van de activiteit. 

    ![opzoek activiteit bewerken](media/data-factory-command-activity/edit-lookup-activity.PNG)

    > [!TIP]
    > Klik op het lege papier gebied om de pijplijn eigenschappen weer te geven. Gebruik het tabblad **Algemeen** om de naam van de pijp lijn te wijzigen. Onze pijp lijn heet *pijp lijn-4 docs*.

### <a name="create-an-azure-data-explorer-dataset-in-lookup-activity"></a>Een Azure Data Explorer-gegevensset maken in de opzoek activiteit

1. Selecteer in **instellingen**uw vooraf gemaakte Azure Data Explorer- **bron gegevensset**of selecteer **+ Nieuw** om een nieuwe gegevensset te maken.
 
    ![gegevensset toevoegen in opzoek instellingen](media/data-factory-command-activity/lookup-settings.png)

1. Selecteer de **Azure Data Explorer-gegevensset (Kusto)** in het venster **nieuwe gegevensset** . Selecteer **door gaan** om de nieuwe gegevensset toe te voegen.

   ![nieuwe gegevensset selecteren](media/data-factory-command-activity/select-new-dataset.png) 

1. De nieuwe para meters van de Azure Data Explorer-gegevensset zijn zichtbaar in **instellingen**. Selecteer **bewerken**om de para meters bij te werken.

    ![Zoek instellingen met Azure Data Explorer-gegevensset](media/data-factory-command-activity/lookup-settings-with-adx-dataset.png)

1. Het nieuwe tabblad **AzureDataExplorerTable** wordt geopend in het hoofd papier. 
    * Selecteer **Algemeen** en bewerk de naam van de gegevensset. 
    * Selecteer **verbinding** om de eigenschappen van de gegevensset te bewerken. 
    * Selecteer de **gekoppelde service** in de vervolg keuzelijst of selecteer **+ Nieuw** om een nieuwe gekoppelde service te maken.

    ![Eigenschappen van Azure Data Explorer-gegevensset bewerken](media/data-factory-command-activity/adx-dataset-properties-edit-connections.png)

1. Wanneer u een nieuwe gekoppelde service maakt, wordt de pagina **nieuwe gekoppelde service (Azure Data Explorer)** geopend:

    ![ADX nieuwe gekoppelde service](media/data-factory-command-activity/adx-new-linked-service.png)

   * Selecteer een **naam** voor de gekoppelde Azure Data Explorer-service. Voeg indien nodig een **Beschrijving** toe.
   * Wijzig de huidige instellingen in **verbinden via Integration runtime**, indien nodig. 
   * In de methode voor het selecteren van **accounts** selecteert u uw cluster op een van de volgende twee manieren: 
        * Selecteer het keuze rondje **van Azure-abonnement** en selecteer uw **Azure-abonnements** account. Selecteer vervolgens uw **cluster**. Opmerking in de vervolg keuzelijst worden alleen clusters vermeld die bij de gebruiker horen.
        * Selecteer in plaats daarvan **hand matig** keuze rondje opgeven en voer uw **eind punt** (cluster-URL) in.
    * Geef de **Tenant**op.
    * Voer de ID van de **Service-Principal**in. De principal-ID moet de juiste machtigingen hebben, afhankelijk van het machtigings niveau dat is vereist voor de opdracht die wordt gebruikt.
    * Selecteer de knop **Service Principal Key** en voer de sleutel van de **Service-Principal**in.
    * Selecteer uw **Data Base** in de vervolg keuzelijst. U kunt ook het selectie vakje **bewerken** selecteren en de naam van uw data base invoeren.
    * Selecteer **verbinding testen** om de gekoppelde service verbinding te testen die u hebt gemaakt. Als u verbinding kunt maken met uw installatie **, wordt een** groen vinkje weer gegeven.
    * Selecteer **volt ooien** om de gekoppelde service te maken.

1. Wanneer u een gekoppelde service hebt ingesteld, voegt u in **AzureDataExplorerTable** > -**verbinding**de naam van de **tabel** toe. Selecteer **Preview-gegevens**om ervoor te zorgen dat de gegevens correct worden weer gegeven.

   Uw gegevensset is nu gereed en u kunt door gaan met het bewerken van de pijp lijn.

### <a name="add-a-query-to-your-lookup-activity"></a>Een query toevoegen aan de opzoek activiteit

1. In **pijp lijn-4-docs** > **instellingen** een query toevoegen in een **query** tekstvak, bijvoorbeeld:

    ```kusto
    ClusterQueries
    | where Database !in ("KustoMonitoringPersistentDatabase", "$systemdb")
    | summarize count() by Database
    ```

1. Wijzig zo nodig de **time-out** van de query of **geen afbreking** en alleen de eigenschappen van de **eerste rij** . In deze stroom blijven we de standaard **time-out voor de querytime** en schakelt u de selectie vakjes uit. 

    ![Laatste instellingen van de opzoek activiteit](media/data-factory-command-activity/lookup-activity-final-settings.png)

## <a name="create-a-for-each-activity"></a>Een for-each-activiteit maken 

De [for-each-](/azure/data-factory/control-flow-for-each-activity) activiteit wordt gebruikt om een verzameling te herhalen en opgegeven activiteiten in een lus uit te voeren. 

1. Nu voegt u een for-each-activiteit toe aan de pijp lijn. Met deze activiteit worden de gegevens verwerkt die zijn geretourneerd door de opzoek activiteit. 
    * Selecteer in het deel venster **activiteiten** onder **iteratie & Conditions**de **foreach** -activiteit en sleep deze naar het canvas.
    * Teken een lijn tussen de uitvoer van de opzoek activiteit en de invoer van de ForEach-activiteit in het canvas om ze te verbinden.

        ![ForEach-activiteit](media/data-factory-command-activity/for-each-activity.png)

1.  Selecteer de ForEach-activiteit in het canvas. Op het tabblad **instellingen** onder:
    * Schakel het selectie vakje **opeenvolgend** in voor een opeenvolgende verwerking van de zoek resultaten of schakel deze optie uit om parallelle verwerking te maken.
    * Stel **batch-aantal**in.
    * Geef in **items**de volgende verwijzing op naar de uitvoer waarde: *@activity (' Lookup1 '). output. Value*

       ![Instellingen voor ForEach-activiteit](media/data-factory-command-activity/for-each-activity-settings.png)

## <a name="create-an-azure-data-explorer-command-activity-within-the-foreach-activity"></a>Een Azure Data Explorer-opdracht activiteit maken binnen de ForEach-activiteit

1. Dubbel klik op de ForEach-activiteit in het canvas om deze te openen in een nieuw canvas om de activiteiten in ForEach op te geven.
1. Selecteer in het deel venster **activiteiten** onder **Azure Data Explorer**de activiteit **Azure Data Explorer opdracht** en sleep deze naar het canvas.

    ![Opdracht activiteit voor Azure Data Explorer](media/data-factory-command-activity/adx-command-activity.png)

1.  Selecteer op het tabblad **verbinding** de zelfde gekoppelde service die eerder is gemaakt.

    ![tabblad activiteit verbinding van Azure Data Explorer](media/data-factory-command-activity/adx-command-activity-connection-tab.png)

1. Geef op het tabblad **opdracht** de volgende opdracht op:

    ```kusto
    .export
    async compressed
    into csv h"http://<storageName>.blob.core.windows.net/data/ClusterQueries;<storageKey>" with (
    sizeLimit=100000,
    namePrefix=export
    )
    <| ClusterQueries | where Database == "@{item().Database}"
    ```

    De **opdracht** geeft Azure Data Explorer de resultaten van een bepaalde query naar een Blob-opslag te exporteren in een gecomprimeerde indeling. Het wordt asynchroon uitgevoerd (met de async-aanpassings functie).
    De query adresseert de kolom data base van elke rij in het resultaat van de opzoek activiteit. De **time-out** van de opdracht kan ongewijzigd blijven.

    ![opdracht activiteit](media/data-factory-command-activity/command.png)   

    > [!NOTE]
    > De opdracht activiteit heeft de volgende limieten:
    > * Maximale grootte: 1 MB antwoord grootte
    > * Tijds limiet: 20 minuten (standaard), 1 uur (maximum).
    > * Indien nodig kunt u een query toevoegen aan het resultaat met behulp van [AdminThenQuery](/azure/kusto/management/index#combining-queries-and-control-commands), om de resulterende grootte/tijd te verminderen.

1.  De pijp lijn is nu klaar. U kunt teruggaan naar de hoofd weergave van de pijp lijn door op de naam van de pijp lijn te klikken.

    ![Opdracht pijplijn voor Azure Data Explorer](media/data-factory-command-activity/adx-command-pipeline.png)

1. Selecteer **fout opsporing** voordat u de pijp lijn publiceert. De voortgang van de pijp lijn kan worden bewaakt op het tabblad **uitvoer** .

    ![uitvoer van opdracht activiteiten van Azure Data Explorer](media/data-factory-command-activity/command-activity-output.png)

1. U kunt **Alles publiceren** en vervolgens een **trigger toevoegen** om de pijp lijn uit te voeren. 

## <a name="control-command-outputs"></a>Uitvoer van opdracht besturings elementen

De structuur van de uitvoer van de opdracht activiteit wordt hieronder beschreven. Deze uitvoer kan worden gebruikt door de volgende activiteit in de pijp lijn.

### <a name="returned-value-of-a-non-async-control-command"></a>Geretourneerde waarde van een niet-async-besturings opdracht

In een niet-async-opdracht is de structuur van de geretourneerde waarde vergelijkbaar met de structuur van het resultaat van de opzoek activiteit. In het veld `count` wordt het aantal geretourneerde records aangegeven. Een vast matrix veld `value` bevat een lijst met records. 

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
 
### <a name="returned-value-of-an-async-control-command"></a>Geretourneerde waarde van een async Control-opdracht

In een async-besturings opdracht pollt de activiteit de bewerkings tabel achter de schermen totdat de asynchrone bewerking is voltooid of een time-out. Daarom bevat de geretourneerde waarde het resultaat van `.show operations OperationId` voor die gegeven eigenschap **OperationId** . Controleer de waarden van **status** en **status** om te controleren of de bewerking is voltooid.

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

* Meer informatie over het [kopiëren van gegevens naar Azure Data Explorer met behulp van Azure Data Factory](data-factory-load-data.md).
* Meer informatie over [het gebruik van Azure Data Factory sjabloon voor bulksgewijs kopiëren van de Data Base naar Azure Data Explorer](data-factory-template.md).
