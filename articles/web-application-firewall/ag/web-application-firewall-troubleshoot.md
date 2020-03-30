---
title: Problemen oplossen - Azure Web Application Firewall
description: In dit artikel vindt u informatie over probleemoplossing voor WAF (Web Application Firewall) voor Azure Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: ant
ms.topic: conceptual
ms.openlocfilehash: 33c85752903edd618044ccbab06aff7df9a791da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74046196"
---
# <a name="troubleshoot-web-application-firewall-waf-for-azure-application-gateway"></a>Problemen met Web Application Firewall (WAF) voor Azure Application Gateway oplossen

Er zijn een paar dingen die u doen als verzoeken die moeten passeren uw Web Application Firewall (WAF) worden geblokkeerd.

Zorg er eerst voor dat u het [WAF-overzicht](ag-overview.md) en de [WAF-configuratiedocumenten](application-gateway-waf-configuration.md) hebt gelezen. Zorg er ook voor dat u [WAF-bewaking](../../application-gateway/application-gateway-diagnostics.md) hebt ingeschakeld In deze artikelen wordt uitgelegd hoe de WAF-regelfuncties werken en hoe u waf-logboeken openen.

## <a name="understanding-waf-logs"></a>WAF-logboeken begrijpen

Het doel van WAF logs is om elk verzoek weer te geven dat wordt geëvenaard of geblokkeerd door de WAF. Het is een grootboek van alle geëvalueerde aanvragen die worden gematcht of geblokkeerd. Als u merkt dat de WAF blokkeert een verzoek dat het niet moet (een vals positief), u een paar dingen doen. Eerst, beperken, en vind de specifieke aanvraag. Bekijk de logboeken om de specifieke URI-, tijdstempel- of transactie-id van het verzoek te vinden. Wanneer u de bijbehorende logboekvermeldingen vindt, u beginnen te handelen op de valse positieven.

Stel dat u een legitiem verkeer hebt dat de tekenreeks *1=1* bevat die u door uw WAF wilt doorstaan. Als u het verzoek probeert, blokkeert de WAF het verkeer dat uw *1=1-tekenreeks* in een parameter of veld bevat. Dit is een tekenreeks die vaak wordt geassocieerd met een SQL-injectieaanval. U door de logboeken kijken en de tijdstempel van het verzoek bekijken en de regels die zijn geblokkeerd/gematched.

In het volgende voorbeeld u zien dat vier regels worden geactiveerd tijdens hetzelfde verzoek (met behulp van het veld TransactionId). De eerste zegt dat het overeenkomt omdat de gebruiker een numerieke / IP-URL voor het verzoek heeft gebruikt, waardoor de anomaliescore met drie wordt verhoogd omdat het een waarschuwing is. De volgende regel die overeenkomt is 942130, dat is degene die je zoekt. U de 1 = `details.data` *1* in het veld zien. Dit verhoogt de anomalie score met drie weer, want het is ook een waarschuwing. Over het algemeen verhoogt elke regel die de actie **Matched** heeft de anomaliescore, en op dit punt zou de anomaliescore zes zijn. Zie [De scoringsmodus Anomalie](ag-overview.md#anomaly-scoring-mode)voor meer informatie .

De laatste twee logboekvermeldingen tonen dat het verzoek is geblokkeerd omdat de anomaliescore hoog genoeg was. Deze vermeldingen hebben een andere actie dan de andere twee. Ze laten zien dat ze het verzoek *hebben geblokkeerd.* Deze regels zijn verplicht en kunnen niet worden uitgeschakeld. Ze moeten niet worden gezien als regels, maar meer als kerninfrastructuur van de WAF internals.

```json
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "920350", 
        "message": "Host header is a numeric IP address", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ", 
            "data": "40.90.218.160", 
            "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"", 
            "line": "791" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "OWASP_CRS", 
        "ruleSetVersion": "3.0.0", 
        "ruleId": "942130", 
        "message": "SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Matched", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ", 
            "data": "Matched Data: 1=1 found within ARGS:text1: 1=1", 
            "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"", 
            "line": "554" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ", 
            "data": "", 
            "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"", 
            "line": "57" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    } 
} 
{ 
    "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2", 
    "operationName": "ApplicationGatewayFirewall", 
    "category": "ApplicationGatewayFirewallLog", 
    "properties": { 
        "instanceId": "appgw_3", 
        "clientIp": "167.220.2.139", 
        "clientPort": "", 
        "requestUri": "\/", 
        "ruleSetType": "", 
        "ruleSetVersion": "", 
        "ruleId": "0", 
        "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.", 
        "action": "Blocked", 
        "site": "Global", 
        "details": { 
            "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ", 
            "data": "", 
            "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"", 
            "line": "73" 
        }, 
        "hostname": "vm000003", 
        "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt" 
    }
}
```

## <a name="fixing-false-positives"></a>Fout-positieven oplossen

Met deze informatie, en de kennis die regel 942130 is degene die overeenkomt met de *1 = 1* string, u een paar dingen doen om dit te stoppen van het blokkeren van uw verkeer:

- Een uitsluitingslijst gebruiken

   Zie [WAF-configuratie](application-gateway-waf-configuration.md#waf-exclusion-lists) voor meer informatie over uitsluitingslijsten.
- Schakel de regel uit.

### <a name="using-an-exclusion-list"></a>Een uitsluitingslijst gebruiken

Om een weloverwogen beslissing te nemen over het omgaan met een vals-positief, is het belangrijk om vertrouwd te raken met de technologieën die uw toepassing gebruikt. Stel dat er geen SQL-server in uw technologiestack zit en dat u false positives krijgt met betrekking tot die regels. Het uitschakelen van die regels verzwakt niet per se je veiligheid.

Een voordeel van het gebruik van een uitsluitingslijst is dat slechts een specifiek deel van een aanvraag wordt uitgeschakeld. Dit betekent echter dat een specifieke uitsluiting van toepassing is op al het verkeer dat door uw WAF gaat, omdat het een globale instelling is. Dit kan bijvoorbeeld leiden tot een probleem als *1=1* een geldig verzoek in de instantie is voor een bepaalde app, maar niet voor anderen. Een ander voordeel is dat u kiezen tussen body, headers en cookies die moeten worden uitgesloten als aan een bepaalde voorwaarde wordt voldaan, in tegenstelling tot het uitsluiten van het hele verzoek.

Af en toe zijn er gevallen waarin specifieke parameters worden doorgegeven in de WAF op een manier die mogelijk niet intuïtief is. Er is bijvoorbeeld een token dat wordt doorgegeven wanneer u azure Active Directory gebruikt. Dit token, *__RequestVerificationToken,* wordt meestal doorgegeven als een Request Cookie. In sommige gevallen waarin cookies zijn uitgeschakeld, wordt dit token echter ook doorgegeven als een aanvraagkenmerk of "arg". Als dit gebeurt, moet u ervoor zorgen dat *__RequestVerificationToken* ook als naam van het **kenmerk Request** aan de uitsluitingslijst wordt toegevoegd.

![Uitsluitingen](../media/web-application-firewall-troubleshoot/exclusion-list.png)

In dit voorbeeld wilt u de naam van het **kenmerk Request** uitsluiten die gelijk is aan *tekst1*. Dit is duidelijk omdat u de kenmerknaam zien in de firewalllogboeken: **gegevens: Overeenkomende gegevens: 1=1 gevonden in ARGS:text1: 1=1**. Het kenmerk is **text1**. U deze kenmerknaam ook op een aantal andere manieren vinden, zie [Namen van het kenmerk zoeken zoeken](#finding-request-attribute-names).

![WAF-uitsluitingslijsten](../media/web-application-firewall-troubleshoot/waf-config.png)

### <a name="disabling-rules"></a>Regels uitschakelen

Een andere manier om rond een vals positief is het uitschakelen van de regel die overeenkomen met de input van de WAF dacht dat was kwaadaardig. Aangezien u de WAF-logboeken hebt ontleed en de regel hebt verkleind tot 942130, u deze uitschakelen in de Azure-portal. Zie [Firewallregels voor webtoepassingen aanpassen via de Azure-portal](application-gateway-customize-waf-rules-portal.md).

Een voordeel van het uitschakelen van een regel is dat als u weet dat al het verkeer dat een bepaalde voorwaarde die normaal zal worden geblokkeerd is geldig verkeer, u uitschakelen die regel voor de gehele WAF. Als het echter alleen geldig verkeer in een specifieke use case is, opent u een beveiligingslek door die regel voor de hele WAF uit te schakelen omdat het een algemene instelling is.

Zie [Firewallregels voor webtoepassingen aanpassen via PowerShell](application-gateway-customize-waf-rules-powershell.md)als u Azure PowerShell wilt gebruiken. Zie [Firewallregels voor webtoepassingen aanpassen via de Azure CLI](application-gateway-customize-waf-rules-cli.md)als u Azure CLI wilt gebruiken.

![WAF-regels](../media/web-application-firewall-troubleshoot/waf-rules.png)

## <a name="finding-request-attribute-names"></a>Namen van het aanvraagkenmerk zoeken

Met behulp van [Fiddler](https://www.telerik.com/fiddler)inspecteert u individuele aanvragen en bepaalt u welke specifieke velden van een webpagina worden genoemd. Dit kan helpen om bepaalde velden uit te sluiten van inspectie met behulp van uitsluitingslijsten.

In dit voorbeeld u zien dat het veld waar de *1=1-tekenreeks* is **ingevoerd, text1**wordt genoemd.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-1.png)

Dit is een veld dat u uitsluiten. Zie [Groottelimieten voor webtoepassingen en uitsluitingslijsten](application-gateway-waf-configuration.md#waf-exclusion-lists)voor meer informatie over uitsluitingslijsten . U de evaluatie in dit geval uitsluiten door de volgende uitsluiting te configureren:

![WAF-uitsluiting](../media/web-application-firewall-troubleshoot/waf-exclusion-02.png)

U ook de firewalllogboeken onderzoeken om de informatie te krijgen om te zien wat u aan de uitsluitingslijst moet toevoegen. Zie [Back-endstatus, diagnostische logboeken en metrische gegevens voor Application Gateway](../../application-gateway/application-gateway-diagnostics.md)voor het inschakelen van logboeken.

Bestudeer het firewalllogboek en bekijk het PT1H.json-bestand voor het uur dat het verzoek dat u wilt inspecteren heeft plaatsgevonden.

In dit voorbeeld u zien dat u vier regels met dezelfde TransactionID hebt en dat ze allemaal op exact hetzelfde moment zijn opgetreden:

```json
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "920350",
-           "message": "Host header is a numeric IP address",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"^[\\\\\\\\d.:]+$\\\" at REQUEST_HEADERS:Host. ",
-               "data": "40.90.218.160",
-               "file": "rules\/REQUEST-920-PROTOCOL-ENFORCEMENT.conf\\\"",
-               "line": "791"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "OWASP_CRS",
-           "ruleSetVersion": "3.0.0",
-           "ruleId": "942130",
-           "message": "SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Matched",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Pattern match \\\"(?i:([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)([\\\\\\\\d\\\\\\\\w]++)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?:(?:=|\\u003c=\\u003e|r?like|sounds\\\\\\\\s+like|regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)\\\\\\\\2|(?:!=|\\u003c=|\\u003e=|\\u003c\\u003e|\\u003c|\\u003e|\\\\\\\\^|is\\\\\\\\s+not|not\\\\\\\\s+like|not\\\\\\\\s+regexp)([\\\\\\\\s'\\\\\\\"`\\\\\\\\(\\\\\\\\)]*?)(?!\\\\\\\\2)([\\\\\\\\d\\\\\\\\w]+)))\\\" at ARGS:text1. ",
-               "data": "Matched Data: 1=1 found within ARGS:text1: 1=1",
-               "file": "rules\/REQUEST-942-APPLICATION-ATTACK-SQLI.conf\\\"",
-               "line": "554"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Score: 8)",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Access denied with code 403 (phase 2). Operator GE matched 5 at TX:anomaly_score. ",
-               "data": "",
-               "file": "rules\/REQUEST-949-BLOCKING-EVALUATION.conf\\\"",
-               "line": "57"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
-   {
-       "resourceId": "/SUBSCRIPTIONS/A6F44B25-259E-4AF5-888A-386FED92C11B/RESOURCEGROUPS/DEMOWAF_V2/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/DEMOWAF-V2",
-       "operationName": "ApplicationGatewayFirewall",
-       "category": "ApplicationGatewayFirewallLog",
-       "properties": {
-           "instanceId": "appgw_3",
-           "clientIp": "167.220.2.139",
-           "clientPort": "",
-           "requestUri": "\/",
-           "ruleSetType": "",
-           "ruleSetVersion": "",
-           "ruleId": "0",
-           "message": "Mandatory rule. Cannot be disabled. Inbound Anomaly Score Exceeded (Total Inbound Score: 8 - SQLI=5,XSS=0,RFI=0,LFI=0,RCE=0,PHPI=0,HTTP=0,SESS=0): SQL Injection Attack: SQL Tautology Detected.",
-           "action": "Blocked",
-           "site": "Global",
-           "details": {
-               "message": "Warning. Operator GE matched 5 at TX:inbound_anomaly_score. ",
-               "data": "",
-               "file": "rules\/RESPONSE-980-CORRELATION.conf\\\"",
-               "line": "73"
-           },
-           "hostname": "vm000003",
-           "transactionId": "AcAcAcAcAKH@AcAcAcAcAyAt"
-       }
-   }
```

Met uw kennis van hoe de CRS-regelsets werken en dat de CRS-regelset 3.0 werkt met een anomaliescoresysteem (zie [Web Application Firewall voor Azure Application Gateway),](ag-overview.md)weet u dat de onderste twee regels met de **actie: Geblokkeerde** eigenschap blokkeren op basis van de totale anomaliescore. De regels om zich op te concentreren zijn de top twee.

Het eerste bericht wordt geregistreerd omdat de gebruiker een numeriek IP-adres heeft gebruikt om naar de Toepassingsgateway te navigeren, wat in dit geval kan worden genegeerd.

De tweede (regel 942130) is de interessante. U in de details zien dat het overeenkomt met een patroon (1=1) en het veld wordt **tekst1**genoemd. Volg dezelfde vorige stappen om de **naam van het kenmerk aanvragen** uit te sluiten dat gelijk is **aan** **1=1**.

## <a name="finding-request-header-names"></a>Namen van de voorkoppen zoeken

Fiddler is opnieuw een handig hulpmiddel om namen van de aanvraagkoptekst te vinden. In de volgende schermafbeelding ziet u de kopteksten voor dit GET-verzoek, waaronder *Content-Type,* *User-Agent,* enzovoort.

![Fiddler](../media/web-application-firewall-troubleshoot/fiddler-2.png)

Een andere manier om aanvraag- en antwoordkoppen te bekijken, is door in de ontwikkelaarstools van Chrome te kijken. U op F12 drukken of met de rechtermuisknop op ->**Extra ontwikkelaars** **inspecteren** -> en het tabblad **Netwerk** selecteren. Een webpagina laden en op het verzoek klikken dat u wilt inspecteren.

![Chrome F12](../media/web-application-firewall-troubleshoot/chrome-f12.png)

## <a name="finding-request-cookie-names"></a>Namen van de aanvraagcookie zoeken

Als het verzoek cookies bevat, kan het tabblad **Cookies** worden geselecteerd om ze in Fiddler te bekijken.

## <a name="restrict-global-parameters-to-eliminate-false-positives"></a>Globale parameters beperken om fout-positieven te elimineren

- Inspectie aanvraaginstantie uitschakelen

   Door **de instantie van de aanvraag van Inspect** in te stellen om uit te schakelen, worden de aanvraaginstanties van al het verkeer niet beoordeeld door uw WAF. Dit kan handig zijn als u weet dat de verzoekende instanties niet kwaadaardig zijn voor uw toepassing.

   Door deze optie uit te schakelen, wordt alleen de aanvraaginstantie niet geïnspecteerd. De headers en cookies blijven geïnspecteerd, tenzij afzonderlijke headers worden uitgesloten met behulp van de functionaliteit van de uitsluitingslijst.

- Limieten voor bestandsgrootte

   Door de bestandsgrootte voor uw WAF te beperken, beperkt u de mogelijkheid dat er een aanval op uw webservers gebeurt. Door het uploaden van grote bestanden neemt het risico toe dat je backend wordt overstelpt. Het beperken van de bestandsgrootte tot een normale use case voor uw toepassing is gewoon een andere manier om aanvallen te voorkomen.

   > [!NOTE]
   > Als u weet dat uw app nooit een bestandsupload boven een bepaalde grootte nodig heeft, u dat beperken door een limiet in te stellen.

## <a name="firewall-metrics-waf_v1-only"></a>Firewallstatistieken (alleen WAF_v1)

Voor v1 Web Application Firewalls zijn de volgende statistieken nu beschikbaar in de portal: 

1. Aantal geblokkeerde aanvragen voor webtoepassingfirewall het aantal aanvragen dat is geblokkeerd
2. Aantal geblokkeerde regels voor webtoepassingsfirewall Alle regels die zijn overeenkomen **en** het verzoek zijn geblokkeerd
3. Totale regelverdeling van webtoepassingsfirewall Alle regels die tijdens de evaluatie zijn overeenkomen
     
Als u statistieken wilt inschakelen, selecteert u het tabblad **Statistieken** in de portal en selecteert u een van de drie statistieken.

## <a name="next-steps"></a>Volgende stappen

Zie [Firewall voor webtoepassingen configureren op Application Gateway](tutorial-restrict-web-traffic-powershell.md).
