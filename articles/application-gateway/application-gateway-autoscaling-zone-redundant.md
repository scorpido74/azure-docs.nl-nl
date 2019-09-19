---
title: Automatisch schalen en zone-redundante Application Gateway v2
description: In dit artikel wordt de Azure-toepassing Standard_v2-en WAF_v2-SKU geïntroduceerd, waaronder automatisch schalen en zones redundante functies.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: b97dab0f41915ac6193c35cad9a6af812b16fd4a
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104892"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Automatisch schalen en zone-redundante Application Gateway v2 

Application Gateway en Web Application firewall (WAF) zijn ook beschikbaar in een Standard_v2-en WAF_v2-SKU. De v2-SKU biedt prestatie verbeteringen en voegt ondersteuning toe voor essentiële nieuwe functies, zoals automatisch schalen, zone redundantie en ondersteuning voor statische Vip's. Bestaande functies onder de Standard-en WAF SKU worden nog steeds ondersteund in de nieuwe v2-SKU, met een paar uitzonde ringen die worden vermeld in het gedeelte [vergelijking](#differences-with-v1-sku) .

De nieuwe v2-SKU bevat de volgende verbeteringen:

- **Automatische schaling**: Application Gateway-of WAF-implementaties onder de SKU voor automatisch schalen kunnen omhoog of omlaag worden geschaald op basis van het wijzigen van de verkeers laad patronen. Automatisch schalen heft ook de vereiste op om tijdens het inrichten een implementatiegrootte of het aantal instanties te kiezen. Deze SKU biedt echte elasticiteit. In de Standard_v2-en WAF_v2-SKU kunt Application Gateway zowel vaste capaciteit (automatisch schalen uitgeschakeld) als in de modus voor automatisch schalen inschakelen. De modus vaste capaciteit is handig voor scenario's met consistente en voorspel bare werk belastingen. De modus voor automatisch schalen is handig voor toepassingen die verschillen in toepassings verkeer bekijken.
- **Zone redundantie**: Een Application Gateway-of WAF-implementatie kan meerdere Beschikbaarheidszones beslaan, waarbij het niet nodig is om afzonderlijke Application Gateway instanties in elke zone in te richten met een Traffic Manager. U kunt kiezen uit één zone of meerdere zones waar Application Gateway instanties worden geïmplementeerd, waardoor de zone storing moeilijker wordt. De back-end-groep voor toepassingen kan net zo worden gedistribueerd over beschikbaarheids zones.

  Zone redundantie is alleen beschikbaar wanneer Azure-zones beschikbaar zijn. In andere regio's worden alle andere functies ondersteund. Zie [Wat zijn Beschikbaarheidszones in azure?](../availability-zones/az-overview.md#services-support-by-region) voor meer informatie.
- **Statisch VIP**: Application Gateway v2 SKU ondersteunt alleen het statische VIP-type. Dit zorgt ervoor dat het VIP dat is gekoppeld aan de toepassings gateway niet wordt gewijzigd voor de levens cyclus van de implementatie, zelfs na het opnieuw opstarten.  Er is geen statische VIP in v1. Daarom moet u de URL van de toepassings gateway gebruiken in plaats van het IP-adres voor de route ring van domein namen naar App Services via de Application Gateway.
- **Header herschrijven**: Met Application Gateway kunt u HTTP-aanvraag-en antwoord headers toevoegen, verwijderen of bijwerken met v2-SKU. Zie [HTTP-headers herschrijven met Application Gateway](rewrite-http-headers.md) voor meer informatie
- **Key Vault-integratie (preview-versie)** : Application Gateway v2 ondersteunt de integratie met Key Vault (in open bare preview) voor server certificaten die zijn gekoppeld aan listeners waarvoor HTTPS is ingeschakeld. Zie [SSL-beëindiging met Key Vault certificaten](key-vault-certs.md)voor meer informatie.
- **Azure Kubernetes service ingangs controller (preview)** : Met de Application Gateway v2 ingress-controller kan de Azure-toepassing gateway worden gebruikt als binnenkomend verkeer voor een Azure Kubernetes-service (AKS) die AKS-cluster wordt genoemd. Zie de [documentatie pagina](https://azure.github.io/application-gateway-kubernetes-ingress/)voor meer informatie.
- **Prestatie verbeteringen**: De v2-SKU biedt tot 5X betere prestaties voor SSL-offload in vergelijking met de Standard-WAF SKU.
- **Snellere implementatie en update tijd** De v2-SKU biedt een snellere implementatie en tijd voor het bijwerken in vergelijking met Standard/WAF SKU. Dit omvat ook wijzigingen in de configuratie van WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Ondersteunde regio’s

De SKU Standard_v2 en WAF_v2 is beschikbaar in de volgende regio's: Noord-Centraal VS, Zuid-Centraal VS, VS-West, VS-West 2, VS-Oost, VS-Oost 2, VS-midden, Europa-noord, Europa-west, Zuidoost-Azië, Frankrijk-centraal, UK-west, Japan-Oost, Japan-West, Australië-Oost, Australië-zuidoost, Canada-centraal, Canada-oost, Azië-oost, Korea Centraal, Korea-zuid, India-zuid, UK-zuid, Centraal-India, West-India, India-zuid.

## <a name="pricing"></a>Prijzen

Met de v2-SKU wordt het prijs model aangestuurd door verbruik en niet meer gekoppeld aan het aantal exemplaren of de grootte van het exemplaar. De v2-SKU-prijzen hebben twee onderdelen:

- **Vaste prijs** : dit is elk uur (of een gedeeltelijke uur) prijs om een Standard_v2-of WAF_v2-gateway in te richten.
- **Eenheids prijs van capaciteit** : Dit zijn kosten op basis van verbruik, die naast de vaste kosten worden berekend. Kosten voor capaciteitseenheden worden ook per uur of per deel van een uur berekend. Er zijn drie dimensies voor capaciteitseenheden: rekeneenheid, permanente verbinding en doorvoer. Rekeneenheid is een maateenheid voor de verbruikte processorcapaciteit. Factoren die invloed hebben op Compute-eenheid zijn TLS-verbindingen per seconde, berekeningen voor het herschrijven van URL'S en de verwerking van WAF-regels. Permanente verbinding is een meting van de tot stand gebrachte TCP-verbindingen met de toepassings gateway in een bepaald facturerings interval. De door Voer is het gemiddelde aantal megabits per seconde dat door het systeem wordt verwerkt in een opgegeven facturerings interval.

Elke capaciteitseenheid bestaat ten minste uit: 1 reken eenheid of 2500 permanente verbindingen, of door Voer van 2,22-Mbps.

Richt lijnen voor reken eenheden:

- **Standard_v2** : elke Compute-eenheid kan ongeveer 50 verbindingen per seconde met een RSA 2048-bits sleutel TLS-certificaat zijn.
- **WAF_v2** : elke Compute-eenheid kan ongeveer 10 gelijktijdige aanvragen per seconde ondersteunen voor 70-30% mix van verkeer met 70% aanvragen van minder dan 2 KB Get/post en resterend. WAF-prestaties worden op dit moment niet beïnvloed door de antwoord grootte.

> [!NOTE]
> Elk exemplaar kan op dit moment ongeveer 10 capaciteits eenheden ondersteunen.
> Het aantal aanvragen dat een Compute-eenheid kan verwerken, is afhankelijk van verschillende criteria, zoals de sleutel grootte van TLS-certificaten, het algoritme voor sleutel uitwisseling, het opnieuw schrijven van kopteksten en in het geval van WAF binnenkomende aanvraag grootte. U wordt aangeraden om toepassings tests uit te voeren om de aanvraag snelheid per Compute-eenheid te bepalen. Zowel de capaciteits eenheid als de reken eenheid worden beschikbaar gesteld als metrische waarde voordat facturering wordt gestart.

In de volgende tabel ziet u voor beelden van prijzen en zijn alleen bedoeld ter illustratie.

**Prijzen in VS Oost**:

|              SKU-naam                             | Vaste prijs ($/uur)  | Eenheids prijs van capaciteit ($/CU-hr)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/application-gateway/)voor meer informatie over prijzen. De facturering begint op 1 juli 2019.

**Voorbeeld 1**

Een Application Gateway Standard_v2 is ingericht zonder automatisch te schalen in hand matige schaal modus met vaste capaciteit van vijf exemplaren.

Vaste prijs = 744 (uren) * $0,20 = $148,8 <br>
Capaciteits eenheden = 744 (uur) * 10 capaciteits eenheid per instantie * 5 exemplaren * $0,008 per capaciteits eenheid uur = $297,6

Totale prijs = $148,8 + $297,6 = $446,4

**Voorbeeld 2**

Een Application Gateway standard_v2 is ingericht voor een maand en gedurende deze tijd worden er 25 nieuwe SSL-verbindingen per seconde ontvangen, gemiddeld 8,88 Mbps gegevens overdracht. Als er verbinding is met de korte levens duur, is uw prijs:

Vaste prijs = 744 (uren) * $0,20 = $148,8

Eenheids prijs van capaciteit = 744 (uur) * Max (25/50 reken eenheid voor verbindingen per seconde, 8.88/2.22 capaciteits eenheid voor door Voer) * $0,008 = 744 * 4 * 0,008 = $23,81

Totale prijs = $148.8 + 23.81 = $172,61

> [!NOTE]
> De functie Max retourneert de grootste waarde in een paar waarden.

**Voorbeeld 3**

Een Application Gateway WAF_v2 is ingericht voor een maand. Gedurende deze periode ontvangt het 25 nieuwe SSL-verbindingen per seconde, gemiddelde van 8,88-Mbps-gegevens overdracht en heeft 80 aanvragen per seconde. Als er een korte levens duur is en de berekening van de reken eenheid voor de toepassing 10 RPS per reken eenheid ondersteunt, is uw prijs:

Vaste prijs = 744 (uren) * $0,36 = $267,84

Prijs per capaciteits eenheid = 744 (uur) * Max (maximale reken eenheid (25/50 voor verbindingen/sec, 80/10 WAF RPS), 8.88/2.22 capaciteits eenheid voor door Voer) * $0,0144 = 744 * 8 * 0,0144 = $85,71

Totale prijs = $267,84 + $85,71 = $353,55

> [!NOTE]
> De functie Max retourneert de grootste waarde in een paar waarden.

## <a name="scaling-application-gateway-and-waf-v2"></a>Application Gateway en WAF v2 schalen

Application Gateway en WAF kunnen worden geconfigureerd om in twee modi te schalen:

- Automatisch **schalen** : als automatische schaling is ingeschakeld, worden de Application Gateway-en WAF v2-sku's omhoog of omlaag geschaald op basis van de vereisten voor toepassings verkeer. Deze modus biedt een betere elasticiteit voor uw toepassing en elimineert de nood zaak om de grootte van de toepassings gateway of het aantal instanties te raden. Met deze modus kunt u ook kosten besparen door te voor komen dat de gateway op piek ingerichte capaciteit wordt uitgevoerd voor de verwachte maximale belasting van het verkeer. U moet een minimum en optioneel maximum aantal exemplaren opgeven. De minimale capaciteit zorgt ervoor dat Application Gateway en WAF v2 niet onder het opgegeven minimum aantal instanties vallen, zelfs als er geen verkeer is. Elk exemplaar telt als 10 extra gereserveerde capaciteits eenheden. 0 betekent geen gereserveerde capaciteit en is uitsluitend automatisch geschaald. 0 extra minimum aantal exemplaren garandeert nog steeds een hoge Beschik baarheid van de service, die altijd is opgenomen in een vaste prijs. U kunt eventueel ook een maximum aantal exemplaren opgeven, waardoor het Application Gateway niet groter wordt dan het opgegeven aantal exemplaren. U wordt gefactureerd voor de hoeveelheid verkeer die door de gateway wordt geleverd. Het aantal exemplaren kan variëren van 0 tot en met 125. De standaard waarde voor het maximum aantal exemplaren is 20 als deze niet is opgegeven. 
- **Hand matig** : u kunt ook hand matige modus kiezen waarbij de gateway niet automatisch wordt geschaald. Als er in deze modus meer verkeer is dan wat Application Gateway of WAF kan verwerken, kan dit leiden tot verlies van verkeer. Bij hand matige modus wordt het aantal exemplaren opgegeven dat verplicht is. Aantal exemplaren kan variëren van 1 tot 125 exemplaren.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Vergelijking van functies tussen v1 SKU en v2 SKU

De volgende tabel vergelijkt de functies die beschikbaar zijn voor elke SKU.

|                                                   | V1-SKU   | V2 SKU   |
| ------------------------------------------------- | -------- | -------- |
| Automatisch schalen                                       |          | &#x2713; |
| Zone redundantie                                   |          | &#x2713; |
| Statisch VIP                                        |          | &#x2713; |
| Azure Kubernetes service (AKS) ingangs controller |          | &#x2713; |
| Integratie van Azure Key Vault                       |          | &#x2713; |
| HTTP (S)-headers opnieuw schrijven                           |          | &#x2713; |
| URL-gebaseerde routering                                 | &#x2713; | &#x2713; |
| Hosting van meerdere sites                             | &#x2713; | &#x2713; |
| Omleiding van verkeer                               | &#x2713; | &#x2713; |
| Web Application Firewall (WAF)                    | &#x2713; | &#x2713; |
| SSL-beëindiging (Secure Sockets Layer)            | &#x2713; | &#x2713; |
| End-to-end SSL-versleuteling                         | &#x2713; | &#x2713; |
| Sessieaffiniteit                                  | &#x2713; | &#x2713; |
| Aangepaste foutpagina's                                | &#x2713; | &#x2713; |
| Ondersteuning voor WebSocket                                 | &#x2713; | &#x2713; |
| Ondersteuning voor HTTP/2                                    | &#x2713; | &#x2713; |
| Verwerkingsstop voor verbindingen                               | &#x2713; | &#x2713; |

> [!NOTE]
> De v2 SKU voor automatisch schalen ondersteunt nu [standaard status tests](application-gateway-probe-overview.md#default-health-probe) om de status van alle resources in de back-end-pool te controleren en de back-end-leden te markeren die als slecht worden beschouwd. De standaard status test wordt automatisch geconfigureerd voor back-ends die geen aangepaste test configuratie hebben. Zie [Health tests in Application Gateway](application-gateway-probe-overview.md)voor meer informatie.

## <a name="differences-with-v1-sku"></a>Verschillen met v1 SKU

|Verschil|Details|
|--|--|
|Verificatie certificaat|Wordt niet ondersteund.<br>Zie [overzicht van end-to-end SSL met Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku)voor meer informatie.|
|Standard_v2 en standaard Application Gateway op hetzelfde subnet mengen|Niet ondersteund|
|Door de gebruiker gedefinieerde route (UDR) op Application Gateway subnet|Niet ondersteund|
|NSG voor binnenkomend poort bereik| -65200 tot 65535 voor Standard_v2-SKU<br>-65503 tot 65534 voor standaard-SKU.<br>Raadpleeg de [Veelgestelde vragen](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet)voor meer informatie.|
|Prestatie Logboeken in azure Diagnostics|Wordt niet ondersteund.<br>De metrische gegevens van Azure moeten worden gebruikt.|
|Billing|De facturering is gepland om te beginnen op 1 juli 2019.|
|FIPS-modus|Deze worden momenteel niet ondersteund.|
|Modus alleen ILB|Dit wordt momenteel niet ondersteund. De open bare en ILB modus samen worden ondersteund.|
|Netwatcher-integratie|Wordt niet ondersteund.|
|Integratie van Azure Security Center|Nog niet beschikbaar.

## <a name="migrate-from-v1-to-v2"></a>Migreren van v1 naar v2

Een Azure PowerShell script is beschikbaar in de Power shell Gallery, waarmee u kunt migreren van uw v1-Application Gateway/WAF naar de v2-SKU voor automatisch schalen. Met dit script kunt u de configuratie van uw v1-gateway kopiëren. Verkeer migratie is nog steeds uw verantwoordelijkheid. Zie [Azure-toepassing gateway migreren van v1 naar v2](migrate-v1-v2.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Webverkeer omleiden met Azure-toepassing gateway-Azure Portal](quick-create-portal.md)
- [Een automatische schaling maken, een zone redundante toepassings gateway met een gereserveerd virtueel IP-adres met behulp van Azure PowerShell](tutorial-autoscale-ps.md)
- Meer informatie over [Application Gateway](overview.md).
- Meer informatie over [Azure firewall](../firewall/overview.md).
