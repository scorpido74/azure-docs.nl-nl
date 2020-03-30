---
title: Back-end status- en diagnostische logboeken
titleSuffix: Azure Application Gateway
description: Meer informatie over het inschakelen en beheren van toegangslogboeken en prestatielogboeken voor Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/22/2019
ms.author: victorh
ms.openlocfilehash: 1ddbc8e909c5ba0b720e893e87c0f495d256a886
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279154"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Back-endstatus- en diagnostische logboeken voor application gateway

U azure application gateway-bronnen op de volgende manieren controleren:

* [Back-endstatus:](#back-end-health)Application Gateway biedt de mogelijkheid om de status van de servers in de back-endpools te controleren via de Azure-portal en via PowerShell. U ook de status van de back-endpools vinden via de diagnostische logboeken voor prestaties.

* [Logboeken:](#diagnostic-logging)Logboeken zorgen ervoor dat prestatie-, toegangs- en andere gegevens worden opgeslagen of verbruikt vanuit een bron voor bewakingsdoeleinden.

* [Statistieken:](application-gateway-metrics.md)Application Gateway heeft verschillende statistieken waarmee u controleren of uw systeem presteert zoals verwacht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Back-end gezondheid

Application Gateway biedt de mogelijkheid om de status van individuele leden van de back-endpools te controleren via de portal, PowerShell en de command-line interface (CLI). U ook een geaggregeerde statussamenvatting van back-endpools vinden via de diagnostische logboeken voor prestaties.

Het back-endstatusrapport weerspiegelt de uitvoer van de statussonde van de Application Gateway naar de back-endinstanties. Wanneer indringende succesvol is en de back-end verkeer kan ontvangen, wordt het als gezond beschouwd. Anders wordt het als ongezond beschouwd.

> [!IMPORTANT]
> Als er een netwerkbeveiligingsgroep (NSG) op een subnet van application gateway staat, opent u poortbereiken 65503-65534 voor v1 SKU's en 65200-65535 voor v2 SKU's op het subnet Application Gateway voor binnenkomend verkeer. Dit poortbereik is vereist voor azure-infrastructuurcommunicatie. Ze zijn beveiligd (vergrendeld) met Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten, inclusief de klanten van die gateways, geen wijzigingen op die eindpunten initiëren.


### <a name="view-back-end-health-through-the-portal"></a>Back-end status bekijken via het portaal

In het portaal wordt automatisch back-end status geleverd. Selecteer**backendstatus** **controleren** > in een bestaande toepassingsgateway .

Elk lid in de back-end pool wordt op deze pagina weergegeven (of het nu een NIC, IP of FQDN is). De naam van back-endpool, poort, back-end HTTP-instellingen en de status van de status worden weergegeven. Geldige waarden voor de status zijn **Gezond,** **Ongezond**en **Onbekend**.

> [!NOTE]
> Als u een back-endstatus van **Onbekend**ziet, moet u ervoor zorgen dat de toegang tot de back-end niet wordt geblokkeerd door een NSG-regel, een door de gebruiker gedefinieerde route (UDR) of een aangepaste DNS in het virtuele netwerk.

![Back-end gezondheid][10]

### <a name="view-back-end-health-through-powershell"></a>Back-end status weergeven via PowerShell

De volgende PowerShell-code laat zien hoe u `Get-AzApplicationGatewayBackendHealth` de back-endstatus weergeven met behulp van de cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Back-endstatus weergeven via Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultaten

In het volgende fragment ziet u een voorbeeld van het antwoord:

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>Diagnostische logboeken

U verschillende typen logboeken in Azure gebruiken om toepassingsgateways te beheren en op te lossen. Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Alle logboeken kunnen worden geëxtraheerd uit Azure Blob-opslag en worden weergegeven in verschillende hulpprogramma's, zoals [Azure Monitor-logboeken,](../azure-monitor/insights/azure-networking-analytics.md)Excel en Power BI. In de volgende lijst vindt u meer informatie over de verschillende typen logboeken:

* **Activiteitenlogboek:** U [Azure-activiteitslogboeken](../monitoring-and-diagnostics/insights-debugging-with-events.md) (voorheen bekend als operationele logboeken en controlelogboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement en hun status. Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.
* **Toegangslogboek:** U dit logboek gebruiken om toegangspatronen van de toepassingsgateway weer te geven en belangrijke informatie te analyseren. Dit omvat het IP-adres van de beller, de gevraagde URL, de reactielatentie, de retourcode en bytes in en uit. Elke 300 seconden wordt een toegangslogboek verzameld. Dit logboek bevat één record per exemplaar van Application Gateway. De instantie Application Gateway wordt geïdentificeerd door de eigenschap instanceId.
* **Prestatielogboek:** U dit logboek gebruiken om te bekijken hoe toepassingsgateway-exemplaren presteren. In dit logboek worden prestatiegegevens voor elk exemplaar vastgelegd, inclusief totale aanvragen die worden ontvangen, doorvoer in bytes, totale aanvragen die worden weergegeven, het aantal mislukte aanvragen en het aantal gezonde en ongezonde back-end-instantie. Elke 60 seconden wordt een prestatielogboek verzameld. Het prestatielogboek is alleen beschikbaar voor de v1 SKU. Gebruik voor de v2 SKU [Metrics](application-gateway-metrics.md) voor prestatiegegevens.
* **Firewalllogboek:** U dit logboek gebruiken om de aanvragen weer te geven die zijn vastgelegd via de detectie- of preventiemodus van een toepassingsgateway die is geconfigureerd met de firewall van de webtoepassing.

> [!NOTE]
> Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het implementatiemodel van Azure Resource Manager. U logboeken niet gebruiken voor resources in het klassieke implementatiemodel. Zie het [artikel Understanding Resource Manager-implementatie en het klassieke implementatieartikel](../azure-resource-manager/management/deployment-models.md) voor een beter begrip van de twee modellen.

U hebt drie opties voor het opslaan van uw logboeken:

* **Opslagaccount**: opslagaccounts kunnen het best worden gebruikt wanneer logboeken voor een langere periode worden opgeslagen en moeten kunnen worden bekeken wanneer dat nodig is.
* **Gebeurtenishubs**: Gebeurtenishubs zijn een geweldige optie om te integreren met andere SIEM-tools (Security Information and Event Management) om waarschuwingen te ontvangen over uw resources.
* **Azure Monitor-logboeken:** Azure Monitor-logboeken worden het best gebruikt voor algemene realtime bewaking van uw toepassing of het bekijken van trends.

### <a name="enable-logging-through-powershell"></a>Logboekregistratie inschakelen via PowerShell

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. U moet toegangs- en prestatielogboekregistratie inschakelen om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Als u logboekregistratie wilt inschakelen, gebruikt u de volgende stappen:

1. Noteer de resource-ID van uw opslagaccount waar de logboekgegevens worden opgeslagen. Deze waarde heeft de volgende indeling: /abonnementen/\<subscriptionId\>/resourceGroups/\<resourcegroepnaam\>/providers/Microsoft.Storage/storageAccounts/\<opslagaccountnaam\>. U kunt elk opslagaccount in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden.

    ![Portal: bron-ID voor opslagaccount](./media/application-gateway-diagnostics/diagnostics1.png)

2. Let op de resource-id van uw toepassingsgateway waarvoor logboekregistratie is ingeschakeld. Deze waarde is van het\<formulier:\>/subscriptions/\<subscriptionId\>/resourceGroups/ resource group name\</providers/Microsoft.Network/applicationGateways/application gateway name\>. U kunt de portal gebruiken om deze informatie te vinden.

    ![Portal: resource-id voor toepassingsgateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Schakel diagnostische logboekregistratie in door de volgende PowerShell-cmdlet te gebruiken:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>Voor activiteitenlogboeken is geen apart opslagaccount vereist. Voor het gebruik van opslag voor toegangs- en prestatielogboeken worden servicekosten in rekening gebracht.

### <a name="enable-logging-through-the-azure-portal"></a>Logboekregistratie inschakelen via de Azure-portal

1. Zoek in de Azure-portal uw bron en selecteer **Diagnostische instellingen**.

   Voor Application Gateway zijn drie logboeken beschikbaar:

   * Toegangslogboek
   * Prestatielogboek
   * Firewalllogboek

2. Als u wilt beginnen met het verzamelen van gegevens, selecteert u **Diagnostische gegevens inschakelen**.

   ![Diagnose inschakelen][1]

3. Op de pagina **Diagnostische instellingen** staan de instellingen voor de diagnostische logboeken. In dit voorbeeld slaat Log Analytics de logboeken op. U kunt ook Event Hubs en een opslagaccount gebruiken om de diagnostische logboeken op te slaan.

   ![Het configuratieproces starten][2]

5. Typ een naam voor de instellingen, bevestig de instellingen en selecteer **Opslaan**.

### <a name="activity-log"></a>Activiteitenlogboek

Azure genereert standaard het activiteitenlogboek. De logboeken worden 90 dagen bewaard in het Azure-gebeurtenislogboekenarchief. Lees het artikel Gebeurtenissen [en activiteitenlogboek en het](../monitoring-and-diagnostics/insights-debugging-with-events.md) logboek van de activiteit.

### <a name="access-log"></a>Toegangslogboek

Het toegangslogboek wordt alleen gegenereerd als u het hebt ingeschakeld op elke instantie van de Toepassingsgateway, zoals beschreven in de voorgaande stappen. De gegevens worden opgeslagen in het opslagaccount dat u hebt opgegeven toen u de logboekregistratie hebt ingeschakeld. Elke toegang tot Application Gateway is aangemeld in JSON-indeling, zoals weergegeven in het volgende voorbeeld voor v1:

|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Instantie Application Gateway waarmee de aanvraag is ingediend.        |
|clientIP     | Ip van oorsprong voor het verzoek.        |
|clientPort     | Van oorsprong poort voor het verzoek.       |
|httpMethod httpMethod     | HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | URI van het ontvangen verzoek.        |
|RequestQuery     | **Server-Routed:** Back-end pool instantie die de aanvraag is verzonden.</br>**X-AzureApplicationGateway-LOG-ID:** Correlatie-id die voor de aanvraag wordt gebruikt. Het kan worden gebruikt om verkeersproblemen op de back-endservers op te lossen. </br>**SERVER-STATUS: HTTP-antwoordcode**die Application Gateway van de back-end heeft ontvangen.       |
|Useragent     | Gebruikersagent van de HTTP-aanvraagkop.        |
|httpStatus     | HTTP-statuscode die vanuit Application Gateway naar de client wordt geretourneerd.       |
|httpVersie     | HTTP-versie van het verzoek.        |
|receivedBytes     | Grootte van het ontvangen pakket, in bytes.        |
|verzonden Bytes| Grootte van verzonden pakket, in bytes.|
|timeTaken| Tijdsduur (in milliseconden) die nodig zijn om een aanvraag te verwerken en het antwoord ervan te worden verzonden. Dit wordt berekend als het interval vanaf het moment dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt tot het moment waarop de bewerking voor antwoordverzenden is voltooid. Het is belangrijk op te merken dat het veld Time-Taken meestal de tijd bevat dat de aanvraag- en antwoordpakketten via het netwerk worden uitgevoerd. |
|sslEnabled| Of communicatie met de back-end pools SSL heeft gebruikt. Geldige waarden zijn aan en uit.|
|host| De hostnaam waarmee de aanvraag naar de backendserver is verzonden. Als backendhostnaam wordt overschreven, wordt deze naam die weergegeven.|
|origineleHost| De hostnaam waarmee de aanvraag is ontvangen door de Application Gateway van de client.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Voor Application Gateway en WAF v2, de logs tonen een beetje meer informatie:

|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Instantie Application Gateway waarmee de aanvraag is ingediend.        |
|clientIP     | Ip van oorsprong voor het verzoek.        |
|clientPort     | Van oorsprong poort voor het verzoek.       |
|httpMethod httpMethod     | HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | URI van het ontvangen verzoek.        |
|Useragent     | Gebruikersagent van de HTTP-aanvraagkop.        |
|httpStatus     | HTTP-statuscode die vanuit Application Gateway naar de client wordt geretourneerd.       |
|httpVersie     | HTTP-versie van het verzoek.        |
|receivedBytes     | Grootte van het ontvangen pakket, in bytes.        |
|verzonden Bytes| Grootte van verzonden pakket, in bytes.|
|timeTaken| Tijdsduur (in **seconden)** die nodig zijn om een aanvraag te verwerken en het antwoord ervan te worden verzonden. Dit wordt berekend als het interval vanaf het moment dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt tot het moment waarop de bewerking voor antwoordverzenden is voltooid. Het is belangrijk op te merken dat het veld Time-Taken meestal de tijd bevat dat de aanvraag- en antwoordpakketten via het netwerk worden uitgevoerd. |
|sslEnabled| Of communicatie met de back-end pools SSL heeft gebruikt. Geldige waarden zijn aan en uit.|
|sslCipher sslCipher| Cipher suite wordt gebruikt voor SSL-communicatie (als SSL is ingeschakeld).|
|sslProtocol| SSL/TLS-protocol dat wordt gebruikt (als SSL is ingeschakeld).|
|serverRouted| De backendserver waar de toepassingsgateway naar toe leidt, leidt het verzoek naar.|
|serverStatus| HTTP-statuscode van de backendserver.|
|serverResponseLatentie| Latentie van het antwoord vanaf de backendserver.|
|host| Adres in de hostheader van de aanvraag.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>Prestatielogboek

Het prestatielogboek wordt alleen gegenereerd als u het hebt ingeschakeld op elke instantie van de Toepassingsgateway, zoals beschreven in de voorgaande stappen. De gegevens worden opgeslagen in het opslagaccount dat u hebt opgegeven toen u de logboekregistratie hebt ingeschakeld. De prestatielogboekgegevens worden gegenereerd in intervallen van 1 minuut. Het is alleen beschikbaar voor de v1 SKU. Gebruik voor de v2 SKU [Metrics](application-gateway-metrics.md) voor prestatiegegevens. De volgende gegevens worden geregistreerd:


|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     |  Toepassingsgateway, bijvoorbeeld waarvoor prestatiegegevens worden gegenereerd. Voor een toepassingsgateway met meerdere instanties is er één rij per instantie.        |
|healthyHostCount     | Aantal gezonde hosts in de back-end pool.        |
|unHealthyHostCount     | Aantal ongezonde hosts in de back-end pool.        |
|requestCount     | Aantal aanmeldingen.        |
|Latency | Gemiddelde latentie (in milliseconden) van aanvragen van de instantie naar de back-end die de aanvragen dient. |
|misluktAantal aanvragen| Aantal mislukte aanvragen.|
|doorvoer| Gemiddelde doorvoer sinds het laatste logboek, gemeten in bytes per seconde.|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> Latentie wordt berekend vanaf het moment waarop de eerste byte van de HTTP-aanvraag wordt ontvangen tot het moment waarop de laatste byte van het HTTP-antwoord wordt verzonden. Het is de som van de verwerkingstijd van de Application Gateway plus de netwerkkosten voor de back-end, plus de tijd die de back-end nodig heeft om de aanvraag te verwerken.

### <a name="firewall-log"></a>Firewalllogboek

Het firewalllogboek wordt alleen gegenereerd als u het voor elke toepassingsgateway hebt ingeschakeld, zoals beschreven in de voorgaande stappen. Dit logboek vereist ook dat de firewall van de webtoepassing is geconfigureerd op een toepassingsgateway. De gegevens worden opgeslagen in het opslagaccount dat u hebt opgegeven toen u de logboekregistratie hebt ingeschakeld. De volgende gegevens worden geregistreerd:


|Waarde  |Beschrijving  |
|---------|---------|
|instanceId     | Toepassingsgateway, bijvoorbeeld waarvoor firewallgegevens worden gegenereerd. Voor een toepassingsgateway met meerdere instanties is er één rij per instantie.         |
|clientIp     |   Ip van oorsprong voor het verzoek.      |
|clientPort     |  Van oorsprong poort voor het verzoek.       |
|requestUri     | URL van het ontvangen verzoek.       |
|ruleSetType     | Regelsettype. De beschikbare waarde is OWASP.        |
|regelSetVersion     | Regelsetversie gebruikt. Beschikbare waarden zijn 2.2.9 en 3.0.     |
|ruleId (ruleId)     | Regel-ID van de triggergebeurtenis.        |
|message     | Gebruiksvriendelijke boodschap voor de triggering event. Meer details zijn opgenomen in de details sectie.        |
|action     |  Maatregelen genomen op het verzoek. Beschikbare waarden zijn gematched en geblokkeerd.      |
|site     | Site waarvoor het logboek is gegenereerd. Momenteel wordt alleen Globaal vermeld omdat regels globaal zijn.|
|Details     | Details van de triggering gebeurtenis.        |
|details.bericht     | Beschrijving van de regel.        |
|details.gegevens     | Specifieke gegevens die in het verzoek zijn gevonden en die overeenkomen met de regel.         |
|details.bestand     | Configuratiebestand dat de regel bevatte.        |
|details.line     | Regelnummer in het configuratiebestand dat de gebeurtenis heeft geactiveerd.       |
|Hostname   | Hostname of IP-adres van de Application Gateway.    |
|transactionId  | Unieke id voor een bepaalde transactie die helpt bij het groeperen van meerdere regelschendingen die binnen dezelfde aanvraag zijn opgetreden.   |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    "instanceId": "ApplicationGatewayRole_IN_0",
    "clientIp": "104.210.252.3",
    "clientPort": "4835",
    "requestUri": "/?a=%3Cscript%3Ealert(%22Hello%22);%3C/script%3E",
    "ruleSetType": "OWASP",
    "ruleSetVersion": "3.0",
    "ruleId": "941320",
    "message": "Possible XSS Attack Detected - HTML Tag Handler",
    "action": "Blocked",
    "site": "Global",
    "details": {
      "message": "Warning. Pattern match \"<(a|abbr|acronym|address|applet|area|audioscope|b|base|basefront|bdo|bgsound|big|blackface|blink|blockquote|body|bq|br|button|caption|center|cite|code|col|colgroup|comment|dd|del|dfn|dir|div|dl|dt|em|embed|fieldset|fn|font|form|frame|frameset|h1|head|h ...\" at ARGS:a.",
      "data": "Matched Data: <script> found within ARGS:a: <script>alert(\\x22hello\\x22);</script>",
      "file": "rules/REQUEST-941-APPLICATION-ATTACK-XSS.conf",
      "line": "865"
    }
    "hostname": "40.90.218.100",
    "transactionId": "AYAcUqAcAcAcAcAcASAcAcAc"
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteitenlogboekgegevens bekijken en analyseren via een van de volgende methoden:

* **Azure-hulpprogramma’s**: Haal informatie uit het activiteitenlogboek op via Azure PowerShell, de Azure CLI, de Azure REST-API of de Azure-portal. In het artikel [Activiteitsbewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md) staan stapsgewijze instructies voor elke methode.
* **Power BI**: Als u nog geen [Power BI](https://powerbi.microsoft.com/pricing)-account hebt, kunt u het gratis uitproberen. Met de [Power BI-sjabloon-apps](https://docs.microsoft.com/power-bi/service-template-apps-overview)u uw gegevens analyseren.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>De toegangs-, prestatie- en firewalllogboeken weergeven en analyseren

[Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md) kunnen de teller- en gebeurtenislogboekbestanden verzamelen van uw Blob-opslagaccount. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>Access-logboeken analyseren via GoAccess

We hebben een Resource Manager-sjabloon gepubliceerd die de populaire [GoAccess-loganalyzer](https://goaccess.io/) voor Application Gateway Access-logboeken installeert en uitvoert. GoAccess biedt waardevolle HTTP-verkeersstatistieken zoals unieke bezoekers, opgevraagde bestanden, hosts, besturingssystemen, browsers, HTTP-statuscodes en meer. Zie het [bestand Readme in de sjabloonmap Resourcebeheer in GitHub](https://aka.ms/appgwgoaccessreadme)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller- en gebeurtenislogboeken met [Azure Monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteitenlogboek met Power BI-blogbericht.](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx)
* [Azure-activiteitslogboeken weergeven en analyseren in Power BI en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogberichten.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
