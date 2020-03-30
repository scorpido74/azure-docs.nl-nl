---
title: Verbinding maken met een virtuele sql server-machine op Azure (klassiek) | Microsoft Documenten
description: Meer informatie over hoe u verbinding maken met SQL Server die wordt uitgevoerd op een virtuele machine in Azure. In dit onderwerp wordt het klassieke implementatiemodel gebruikt. De scenario's verschillen afhankelijk van de netwerkconfiguratie en de locatie van de client.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249709"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Verbinding maken met een virtuele SQL Server-machine op Azure (klassieke implementatie)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Klassiek](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Overzicht
In dit onderwerp wordt beschreven hoe u verbinding maken met uw SQL Server-exemplaar dat wordt uitgevoerd op een virtuele Azure-machine. Het omvat een aantal [algemene connectiviteitsscenario's](#connection-scenarios) en biedt vervolgens [gedetailleerde stappen voor het configureren van SQL Server-connectiviteit in een Azure VM.](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)

> [!IMPORTANT] 
> Azure heeft twee verschillende implementatiemodellen voor het maken en werken met resources: [Resource Manager en Classic.](../../../azure-resource-manager/management/deployment-models.md) In dit artikel wordt het implementatiemodel Classic gebruikt. U doet er verstandig aan voor de meeste nieuwe implementaties het Resource Manager-model te gebruiken. Zie Verbinding maken met een [virtuele sql server op Azure met Behulp van ResourceBeheer](../sql/virtual-machines-windows-sql-connect.md)als u Vm's voor Resourcebeheer gebruikt.

## <a name="connection-scenarios"></a>Verbindingsscenario's
De manier waarop een client verbinding maakt met SQL Server die op een virtuele machine wordt uitgevoerd, verschilt afhankelijk van de locatie van de client en de machine/netwerkconfiguratie. Deze scenario's omvatten:

* [Verbinding maken met SQL Server in dezelfde cloudservice](#connect-to-sql-server-in-the-same-cloud-service)
* [Verbinding maken met SQL Server via internet](#connect-to-sql-server-over-the-internet)
* [Verbinding maken met SQL Server in hetzelfde virtuele netwerk](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Voordat u verbinding maakt met een van deze methoden, moet u de stappen in dit artikel volgen [om de connectiviteit te configureren.](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm)
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Verbinding maken met SQL Server in dezelfde cloudservice
Meerdere virtuele machines kunnen worden gemaakt in dezelfde cloudservice. Zie Hoe u virtuele [machines wilt verbinden met een virtueel netwerk of cloudservice](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service)om dit scenario voor virtuele machines te begrijpen. Dit scenario is wanneer een client op een virtuele machine probeert verbinding te maken met SQL Server die wordt uitgevoerd op een andere virtuele machine in dezelfde cloudservice.

In dit scenario u verbinding maken met de **VM-naam** (ook weergegeven als **computernaam** of **hostnaam** in de portal). Dit is de naam die u tijdens het maken voor de vm hebt opgegeven. Als u bijvoorbeeld uw SQL VM **mysqlvm**hebt benoemd, kan een client-vm in dezelfde cloudservice de volgende verbindingstekenreeks gebruiken om verbinding te maken:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Verbinding maken met SQL Server via internet
Als u vanaf internet verbinding wilt maken met uw SQL Server-databaseengine, moet u een eindpunt voor virtuele machines maken voor binnenkomende TCP-communicatie. Deze configuratiestap van Azure zorgt ervoor dat binnenkomend TCP-poortverkeer naar een TCP-poort wordt geleid die toegankelijk is voor de virtuele machine.

Als u verbinding wilt maken via internet, moet u de DNS-naam van de VM en het VM-eindpuntpoortnummer (later in dit artikel) gebruiken. Als u de DNS-naam wilt zoeken, navigeert u naar de Azure-portal en selecteert u **Virtuele machines (klassiek).** Selecteer vervolgens uw virtuele machine. De **DNS-naam** wordt weergegeven in de sectie **Overzicht.**

Denk bijvoorbeeld aan een klassieke virtuele machine met de naam **mysqlvm** met een DNS-naam van **mysqlvm7777.cloudapp.net** en een VM-eindpunt van **57500**. Uitgaande van goed geconfigureerde connectiviteit, kan de volgende verbindingstekenreeks worden gebruikt om toegang te krijgen tot de virtuele machine vanaf elke locatie op het internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Hoewel dit connectiviteit voor clients via internet mogelijk maakt, betekent dit niet dat iemand verbinding kan maken met uw SQL Server. Externe klanten moeten de juiste gebruikersnaam en wachtwoord. Gebruik voor extra beveiliging de bekende poort 1433 niet voor het eindpunt van de openbare virtuele machine. En indien mogelijk, overweeg dan het toevoegen van een ACL op uw eindpunt om het verkeer alleen te beperken tot de klanten die u toestaat. Zie [ACL beheren op een eindpunt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint)voor instructies over het gebruik van ACL's met eindpunten.

> [!NOTE]
> Het is belangrijk op te merken dat wanneer u deze techniek gebruikt om met SQL Server te communiceren, alle uitgaande gegevens uit het Azure-datacenter onderworpen zijn aan normale [prijzen voor uitgaande gegevensoverdrachten.](https://azure.microsoft.com/pricing/details/data-transfers/)
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Verbinding maken met SQL Server in hetzelfde virtuele netwerk
[Virtueel netwerk](../../../virtual-network/virtual-networks-overview.md) maakt extra scenario's mogelijk. U VM's in hetzelfde virtuele netwerk verbinden, zelfs als deze VM's in verschillende cloudservices bestaan. En met een [site-to-site VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)u een hybride architectuur maken die VM's verbindt met on-premises netwerken en machines.

Met virtuele netwerken u ook uw Azure VM's aansluiten bij een domein. Dit is de enige manier om Windows-verificatie te gebruiken voor SQL Server. De andere verbindingsscenario's vereisen SQL-verificatie met gebruikersnamen en wachtwoorden.

Als u een domeinomgeving en Windows-verificatie wilt configureren, hoeft u de stappen in dit artikel niet te gebruiken om het openbare eindpunt of de SQL-verificatie en aanmeldingen te configureren. In dit scenario u verbinding maken met uw SQL Server-instantie door de SQL Server VM-naam op te geven in de verbindingstekenreeks. In het volgende voorbeeld wordt ervan uitgegaan dat windows-verificatie ook is geconfigureerd en dat de gebruiker toegang heeft gekregen tot het SQL Server-exemplaar.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Stappen voor het configureren van SQL Server-connectiviteit in een Azure VM
De volgende stappen laten zien hoe u verbinding maakt met de SQL Server-instantie via internet met SQL Server Management Studio (SSMS). Dezelfde stappen zijn echter van toepassing op het toegankelijk maken van uw virtuele SQL Server-machine voor uw toepassingen, die zowel on-premises als in Azure worden uitgevoerd.

Voordat u verbinding maken met de instantie SQL Server van een andere virtuele machine of internet, moet u de volgende taken uitvoeren zoals beschreven in de volgende secties:

* [Een TCP-eindpunt maken voor de virtuele machine](#create-a-tcp-endpoint-for-the-virtual-machine)
* [TCP-poorten openen in de Windows-firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [SQL Server configureren om te luisteren naar het TCP-protocol](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [SQL Server configureren voor verificatie in gemengde modus](#configure-sql-server-for-mixed-mode-authentication)
* [Aanmelding voor SQL Server-verificatie maken](#create-sql-server-authentication-logins)
* [De DNS-naam van de virtuele machine achterhalen](#determine-the-dns-name-of-the-virtual-machine)
* [Verbinding maken met de Database-engine vanaf een andere computer](#connect-to-the-database-engine-from-another-computer)

Het verbindingspad wordt samengevat door het volgende diagram:

![Verbinding maken met een virtuele SQL Server-machine](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Volgende stappen
Als u ook van plan bent om AlwaysOn-beschikbaarheidsgroepen te gebruiken voor hoge beschikbaarheid en herstel na noodgevallen, moet u overwegen een listener te implementeren. Databaseclients maken verbinding met de listener in plaats van rechtstreeks met een van de SQL Server-exemplaren. De listener leidt clients naar de primaire replica in de beschikbaarheidsgroep. Zie [Een ILB-listener configureren voor AlwaysOn-beschikbaarheidsgroepen in Azure voor](../classic/ps-sql-int-listener.md)meer informatie.

Het is belangrijk om alle aanbevolen beveiligingsprocedures voor SQL Server die op een virtuele Azure-machine worden uitgevoerd, te bekijken. Zie [Veiligheidsoverwegingen voor SQL Server in virtuele Azure-machines](../sql/virtual-machines-windows-sql-security.md) voor meer informatie.

[Bekijk het leertraject](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) voor virtuele SQL Server-machines in Azure. 

Zie [SQL Server op Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's.

