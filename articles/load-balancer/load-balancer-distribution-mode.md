---
title: De distributiemodus azure load balancer configureren
titleSuffix: Azure Load Balancer
description: Ga in dit artikel aan de slag met het configureren van de distributiemodus voor Azure Load Balancer om bron-IP-affiniteit te ondersteunen.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/19/2019
ms.author: allensu
ms.openlocfilehash: 5c50186692438be5d0922cd329c28e665310e5c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77023528"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>De distributiemodus configureren voor Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Op hash gebaseerde distributiemodus

De standaarddistributiemodus van Azure Load Balancer is een hash met vijf tuples. 

De tuple bestaat uit:
* **Bron-IP**
* **Bronpoort**
* **Bestemming IP**
* **Bestemmingshaven**
* **Protocoltype**

De hash wordt gebruikt om verkeer naar de beschikbare servers in kaart te brengen. Het algoritme zorgt alleen voor plakkerigheid binnen een transportsessie. Pakketten die zich in dezelfde sessie bevinden, worden naar hetzelfde datacenter-IP achter het load-balanced endpoint geleid. Wanneer de client een nieuwe sessie start vanaf hetzelfde bron-IP, verandert de bronpoort en gaat het verkeer naar een ander eindpunt van het datacenter.

![Distributiemodus op basis van vijf tuple-hasa](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Bron-IP-affiniteitsmodus

De load balancer kan ook worden geconfigureerd met behulp van de bron IP-affiniteit distributiemodus. Deze distributiemodus wordt ook wel sessieaffiniteit of client-IP-affiniteit genoemd. De modus maakt gebruik van een twee-tuple (bron IP en bestemming IP) of drie-tuple (bron IP, doel IP, en protocol type) hash om verkeer in kaart te brengen naar de beschikbare servers. Door gebruik te maken van bron-IP-affiniteit gaan verbindingen die vanaf dezelfde clientcomputer worden gestart naar hetzelfde datacentereindpunt.

De volgende figuur illustreert een twee-tuple configuratie. Merk op hoe de twee-tuple loopt door de load balancer naar virtuele machine 1 (VM1). VM1 wordt vervolgens ondersteund door VM2 en VM3.

![Distributiemodus voor affiniteit met twee tuple-sessies](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

De bron-IP-affiniteitsmodus lost een incompatibiliteit op tussen Azure Load Balancer en Extern bureaublad-gateway (Extern bureaublad-gateway). Met deze modus u een Extern bureaublad-gatewayfarm bouwen in één cloudservice.

Een ander use case scenario is media uploaden. Het uploaden van gegevens gebeurt via UDP, maar het controlevlak wordt bereikt via TCP:

* Een client start een TCP-sessie naar het laadgebalanceerde openbare adres en wordt doorverwezen naar een specifieke DIP. Het kanaal blijft actief om de verbindingsstatus te controleren.
* Een nieuwe UDP-sessie vanaf dezelfde clientcomputer wordt gestart tot hetzelfde laadpunt voor belasting. De verbinding wordt naar hetzelfde DIP-eindpunt geleid als de vorige TCP-verbinding. De media-upload kan worden uitgevoerd bij hoge doorvoer met behoud van een controlekanaal via TCP.

> [!NOTE]
> Wanneer een load-balanced set verandert door een virtuele machine te verwijderen of toe te voegen, wordt de verdeling van clientaanvragen opnieuw berekend. U niet afhankelijk zijn van nieuwe verbindingen van bestaande clients om op dezelfde server te belanden. Bovendien kan het gebruik van de ip-affiniteitsmodus van de bron leiden tot een ongelijke verkeersverdeling. Clients die achter proxy's lopen, kunnen worden gezien als één unieke clienttoepassing.

## <a name="configure-source-ip-affinity-settings"></a>Instellingen voor bron-IP-affiniteit configureren

### <a name="azure-portal"></a>Azure Portal

U de configuratie van de distributiemodus wijzigen door de regel voor het balanceren van de regel in de portal te wijzigen.

1. Meld u aan bij de Azure-portal en zoek de resourcegroep met de load balancer die u wilt wijzigen door op **Resourcegroepen**te klikken.
2. Klik in het overzichtsscherm van de load balancer op **Regels voor laden** onder **Instellingen**.
3. Klik in het scherm load-balancing rules op de regel voor het balanceren van de lastenafweging dat u de distributiemodus wilt wijzigen.
4. Onder de regel wordt de distributiemodus gewijzigd door het vervolgkeuzeveld **Sessiepersistentie** te wijzigen.  De volgende opties zijn beschikbaar:
    
    * **None (hash-based)** - Geeft aan dat opeenvolgende aanvragen van dezelfde client door een virtuele machine kunnen worden afgehandeld.
    * **Client-IP (bron IP-affiniteit 2-tuple)** - Geeft op dat opeenvolgende aanvragen van hetzelfde CLIENT-IP-adres door dezelfde virtuele machine worden afgehandeld.
    * **Client-IP en protocol (bron IP-affiniteit 3-tuple)** - Geeft op dat opeenvolgende aanvragen van hetzelfde CLIENT-IP-adres en protocolcombinatie door dezelfde virtuele machine worden afgehandeld.

5. Kies de distributiemodus en klik op **Opslaan**.

### <a name="azure-powershell"></a>Azure PowerShell

Voor virtuele machines die met Resource Manager zijn geïmplementeerd, gebruikt u PowerShell om de distributie-instellingen voor de load-balancer te wijzigen op een bestaande regel voor het balanceren van de belasting. Met de volgende opdracht wordt de distributiemodus bijgewerkt: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Voor klassieke virtuele machines gebruikt u Azure PowerShell om de distributie-instellingen te wijzigen. Voeg een Azure-eindpunt toe aan een virtuele machine en configureer de distributiemodus voor de load balancer:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Stel de waarde `LoadBalancerDistribution` van het element in voor de vereiste hoeveelheid taakverdeling. Geef sourceIP op voor het taakverdeling van de taakverdeling met twee tuple (bron-IP en bestemmings-IP). Geef sourceIPProtocol op voor de taakverdeling van de taakverdeling van drie tuple (bron-IP, doel-IP en protocoltype). Geef geen op voor het standaardgedrag van het balanceren van vijf tuple-lasten.

Haal een configuratie van de eindpuntlastenbalanssconfiguratie op met behulp van de volgende instellingen:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15
    LoadBalancerDistribution : sourceIP

Wanneer `LoadBalancerDistribution` het element niet aanwezig is, gebruikt Azure Load Balancer het standaard algoritme voor vijf tuple.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Distributiemodus configureren op de set met een laadgebalanceerde eindpunt

Wanneer eindpunten deel uitmaken van een load-balanced endpoint-set, moet de distributiemodus worden geconfigureerd op de taakset voor het laadgebied:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Distributiemodus configureren voor eindpunten van Cloud Services

Gebruik de Azure SDK voor .NET 2.5 om uw cloudservice bij te werken. De eindpuntinstellingen voor Cloud Services worden gemaakt in het .csdef-bestand. Als u de distributiemodus voor de load balancer voor een implementatie van Cloud Services wilt bijwerken, is een implementatie-upgrade vereist.

Hier is een voorbeeld van .csdef-wijzigingen voor eindpuntinstellingen:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
<NetworkConfiguration>
    <VirtualNetworkSite name="VNet"/>
    <AddressAssignments>
<InstanceAddress roleName="VMRolePersisted">
    <PublicIPs>
    <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
    </PublicIPs>
</InstanceAddress>
    </AddressAssignments>
</NetworkConfiguration>
```

## <a name="api-example"></a>API-voorbeeld

In het volgende voorbeeld ziet u hoe u de verdelingsmodus voor de load balancer opnieuw configureert voor een opgegeven load-balanced set in een implementatie. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>De distributiemodus wijzigen voor geïmplementeerde load-balanced-set

Gebruik het klassieke azure-implementatiemodel om een bestaande implementatieconfiguratie te wijzigen. Voeg `x-ms-version` de koptekst toe en stel de waarde in op versie 2014-09-01 of hoger.

#### <a name="request"></a>Aanvraag

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Zoals eerder beschreven, `LoadBalancerDistribution` stelt u het element in op sourceIP voor affiniteit met twee tuple, sourceIPProtocol voor affiniteit met drie tuple of geen voor geen affiniteit (affiniteit met vijf tuple).

#### <a name="response"></a>Antwoord

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure Internal Load Balancer](load-balancer-internal-overview.md)
* [Aan de slag met het configureren van een op internet gerichte load balancer](quickstart-create-standard-load-balancer-powershell.md)
* [TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
