---
title: Verbinding maken met een VIRTUELE SQL Server -machine (Resource Manager) | Microsoft Documenten
description: Meer informatie over hoe u verbinding maken met SQL Server die wordt uitgevoerd op een virtuele machine in Azure. In dit onderwerp wordt het klassieke implementatiemodel gebruikt. De scenario's verschillen afhankelijk van de netwerkconfiguratie en de locatie van de client.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: deb337d989a3658e909cefa7a9ab028e37792562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243170"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Verbinding maken met een virtuele sql server-machine op Azure

## <a name="overview"></a>Overzicht

In dit onderwerp wordt beschreven hoe u verbinding maken met uw SQL Server-exemplaar dat wordt uitgevoerd op een virtuele Azure-machine. Het omvat een aantal [algemene connectiviteitsscenario's](#connection-scenarios) en biedt vervolgens [stappen in de portal voor het wijzigen van verbindingsinstellingen.](#change) Als u problemen moet oplossen of connectiviteit buiten de portal wilt configureren, raadpleegt u de [handmatige configuratie](#manual) aan het einde van dit onderwerp. 

Zie [Een SQL Server Virtual Machine inrichten op Azure](virtual-machines-windows-portal-sql-server-provision.md)als u liever een volledige doorloop van zowel provisioning als connectiviteit wilt hebben.

## <a name="connection-scenarios"></a>Verbindingsscenario's

De manier waarop een client verbinding maakt met SQL Server die op een virtuele machine wordt uitgevoerd, verschilt afhankelijk van de locatie van de client en de netwerkconfiguratie.

Als u een SQL Server VM inde inde Azure-portal indient, u het type **SQL-connectiviteit**opgeven.

![Openbare SQL-connectiviteitsoptie tijdens het inrichten](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Uw opties voor connectiviteit zijn:

| Optie | Beschrijving |
|---|---|
| **Public** | Verbinding maken met SQL Server via internet |
| **Privé** | Verbinding maken met SQL Server in hetzelfde virtuele netwerk |
| **Lokale** | Lokaal verbinding maken met SQL Server op dezelfde virtuele machine | 

In de volgende secties worden de **openbare** en **private** opties nader toegelicht.

## <a name="connect-to-sql-server-over-the-internet"></a>Verbinding maken met SQL Server via internet

Als u verbinding wilt maken met uw SQL Server-databaseengine vanaf internet, selecteert u **Openbaar** voor het **SQL-connectiviteitstype** in de portal tijdens de inrichting. De portal doet automatisch de volgende stappen:

* Hiermee schakelt u het TCP/IP-protocol voor SQL Server in.
* Hiermee configureert u een firewallregel om de SQL Server TCP-poort te openen (standaard 1433).
* Hiermee schakelt SQL Server-verificatie in, vereist voor openbare toegang.
* Hiermee configureert u de netwerkbeveiligingsgroep op de VM voor al het TCP-verkeer op de SQL Server-poort.

> [!IMPORTANT]
> De virtuele machineafbeeldingen voor de SQL Server Developer- en Express-edities schakelen het TCP/IP-protocol niet automatisch in. Voor Ontwikkelaars- en Express-edities moet u SQL Server Configuration Manager gebruiken om [het TCP/IP-protocol handmatig in](#manualtcp) te schakelen nadat u de VM hebt gemaakt.

Elke client met internettoegang kan verbinding maken met de SQL Server-instantie door het openbare IP-adres van de virtuele machine of een DNS-label op te geven dat aan dat IP-adres is toegewezen. Als de SQL Server-poort 1433 is, hoeft u deze niet op te geven in de verbindingstekenreeks. De volgende verbindingstekenreeks maakt verbinding met een `sqlvmlabel.eastus.cloudapp.azure.com` SQL VM met een DNS-label met SQL-verificatie (u ook het openbare IP-adres gebruiken).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Hoewel dit connectiviteit voor clients via internet mogelijk maakt, betekent dit niet dat iemand verbinding kan maken met uw SQL Server. Externe klanten moeten de juiste gebruikersnaam en wachtwoord. Voor extra beveiliging u echter de bekende poort 1433 vermijden. Als u SQL Server bijvoorbeeld hebt geconfigureerd om te luisteren op poort 1500 en de juiste regels voor firewall- en netwerkbeveiligingsgroepen hebt ingesteld, u verbinding maken door het poortnummer toe te schrijven aan de servernaam. In het volgende voorbeeld wordt het vorige gewijzigd door een aangepast poortnummer, **1500,** toe te voegen aan de servernaam:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Wanneer u SQL Server in een VM via internet opvraagt, zijn alle uitgaande gegevens uit het Azure-datacenter onderworpen aan normale [prijzen voor uitgaande gegevensoverdrachten.](https://azure.microsoft.com/pricing/details/data-transfers/)

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Verbinding maken met SQL Server binnen een virtueel netwerk

Wanneer u **Privé** kiest voor het **SQL-connectiviteitstype** in de portal, configureert Azure de meeste instellingen die identiek zijn aan **Openbaar.** Het enige verschil is dat er geen netwerkbeveiligingsgroepregel is om extern verkeer toe te staan op de SQL Server-poort (standaard 1433).

> [!IMPORTANT]
> De virtuele machineafbeeldingen voor de SQL Server Developer- en Express-edities schakelen het TCP/IP-protocol niet automatisch in. Voor Ontwikkelaars- en Express-edities moet u SQL Server Configuration Manager gebruiken om [het TCP/IP-protocol handmatig in](#manualtcp) te schakelen nadat u de VM hebt gemaakt.

Privéconnectiviteit wordt vaak gebruikt in combinatie met [Virtual Network](../../../virtual-network/virtual-networks-overview.md), waardoor verschillende scenario's mogelijk zijn. U VM's in hetzelfde virtuele netwerk verbinden, zelfs als deze VM's in verschillende brongroepen bestaan. En met een [site-to-site VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)u een hybride architectuur maken die VM's verbindt met on-premises netwerken en machines.

Met virtuele netwerken u ook uw Azure VM's aansluiten bij een domein. Dit is de enige manier om Windows-verificatie te gebruiken voor SQL Server. De andere verbindingsscenario's vereisen SQL-verificatie met gebruikersnamen en wachtwoorden.

Ervan uitgaande dat u DNS in uw virtuele netwerk hebt geconfigureerd, u verbinding maken met uw SQL Server-instantie door de naam van de SQL Server VM-computer op te geven in de verbindingstekenreeks. In het volgende voorbeeld wordt ook ervan uitgegaan dat Windows-verificatie ook is geconfigureerd en dat de gebruiker toegang heeft gekregen tot het SQL Server-exemplaar.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>SQL-connectiviteitsinstellingen wijzigen

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U de verbindingsinstellingen voor uw virtuele SQL Server-machine wijzigen in de Azure-portal.

1. Selecteer SQL virtuele machines in de **Azure-portal.**

2. Selecteer uw SQL Server VM.

3. Selecteer **Onder Instellingen**de optie **Beveiliging**.

4. Wijzig het **SQL-connectiviteitsniveau** in de vereiste instelling. U dit gebied optioneel gebruiken om de SQL Server-poort of de SQL-verificatieinstellingen te wijzigen.

   ![SQL-connectiviteit wijzigen](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Wacht enkele minuten tot de update is voltooid.

   ![SQL VM-updatemelding](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>TCP/IP inschakelen voor ontwikkelaars- en Express-edities

Wanneer u sql server-connectiviteitsinstellingen wijzigt, schakelt Azure het TCP/IP-protocol voor SQL Server Developer- en Express-edities niet automatisch in. In de onderstaande stappen wordt uitgelegd hoe u TCP/IP handmatig kunt inschakelen, zodat u op afstand via een IP-adres verbinding kunt maken.

Maak eerst verbinding met de SQL Server-machine met extern bureaublad.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Schakel vervolgens het TCP/IP-protocol in met **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Verbinden met SSMS

In de volgende stappen wordt uitgelegd hoe u een optioneel DNS-label voor uw Azure VM maakt en vervolgens verbinding maakt met SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Handmatige configuratie en probleemoplossing

Hoewel de portal opties biedt om de connectiviteit automatisch te configureren, is het handig om te weten hoe u de connectiviteit handmatig configureren. Inzicht in de vereisten kan ook helpen bij het oplossen van problemen.

In de volgende tabel worden de vereisten weergegeven om verbinding te maken met SQL Server die in een Azure-vm wordt uitgevoerd.

| Vereiste | Beschrijving |
|---|---|
| [SQL Server-verificatiemodus inschakelen](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | SQL Server-verificatie is nodig om op afstand verbinding te maken met de virtuele machine, tenzij u Active Directory op een virtueel netwerk hebt geconfigureerd. |
| [Een SQL-aanmelding maken](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Als u SQL-verificatie gebruikt, hebt u een SQL-login nodig met een gebruikersnaam en wachtwoord met machtigingen voor uw doeldatabase. |
| [TCP/IP-protocol inschakelen](#manualtcp) | SQL Server moet verbindingen via TCP toestaan. |
| [Firewallregel inschakelen voor de SQL Server-poort](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | De firewall op de VM moet binnenkomend verkeer toestaan op de SQL Server-poort (standaard 1433). |
| [Een netwerkbeveiligingsgroepregel maken voor TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | U moet toestaan dat de VM verkeer ontvangt op de SQL Server-poort (standaard 1433) als u verbinding wilt maken via internet. Lokale en virtuele netwerkverbindingen vereisen dit niet. Dit is de enige stap die nodig is in de Azure-portal. |

> [!TIP]
> De stappen in de bovenstaande tabel worden voor u uitgevoerd wanneer u de verbinding in de portal configureert. Gebruik deze stappen alleen om uw configuratie te bevestigen of om handmatig connectiviteit in te stellen voor SQL Server.

## <a name="next-steps"></a>Volgende stappen

Zie [Een SQL Server Virtual Machine inrichten op Azure](virtual-machines-windows-portal-sql-server-provision.md)voor het inrichten van instructies samen met deze verbindingsstappen.

Zie [SQL Server op Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)voor andere onderwerpen met betrekking tot het uitvoeren van SQL Server in Azure VM's.