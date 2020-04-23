---
title: Problemen met toegang tot VM-toepassingen in azure oplossen | Microsoft Docs
description: Gebruik deze gedetailleerde stappen voor probleem oplossing om problemen te isoleren bij het maken van verbinding met toepassingen die worden uitgevoerd op virtuele machines in Azure.
services: virtual-machines
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: kan de toepassing niet starten, het programma wordt niet geopend, de poort is geblokkeerd, het programma kan niet worden gestart, de luister poort is geblokkeerd
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

Er zijn verschillende redenen wanneer u niet kunt starten of verbinding kunt maken met een toepassing die wordt uitgevoerd op een virtuele Azure-machine (VM). De reden hiervoor is dat de toepassing niet actief is of niet op de verwachte poorten luistert, de luister poort blokkeert of netwerk regels het verkeer naar de toepassing niet goed door geven. In dit artikel wordt een methode beschreven om het probleem op te sporen en te verhelpen.

Als u problemen ondervindt met het maken van verbinding met uw virtuele machine met behulp van RDP of SSH, raadpleegt u eerst een van de volgende artikelen:

* [Problemen met Extern bureaublad verbindingen met een virtuele Azure-machine op Windows oplossen](troubleshoot-rdp-connection.md)
* [Problemen met ssh-verbindingen (Secure Shell) met een op Linux gebaseerde Azure Virtual Machine oplossen](troubleshoot-ssh-connection.md).

Als u op elk moment in dit artikel meer hulp nodig hebt, kunt u contact opnemen met de Azure-experts op [MSDN Azure en de stack overflow-forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

## <a name="quick-start-troubleshooting-steps"></a>Stappen voor snel starten van problemen oplossen
Als u problemen ondervindt bij het maken van verbinding met een toepassing, probeert u de volgende algemene stappen voor probleem oplossing. Probeer na elke stap opnieuw verbinding te maken met uw toepassing:

* Start de virtuele machine opnieuw op
* De regels voor het eind punt/firewall/netwerk beveiligings groep (NSG) opnieuw maken
  * [Resource Manager-model: netwerk beveiligings groepen beheren](../../virtual-network/manage-network-security-group.md)
  * [Klassiek model-Cloud Services-eind punten beheren](../../cloud-services/cloud-services-enable-communication-role-instances.md)
* Verbinding maken vanaf een andere locatie, zoals een ander virtueel Azure-netwerk
* Implementeer de virtuele machine opnieuw
  * [Windows-VM opnieuw implementeren](redeploy-to-new-node-windows.md)
  * [Linux-VM opnieuw implementeren](redeploy-to-new-node-linux.md)
* De virtuele machine opnieuw maken

Zie [Troubleshooting endpoint Connectivity (RDP/SSH/http, enzovoort)](https://social.msdn.microsoft.com/Forums/azure/en-US/538a8f18-7c1f-4d6e-b81c-70c00e25c93d/troubleshooting-endpoint-connectivity-rdpsshhttp-etc-failures?forum=WAVirtualMachinesforWindows)voor meer informatie.

## <a name="detailed-troubleshooting-overview"></a>Overzicht van gedetailleerde probleem oplossing
Er zijn vier hoofd gebieden voor het oplossen van de toegang tot een toepassing die wordt uitgevoerd op een virtuele machine van Azure.

![problemen met de toepassing kan niet worden gestart](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access1.png)

1. De toepassing die wordt uitgevoerd op de virtuele machine van Azure.
   * Wordt de toepassing zelf correct uitgevoerd?
2. De virtuele machine van Azure.
   * Wordt de VM zelf correct uitgevoerd en reageert deze op aanvragen?
3. Azure-netwerk eindpunten.
   * Cloud service-eind punten voor virtuele machines in het klassieke implementatie model.
   * Netwerk beveiligings groepen en binnenkomende NAT-regels voor virtuele machines in het Resource Manager-implementatie model.
   * Kan verkeer van gebruikers naar de virtuele machine of toepassing op de verwachte poorten worden gestroomd?
4. Uw Internet edge-apparaat.
   * Zijn er firewall regels aanwezig waardoor verkeer niet goed kan worden gestroomd?

Voor client computers die toegang tot de toepassing hebben via een site-naar-site VPN-of ExpressRoute-verbinding, zijn de belangrijkste gebieden die problemen kunnen veroorzaken, de toepassing en de virtuele Azure-machine.

Volg deze stappen om de oorzaak van het probleem en de correctie te bepalen.

## <a name="step-1-access-application-from-target-vm"></a>Stap 1: toegang tot de toepassing vanaf de doel-VM
Probeer toegang te krijgen tot de toepassing met het juiste client programma van de virtuele machine waarop het wordt uitgevoerd. Gebruik de naam van de lokale host, het lokale IP-adres of het loop back-adres (127.0.0.1).

![toepassing rechtstreeks vanuit de VM starten](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access2.png)

Als de toepassing bijvoorbeeld een webserver is, opent u een browser op de virtuele machine en probeert u een webpagina te openen die wordt gehost op de virtuele machine.

Als u toegang tot de toepassing kunt krijgen, gaat u naar [stap 2](#step2).

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* De toepassing wordt uitgevoerd op de virtuele doel machine.
* De toepassing luistert op de verwachte TCP-en UDP-poorten.

Gebruik op zowel Windows-als op Linux gebaseerde virtuele machines de **netstat-a-** opdracht om de actieve luisterende poorten weer te geven. Controleer de uitvoer voor de verwachte poorten waarop uw toepassing luistert. Start de toepassing opnieuw of configureer deze zo dat deze de verwachte poorten gebruikt en probeer de toepassing opnieuw lokaal te openen.

## <a name="step-2-access-application-from-another-vm-in-the-same-virtual-network"></a><a id="step2"></a>Stap 2: toegang tot de toepassing vanaf een andere VM in hetzelfde virtuele netwerk
Probeer toegang te krijgen tot de toepassing vanaf een andere VM, maar in hetzelfde virtuele netwerk, met behulp van de hostnaam van de virtuele machine of het door Azure toegewezen open bare, privé-of provider-IP-adres. Gebruik het open bare IP-adres van de Cloud service niet voor virtuele machines die zijn gemaakt met het klassieke implementatie model.

![toepassing starten vanuit een andere VM](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access3.png)

Als de toepassing bijvoorbeeld een webserver is, probeert u een webpagina te openen vanuit een browser op een andere VM in hetzelfde virtuele netwerk.

Als u toegang tot de toepassing kunt krijgen, gaat u naar [stap 3](#step3).

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* De host-firewall op de doel-VM staat de inkomende aanvraag en het uitgaande antwoord verkeer toe.
* Het verkeer wordt toegestaan door inbraak detectie of de netwerk bewakings software die wordt uitgevoerd op de doel-VM.
* Cloud Services-eind punten of netwerk beveiligings groepen staan het verkeer toe:
  * [Klassiek model-Cloud Services-eind punten beheren](../../cloud-services/cloud-services-enable-communication-role-instances.md)
  * [Resource Manager-model: netwerk beveiligings groepen beheren](../../virtual-network/manage-network-security-group.md)
* Een afzonderlijk onderdeel dat op uw virtuele machine wordt uitgevoerd in het pad tussen de virtuele test machine en uw virtuele machine, zoals een load balancer of firewall, staat het verkeer toe.

Gebruik Windows Firewall met geavanceerde beveiliging op een virtuele Windows-machine om te bepalen of de firewall regels het binnenkomende en uitgaande verkeer van uw toepassing uitsluiten.

## <a name="step-3-access-application-from-outside-the-virtual-network"></a><a id="step3"></a>Stap 3: toegang tot toepassingen buiten het virtuele netwerk
Probeer toegang te krijgen tot de toepassing vanaf een computer buiten het virtuele netwerk als de VM waarop de toepassing wordt uitgevoerd. Gebruik een ander netwerk als uw oorspronkelijke client computer.

![toepassing starten vanaf een computer buiten het virtuele netwerk](./media/virtual-machines-common-troubleshoot-app-connection/tshoot_app_access4.png)

Als de toepassing bijvoorbeeld een webserver is, probeert u de webpagina te openen vanuit een browser die wordt uitgevoerd op een computer die zich niet in het virtuele netwerk bevindt.

Als u geen toegang hebt tot de toepassing, controleert u de volgende instellingen:

* Voor virtuele machines die zijn gemaakt met het klassieke implementatie model:
  
  * Controleer of de eindpunt configuratie voor de virtuele machine het binnenkomende verkeer, met name het Protocol (TCP of UDP) en de open bare en particuliere poort nummers toestaat.
  * Controleer of Acl's (toegangs beheer lijsten) op het eind punt geen binnenkomend verkeer van Internet verhinderen.
  * Zie [eind punten instellen voor een virtuele machine](../windows/classic/setup-endpoints.md)voor meer informatie.
* Voor virtuele machines die zijn gemaakt met het Resource Manager-implementatie model:
  
  * Controleer of de configuratie van de binnenkomende NAT-regel voor de virtuele machine het binnenkomende verkeer, met name het Protocol (TCP of UDP) en de open bare en particuliere poort nummers toestaat.
  * Controleer of de netwerk beveiligings groepen de inkomende aanvraag en het uitgaande antwoord verkeer toestaan.
  * Zie [Wat is een netwerk beveiligings groep?](../../virtual-network/security-overview.md) voor meer informatie.

Als de virtuele machine of het eind punt lid is van een set met gelijke taak verdeling:

* Controleer of het test protocol (TCP of UDP) en het poort nummer juist zijn.
* Als het test protocol en de poort afwijkt van het protocol en de poort met gelijke taak verdeling:
  * Controleer of de toepassing luistert op het test protocol (TCP of UDP) en het poort nummer (gebruik **netstat – a** op de doel-VM).
  * Controleer of de host-firewall op de doel-VM de binnenkomende test aanvraag en het uitgaande verkeer van de test respons toestaat.

Als u toegang hebt tot de toepassing, moet u ervoor zorgen dat het Internet edge-apparaat het volgende toestaat:

* Het uitgaande toepassings verkeer van uw client computer naar de virtuele machine van Azure.
* Het binnenkomende verkeer van toepassings antwoorden van de virtuele Azure-machine.

## <a name="step-4-if-you-cannot-access-the-application-use-ip-verify-to-check-the-settings"></a>Stap 4 als u geen toegang hebt tot de toepassing, gebruikt u IP controleren om de instellingen te controleren. 

Zie overzicht van Azure- [netwerk bewaking](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)voor meer informatie. 

## <a name="additional-resources"></a>Extra resources
[Problemen met Extern bureaublad verbindingen met een virtuele Azure-machine op Windows oplossen](troubleshoot-rdp-connection.md)

[Problemen met SSH-verbindingen (Secure Shell) met een op Linux gebaseerde Azure Virtual Machine oplossen](troubleshoot-ssh-connection.md)


