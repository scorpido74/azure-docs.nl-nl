---
title: Problemen met netwerkvirtuele apparaten oplossen in Azure | Microsoft Documenten
description: Meer informatie over het oplossen van problemen met virtuele apparaten in Azure in het netwerk.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: b998043bc7d896989590ac21db5f309a81cc02bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056837"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemen met virtuele apparaten in Azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U problemen en fouten ondervinden van VM- of VPN-connectiviteit bij het gebruik van een Network Virtual Appliance (NVA) van derden in Microsoft Azure. In dit artikel vindt u basisstappen om basisvereisten voor Azure Platform voor NVA-configuraties te valideren.

Technische ondersteuning voor NVA's van derden en hun integratie met het Azure-platform wordt geleverd door de NVA-leverancier.

> [!NOTE]
> Als u een verbindings- of routeringsprobleem hebt waarbij een NVA betrokken is, moet u rechtstreeks [contact opnemen met de leverancier van de NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Checklist voor het oplossen van problemen met NVA-leverancier

- Software-updates voor NVA VM-software
- Instelling en functionaliteit van serviceaccount
- Door de gebruiker gedefinieerde routes (UDR's) op virtuele netwerksubnetten die verkeer naar NVA leiden
- UDR's op virtuele netwerksubnetten die het verkeer van NVA leiden
- Routeringstabellen en regels binnen de NVA (bijvoorbeeld van NIC1 naar NIC2)
- Tracering op NVA NIC's om het ontvangen en verzenden van netwerkverkeer te verifiëren
- Bij het gebruik van een Standaard SKU en Openbare IP's moet er een NSG zijn gemaakt en moet er een expliciete regel zijn om het verkeer naar de NVA te laten leiden.

## <a name="basic-troubleshooting-steps"></a>Basisstappen voor het oplossen van problemen

- De basisconfiguratie controleren
- NvA-prestaties controleren
- Geavanceerde netwerkprobleemoplossing

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Controleer de minimale configuratievereisten voor NVA's op Azure

Elke NVA heeft basisconfiguratievereisten om correct te kunnen functioneren op Azure. In de volgende sectie vindt u de stappen om deze basisconfiguraties te verifiëren. Neem voor meer informatie [contact op met de leverancier van de NVA.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

**Controleren of IP forwarding is ingeschakeld op NVA**

Azure Portal gebruiken

1. Zoek de NVA-bron in de [Azure-portal,](https://portal.azure.com)selecteer Netwerken en selecteer vervolgens de netwerkinterface.
2. Selecteer IP-configuratie op de pagina Netwerkinterface.
3. Zorg ervoor dat IP-forwarding is ingeschakeld.

PowerShell gebruiken

1. Open PowerShell en meld u aan bij uw Azure-account.
2. Voer de volgende opdracht uit (vervang de waarden tussen haakjes door uw gegevens):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Schakel de eigenschap **EnableIPForwarding in.**
4. Als IP-forwarding niet is ingeschakeld, voert u de volgende opdrachten uit om dit in te schakelen:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Controleren op NSG bij gebruik van Standard SKU Pubilc IP** Bij het gebruik van een Standaard SKU en Openbare IP's moet er een NSG zijn gemaakt en moet er een expliciete regel zijn om het verkeer naar de NVA toe te staan.

**Controleer of het verkeer kan worden doorgestuurd naar de NVA**

1. Open **Netwerkwatcher**op [Azure-portal](https://portal.azure.com)en selecteer **Volgende hop**.
2. Geef een VM op die is geconfigureerd om het verkeer om te leiden naar de NVA en een doel-IP-adres waarop de volgende hop kan worden bekeken. 
3. Als de NVA niet wordt vermeld als de **volgende hop,** controleert en werkt u de Azure-routetabellen in en werkt u deze bij.

**Controleer of het verkeer de NVA kan bereiken**

1. Open **netwerkwatcher**in [Azure-portal](https://portal.azure.com)en selecteer **IP-stroomverificatie**. 
2. Geef de VM en het IP-adres van de NVA op en controleer of het verkeer wordt geblokkeerd door netwerkbeveiligingsgroepen (NSG).
3. Als er een NSG-regel is die het verkeer blokkeert, u de NSG in **effectieve beveiligingsregels** lokaliseren en deze vervolgens bijwerken om het verkeer te laten passeren. Voer vervolgens **ip-stroomverificatie** opnieuw uit en gebruik **verbindingsproblemen** om TCP-communicatie van VM naar uw interne of externe IP-adres te testen.

**Controleer of NVA en VM's luisteren naar verwacht verkeer**

1. Maak verbinding met de NVA met RDP of SSH en voer de volgende opdracht uit:

    Voor Windows:

        netstat -an

    Voor Linux:

        netstat -an | grep -i listen
2. Als u de TCP-poort die wordt gebruikt door de NVA-software die in de resultaten wordt weergegeven, niet ziet, moet u de toepassing op de NVA en VM configureren om te luisteren en te reageren op verkeer dat deze poorten bereikt. [Neem indien nodig contact op met de NVA-leverancier voor hulp.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

## <a name="check-nva-performance"></a>Nva-prestaties controleren

### <a name="validate-vm-cpu"></a>VM-cpu valideren

Als het CPU-gebruik bijna 100 procent bevindt, u problemen ondervinden die van invloed zijn op het aantal netwerkpakketten. Uw VM rapporteert de gemiddelde CPU voor een specifieke tijdspanne in de Azure-portal. Tijdens een CPU-piek, onderzoeken welk proces op de gast VM is de oorzaak van de hoge CPU, en te beperken, indien mogelijk. Het kan ook zijn dat u het formaat van de VM moet wijzigen in een grotere SKU-grootte of, voor de grootte van virtuele machines, het aantal instanties moet verhogen of automatisch moet schalen op het CPU-gebruik. Neem voor een van deze problemen, indien nodig, [contact op met de NVA-leverancier voor hulp.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)

### <a name="validate-vm-network-statistics"></a>VM-netwerkstatistieken valideren

Als het VM-netwerk pieken gebruikt of perioden van hoog gebruik weergeeft, moet u mogelijk ook de SKU-grootte van de VM vergroten om hogere doorvoermogelijkheden te verkrijgen. U de VM ook opnieuw implementeren door Accelerated Networking ingeschakeld te hebben. Neem indien nodig contact op met [de NVA-leverancier voor assistentie](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)om te controleren of de NVA de functie Accelerated Networking ondersteunt.

## <a name="advanced-network-administrator-troubleshooting"></a>Probleemoplossing voor geavanceerde netwerkbeheerder

### <a name="capture-network-trace"></a>Netwerktracering vastleggen
Leg een gelijktijdig netwerktracering vast op de bron-VM, de NVA en de doel-VM terwijl u **[PsPing](https://docs.microsoft.com/sysinternals/downloads/psping)** of **Nmap**uitvoert en stop het trace.

1. Voer de volgende opdracht uit om een gelijktijdige netwerktracering vast te leggen:

   **Voor Windows**

   netsh trace start capture=yes tracefile=c:\server_IP.etl scenario=netconnection

   **Voor Linux**

   sudo tcpdump -s0 -i eth0 -X -w vmtrace.cap

2. Gebruik **PsPing** of **Nmap** van de bron-VM `PsPing 10.0.0.4:80` naar `Nmap -p 80 10.0.0.4`de doel-VM (bijvoorbeeld: of ).
3. Open het netwerktracering vanaf de doel-VM met behulp van [Netwerkmonitor](https://www.microsoft.com/download/details.aspx?id=4865) of tcpdump. Pas een weergavefilter toe voor het IP van de bron-VM `IPv4.address==10.0.0.4 (Windows netmon)` `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` waarvan u **PsPing** of **Nmap** hebt uitgevoerd, zoals of (Linux).

### <a name="analyze-traces"></a>Sporen analyseren

Als u de pakketten niet naar de backend VM-tracering ziet binnenkomen, is er waarschijnlijk een NSG- of UDR-storing of zijn de NVA-routeringstabellen onjuist.

Als de inkomende pakketten wel worden weergegeven, maar er geen antwoord is, moet u mogelijk een probleem met een VM-toepassing of firewall oplossen. Neem voor een van deze problemen [contact op met de NVA-leverancier voor hulp als dat nodig is.](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines)