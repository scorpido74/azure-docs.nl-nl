---
title: Onbewerkte HTTP-logboeken Azure CDN
description: In dit artikel worden de Azure CDN RAW-Logboeken beschreven.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/22/2020
ms.author: allensu
ms.openlocfilehash: 3b36e528a013403a2ed664d3011338d92f37a3db
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87040154"
---
# <a name="azure-cdn-http-raw-logs"></a>Onbewerkte HTTP-logboeken Azure CDN
Onbewerkte logboeken bieden uitgebreide informatie over bewerkingen en fouten die belang rijk zijn voor controle en probleem oplossing. Onbewerkte logboeken verschillen van activiteiten Logboeken. Met activiteiten logboeken krijgt u inzicht in de bewerkingen die worden uitgevoerd op Azure-resources. Onbewerkte logboeken bieden een overzicht van de bewerkingen van uw resource. Onbewerkte logboeken bevatten uitgebreide informatie over elke aanvraag die door CDN wordt ontvangen. 

> [!IMPORTANT]
> De functie voor onbewerkte HTTP-Logboeken is beschikbaar voor Azure CDN van micro soft.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint. 

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Configuratie-Azure Portal

RAW-logboeken voor uw Azure CDN van het micro soft-profiel configureren: 

1. Selecteer **alle resources**in het menu Azure Portal  >>  **\<your-CDN-profile>** .

2. Selecteer onder **Bewaking** de optie **Diagnostische instellingen**.

3. Selecteer **+ Diagnostische instelling toevoegen**.

    ![Instelling voor CDN-diagnose](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Onbewerkte logboeken zijn alleen beschikbaar in het profiel niveau, terwijl geaggregeerde HTTP-status code logboeken beschikbaar zijn op het niveau van het eind punt.

4. Voer onder **Diagnostische instellingen**een naam in voor de diagnostische instelling onder **naam van diagnostische instellingen**.

5. Selecteer het **logboek** en stel de Bewaar periode in dagen in.

6. Selecteer de **doel gegevens**. Bestemmings opties zijn:
    * **Verzenden naar Log Analytics**
        * Selecteer het **abonnement** en de **log Analytics-werk ruimte**.
    * **Archiveren naar een opslag account**
        * Selecteer het **abonnement** en het **opslag account**.
    * **Streamen naar een Event Hub**
        * Selecteer het **abonnement**, de **Event hub-naam ruimte**, de naam van de **Event hub (optioneel)** en de naam van het **Event hub-beleid**.

    ![Instelling voor CDN-diagnose](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecteer **Opslaan**.

## <a name="configuration---azure-powershell"></a>Configuratie-Azure PowerShell

Gebruik [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest) om de diagnostische instelling voor onbewerkte logboeken te configureren.

Bewaar gegevens worden gedefinieerd door de optie **-RetentionInDays** in de opdracht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Diagnostische logboeken inschakelen in een opslag account

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Voer de volgende opdrachten in om Diagnostische logboeken in te scha kelen in een opslag account. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Diagnostische logboeken voor Log Analytics werk ruimte inschakelen

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Log Analytics-werk ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Diagnostische logboeken inschakelen voor Event Hub naam ruimte

1. Meld u aan bij Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Als u Diagnostische logboeken wilt inschakelen voor een Event Hub naam ruimte, voert u deze opdrachten in. Vervang de variabelen door de waarden:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Eigenschappen van onbewerkte logboeken

Azure CDN van de micro soft-service biedt momenteel onbewerkte Logboeken. RAW-logboeken bieden afzonderlijke API-aanvragen voor elke vermelding met het volgende schema: 

| Eigenschap              | Beschrijving                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | De unieke verwijzings reeks die een aanvraag identificeert die wordt geleverd door de voor deur, ook verzonden als X-Azure-ref-header naar de client. Vereist voor het zoeken naar details in de logboeken van de toegang voor een specifieke aanvraag. |
| HttpMethod            | De HTTP-methode die door de aanvraag wordt gebruikt.                                                                                                                                                                     |
| HttpVersion           | Het type van de aanvraag of verbinding.                                                                                                                                                                   |
| RequestUri            | De URI van de ontvangen aanvraag.                                                                                                                                                                         |
| RequestBytes          | De grootte van het HTTP-aanvraag bericht in bytes, met inbegrip van de aanvraag headers en de hoofd tekst van de aanvraag.                                                                                                   |
| ResponseBytes         | Bytes dat als reactie door de back-endserver wordt verzonden.                                                                                                                                                    |
| User agent             | Het browser type dat door de client wordt gebruikt.                                                                                                                                                               |
| ClientIp              | Het IP-adres van de client die de aanvraag heeft ingediend.                                                                                                                                                  |
| TimeTaken             | De duur van de actie in milliseconden.                                                                                                                                            |
| Exemplaar      | De TLS/SSL-protocol versie die wordt gebruikt door de aanvraag of null als er geen versleuteling is.                                                                                                                           |
| Eindpunt              | De CDN-eindpunt-host is geconfigureerd onder het bovenliggende CDN-profiel.                                                                                                                                   |
| Hostnaam van back-end     | De naam van de back-end-host of de oorsprong waar aanvragen worden verzonden.                                                                                                                                |
| Verzonden naar het oorspronkelijke schild </br> (afgeschaft) * **Zie opmerking bij afschaffing hieronder.** | Als dit het geval is, betekent dit dat de aanvraag is beantwoord van de oorspronkelijke schild in de cache in plaats van de Edge-pop. Schild van oorsprong is een bovenliggende cache die wordt gebruikt om de verhouding van de cache treffers te verbeteren.                                       |
| isReceivedFromClient | Als deze waarde True is, betekent dit dat de aanvraag afkomstig is van de client. Als de waarde False is, is de aanvraag een Missing in de rand (Child POP) en wordt gereageerd vanaf het afschermen van de oorsprong (bovenliggende POP). 
| Http status code        | De HTTP-status code die is geretourneerd door de proxy.                                                                                                                                                        |
| HttpStatusDetails     | De resulterende status van de aanvraag. De betekenis van deze teken reeks waarde is te vinden in een status verwijzings tabel.                                                                                              |
| Keuzemenu                   | De rand pop, die op de aanvraag van de gebruiker reageerde. De afkortingen van Pop's zijn luchthaven codes van hun respectieve metro lijnen.                                                                                   |
| Cache status          | Geeft aan of het object is geretourneerd uit de cache of afkomstig is van de oorsprong.                                                                                                             |
> [!NOTE]
> De logboeken kunnen worden weer gegeven onder uw Log Analytics-profiel door een query uit te voeren. Een voorbeeld query zou er als volgt uitzien als AzureDiagnostics | Where Category = = "AzureCdnAccessLog"


### <a name="sent-to-origin-shield-deprecation"></a>Verzonden naar afkomst van oorsprong afschermen
De onbewerkte logboek eigenschap **isSentToOriginShield** is afgeschaft en vervangen door een nieuw veld **isReceivedFromClient**. Gebruik het nieuwe veld als u het afgeschafte veld al gebruikt. 

Onbewerkte logboeken bevatten logboeken die zijn gegenereerd op basis van CDN Edge (onderliggende POP) en Afscherm van oorsprong. Schild van oorsprong verwijst naar bovenliggende knoop punten die zich wereld wijd bevinden. Deze knoop punten communiceren met de oorspronkelijke servers en verminderen de belasting van het verkeer op de oorsprong. 

Voor elke aanvraag die naar de oorsprong van het schild gaat, zijn er twee logboek vermeldingen:

* Eén voor Edge-knoop punten
* Eén voor het afschermen van oorsprong. 

U kunt het veld isReceivedFromClient gebruiken om de juiste gegevens op te halen om de uitkomsten of reacties te onderscheiden van de Edge-knoop punten versus het oorsprongs schild. 

Als de waarde ONWAAR is, betekent dit dat de aanvraag wordt gereageerd van het afschermen van de oorsprong van het Schild-knoop punt. Deze aanpak is van kracht om onbewerkte logboeken te vergelijken met facturerings gegevens. Er worden geen kosten in rekening gebracht voor het afbreken van de oorsprong van het schild naar de Edge-knoop punten. Er worden kosten in rekening gebracht voor het uitschrijven van de Edge-knoop punten naar clients. 

**Kusto-query voorbeeld voor het uitsluiten van logboeken die zijn gegenereerd op basis van een schild in Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> De functie voor onbewerkte HTTP-Logboeken is automatisch beschikbaar voor profielen die na **25 februari 2020**zijn gemaakt of bijgewerkt. Voor CDN-profielen die u eerder hebt gemaakt, moet het CDN-eind punt bij het instellen van de logboek registratie worden bijgewerkt. Een voor beeld: er kan worden genavigeerd naar geo-filtering onder CDN-eind punten en alle landen/regio's blok keren die niet relevant zijn voor de werk belasting en op Opslaan drukken. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u HTTP RAW-logboeken ingeschakeld voor de micro soft CDN-service.

Zie voor meer informatie over Azure CDN en de andere Azure-Services die in dit artikel worden genoemd:

* [Analyseren](cdn-log-analysis.md) Gebruiks patronen Azure CDN.

* Meer informatie over [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* [Log Analytics configureren in azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
