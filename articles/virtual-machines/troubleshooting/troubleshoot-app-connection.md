---
title: Problemen met de toegang tot VM-toepassingen in Azure oplossen | Microsoft Documenten
description: Gebruik deze gedetailleerde stappen voor het oplossen van problemen om problemen bij het maken van verbinding met toepassingen die op virtuele machines in Azure worden uitgevoerd, te isoleren.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan de toepassing niet starten, het programma wordt niet geopend, de luisterpoort geblokkeerd, kan het programma niet starten, de luisterpoort geblokkeerd
ms.assetid: b9ff7cd0-0c5d-4c3c-a6be-3ac47abf31ba
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: caf73ffbc18a603ace22acfbd0da490048da698a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058124"
---
# <a name="troubleshoot-application-connectivity-issues-on-virtual-machines-in-azure"></a>Problemen met toepassingsverbindingen op virtuele machines in Azure oplossen

Er zijn verschillende redenen waarom u geen verbinding maken met een toepassing die wordt uitgevoerd op een virtuele Azure-machine (VM). Redenen zijn onder meer de toepassing niet draaien of luisteren op de verwachte poorten, de luisterpoort geblokkeerd, of netwerkregels niet correct doorgeven verkeer naar de toepassing. In dit artikel wordt een methodische benadering beschreven om het probleem te vinden en te corrigeren.

Als u problemen ondervindt bij het maken van verbinding met uw VM via RDP of SSH, raadpleegt u eerst een van de volgende artikelen:

* [Problemen met Extern bureaublad-verbindingen met een Windows-gebaseerde Azure Virtual Machine oplossen](troubleshoot-rdp-connection.md)
* [Problemen oplossen met Secure Shell-verbindingen (SSH) met een op Linux gebaseerde Azure-virtuele machine.](troubleshoot-ssh-connection.md)

Als u op enig moment in dit artikel meer hulp nodig hebt, u contact opnemen met de Azure-experts op [de MSDN Azure- en de Stack Overflow-forums.](https://azure.microsoft.com/support/forums/) U ook een Azure-ondersteuningsincident indienen. Ga naar de [Azure-ondersteuningssite](https://azure.microsoft.com/support/options/) en selecteer **Ondersteuning opdoen**.

## <a name="quick-start-troubleshooting-steps"></a>Stappen voor snel oplossen van problemen
Als u problemen ondervindt bij het maken van verbinding met een toepassing, probeert u de volgende algemene stappen voor het oplossen van problemen. Probeer na elke stap opnieuw verbinding te maken met uw toepassing:

* Start de virtuele machine opnieuw op
* De regels voor eindpunt / firewallregels / netwerkbeveiligingsgroepen (NSG) opnieuw maken
  * [Resourcebeheermodel - Netwerkbeveiligingsgroepen beheren](../../virtual-network/manage-network-security-group.md)
  * [Klassiek model - Eindpunten voor Cloud Services beheren](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Verbinding maken vanaf een andere locatie, zoals een ander Virtueel Azure-netwerk
* Implementeer de virtuele machine opnieuw
  * [Windows VM opnieuw implementeren](redeploy-to-new-node-windows.md)
  * [Linux VM opnieuw implementeren](redeploy-to-new-node-linux.md)
* De virtuele machine opnieuw maken

Zie [Endpoint Connectivity (RDP/SSH/HTTP, etc. storingen) voor](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)meer informatie.

## <a name="detailed-troubleshooting-overview"></a>Gedetailleerd overzicht van probleemoplossing
Er zijn vier hoofdgebieden om problemen op te lossen met de toegang tot een toepassing die wordt uitgevoerd op een virtuele Azure-machine.

![probleemoplossing kan toepassing niet starten](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. De toepassing die wordt uitgevoerd op de virtuele Azure-machine.
   * Is de toepassing zelf correct uitgevoerd?
2. De virtuele Azure-machine.
   * Werkt de VM zelf correct en reageert het op verzoeken?
3. Eindpunten van Azure-netwerken.
   * Eindpunten voor cloudservices voor virtuele machines in het klassieke implementatiemodel.
   * Netwerkbeveiligingsgroepen en binnenkomende NAT-regels voor virtuele machines in het implementatiemodel resourcebeheer.
   * Kan het verkeer van gebruikers naar de VM/toepassing op de verwachte poorten stromen?
4. Uw internetrandapparaat.
   * Zijn firewallregels die voorkomen dat het verkeer correct stroomt?

Voor clientcomputers die toegang hebben tot de toepassing via een site-to-site VPN- of ExpressRoute-verbinding, zijn de belangrijkste gebieden die problemen kunnen veroorzaken de toepassing en de virtuele Azure-machine.

Volg de volgende stappen om de bron van het probleem en de correctie ervan te bepalen.

## <a name="step-1-access-application-from-target-vm"></a>Stap 1: Toegang tot toepassing van doel-VM
Probeer toegang te krijgen tot de toepassing met het juiste clientprogramma van de VM waarop deze wordt uitgevoerd. Gebruik de lokale hostnaam, het lokale IP-adres of het loopback-adres (127.0.0.1).

![toepassing rechtstreeks vanaf de VM starten](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Als de toepassing bijvoorbeeld een webserver is, opent u een browser op de virtuele machine en probeert u toegang te krijgen tot een webpagina die op de VM wordt gehost.

Als u toegang hebt tot de toepassing, gaat u naar [Stap 2](#step2).

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* De toepassing wordt uitgevoerd op de doel virtuele machine.
* De applicatie luistert op de verwachte TCP- en UDP-poorten.

Gebruik op zowel Windows- als Linux-gebaseerde virtuele machines de **netstat-een** opdracht om de actieve luisterpoorten weer te geven. Controleer de uitvoer voor de verwachte poorten waarop uw toepassing moet worden beluisterd. Start de toepassing opnieuw of configureer deze om de verwachte poorten te gebruiken als dat nodig is en probeer de toepassing opnieuw lokaal te openen.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Stap 2: Toegang tot toepassing van een andere virtuele machine in hetzelfde virtuele netwerk
Try to access the application from a different VM but in the same virtual network, using the VM's host name or its Azure-assigned public, private, or provider IP address. Gebruik het openbare IP-adres van de cloudservice niet voor virtuele machines die zijn gemaakt met behulp van het klassieke implementatiemodel.

![toepassing starten vanaf een andere VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Als de toepassing bijvoorbeeld een webserver is, probeert u een webpagina te openen vanuit een browser op een andere virtuele virtuele hardware in hetzelfde virtuele netwerk.

Als u toegang hebt tot de toepassing, gaat u naar [Stap 3](#step3).

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* De hostfirewall op de doel-VM staat het binnenkomende verzoek en uitgaand reactieverkeer toe.
* Inbraakdetectie of netwerkbewakingssoftware die op de doel-VM wordt uitgevoerd, staat het verkeer toe.
* Eindpunten voor Cloud Services of Netwerkbeveiligingsgroepen staan het verkeer toe:
  * [Klassiek model - Eindpunten voor Cloud Services beheren](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resourcebeheermodel - Netwerkbeveiligingsgroepen beheren](../../virtual-network/manage-network-security-group.md)
* Een afzonderlijke component die in uw VM wordt uitgevoerd in het pad tussen de test-VM en uw VM, zoals een load balancer of firewall, staat het verkeer toe.

Gebruik windows firewall met geavanceerde beveiliging op een virtuele windows-machine om te bepalen of de firewallregels het inkomende en uitgaande verkeer van uw toepassing uitsluiten.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Stap 3: Toegang tot toepassing van buiten het virtuele netwerk
Probeer toegang te krijgen tot de toepassing vanaf een computer buiten het virtuele netwerk als de VM waarop de toepassing wordt uitgevoerd. Gebruik een ander netwerk als uw oorspronkelijke clientcomputer.

![toepassing starten vanaf een computer buiten het virtuele netwerk](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Als de toepassing bijvoorbeeld een webserver is, probeert u de webpagina te openen vanuit een browser die wordt uitgevoerd op een computer die zich niet in het virtuele netwerk bevindt.

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* Voor VM's die zijn gemaakt met behulp van het klassieke implementatiemodel:
  
  * Controleer of de eindpuntconfiguratie voor de VM het binnenkomende verkeer toestaat, met name het protocol (TCP of UDP) en de openbare en private poortnummers.
  * Controleer of toegangscontrolelijsten (ACL's) op het eindpunt binnenkomend verkeer van internet niet verhinderen.
  * Zie [Eindpunten instellen op een virtuele machine voor](../windows/classic/setup-endpoints.md)meer informatie.
* Voor VM's die zijn gemaakt met het implementatiemodel Resource Manager:
  
  * Controleer of de inkomende NAT-regelconfiguratie voor de VM het binnenkomende verkeer toestaat, met name het protocol (TCP of UDP) en de openbare en private poortnummers.
  * Controleer of netwerkbeveiligingsgroepen het binnenkomende verzoek en uitgaand reactieverkeer toestaan.
  * Zie [Wat is een netwerkbeveiligingsgroep voor](../../virtual-network/security-overview.md) meer informatie?

Als de virtuele machine of het eindpunt lid is van een load-balanced set:

* Controleer of het sondeprotocol (TCP of UDP) en het poortnummer correct zijn.
* Als het sondeprotocol en de poort anders zijn dan het protocol en de poort met een load-balanced set:
  * Controleer of de toepassing luistert op het sondeprotocol (TCP of UDP) en het poortnummer (gebruik **netstat –a** op de doel-VM).
  * Controleer of de hostfirewall op de doel-VM het inkomende sondeverzoek en uitgaand sonderesponsverkeer toestaat.

Als u toegang hebt tot de toepassing, moet u ervoor zorgen dat uw internetrandapparaat het toestaan van:

* De uitgaande toepassing vraagt verkeer van uw clientcomputer naar de virtuele Azure-machine.
* Het binnenkomende reactieverkeer van de virtuele Azure-machine.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Stap 4 Als u geen toegang hebt tot de toepassing, gebruikt u IP-verifiëren om de instellingen te controleren. 

Zie overzicht van [Azure-netwerkbewaking](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)voor meer informatie. 

## <a name="additional-resources"></a>Aanvullende bronnen
[Problemen met Extern bureaublad-verbindingen met een Windows-gebaseerde Azure Virtual Machine oplossen](troubleshoot-rdp-connection.md)

[Problemen met Secure Shell-verbindingen (SSH) oplossen met een op Linux gebaseerde Azure-virtuele machine](troubleshoot-ssh-connection.md)


