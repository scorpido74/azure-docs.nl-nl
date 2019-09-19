---
title: Een interne Load Balancer maken - klassieke versie van PowerShell
titlesuffix: Azure Load Balancer
description: Meer informatie over hoe u met PowerShell een interne load balancer maakt in het klassieke implementatiemodel
services: load-balancer
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms:custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: genli
ms.openlocfilehash: b864a4bf352c547779bb368650971fa8b805fca7
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090967"
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Aan de slag met het maken van een interne load balancer (klassiek) met behulp van PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure-CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloudservices](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../azure-resource-manager/resource-manager-deployment-model.md).  Dit artikel gaat over het gebruik van het klassieke implementatiemodel. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Lees [meer informatie over het uitvoeren van deze stappen met het Resource Manager-model](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Een interne load balancer-set maken voor virtuele machines

Ga als volgt te werk om een interne load balancer-set te maken, evenals de servers die hun verkeer naar deze load balancer verzenden:

1. Maak een exemplaar van Interne taakverdeling dat het eindpunt wordt van binnenkomend verkeer dat gelijkmatig moet worden verdeeld over de servers van een set met gelijke taakverdeling.
2. Voeg eindpunten toe die overeenkomen met de virtuele machines die het binnenkomende verkeer ontvangen.
3. Configureer de servers die het verkeer voor gelijke taakverdeling verzenden, zodanig dat het verkeer wordt verzonden naar het VIP-adres (virtuele IP) van het exemplaar van Interne taakverdeling.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Stap 1: Een exemplaar van een interne taak verdeling maken

Voor een bestaande cloudservice of een cloudservice die in een regionaal virtueel netwerk is geïmplementeerd, kunt u een exemplaar van Interne taakverdeling maken met de volgende Windows PowerShell-opdrachten:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Als de Windows PowerShell-cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) op deze manier wordt gebruikt, wordt de parameterset DefaultProbe gebruikt. Zie [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) voor meer informatie over aanvullende parametersets.

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Stap 2: Eind punten toevoegen aan het exemplaar van de interne taak verdeling

Hier volgt een voorbeeld:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Stap 3: Uw servers zo configureren dat ze hun verkeer verzenden naar het nieuwe eind punt van de interne taak verdeling

U moet de servers waarvan het verkeer gelijkmatig moet worden verdeeld, zodanig configureren dat ze het nieuwe IP-adres (VIP) van het exemplaar van Interne taakverdeling gaan gebruiken. Dit is het adres waarop het exemplaar van Interne taakverdeling luistert. In de meeste gevallen hoeft u alleen een DNS-record voor het VIP van het exemplaar van Interne taakverdeling toe te voegen of aan te passen.

Als u het IP-adres tijdens het maken van het exemplaar van Interne taakverdeling hebt opgegeven, hebt u het VIP al. Anders kunt u het VIP met de volgende opdrachten zien:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

U gebruikt deze opdrachten door de waarden in te vullen en de < en > te verwijderen. Hier volgt een voorbeeld:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Noteer het IP-adres in de weergave van de opdracht Get-AzureInternalLoadBalancer en breng de nodige wijzigingen aan op uw servers of DNS-records om ervoor te zorgen dat er verkeer naar het VIP wordt verzonden.

> [!NOTE]
> Het Microsoft Azure-platform gebruikt een statisch, openbaar routeerbaar IPv4-adres voor diverse beheerscenario's. Het IP-adres is 168.63.129.16. Dit IP-adres mag niet door firewalls worden geblokkeerd. Dit kan onverwacht gedrag veroorzaken.
> Wat Azure Interne taakverdeling betreft, wordt dit IP-adres gebruikt door bewakingstests van de load balancer om de status van virtuele machines in een set met gelijke taakverdeling te bepalen. Als er een netwerkbeveiligingsgroep wordt gebruikt om het verkeer naar virtuele Azure-machines in een set met intern gelijke taakverdeling te beperken of wordt toegepast op een subnet in een virtueel netwerk, zorg er dan voor dat er een netwerkbeveiligingsregel wordt toegevoegd om verkeer van 168.63.129.16 toe te staan.

## <a name="example-of-internal-load-balancing"></a>Voorbeeld van intern gelijke taakverdeling

In de volgende secties wordt het hele proces voor het maken van een set met gelijke taakverdeling voor twee voorbeeldconfiguraties stapsgewijs besproken.

### <a name="an-internet-facing-multi-tier-application"></a>Een internetgerichte toepassing met meerdere lagen

U wilt een databaseservice met gelijke taakverdeling leveren voor een set internetgerichte webservers. Beide serversets worden gehost in één Azure-cloudservice. Webserververkeer op TCP-poort 1433 moet worden verdeeld over twee virtuele machines in de databaselaag. In afbeelding 1 ziet u de configuratie.

![Set met intern gelijke taakverdeling voor de databaselaag](./media/load-balancer-internal-getstarted/IC736321.png)

De configuratie bestaat uit het volgende:

* De bestaande cloudservice die als host fungeert voor de virtuele machines is mytestcloud.
* De twee bestaande databaseservers zijn DB1 en DB2.
* Webservers in de weblaag maken verbinding met de databaseservers in de databaselaag met behulp van het particuliere IP-adres. U kunt ook uw eigen DNS voor het virtuele netwerk gebruiken en handmatig een A-record voor de interne load balancer-set registreren.

Met de volgende opdrachten configureert u een nieuw exemplaar van Interne taakverdeling met de naam **ILBset** en voegt u eindpunten aan de virtuele machines toe die overeenkomen met de twee databaseservers:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Een configuratie van Interne taakverdeling verwijderen

Gebruik de volgende opdrachten als u een virtuele machine als een eindpunt uit een exemplaar van een interne load balancer wilt verwijderen:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

U gebruikt deze opdrachten door de waarden in te vullen en de < en > te verwijderen.

Hier volgt een voorbeeld:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Gebruik de volgende opdrachten als u een exemplaar van een interne load balancer uit een cloudservice wilt verwijderen:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

U gebruikt deze opdrachten door de waarde in te vullen en de < en > te verwijderen.

Hier volgt een voorbeeld:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Meer informatie over cmdlets voor interne load balancers

Voor meer informatie over de cmdlets van Interne taakverdeling voert u de volgende opdrachten uit via een Windows PowerShell-prompt:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Volgende stappen

[Een distributiemodus voor de load balancer configureren met bron-IP-affiniteit](load-balancer-distribution-mode.md)

[TCP-time-outinstellingen voor inactiviteit voor de load balancer configureren](load-balancer-tcp-idle-timeout.md)

