---
title: Pijplijnen bewaken en beheren met behulp van de Azure-portal en PowerShell
description: Meer informatie over het gebruik van de Azure-portal en Azure PowerShell om de Azure-gegevensfabrieken en -pijplijnen die u hebt gemaakt, te controleren en te beheren.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/30/2018
ms.openlocfilehash: 44aadecfa80524345932c03abb51e8ebd040a902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73666970"
---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>Azure Data Factory-pijplijnen bewaken en beheren met behulp van de Azure-portal en PowerShell
> [!div class="op_single_selector"]
> * [Azure-portal/Azure PowerShell gebruiken](data-factory-monitor-manage-pipelines.md)
> * [De app Monitoring en Beheer gebruiken](data-factory-monitor-manage-app.md)

> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de datafabriekservice gebruikt, raadpleegt u [De pijplijnen van Gegevensfabriek controleren en beheren in](../monitor-visually.md).

In dit artikel wordt beschreven hoe u uw pijplijnen controleren, beheren en debuggen met Azure-portal en PowerShell.

> [!IMPORTANT]
> De monitoring & beheertoepassing biedt een betere ondersteuning voor het bewaken en beheren van uw gegevenspijplijnen en het oplossen van eventuele problemen. Zie Data [Factory-pijplijnen bewaken en beheren met behulp van de app Monitoring and Management](data-factory-monitor-manage-app.md)voor meer informatie over het gebruik van de toepassing. 

> [!IMPORTANT]
> Azure Data Factory versie 1 maakt nu gebruik van de nieuwe [Azure Monitor waarschuwingsinfrastructuur](../../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). De oude alarminfrastructuur is afgeschaft. Als gevolg hiervan werken uw bestaande waarschuwingen die zijn geconfigureerd voor gegevensfabrieken van versie 1 niet meer. Uw bestaande waarschuwingen voor v1-gegevensfabrieken worden niet automatisch gemigreerd. U moet deze waarschuwingen opnieuw maken op de nieuwe waarschuwingsinfrastructuur. Meld u aan bij de Azure-portal en selecteer **Monitor** om nieuwe waarschuwingen te maken voor metrische gegevens (zoals mislukte uitvoeringen of geslaagde uitvoeringen) voor uw gegevensfabrieken in versie 1.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="understand-pipelines-and-activity-states"></a>Pijplijnen en activiteitsstatussen begrijpen
Door de Azure-portal te gebruiken, u het:

* Bekijk uw gegevensfabriek als een diagram.
* Activiteiten in een pijplijn weergeven.
* Invoer- en uitvoergegevenssets weergeven.

In deze sectie wordt ook beschreven hoe een gegevenssetsegment overgaat van de ene status naar de andere status.   

### <a name="navigate-to-your-data-factory"></a>Navigeren naar uw gegevensfabriek
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Klik op **Gegevensfabrieken** in het menu aan de linkerkant. Als u het niet ziet, klikt u op **Meer services >** en klikt u vervolgens op **Gegevensfabrieken** onder de categorie **INTELLIGENTIE + ANALYTICS.**

   ![Blader door alle > Data fabrieken](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)
3. Selecteer op het blad **gegevensfabrieken** de gegevensfabriek waarin u geïnteresseerd bent.

    ![Data factory selecteren](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   U ziet de startpagina van de gegevensfabriek.

   ![Blade Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>Diagramweergave van uw gegevensfabriek
De **diagramweergave** van een gegevensfabriek biedt één ruit om de gegevensfabriek en de bijbehorende activa te controleren en te beheren. Als u de **diagramweergave** van uw gegevensfabriek wilt bekijken, klikt u op **Diagram** op de startpagina voor de gegevensfabriek.

![Diagramweergave](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

U inzoomen, uitzoomen, inzoomen om te passen, inzoomen op 100%, de lay-out van het diagram vergrendelen en automatisch pijplijnen en gegevenssets plaatsen. U ook de gegevensregelgegevens bekijken (dat wil zeggen upstream- en downstreamartikelen van geselecteerde items weergeven).

### <a name="activities-inside-a-pipeline"></a>Activiteiten binnen een pijplijn
1. Klik met de rechtermuisknop op de pijplijn en klik vervolgens op **Pijplijn openen** om alle activiteiten in de pijplijn te bekijken, samen met invoer- en uitvoergegevenssets voor de activiteiten. Deze functie is handig wanneer uw pijplijn meer dan één activiteit bevat en u de operationele afstamming van één pijplijn wilt begrijpen.

    ![Menu Pijplijn openen](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. In het volgende voorbeeld ziet u een kopieeractiviteit in de pijplijn met een invoer en een uitvoer. 

    ![Activiteiten binnen een pijplijn](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. U terug naar de startpagina van de gegevensfabriek navigeren door op de koppeling **Gegevensfabriek** in de broodkruimel in de linkerbovenhoek te klikken.

    ![Terug naar gegevensfabriek navigeren](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>De status van elke activiteit in een pijplijn weergeven
U de huidige status van een activiteit bekijken door de status van een van de gegevenssets die door de activiteit worden geproduceerd, te bekijken.

Door te dubbelklikken op de **OutputBlobTable** in het **diagram,** u alle segmenten zien die worden geproduceerd door verschillende activiteitsruns in een pijplijn. U zien dat de kopieeractiviteit de afgelopen acht uur is uitgevoerd en de segmenten in de status **Gereed** heeft geproduceerd.  

![Staat van de pijpleiding](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

De gegevenssetsegmenten in de gegevensfabriek kunnen een van de volgende statussen hebben:

<table>
<tr>
    <th align="left">Status</th><th align="left">Substaat</th><th align="left">Beschrijving</th>
</tr>
<tr>
    <td rowspan="8">Wachten</td><td>PlanningTijd</td><td>De tijd is niet gekomen om het stuk te laten lopen.</td>
</tr>
<tr>
<td>Afhankelijkvan gegevenssetafhankelijkheden</td><td>De upstream afhankelijkheden zijn nog niet klaar.</td>
</tr>
<tr>
<td>ComputeResources</td><td>De rekenbronnen zijn niet beschikbaar.</td>
</tr>
<tr>
<td>GelijktijdigheidLimiet</td> <td>Alle activiteitsinstanties zijn bezig met het uitvoeren van andere segmenten.</td>
</tr>
<tr>
<td>ActiviteitHervat</td><td>De activiteit wordt onderbroken en kan de segmenten niet uitvoeren totdat de activiteit is hervat.</td>
</tr>
<tr>
<td>Opnieuw proberen</td><td>De uitvoering van activiteiten wordt opnieuw geprobeerd.</td>
</tr>
<tr>
<td>Validatie</td><td>Validatie is nog niet gestart.</td>
</tr>
<tr>
<td>ValidatieRetry</td><td>Validatie wacht om opnieuw te worden geprobeerd.</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>Valideren</td><td>De validatie is in volle gang.</td>
</tr>
<td>-</td>
<td>Het segment wordt verwerkt.</td>
</tr>
<tr>
<td rowspan="4">Mislukt</td><td>Time-out</td><td>De uitvoering van de activiteit duurde langer dan wat is toegestaan door de activiteit.</td>
</tr>
<tr>
<td>Geannuleerd</td><td>Het segment is geannuleerd door de actie van de gebruiker.</td>
</tr>
<tr>
<td>Validatie</td><td>Validatie is mislukt.</td>
</tr>
<tr>
<td>-</td><td>Het segment kan niet worden gegenereerd en/of gevalideerd.</td>
</tr>
<td>Gereed</td><td>-</td><td>Het segment is klaar voor consumptie.</td>
</tr>
<tr>
<td>Overgeslagen</td><td>Geen</td><td>Het segment wordt niet verwerkt.</td>
</tr>
<tr>
<td>Geen</td><td>-</td><td>Een segment bestond vroeger met een andere status, maar is opnieuw ingesteld.</td>
</tr>
</table>



U de details van een segment bekijken door op een segmentitem te klikken op het blad **Onlangs bijgewerkte segmenten.**

![Segmentdetails](./media/data-factory-monitor-manage-pipelines/slice-details.png)

Als het segment meerdere keren is uitgevoerd, ziet u meerdere rijen in de lijst **Activiteitsuitvoeringen.** U details over een activiteit weergeven die wordt uitgevoerd door op de run-vermelding in de lijst **Activiteitsuitvoeringen te** klikken. De lijst toont alle logboekbestanden, samen met een foutbericht als er een is. Deze functie is handig om logboeken te bekijken en te debuggen zonder uw gegevensfabriek te hoeven verlaten.

![Details uitvoering van activiteit](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Als het segment niet in de status **Gereed** staat staat staat is, u de upstreamsegmenten zien die nog niet klaar zijn en het huidige segment blokkeren om uit te voeren in de **lijst Upstream segmenten die niet klaar zijn.** Deze functie is handig wanneer uw segment in **de wachtstatus** staat en u de upstream-afhankelijkheden wilt begrijpen waarop het segment wacht.

![Upstream segmenten die nog niet klaar zijn](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>Diagram met gegevenssetstatus
Nadat u een gegevensfabriek hebt geïmplementeerd en de pijplijnen een geldige actieve periode hebben, worden de gegevenssetsegmenten van de ene status naar de andere segmenten. Momenteel volgt de segmentstatus het volgende statusdiagram:

![Statusdiagram](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

De overgangstroom van gegevensstatusstatus in de gegevensfabriek is de volgende: Wachten -> In-Progress/In-Progress (Valideren) -> klaar/mislukt.

Het segment begint in een **wachtstatus,** wachtend op voorwaarden waaraan moet worden voldaan voordat het wordt uitgevoerd. Vervolgens begint de activiteit uit te voeren en wordt het segment in de **status In-Progress** uitgevoerd. De activiteitsuitvoering kan slagen of mislukken. Het segment is gemarkeerd als **Gereed** of **mislukt**, op basis van het resultaat van de uitvoering.

U het segment opnieuw instellen om terug te gaan van de status **Gereed** of **Mislukt** naar de **status Wachten.** U de segmentstatus ook markeren als **Overslaan,** waardoor de activiteit het segment niet kan uitvoeren en niet verwerkt.

## <a name="pause-and-resume-pipelines"></a>Pijplijnen onderbreken en hervatten
U uw pijplijnen beheren met Azure PowerShell. U bijvoorbeeld pijplijnen onderbreken en hervatten door Azure PowerShell-cmdlets uit te voeren. 

> [!NOTE] 
> De diagramweergave biedt geen ondersteuning voor het onderbreken en hervatten van pijplijnen. Als u een gebruikersinterface wilt gebruiken, gebruikt u de toepassing monitoring en beheer. Zie Data [Factory-pijplijnen bewaken en beheren met het artikel Monitoring and Management-app](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

U pijplijnen onderbreken/onderbreken met de **cmdlet Suspend-AzDataFactoryPipeline** PowerShell. Deze cmdlet is handig als u uw pijplijnen niet wilt uitvoeren totdat een probleem is opgelost. 

```powershell
Suspend-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Bijvoorbeeld:

```powershell
Suspend-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

Nadat het probleem met de pijplijn is opgelost, u de opgeschorte pijplijn hervatten door de volgende PowerShell-opdracht uit te voeren:

```powershell
Resume-AzDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
Bijvoorbeeld:

```powershell
Resume-AzDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

## <a name="debug-pipelines"></a>Foutopsporingspijplijnen
Azure Data Factory biedt uitgebreide mogelijkheden om pijplijnen te opsporen en op te lossen met behulp van de Azure-portal en Azure PowerShell.

> [!NOTE] 
> Het is veel gemakkelijker om troubleshot fouten met behulp van de Monitoring & Management App. Zie Data [Factory-pijplijnen bewaken en beheren met het artikel Monitoring and Management-app](data-factory-monitor-manage-app.md) voor meer informatie over het gebruik van de toepassing. 

### <a name="find-errors-in-a-pipeline"></a>Fouten in een pijplijn zoeken
Als de activiteit mislukt in een pijplijn, is de gegevensset die door de pijplijn wordt geproduceerd, in een foutstatus vanwege de fout. U fouten in Azure Data Factory opsporen en oplossen met behulp van de volgende methoden.

#### <a name="use-the-azure-portal-to-debug-an-error"></a>De Azure-portal gebruiken om een fout te opsporen
1. Klik op het **tabelblad** op het probleemsegment waarop de **status** is ingesteld **op Mislukt**.

   ![Tafelblad met probleemsegment](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. Klik op het **segmentblad Gegevens** op de activiteitsrun die is mislukt.

   ![Gegevenssegment met een fout](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. Op het **paginablad Activiteitsrun** u de bestanden downloaden die zijn gekoppeld aan de HDInsight-verwerking. Klik **op Downloaden** voor status/stderr om het foutlogboekbestand met details over de fout te downloaden.

   ![Activiteit smeerdetails blad met fout](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>PowerShell gebruiken om een fout te opsporen
1. PowerShell **starten**.
2. Voer de opdracht **Get-AzDataFactorySlice uit** om de segmenten en hun statussen te bekijken. U ziet een segment met de status **mislukt**.        

    ```powershell   
    Get-AzDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   Bijvoorbeeld:

    ```powershell   
    Get-AzDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   Vervang **StartDateTime** door de begintijd van de pijplijn. 
3. Voer nu de **cmdlet Get-AzDataFactoryRun** uit om meer informatie te krijgen over de activiteit die voor het segment wordt uitgevoerd.

    ```powershell   
    Get-AzDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    Bijvoorbeeld:

    ```powershell   
    Get-AzDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    De waarde van StartDateTime is de begintijd voor het fout-/probleemsegment dat u in de vorige stap hebt opgemerkt. De datum-tijd moet worden ingesloten in dubbele aanhalingstekens.
4. U ziet uitvoer met details over de fout die vergelijkbaar is met de volgende:

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. U de **cmdlet Save-AzDataFactoryLog** uitvoeren met de id-waarde die u ziet van de uitvoer en de logbestanden downloaden met de **optie -DownloadLogs** voor de cmdlet.

    ```powershell
    Save-AzDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>Fouten in een pijplijn opnieuw uitvoeren

> [!IMPORTANT]
> Het is eenvoudiger om fouten op te lossen en mislukte segmenten opnieuw uit te voeren met behulp van de app Monitoring & Management. Zie Data [Factory-pijplijnen bewaken en beheren met behulp van de app Monitoring and Management](data-factory-monitor-manage-app.md)voor meer informatie over het gebruik van de toepassing. 

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken
Nadat u fouten in een pijplijn hebt opgelost en foutopsporing hebt opgelost, u fouten opnieuw uitvoeren door naar het foutsegment te navigeren en op de knop **Uitvoeren** op de opdrachtbalk te klikken.

![Een mislukt segment opnieuw uitvoeren](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Als het segment is mislukt als validatie vanwege een beleidsfout (bijvoorbeeld als er geen gegevens beschikbaar zijn), u de fout oplossen en opnieuw valideren door op de knop **Valideren** op de opdrachtbalk te klikken.

![Fouten oplossen en valideren](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>Azure PowerShell gebruiken
U fouten opnieuw uitvoeren met de cmdlet **Set-AzDataFactorySliceStatus.** Zie het onderwerp [Set-AzDataFactorySliceStatus](https://docs.microsoft.com/powershell/module/az.datafactory/set-azdatafactoryslicestatus) voor syntaxis en andere details over de cmdlet.

**Voorbeeld:**

In het volgende voorbeeld wordt de status van alle segmenten voor de tabel 'DAWikiAggregatedData' ingesteld op 'Wachten' in de Azure-gegevensfabriek 'WikiADF'.

De 'UpdateType' is ingesteld op 'UpstreamInPipeline', wat betekent dat statussen van elk segment voor de tabel en alle afhankelijke (upstream) tabellen zijn ingesteld op 'Wachten'. De andere mogelijke waarde voor deze parameter is 'Individueel'.

```powershell
Set-AzDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```
## <a name="create-alerts-in-the-azure-portal"></a>Waarschuwingen maken in de Azure-portal

1.  Meld u aan bij de Azure-portal en selecteer **Monitor -> waarschuwingen** om de pagina Waarschuwingen te openen.

    ![Open de pagina Waarschuwingen.](media/data-factory-monitor-manage-pipelines/v1alerts-image1.png)

2.  Selecteer **+ Nieuwe waarschuwingsregel** om een nieuwe waarschuwing te maken.

    ![Een nieuwe waarschuwing maken](media/data-factory-monitor-manage-pipelines/v1alerts-image2.png)

3.  Definieer de **voorwaarde Waarschuwing**. (Selecteer **Gegevensfabrieken** in het veld **Filteren op resourcetype.)** U ook waarden opgeven voor **Dimensies.**

    ![De waarschuwingsvoorwaarde definiëren - Doel selecteren](media/data-factory-monitor-manage-pipelines/v1alerts-image3.png)

    ![De waarschuwingsvoorwaarde definiëren - Waarschuwingscriteria toevoegen](media/data-factory-monitor-manage-pipelines/v1alerts-image4.png)

    ![De waarschuwingsvoorwaarde definiëren - Waarschuwingslogica toevoegen](media/data-factory-monitor-manage-pipelines/v1alerts-image5.png)

4.  Definieer de **details van de waarschuwing**.

    ![De waarschuwingsgegevens definiëren](media/data-factory-monitor-manage-pipelines/v1alerts-image6.png)

5.  Definieer de **actiegroep**.

    ![De actiegroep definiëren - een nieuwe actiegroep maken](media/data-factory-monitor-manage-pipelines/v1alerts-image7.png)

    ![De actiegroep definiëren - eigenschappen instellen](media/data-factory-monitor-manage-pipelines/v1alerts-image8.png)

    ![De actiegroep definiëren - nieuwe actiegroep gemaakt](media/data-factory-monitor-manage-pipelines/v1alerts-image9.png)

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>Een gegevensfabriek verplaatsen naar een andere resourcegroep of -abonnement
U een gegevensfabriek verplaatsen naar een andere brongroep of een ander abonnement met de knop **Opdrachtbalk verplaatsen** op de startpagina van uw gegevensfabriek.

![Gegevensfabriek verplaatsen](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

U ook alle gerelateerde bronnen verplaatsen (zoals waarschuwingen die zijn gekoppeld aan de gegevensfabriek), samen met de gegevensfabriek.

![Dialoogvenster Resources verplaatsen](./media/data-factory-monitor-manage-pipelines/MoveResources.png)
