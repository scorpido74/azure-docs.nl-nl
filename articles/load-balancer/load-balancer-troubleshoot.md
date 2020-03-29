---
title: Problemen met Azure Load Balancer oplossen
description: Meer informatie over het oplossen van bekende problemen met Azure Load Balancer.
services: load-balancer
documentationcenter: na
author: asudbring
manager: dcscontentpm
ms.custom: seodoc18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/28/2020
ms.author: allensu
ms.openlocfilehash: ca9b70bd71a618f8e3d5f4fe9504ba66a9f14c6f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935483"
---
# <a name="troubleshoot-azure-load-balancer"></a>Problemen met Azure Load Balancer oplossen

Op deze pagina vindt u informatie over probleemoplossing voor basis- en standaardalgemene Azure Load Balancer-vragen. Zie Overzicht van Standaard load [balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics)voor meer informatie over Standard Load Balancer.

Wanneer de load balancer-connectiviteit niet beschikbaar is, zijn de meest voorkomende symptomen als volgt: 

- VM's achter de Load Balancer reageren niet op gezondheidssondes 
- VM's achter de Load Balancer reageren niet op het verkeer op de geconfigureerde poort

Wanneer de externe clients naar de backend VM's via de load balancer gaan, wordt het IP-adres van de clients gebruikt voor de communicatie. Zorg ervoor dat het IP-adres van de clients wordt toegevoegd aan de lijst met NSG-toegestane clients. 

## <a name="symptom-vms-behind-the-load-balancer-are-not-responding-to-health-probes"></a>Symptoom: VM's achter de Load Balancer reageren niet op gezondheidssondes
Als de backendservers kunnen deelnemen aan de load balancer-set, moeten ze de sondecontrole doorstaan. Zie [Inzicht in Load Balancer Probes voor](load-balancer-custom-probe-overview.md)meer informatie over statussondes. 

De backendpool VM's van de Load Balancer-backendpool reageren mogelijk niet op de sondes om een van de volgende redenen: 
- Vm van load Balancer backend pool is niet in orde 
- Load Balancer backend pool VM luistert niet op de sondepoort 
- Firewall of een netwerkbeveiligingsgroep blokkeert de poort op de backendpool VM's van de Load Balancer-backendpool 
- Andere verkeerde configuraties in Load Balancer

### <a name="cause-1-load-balancer-backend-pool-vm-is-unhealthy"></a>Oorzaak 1: Load Balancer backend pool VM is ongezond 

**Validatie en oplossing**

Als u dit probleem wilt oplossen, meldt u zich aan bij de deelnemende VM's en controleert u of de VM-status in orde is en u reageren op **PsPing** of **TCPing** vanuit een andere VM in de groep. Als de vm niet in orde is of niet kan reageren op de sonde, moet u het probleem verhelpen en de VM weer in orde maken voordat deze kan deelnemen aan taakverdeling.

### <a name="cause-2-load-balancer-backend-pool-vm-is-not-listening-on-the-probe-port"></a>Oorzaak 2: Load Balancer backend pool VM luistert niet op de sondepoort
Als de VM gezond is, maar niet reageert op de sonde, kan een mogelijke reden zijn dat de sondepoort niet is geopend op de deelnemende VM of dat de VM niet op die poort luistert.

**Validatie en oplossing**

1. Log in op de backend VM. 
2. Open een opdrachtprompt en voer de volgende opdracht uit om te valideren dat er een toepassing luistert op de sondepoort:   
            netstat -an
3. Als de poortstatus niet wordt vermeld als **LUISTEREN,** configureert u de juiste poort. 
4. U ook een andere poort selecteren, die wordt vermeld als **LUISTEREN,** en de configuratie van de load balancer dienovereenkomstig bijwerken.              

### <a name="cause-3-firewall-or-a-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vms"></a>Oorzaak 3: Firewall of een netwerkbeveiligingsgroep blokkeert de poort op de backendpool VM's van de load balancer-backendpool  
Als de firewall op de VM de sondepoort blokkeert of een of meer netwerkbeveiligingsgroepen die zijn geconfigureerd op het subnet of op de VM, de sonde niet in staat stelt de poort te bereiken, kan de VM niet reageren op de statussonde.          

**Validatie en oplossing**

* Als de firewall is ingeschakeld, controleert u of deze is geconfigureerd om de sondepoort toe te staan. Zo niet, configureer dan de firewall om verkeer op de sondepoort toe te staan en test opnieuw. 
* Controleer in de lijst met netwerkbeveiligingsgroepen of het binnenkomende of uitgaande verkeer op de sondepoort storing heeft. 
* Controleer ook of een regel **Voor alle** netwerkbeveiligingsgroepen weigeren op de NIC van de VM of het subnet met een hogere prioriteit dan de standaardregel waarmee LB-sondes & verkeer kunnen worden weergegeven (netwerkbeveiligingsgroepen moeten het IP van Load Balancer van 168.63.129.16 toestaan). 
* Als een van deze regels het sondeverkeer blokkeert, verwijdert en configureert u de regels om het sondeverkeer toe te staan.  
* Test of de VM nu is begonnen met reageren op de statussondes. 

### <a name="cause-4-other-misconfigurations-in-load-balancer"></a>Oorzaak 4: Andere verkeerde configuraties in Load Balancer
Als alle voorgaande oorzaken correct lijken te zijn gevalideerd en opgelost en de backend-VM nog steeds niet reageert op de statussonde, test dan handmatig op connectiviteit en verzamel enkele sporen om de connectiviteit te begrijpen.

**Validatie en oplossing**

* Gebruik **Psping** van een van de andere VM's in het VNet om de reactie van de sondepoort te testen (bijvoorbeeld :.psping.exe -t 10.0.0.4:3389) en resultaten op te nemen. 
* Gebruik **TCPing** van een van de andere VM's in het VNet om de reactie van de sondepoort te testen (bijvoorbeeld .\tcping.exe 10.0.0.4 3389) en resultaten op te nemen. 
* Als er geen reactie wordt ontvangen in deze pingtests,
    - Voer een gelijktijdige Netsh-tracering uit op de vm van de doelbackendpool en een andere test-VM van hetzelfde VNet. Voer nu een tijdje een PsPing-test uit, verzamel wat netwerksporen en stop de test. 
    - Analyseer de netwerkopname en kijk of er zowel inkomende als uitgaande pakketten zijn die verband houden met de ping-query. 
        - Als er geen binnenkomende pakketten worden waargenomen op de backend pool VM, is er mogelijk een netwerk beveiligingsgroepen of UDR verkeerde configuratie blokkeren van het verkeer. 
        - Als er geen uitgaande pakketten worden waargenomen op de vm van de backendpool, moet de VM worden gecontroleerd op niet-gerelateerde problemen (bijvoorbeeld Toepassing die de sondepoort blokkeert). 
    - Controleer of de sondepakketten naar een andere bestemming worden gedwongen (mogelijk via UDR-instellingen) voordat u de load balancer bereikt. Hierdoor kan het verkeer nooit de backend VM bereiken. 
* Wijzig het sondetype (bijvoorbeeld HTTP in TCP) en configureer de bijbehorende poort in acl. en firewall van netwerkbeveiligingsgroepen om te valideren of het probleem zich met de configuratie van de sonderespons bevindt. Zie [Statussenconfiguratie van de taakverdeling van eindpunten voor](https://blogs.msdn.microsoft.com/mast/2016/01/26/endpoint-load-balancing-heath-probe-configuration-details/)meer informatie over de configuratie van de statussondes van de status van de status van de status .

## <a name="symptom-vms-behind-load-balancer-are-not-responding-to-traffic-on-the-configured-data-port"></a>Symptoom: VM's achter Load Balancer reageren niet op verkeer op de geconfigureerde gegevenspoort

Als een backend pool VM als gezond wordt vermeld en reageert op de statussondes, maar nog steeds niet deelneemt aan de Load Balancing of niet reageert op het gegevensverkeer, kan dit te wijten zijn aan een van de volgende redenen: 
* Load Balancer Backend pool VM luistert niet op de datapoort 
* Netwerkbeveiligingsgroep blokkeert de poort op de VM backendpool Load Balancer  
* Toegang tot de load balancer vanuit dezelfde VM en NIC 
* Toegang tot de Frontend van Internet Load Balancer vanuit de deelnemende Load Balancer backend pool VM 

### <a name="cause-1-load-balancer-backend-pool-vm-is-not-listening-on-the-data-port"></a>Oorzaak 1: Load Balancer backend pool VM luistert niet op de datapoort 
Als een VM niet reageert op het gegevensverkeer, kan dit zijn omdat de doelpoort niet is geopend op de deelnemende VM of omdat de VM niet op die poort luistert. 

**Validatie en oplossing**

1. Log in op de backend VM. 
2. Open een opdrachtprompt en voer de volgende opdracht uit om  te valideren dat er een toepassing luistert op de gegevenspoort: netstat -an 
3. Als de poort niet wordt vermeld met Status 'LUISTEREN', configureert u de juiste listenerpoort 
4. Als de poort is gemarkeerd als Luisteren, controleert u de doeltoepassing op die poort op eventuele problemen.

### <a name="cause-2-network-security-group-is-blocking-the-port-on-the-load-balancer-backend-pool-vm"></a>Oorzaak 2: Netwerkbeveiligingsgroep blokkeert de poort op de backendpool VM load Balancer  

Als een of meer netwerkbeveiligingsgroepen die zijn geconfigureerd op het subnet of op de vm, het bron-IP of -poort blokkeren, kan de VM niet reageren.

Voor de public load balancer wordt het IP-adres van de internetclients gebruikt voor communicatie tussen de clients en de backend VM's van de load balancer. Zorg ervoor dat het IP-adres van de clients is toegestaan in de netwerkbeveiligingsgroep van de backend VM.

1. Vermeld de netwerkbeveiligingsgroepen die zijn geconfigureerd op de backend-vm. Zie [Netwerkbeveiligingsgroepen beheren](../virtual-network/manage-network-security-group.md) voor meer informatie
1. Controleer in de lijst met netwerkbeveiligingsgroepen of:
    - het binnenkomende of uitgaande verkeer op de datapoort heeft interferentie. 
    - a **Deny All** network security group rule on the NIC of the VM or the subnet that has a higher priority that the default rule that allows Load Balancer probes and traffic (network security groups must allow Load Balancer IP of 168.63.129.16, that is probe port)
1. Als een van de regels het verkeer blokkeert, verwijdert en configureert u deze regels om het gegevensverkeer toe te staan.  
1. Test of de VM nu is begonnen met het reageren op de statussondes.

### <a name="cause-3-accessing-the-load-balancer-from-the-same-vm-and-network-interface"></a>Oorzaak 3: Toegang tot de load balancer vanuit dezelfde VM- en netwerkinterface 

Als uw toepassing die wordt gehost in de backend-VM van een load balancer probeert toegang te krijgen tot een andere toepassing die wordt gehost in dezelfde backend-vm via dezelfde netwerkinterface, is dit een niet-ondersteund scenario en mislukt het. 

**Resolutie** U dit probleem oplossen via een van de volgende methoden:
* Afzonderlijke backendpoolVM's per toepassing configureren. 
* Configureer de toepassing in dubbele NIC VM's, zodat elke toepassing zijn eigen netwerkinterface en IP-adres gebruikte. 

### <a name="cause-4-accessing-the-internal-load-balancer-frontend-from-the-participating-load-balancer-backend-pool-vm"></a>Oorzaak 4: Toegang tot de interne Load Balancer frontend van de deelnemende Load Balancer backend pool VM

Als een interne load balancer is geconfigureerd in een VNet en een van de backend VM's van de deelnemer probeert toegang te krijgen tot de interne frontend load balancer, kunnen fouten optreden wanneer de stroom wordt toegewezen aan de oorspronkelijke VM. Een dergelijk scenario wordt niet ondersteund. Bekijk [de beperkingen](concepts-limitations.md#limitations) voor een gedetailleerde discussie.

**Resolutie** Er zijn verschillende manieren om dit scenario te deblokkeren, waaronder het gebruik van een proxy. Application Gateway of andere proxy's van derden evalueren (bijvoorbeeld nginx of haproxy). Zie Overzicht van application [gateway](../application-gateway/application-gateway-introduction.md) voor meer informatie over Application Gateway

## <a name="symptom-cannot-change-backend-port-for-existing-lb-rule-of-a-load-balancer-which-has-vm-scale-set-deployed-in-the-backend-pool"></a>Symptoom: Kan backendpoort niet wijzigen voor bestaande LB-regel van een load balancer met VM-schaalset geïmplementeerd in de backendpool. 
### <a name="cause--the-backend-port-cannot-be-modified-for-a-load-balancing-rule-thats-used-by-a-health-probe-for-load-balancer-referenced-by-vm-scale-set"></a>Oorzaak : De backendpoort kan niet worden gewijzigd voor een regel voor het balanceren van de regel die wordt gebruikt door een statussonde voor load balancer waarnaar wordt verwezen door VM-schaalset.
**Resolutie** Als u de poort wilt wijzigen, u de statussonde verwijderen door de VM-schaalset bij te werken, de poort bij te werken en vervolgens de statussonde opnieuw te configureren.

## <a name="additional-network-captures"></a>Extra netwerkopnames
Als u besluit een ondersteuningsaanvraag te openen, verzamelt u de volgende informatie voor een snellere oplossing. Kies één backend-vm om de volgende tests uit te voeren:
- Gebruik Psping van een van de backend VM's in het VNet om de reactie van de sondepoort te testen (bijvoorbeeld psping 10.0.0.4:3389) en resultaten op te nemen. 
- Als er geen reactie wordt ontvangen in deze ping-tests, voert u een gelijktijdige Netsh-tracering uit op de backend VM en de VNet-testVM terwijl u PsPing uitvoert en stopt u vervolgens het Netsh-spoor. 
  
## <a name="next-steps"></a>Volgende stappen

Als het probleem in de voorgaande stappen niet is opgelost, opent u een [ondersteuningsticket](https://azure.microsoft.com/support/options/).

