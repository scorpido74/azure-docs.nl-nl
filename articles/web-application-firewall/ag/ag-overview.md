---
title: Inleiding tot Azure Web Application Firewall
titleSuffix: Azure Web Application Firewall
description: Dit artikel geeft een overzicht van webapplication firewall (WAF) op Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: 8d43851a6a546bd23881c4d0a72185ca03e63092
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983616"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure Web Application Firewall op Azure Application Gateway

Azure Web Application Firewall (WAF) op Azure Application Gateway biedt gecentraliseerde bescherming van uw webtoepassingen tegen veelvoorkomende exploits en kwetsbaarheden. Webapplicaties zijn steeds vaker het doelwit van kwaadaardige aanvallen die misbruik maken van bekende kwetsbaarheden. SQL-injectie en cross-site scripting behoren tot de meest voorkomende aanvallen.

WAF on Application Gateway is gebaseerd op [Core Rule Set (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1, 3.0 of 2.2.9 van het Open Web Application Security Project (OWASP). De WAF wordt automatisch bijgewerkt om bescherming tegen nieuwe kwetsbaarheden op te nemen, zonder dat extra configuratie nodig is. 

Alle waf-functies die hieronder worden vermeld, bestaan binnen een WAF-beleid. U meerdere beleidsregels maken en deze kunnen worden gekoppeld aan een toepassingsgateway, aan individuele listeners of aan routeringsregels op basis van paden op een toepassingsgateway. Op deze manier u indien nodig afzonderlijke beleidsregels hebben voor elke site achter uw application gateway. Zie [Een WAF-beleid maken voor](create-waf-policy-ag.md)meer informatie over waf-beleid .

   > [!NOTE]
   > Per site en per URI WAF-beleid staan in Public Preview. Dat betekent dat deze functie is onderworpen aan microsoft's aanvullende gebruiksvoorwaarden. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

![WAF-diagram application gateway](../media/ag-overview/waf1.png)

Application Gateway werkt als een application delivery controller (ADC). Het biedt Ssl-beëindiging (Secure Sockets Layer), sessieaffiniteit op basis van cookies, round-robin-belastingdistributie, op inhoud gebaseerde routering, de mogelijkheid om meerdere websites te hosten en beveiligingsverbeteringen.

Beveiligingsverbeteringen van de toepassingsgateway omvatten SSL-beleidsbeheer en end-to-end SSL-ondersteuning. Applicatiebeveiliging wordt versterkt door waf-integratie in Application Gateway. De combinatie beschermt uw webapplicaties tegen veelvoorkomende kwetsbaarheden. En het biedt een eenvoudig te configureren centrale locatie om te beheren.

## <a name="benefits"></a>Voordelen

In deze sectie worden de belangrijkste voordelen beschreven die WAF on Application Gateway biedt.

### <a name="protection"></a>Beveiliging

* Bescherm uw webapplicaties tegen kwetsbaarheden en aanvallen op het web zonder wijziging van back-endcode.

* Bescherm meerdere webapplicaties tegelijk. Een exemplaar van Application Gateway kan maximaal 40 websites hosten die worden beschermd door een firewall voor webtoepassingen.

* Aangepaste WAF-beleidsregels maken voor verschillende sites achter dezelfde WAF 

* Bescherm uw webtoepassingen tegen schadelijke bots met de regelset IP-reputatie (voorbeeld)

### <a name="monitoring"></a>Bewaking

* Monitor aanvallen op uw webapplicaties met behulp van een real-time WAF-logboek. Het logboek is geïntegreerd met [Azure Monitor](../../azure-monitor/overview.md) om WAF-waarschuwingen bij te houden en trends eenvoudig te volgen.

* De WAF van de Toepassingsgateway is geïntegreerd met Azure Security Center. Security Center biedt een centrale weergave van de beveiligingsstatus van al uw Azure-bronnen.

### <a name="customization"></a>Aanpassing

* Pas WAF-regels en regelgroepen aan uw toepassingsvereisten aan en elimineer false positives.

* Een WAF-beleid koppelen voor elke site achter uw WAF om sitespecifieke configuratie mogelijk te maken

* Aangepaste regels maken die aan de behoeften van uw toepassing voldoen

## <a name="features"></a>Functies

- SQL-injectiebescherming.
- Bescherming voor scripts op verschillende plaatsen.
- Bescherming tegen andere veelvoorkomende webaanvallen, zoals commando-injectie, HTTP-verzoeksmokkel, HTTP-responssplitsing en opname van externe bestanden.
- Bescherming tegen http-protocolschendingen.
- Bescherming tegen http-protocolafwijkingen, zoals ontbrekende hostuser-agent en accepteren headers.
- Bescherming tegen crawlers en scanners.
- Detectie van veelvoorkomende toepassingsverkeerde configuraties (bijvoorbeeld Apache en IIS).
- Configureerbare aanvraaggroottelimieten met onder- en bovengrenzen.
- Met uitsluitingslijsten u bepaalde aanvraagkenmerken weglaten uit een WAF-evaluatie. Een veelvoorkomend voorbeeld is Active Directory-ingevoegde tokens die worden gebruikt voor verificatie- of wachtwoordvelden.
- Maak aangepaste regels die passen bij de specifieke behoeften van uw toepassingen.
- Geofilterverkeer om bepaalde landen toegang te geven tot uw toepassingen of te blokkeren. (preview)
- Bescherm uw toepassingen tegen bots met de botmitigatieregelset. (preview)

## <a name="waf-policy"></a>WAF-beleid

Als u een webtoepassingsfirewall op een toepassingsgateway wilt inschakelen, moet u een WAF-beleid maken. In dit beleid bestaan alle beheerde regels, aangepaste regels, uitsluitingen en andere aanpassingen, zoals de limiet voor het uploaden van bestanden. 

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway ondersteunt drie regelsets: CRS 3.1, CRS 3.0 en CRS 2.2.9. Deze regels beschermen uw webtoepassingen tegen schadelijke activiteiten.

Zie [CRS-regelgroepen en -regels voor webtoepassingen firewall.](application-gateway-crs-rulegroups-rules.md)

### <a name="custom-rules"></a>Aangepaste regels

Application Gateway ondersteunt ook aangepaste regels. Met aangepaste regels u uw eigen regels maken, die worden geëvalueerd voor elk verzoek dat door WAF gaat. Deze regels hebben een hogere prioriteit dan de rest van de regels in de beheerde regelsets. Als aan een reeks voorwaarden is voldaan, wordt actie ondernomen om toe te staan of te blokkeren. 

De geomatch-operator is nu beschikbaar in een openbare preview voor aangepaste regels. Zie [aangepaste regels voor geomatch](custom-waf-rules-overview.md#geomatch-custom-rules-preview) voor meer informatie.

> [!NOTE]
> De geomatch-operator voor aangepaste regels bevindt zich momenteel in een openbare preview en wordt voorzien van een preview-serviceniveauovereenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zie Aangepaste regels voor [toepassingsgateway voor](custom-waf-rules-overview.md) meer informatie over aangepaste regels.

### <a name="bot-mitigation-preview"></a>Bot mitigatie (preview)

Een beheerde Bot-beveiligingsregelset kan worden ingeschakeld voor uw WAF om aanvragen van bekende schadelijke IP-adressen te blokkeren of te registreren, naast de beheerde regelset. De IP-adressen zijn afkomstig van de Microsoft Threat Intelligence-feed. Intelligent Security Graph is de drijvende kracht achter Microsoft-bedreigingsinformatie en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!NOTE]
> Bot bescherming regel ingesteld is momenteel in openbare preview en is voorzien van een preview service level overeenkomst. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als Bot Protection is ingeschakeld, worden binnenkomende aanvragen die overeenkomen met de client-IP's van Malicious Bot aangemeld bij het firewalllogboek, zie hieronder meer informatie. U WAF-logboeken openen via opslagaccount, gebeurtenishub of logboekanalyse. 

### <a name="waf-modes"></a>WAF-modi

De WAF van de toepassingsgateway kan worden geconfigureerd om in de volgende twee modi uit te voeren:

* **Detectiemodus:** controleert en registreert alle dreigingswaarschuwingen. U schakelt logboekregistratievoor Application Gateway in de sectie **Diagnostische gegevens in.** U moet er ook voor zorgen dat het WAF-logboek is geselecteerd en ingeschakeld. De firewall van de webtoepassing blokkeert geen binnenkomende aanvragen wanneer deze in de detectiemodus worden uitgevoerd.
* **Preventiemodus**: Blokkeert inbraken en aanvallen die de regels detecteren. De aanvaller ontvangt een uitzondering voor '403 ongeautoriseerde toegang' en de verbinding is gesloten. Preventie modus registreert dergelijke aanvallen in de WAF logs.

> [!NOTE]
> Het wordt aanbevolen dat u een nieuw geïmplementeerde WAF in de detectiemodus voor een korte periode in een productieomgeving uitvoert. Dit biedt de mogelijkheid om [firewalllogboeken](../../application-gateway/application-gateway-diagnostics.md#firewall-log) te verkrijgen en eventuele uitzonderingen of [aangepaste regels](./custom-waf-rules-overview.md) bij te werken voordat u de preventiemodus overschakelt. Dit kan helpen het voorkomen van onverwacht geblokkeerd verkeer te verminderen.

### <a name="anomaly-scoring-mode"></a>Anomaliescoremodus

OWASP heeft twee modi om te beslissen of het verkeer moet worden geblokkeerd: traditionele modus en anomaly scoring-modus.

In de traditionele modus wordt verkeer dat overeenkomt met elke regel onafhankelijk van een andere regel overeenkomen. Deze modus is gemakkelijk te begrijpen. Maar het gebrek aan informatie over hoeveel regels overeenkomen met een specifiek verzoek is een beperking. Dus, Anomaly Scoring mode werd geïntroduceerd. Het is de standaard instelling voor OWASP 3. *x*.

In de modus Anomaliescore wordt verkeer dat overeenkomt met een regel niet onmiddellijk geblokkeerd wanneer de firewall zich in de preventiemodus bevindt. Regels hebben een bepaalde ernst: *kritiek*, *fout,* *waarschuwing*of *kennisgeving*. Die ernst is van invloed op een numerieke waarde voor het verzoek, dat de Anomaliescore wordt genoemd. Bijvoorbeeld, een *waarschuwing* regel match draagt 3 bij aan de score. Kritieke *Critical* regelgelijke draagt 5 bij.

|Severity  |Waarde  |
|---------|---------|
|Kritiek     |5|
|Fout        |4|
|Waarschuwing      |3|
|Kennisgeving       |2|

Er is een drempel van 5 voor de Anomalie Score om verkeer te blokkeren. Eén overeenkomst *met kritieke* regel is dus voldoende voor de WAF van de Toepassingsgateway om een aanvraag te blokkeren, zelfs in de preventiemodus. Maar een *waarschuwing* regel wedstrijd verhoogt alleen de Anomalie Score met 3, dat is niet genoeg op zichzelf om het verkeer te blokkeren.

> [!NOTE]
> Het bericht dat wordt geregistreerd wanneer een WAF-regel overeenkomt met het verkeer, bevat de actiewaarde 'Geblokkeerd'. Maar het verkeer is eigenlijk alleen geblokkeerd voor een Anomalie Score van 5 of hoger.  

### <a name="waf-monitoring"></a>WAF-monitoring

Het bewaken van de status van uw toepassingsgateway is belangrijk. Het bewaken van de status van uw WAF en de toepassingen die worden beschermd, worden ondersteund door integratie met Azure Security Center, Azure Monitor en Azure Monitor-logboeken.

![WAF-diagnosediagram voor toepassingsgateway](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Toepassingsgatewaylogboeken zijn geïntegreerd met [Azure Monitor.](../../azure-monitor/overview.md) Hiermee u diagnostische informatie bijhouden, waaronder WAF-waarschuwingen en logboeken. U hebt toegang tot deze mogelijkheid op het tabblad **Diagnostische gegevens** in de toepassingsgatewaybron in de portal of rechtstreeks via Azure Monitor. Zie Application Gateway diagnostics voor meer informatie over het inschakelen van [logboeken.](../../application-gateway/application-gateway-diagnostics.md)

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) helpt u bedreigingen te voorkomen, detecteren en erop te reageren. Het biedt meer inzicht in en controle over de beveiliging van uw Azure-bronnen. Application Gateway is [geïntegreerd met Security Center.](../../application-gateway/application-gateway-integration-security-center.md) Security Center scant uw omgeving om onbeschermde webapplicaties te detecteren. Het kan Application Gateway WAF aanbevelen om deze kwetsbare bronnen te beschermen. U maakt de firewalls rechtstreeks vanuit Security Center. Deze WAF-exemplaren zijn geïntegreerd met Security Center. Ze sturen waarschuwingen en gezondheidsinformatie naar Security Center voor rapportage.

![Overzichtsvenster Beveiligingscentrum](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel is een schaalbare, cloud-native SIEM (Security Information Event Management) en security orchestration automated response (SOAR) oplossing. Azure Sentinel levert intelligente beveiligingsanalyses en bedreigingsinformatie in de hele onderneming en biedt één oplossing voor waarschuwingsdetectie, zichtbaarheid van bedreigingen, proactieve jacht en bedreigingsrespons.

Met de ingebouwde Azure WAF-firewallgebeurtenissenwerkmap krijgt u een overzicht van de beveiligingsgebeurtenissen op uw WAF. Dit omvat gebeurtenissen, overeenkomende en geblokkeerde regels en al het andere dat wordt aangemeld bij de firewalllogboeken. Zie meer over het inloggen hieronder. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Logboekregistratie

Application Gateway WAF biedt gedetailleerde rapportage over elke bedreiging die het detecteert. Logboekregistratie is geïntegreerd met Azure Diagnostics-logboeken. Waarschuwingen worden geregistreerd in de .json-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure Monitor-logboeken.](../../azure-monitor/insights/azure-networking-analytics.md)

![Application Gateway-diagnose registreert vensters](../media/ag-overview/waf2.png)

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupId}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{appGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
    {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.145",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343"
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/globalWafPolicy",
      "policyScope": "Global",
      "policyScopeName": " Global "
    }
  }
} 

```

## <a name="application-gateway-waf-sku-pricing"></a>Prijzen voor de Application Gateway WAF-voorraadeenheid

De prijsmodellen zijn verschillend voor de WAF_v1 en WAF_v2 SKU's. Zie de [prijspagina van Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

- Aan de slag met [het maken van een WAF-beleid](create-waf-policy-ag.md)
- Meer informatie over [waf beheerde regels](application-gateway-crs-rulegroups-rules.md)
- Meer informatie over [aangepaste regels](custom-waf-rules-overview.md)
- Meer informatie over [Web Application Firewall op Azure Front Door](../afds/afds-overview.md)

