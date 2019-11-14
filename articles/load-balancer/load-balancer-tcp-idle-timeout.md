---
title: load balancer TCP-time-out voor inactiviteit in azure configureren
titleSuffix: Azure Load Balancer
description: In dit artikel vindt u informatie over het configureren van Azure Load Balancer TCP-time-out voor inactiviteit.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 530bfbe85a564b3dd517e14df819586dee332a78
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076968"
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Instellingen voor time-out voor TCP-inactiviteit configureren voor Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

In de standaard configuratie heeft Azure Load Balancer een time-outinstellingen voor inactiviteit van vier minuten. Als een periode van inactiviteit langer is dan de time-outwaarde, is er geen garantie dat de TCP-of HTTP-sessie tussen de client en de Cloud service wordt bewaard.

Wanneer de verbinding wordt gesloten, kan uw client toepassing het volgende fout bericht ontvangen: ' de onderliggende verbinding is gesloten: er is een verbinding die naar verwachting wordt gehouden door de server verbroken. '

Een veelvoorkomende procedure is het gebruik van een TCP-Keep-Alive. Deze procedure houdt de verbinding gedurende een langere periode actief. Zie voor meer informatie deze [.net-voor beelden](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Als Keep-Alive is ingeschakeld, worden pakketten verzonden tijdens peri Oden van inactiviteit op de verbinding. Deze Keep-Alive-pakketten zorgen ervoor dat de waarde voor de time-out voor inactiviteit nooit wordt bereikt en de verbinding gedurende een lange periode wordt bewaard.

Deze instelling werkt alleen voor binnenkomende verbindingen. Om te voor komen dat de verbinding wordt verbroken, moet u de TCP-keepalive configureren met een interval van minder dan de instelling voor niet-actieve time-out of de waarde voor de time-out voor inactiviteit verhogen. Ter ondersteuning van dergelijke scenario's hebben we ondersteuning toegevoegd voor een Configureer bare time-out voor inactiviteit. U kunt deze nu instellen voor een duur van 4 tot 30 minuten.

TCP Keep-Alive werkt goed voor scenario's waarbij de levens duur van de accu geen beperking is. Het wordt niet aanbevolen voor mobiele toepassingen. Het gebruik van een TCP-Keep-Alive in een mobiele toepassing kan de accu van het apparaat sneller leeg laten.

![TCP-time-out](./media/load-balancer-tcp-idle-timeout/image1.png)

In de volgende secties wordt beschreven hoe u instellingen voor inactieve time-outs wijzigt in virtuele machines en Cloud Services.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>De TCP-time-out voor uw open bare IP-adres op exemplaar niveau configureren tot 15 minuten

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` is optioneel. Als deze niet is ingesteld, is de standaard time-out 4 minuten. Het bereik van de acceptabele time-out is 4 tot 30 minuten.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Time-out voor inactiviteit instellen bij het maken van een Azure-eind punt op een virtuele machine

Als u de time-outinstelling voor een eind punt wilt wijzigen, gebruikt u het volgende:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Gebruik de volgende opdracht om de configuratie van de time-out voor inactiviteit op te halen:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>De TCP-time-out instellen voor een eindpuntset met gelijke taak verdeling

Als eind punten deel uitmaken van een eindpuntset met gelijke taak verdeling, moet de TCP-time-out worden ingesteld voor de eindpuntset met gelijke taak verdeling. Bijvoorbeeld:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Time-outinstellingen voor Cloud Services wijzigen

U kunt de Azure SDK gebruiken om uw Cloud service bij te werken. U maakt eindpunt instellingen voor Cloud Services in het. csdef-bestand. Voor het bijwerken van de TCP-time-out voor de implementatie van een Cloud service is een implementatie-upgrade vereist. Een uitzonde ring hierop is als de TCP-time-out alleen is opgegeven voor een openbaar IP-adres. Open bare IP-instellingen bevinden zich in het. cscfg-bestand en u kunt ze bijwerken via implementatie-update en-upgrade.

De csdef-wijzigingen voor de instellingen van het eind punt zijn:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

De. cscfg-wijzigingen voor de time-outinstelling voor open bare Ip's zijn:

```xml
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

## <a name="rest-api-example"></a>REST API-voor beeld

U kunt de time-out voor TCP-inactiviteit configureren met behulp van de Service Management-API. Zorg ervoor dat de `x-ms-version`-header is ingesteld op versie `2014-06-01` of hoger. De configuratie van de opgegeven invoer eindpunten met gelijke taak verdeling bijwerken op alle virtuele machines in een implementatie.

### <a name="request"></a>Aanvraag

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Antwoord

```xml
<LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
        <Path>path-of-probe</Path>
        <Port>port-assigned-to-probe</Port>
        <Protocol>probe-protocol</Protocol>
        <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
        <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
        <Rules>
        <Rule>
            <Order>priority-of-the-rule</Order>
            <Action>permit-rule</Action>
            <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
            <Description>description-of-the-rule</Description>
        </Rule>
        </Rules>
    </EndpointACL>
    </InputEndpoint>
</LoadBalancedEndpointList>
```

## <a name="next-steps"></a>Volgende stappen

[Overzicht van interne load balancer](load-balancer-internal-overview.md)

[Aan de slag met het configureren van een Internet gerichte load balancer](load-balancer-get-started-internet-arm-ps.md)

[Een distributiemodus voor de load balancer configureren](load-balancer-distribution-mode.md)
