---
title: Azure diagnostische logboeken | Microsoft Documenten
description: Klant kan logboekanalyse inschakelen voor Azure CDN.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: magattus
ms.openlocfilehash: 86696ed6715b4e43a9d02232c013eb64feb61f67
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594214"
---
# <a name="azure-diagnostic-logs"></a>Diagnostische logboeken in Azure

Met diagnostische logboeken van Azure u kernanalyses bekijken en opslaan in een of meer bestemmingen, waaronder:

 - Azure Storage-account
 - Azure Event Hubs
 - [Log Analytics-werkruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Deze functie is beschikbaar op CDN-eindpunten voor alle prijsniveaus. 

Met Azure-diagnostische logboeken u basisgebruiksstatistieken exporteren van uw CDN-eindpunt naar verschillende bronnen, zodat u ze op een aangepaste manier gebruiken. U bijvoorbeeld de volgende typen gegevens exporteren:

- Exporteer gegevens naar blobopslag, exporteer naar CSV en genereer grafieken in Excel.
- Exporteer gegevens naar gebeurtenishubs en correleer met gegevens uit andere Azure-services.
- Gegevens exporteren naar Azure Monitor-logboeken en gegevens weergeven in uw eigen Log Analytics-werkruimte

In het volgende diagram ziet u een typische CDN-kernanalyseweergave van gegevens.

![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Afbeelding 1 - CDN-kernanalyseweergave*

Zie [Diagnostische logboeken voor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)meer informatie over diagnostische logboeken .

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Logboekregistratie inschakelen met de Azure-portal

Volg deze stappen om logboekregistratie met CDN-kernanalyses mogelijk te maken:

Meld u aan bij [Azure Portal](https://portal.azure.com). Als u CDN nog niet hebt ingeschakeld voor uw werkstroom, [maakt u een Azure CDN-profiel en eindpunt](cdn-create-new-endpoint.md) voordat u verdergaat.

1. Navigeer in de Azure-portal naar **cdn-profiel.**

2. Zoek in de Azure-portal naar een CDN-profiel of selecteer er een in uw dashboard. Selecteer vervolgens het CDN-eindpunt waarvoor u diagnostische logboeken wilt inschakelen.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecteer **Diagnostische logboeken** in de sectie CONTROLE.

   De pagina **Diagnostische logboeken** wordt weergegeven.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Logboekregistratie inschakelen met Azure Storage

Voer de volgende stappen uit om een opslagaccount te gebruiken om de logboeken op te slaan:
    
1. Voer voor **Naam**een naam in voor uw diagnostische logboekinstellingen.
 
2. Selecteer **Archiveren in een opslagaccount**en selecteer **Vervolgens CoreAnalytics**. 

2. Kies **voor Retentie (dagen)** het aantal bewaardagen. Een retentie van nul dagen slaat de logs voor onbepaalde tijd op. 

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selecteer **Opslagaccount**.

    De pagina **Een opslagaccount selecteren** wordt weergegeven.

4. Selecteer een opslagaccount in de vervolgkeuzelijst en selecteer **OK**.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Nadat u klaar bent met het maken van uw diagnostische logboekinstellingen, selecteert u **Opslaan**.

### <a name="logging-with-azure-monitor"></a>Logboekregistratie met Azure-monitor

Voer de volgende stappen uit om Azure Monitor te gebruiken om de logboeken op te slaan:

1. Selecteer op de pagina **Diagnostische logboeken** de optie **Verzenden naar Logboekanalyse**. 

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Selecteer **Configureren** om Azure Monitor-logboekregistratie te configureren. 

   De pagina **Logboekanalyse-werkruimten** wordt weergegeven.

    >[!NOTE] 
    >OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Selecteer **Nieuwe werkruimte maken**.

    De **werkruimtepagina Log Analytics** wordt weergegeven.

    >[!NOTE] 
    >OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/07_Create-new.png)

4. Voer voor **de werkruimte Log Analytics**een naam van de Log Analytics-werkruimte in. De naam van de Log Analytics-werkruimte moet uniek zijn en alleen letters, cijfers en koppeltekens bevatten. spaties en underscores zijn niet toegestaan. 

5. Selecteer **voor Abonnement**een bestaand abonnement in de vervolgkeuzelijst. 

6. Maak **voor resourcegroep**een nieuwe resourcegroep of selecteer een bestaande groep.

7. Selecteer **voor Locatie**een locatie in de lijst.

8. Selecteer **Vastmaken aan het dashboard** als u de logboekconfiguratie op uw dashboard wilt opslaan. 

9. Selecteer **OK** om de configuratie te voltooien.

10. Nadat uw werkruimte is gemaakt, wordt u teruggestuurd naar de pagina **Diagnostische logboeken.** Bevestig de naam van uw nieuwe Log Analytics-werkruimte.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selecteer **CoreAnalytics**en selecteer **Opslaan**.

12. Als u de nieuwe werkruimte Log Analytics wilt weergeven, selecteert u **Core-analyses** op uw CDN-eindpuntpagina.

    ![portal - Diagnostische logboeken](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Uw Log Analytics-werkruimte is nu klaar om gegevens te registreren. Om die gegevens te kunnen gebruiken, moet u een [Azure Monitor-logboekenoplossing](#consuming-diagnostics-logs-from-a-log-analytics-workspace)gebruiken, die later in dit artikel wordt behandeld.

Zie Vertragingen in [loggegevens voor](#log-data-delays)meer informatie over vertragingen in logboekgegevens.

## <a name="enable-logging-with-powershell"></a>Logboekregistratie inschakelen met PowerShell

In het volgende voorbeeld ziet u hoe u diagnostische logboeken inschakelt via de Azure PowerShell-cmdlets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Diagnostische logboeken inschakelen in een opslagaccount

1. Log in en selecteer een abonnement:

    Connect-AzAccount 

    Select-AzureAbonnement -AbonnementId 

2. Voer de volgende opdracht in om diagnostische logboeken in een opslagaccount in te schakelen:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Voer de opdracht in om diagnostische logboeken in te schakelen in een werkruimte van Log Analytics:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnostische logboeken van Azure Storage gebruiken
In deze sectie wordt het schema van CDN-kernanalyses beschreven, hoe het binnen een Azure-opslagaccount is georganiseerd en wordt voorbeeldcode bevat om de logboeken in een CSV-bestand te downloaden.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer gebruiken
Voordat u toegang hebt tot de kernanalysegegevens van een Azure-opslagaccount, hebt u eerst een hulpprogramma nodig om toegang te krijgen tot de inhoud in een opslagaccount. Hoewel er verschillende hulpprogramma's op de markt zijn, is de Microsoft Azure Storage Explorer, die we aanbevelen. Zie Azure Storage [Explorer](https://storageexplorer.com/)als u het hulpprogramma wilt downloaden. Nadat u de software hebt gedownload en geïnstalleerd, configureert u deze om hetzelfde Azure-opslagaccount te gebruiken dat is geconfigureerd als bestemming voor de CDN Diagnostics-logboeken.

1.  **Microsoft Azure Storage Explorer openen**
2.  Het opslagaccount zoeken
3.  Het knooppunt **Blobcontainers** uitvouwen onder dit opslagaccount.
4.  Selecteer de container met de naam *insights-logs-coreanalytics*.
5.  Resultaten worden weergegeven in het rechterdeelvenster, te beginnen met het eerste niveau, als *resourceId=*. Blijf elk niveau selecteren totdat u het bestand *PT1H.json*vindt. Zie [Blob-padindeling](cdn-azure-diagnostic-logs.md#blob-path-format)voor een uitleg van het pad .
6.  Elk blob *PT1H.json-bestand* vertegenwoordigt de analyselogboeken gedurende een uur voor een specifiek CDN-eindpunt of het aangepaste domein.
7.  Het schema van de inhoud van dit JSON-bestand wordt beschreven in het sectieschema van de kernanalyselogboeken.


#### <a name="blob-path-format"></a>Blobpadindeling

Core analytics logs worden elk uur gegenereerd en de gegevens worden verzameld en opgeslagen in een enkele Azure blob als een JSON-payload. Omdat het gereedschap Opslagverkenner '/' interpreteert als een mapscheidingsteken en de hiërarchie weergeeft, wordt het pad naar de Azure-blob weergegeven alsof er een hiërarchische structuur is en de naam van de blob wordt weergegeven. De naam van de blob volgt de volgende naamgevingsconventie:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beschrijving van de velden:**

|Waarde|Beschrijving|
|-------|---------|
|Abonnements-id    |ID van het Azure-abonnement in Guid-indeling.|
|Naam resourcegroep |Naam van de resourcegroep waartoe de CDN-resources behoren.|
|Profile Name (Profielnaam) |Naam van het CDN-profiel|
|Eindpuntnaam |Naam van het CDN-eindpunt|
|Jaar|  Viercijferige voorstelling van het jaar, bijvoorbeeld 2017|
|Month| Tweecijferige weergave van het maandnummer. 01=Januari ... 12=December|
|Dag|   Tweecijferige weergave van de dag van de maand|
|PT1H.json| Actueel JSON-bestand waarin de analysegegevens worden opgeslagen|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>De kernanalysegegevens exporteren naar een CSV-bestand

Om toegang te krijgen tot kernanalyses wordt voorbeeldcode voor een tool verstrekt. Met deze tool kunnen de JSON-bestanden worden gedownload naar een platte door komma gescheiden bestandsindeling, die kan worden gebruikt om grafieken of andere aggregaties te maken.

Zo u de tool gebruiken:

1.  Ga naar de GitHub-link:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Download de code.
3.  Volg de instructies voor het compileren en configureren.
4.  Voer het gereedschap uit.
5.  Het resulterende CSV-bestand toont de analysegegevens in een eenvoudige platte hiërarchie.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Diagnostische logboeken van een Log Analytics-werkruimte gebruiken
Azure Monitor is een Azure-service die uw cloud- en on-premises omgevingen bewaakt om hun beschikbaarheid en prestaties te behouden. De service verzamelt gegevens afkomstig van resources in uw cloud- en on-premises omgevingen en van andere bewakingsprogramma's om analyse over meerdere resources aan te bieden. 

Als u Azure Monitor wilt gebruiken, moet u [logboekregistratie inschakelen](#enable-logging-with-azure-storage) voor de Azure Log Analytics-werkruimte, die eerder in dit artikel wordt besproken.

### <a name="using-the-log-analytics-workspace"></a>De werkruimte Log Analytics gebruiken

 In het volgende diagram ziet u de architectuur van de ingangen en uitgangen van de opslagplaats:

![Log Analytics-werkruimte](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Figuur 3 - Log Analytics Repository*

U de gegevens op verschillende manieren weergeven met behulp van Management Solutions. U beheeroplossingen verkrijgen via de [Azure Marketplace.](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)

U bewakingsoplossingen installeren vanuit Azure marketplace door de koppeling **Nu ophalen** onderaan elke oplossing te selecteren.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Een CDN-bewakingsoplossing voor Azure Monitor toevoegen

Volg de volgende stappen om een Azure Monitor-bewakingsoplossing toe te voegen:

1.   Meld u aan bij de Azure-portal met uw Azure-abonnement en ga naar uw dashboard.
    ![Azure-dashboard](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Selecteer **op** de pagina Nieuw onder **Marktplaats**de optie **Monitoring + beheer**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Selecteer **alles bekijken**op de pagina Controleren **+ beheer** .

    ![Alles weergeven](./media/cdn-diagnostics-log/15_See-all.png)

4. Zoek naar CDN in het zoekvak.

    ![Alles weergeven](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selecteer **Azure CDN Core Analytics**. 

    ![Alles weergeven](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Nadat u **Maken**hebt geselecteerd, wordt u gevraagd een nieuwe loganalytics-werkruimte te maken of een bestaande werkruimte te gebruiken. 

    ![Alles weergeven](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selecteer de werkruimte die u eerder hebt gemaakt. U moet dan een automatiseringsaccount toevoegen.

    ![Alles weergeven](./media/cdn-diagnostics-log/19_Add-automation.png)

8. In het volgende scherm ziet u het formulier voor automatiseringsaccount dat u moet invullen. 

    ![Alles weergeven](./media/cdn-diagnostics-log/20_Automation.png)

9. Zodra u het automatiseringsaccount hebt gemaakt, bent u klaar om uw oplossing toe te voegen. Selecteer de knop **Create** (Maken).

    ![Alles weergeven](./media/cdn-diagnostics-log/21_Ready.png)

10. Uw oplossing is nu toegevoegd aan uw werkruimte. Ga terug naar uw Azure-portaldashboard.

    ![Alles weergeven](./media/cdn-diagnostics-log/22_Dashboard.png)

    Selecteer de werkruimte Log Analytics die u hebt gemaakt om naar uw werkruimte te gaan. 

11. Selecteer de **tegel OMS-portal** om uw nieuwe oplossing te zien.

    ![Alles weergeven](./media/cdn-diagnostics-log/23_workspace.png)

12. Uw portal moet er nu uitzien als het volgende scherm:

    ![Alles weergeven](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selecteer een van de tegels om verschillende weergaven in uw gegevens te bekijken.

    ![Alles weergeven](./media/cdn-diagnostics-log/25_Interior-view.png)

    U naar links of rechts schuiven om verdere tegels te zien die afzonderlijke weergaven in de gegevens weergeven. 

    Selecteer een van de tegels om meer details over uw gegevens te bekijken.

     ![Alles weergeven](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Aanbiedingen en prijsniveaus

U [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)aanbiedingen en prijsniveaus voor beheeroplossingen bekijken.

### <a name="customizing-views"></a>Weergaven aanpassen

U de weergave aanpassen aan uw gegevens met behulp van de **View Designer**. Als u wilt beginnen met ontwerpen, gaat u naar de werkruimte Log Analytics en selecteert u de tegel **Ontwerper weergeven.**

![Designer weergeven](./media/cdn-diagnostics-log/27_Designer.png)

Sleep de typen grafieken en vul de gegevens in die u wilt analyseren.

![Designer weergeven](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Vertragingen in logboekgegevens

In de volgende tabel ziet u vertragingen in logboekgegevens voor **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Akamai**en **Azure CDN Standard/Premium van Verizon**.

Microsoft log gegevens vertragingen | Verizon log gegevens vertragingen | Akamai log gegevens vertragingen
--- | --- | ---
Vertraagd met 1 uur. | Vertraagd met 1 uur en kan tot 2 uur duren om te beginnen verschijnen na eindpunt voortplanting voltooiing. | 24 uur vertraagd; als het meer dan 24 uur geleden is gemaakt, duurt het maximaal 2 uur om te verschijnen. Als het onlangs is gemaakt, kan het tot 25 uur duren voordat de logboeken worden weergegeven.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostische logboektypen voor CDN-kernanalyses

Microsoft biedt momenteel alleen kernanalyselogboeken aan, die statistieken bevatten met HTTP-responsstatistieken en egress-statistieken zoals te zien is in de CDN-POP's/-randen.

### <a name="core-analytics-metrics-details"></a>Details van de belangrijkste analysestatistieken
In de volgende tabel ziet u een lijst met statistieken die beschikbaar zijn in de kernanalyselogboeken voor **Azure CDN Standard van Microsoft,** **Azure CDN Standard van Akamai**en **Azure CDN Standard/Premium van Verizon**. Niet alle statistieken zijn beschikbaar bij alle providers, hoewel dergelijke verschillen minimaal zijn. In de tabel wordt ook weergegeven of een bepaalde statistiek beschikbaar is bij een provider. De statistieken zijn alleen beschikbaar voor cdn-eindpunten waarop verkeer is weergegeven.


|Gegevens                     | Beschrijving | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| Aantal aanvragentotaal         | Totaal aantal aanvragen in deze periode. | Ja | Ja |Ja |
| Aantal aanvragenhttpStatus2xx | Aantal aanvragen die hebben geresulteerd in een 2xx HTTP-code (bijvoorbeeld 200, 202). | Ja | Ja |Ja |
| Aantal aanvragenhttpStatus3xx | Aantal aanvragen die resulteerden in een 3xx HTTP-code (bijvoorbeeld 300, 302). | Ja | Ja |Ja |
| Aantal aanvragenhttpStatus4xx | Aantal aanvragen die resulteerden in een 4xx HTTP-code (bijvoorbeeld 400, 404). | Ja | Ja |Ja |
| Aantal aanvragenhttpStatus5xx | Aantal aanvragen die resulteerden in een 5xx HTTP-code (bijvoorbeeld 500, 504). | Ja | Ja |Ja |
| Aantal aanvragenhttpstatusoverige | Aantal andere HTTP-codes (buiten 2xx-5xx). | Ja | Ja |Ja |
| Aantal aanvragenhttpstatus200 | Aantal aanvragen die hebben geresulteerd in een reactie van 200 HTTP-code. | Ja | Nee  |Ja |
| Aantal aanvragenhttpstatus206 | Aantal aanvragen die hebben geresulteerd in een reactie van 206 HTTP-code. | Ja | Nee  |Ja |
| Aantal aanvragenhttpstatus302 | Aantal aanvragen die hebben geresulteerd in een reactie van 302 HTTP-code. | Ja | Nee  |Ja |
| Aantal aanvragenhttpstatus304 | Aantal aanvragen die hebben geresulteerd in een reactie van 304 HTTP-code. | Ja | Nee  |Ja |
| Aantal aanvragenhttpstatus404 | Aantal aanvragen die hebben geresulteerd in een reactie van 404 HTTP-code. | Ja | Nee  |Ja |
| RequestCountCacheHit | Aantal aanvragen die resulteerden in een cachehit. Het actief werd rechtstreeks van de POP aan de klant geserveerd. | Ja | Ja | Nee  |
| AanvraagCountCacheMiss | Aantal aanvragen waardoor een cachemis is ontstaan. Een cachemiss betekent dat het element niet is gevonden op de POP die het dichtst bij de client staat en daarom is opgehaald uit de Origin. | Ja | Ja | Nee |
| RequestCountCacheNoCache | Aantal aanvragen voor een asset die niet in de cache kan worden opgeslagen vanwege een gebruikersconfiguratie aan de rand. | Ja | Ja | Nee |
| RequestCountCacheUncacheable | Aantal aanvragen voor assets die niet in de cache kunnen worden opgeslagen door de cachebeheer- en verloopt-headers van het asset, die aangeven dat het niet in de cache mag worden opgeslagen op een POP of door de HTTP-client. | Ja | Ja | Nee |
| RequestCountCacheOthers | Aantal aanvragen met cachestatus die hierboven niet onder de bovenstaande status vallen. | Nee | Ja | Nee  |
| UitgangTotaal | Uitgaande gegevensoverdracht in GB | Ja |Ja |Ja |
| UitganghttpStatus2xx | Uitgaande gegevensoverdracht* voor reacties met 2xx HTTP-statuscodes in GB. | Ja | Ja | Nee  |
| UitganghttpStatus3xx | Uitgaande gegevensoverdracht voor reacties met 3xx HTTP-statuscodes in GB. | Ja | Ja | Nee  |
| UitganghttpStatus4xx | Uitgaande gegevensoverdracht voor reacties met 4xx HTTP-statuscodes in GB. | Ja | Ja | Nee  |
| UitganghttpStatus5xx | Uitgaande gegevensoverdracht voor reacties met 5xx HTTP-statuscodes in GB. | Ja | Ja | Nee |
| EgresshttpStatusOthers | Uitgaande gegevensoverdracht voor reacties met andere HTTP-statuscodes in GB. | Ja | Ja | Nee  |
| EgressCacheHit | Uitgaande gegevensoverdracht voor reacties die rechtstreeks vanuit de CDN-cache op de CDN-POP's/Randen zijn geleverd. | Ja | Ja | Nee |
| EgressCacheMiss. | Uitgaande gegevensoverdracht voor reacties die niet op de dichtstbijzijnde POP-server zijn gevonden en die zijn opgehaald van de oorspronkelijke server. | Ja | Ja | Nee |
| EgressCacheNoCache | Uitgaande gegevensoverdracht voor assets die niet in de cache kunnen worden opgeslagen vanwege een gebruikersconfiguratie aan de rand. | Ja | Ja | Nee |
| EgressCacheUncacheable | Uitgaande gegevensoverdracht voor assets die niet in de cache kunnen worden opgeslagen door de cachebeheer- en/of verloopt-headers van het asset. Geeft aan dat het niet in de cache mag worden opgeslagen op een POP of door de HTTP-client. | Ja | Ja | Nee |
| EgressCacheOthers | Uitgaande gegevensoverdrachten voor andere cachescenario's. | Nee | Ja | Nee |

*Uitgaande gegevensoverdracht verwijst naar verkeer dat van CDN POP-servers naar de client wordt geleverd.


### <a name="schema-of-the-core-analytics-logs"></a>Schema van de kernanalyselogboeken 

Alle logboeken worden opgeslagen in JSON-indeling en elk item heeft tekenreeksvelden volgens het volgende schema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Wanneer *de tijd* de begintijd weergeeft van de uurgrens waarvoor de statistieken worden gerapporteerd. Wanneer een statistiek niet wordt ondersteund door een CDN-provider, in plaats van een dubbele of gehele waarde, is er een null-waarde. Deze null-waarde geeft de afwezigheid van een statistiek aan en verschilt van een waarde van 0. Er is één set van deze statistieken per domein geconfigureerd op het eindpunt.

Voorbeeldeigenschappen:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Aanvullende bronnen

* [Azure Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Kernanalyses via aanvullende azure-portal voor CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







