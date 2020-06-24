---
title: Diagnostische logboeken van Azure | Microsoft Docs
description: De klant kan logboek analyse voor Azure CDN inschakelen.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 2c432b28250dca382f69a992de73d633b5ea45b8
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84883988"
---
# <a name="azure-diagnostic-logs"></a>Diagnostische logboeken in Azure

Met Diagnostische logboeken van Azure kunt u kern analyses bekijken en deze opslaan in een of meer bestemmingen, waaronder:

 - Azure Storage-account
 - Azure Event Hubs
 - [Log Analytics werk ruimte](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Deze functie is beschikbaar voor CDN-eind punten voor alle prijs categorieën. 

Met de diagnostische logboeken van Azure kunt u elementaire metrische gegevens over het gebruik van uw CDN-eind punt naar een groot aantal bronnen exporteren, zodat u ze op een aangepaste manier kunt gebruiken. U kunt bijvoorbeeld de volgende typen gegevens export uitvoeren:

- Gegevens exporteren naar Blob Storage, exporteren naar CSV en grafieken genereren in Excel.
- Gegevens exporteren naar Event Hubs en correleren met gegevens van andere Azure-Services.
- Gegevens exporteren naar Azure Monitor logboeken en gegevens weer geven in uw eigen Log Analytics werk ruimte

Het volgende diagram toont een typisch voor beeld van een CDN-kern analyse van gegevens.

![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Afbeelding 1-weer gave van CDN-kern analyse*

Zie [Diagnostische logboeken](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)voor meer informatie over Diagnostische logboeken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Logboekregistratie inschakelen met de Azure-portal

Volg deze stappen om logboek registratie in te scha kelen met CDN core Analytics:

Meld u aan bij de [Azure-portal](https://portal.azure.com). Als u CDN nog niet hebt ingeschakeld voor uw werk stroom, [maakt u een Azure CDN profiel en een eind punt](cdn-create-new-endpoint.md) voordat u doorgaat.

1. Navigeer in het Azure Portal naar het **CDN-profiel**.

2. Zoek in het Azure Portal naar een CDN-profiel of selecteer er een in het dash board. Selecteer vervolgens het CDN-eind punt waarvoor u Diagnostische logboeken wilt inschakelen.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. Selecteer **Diagnostische logboeken** in het gedeelte bewaking.

   De pagina **Diagnostische logboeken** wordt weer gegeven.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Logboek registratie inschakelen met Azure Storage

Als u een opslag account wilt gebruiken om de logboeken op te slaan, voert u de volgende stappen uit:
    
1. Voer bij **naam**een naam in voor de instellingen van het diagnostische logboek.
 
2. Selecteer **archiveren naar een opslag account**en selecteer vervolgens **CoreAnalytics**. 

2. Kies voor **retentie (dagen)** het aantal Bewaar dagen. Een Bewaar periode van nul dagen slaat de logboeken voor onbepaalde tijd op. 

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Selecteer een **opslag account**.

    De pagina **een opslag account selecteren** wordt weer gegeven.

4. Selecteer een opslag account in de vervolg keuzelijst en selecteer vervolgens **OK**.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Wanneer u klaar bent met het vastleggen van de diagnostische logboek instellingen, selecteert u **Opslaan**.

### <a name="logging-with-azure-monitor"></a>Registreren met Azure Monitor

Volg deze stappen om Azure Monitor te gebruiken om de logboeken op te slaan:

1. Selecteer op de pagina **Diagnostische logboeken** de optie **verzenden naar log Analytics**. 

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Selecteer **configureren** om Azure monitor logboek registratie te configureren. 

   De pagina **log Analytics-werk ruimten** wordt weer gegeven.

    >[!NOTE] 
    >OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Selecteer **nieuwe werk ruimte maken**.

    De pagina **log Analytics werk ruimte** wordt weer gegeven.

    >[!NOTE] 
    >OMS-werkruimten worden nu aangeduid als Log Analytics-werkruimten.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/07_Create-new.png)

4. Voer de naam van een Log Analytics werk ruimte in bij **log Analytics werk ruimte**. De naam van de Log Analytics werkruimte moet uniek zijn en mag alleen letters, cijfers en afbreek streepjes bevatten. spaties en onderstrepings tekens zijn niet toegestaan. 

5. Selecteer bij **abonnement**een bestaand abonnement in de vervolg keuzelijst. 

6. Geef voor **Resourcegroep** een nieuwe resourcegroep op of selecteer een bestaande.

7. Selecteer voor **locatie**een locatie in de lijst.

8. Selecteer **vastmaken aan dash board** als u de logboek configuratie wilt opslaan op uw dash board. 

9. Selecteer **OK** om de configuratie te volt ooien.

10. Nadat de werk ruimte is gemaakt, keert u terug naar de pagina **Diagnostische logboeken** . Bevestig de naam van de nieuwe Log Analytics-werk ruimte.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Selecteer **CoreAnalytics**en selecteer vervolgens **Opslaan**.

12. Als u de nieuwe Log Analytics-werk ruimte wilt weer geven, selecteert u **kern analyse** op de pagina van uw CDN-eind punt.

    ![Portal: Diagnostische logboeken](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Uw Log Analytics-werk ruimte is nu gereed om gegevens te registreren. Als u deze gegevens wilt gebruiken, moet u een [oplossing voor Azure monitor logboeken](#consuming-diagnostics-logs-from-a-log-analytics-workspace)hebben, die verderop in dit artikel wordt besproken.

Zie [logboek gegevens vertragingen](#log-data-delays)voor meer informatie over vertragingen bij logboek gegevens.

## <a name="enable-logging-with-powershell"></a>Logboekregistratie inschakelen met PowerShell

In het volgende voor beeld ziet u hoe u Diagnostische logboeken inschakelt via de Azure PowerShell-cmdlets.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Diagnostische logboeken inschakelen in een opslag account

1. Meld u aan en selecteer een abonnement:

    Connect-AzAccount 

    Select-abonnement-SubscriptionId 

2. Als u Diagnostische logboeken in een opslag account wilt inschakelen, voert u de volgende opdracht in:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Voer de volgende opdracht in om Diagnostische logboeken in te scha kelen in een Log Analytics-werk ruimte:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Diagnostische logboeken van Azure Storage gebruiken
In deze sectie wordt het schema van de CDN-kern analyse beschreven, de manier waarop deze zijn georganiseerd in een Azure-opslag account en voorbeeld code voor het downloaden van de logboeken in een CSV-bestand.

### <a name="using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer gebruiken
Voordat u toegang kunt krijgen tot de kern gegevens van een Azure-opslag account, hebt u eerst een hulp programma nodig om toegang te krijgen tot de inhoud in een opslag account. Hoewel er verschillende hulpprogram ma's op de markt beschikbaar zijn, is de Microsoft Azure Storage Explorer. Zie [Azure Storage Explorer](https://storageexplorer.com/)als u het hulp programma wilt downloaden. Nadat u de software hebt gedownload en geïnstalleerd, configureert u deze voor gebruik van hetzelfde Azure-opslag account dat is geconfigureerd als een bestemming voor de CDN Diagnostische logboeken.

1.  **Microsoft Azure Storage Explorer** openen
2.  Zoek het opslag account
3.  Vouw het knoop punt **BLOB containers** onder dit opslag account uit.
4.  Selecteer de container met de naam *Insights-logs-coreanalytics*.
5.  Resultaten worden weer gegeven in het rechterdeel venster, te beginnen met het eerste niveau, als *resourceId =*. Ga door met het selecteren van elk niveau totdat u het bestand hebt gevonden *PT1H.js*. Zie [indeling van BLOB-pad](cdn-azure-diagnostic-logs.md#blob-path-format)voor een uitleg van het pad.
6.  Elke BLOB- *PT1H.jsin* het bestand vertegenwoordigt de analyse logboeken gedurende één uur voor een specifiek CDN-eind punt of het aangepaste domein.
7.  Het schema van de inhoud van dit JSON-bestand wordt beschreven in het sectie schema van de logboeken van de kern analyse.


#### <a name="blob-path-format"></a>Indeling van BLOB-pad

Kern analyse logboeken worden elk uur gegenereerd en de gegevens worden verzameld en opgeslagen in één Azure-Blob als JSON-nettolading. Omdat het hulp programma opslag Verkenner '/' interpreteert als een mappen scheidings teken en de hiërarchie ziet, wordt het pad naar de Azure-Blob weer gegeven alsof er sprake is van een hiërarchische structuur en de naam van de BLOB vertegenwoordigt. De naam van de BLOB volgt de volgende naam Conventie:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Beschrijving van velden:**

|Waarde|Beschrijving|
|-------|---------|
|Abonnements-id    |ID van het Azure-abonnement in GUID-indeling.|
|Naam van resource groep |De naam van de resource groep waarvan de CDN-bronnen deel uitmaken.|
|Profile Name (Profielnaam) |Naam van het CDN-profiel|
|Naam van eind punt |Naam van het CDN-eind punt|
|Jaar|  Weer gave van vier cijfers van het jaar, bijvoorbeeld 2017|
|Maand| Weer gave van twee cijfers van het maand nummer. 01 = januari... 12 = december|
|Dag|   Weer gave van twee cijfers van de dag van de maand|
|PT1H.jsop| Het daad werkelijke JSON-bestand waarin de Analytics-gegevens worden opgeslagen|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>De kern analyse gegevens exporteren naar een CSV-bestand

Om toegang te krijgen tot kern analyse, wordt voorbeeld code voor een hulp programma gegeven. Met dit hulp programma kunt u de JSON-bestanden downloaden naar een door komma's gescheiden bestands indeling, die kan worden gebruikt voor het maken van grafieken of andere aggregaties.

U kunt het hulp programma als volgt gebruiken:

1.  Ga naar de GitHub-koppeling:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Down load de code.
3.  Volg de instructies voor het compileren en configureren.
4.  Voer het hulp programma uit.
5.  In het resulterende CSV-bestand worden de analyse gegevens weer gegeven in een eenvoudige, vlakke hiërarchie.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Diagnostische logboeken van een Log Analytics-werkruimte gebruiken
Azure Monitor is een Azure-service die uw Cloud-en on-premises omgevingen bewaakt om hun Beschik baarheid en prestaties te behouden. De service verzamelt gegevens afkomstig van resources in uw cloud- en on-premises omgevingen en van andere bewakingsprogramma's om analyse over meerdere resources aan te bieden. 

Als u Azure Monitor wilt gebruiken, moet u [logboek registratie inschakelen](#enable-logging-with-azure-storage) voor de Azure log Analytics-werk ruimte, die eerder in dit artikel wordt besproken.

### <a name="using-the-log-analytics-workspace"></a>De Log Analytics-werk ruimte gebruiken

 In het volgende diagram ziet u de architectuur van de invoer en uitvoer van de opslag plaats:

![Log Analytics-werkruimte](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Afbeelding 3-Log Analytics opslag plaats*

U kunt de gegevens op verschillende manieren weer geven met behulp van beheer oplossingen. U kunt beheer oplossingen verkrijgen via [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

U kunt bewakings oplossingen van Azure Marketplace installeren door de koppeling **nu** aan de onderkant van elke oplossing te selecteren.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Een Azure Monitor CDN-bewakings oplossing toevoegen

Volg deze stappen om een Azure Monitor bewakings oplossing toe te voegen:

1.   Meld u aan bij de Azure Portal met uw Azure-abonnement en ga naar uw dash board.
    ![Azure-dashboard](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Selecteer op de pagina **Nieuw** onder **Marketplace**de optie **bewaking en beheer**.

    ![Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Selecteer op de pagina **bewaking en beheer** de optie **alles weer geven**.

    ![Alles weergeven](./media/cdn-diagnostics-log/15_See-all.png)

4. Zoek in het zoekvak naar CDN.

    ![Alles weergeven](./media/cdn-diagnostics-log/16_Search-for.png)

5. Selecteer **Azure CDN kern analyse**. 

    ![Alles weergeven](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Nadat u **maken**hebt geselecteerd, wordt u gevraagd een nieuwe log Analytics-werk ruimte te maken of een bestaande te gebruiken. 

    ![Alles weergeven](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Selecteer de werk ruimte die u eerder hebt gemaakt. Vervolgens moet u een Automation-account toevoegen.

    ![Alles weergeven](./media/cdn-diagnostics-log/19_Add-automation.png)

8. In het volgende scherm ziet u het formulier voor het Automation-account dat u moet invullen. 

    ![Alles weergeven](./media/cdn-diagnostics-log/20_Automation.png)

9. Zodra u het Automation-account hebt gemaakt, bent u klaar om uw oplossing toe te voegen. Selecteer de knop **Create** (Maken).

    ![Alles weergeven](./media/cdn-diagnostics-log/21_Ready.png)

10. Uw oplossing is nu toegevoegd aan uw werk ruimte. Ga terug naar het dash board van Azure Portal.

    ![Alles weergeven](./media/cdn-diagnostics-log/22_Dashboard.png)

    Selecteer de Log Analytics werk ruimte die u hebt gemaakt om naar uw werk ruimte te gaan. 

11. Selecteer de tegel **OMS-Portal** om uw nieuwe oplossing weer te geven.

    ![Alles weergeven](./media/cdn-diagnostics-log/23_workspace.png)

12. Uw portal moet er nu uitzien als in het volgende scherm:

    ![Alles weergeven](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Selecteer een van de tegels om verschillende weer gaven in uw gegevens weer te geven.

    ![Alles weergeven](./media/cdn-diagnostics-log/25_Interior-view.png)

    U kunt naar links of rechts schuiven om verdere tegels weer te geven die afzonderlijke weer gaven vertegenwoordigen in de gegevens. 

    Selecteer een van de tegels om meer informatie over uw gegevens weer te geven.

     ![Alles weergeven](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Aanbiedingen en prijs Categorieën

[Hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)vindt u aanbiedingen en prijs categorieën voor beheer oplossingen.

### <a name="customizing-views"></a>Weer gaven aanpassen

U kunt de weer gave in uw gegevens aanpassen met behulp van de **weer gave Designer**. Als u met ontwerpen wilt beginnen, gaat u naar uw Log Analytics-werk ruimte en selecteert u de tegel **Designer weer geven** .

![Designer weergeven](./media/cdn-diagnostics-log/27_Designer.png)

U kunt de typen grafieken slepen en neerzetten en de gegevens details die u wilt analyseren.

![Designer weergeven](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Gegevens vertragingen vastleggen

De volgende tabel geeft een logboek gegevens vertraging voor **Azure CDN standaard van micro soft**, **Azure CDN standaard van Akamai**en **Azure CDN Standard/Premium van Verizon**.

Micro soft logboek gegevens vertragingen | Vertragingen bij Verizon-logboek gegevens | Vertragingen in Akamai-logboek gegevens
--- | --- | ---
Uitgesteld met een tijd van 1 uur. | Vertraagd met 1 uur en kan Maxi maal twee uur duren voordat de voltooiing van het eind punt wordt weer gegeven. | Uitgesteld met 24 uur; Als de app meer dan 24 uur geleden is gemaakt, duurt het Maxi maal twee uur om weer te starten. Als deze onlangs is gemaakt, kan het tot 25 uur duren voordat de logboeken worden weer gegeven.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Diagnostische logboek typen voor CDN-kern analyse

Micro soft biedt momenteel alleen kern analyse logboeken, die metrische gegevens bevatten met statistieken over HTTP-antwoorden en uitgangs statistieken, zoals u kunt zien van de CDN-Pop's/-randen.

### <a name="core-analytics-metrics-details"></a>Details van metrische gegevens van basis analyse
In de volgende tabel ziet u een lijst met metrische gegevens die beschikbaar zijn in de logboeken van de kern analyse voor **Azure CDN standaard van micro soft**, **Azure CDN standaard van Akamai**en **Azure CDN Standard/Premium van Verizon**. Niet alle metrische gegevens zijn beschikbaar voor alle providers, hoewel deze verschillen mini maal zijn. In de tabel wordt ook weer gegeven of een bepaalde metriek beschikbaar is van een provider. De metrische gegevens zijn alleen beschikbaar voor de CDN-eind punten die verkeer hebben.


|Gegevens                     | Beschrijving | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Totaal aantal aanvraag treffers tijdens deze periode. | Ja | Ja |Ja |
| RequestCountHttpStatus2xx | Het aantal aanvragen dat heeft geleid tot een 2xx HTTP-code (bijvoorbeeld 200, 202). | Ja | Ja |Ja |
| RequestCountHttpStatus3xx | Het aantal aanvragen dat heeft geleid tot een 3xx HTTP-code (bijvoorbeeld 300, 302). | Ja | Ja |Ja |
| RequestCountHttpStatus4xx | Het aantal aanvragen dat heeft geleid tot een 4xx HTTP-code (bijvoorbeeld 400, 404). | Ja | Ja |Ja |
| RequestCountHttpStatus5xx | Het aantal aanvragen dat heeft geleid tot een 5xx HTTP-code (bijvoorbeeld 500, 504). | Ja | Ja |Ja |
| RequestCountHttpStatusOthers | Aantal andere HTTP-codes (buiten 2xx-5xx). | Ja | Ja |Ja |
| RequestCountHttpStatus200 | Het aantal aanvragen dat heeft geleid tot een 200 HTTP-code antwoord. | Yes | Nee  |Yes |
| RequestCountHttpStatus206 | Het aantal aanvragen dat heeft geleid tot een 206 HTTP-code antwoord. | Yes | Nee  |Yes |
| RequestCountHttpStatus302 | Het aantal aanvragen dat heeft geleid tot een 302 HTTP-code antwoord. | Yes | Nee  |Yes |
| RequestCountHttpStatus304 | Het aantal aanvragen dat heeft geleid tot een 304 HTTP-code antwoord. | Yes | Nee  |Yes |
| RequestCountHttpStatus404 | Het aantal aanvragen dat heeft geleid tot een 404 HTTP-code antwoord. | Yes | Nee  |Yes |
| RequestCountCacheHit | Het aantal aanvragen dat heeft geleid tot een cache-treffer. De Asset werd rechtstreeks vanuit de POP naar de client geleverd. | Ja | Ja | Nee  |
| RequestCountCacheMiss | Het aantal aanvragen dat heeft geleid tot een Missing in de cache. Een cache-Misser betekent dat de Asset niet is gevonden op de POP die het dichtst bij de client ligt en daarom is opgehaald van de oorsprong. | Ja | Ja | Nee |
| RequestCountCacheNoCache | Het aantal aanvragen voor een Asset dat niet in de cache kan worden opgeslagen vanwege een gebruikers configuratie aan de rand. | Ja | Ja | Nee |
| RequestCountCacheUncacheable | Het aantal aanvragen voor activa dat voor komt dat het in de cache wordt opgeslagen door de cache-Control en Expires van het Asset, waarmee wordt aangegeven dat deze niet in de cache moeten worden opgeslagen in een POP of de HTTP-client. | Ja | Ja | Nee |
| RequestCountCacheOthers | Aantal aanvragen met een cache status die niet onder bovenstaande vallen. | Nee | Yes | Nee  |
| EgressTotal | Uitgaande gegevens overdracht in GB | Ja |Ja |Ja |
| EgressHttpStatus2xx | Uitgaande gegevens overdracht * voor antwoorden met 2xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus3xx | Uitgaande gegevens overdracht voor antwoorden met 3xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus4xx | Uitgaande gegevens overdracht voor antwoorden met 4xx HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressHttpStatus5xx | Uitgaande gegevens overdracht voor antwoorden met 5xx HTTP-status codes in GB. | Ja | Ja | Nee |
| EgressHttpStatusOthers | Uitgaande gegevens overdracht voor antwoorden met andere HTTP-status codes in GB. | Ja | Ja | Nee  |
| EgressCacheHit | Uitgaande gegevens overdracht voor antwoorden die rechtstreeks vanuit de CDN-cache op de CDN Pop's/randen zijn geleverd. | Ja | Ja | Nee |
| EgressCacheMiss. | Uitgaande gegevens overdracht voor antwoorden die niet zijn gevonden op de dichtstbijzijnde POP-server en die is opgehaald van de oorspronkelijke server. | Ja | Ja | Nee |
| EgressCacheNoCache | Uitgaande gegevens overdracht voor activa die niet in de cache worden opgeslagen vanwege een gebruikers configuratie aan de rand. | Ja | Ja | Nee |
| EgressCacheUncacheable | Uitgaande gegevens overdracht voor activa die niet in de cache worden opgeslagen door de cache-Control en/of expires-headers van het Asset. Geeft aan dat deze niet in de cache moet worden opgeslagen in een POP of door de HTTP-client. | Ja | Ja | Nee |
| EgressCacheOthers | Uitgaande gegevens overdracht voor andere cache scenario's. | Nee | Yes | Nee |

* Uitgaande gegevens overdracht verwijst naar verkeer dat van CDN POP-servers naar de client wordt geleverd.


### <a name="schema-of-the-core-analytics-logs"></a>Schema van de logboeken met kern analyse 

Alle logboeken worden opgeslagen in JSON-indeling en elk item bevat teken reeks velden volgens het volgende schema:

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

WHERE- *tijd* vertegenwoordigt de begin tijd van de grens van het uur waarvoor de statistieken worden gerapporteerd. Wanneer een metrische waarde niet wordt ondersteund door een CDN-provider, is er een null-waarde. Deze null-waarde geeft het ontbreken van een metriek aan en wijkt af van de waarde 0. Er is één set van deze metrische gegevens per domein dat op het eind punt is geconfigureerd.

Voorbeeld eigenschappen:

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

* [Diagnostische logboeken van Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Kern analyse via Azure CDN aanvullende portal](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Azure Monitor-logboeken](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Azure Log Analytics REST API](https://docs.microsoft.com/rest/api/loganalytics)







