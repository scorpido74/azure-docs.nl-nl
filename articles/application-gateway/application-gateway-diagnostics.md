---
title: Toegangs logboeken, prestatie logboeken, status van back-end en metrische gegevens voor Azure-toepassing gateway bewaken
description: Meer informatie over het inschakelen en beheren van toegangs logboeken en prestatie logboeken voor Azure-toepassing gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 3/28/2019
ms.author: victorh
ms.openlocfilehash: 6df78a46e6bc8055f8cce89e199d01ad631e178e
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306192"
---
# <a name="back-end-health-and-diagnostic-logs-for-application-gateway"></a>Back-end status-en Diagnostische logboeken voor Application Gateway

Als u Azure-toepassing gateway gebruikt, kunt u resources op de volgende manieren controleren:

* [Status van back-end](#back-end-health): Application Gateway biedt de mogelijkheid om de status van de servers in de back-endservers te bewaken via de Azure Portal en via Power shell. U kunt ook de status van de back-endservers vinden via de diagnostische logboeken voor prestaties.

* [Logboeken](#diagnostic-logging): Met logboeken kunnen prestaties, toegang en andere gegevens worden opgeslagen of gebruikt voor bewakings doeleinden van een resource.

* [Metrische gegevens](application-gateway-metrics.md): Application Gateway heeft verschillende metrische gegevens waarmee u kunt controleren of uw systeem wordt uitgevoerd zoals verwacht.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="back-end-health"></a>Status van back-end

Application Gateway biedt de mogelijkheid om de status van afzonderlijke leden van de back-endservers te bewaken via de portal, Power shell en de opdracht regel interface (CLI). U kunt ook een geaggregeerde status samenvatting van back-end-Pools vinden via de diagnostische logboeken voor prestaties. 

Het status rapport van de back-end weerspiegelt de uitvoer van de Application Gateway status test naar de back-end-exemplaren. Wanneer het zoeken is geslaagd en de back-end verkeer kan ontvangen, wordt het als gezond beschouwd. Anders wordt het beschouwd als een slechte status.

> [!IMPORTANT]
> Als er een netwerk beveiligings groep (NSG) op een Application Gateway-subnet aanwezig is, opent u poort bereik 65503-65534 in het subnet van het Application Gateway voor binnenkomend verkeer. Dit poort bereik is vereist voor de communicatie van Azure-infra structuur. Ze zijn beveiligd (vergrendeld) met Azure-certificaten. Zonder de juiste certificaten kunnen externe entiteiten, met inbegrip van de klanten van deze gateways, geen wijzigingen op deze eind punten initiëren.


### <a name="view-back-end-health-through-the-portal"></a>Status van de back-end weer geven via de portal

In de portal wordt de status van de back-end automatisch ontvangen. In een bestaande toepassings gateway selecteert u**status van back-end** **controleren** > . 

Elk lid van de back-end-pool wordt op deze pagina vermeld (of het nu een NIC, IP of FQDN-naam is). De naam van de back-end, de poort, de HTTP-instellingen voor de back-end en de integriteits status worden weer gegeven. Geldige waarden voor de integriteits status zijn **in orde**, **beschadigd**en **onbekend**.

> [!NOTE]
> Als u de status **onbekend**van de back-end ziet, moet u ervoor zorgen dat de toegang tot de back-end niet wordt geblokkeerd door een NSG-regel, een door de gebruiker gedefinieerde route (UDR) of een aangepaste DNS-server in het virtuele netwerk.

![Status van back-end][10]

### <a name="view-back-end-health-through-powershell"></a>Status van de back-end weer geven via Power shell

De volgende Power shell-code laat zien hoe u de status van de back `Get-AzApplicationGatewayBackendHealth` -end kunt weer geven met behulp van de cmdlet:

```powershell
Get-AzApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

### <a name="view-back-end-health-through-azure-cli"></a>Status van de back-end weer geven via Azure CLI

```azurecli
az network application-gateway show-backend-health --resource-group AdatumAppGatewayRG --name AdatumAppGateway
```

### <a name="results"></a>Resultaten

Het volgende code fragment toont een voor beeld van het antwoord:

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

## <a name="diagnostic-logging"></a>Diagnostische logboeken

U kunt verschillende typen logboeken in azure gebruiken om toepassings gateways te beheren en op te lossen. Via de portal kunt u toegang verkrijgen tot sommige van deze logboeken. Alle logboeken kunnen worden geëxtraheerd uit Azure Blob-opslag en worden weer gegeven in verschillende hulpprogram ma's, zoals [Azure monitor logboeken](../azure-monitor/insights/azure-networking-analytics.md), Excel en Power bi. Meer informatie over de verschillende typen logboeken vindt u in de volgende lijst:

* **Activiteiten logboek**: U kunt [Azure-activiteiten logboeken](../monitoring-and-diagnostics/insights-debugging-with-events.md) (voorheen bekend als operationele logboeken en audit Logboeken) gebruiken om alle bewerkingen weer te geven die zijn verzonden naar uw Azure-abonnement en hun status. Activiteitenlogboekitems worden standaard verzameld en kunnen in de Azure-portal worden bekeken.
* **Toegangs logboek**: U kunt dit logboek gebruiken om Application Gateway toegangs patronen weer te geven en belang rijke informatie te analyseren. Dit geldt ook voor de IP van de beller, de aangevraagde URL, reactie latentie, retour code en bytes in en uit. Er wordt elke 300 seconden een toegangs logboek verzameld. Dit logboek bevat één record per exemplaar van Application Gateway. Het Application Gateway exemplaar wordt geïdentificeerd door de eigenschap instanceId.
* **Prestatie logboek**: U kunt dit logboek gebruiken om te zien hoe Application Gateway exemplaren worden uitgevoerd. In dit logboek worden de prestatie gegevens voor elk exemplaar vastgelegd, met inbegrip van het totale aantal geleverde aanvragen, de door Voer in bytes, het totale aantal geleverde aanvragen, de telling van mislukte aanvragen en een gezonde en slechte back-end-instantie. Er wordt elke 60 seconden een prestatie logboek verzameld.
* **Firewall logboek**: U kunt dit logboek gebruiken om de aanvragen weer te geven die zijn geregistreerd via de detectie-of preventie modus van een toepassings gateway die is geconfigureerd met de Web Application Firewall.

> [!NOTE]
> Logboeken zijn alleen beschikbaar voor resources die zijn geïmplementeerd in het Azure Resource Manager-implementatie model. U kunt geen Logboeken gebruiken voor bronnen in het klassieke implementatie model. Zie het artikel wat is de [implementatie van Resource Manager en de klassieke implementatie](../azure-resource-manager/resource-manager-deployment-model.md) voor een beter inzicht in de twee modellen.

U hebt drie opties voor het opslaan van uw logboeken:

* **Opslagaccount**: Opslag accounts worden het beste gebruikt voor Logboeken wanneer logboeken worden opgeslagen voor een langere duur en worden gecontroleerd wanneer dit nodig is.
* **Event hubs**: Event hubs zijn een uitstekende optie om te integreren met andere hulpprogram ma's voor SIEM (Security Information and Event Management) om waarschuwingen over uw resources te krijgen.
* **Azure monitor logboeken**: Azure Monitor logboeken worden het beste gebruikt voor algemene realtime-bewaking van uw toepassing of voor het bekijken van trends.

### <a name="enable-logging-through-powershell"></a>Logboek registratie via Power shell inschakelen

Activiteitenlogboekregistratie is automatisch ingeschakeld voor elke Resource Manager-resource. U moet logboek registratie van toegang en prestaties inschakelen om te beginnen met het verzamelen van de gegevens die beschikbaar zijn via deze logboeken. Als u logboek registratie wilt inschakelen, gebruikt u de volgende stappen:

1. Noteer de resource-ID van uw opslagaccount waar de logboekgegevens worden opgeslagen. Deze waarde heeft de indeling:/Subscriptions/\<subscriptionId\>/resourceGroups/\<resource groep name\>/providers/Microsoft.Storage/storageAccounts/\<Storage account name\>. U kunt elk opslagaccount in uw abonnement gebruiken. U kunt de Azure-portal gebruiken om deze informatie te vinden.

    ![Portal: Resource-ID voor opslag account](./media/application-gateway-diagnostics/diagnostics1.png)

2. Noteer de resource-ID van uw toepassings gateway waarvoor logboek registratie is ingeschakeld. Deze waarde heeft de indeling:/Subscriptions/\<subscriptionId\>/resourceGroups/\<resource groep name\>/providers/Microsoft.Network/applicationGateways/\<Application Gateway name \>. U kunt de portal gebruiken om deze informatie te vinden.

    ![Portal: Resource-ID voor toepassings gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Schakel diagnostische logboekregistratie in door de volgende PowerShell-cmdlet te gebruiken:

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Voor activiteiten Logboeken is geen afzonderlijk opslag account vereist. Voor het gebruik van opslag voor toegangs- en prestatielogboeken worden servicekosten in rekening gebracht.

### <a name="enable-logging-through-the-azure-portal"></a>Logboekregistratie inschakelen via de Azure-portal

1. Zoek in het Azure Portal uw resource en selecteer **Diagnostische instellingen**.

   Voor Application Gateway zijn drie logboeken beschikbaar:

   * Access-logboek
   * Prestatie logboek
   * Firewall logboek

2. Selecteer **diagnostiek inschakelen**om gegevens te verzamelen.

   ![Diagnostische gegevens inschakelen][1]

3. Op de pagina **Diagnostische instellingen** staan de instellingen voor de diagnostische logboeken. In dit voor beeld slaat Log Analytics de logboeken op. U kunt ook Event Hubs en een opslagaccount gebruiken om de diagnostische logboeken op te slaan.

   ![Het configuratie proces wordt gestart][2]

5. Typ een naam voor de instellingen, bevestig de instellingen en selecteer **Opslaan**.

### <a name="activity-log"></a>Activiteitenlogboek

Azure genereert standaard het activiteiten logboek. De logboeken blijven 90 dagen bewaard in de Azure gebeurtenis logboeken Store. Lees het artikel [gebeurtenissen en activiteiten logboek weer geven](../monitoring-and-diagnostics/insights-debugging-with-events.md) voor meer informatie over deze logboeken.

### <a name="access-log"></a>Access-logboek

Het toegangs logboek wordt alleen gegenereerd als u het hebt ingeschakeld op elk Application Gateway-exemplaar, zoals in de voor gaande stappen wordt beschreven. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. Elke toegang van Application Gateway wordt geregistreerd in JSON-indeling, zoals wordt weer gegeven in het volgende voor beeld voor v1:

|Value  |Description  |
|---------|---------|
|instanceId     | Application Gateway exemplaar dat de aanvraag heeft verzonden.        |
|clientIP     | Oorspronkelijk IP-adres voor de aanvraag.        |
|clientPort     | Bron poort voor de aanvraag.       |
|httpMethod     | De HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | De URI van de ontvangen aanvraag.        |
|RequestQuery     | Door de **server gerouteerd**: Het exemplaar van de back-end-pool dat de aanvraag heeft verzonden.</br>**X-AzureApplicationGateway-LOG-ID**: De correlatie-ID die voor de aanvraag wordt gebruikt. Het kan worden gebruikt om problemen met verkeer op de back-endservers op te lossen. </br>**SERVER-STATUS**: HTTP-antwoord code die Application Gateway ontvangen van de back-end.       |
|User agent     | Gebruikers agent van de header van de HTTP-aanvraag.        |
|httpStatus     | Er is een HTTP-status code geretourneerd naar de client van Application Gateway.       |
|httpVersion     | HTTP-versie van de aanvraag.        |
|receivedBytes     | Grootte van ontvangen pakket, in bytes.        |
|sentBytes| Grootte van het verzonden pakket, in bytes.|
|timeTaken| De tijds duur (in milliseconden) die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat het veld voor de duur doorgaans de tijd bevat dat de aanvraag-en antwoord pakketten via het netwerk onderweg zijn. |
|sslEnabled| Hiermee wordt aangegeven of communicatie met de back-endservers gebruik maakt van SSL. Geldige waarden zijn in-en uitgeschakeld.|
|host| De hostnaam waarmee de aanvraag is verzonden naar de back-endserver. Als de hostnaam van de back-end wordt overschreven, wordt deze naam weer gegeven.|
|originalHost| De hostnaam waarmee de aanvraag is ontvangen door de Application Gateway van de client.|
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
Voor Application Gateway en WAF v2 bevatten de logboeken nog iets meer informatie:

|Value  |Description  |
|---------|---------|
|instanceId     | Application Gateway exemplaar dat de aanvraag heeft verzonden.        |
|clientIP     | Oorspronkelijk IP-adres voor de aanvraag.        |
|clientPort     | Bron poort voor de aanvraag.       |
|httpMethod     | De HTTP-methode die door de aanvraag wordt gebruikt.       |
|requestUri     | De URI van de ontvangen aanvraag.        |
|User agent     | Gebruikers agent van de header van de HTTP-aanvraag.        |
|httpStatus     | Er is een HTTP-status code geretourneerd naar de client van Application Gateway.       |
|httpVersion     | HTTP-versie van de aanvraag.        |
|receivedBytes     | Grootte van ontvangen pakket, in bytes.        |
|sentBytes| Grootte van het verzonden pakket, in bytes.|
|timeTaken| De tijds duur (in milliseconden) die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat het veld voor de duur doorgaans de tijd bevat dat de aanvraag-en antwoord pakketten via het netwerk onderweg zijn. |
|sslEnabled| Hiermee wordt aangegeven of communicatie met de back-endservers gebruik maakt van SSL. Geldige waarden zijn in-en uitgeschakeld.|
|sslCipher| Coderings suite wordt gebruikt voor SSL-communicatie (als SSL is ingeschakeld).|
|sslProtocol| SSL-protocol dat wordt gebruikt (als SSL is ingeschakeld).|
|serverRouted| De back-endserver die Application Gateway naar verzendt.|
|serverStatus| HTTP-status code van de back-endserver.|
|serverResponseLatency| Latentie van het antwoord van de back-endserver.|
|host| Het adres dat wordt vermeld in de host-header van de aanvraag.|
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

### <a name="performance-log"></a>Prestatie logboek

Het prestatie logboek wordt alleen gegenereerd als u het hebt ingeschakeld op elk Application Gateway-exemplaar, zoals in de voor gaande stappen wordt beschreven. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. De gegevens van het prestatie logboek worden gegenereerd in intervallen van 1 minuten. De volgende gegevens worden geregistreerd:


|Value  |Description  |
|---------|---------|
|instanceId     |  Application Gateway exemplaar waarvoor prestatie gegevens worden gegenereerd. Voor een toepassings gateway met meerdere instanties is er één rij per exemplaar.        |
|healthyHostCount     | Aantal gezonde hosts in de back-end-pool.        |
|unHealthyHostCount     | Aantal beschadigde hosts in de back-end-pool.        |
|requestCount     | Aantal geleverde aanvragen.        |
|latentie | De gemiddelde latentie (in milliseconden) van aanvragen van het exemplaar naar de back-end die de aanvragen verzendt. |
|failedRequestCount| Aantal mislukte aanvragen.|
|throughput| Gemiddelde door Voer sinds het laatste logboek, gemeten in bytes per seconde.|

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
> De latentie wordt berekend op basis van de tijd waarop de eerste byte van de HTTP-aanvraag wordt ontvangen op het moment dat de laatste byte van het HTTP-antwoord wordt verzonden. Het is de som van de verwerkings tijd van de Application Gateway plus de netwerk kosten voor de back-end, plus de tijd die de back-end nodig heeft om de aanvraag te verwerken.

### <a name="firewall-log"></a>Firewall logboek

Het firewall logboek wordt alleen gegenereerd als u het hebt ingeschakeld voor elke toepassings gateway, zoals in de voor gaande stappen wordt beschreven. Het logboek vereist ook dat de Web Application Firewall is geconfigureerd op een toepassings gateway. De gegevens worden opgeslagen in het opslag account dat u hebt opgegeven tijdens het inschakelen van de logboek registratie. De volgende gegevens worden geregistreerd:


|Value  |Description  |
|---------|---------|
|instanceId     | Application Gateway exemplaar waarvoor de firewall gegevens worden gegenereerd. Voor een toepassings gateway met meerdere instanties is er één rij per exemplaar.         |
|clientIp     |   Oorspronkelijk IP-adres voor de aanvraag.      |
|clientPort     |  Bron poort voor de aanvraag.       |
|requestUri     | De URL van de ontvangen aanvraag.       |
|ruleSetType     | Type regel instellingen. De beschik bare waarde is OWASP.        |
|ruleSetVersion     | Gebruikte versie van regel instellingen. Beschik bare waarden zijn 2.2.9 en 3,0.     |
|ruleId     | De regel-ID van de trigger gebeurtenis.        |
|message     | Gebruikers vriendelijk bericht voor de activerings gebeurtenis. Meer informatie vindt u in de sectie Details.        |
|Actie     |  De actie die voor de aanvraag is uitgevoerd. Beschik bare waarden zijn geblokkeerd en toegestaan.      |
|site     | De site waarvoor het logboek is gegenereerd. Momenteel wordt alleen globaal weer gegeven omdat regels globaal zijn.|
|details informatie     | Details van de trigger gebeurtenis.        |
|Details. bericht     | Beschrijving van de regel.        |
|details.data     | Specifieke gegevens gevonden in de aanvraag die overeenkomen met de regel.         |
|Details. bestand     | Configuratie bestand waarin de regel is opgenomen.        |
|details.line     | Regel nummer in het configuratie bestand dat de gebeurtenis heeft geactiveerd.       |

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
  }
} 

```

### <a name="view-and-analyze-the-activity-log"></a>Het activiteitenlogboek bekijken en analyseren

U kunt activiteitenlogboekgegevens bekijken en analyseren via een van de volgende methoden:

* **Azure-hulpprogramma's**: haal informatie uit het activiteitenlogboek op via Azure PowerShell, de Azure CLI, de Azure REST-API of de Azure-portal. In het artikel [Activiteitsbewerkingen met Resource Manager](../azure-resource-manager/resource-group-audit.md) staan stapsgewijze instructies voor elke methode.
* **Power BI**: als u nog geen [Power BI](https://powerbi.microsoft.com/pricing)-account hebt, kunt u het gratis uitproberen. Door het [inhoudspakket voor Azure-activiteitenlogboeken voor Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) te gebruiken, kunt u uw gegevens analyseren met vooraf geconfigureerde dashboards die u kunt gebruiken zoals geleverd of kunt aanpassen.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>De logboeken voor toegang, prestaties en firewalls weer geven en analyseren

[Azure monitor logboeken](../azure-monitor/insights/azure-networking-analytics.md) kunnen de item-en gebeurtenis logboek bestanden van uw Blob Storage-account verzamelen. Het omvat visualisaties en krachtige zoekmogelijkheden om uw logboeken te analyseren.

U kunt ook verbinding maken met uw opslagaccount en de JSON-logboekitems voor toegangs- en prestatielogboeken ophalen. Nadat u de JSON-bestanden hebt gedownload, kunt u ze naar de CSV-indeling converteren en in Excel, Power BI of een ander hulpprogramma voor gegevensvisualisatie bekijken.

> [!TIP]
> Als u bekend bent met Visual Studio en de basisconcepten van het wijzigen van waarden voor constanten en variabelen in C#, kunt u de [logboekconversieprogramma’s](https://github.com/Azure-Samples/networking-dotnet-log-converter) gebruiken die beschikbaar zijn in GitHub.
> 
> 

#### <a name="analyzing-access-logs-through-goaccess"></a>Access-logboeken analyseren via GoAccess

We hebben een resource manager-sjabloon gepubliceerd waarmee de populaire [GoAccess](https://goaccess.io/) log analyzer voor Application Gateway Access-Logboeken wordt geïnstalleerd en uitgevoerd. GoAccess biedt waardevolle statistieken voor HTTP-verkeer, zoals unieke bezoekers, aangevraagde bestanden, hosts, besturings systemen, browsers, HTTP-status codes en meer. Raadpleeg het [Leesmij-bestand in de map Resource Manager-sjabloon in github](https://aka.ms/appgwgoaccessreadme)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Visualiseer teller-en gebeurtenis logboeken met behulp van [Azure monitor-logboeken](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualiseer uw Azure-activiteiten logboek met Power bi](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog bericht.
* [Bekijk en analyseer activiteiten logboeken van Azure in Power bi en meer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog berichten.

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
