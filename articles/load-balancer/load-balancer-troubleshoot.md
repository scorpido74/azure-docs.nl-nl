---
title: Problemen met Azure Load Balancer oplossen
titlesuffix: Azure Load Balancer
description: Bekende problemen met Azure Load Balancer oplossen
services: load-balancer
documentationcenter: na
author: chadmath
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2018
ms.author: genli
ms.openlocfilehash: 4e0e3cf6067467947bcb799a915a93d1bb342ea1
ms.sourcegitcommit: 116bc6a75e501b7bba85e750b336f2af4ad29f5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71154927"
---
# <a name="troubleshoot-azure-load-balancer"></a>Problemen met Azure Load Balancer oplossen

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Deze pagina bevat informatie over het oplossen van problemen met veelgestelde vragen over Azure Load Balancer. Wanneer de Load Balancer verbinding niet beschikbaar is, zijn de meest voorkomende symptomen als volgt: 
- Vm's achter de Load Balancer reageren niet op status controles 
- Vm's achter de Load Balancer reageren niet op het verkeer op de geconfigureerde poort

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Duid Vm's achter de Load Balancer reageren niet op status controles
Voor de back-endservers die deel uitmaken van de load balancerset, moeten ze de controle van de test door geven. Zie [informatie over Load Balancer probe](load-balancer-custom-probe-overview.md)(Engelstalig) voor meer informatie over status controles. 

De Load Balancer Vm's van de back-end-groep reageren mogelijk niet op de tests om een van de volgende redenen: 
- Load Balancer VM van back-end is beschadigd 
- Load Balancer VM van de back-end-pool luistert niet op de test poort 
- Firewall of een netwerk beveiligings groep blokkeert de poort op de Load Balancer Vm's van de back-end-groep 
- Andere onjuiste configuratie in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Oorzaak 1: Load Balancer VM van back-end is beschadigd 

**Validatie en oplossing**

U kunt dit probleem oplossen door u aan te melden bij de deelnemende Vm's en te controleren of de status van de virtuele machine in orde is en om te reageren op **PsPing** of **TCPing** van een andere virtuele machine in de groep. Als de virtuele machine niet in orde is of niet kan reageren op de test, moet u het probleem oplossen en de VM weer herstellen naar een goede status voordat deze kan deel nemen aan de taak verdeling.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Oorzaak 2: Load Balancer VM van de back-end-pool luistert niet op de test poort
Als de virtuele machine in orde is, maar niet op de test reageert, kan het zijn dat de test poort niet op de deelnemende virtuele machine is geopend of dat de virtuele machine niet op die poort luistert.

**Validatie en oplossing**

1. Meld u aan bij de back-end-VM. 
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de test poort:   
            netstat: een
3. Als de poort status niet wordt weer gegeven als **luistert**, configureert u de juiste poort. 
4. U kunt ook een andere poort selecteren, die wordt weer gegeven als **Luis teren**en Load Balancer configuratie dienovereenkomstig bijwerken.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Oorzaak 3: Firewall of een netwerk beveiligings groep blokkeert de poort op de load balancer Vm's van de back-end-groep  
Als de firewall op de virtuele machine de test poort blokkeert, of een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de VM, is de test niet in staat om de poort te bereiken, kan de virtuele machine niet reageren op de status test.          

**Validatie en oplossing**

* Als de firewall is ingeschakeld, controleert u of deze is geconfigureerd om de test poort toe te staan. Als dat niet het geval is, configureert u de firewall om verkeer toe te staan op de test poort en test u het opnieuw. 
* Controleer in de lijst met netwerk beveiligings groepen of het binnenkomende of uitgaande verkeer op de test poort interferentie heeft. 
* Controleer ook of een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit heeft dan de standaard regel waarmee lb-tests & verkeer worden toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van 168.63.129.16). 
* Als een van deze regels het test verkeer blokkeert, verwijdert u de regels en configureert u deze opnieuw om het test verkeer toe te staan.  
* Test of de VM nu heeft gereageerd op de status controles. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Oorzaak 4: Andere onjuiste configuratie in Load Balancer
Als alle voor gaande oorzaken correct worden gevalideerd en opgelost en de back-end-VM nog steeds niet reageert op de status test, kunt u hand matig testen op connectiviteit en een aantal traceringen verzamelen om inzicht te krijgen in de connectiviteit.

**Validatie en oplossing**

* Gebruik **Psping** van een van de andere virtuele machines in het VNet om het antwoord van de test poort te testen (bijvoorbeeld: .\psping.exe-t 10.0.0.4:3389) en de resultaten op te nemen. 
* Gebruik **TCPing** van een van de andere virtuele machines in het VNet om het antwoord van de test poort te testen (bijvoorbeeld: .\tcping.exe 10.0.0.4 3389) en de resultaten op te nemen. 
* Als er geen reactie wordt ontvangen in deze ping-tests,
    - Voer een gelijktijdige Netsh-tracering uit op de doel-back-end-VM-groep en een andere test-VM van hetzelfde VNet. Voer nu een PsPing-test uit, verzamel een aantal netwerk traceringen en stop de test vervolgens. 
    - Analyseer het netwerk vastleggen en controleer of er binnenkomende en uitgaande pakketten zijn gerelateerd aan de ping-query. 
        - Als er geen binnenkomende pakketten worden waargenomen op de back-end-VM-groep, is er mogelijk een netwerk beveiligings groep of een UDR mis-configuratie die het verkeer blokkeert. 
        - Als er geen uitgaande pakketten worden waargenomen op de back-end-VM-groep, moet de virtuele machine worden gecontroleerd op ongerelateerde problemen (de toepassing blokkeert bijvoorbeeld de test poort). 
    - Controleer of de test pakketten worden afgedwongen voor een andere bestemming (mogelijk via UDR-instellingen) voordat de load balancer wordt bereikt. Dit kan ervoor zorgen dat het verkeer nooit de back-end-VM bereikt. 
* Wijzig het test type (bijvoorbeeld HTTP in TCP) en configureer de bijbehorende poort in de Acl's voor netwerk beveiligings groepen en de firewall om te controleren of het probleem wordt veroorzaakt door de configuratie van de test reactie. Zie [configuratie endpoint Load Balancing Health probe](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)(Engelstalig) voor meer informatie over Health probe configuratie.

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Duid Vm's achter Load Balancer reageren niet op verkeer op de geconfigureerde gegevens poort

Als een virtuele machine in de back-end wordt weer gegeven als in orde en reageert op de status tests, maar nog steeds niet deelneemt aan de taak verdeling, of niet reageert op het gegevens verkeer, kan dit een van de volgende oorzaken hebben: 
* Load Balancer VM van de back-end-pool luistert niet op de gegevens poort 
* De netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  
* Toegang tot de Load Balancer vanaf dezelfde VM en NIC 
* Toegang tot de internet-Load Balancer frontend van de deelnemende Load Balancer back-end-VM-groep 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Oorzaak 1: Load Balancer VM van de back-end-pool luistert niet op de gegevens poort 
Als een virtuele machine niet reageert op het gegevens verkeer, komt dit mogelijk doordat de doel poort niet is geopend op de deelnemende virtuele machine, of de virtuele machine luistert niet op die poort. 

**Validatie en oplossing**

1. Meld u aan bij de back-end-VM. 
2. Open een opdracht prompt en voer de volgende opdracht uit om te controleren of er een toepassing luistert op de  gegevens poort: netstat-a 
3. Als de poort niet wordt vermeld met de status Luis TEREn, configureert u de juiste listener-poort 
4. Als de poort is gemarkeerd als luistert, controleert u de doel toepassing op die poort voor mogelijke problemen. 

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Oorzaak 2: De netwerk beveiligings groep blokkeert de poort op de Load Balancer back-end-VM-groep  

Als een of meer netwerk beveiligings groepen die zijn geconfigureerd op het subnet of op de virtuele machine, de bron-IP of poort blokkeert, kan de virtuele machine niet reageren.

* De netwerk beveiligings groepen weer geven die zijn geconfigureerd op de back-end-VM. Zie [netwerk beveiligings groepen beheren](../virtual-network/manage-network-security-group.md)voor meer informatie.
* Controleer in de lijst met netwerk beveiligings groepen of:
    - het binnenkomende of uitgaande verkeer op de gegevens poort heeft storingen. 
    - een regel voor het **weigeren van alle** netwerk beveiligings groepen op de NIC van de virtuele machine of het subnet met een hogere prioriteit dan de standaard regel waarmee Load Balancer tests en verkeer wordt toegestaan (netwerk beveiligings groepen moeten Load Balancer IP-adres van de 168.63.129.16 toestaan, dat wil zeggen test poort) 
* Als een van de regels het verkeer blokkeert, moet u deze regels verwijderen en opnieuw configureren om het gegevens verkeer toe te staan.  
* Test of de VM nu heeft gereageerd op de status controles.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Oorzaak 3: Toegang tot de Load Balancer vanaf dezelfde VM en netwerk interface 

Als uw toepassing die wordt gehost in de back-end-VM van een Load Balancer probeert toegang te krijgen tot een andere toepassing die wordt gehost op dezelfde back-end-VM via dezelfde netwerk interface, is dit een niet-ondersteund scenario. 

**Oplossing** U kunt dit probleem oplossen met behulp van een van de volgende methoden:
* Configureer afzonderlijke Vm's van de back-end-groep per toepassing. 
* Configureer de toepassing in dual NIC-Vm's zodat elke toepassing een eigen netwerk interface en IP-adres gebruikt. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Oorzaak 4: Toegang tot de interne Load Balancer frontend van de deelnemende Load Balancer back-end-VM-groep

Als er een intern Load Balancer is geconfigureerd in een VNet en een van de back-end-Vm's van de deel nemer probeert toegang te krijgen tot de interne Load Balancer frontend, kunnen er fouten optreden wanneer de stroom wordt toegewezen aan de oorspronkelijke virtuele machine. Dit scenario wordt niet ondersteund. Lees de [beperkingen](load-balancer-overview.md#limitations) voor een gedetailleerde discussie.

**Oplossing** Er zijn verschillende manieren om dit scenario op te heffen, met inbegrip van het gebruik van een proxy. Evalueer Application Gateway of andere proxy's van derden (bijvoorbeeld nginx of haproxy). Zie [overzicht van Application Gateway](../application-gateway/application-gateway-introduction.md) voor meer informatie over Application Gateway.

## <a name="additional-network-captures"></a>Aanvullende netwerk opnamen
Als u besluit een ondersteunings aanvraag te openen, verzamelt u de volgende informatie voor een snellere oplossing. Kies één back-end-VM om de volgende tests uit te voeren:
- Gebruik Psping van een van de back-end-Vm's binnen het VNet om de test poort reactie te testen (bijvoorbeeld: Psping 10.0.0.4:3389) en de resultaten op te nemen. 
- Als er geen antwoord wordt ontvangen in deze ping-tests, voert u een gelijktijdige Netsh-tracering uit op de back-end-VM en de VNet-test-VM terwijl u PsPing uitvoert en stopt u de Netsh-tracering. 
  
## <a name="next-steps"></a>Volgende stappen

Als de voor gaande stappen het probleem niet oplossen, opent u een [ondersteunings ticket](https://azure.microsoft.com/support/options/).

