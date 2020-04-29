---
title: Inleiding tot de firewall van Azure Web Application
titleSuffix: Azure Web Application Firewall
description: Dit artikel bevat een overzicht van Web Application Firewall (WAF) op Application Gateway
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 11/14/2019
ms.author: victorh
ms.topic: overview
ms.openlocfilehash: e0e5c143e619b1c381a4a618a811883ad189719b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314361"
---
# <a name="azure-web-application-firewall-on-azure-application-gateway"></a>Azure Web Application firewall op Azure-toepassing gateway

Azure Web Application firewall (WAF) op Azure-toepassing gateway biedt gecentraliseerde beveiliging van uw webtoepassingen van veelvoorkomende aanvallen en beveiligings problemen. Webtoepassingen worden steeds gericht op kwaad aardige aanvallen die veelvoorkomende beveiligings problemen misbruiken. SQL-injectie en cross-site scripting zijn onder de meest voorkomende aanvallen.

WAF op Application Gateway is gebaseerd op de [Basisrule set (CRS)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1, 3,0 of 2.2.9 van het open Web Application Security-project (OWASP). De WAF wordt automatisch bijgewerkt zodat deze bescherming biedt tegen nieuwe beveiligings lekken, zonder dat er aanvullende configuratie nodig is. 

Alle WAF-functies die hieronder worden weer gegeven, bevinden zich in een WAF-beleid. U kunt meerdere beleids regels maken en deze kunnen worden gekoppeld aan een Application Gateway, aan individuele listeners of aan op pad gebaseerde routerings regels op een Application Gateway. Op deze manier kunt u, indien nodig, afzonderlijke beleids regels instellen voor elke site achter uw Application Gateway. Zie [een WAF-beleid maken](create-waf-policy-ag.md)voor meer informatie over WAF-beleid.

   > [!NOTE]
   > WAF-beleid per site en per URI zijn beschikbaar als open bare preview. Dit betekent dat deze functie is onderhevig aan de aanvullende gebruiks voorwaarden van micro soft. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

![WAF diagram van Application Gateway](../media/ag-overview/waf1.png)

Application Gateway fungeert als een ADC (Application Delivery controller). Het biedt Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), beëindiging, sessie affiniteit op basis van cookies, Round Robin-taak verdeling, route ring op basis van inhoud, de mogelijkheid om meerdere websites en beveiligings uitbreidingen te hosten.

Application Gateway beveiligings verbeteringen zijn onder andere TLS-beleids beheer en end-to-end TLS-ondersteuning. De beveiliging van toepassingen wordt versterkt door integratie van WAF in Application Gateway. Met deze combi natie worden uw webtoepassingen beschermd tegen veelvoorkomende beveiligings problemen. En het biedt een eenvoudig te configureren centrale locatie voor het beheren van.

## <a name="benefits"></a>Voordelen

In deze sectie worden de belangrijkste voor delen beschreven die WAF in Application Gateway biedt.

### <a name="protection"></a>Beveiliging

* Bescherm uw webtoepassingen tegen beveiligings lekken en aanvallen zonder wijzigingen aan back-end-code.

* Meerdere webtoepassingen tegelijk beveiligen. Een instantie van Application Gateway kan Maxi maal 40 websites hosten die worden beveiligd door een Web Application Firewall.

* Aangepaste WAF-beleids regels maken voor verschillende sites achter dezelfde WAF 

* Uw webtoepassingen beveiligen tegen kwaad aardige bots met de IP-reputatie-ruleset (preview)

### <a name="monitoring"></a>Bewaking

* Bewaak aanvallen op uw webtoepassingen met behulp van een real-time WAF-logboek. Het logboek is geïntegreerd met [Azure monitor](../../azure-monitor/overview.md) om WAF-waarschuwingen bij te houden en trends eenvoudig te bewaken.

* De Application Gateway WAF is geïntegreerd met Azure Security Center. Security Center biedt een centraal overzicht van de beveiligings status van al uw Azure-resources.

### <a name="customization"></a>Aanpassing

* WAF regels en regel groepen aanpassen aan de vereisten van uw toepassing en valse positieven elimineren.

* Een WAF-beleid koppelen voor elke site achter uw WAF om site-specifieke configuratie te bieden

* Aangepaste regels maken die voldoen aan de behoeften van uw toepassing

## <a name="features"></a>Functies

- SQL-injectie beveiliging.
- Beveiliging voor cross-site scripting.
- Bescherming tegen andere veelvoorkomende webaanvalen, zoals het injecteren van opdrachten, HTTP-aanvragen smuggling, het splitsen van HTTP-antwoorden en het insluiting van externe bestanden.
- Beveiliging tegen schendingen van het HTTP-protocol.
- Bescherming tegen afwijkingen van het HTTP-protocol, zoals de ontbrekende host user-agent en Accept-headers.
- Bescherming tegen crawlers en scanners.
- Detectie van veelvoorkomende onjuiste configuraties van toepassingen (bijvoorbeeld Apache en IIS).
- Configureer bare limieten voor aanvraag grootte met boven-en ondergrenzen.
- Met uitsluitings lijsten kunt u bepaalde kenmerken van aanvragen weglaten uit een WAF-evaluatie. Een voor beeld hiervan is Active Directory ingevoegde tokens die worden gebruikt voor verificatie-of wachtwoord velden.
- Aangepaste regels maken voor de specifieke behoeften van uw toepassingen.
- Geo-filtert verkeer om te voor komen of blok keren dat bepaalde landen toegang krijgen tot uw toepassingen. (preview)
- Bescherm uw toepassingen tegen bots met de Rules-regelset voor beperking van bot. (preview)

## <a name="waf-policy"></a>WAF-beleid

Als u een Web Application firewall wilt inschakelen op een Application Gateway, moet u een WAF-beleid maken. Dit beleid is waar alle beheerde regels, aangepaste regels, uitsluitingen en andere aanpassingen, zoals de upload limiet voor bestanden, bestaan. 

### <a name="core-rule-sets"></a>Core Rule Sets

Application Gateway ondersteunt drie regel sets: CRS 3,1, CRS 3,0 en CRS 2.2.9. Met deze regels worden uw webtoepassingen beschermd tegen schadelijke activiteiten.

Zie voor meer informatie, [CRS-regel groepen en-regels voor Web Application firewall](application-gateway-crs-rulegroups-rules.md).

### <a name="custom-rules"></a>Aangepaste regels

Application Gateway biedt ook ondersteuning voor aangepaste regels. Met aangepaste regels kunt u uw eigen regels maken, die worden geëvalueerd voor elke aanvraag die via WAF wordt door gegeven. Deze regels bevatten een hogere prioriteit dan de rest van de regels in de beheerde regel sets. Als aan een set voor waarden wordt voldaan, wordt er een actie uitgevoerd om toe te staan of te blok keren. 

De operator geomatch is nu beschikbaar in open bare Preview voor aangepaste regels. Zie de [aangepaste regels voor geomatching](custom-waf-rules-overview.md#geomatch-custom-rules-preview) voor meer informatie.

> [!NOTE]
> De operator geomatch voor aangepaste regels bevindt zich momenteel in de open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Zie [aangepaste regels voor Application Gateway](custom-waf-rules-overview.md) voor meer informatie over aangepaste regels.

### <a name="bot-mitigation-preview"></a>Beperking van bot (preview-versie)

Een set Managed bot Protection kan worden ingeschakeld voor uw WAF om aanvragen van bekende schadelijke IP-adressen, naast de beheerde ruleSet, te blok keren of te registreren. De IP-adressen worden vanuit de micro soft Threat Intelligence-feed gebrond. Intelligent Security Graph voorziet in micro soft Threat Intelligence en wordt gebruikt door meerdere services, waaronder Azure Security Center.

> [!NOTE]
> De set bot Protection-regelset bevindt zich momenteel in een open bare preview en wordt weer gegeven met een preview-service level agreement. De reden hiervoor is dat bepaalde functies mogelijk niet worden ondersteund of beperkte mogelijkheden hebben. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Als bot-beveiliging is ingeschakeld, worden inkomende aanvragen die overeenkomen met de client Ip's van de schadelijke bot, geregistreerd in het firewall logboek. Zie hieronder voor meer informatie. U kunt toegang krijgen tot WAF-logboeken vanuit het opslag account, Event Hub of log Analytics. 

### <a name="waf-modes"></a>WAF modi

De Application Gateway WAF kan worden geconfigureerd om te worden uitgevoerd in de volgende twee modi:

* **Detectie modus**: Hiermee worden alle bedreigings waarschuwingen gecontroleerd en geregistreerd. U schakelt logboek registratie diagnose in voor Application Gateway in de sectie **Diagnostische gegevens** . U moet er ook voor zorgen dat het WAF-logboek is geselecteerd en ingeschakeld. Web Application firewall blokkeert geen binnenkomende aanvragen wanneer deze in de detectie modus wordt uitgevoerd.
* **Preventie modus**: blokkeert indringingen en aanvallen die de regels detecteren. De aanvaller ontvangt een uitzonde ring "403 niet-geautoriseerde toegang" en de verbinding is gesloten. Met preventie modus worden dergelijke aanvallen in de WAF-logboeken vastgelegd.

> [!NOTE]
> Het is raadzaam dat u een nieuw geïmplementeerde WAF uitvoert in de detectie modus voor een korte periode in een productie omgeving. Dit biedt de mogelijkheid om [firewall logboeken](../../application-gateway/application-gateway-diagnostics.md#firewall-log) te verkrijgen en eventuele uitzonde ringen of [aangepaste regels](./custom-waf-rules-overview.md) bij te werken vóór de overgang naar de modus voor preventie. Hierdoor kan het aantal onverwacht geblokkeerd verkeer worden verminderd.

### <a name="anomaly-scoring-mode"></a>Afwijkende Score modus

OWASP heeft twee modi om te bepalen of verkeer moet worden geblokkeerd: traditionele modus en afwijkings modus.

In de traditionele modus wordt verkeer dat overeenkomt met elke regel, onafhankelijk van andere regel overeenkomsten beschouwd. Deze modus is eenvoudig te begrijpen. Maar het ontbreken van informatie over het aantal regels dat overeenkomt met een specifieke aanvraag is een beperking. Daarom is de afwijkende Score modus geïntroduceerd. Dit is de standaard waarde voor OWASP 3. *x*.

In de afwijkende Score modus wordt verkeer dat overeenkomt met een regel niet onmiddellijk geblokkeerd wanneer de firewall in de modus preventie wordt uitgevoerd. Regels hebben een bepaalde ernst: *kritiek*, *fout*, *waarschuwing*of *kennisgeving*. Deze ernst is van invloed op een numerieke waarde voor de aanvraag, die de afwijkings score wordt genoemd. Een voor beeld van een *waarschuwings* regel die overeenkomt met 3 bij de score. Een *kritieke* regel overeenkomst draagt bij tot 5.

|Severity  |Waarde  |
|---------|---------|
|Kritiek     |5|
|Fout        |4|
|Waarschuwing      |3|
|Zie       |2|

Er is een drempel van 5 voor de afwijkings Score om verkeer te blok keren. Daarom is een enkele *essentiële* regel overeenkomst voldoende voor de Application Gateway WAF om een aanvraag te blok keren, zelfs in de modus preventie. Maar een overeenkomst met een *waarschuwing* neemt alleen de afwijkings Score met 3 toe, wat niet voldoende is om het verkeer te blok keren.

> [!NOTE]
> Het bericht dat wordt geregistreerd wanneer een WAF-regel overeenkomt met verkeer bevat de actie waarde ' geblokkeerd '. Het verkeer wordt echter alleen in werkelijkheid geblokkeerd voor een afwijkings Score van 5 of hoger.  

### <a name="waf-monitoring"></a>WAF-controle

Het bewaken van de status van uw toepassingsgateway is belangrijk. Het controleren van de status van uw WAF en de toepassingen die deze beveiligt, wordt ondersteund door integratie met Azure Security Center, Azure Monitor en Azure Monitor Logboeken.

![Diagram van Application Gateway WAF diagnostische gegevens](../media/ag-overview/diagnostics.png)

#### <a name="azure-monitor"></a>Azure Monitor

Application Gateway-logboeken zijn geïntegreerd met [Azure monitor](../../azure-monitor/overview.md). Zo kunt u Diagnostische gegevens bijhouden, met inbegrip van WAF-waarschuwingen en-Logboeken. U kunt deze mogelijkheid gebruiken op het tabblad **Diagnostische gegevens** in de Application Gateway resource in de portal of rechtstreeks via Azure monitor. Zie [Application Gateway Diagnostics](../../application-gateway/application-gateway-diagnostics.md)(Engelstalig) voor meer informatie over het inschakelen van Logboeken.

#### <a name="azure-security-center"></a>Azure Security Center

[Security Center](../../security-center/security-center-intro.md) helpt u bij het voor komen, detecteren en reageren op bedreigingen. Het biedt meer inzicht in en controle over de beveiliging van uw Azure-resources. Application Gateway is [geïntegreerd met Security Center](../../application-gateway/application-gateway-integration-security-center.md). Security Center scant uw omgeving om niet-beveiligde webtoepassingen te detecteren. Het kan Application Gateway WAF aanbevelen om deze kwets bare bronnen te beveiligen. U maakt de firewalls rechtstreeks van Security Center. Deze WAF-instanties zijn geïntegreerd met Security Center. Ze sturen waarschuwingen en status informatie naar Security Center voor rapportage.

![Venster Overzicht van Security Center](../media/ag-overview/figure1.png)

#### <a name="azure-sentinel"></a>Azure Sentinel

Microsoft Azure Sentinel is een schaal bare, Cloud-native, SIEM-oplossing (Security Information Event Management) en via (Security Orchestration Automated Response). Azure Sentinel levert intelligente beveiligings analyses en bedreigings informatie over de hele onderneming, waardoor er één oplossing is voor waarschuwings detectie, zicht baarheid van bedreigingen, proactieve jacht en reactie op bedreigingen.

Met de ingebouwde werkmap van de Azure WAF firewall-gebeurtenis kunt u een overzicht krijgen van de beveiligings gebeurtenissen op uw WAF. Dit omvat gebeurtenissen, overeenkomende en geblokkeerde regels, en alles wat u kunt vastleggen in de logboeken van de firewall. Zie hieronder voor meer informatie over logboek registratie. 


![Sentinel](../media/ag-overview/sentinel.png)

#### <a name="logging"></a>Logboekregistratie

Application Gateway WAF biedt gedetailleerde rapportage over elke bedreiging die wordt gedetecteerd. Logboek registratie is geïntegreerd met Azure Diagnostics-Logboeken. Waarschuwingen worden vastgelegd in de JSON-indeling. Deze logboeken kunnen worden geïntegreerd met [Azure monitor-logboeken](../../azure-monitor/insights/azure-networking-analytics.md).

![Application Gateway Diagnostische logboeken Vensters](../media/ag-overview/waf2.png)

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

De prijs modellen verschillen voor de WAF_v1 en WAF_v2 Sku's. Ga naar de pagina met [prijzen voor Application Gateway](https://azure.microsoft.com/pricing/details/application-gateway/) voor meer informatie. 

## <a name="next-steps"></a>Volgende stappen

- Aan de slag met het [maken van een WAF-beleid](create-waf-policy-ag.md)
- Meer informatie over door [WAF beheerde regels](application-gateway-crs-rulegroups-rules.md)
- Meer informatie over [aangepaste regels](custom-waf-rules-overview.md)
- Meer informatie over [Web Application firewall op de voor deur van Azure](../afds/afds-overview.md)

