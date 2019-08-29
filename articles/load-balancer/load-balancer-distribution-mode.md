---
title: Distributiemodus voor Load Balancer van Azure configureren
titlesuffix: Azure Load Balancer
description: Klik hier voor meer informatie over het configureren van de distributiemodus voor Azure Load Balancer ter ondersteuning van de bron-IP-affiniteit.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 0d3ddf2e005338a19972cfcdef025579764f7f23
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114723"
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>De distributiemodus configureren voor Azure Load Balancer

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="hash-based-distribution-mode"></a>Distributiemodus voor de hash-gebaseerde

De standaardmodus voor distributie voor Azure Load Balancer is een 5-tuple hash. De tuple bestaat uit de bron-IP, bronpoort, doel-IP, doelpoort en protocoltype. De hash wordt gebruikt om het verkeer worden toegewezen aan de beschikbare servers en de algoritme biedt persistentie alleen binnen een transportsessie. Pakketten die zich in dezelfde sessie worden omgeleid naar hetzelfde datacenter-IP (DIP) exemplaar achter de load balancing-eindpunt. Wanneer de client wordt een nieuwe sessie vanuit de dezelfde bron-IP gestart, wordt de bron-poort gewijzigd en zorgt ervoor dat het verkeer naar een ander DIP-eindpunt.

![5-tuple hash-distributiepunt in de modus](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Affiniteitsmodus voor bron-IP

Load Balancer kan ook worden geconfigureerd met behulp van de bron IP-affiniteit distributie-modus. Deze distributiemodus wordt ook wel bekend als sessieaffiniteit of client-IP-affiniteit. De modus gebruikt een 2-tuple (bron-IP en doel-IP) of 3-tuple (bron-IP, doel-IP en protocol type) hash-verkeer worden toegewezen aan de beschikbare servers. Met behulp van bron-IP-affiniteit gaan verbindingen die vanaf dezelfde client computer worden gestart naar hetzelfde DIP-eind punt.

De volgende afbeelding ziet u een configuratie met 2-tuple. U ziet hoe de 2-tuple wordt uitgevoerd via de load balancer voor virtuele machine, 1 (VM1). VM1 vervolgens een back-up VM2 en vm3 maakt.

![distributiemodus voor 2-tuple sessie-affiniteit](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Affiniteitsmodus voor bron-IP-lost incompatibiliteit tussen Azure Load Balancer en extern bureaublad-Gateway (RD-Gateway). Met deze modus kunt kunt u een farm RD-Gateway in een enkele cloudservice bouwen.

Een andere use-casescenario is media uploaden. Het uploaden van gegevens gebeurt via UDP, maar het besturingselement vlak wordt bereikt via TCP:

* Een client start een TCP-sessie naar het open bare adres met taak verdeling en wordt omgeleid naar een specifieke DIP. Het kanaal blijft actief voor het bewaken van de status van de verbinding.
* Een nieuwe sessie UDP vanaf dezelfde computer wordt gestart met de dezelfde openbare load balancing-eindpunt. De verbinding is omgeleid naar hetzelfde DIP-eindpunt als de vorige TCP-verbinding. Het uploaden van de media kan worden uitgevoerd met een hoge doorvoer terwijl een besturingskanaal via TCP.

> [!NOTE]
> Wanneer een load balancer-set door te verwijderen of toevoegen van een virtuele machine wordt gewijzigd, worden de distributie van aanvragen van clients is herberekend. U kunt geen afhankelijk zijn van nieuwe verbindingen van bestaande clients uiteindelijk te maken op dezelfde server. Bovendien met behulp van de bron-IP distributiemodus voor de affiniteit kan leiden tot een ongelijke distributie van verkeer. Clients die achter een proxy's worden uitgevoerd, kunnen worden gezien als een unieke client-toepassing.

## <a name="configure-source-ip-affinity-settings"></a>Bron-IP-affiniteit-instellingen configureren

### <a name="azure-portal"></a>Azure Portal

U kunt de configuratie van de distributie modus wijzigen door de taakverdelings regel in de portal te wijzigen.

1. Meld u aan bij de Azure Portal en zoek de resource groep met de load balancer die u wilt wijzigen door te klikken op **resource groepen**.
2. Klik op de Blade load balancer overzicht op **taakverdelings regels** onder **instellingen**.
3. Klik op de Blade taakverdelings regels op de taakverdelings regel waarvan u de distributie modus wilt wijzigen.
4. Onder de regel wordt de distributie modus gewijzigd door de vervolg keuzelijst **sessie persistentie** te wijzigen.  De volgende opties zijn beschikbaar:
    
    * **Geen (op hash gebaseerd)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde client door elke virtuele machine kunnen worden verwerkt.
    * **Client-IP (bron-IP-affiniteit 2-tuple)** -geeft aan dat opeenvolgende aanvragen van hetzelfde client-IP-adres worden verwerkt door dezelfde virtuele machine.
    * **Client-IP en-protocol (bron-IP-affiniteit 3-tuple)** : Hiermee geeft u op dat opeenvolgende aanvragen van dezelfde combi natie van client-IP-adres en-protocol worden verwerkt door dezelfde virtuele machine.

5. Kies de distributie modus en klik vervolgens op **Opslaan**.

### <a name="azure-powershell"></a>Azure PowerShell

Voor virtuele machines die met Resource Manager zijn geïmplementeerd, gebruikt u Power shell om de distributie-instellingen voor Load Balancer te wijzigen voor een bestaande regel voor taak verdeling. De volgende opdracht werkt de distributie modus bij: 

```azurepowershell-interactive
$lb = Get-AzLoadBalancer -Name MyLb -ResourceGroupName MyLbRg
$lb.LoadBalancingRules[0].LoadDistribution = 'sourceIp'
Set-AzLoadBalancer -LoadBalancer $lb
```

Voor klassieke virtuele machines, gebruikt u Azure PowerShell om de distributie-instellingen te wijzigen. Een Azure-eindpunt toevoegen aan een virtuele machine en de distributiemodus voor load balancer configureren:

```azurepowershell-interactive
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Stel de waarde van de `LoadBalancerDistribution` -element voor de gewenste hoeveelheid taakverdeling. Geef sourceIP voor taakverdeling van 2-tuple (bron-IP en doel-IP). Geef sourceIPProtocol voor taakverdeling 3-tuple (bron-IP, doel-IP en protocol type). Geef geen op voor het standaardgedrag van 5-tuple load balancing.

Een eindpunt load balancer-distributie modus-configuratie ophalen met behulp van deze instellingen:

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

Wanneer het `LoadBalancerDistribution` element niet aanwezig is, gebruikt Azure Load Balancer het standaard algoritme van 5-tuple.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Distributiemodus configureren voor de eindpuntset met gelijke

Als eindpunten onderdeel van een eindpuntset met load balancing-zijn, moet de distributiemodus worden geconfigureerd op de eindpuntset met gelijke:

```azurepowershell-interactive
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Distributiemodus voor Cloud Services-eindpunten configureren

Gebruik de Azure SDK voor .NET 2.5 om bij te werken van uw cloudservice. De eindpuntinstellingen voor Cloud Services worden aangebracht in het csdef-bestand. Voor het bijwerken van de distributiemodus voor load balancer voor een implementatie van Cloud Services, is de upgrade van een implementatie vereist.

Hier volgt een voorbeeld van wijzigingen voor eindpuntinstellingen .csdef:

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

Het volgende voorbeeld ziet u hoe de distributiemodus voor load balancer voor een opgegeven set met load balancing in een implementatie opnieuw configureren. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Distributiemodus voor geïmplementeerde load balancer-set wijzigen

Het model van de klassieke Azure-implementatie gebruiken om een bestaande implementatieconfiguratie te wijzigen. Voeg de `x-ms-version` kop- en stel de waarde in op versie 2014-09-01 of hoger.

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

Zoals eerder beschreven, stel de `LoadBalancerDistribution` element sourceIP voor 2-tuple affiniteit, sourceIPProtocol voor relatie met 3-tuple of none voor geen relatie met (5-tuple affiniteit).

#### <a name="response"></a>Antwoord

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van de interne Load Balancer van Azure](load-balancer-internal-overview.md)
* [Aan de slag met het configureren van een internetgerichte load balancer](load-balancer-get-started-internet-arm-ps.md)
* [TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)
