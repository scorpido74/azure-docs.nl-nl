---
title: Automatisch schalen en zone-redundantie in Application Gateway v2
description: In dit artikel wordt de Azure Application-Standard_v2 en WAF_v2 SKU geïntroduceerd, waaronder functies voor automatisch schalen en zoneredundant.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 4cd2969f9a56c96af2b2c6db216f6829a080260c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371277"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatisch schalen en zone-redundantie in Application Gateway v2 

Application Gateway en Web Application Firewall (WAF) zijn ook beschikbaar onder een Standard_v2 en WAF_v2 SKU. De v2 SKU biedt prestatieverbeteringen en voegt ondersteuning toe voor kritieke nieuwe functies zoals automatisch schalen, zoneredundantie en ondersteuning voor statische VIP's. Bestaande functies onder de Standaard en WAF SKU worden nog steeds ondersteund in de nieuwe v2 SKU, met een paar uitzonderingen in [de vergelijkingssectie.](#differences-with-v1-sku)

De nieuwe v2 SKU bevat de volgende verbeteringen:

- **Automatisch schalen:** Application Gateway- of WAF-implementaties onder de autoscaling SKU kunnen worden opgeschaald of omlaag op basis van veranderende verkeersbelastingspatronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. Deze SKU biedt echte elasticiteit. In de Standard_v2 en WAF_v2 SKU kan Application Gateway zowel in vaste capaciteit (automatisch schalen uitgeschakeld) als in de modus Automatisch schalen werken. De modus Vaste capaciteit is handig voor scenario's met consistente en voorspelbare workloads. De modus Automatisch schalen is gunstig in toepassingen die variantie in toepassingsverkeer zien.
- **Zoneredundantie:** een Application Gateway- of WAF-implementatie kan meerdere beschikbaarheidszones omvatten, waardoor u geen afzonderlijke toepassingsgateway-exemplaren in elke zone hoeft in te richten met een trafficmanager. U één zone of meerdere zones kiezen waar toepassingsgateway-exemplaren worden geïmplementeerd, waardoor deze beter bestand is tegen zonefouten. De backendpool voor toepassingen kan op dezelfde manier worden verdeeld over beschikbaarheidszones.

  Zoneredundantie is alleen beschikbaar wanneer Azure-zones beschikbaar zijn. In andere regio's worden alle andere functies ondersteund. Zie [Wat zijn beschikbaarheidszones in Azure voor](../availability-zones/az-overview.md#services-support-by-region) meer informatie?
- **Static VIP**: Application Gateway v2 SKU ondersteunt het statische VIP-type uitsluitend. Dit zorgt ervoor dat de VIP die is gekoppeld aan de toepassingsgateway niet verandert voor de levenscyclus van de implementatie, zelfs niet na een herstart.  Er is geen statische VIP in v1, dus u moet de URL van de toepassingsgateway gebruiken in plaats van het IP-adres voor het routeren van domeinnamen naar App Services via de toepassingsgateway.
- **Header Herschrijven:** Met Application Gateway u HTTP-aanvraag- en antwoordkoppen toevoegen, verwijderen of bijwerken met v2 SKU. Zie [HTTP-headers opnieuw schrijven met Application Gateway voor](rewrite-http-headers.md) meer informatie
- **Key Vault Integration**: Application Gateway v2 ondersteunt integratie met Key Vault voor servercertificaten die zijn gekoppeld aan https-ingeschakelde luisteraars. Zie [SSL-beëindiging met Key Vault-certificaten voor](key-vault-certs.md)meer informatie.
- **Azure Kubernetes Service Ingress Controller**: Met de Application Gateway v2 Ingress Controller kan de Azure Application Gateway worden gebruikt als de ingress voor een Azure Kubernetes Service (AKS) dat bekend staat als AKS-cluster. Zie [Wat is Application Gateway Ingress Controller voor](ingress-controller-overview.md)meer informatie? .
- **Prestatieverbeteringen**: De v2 SKU biedt tot 5x betere SSL-offloadprestaties in vergelijking met de Standard/WAF SKU.
- **Snellere implementatie- en updatetijd** De v2 SKU biedt snellere implementatie- en updatetijd in vergelijking met Standard/WAF SKU. Dit omvat ook WAF-configuratiewijzigingen.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Ondersteunde regio’s

De Standard_v2 en WAF_v2 SKU is beschikbaar in de volgende regio's: Noord-Centraal VS, Zuid-Centraal VS, West-VS, West-VS, Oost-VS 2, Centraal-VS, Noord-Europa, West-Europa, Zuidoost-Azië, Frankrijk Centraal, Verenigd Koninkrijk, Japan-West, Japan West, Australië Oost , Australië Zuidoost, Brazilië Zuid, Canada Centraal, Canada Oost-Azië, Korea Centraal, Korea Zuid, Verenigd Koninkrijk Zuid, Centraal-India, West-India, Zuid-India.

## <a name="pricing"></a>Prijzen

Met de v2 SKU wordt het prijsmodel aangedreven door het verbruik en is het niet langer gekoppeld aan instantietellingen of -maten. De v2 SKU-prijzen hebben twee componenten:

- **Vaste prijs** - Dit is per uur (of gedeeltelijk uur) prijs voor het inrichten van een Standard_v2 of WAF_v2 Gateway. Houd er rekening mee dat 0 extra minimumexemplaren nog steeds zorgen voor een hoge beschikbaarheid van de service die altijd is inbegrepen bij de vaste prijs.
- **Capaciteit Eenheidsprijs** - Dit is een op verbruik gebaseerde kosten die in rekening wordt gebracht naast de vaste kosten. Capaciteitseenheidskosten worden ook elk uur of gedeeltelijk berekend. Er zijn drie dimensies aan capaciteitseenheid - compute unit, permanente verbindingen en doorvoer. Compute unit is een maat voor de processorcapaciteit verbruikt. Factoren die van invloed zijn op de compute unit zijn TLS-verbindingen per seconde, URL Rewrite berekeningen en WAF-regelverwerking. Permanente verbinding is een maat voor gevestigde TCP-verbindingen met de toepassingsgateway in een bepaald factureringsinterval. Doorvoer is gemiddeld Megabits/sec verwerkt door het systeem in een bepaald factureringsinterval.  De facturering gebeurt op capaciteitseenheidsniveau voor alles boven het aantal gereserveerde instanties.

Elke capaciteitseenheid bestaat uit hoogstens: 1 rekeneenheid, of 2500 permanente verbindingen, of 2,22-Mbps doorvoer.

Richtlijnen voor compute unit:

- **Standard_v2** - Elke compute unit is geschikt voor ongeveer 50 verbindingen per seconde met RSA 2048-bit key TLS-certificaat.
- **WAF_v2** - Elke compute unit kan ongeveer 10 gelijktijdige aanvragen per seconde ondersteunen voor 70-30% mix van verkeer met 70% aanvragen minder dan 2 KB GET/POST en hoger blijven. De WAF-prestaties worden momenteel niet beïnvloed door de reactiegrootte.

> [!NOTE]
> Elke instantie kan momenteel ongeveer 10 capaciteitseenheden ondersteunen.
> Het aantal aanvragen dat een rekeneenheid kan verwerken, is afhankelijk van verschillende criteria, zoals de grootte van de TLS-certificaatsleutel, het algoritme voor sleuteluitwisseling, header-herschrijft en in het geval van de inkomende aanvraaggrootte van WAF. We raden u aan toepassingstests uit te voeren om de aanvraagsnelheid per rekeneenheid te bepalen. Zowel de capaciteitseenheid als de rekeneenheid worden als statistiek beschikbaar gesteld voordat de facturering begint.

De volgende tabel toont voorbeeldprijzen en zijn alleen voor illustratiedoeleinden.

**Prijzen in het Oosten van de VS:**

|              SKU-naam                             | Vaste prijs ($/hr)  | Prijs per eenheid van capaciteit ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0.0080                          |
| WAF_v2                                            |    0.36             | 0.0144                          |

Zie de [prijspagina](https://azure.microsoft.com/pricing/details/application-gateway/)voor meer prijsinformatie. 

**Voorbeeld 1**

Een Application Gateway-Standard_v2 is ingericht zonder automatisch te schalen in de handmatige schalingmodus met een vaste capaciteit van vijf exemplaren.

Vaste prijs = 744(uren) * $0,20 = $148,8 <br>
Capaciteitseenheden = 744 (uren) * 10 capaciteitseenheid per exemplaar * 5 exemplaren * $0,008 per capaciteitseenheiduur = $297,6

Totale prijs = $148.8 + $297.6 = $446.4

**Voorbeeld 2**

Een Application Gateway-standard_v2 is ingericht voor een maand, met nul minimale exemplaren, en gedurende deze tijd ontvangt het 25 nieuwe SSL-verbindingen per seconde, gemiddeld 8,88 Mbps gegevensoverdracht. Ervan uitgaande dat verbindingen van korte duur zijn, zou uw prijs zijn:

Vaste prijs = 744(uren) * $0,20 = $148,8

Capaciteit eenheidsprijs = 744(uren) * Max (25/50 compute unit voor verbindingen/sec, 8,88/2,22 capaciteitseenheid voor doorvoer) * $0,008 = 744 * 4 * 0,008 = $23.81

Totale prijs = $148.8+23.81 = $172.61

Zoals u zien, wordt u alleen gefactureerd voor vier capaciteitseenheden, niet voor de hele instantie. 

> [!NOTE]
> De functie Max retourneert de grootste waarde in een paar waarden.


**Voorbeeld 3**

Een Application Gateway-standard_v2 is ingericht voor een maand, met een minimum van vijf exemplaren. Ervan uitgaande dat er geen verkeer en verbindingen zijn van korte duur, zou uw prijs zijn:

Vaste prijs = 744(uren) * $0,20 = $148,8

Capaciteit eenheidsprijs = 744(uren) * Max (0/50 rekeneenheid voor verbindingen/sec, 0/2,22 capaciteitseenheid voor doorvoer) * $0,008 = 744 * 50 * 0,008 = $297,60

Totale prijs = $148.80+297.60 = $446.4

In dit geval wordt u gefactureerd voor het geheel van de vijf instanties, ook al is er geen verkeer.

**Voorbeeld 4**

Een Application Gateway-standard_v2 is ingericht voor een maand, met een minimum van vijf exemplaren, maar deze keer is er een gemiddelde van 125-mbps gegevensoverdracht, en 25 SSL-verbindingen per seconde. Ervan uitgaande dat er geen verkeer en verbindingen zijn van korte duur, zou uw prijs zijn:

Vaste prijs = 744(uren) * $0,20 = $148,8

Capaciteit eenheidsprijs = 744(uren) * Max (25/50 compute unit voor verbindingen/sec, 125/2,22 capaciteitseenheid voor doorvoer) * $0,008 = 744 * 57 * 0,008 = $339,26

Totale prijs = $148.80+339.26 = $488.06

In dit geval wordt u gefactureerd voor de volledige vijf exemplaren, plus zeven capaciteitseenheden (dat is 7/10 van een instantie).  

**Voorbeeld 5**

Een application gateway-WAF_v2 is ingericht voor een maand. Gedurende deze tijd ontvangt het 25 nieuwe SSL-verbindingen per seconde, gemiddeld 8,88 Mbps gegevensoverdracht en doet 80 aanvragen per seconde. Ervan uitgaande dat verbindingen van korte duur zijn en dat compute unit-berekening voor de toepassing 10 RPS per rekeneenheid ondersteunt, zou uw prijs zijn:

Vaste prijs = 744(uren) * $0,36 = $267,84

Capaciteit eenheidsprijs = 744(uur) * Max (rekeneenheid Max(25/50 voor verbindingen/sec, 80/10 WAF RPS), 8,88/2,22 capaciteitseenheid voor doorvoer) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Totale prijs = $267.84 + $85.71 = $353.55

> [!NOTE]
> De functie Max retourneert de grootste waarde in een paar waarden.

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway en WAF v2 schalen

Application Gateway en WAF kunnen worden geconfigureerd om te schalen in twee modi:

- **Automatisch schalen** - Met automatisch schalen ingeschakeld, worden de Application Gateway en WAF v2 SKU's opgeschaald of omlaag op basis van de vereisten voor toepassingsverkeer. Deze modus biedt een betere elasticiteit van uw toepassing en elimineert de noodzaak om de grootte van de toepassingsgateway of het aantal instance's te raden. Met deze modus u ook kosten besparen door dat de gateway niet hoeft te worden uitgevoerd op een piek in gerichte capaciteit voor verwachte maximale verkeersbelasting. U moet een minimum en optioneel maximaal aantal instanties opgeven. De minimale capaciteit zorgt ervoor dat Application Gateway en WAF v2 niet onder het opgegeven minimumaantal instance's vallen, zelfs als er geen verkeer is. Elke instantie telt als 10 extra gereserveerde capaciteitseenheden. Nul betekent geen gereserveerde capaciteit en is puur automatisch schalen in de natuur. Houd er rekening mee dat nul extra minimumexemplaren nog steeds zorgen voor een hoge beschikbaarheid van de service die altijd is inbegrepen bij de vaste prijs. U ook optioneel een maximaal aantal exemplaren opgeven, wat ervoor zorgt dat de Toepassingsgateway niet verder schaalt dan het opgegeven aantal exemplaren. Er worden nog steeds kosten in rekening gebracht voor de hoeveelheid verkeer die door de Gateway wordt aangewend. De instantietellingen kunnen variëren van 0 tot 125. De standaardwaarde voor het maximale aantal instance's is 20 indien niet opgegeven.
- **Handleiding** - U ook handmatige modus kiezen waarin de gateway niet automatisch wordt geschaald. In deze modus, als er meer verkeer is dan wat Application Gateway of WAF aankan, kan dit leiden tot verkeersverlies. Bij de handmatige modus is het opgeven van het aantal instance's verplicht. Het aantal exemplaren kan variëren van 1 tot 125 exemplaren.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Functievergelijking tussen v1 SKU en v2 SKU

In de volgende tabel worden de beschikbare functies vergeleken met elke SKU.

|                                                   | v1 SKU   | v2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisch schalen                                       |          | &#x2713; |
| Zoneredundantie                                   |          | &#x2713; |
| Statische VIP                                        |          | &#x2713; |
| Azure Kubernetes Service (AKS) Ingress-controller |          | &#x2713; |
| Integratie van Azure Sleutelkluis                       |          | &#x2713; |
| HTTP-kopteksten opnieuw schrijven                           |          | &#x2713; |
| URL-gebaseerde routering                                 | &#x2713; | &#x2713; |
| Hosting van meerdere sites                             | &#x2713; | &#x2713; |
| Omleiding verkeer                               | &#x2713; | &#x2713; |
| Web Application Firewall (WAF)                    | &#x2713; | &#x2713; |
| Aangepaste WAF-regels                                  |          | &#x2713; |
| SSL-beëindiging (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| End-to-end SSL-versleuteling                         | &#x2713; | &#x2713; |
| Sessieaffiniteit                                  | &#x2713; | &#x2713; |
| Aangepaste foutenpagina's                                | &#x2713; | &#x2713; |
| Ondersteuning voor WebSocket                                 | &#x2713; | &#x2713; |
| Ondersteuning voor HTTP/2                                    | &#x2713; | &#x2713; |
| Verwerkingsstop voor verbindingen                               | &#x2713; | &#x2713; |

> [!NOTE]
> De autoscaling v2 SKU ondersteunt nu [standaardstatussondes](application-gateway-probe-overview.md#default-health-probe) om automatisch de status van alle resources in de back-endgroep te controleren en de backend-leden te markeren die als ongezond worden beschouwd. De standaardstatussonde wordt automatisch geconfigureerd voor backends die geen aangepaste sondeconfiguratie hebben. Zie [statussondes in toepassingsgateway voor](application-gateway-probe-overview.md)meer informatie.

## <a name="differences-with-v1-sku"></a>Verschillen met v1 SKU

|Verschil|Details|
|--|--|
|Verificatiecertificaat|Wordt niet ondersteund.<br>Zie [Overzicht van ssl van einde tot einde met Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)voor meer informatie.|
|Mixen Standard_v2 en Standard Application Gateway op hetzelfde subnet|Niet ondersteund|
|UDR (User-Defined Route) op subnet Application Gateway|Ondersteund (specifieke scenario's). In preview.<br> Zie [Configuratieoverzicht Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)voor meer informatie over ondersteunde scenario's.|
|NSG voor inkomende poortbereik| - 65200 tot 65535 voor Standard_v2 SKU<br>- 65503 tot 65534 voor standaard SKU.<br>Zie voor meer informatie de [FAQ](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Prestatielogboeken in Azure-diagnose|Wordt niet ondersteund.<br>Azure-statistieken moeten worden gebruikt.|
|Billing|Facturering gepland om te beginnen op 1 juli 2019.|
|FIPS-modus|Deze worden momenteel niet ondersteund.|
|ILB alleen-modus|Dit wordt momenteel niet ondersteund. De openbare en ILB-modus worden samen ondersteund.|
|Netwatcher integratie|Wordt niet ondersteund.|
|Azure Security Center-integratie|Nog niet beschikbaar.

## <a name="migrate-from-v1-to-v2"></a>Migreren van v1 naar v2

Een Azure PowerShell-script is beschikbaar in de PowerShell-galerie om u te helpen migreren van uw v1 Application Gateway/WAF naar de v2 Autoscaling SKU. Met dit script u de configuratie kopiëren vanaf uw v1-gateway. Verkeersmigratie is nog steeds uw verantwoordelijkheid. Zie [Azure Application Gateway migreren van v1 naar v2](migrate-v1-v2.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Webverkeer omleiden met Azure Application Gateway - Azure Portal](quick-create-portal.md)
- [Een poort voor automatisch schalen, redundante toepassingsgateway met een gereserveerd virtueel IP-adres maken met Azure PowerShell](tutorial-autoscale-ps.md)
- Meer informatie over [Application Gateway](overview.md).
- Meer informatie over [Azure Firewall](../firewall/overview.md).
