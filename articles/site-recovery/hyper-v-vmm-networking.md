---
title: IP-adressering instellen na failover naar een secundaire site met Azure Site Recovery
description: Beschrijft hoe u IP-adressering instelt voor het maken van verbinding met VM's op een secundaire on-premises site na herstel na noodgevallen en failover met Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: a61f7ff69e648262eb721eb61a98b09dbbee924c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73961430"
---
# <a name="set-up-ip-addressing-to-connect-to-a-secondary-on-premises-site-after-failover"></a>IP-adressering instellen om verbinding te maken met een secundaire on-premises site na failover

Nadat u niet meer hebt dan Hyper-V VM's in VMM-clouds (System Center Virtual Machine Manager) naar een secundaire site, moet u verbinding kunnen maken met de replica VM's. Dit artikel helpt u om dit te doen. 

## <a name="connection-options"></a>Verbindingsopties

Na failover zijn er een paar manieren om IP-adressering voor replica VM's te verwerken: 

- **Behoudt hetzelfde IP-adres na failover:** In dit scenario heeft de gerepliceerde vm hetzelfde IP-adres als de primaire vm. Dit vereenvoudigt netwerkgerelateerde problemen na failover, maar vereist enige infrastructuurwerk.
- **Gebruik een ander IP-adres na failover:** In dit scenario krijgt de VM een nieuw IP-adres na failover. 
 

## <a name="retain-the-ip-address"></a>Het IP-adres behouden

Als u de IP-adressen van de primaire site wilt behouden, u na een fail-over naar de secundaire site:

- Implementeer een uitgerekt subnet tussen de primaire en de secundaire sites.
- Voer een volledige subnetfailover uit van de primaire naar de secundaire site. U moet routes bijwerken om de nieuwe locatie van de IP-adressen aan te geven.


### <a name="deploy-a-stretched-subnet"></a>Een uitgerekt subnet implementeren

In een uitgerekte configuratie is het subnet gelijktijdig beschikbaar op zowel de primaire als secundaire sites. In een uitgerekt subnet leidt het netwerk het verkeer automatisch naar de nieuwe locatie wanneer u een machine en de IP-adresconfiguratie (Layer 3) naar de secundaire site verplaatst. 

- Vanuit het perspectief van Layer 2 (datalinklaag) hebt u netwerkapparatuur nodig die een uitgerekte VLAN kan beheren.
- Door het uitrekken van de VLAN, de potentiële fout domein strekt zich uit tot beide sites. Dit wordt een enkel punt van mislukking. Hoewel onwaarschijnlijk, in een dergelijk scenario u misschien niet in staat zijn om een incident te isoleren, zoals een uitzending storm. 


### <a name="fail-over-a-subnet"></a>Mislukt boven een subnet

U falen over het gehele subnet om de voordelen van de uitgerekte subnet te verkrijgen, zonder daadwerkelijk rekken. In deze oplossing is een subnet beschikbaar in de bron- of doelsite, maar niet in beide tegelijk.

- Als u de IP-adresruimte wilt behouden in het geval van een failover, u programmatisch regelen dat de routerinfrastructuur subnetten van de ene site naar de andere verplaatst.
- Wanneer een failover optreedt, worden subnetten verplaatst met de bijbehorende VM's.
- Het belangrijkste nadeel van deze aanpak is dat in het geval van een storing, moet je het hele subnet te verplaatsen.

#### <a name="example"></a>Voorbeeld

Hier is een voorbeeld van volledige subnet failover. 

- Voor failover heeft de primaire site toepassingen die worden uitgevoerd in subnet 192.168.1.0/24.
- Tijdens failover zijn alle VM's in dit subnet niet overnaar de secundaire site en behouden ze hun IP-adressen. 
- Routes tussen alle locaties moeten worden aangepast om rekening te houden met het feit dat alle VM's in subnet 192.168.1.0/24 nu naar de secundaire site zijn verhuisd.

De volgende afbeeldingen illustreren de subnetten voor en na failover.


**Vóór failover**

![Vóór failover](./media/hyper-v-vmm-networking/network-design2.png)

**Na failover**

![Na failover](./media/hyper-v-vmm-networking/network-design3.png)

Na failover kent Site Recovery een IP-adres toe voor elke netwerkinterface op de VM. Het adres wordt toegewezen vanuit de statische IP-adresgroep in het relevante netwerk, voor elke VM-instantie.

- Als de IP-adresgroep op de secundaire site dezelfde is als die op de bronsite, wijst Site Recovery hetzelfde IP-adres (van de bron-VM) toe aan de replica-VM. Het IP-adres is gereserveerd in VMM, maar het is niet ingesteld als het failover-IP-adres op de Hyper-V-host. Het failover-IP-adres op een Hyper-v-host wordt ingesteld vlak voor de failover.
- Als hetzelfde IP-adres niet beschikbaar is, wijst Site Recovery een ander beschikbaar IP-adres uit de groep toe.
- Als VM's DHCP gebruiken, beheert Site Recovery de IP-adressen niet. U moet controleren of de DHCP-server op de secundaire site adressen van hetzelfde bereik als de bronsite kan toewijzen.

### <a name="validate-the-ip-address"></a>Het IP-adres valideren

Nadat u de beveiliging van een virtuele machine hebt ingeschakeld, u het volgende voorbeeldscript gebruiken om het adres te verifiëren dat aan de vm is toegewezen. Dit IP-adres is ingesteld als het IP-adres van failover en toegewezen aan de VM op het moment van failover:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="use-a-different-ip-address"></a>Een ander IP-adres gebruiken

In dit scenario worden de IP-adressen van VM's die mislukken, gewijzigd. Het nadeel van deze oplossing is het benodigde onderhoud.  DNS- en cachevermeldingen moeten mogelijk worden bijgewerkt. Dit kan leiden tot downtime, die als volgt kan worden beperkt:

- Gebruik lage TTL-waarden voor intranettoepassingen.
- Gebruik het volgende script in een herstelplan voor siteherstel voor een tijdige update van de DNS-server. U hebt het script niet nodig als u dynamische DNS-registratie gebruikt.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Voorbeeld 

In dit voorbeeld hebben we verschillende IP-adressen op primaire en secundaire sites en is er een derde site van waaruit toepassingen die op de primaire of herstelsite worden gehost, kunnen worden geopend.

- Voor failover worden apps gehost subnet 192.168.1.0/24 op de primaire site.
- Na failover worden apps geconfigureerd in subnet 172.16.1.0/24 op de secundaire site.
- Alle drie de sites hebben toegang tot elkaar.
- Na failover worden apps hersteld in het herstelsubnet.
- In dit scenario hoeft u niet te falen over het gehele subnet en zijn er geen wijzigingen nodig om VPN- of netwerkroutes opnieuw te configureren. De failover en sommige DNS-updates zorgen ervoor dat toepassingen toegankelijk blijven.
- Als DNS is geconfigureerd om dynamische updates toe te staan, registreren de VM's zichzelf met behulp van het nieuwe IP-adres, wanneer ze starten na failover.

**Vóór failover**

![Ander IP-adres - vóór failover](./media/hyper-v-vmm-networking/network-design10.png)

**Na failover**

![Ander IP-adres - na failover](./media/hyper-v-vmm-networking/network-design11.png)


## <a name="next-steps"></a>Volgende stappen

[Een failover uitvoeren](hyper-v-vmm-failover-failback.md)

