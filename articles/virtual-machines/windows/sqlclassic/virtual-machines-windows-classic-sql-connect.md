---
title: Verbinding maken met een SQL Server virtuele machine in azure (klassiek) | Microsoft Docs
description: Meer informatie over hoe u verbinding maakt met SQL Server die worden uitgevoerd op een virtuele machine in Azure. In dit onderwerp wordt gebruikgemaakt van het klassieke implementatie model. De scenario's variëren afhankelijk van de netwerk configuratie en de locatie van de client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 4627d9c4fa5c87e8e80ab80892062dabd77e9229
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978224"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Verbinding maken met een virtuele SQL Server-machine op Azure (klassieke implementatie)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klassiek](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Overzicht
In dit onderwerp wordt beschreven hoe u verbinding maakt met uw SQL Server-exemplaar dat wordt uitgevoerd op een virtuele Azure-machine. Dit omvat enkele [algemene verbindings scenario's](#connection-scenarios) en bevat [gedetailleerde stappen voor het configureren van SQL Server connectiviteit in een Azure-VM](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatie modellen voor het maken van en werken met resources: [Resource Manager en klassiek](../../../azure-resource-manager/management/deployment-models.md). In dit artikel wordt beschreven hoe u het klassieke implementatie model gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Als u Resource Manager-Vm's gebruikt, raadpleegt u [verbinding maken met een SQL Server virtuele machine in azure met behulp van Resource Manager](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Verbindings scenario's
De manier waarop een client verbinding maakt met SQL Server die op een virtuele machine wordt uitgevoerd, verschilt afhankelijk van de locatie van de client en de configuratie van de computer/netwerk. Deze scenario's omvatten:

* [Verbinding maken met SQL Server in dezelfde Cloud service](#connect-to-sql-server-in-the-same-cloud-service)
* [Via Internet verbinding maken met SQL Server](#connect-to-sql-server-over-the-internet)
* [Verbinding maken met SQL Server in hetzelfde virtuele netwerk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Voordat u met een van deze methoden verbinding maakt, moet u de [stappen in dit artikel volgen om de verbinding te configureren](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Verbinding maken met SQL Server in dezelfde Cloud service
Er kunnen meerdere virtuele machines worden gemaakt in dezelfde Cloud service. Zie [virtuele machines verbinden met een virtueel netwerk of een Cloud service](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service)voor meer informatie over dit scenario voor virtuele machines. Dit scenario is wanneer een client op één virtuele machine verbinding probeert te maken met SQL Server die worden uitgevoerd op een andere virtuele machine in dezelfde Cloud service.

In dit scenario kunt u verbinding maken met behulp van de VM- **naam** (ook weer gegeven als **computer naam** of **hostnaam** in de portal). Dit is de naam die u voor de virtuele machine hebt ingesteld tijdens het maken. Als u bijvoorbeeld de SQL-VM **mysqlvm**heet, kan een client-vm in dezelfde Cloud service de volgende Connection String gebruiken om verbinding te maken:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Via Internet verbinding maken met SQL Server
Als u via Internet verbinding wilt maken met uw SQL Server data base-engine, moet u een eind punt voor de virtuele machine maken voor binnenkomende TCP-communicatie. Deze configuratiestap van Azure zorgt ervoor dat binnenkomend TCP-poortverkeer naar een TCP-poort wordt geleid die toegankelijk is voor de virtuele machine.

Als u verbinding wilt maken via internet, moet u de DNS-naam van de virtuele machine en het poort nummer van het VM-eind punt gebruiken (later in dit artikel geconfigureerd). Als u de DNS-naam wilt vinden, gaat u naar de Azure Portal en selecteert u **virtuele machines (klassiek)** . Selecteer vervolgens de virtuele machine. De **DNS-naam** wordt weer gegeven in de sectie **overzicht** .

Denk bijvoorbeeld aan een klassieke virtuele machine met de naam **mysqlvm** met een DNS-naam van **MYSQLVM7777.CLOUDAPP.net** en een VM-eind punt van **57500**. Ervan uitgaande dat de connectiviteit correct is geconfigureerd, kan de volgende connection string worden gebruikt voor toegang tot de virtuele machine vanaf elke locatie op Internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Hoewel Hiermee connectiviteit voor clients via internet is ingeschakeld, betekent dit niet dat iedereen verbinding kan maken met uw SQL Server. Externe clients hebben de juiste gebruikers naam en wacht woord. Gebruik voor extra beveiliging niet de bekende poort 1433 voor het eind punt van de open bare virtuele machine. En als dat mogelijk is, kunt u een ACL op uw eind punt toevoegen om alleen verkeer te beperken voor de clients die u toestaat. Zie [de ACL voor een eind punt beheren](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)voor instructies over het gebruik van acl's met eind punten.

> [!NOTE]
> Het is belang rijk te weten dat wanneer u deze methode gebruikt om met SQL Server te communiceren, alle uitgaande gegevens van het Azure-Data Center onderhevig zijn aan de normale [prijzen voor uitgaande gegevens overdracht](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Verbinding maken met SQL Server in hetzelfde virtuele netwerk
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) biedt extra scenario's. U kunt virtuele machines in hetzelfde virtuele netwerk verbinden, zelfs als deze Vm's bestaan in verschillende Cloud Services. En met een [site-naar-site-VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)kunt u een hybride architectuur maken waarmee vm's worden verbonden met on-premises netwerken en machines.

Met virtuele netwerken kunt u ook uw Azure-Vm's toevoegen aan een domein. Dit is de enige manier om Windows-verificatie te gebruiken om SQL Server. De andere verbindings scenario's vereisen SQL-verificatie met gebruikers namen en wacht woorden.

Als u een domein omgeving en Windows-verificatie wilt configureren, hoeft u de stappen in dit artikel niet te gebruiken om het open bare eind punt of de SQL-verificatie en-aanmeldingen te configureren. In dit scenario kunt u verbinding maken met uw SQL Server-exemplaar door de SQL Server VM-naam op te geven in de connection string. In het volgende voor beeld wordt ervan uitgegaan dat Windows-verificatie ook is geconfigureerd en dat de gebruiker toegang heeft gekregen tot het SQL Server exemplaar.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Stappen voor het configureren van SQL Server connectiviteit in een Azure VM
De volgende stappen laten zien hoe u via Internet verbinding maakt met het SQL Server-exemplaar met behulp van SQL Server Management Studio (SSMS). Dezelfde stappen zijn echter van toepassing om uw SQL Server virtuele machine toegankelijk te maken voor uw toepassingen, zowel on-premises als in Azure.

Voordat u verbinding kunt maken met het exemplaar van SQL Server vanaf een andere virtuele machine of het Internet, moet u de volgende taken uitvoeren, zoals beschreven in de onderstaande secties:

* [Een TCP-eind punt voor de virtuele machine maken](#create-a-tcp-endpoint-for-the-virtual-machine)
* [TCP-poorten openen in de Windows Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [SQL Server configureren om te Luis teren naar het TCP-protocol](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [SQL Server configureren voor verificatie in gemengde modus](#configure-sql-server-for-mixed-mode-authentication)
* [SQL Server-verificatie aanmeldingen maken](#create-sql-server-authentication-logins)
* [De DNS-naam van de virtuele machine bepalen](#determine-the-dns-name-of-the-virtual-machine)
* [Verbinding maken met de data base-engine vanaf een andere computer](#connect-to-the-database-engine-from-another-computer)

Het pad van de verbinding wordt samenvatten in het volgende diagram:

![Verbinding maken met een virtuele machine van SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Volgende stappen
Als u ook van plan bent om AlwaysOn-beschikbaarheidsgroepen te gebruiken voor hoge Beschik baarheid en herstel na nood gevallen, kunt u overwegen een listener te implementeren. Data base-clients maken verbinding met de listener in plaats van rechtstreeks naar een van de SQL Server exemplaren. De listener stuurt clients naar de primaire replica in de beschikbaarheids groep. Zie [een ILB-listener configureren voor AlwaysOn-beschikbaarheidsgroepen in azure](../classic/ps-sql-int-listener.md)voor meer informatie.

Het is belang rijk dat u alle aanbevolen beveiligings procedures controleert voor SQL Server die worden uitgevoerd op een virtuele machine van Azure. Zie [Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines](../sql/virtual-machines-windows-sql-security.md) voor meer informatie.

[Bekijk het leertraject](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) voor virtuele SQL Server-machines in Azure. 

Zie [SQL Server op azure virtual machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in azure vm's.

