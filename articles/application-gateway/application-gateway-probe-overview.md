---
title: Overzicht van statusbewaking voor Azure Application Gateway
description: Azure Application Gateway bewaakt de status van alle resources in de back-endgroep en verwijdert automatisch alle resources die als ongezond worden beschouwd uit de groep.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2020
ms.author: victorh
ms.openlocfilehash: c5a53167c6a4ca6c886b858a1608eaa173185bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335847"
---
# <a name="application-gateway-health-monitoring-overview"></a>Overzicht van statusbewaking van toepassingsgateway

Azure Application Gateway bewaakt standaard de status van alle resources in de back-endgroep en verwijdert automatisch alle resources die als ongezond worden beschouwd uit de groep. Application Gateway blijft de exemplaren met slechte status bewaken en voegt deze weer aan de goede back-end-adrespool toe zodra ze beschikbaar komen en beantwoorden aan de statuscontroles. Toepassingsgateway verzendt de statussondes met dezelfde poort die is gedefinieerd in de back-end HTTP-instellingen. Deze configuratie zorgt ervoor dat de sonde dezelfde poort test die klanten zouden gebruiken om verbinding te maken met de backend. 

Het gebruik van het bron-IP-adres Application Gateway voor statussondes is afhankelijk van de backendpool:
 
- Als de backendpool een openbaar eindpunt is, is het bronadres het openbare IP-adres van de toepassingsgateway.
- Als de backendpool een privéeindpunt is, is het ip-adres van de bron afkomstig van de subnet-privé-IP-adresruimte van de toepassingsgateway.


![voorbeeld van toepassingsgateways][1]

Naast het gebruik van standaard statussondebewaking, u de statussonde ook aanpassen aan de vereisten van uw toepassing. In dit artikel worden zowel standaard- als aangepaste statussondes behandeld.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Standaardstatussonde

Een toepassingsgateway configureert automatisch een standaardstatussonde wanneer u geen aangepaste sondeconfiguratie instelt. Het controlegedrag werkt door een HTTP-aanvraag in te dienen bij de IP-adressen die zijn geconfigureerd voor de back-endpool. Voor standaardsondes als de backend http-instellingen zijn geconfigureerd voor HTTPS, gebruikt de sonde ook HTTPS om de status van de backends te testen.

Bijvoorbeeld: u configureert de toepassingsgateway om back-endservers A, B en C te gebruiken om HTTP-netwerkverkeer op poort 80 te ontvangen. De standaardstatusbewaking test de drie servers elke 30 seconden op een gezonde HTTP-respons. Een gezonde HTTP-reactie heeft een [statuscode](https://msdn.microsoft.com/library/aa287675.aspx) tussen 200 en 399.

Als de standaardsondecontrole mislukt voor server A, wordt deze door de toepassingsgateway uit de back-endpool verwijderd en wordt het netwerkverkeer niet meer naar deze server geleid. De standaardsonde blijft nog steeds controleren op server A om de 30 seconden. Wanneer server A reageert op één verzoek van een standaardstatussonde, wordt deze weer als gezond toegevoegd aan de back-endpool en begint het verkeer weer naar de server te stromen.

### <a name="probe-matching"></a>Sondematching

Standaard wordt een HTTP(S)-antwoord met statuscode tussen 200 en 399 als gezond beschouwd. Aangepaste statussondes ondersteunen bovendien twee overeenkomende criteria. Matchingcriteria kunnen worden gebruikt om optioneel de standaardinterpretatie van wat een gezonde respons maakt, te wijzigen.

Hieronder volgen de volgende criteria: 

- **HTTP-antwoordstatuscode match** - Probe matching criterium voor het accepteren van door de gebruiker opgegeven http-antwoordcode of antwoordcode bereiken. Individuele door komma's gescheiden antwoordstatuscodes of een reeks statuscode worden ondersteund.
- **HTTP response body match** - Probe matching criterium dat kijkt naar HTTP response body en overeenkomt met een door de gebruiker opgegeven tekenreeks. De overeenkomst zoekt alleen naar de aanwezigheid van door de gebruiker opgegeven tekenreeks in de reactietekst en is geen volledige reguliere expressieovereenkomst.

Wedstrijdcriteria kunnen worden `New-AzApplicationGatewayProbeHealthResponseMatch` opgegeven met behulp van de cmdlet.

Bijvoorbeeld:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Zodra de wedstrijdcriteria zijn opgegeven, kan deze worden `-Match` gekoppeld aan de configuratie van de sonde met behulp van een parameter in PowerShell.

### <a name="default-health-probe-settings"></a>Instellingen voor standaardstatussonde

| Sonde, eigenschap | Waarde | Beschrijving |
| --- | --- | --- |
| Probe URL |http://127.0.0.1:\<port\>/ |URL-pad |
| Interval |30 |De hoeveelheid tijd in seconden om te wachten voordat de volgende statussonde wordt verzonden.|
| Time-out |30 |De hoeveelheid tijd in seconden dat de toepassingsgateway wacht op een sonderespons voordat de sonde als ongezond wordt gemarkeerd. Als een sonde als gezond terugkeert, wordt de bijbehorende backend onmiddellijk gemarkeerd als gezond.|
| Ongezonde drempelwaarde |3 |Bepaalt hoeveel sondes te sturen in het geval er een storing van de reguliere gezondheid sonde. Deze extra statussen worden snel achter elkaar verzonden om de status van de backend snel te bepalen en wachten niet op het sondeinterval. Deze behaivor is slechts v1 SKU. In het geval van v2 SKU wachten de statussondes op het interval. De back-endserver wordt gemarkeerd nadat het aantal opeenvolgende sondefouten de ongezonde drempelwaarde heeft bereikt. |

> [!NOTE]
> De poort is dezelfde poort als de back-end HTTP-instellingen.

De standaardsonde kijkt\/alleen naar http: /127.0.0.1:\<poort\> om de status van de status te bepalen. Als u de statussonde moet configureren om naar een aangepaste URL te gaan of andere instellingen te wijzigen, moet u aangepaste sondes gebruiken.

### <a name="probe-intervals"></a>Sondeintervallen

Alle exemplaren van Application Gateway sonde de backend onafhankelijk van elkaar. Dezelfde sondeconfiguratie is van toepassing op elke instantie van Application Gateway. Als de sondeconfiguratie bijvoorbeeld is om statussondes elke 30 seconden te verzenden en de toepassingsgateway twee instanties heeft, verzenden beide instanties de statussonde elke 30 seconden.

Ook als er meerdere luisteraars zijn, dan sondes elke luisteraar de backend onafhankelijk van elkaar. Als er bijvoorbeeld twee listeners naar dezelfde backendpool wijzen op twee verschillende poorten (geconfigureerd door twee backend http-instellingen), sonde elke listener onafhankelijk van elkaar dezelfde backend. In dit geval zijn er twee sondes van elke toepassingsgatewayinstantie voor de twee listeners. Als er in dit scenario twee exemplaren van de toepassingsgateway zijn, ziet de backend virtuele machine vier sondes per het geconfigureerde sondeinterval.

## <a name="custom-health-probe"></a>Aangepaste statussonde

Met aangepaste sondes u een meer gedetailleerde controle hebben over de statusbewaking. Wanneer u aangepaste sondes gebruikt, u het sondeinterval, de URL en het te testen pad configureren en hoeveel mislukte antwoorden moeten worden geaccepteerd voordat u de instantie back-endpool als ongezond markeert.

### <a name="custom-health-probe-settings"></a>Aangepaste statussondeinstellingen

In de volgende tabel vindt u definities voor de eigenschappen van een aangepaste statussonde.

| Sonde, eigenschap | Beschrijving |
| --- | --- |
| Name |Naam van de sonde. Deze naam wordt gebruikt om te verwijzen naar de sonde in back-end HTTP-instellingen. |
| Protocol |Protocol gebruikt om de sonde te sturen. De sonde gebruikt het protocol dat is gedefinieerd in de back-end HTTP-instellingen |
| Host |Host naam om de sonde te sturen. Alleen van toepassing wanneer multi-site is geconfigureerd op Application Gateway, gebruik anders '127.0.0.1'. Deze waarde verschilt van de naam VM-host. |
| Pad |Relatief pad van de sonde. Het geldige pad begint met '/'. |
| Interval |Sonde interval in seconden. Deze waarde is het tijdsinterval tussen twee opeenvolgende sondes. |
| Time-out |Sonde time-out in seconden. Als een geldig antwoord niet binnen deze time-outperiode wordt ontvangen, wordt de sonde gemarkeerd als mislukt.  |
| Ongezonde drempelwaarde |Sonde opnieuw proberen tellen. De back-endserver wordt gemarkeerd nadat het aantal opeenvolgende sondefouten de ongezonde drempelwaarde heeft bereikt. |

> [!IMPORTANT]
> Als Application Gateway is geconfigureerd voor één site, moet standaard de hostnaam worden opgegeven als '127.0.0.1', tenzij anders geconfigureerd in aangepaste sonde.
> Voor referentie wordt een \<aangepaste\>\<sonde verzonden naar protocol ://\>host :\<poortpad\>\<\>. De gebruikte poort is dezelfde poort als gedefinieerd in de back-end HTTP-instellingen.

## <a name="nsg-considerations"></a>NSG-overwegingen

U moet binnenkomend internetverkeer toestaan op TCP-poorten 65503-65534 voor de Application Gateway v1 SKU en TCP-poorten 65200-65535 voor de v2 SKU met het doelsubnet als **GatewayManager-servicetag.** **Any** Dit poortbereik is vereist voor azure-infrastructuurcommunicatie.

Bovendien kan uitgaande internetverbinding niet worden geblokkeerd en moet binnenkomend verkeer afkomstig van de **AzureLoadBalancer-tag** worden toegestaan.

Zie [Configuratieoverzicht Application Gateway](configuration-overview.md#network-security-groups-on-the-application-gateway-subnet)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Nadat u meer te weten bent gekomen over de statusbewaking van de toepassingsgateway, u een [aangepaste statussonde](application-gateway-create-probe-portal.md) configureren in de Azure-portal of een [aangepaste statussonde](application-gateway-create-probe-ps.md) met PowerShell en het implementatiemodel azure resource manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
