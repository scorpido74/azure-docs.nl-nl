---
title: Foute gatewayfouten oplossen - Azure Application Gateway
description: 'Informatie over het oplossen van fout in application gatewayserver: 502 - Webserver heeft een ongeldig antwoord ontvangen terwijl deze fungeert als gateway- of proxyserver.'
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 17bed17b536f6e88fc821fd83e09a1d6ea218bc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74130484"
---
# <a name="troubleshooting-bad-gateway-errors-in-application-gateway"></a>Fouten met ongeldige gateway oplossen in Application Gateway

Meer informatie over het oplossen van foutie fouten (502) die zijn ontvangen bij het gebruik van Azure Application Gateway.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Overzicht

Na het configureren van een toepassingsgateway is een van de fouten die u zien Serverfout: 502 - Webserver heeft een ongeldig antwoord ontvangen terwijl u optreedt als gateway- of proxyserver. Deze fout kan gebeuren om de volgende belangrijkste redenen:

* NSG, UDR of Custom DNS blokkeert de toegang tot backendpoolleden.
* Back-end VM's of exemplaren van virtuele machineschaalset reageren niet op de standaardstatussonde.
* Ongeldige of onjuiste configuratie van aangepaste statustests.
* De [back-endpool](#empty-backendaddresspool)van Azure Application Gateway is niet geconfigureerd of leeg.
* Geen van de VM's of exemplaren in [de virtuele machineschaalset is in orde.](#unhealthy-instances-in-backendaddresspool)
* [Time-out- of verbindingsproblemen aanvragen](#request-time-out) met gebruikersverzoeken.

## <a name="network-security-group-user-defined-route-or-custom-dns-issue"></a>Netwerkbeveiligingsgroep, door de gebruiker gedefinieerde route of aangepast DNS-probleem

### <a name="cause"></a>Oorzaak

Als de toegang tot de backend is geblokkeerd vanwege een NSG, UDR of aangepaste DNS, kunnen toepassingsgateway-exemplaren de backendpool niet bereiken. Dit veroorzaakt sondefouten, wat resulteert in 502 fouten.

De NSG/UDR kan aanwezig zijn in het subnet van de toepassingsgateway of in het subnet waarin de toepassings-VM's worden geïmplementeerd.

Ook de aanwezigheid van een aangepaste DNS in het VNet kan problemen veroorzaken. Een FQDN die wordt gebruikt voor backendpoolleden, wordt mogelijk niet correct opgelost door de door de gebruiker geconfigureerde DNS-server voor de VNet.

### <a name="solution"></a>Oplossing

Valideren van NSG-, UDR- en DNS-configuratie door de volgende stappen te doorlopen:

* Controleer NSGs die zijn gekoppeld aan het subnet van de toepassingsgateway. Zorg ervoor dat de communicatie met backend niet wordt geblokkeerd.
* Controleer UDR die is gekoppeld aan het subnet van de toepassingsgateway. Zorg ervoor dat de UDR het verkeer niet wegleidt van het backend-subnet. Controleer bijvoorbeeld of routering naar virtuele netwerkapparaten of standaardroutes worden geadverteerd naar het subnet van de toepassingsgateway via ExpressRoute/VPN.

```azurepowershell
$vnet = Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName
Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
```

* Controleer effectieve NSG en route met de backend VM

```azurepowershell
Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName nic1 -ResourceGroupName testrg
Get-AzEffectiveRouteTable -NetworkInterfaceName nic1 -ResourceGroupName testrg
```

* Controleer de aanwezigheid van aangepaste DNS in het VNet. DNS kan worden gecontroleerd door te kijken naar details van de VNet-eigenschappen in de uitvoer.

```json
Get-AzVirtualNetwork -Name vnetName -ResourceGroupName rgName 
DhcpOptions            : {
                           "DnsServers": [
                             "x.x.x.x"
                           ]
                         }
```
Controleer als u aanwezig bent of de DNS-server de FQDN van het backend-poollid correct kan oplossen.

## <a name="problems-with-default-health-probe"></a>Problemen met standaard statussonde

### <a name="cause"></a>Oorzaak

502-fouten kunnen ook frequente indicatoren zijn die de standaardstatussonde geen back-end VM's kan bereiken.

Wanneer een toepassingsgateway-instantie is ingericht, configureert deze automatisch een standaardstatussonde naar elke BackendAddressPool met behulp van eigenschappen van de backendhttpsetting. Er is geen gebruikersinvoer vereist om deze sonde in te stellen. Wanneer een regel voor het balanceren van de taak is geconfigureerd, wordt er een koppeling gemaakt tussen een backendhttpsetting en een backendAddresspool. Voor elk van deze koppelingen is een standaardsonde geconfigureerd en de toepassingsgateway start een periodieke statuscontroleverbinding met elke instantie in de BackendAddressPool op de poort die is opgegeven in het element BackendHttpSetting. 

In de volgende tabel worden de waarden weergegeven die zijn gekoppeld aan de standaardstatussonde:

| Sonde, eigenschap | Waarde | Beschrijving |
| --- | --- | --- |
| Probe URL |`http://127.0.0.1/` |URL-pad |
| Interval |30 |Sondeinterval in seconden |
| Time-out |30 |Time-out van de sonde in enkele seconden |
| Ongezonde drempelwaarde |3 |Sonde opnieuw proberen tellen. De back-endserver wordt gemarkeerd nadat het aantal opeenvolgende sondefouten de ongezonde drempelwaarde heeft bereikt. |

### <a name="solution"></a>Oplossing

* Controleer of een standaardsite is geconfigureerd en luistert op 127.0.0.1.
* Als BackendHttpSetting een andere poort dan 80 opgeeft, moet de standaardsite worden geconfigureerd om op die poort te luisteren.
* De aanroep moet `http://127.0.0.1:port` een HTTP-resultaatcode van 200 retourneren. Dit moet worden geretourneerd binnen de time-outperiode van 30 seconden.
* Controleer of de geconfigureerde poort open is en dat er geen firewallregels of Azure Network Security Groups zijn, die binnenkomend of uitgaand verkeer op de geconfigureerde poort blokkeren.
* Als Azure classic VM's of Cloud Service wordt gebruikt met een FQDN of een openbaar IP-adres, moet u ervoor zorgen dat het bijbehorende [eindpunt](../virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fapplication-gateway%2ftoc.json) wordt geopend.
* Als de VM is geconfigureerd via Azure Resource Manager en zich buiten het VNet bevindt waar de toepassingsgateway is geïmplementeerd, moet een [netwerkbeveiligingsgroep](../virtual-network/security-overview.md) zijn geconfigureerd om toegang op de gewenste poort mogelijk te maken.

## <a name="problems-with-custom-health-probe"></a>Problemen met aangepaste statussonde

### <a name="cause"></a>Oorzaak

Aangepaste statussondes zorgen voor extra flexibiliteit in het standaard indringende gedrag. Wanneer u aangepaste sondes gebruikt, u het sondeinterval, de URL, het te testen pad en het aantal mislukte antwoorden configureren voordat u de instantie back-endpool als ongezond markeert.

De volgende extra eigenschappen worden toegevoegd:

| Sonde, eigenschap | Beschrijving |
| --- | --- |
| Name |Naam van de sonde. Deze naam wordt gebruikt om te verwijzen naar de sonde in back-end HTTP-instellingen. |
| Protocol |Protocol gebruikt om de sonde te sturen. De sonde gebruikt het protocol dat is gedefinieerd in de back-end HTTP-instellingen |
| Host |Host naam om de sonde te sturen. Alleen van toepassing wanneer multi-site is geconfigureerd op de toepassingsgateway. Dit is anders dan de naam VM-host. |
| Pad |Relatief pad van de sonde. Het geldige pad begint met '/'. De sonde \<\>wordt\<\>verzonden naar protocol :// host :\<poortpad\>\<\> |
| Interval |Sonde interval in seconden. Dit is het tijdsinterval tussen twee opeenvolgende sondes. |
| Time-out |Sonde time-out in seconden. Als een geldig antwoord niet binnen deze time-outperiode wordt ontvangen, wordt de sonde gemarkeerd als mislukt. |
| Ongezonde drempelwaarde |Sonde opnieuw proberen tellen. De back-endserver wordt gemarkeerd nadat het aantal opeenvolgende sondefouten de ongezonde drempelwaarde heeft bereikt. |

### <a name="solution"></a>Oplossing

Controleer of de aangepaste statussonde correct is geconfigureerd als de vorige tabel. Zorg naast de voorgaande stappen voor het oplossen van problemen ook op het volgende:

* Zorg ervoor dat de sonde correct is opgegeven volgens de [geleider](application-gateway-create-probe-ps.md).
* Als de toepassingsgateway is geconfigureerd voor één site, moet standaard `127.0.0.1`de hostnaam worden opgegeven als , tenzij anders geconfigureerd in aangepaste sonde.
* Zorg ervoor dat\<een\>\<aanroep naar http:// host: poortpad\>\<\> een HTTP-resultaatcode van 200 retourneert.
* Zorg ervoor dat Interval, Timeout en UnhealtyThreshold binnen de aanvaardbare bereiken liggen.
* Als u een HTTPS-sonde gebruikt, moet u ervoor zorgen dat de backendserver Geen SNI nodig heeft door een terugvalcertificaat op de backendserver zelf te configureren.

## <a name="request-time-out"></a>Time-out aanvragen

### <a name="cause"></a>Oorzaak

Wanneer een gebruikersverzoek wordt ontvangen, past de toepassingsgateway de geconfigureerde regels toe op de aanvraag en leidt deze door naar een back-endpoolinstantie. Het wacht op een configureerbaar tijdsinterval voor een antwoord van de back-endinstantie. Standaard is dit interval **20** seconden. Als de toepassingsgateway in dit interval geen antwoord van back-endtoepassing ontvangt, krijgt de gebruikersaanvraag een 502-fout.

### <a name="solution"></a>Oplossing

Met Application Gateway u deze instelling configureren via de BackendHttpSetting, die vervolgens op verschillende groepen kan worden toegepast. Verschillende back-endpools kunnen verschillende back-endhttpsetting hebben en een andere time-out van aanvragen geconfigureerd.

```azurepowershell
    New-AzApplicationGatewayBackendHttpSettings -Name 'Setting01' -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 60
```

## <a name="empty-backendaddresspool"></a>Lege BackendAddressPool

### <a name="cause"></a>Oorzaak

Als de toepassingsgateway geen VM's of virtuele machineschaalset heeft die is geconfigureerd in de back-endadresgroep, kan deze geen klantaanvragen doorsturen en een foutopfoutmelding verzenden.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de groep back-endadres niet leeg is. Dit kan worden gedaan via PowerShell, CLI, of portal.

```azurepowershell
Get-AzApplicationGateway -Name "SampleGateway" -ResourceGroupName "ExampleResourceGroup"
```

De uitvoer van de voorgaande cmdlet moet een niet-lege back-end-addresspool bevatten. In het volgende voorbeeld worden twee geretourneerde groepen weergegeven die zijn geconfigureerd met een FQDN of een IP-adres voor de backend-VM's. De inrichtingsstatus van de BackendAddressPool moet 'Geslaagd' worden.

BackendAddressPoolsText:

```json
[{
    "BackendAddresses": [{
        "ipAddress": "10.0.0.10",
        "ipAddress": "10.0.0.11"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool01",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool01"
}, {
    "BackendAddresses": [{
        "Fqdn": "xyx.cloudapp.net",
        "Fqdn": "abc.cloudapp.net"
    }],
    "BackendIpConfigurations": [],
    "ProvisioningState": "Succeeded",
    "Name": "Pool02",
    "Etag": "W/\"00000000-0000-0000-0000-000000000000\"",
    "Id": "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>/backendAddressPools/pool02"
}]
```

## <a name="unhealthy-instances-in-backendaddresspool"></a>Ongezonde exemplaren in BackendAddressPool

### <a name="cause"></a>Oorzaak

Als alle exemplaren van BackendAddressPool niet in orde zijn, heeft de toepassingsgateway geen back-end om het verzoek van de gebruiker naar te leiden. Dit kan ook het geval zijn wanneer back-endexemplaren in orde zijn, maar niet de vereiste toepassing hebben geïmplementeerd.

### <a name="solution"></a>Oplossing

Zorg ervoor dat de exemplaren in orde zijn en dat de toepassing goed is geconfigureerd. Controleer of de back-end-exemplaren kunnen reageren op een ping van een andere vm in hetzelfde VNet. Als u bent geconfigureerd met een openbaar eindpunt, moet u ervoor zorgen dat een browseraanvraag voor de webtoepassing bruikbaar is.

## <a name="next-steps"></a>Volgende stappen

Als het probleem in de voorgaande stappen niet is opgelost, opent u een [ondersteuningsticket](https://azure.microsoft.com/support/options/).

