---
title: SAP HANA uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp Files op SUSE Linux Enterprise Server | Microsoft Docs
description: Hand leiding voor hoge Beschik baarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 76369c1a4beb792de03cf0ccae5c86825812f103
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934166"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>SAP HANA uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp Files op SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


In dit artikel wordt beschreven hoe u Maxi maal beschik bare HANA-systemen kunt implementeren in scale-out configuratie met stand-by op Azure virtual machines met [Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) voor de gedeelde opslag volumes.  
In de voorbeeld configuraties, installatie opdrachten, enzovoort, is de HANA-instantie **03** en de Hana-systeem-id **HN1**. De voor beelden zijn gebaseerd op HANA 2,0 SP4 en SUSE Linux Enterprise Server voor SAP 12 SP4. 

Lees eerst de volgende SAP-opmerkingen en-documenten:

* [Documentatie over Azure NetApp Files][anf-azure-doc] 
* SAP-opmerking [1928533], die:  
  * Lijst met Azure-VM-grootten die worden ondersteund voor de implementatie van SAP-software
  * Informatie over belang rijke capaciteit voor Azure VM-grootten
  * Ondersteunde SAP-software en besturings systemen (OS) en database combinaties
  * Vereiste versie van de SAP-kernel voor Windows en Linux op Microsoft Azure
* SAP-opmerking [2015553] bevat vereisten voor SAP-ondersteuning voor SAP-software-implementaties in Azure.
* SAP Note [2205917] heeft aanbevolen instellingen voor het besturings systeem voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799] heeft SAP-richt lijnen voor SuSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632] bevat gedetailleerde informatie over alle bewakings gegevens die zijn gerapporteerd voor SAP in Azure.
* SAP Note [2191498] heeft de vereiste SAP host agent-versie voor Linux in Azure.
* SAP Note [2243692] bevat informatie over SAP-licentie verlening op Linux in Azure.
* SAP Note [1984787] bevat algemene informatie over SuSE Linux Enterprise Server 12.
* SAP Note [1999351] bevat extra informatie over probleem oplossing voor de uitgebreide bewakings extensie van Azure voor SAP.
* SAP Note [1900823] bevat informatie over SAP Hana opslag vereisten
* Op de [SAP Community wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) zijn alle vereiste SAP-notities voor Linux geïnstalleerd.
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [Best Practice-hand leidingen voor SuSE SAP ha][suse-ha-guide] De gidsen bevatten alle vereiste informatie voor het instellen van netweave HA en SAP HANA systeem replicatie on-premises. Gebruik deze hand leidingen als algemene basis lijn. Ze bieden veel meer gedetailleerde informatie.
* [Release opmerkingen voor de SUSE-extensie voor hoge Beschik baarheid van 12 SP3][suse-ha-12sp3-relnotes]
* [NetApp SAP-toepassingen op Microsoft Azure met behulp van Azure NetApp Files][anf-sap-applications-azure]
* [SAP Hana op NetApp-systemen met NFS](https://www.netapp.com/us/media/tr-4435.pdf). De configuratie handleiding bevat informatie over het instellen van SAP HANA met behulp van NFS dat door Azure NetApp Files wordt verstrekt.


## <a name="overview"></a>Overzicht

Een van de methoden voor het aanleveren van HANA hoge Beschik baarheid is automatische failover van de host. Als u automatische failover wilt configureren, worden een of meer virtuele machines toegevoegd aan het HANA-systeem en geconfigureerd als stand-by-knoop punten. Wanneer het actieve knoop punt mislukt, neemt een stand-by-knoop punt automatisch over. In de gepresenteerde configuratie met virtuele machines van Azure, die door [NFS op Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)worden uitgevoerd.  

Het standby-knoop punt moet toegang hebben tot alle database volumes. De HANA-volumes moeten worden gekoppeld als NFSv4-volumes. Het verbeterde vergrendelings mechanisme op basis van bestands leases in het NFSv4-protocol wordt gebruikt voor `I/O` omheining. 

> [!IMPORTANT]
> Het is verplicht om de HANA-gegevens en logboek volumes als NFSv 4.1-volumes te implementeren en deze te koppelen met behulp van het NFSv 4.1-protocol, zodat de configuratie wordt ondersteund. De configuratie van de automatische failover van HANA-hosts met het stand-by-knoop punt wordt niet ondersteund met NFSv3.

![Overzicht van de hoge Beschik baarheid van SAP netweave](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

Na de SAP HANA aanbevelingen voor het netwerk zijn drie subnetten gemaakt binnen één virtueel Azure-netwerk: voor communicatie met het opslag systeem voor interne HANA-communicatie tussen knoop punten en voor client communicatie. De Azure NetApp-volumes bevinden zich in een afzonderlijk subnet, [gedelegeerd aan Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

Voor deze voorbeeld configuratie zijn de subnetten:  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>De Azure NetApp Files-infra structuur instellen 

Voordat u doorgaat met de installatie van de Azure NetApp-infra structuur, moet u vertrouwd raken met de [Azure NetApp files-documentatie][anf-azure-doc]. Azure NetApp-bestanden zijn beschikbaar in verschillende [Azure-regio's](https://azure.microsoft.com/global-infrastructure/services/?products=netapp). Controleer of de geselecteerde Azure-regio Azure NetApp Files bevat.  

De volgende koppeling toont de beschik baarheid van Azure NetApp Files per Azure-regio: [Azure NetApp files Beschik baarheid per Azure-regio][anf-avail-matrix].  
Voordat u Azure NetApp Files implementeert, moet u onboarding aanvragen bij Azure NetApp Files, volgens de [instructies registreren voor Azure NetApp files][anf-register]. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files-resources implementeren  

Bij de volgende stappen wordt ervan uitgegaan dat u [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)al hebt geïmplementeerd. De Azure NetApp Files resources en de virtuele machines, waarbij de Azure NetApp Files resources worden gekoppeld, moeten worden geïmplementeerd in dezelfde Azure-Virtual Network of in gepeerde Azure Virtual Networks.  

1. Als u dat nog niet hebt gedaan, vraagt u [om onboarding naar Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Maak het NetApp-account in de geselecteerde Azure-regio en volg de [instructies voor het maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)van een NetApp-account.  

3. Stel Azure NetApp Files capaciteits groep in en volg de [instructies voor het instellen van Azure NetApp files capaciteits groep](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool).  
De HANA-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp Files capaciteits groep, Ultra service niveau. U kunt het beste een [service niveau](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) van extreem of Premium Azure NETAPP files voor Hana-workloads op Azure.  

4. Delegeer een subnet naar Azure NetApp-bestanden zoals beschreven in de [instructies een subnet overdragen aan Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Implementeer Azure NetApp Files volumes, gevolgd door de instructies voor het [maken van een volume voor Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  Zorg ervoor dat u **nfsv 4.1** -versie selecteert bij het implementeren van de volumes. Voor momenteel toegang tot NFSv 4.1 is extra White List vereist. Implementeer de volumes in het aangewezen Azure NetApp Files [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets). Houd er rekening mee dat de Azure NetApp Files resources en de virtuele Azure-machines zich in dezelfde Azure-Virtual Network moeten bevinden of in een Peerd Azure Virtual Network. Bijvoorbeeld <b>HN1</b>-data-Mnt00001, <b>HN1</b>-log-mnt00001, enzovoort zijn de volume namen en NFS://10.23.1.5/<b>HN1</b>-data-mnt00001, NFS://10.23.1.4/<b>HN1</b>-log-mnt00001, enzovoort. Dit zijn de bestands paden voor de Azure NetApp files volumes.  

   1. volume <b>HN1</b>-data-mnt00001 (NFS://10.23.1.5/<b>HN1</b>-data-mnt00001)
   2. volume <b>HN1</b>-data-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-data-mnt00002)
   3. volume <b>HN1</b>-log-mnt00001 (NFS://10.23.1.4/<b>HN1</b>-log-mnt00001)
   4. volume <b>HN1</b>-log-mnt00002 (NFS://10.23.1.6/<b>HN1</b>-log-mnt00002)
   5. volume <b>HN1</b>-shared (NFS://10.23.1.4/<b>HN1</b>-Shared)
   
   In dit voor beeld hebben we afzonderlijke Azure NetApp Files gebruikt voor elke HANA-gegevens en logboek volumes. Voor een hogere kosten geoptimaliseerde configuratie op kleinere of niet-productieve systemen, is het mogelijk om alle gegevens koppelingen en alle logboeken te koppelen aan één volume.  

### <a name="important-considerations"></a>Belang rijke overwegingen

Houd rekening met de volgende belang rijke overwegingen bij het overwegen van Azure NetApp Files voor de SAP net-Weaver op SUSE-architectuur met hoge Beschik baarheid:

- De minimale capaciteits pool is 4 TiB.  
- De minimale volume grootte is 100 GiB
- Azure NetApp Files en alle virtuele machines, waarbij Azure NetApp Files volumes worden gekoppeld, moeten zich in dezelfde Azure-Virtual Network of in gekoppelde [virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in dezelfde regio bevinden.  
- Het geselecteerde virtuele netwerk moet een subnet hebben, gedelegeerd aan Azure NetApp Files.
- De door Voer van een Azure NetApp-volume is een functie van volume quota en service niveau, zoals beschreven in [service niveau voor Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). Wanneer u de grootte van de HANA Azure NetApp-volumes wijzigt, moet u ervoor zorgen dat de resulterende door voer voldoet aan de HANA-systeem vereisten.  
- Azure NetApp Files biedt [export beleid](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): u kunt de toegestane clients, het toegangs type (lezen & schrijven, alleen-lezen, enz.) beheren. 
- Er is nog geen zone bewust van Azure NetApp Files-functie. Momenteel wordt Azure NetApp Files-functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's.  
- Het is belang rijk om de virtuele machines dicht bij de Azure NetApp-opslag te laten implementeren voor een lage latentie. Voor SAP HANA workloads is een lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes dicht bij elkaar worden geïmplementeerd.  
- De gebruikers-ID voor <b>sid</b>adm en de groeps-ID voor `sapsys` op de virtuele machines moeten overeenkomen met de configuratie in azure NetApp files. 

> [!IMPORTANT]
> Voor SAP HANA workloads is een lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes dicht bij elkaar worden geïmplementeerd.  

> [!IMPORTANT]
> Als de gebruikers-ID voor <b>sid</b>adm niet overeenkomt met de groeps-id voor `sapsys` tussen de virtuele machine en de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes, gekoppeld op virtuele machines, weer gegeven als `nobody`. Zorg ervoor dat u de juiste gebruikers-ID voor <b>sid</b>adm en de groeps-id voor `sapsys` opgeeft, wanneer u [een nieuw systeem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) wilt Azure NetApp files.

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Grootte van HANA-Data Base op Azure NetApp Files

De door Voer van een Azure NetApp-volume is een functie van de volume grootte en het service niveau, zoals beschreven in [service niveau voor Azure NetApp files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels). 

Bij het ontwerpen van de infra structuur voor SAP in azure moet u rekening houden met een aantal minimale opslag vereisten van SAP, die worden omgezet in kenmerken met minimale door Voer:

- Lezen/schrijven inschakelen op/Hana/log van een 250 MB/sec. met 1 MB I/O-grootten  
- Lees activiteit van ten minste 400 MB/sec. voor/Hana/data met 16 MB en 64 MB I/O-grootte inschakelen  
- Schrijf activiteit van ten minste 250 MB per seconde voor/Hana/data met 16 MB en 64 MB I/O-grootte inschakelen  

De [Azure NetApp files doorvoer limieten](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per 1 TIB volume quota zijn:
- Premium Storage-laag-64-MiB/s  
- Ultra Storage-laag-128 MiB/s  

Om te voldoen aan de vereisten voor de minimale door Voer van SAP voor gegevens en Logboeken, en volgens de richt lijnen voor `/hana/shared`, zien de aanbevolen grootten er als volgt uit:

| Volume | Grootte<br /> Laag Premium Storage | Grootte<br /> Ultra Storage-laag | Ondersteund NFS-protocol |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /hana/shared | Max (512 GB, 1xRAM) per 4 worker-knoop punten | Max (512 GB, 1xRAM) per 4 worker-knoop punten | v3 of v 4.1 |

De configuratie van de SAP HANA voor de indeling die in dit artikel wordt weer gegeven, met behulp van Azure NetApp Files Ultra Storage-laag, ziet er als volgt uit:

| Volume | Grootte<br /> Ultra Storage-laag | Ondersteund NFS-protocol |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB | v 4.1 |
| /hana/log/mnt00002 | 2 TiB | v 4.1 |
| /hana/data/mnt00001 | 3,2 TiB | v 4.1 |
| /hana/data/mnt00002 | 3,2 TiB | v 4.1 |
| /hana/shared | 2 TiB | v3 of v 4.1 |

> [!NOTE]
> De aanbevelingen voor het Azure NetApp Files formaat dat hier wordt beschreven, zijn bedoeld om te voldoen aan de minimale vereisten SAP houdt bij hun infrastructuur providers. In echte klant implementaties en werkbelasting scenario's, die mogelijk niet voldoende zijn. U kunt deze aanbevelingen als uitgangs punt gebruiken en aanpassen op basis van de vereisten van uw specifieke werk belasting.  

> [!TIP]
> U kunt de grootte van Azure NetApp Files volumes dynamisch opnieuw instellen, zonder dat u de volumes hoeft te `unmount`, de virtuele machines te stoppen of SAP HANA te stoppen. Zo kan de toepassing voldoen aan de verwachte en onvoorziene doorvoer vereisten.

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>Virtuele Linux-machines implementeren via Azure Portal

Eerst moet u de Azure NetApp Files volumes maken. Maak de [subnetten van het virtuele Azure-netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) in de [Azure-Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview). Implementeer de virtuele machines. Maak de extra netwerk interfaces en koppel de netwerk interfaces aan de bijbehorende Vm's. Elke virtuele machine heeft drie netwerk interfaces, die overeenkomen met de drie subnetten van het virtuele netwerk van Azure (`storage`, `hana` en `client`).  Zie [een virtuele Linux-machine maken in azure met meerdere netwerk interface kaarten](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics).  

> [!IMPORTANT]
> Voor SAP HANA workloads is een lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes dicht bij elkaar worden geïmplementeerd om een lage latentie te krijgen. Verzend de benodigde gegevens wanneer u een [nieuw SAP Hana systeem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)hebt dat gebruikmaakt van SAP Hana Azure NetApp files.  
> 
In de volgende stappen wordt ervan uitgegaan dat u de resource groep, het virtuele Azure-netwerk en de drie Azure Virtual Network subnetten hebt gemaakt: `storage`, `hana` en `client`.  Wanneer u de virtuele machines implementeert, selecteert u het opslag subnet, zodat de netwerk interface van de opslag de primaire interface op de virtuele machines is. Als dat niet mogelijk is, moet u een expliciete route naar het met Azure NetApp gedelegeerde subnet via de gateway van het opslag subnet configureren. 

> [!IMPORTANT]
> Zorg ervoor dat het besturings systeem dat u selecteert, SAP gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. De lijst met SAP HANA gecertificeerde VM-typen en versies van het besturings systeem voor die apparaten kunnen worden opgezocht in [SAP Hana gecertificeerde IaaS-platformen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Zorg ervoor dat u in de details van het opgegeven VM-type klikt om de volledige lijst met SAP HANA ondersteunde versies van besturings systemen voor het specifieke VM-type op te halen.  

1. Maak een Beschikbaarheidsset voor SAP HANA. Zorg ervoor dat u het maximale update domein instelt.  

2. Drie Virtual Machines maken (**hanadb1**, **hanadb2**, **hanadb3**)  
   - Gebruik een SLES4SAP-installatie kopie in de Azure-galerie die wordt ondersteund voor SAP HANA. In dit voor beeld hebben we SLES4SAP 12 SP4-installatie kopie gebruikt.  
   - Selecteer een Beschikbaarheidsset die eerder is gemaakt voor SAP HANA.  
   - Selecteer het subnet van het virtuele netwerk van Azure Storage. Selecteer [versneld netwerk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  
Wanneer u de virtuele machines implementeert, wordt de naam van de netwerk interface automatisch gegenereerd. We verwijzen naar de netwerk interfaces die zijn gekoppeld aan de opslag-Azure-Virtual Network subnet als **hanadb1-Storage**, **hanadb2-Storage**en **hanadb3-Storage**. 
3. Maak drie netwerk interfaces: één voor elke virtuele machine, voor het subnet van het `hana` virtuele netwerk. In dit voor beeld: **hanadb1-Hana**, **hanadb2-Hana**en **hanadb3-Hana**.  
4. Maak drie netwerk interfaces, één voor elke virtuele machine, voor het subnet van het virtuele netwerk van de **client** . In dit voor beeld: **hanadb1-client**, **hanadb2-client**en **hanadb3-client**.  
5. De zojuist gemaakte virtuele netwerk interfaces koppelen aan de bijbehorende virtuele machines  

    1. Ga naar de virtuele machine in [Azure Portal](https://portal.azure.com/#home).  
    2. Selecteer Virtual Machines in het navigatie deel venster aan de linkerkant. Filter op de naam van de virtuele machine, bijvoorbeeld **hanadb1**. Klik op de virtuele machine.  
    3. Selecteer bij overzicht stop om de toewijzing van de virtuele machine ongedaan te maken.  
    4. Selecteer netwerken, voeg netwerk interface toe. Selecteer in de vervolg keuzelijst onder ' netwerk interface koppelen ' de al gemaakte netwerk interfaces voor **`hana`** en **client** subnetten.  Sla op. 
    5. Herhaal dit voor de resterende virtuele machines. In ons voor beeld: **hanadb2** en **hanadb3**.
    6. Zorg ervoor dat de virtuele machines met de status gestopt nu actief zijn. Vervolgens wordt [versneld netwerk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) ingeschakeld voor alle nieuw gekoppelde netwerk interfaces.  

6. Schakel versneld netwerken in voor de extra netwerk interfaces voor **`hana`** en **`client`** subnetten.  

    1. [Cloud shell](https://azure.microsoft.com/features/cloud-shell/) openen in [Azure Portal](https://portal.azure.com/#home)  
    2. Voer de volgende opdrachten uit om versneld netwerken in te scha kelen voor de extra netwerk interfaces die zijn gekoppeld aan **`hana`** en **`client`** subnetten.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. De virtuele machines starten  

    1. Selecteer Virtual Machines in het navigatie deel venster aan de linkerkant. Filter op de naam van de virtuele machine, bijvoorbeeld **hanadb1**. Klik op de virtuele machine.  
    2. Selecteer in het overzicht start.  

## <a name="operating-system-configuration-and-preparation"></a>Configuratie en voor bereiding van het besturings systeem

De volgende items worden voorafgegaan door **[A]** , van toepassing op alle knoop punten, **[1]** -alleen van toepassing op knoop punt 1, **[2]** -alleen van toepassing op knoop punt 2 of **[3]** -alleen van toepassing op knoop punt 3.

1. **[A]** de hosts-bestanden op de virtuele machines onderhouden. Neem vermeldingen op voor alle subnetten. De volgende vermeldingen zijn toegevoegd aan `/etc/hosts` voor dit voor beeld.  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A] Wijzig de** DHCP-en Cloud configuratie-instellingen om te voor komen dat er onbedoelde hostnamen worden gewijzigd.  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** bereid het besturings systeem voor op het uitvoeren van SAP Hana op NetApp-systemen met NFS zoals beschreven in [SAP Hana op NetApp aff-systemen met NFS-configuratie handleiding](https://www.netapp.com/us/media/tr-4435.pdf). Maak een configuratie bestand voor de configuratie-instellingen NetApp: `/etc/sysctl.d/netapp-hana.conf`.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** configuratie bestand maken met micro soft voor Azure-configuratie-instellingen: `/etc/sysctl.d/ms-az.conf`.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** sunrpc-instellingen aanpassen zoals aanbevolen in [SAP Hana op NetApp aff-systemen met NFS-configuratie handleiding](https://www.netapp.com/us/media/tr-4435.pdf).  
    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>De Azure NetApp Files volumes koppelen

1. **[A]** koppel punten maken voor de Hana-database volumes.  
    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Maak knoop punt-specifieke directory's voor `/usr/sap` op **HN1**.  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Controleer de NFS-domein instelling. Zorg ervoor dat het domein is geconfigureerd als **`localdomain`** en dat de toewijzing is ingesteld op **niemand**.  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** NFSv4-id-toewijzing uitschakelen. Voer de koppelings opdracht uit om de mapstructuur te maken, waar `nfs4_disable_idmapping` zich bevindt.  U kunt de map niet hand matig maken onder/sys/modules, omdat de toegang is gereserveerd voor de kernel/Stuur Programma's.  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** maak de SAP Hana groep en de gebruiker hand matig. De Id's voor de groep sapsys en de gebruiker **HN1**adm moeten worden ingesteld op dezelfde id's die tijdens het onboarden worden opgegeven. Dit voor beeld zijn de Id's ingesteld op **1001**. Anders is het niet mogelijk om toegang te krijgen tot de volumes.  De Id's voor de groeps-sapsys en gebruikers accounts **HN1**adm en sapadm, moeten op alle virtuele machines hetzelfde zijn.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** Hiermee koppelt u de gedeelde Azure NetApp files volumes.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** koppel de knooppunt-specifieke volumes op **hanadb1**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** koppel de knooppunt-specifieke volumes op **hanadb2**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** koppel de knooppunt-specifieke volumes op **hanadb3**.  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Controleer of alle Hana-volumes zijn gekoppeld met de NFS-protocol versie **NFSv4**.  
    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>Installatie  

In dit voor beeld voor de implementatie van SAP HANA in scale-out configuratie met het stand-by-knoop punt met Azure, hebben we HANA 2,0 SP4 gebruikt.  

### <a name="prepare-for-hana-installation"></a>Voor bereiding voor HANA-installatie

1. **[A]** het Hoofdwacht woord instellen vóór de Hana-installatie (u kunt het hoofd wachtwoord uitschakelen nadat de installatie is voltooid). Uitvoeren als `root` opdracht `passwd`.  

2. **[1]** Controleer of u SSH kunt **hanadb2** en **hanadb3**, zonder dat u wordt gevraagd om wacht woord.  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** extra pakketten installeren, vereist voor Hana 2,0 SP4. Zie SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824) voor meer informatie. 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2, 3]** Wijzig het eigendom van SAP HANA `data` en `log` directory's in **HN1**adm.   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA-installatie

1. **[1]** installeer SAP Hana, volg de instructies in [SAP Hana 2,0-installatie en update handleiding](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html). In dit voor beeld installeren we SAP HANA scale-out met Master, één werk nemer en één stand-by-knoop punt.  
   Start het **hdblcm** -programma vanuit de map Hana-installatie software. Gebruik de para meter `internal_network` en geef de adres ruimte door voor het subnet, dat wordt gebruikt voor de interne HANA-communicatie tussen knoop punten.  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   Voer de volgende waarden in bij de prompt.

     * Kies een actie: Voer **1** in (voor installatie)
     * Selecteer extra onderdelen voor installatie: Voer **2** in
     * Voer het installatiepad in: druk op ENTER (standaard ingesteld op/Hana/Shared)
     * Voer de naam van de lokale host in: druk op ENTER om de standaard waarde te accepteren
     * Wilt u hosts toevoegen aan het systeem? **Y** invoeren
     * Geef Coma op die moeten worden toegevoegd: **hanadb2, hanadb3**
     * Voer de naam van de hoofd gebruiker in [root]: druk op ENTER om de standaard waarde te accepteren
     * Voer het hoofd-gebruikers wachtwoord in: Voer het wacht woord in
     * Rollen selecteren voor host hanadb2: Voer **1** in (voor werk nemer)
     * Host-Failovergroep voor host-hanadb2 opgeven [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voer een opslag partitie nummer in voor de host hanadb2 [<<assign automatically>>]: druk op ENTER om de standaard waarde te accepteren
     * Een werk groep voor de host hanadb2 opgeven [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Rollen selecteren voor host hanadb3: Voer **2** in (voor stand-by)
     * Host-Failovergroep voor host-hanadb3 opgeven [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Een werk groep voor de host hanadb3 opgeven [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Voer SAP HANA systeem-ID in: Voer **HN1** in
     * Instantie nummer [00] invoeren: Voer **03** in
     * Voer de groep van de lokale host in [standaard]: druk op ENTER om de standaard instelling te accepteren
     * Systeem gebruik selecteren/index invoeren [4]: Voer **4** in (voor aangepast)
     * Voer de locatie van de gegevens volumes [/hana/data/HN1] in: druk op ENTER om de standaard waarde te accepteren
     * Voer de locatie van de logboek volumes [/hana/log/HN1] in: druk op ENTER om de standaard waarde te accepteren
     * Maximale geheugen toewijzing beperken? [n]: Voer **n** in
     * Voer de hostnaam van het certificaat in voor de host hanadb1 [hanadb1]: druk op ENTER om de standaard waarde te accepteren
     * Voer de hostnaam van het certificaat in voor de host hanadb2 [hanadb2]: druk op ENTER om de standaard waarde te accepteren
     * Voer de hostnaam van het certificaat in voor de host hanadb3 [hanadb3]: druk op ENTER om de standaard waarde te accepteren
     * Voer het wacht woord voor de systeem beheerder (hn1adm) in: Voer het wacht woord in
     * Voer het systeem database gebruikers (systeem) wacht woord in: Voer het systeem wachtwoord in
     * Wacht woord van systeem database gebruiker (systeem) bevestigen: wacht woord van het systeem invoeren
     * Systeem opnieuw opstarten nadat de computer opnieuw is opgestart? [n]: Voer **n** in 
     * Wilt u door gaan (j/n): Valideer de samen vatting en als alles goed lijkt, Voer **y** in


2. **[1]** globale. ini controleren  

   Geef Global. ini weer en zorg ervoor dat de configuratie voor de interne communicatie tussen knoop punten van SAP HANA is geïmplementeerd. Sectie **communicatie**verifiëren. Deze moet de adres ruimte voor **`hana`** subnet hebben en `listeninterface` moet worden ingesteld op `.internal`. Controleer de sectie **internal_hostname_resolution**. Deze moet de IP-adressen hebben voor de HANA-virtuele machines die deel uitmaken van het **`hana`** subnet.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** toewijzing van hosts toevoegen om ervoor te zorgen dat de client-IP-adressen worden gebruikt voor client communicatie. Voeg een sectie toe `public_host_resolution` en voeg de bijbehorende IP-adressen uit het subnet van de client toe.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Start SAP Hana opnieuw op om de wijzigingen te activeren.  
   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Controleer of de client interface de IP-adressen uit het subnet van de **client** gebruikt voor communicatie.  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Zie SAP Note [2183363-configuratie van SAP Hana intern netwerk](https://launchpad.support.sap.com/#/notes/2183363) voor meer informatie over het controleren van de configuratie.  

6. Als u SAP HANA voor de onderliggende opslag van Azure NetApp-bestanden wilt optimaliseren, stelt u de volgende SAP HANA-para meters in:

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **op**
   - `async_write_submit_active` **op**
   - **alles** `async_write_submit_blocks`

   Zie [SAP Hana op NETAPP aff-systemen met NFS-configuratie handleiding](https://www.netapp.com/us/media/tr-4435.pdf)voor meer informatie. 

   Vanaf SAP HANA 2,0-systemen kunt u de para meters instellen in `global.ini`. Zie SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930).  
   Voor SAP HANA 1,0-systemen, versies tot SPS12, kunnen deze para meters tijdens de installatie worden ingesteld, zoals beschreven in SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. De opslag die door Azure NetApp Files wordt gebruikt, heeft een limiet voor de bestands grootte of 16 TB. SAP HANA is niet impliciet op de hoogte van de opslag beperking en er wordt niet automatisch een nieuw gegevens bestand gemaakt wanneer de maximale bestands grootte van 16 TB is bereikt. Als SAP HANA probeert het bestand groter dan 16 TB te verg Roten, waardoor er fouten optreden en uiteindelijk de index server vastloopt. 

   > [!IMPORTANT]
   > Stel de volgende para meters in `global.ini` in om te voor komen SAP HANA gegevens bestanden te verg Roten buiten de [16TB-limiet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) van de opslag sybsystem.  
   > -  datavolume_striping = True
   > - datavolume_striping_size_gb = 15000 voor meer informatie, zie SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005).
   > Pas op de SAP-opmerking [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>SAP HANA failover testen 

1. Knoop punt crash simuleren op een SAP HANA worker-knoop punt  

   Voer de volgende opdrachten uit als **HN1**adm voor het vastleggen van de status van de omgeving, voordat het knoop punt vastloopt.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   Voer de volgende opdracht uit als basis op het worker-knoop punt, in dit geval **hanadb2** om een storing in een knoop punt te simuleren.  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   Controleer het systeem op voltooiing van de failover. Wanneer de failover is voltooid, moet deze er als volgt uitzien: de status die hieronder wordt weer gegeven.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > Als u wilt voor komen dat er vertragingen optreden met SAP HANA failover, stelt u `kernel.panic` tot 20 seconden in op **alle** Hana virtuele machines, wanneer een knoop punt zich in de kernel bevindt. De configuratie wordt uitgevoerd in `/etc/sysctl`. Start de virtuele machines opnieuw op om de wijziging te activeren. Failover: wanneer een knoop punt zich in de kernel voordoet, kan het 10 of meer minuten duren als deze wijziging niet wordt uitgevoerd.  

2. De naam server afbreken  
   Voer de volgende opdrachten uit als **HN1**adm om de status van de omgeving te controleren voordat u de test uitvoert:  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   Voer de volgende opdrachten uit als **HN1**adm op het actieve hoofd knooppunt, in dit geval **hanadb1**.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Het **hanadb3** van het stand-by-knoop punt gaat over als hoofd knooppunt. Resource status na voltooiing van de failover-test:  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Start opnieuw het HANA-exemplaar op **hanadb1**, dat wil zeggen, op dezelfde virtuele machine, waar de naam server is afgebroken. Het **hanadb1** -knoop punt wordt opnieuw lid van de omgeving en blijft de functie stand-by.  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Verwacht de volgende status, nadat SAP HANA is gestart op **hanadb1**:  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   Nu kunt u de naam server opnieuw doden op het huidige actieve hoofd knooppunt, dat wil zeggen, op hanadb3.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   De rol van het hoofd knooppunt wordt hervat met het knoop punt **hanadb1** . Nadat de testfailover is voltooid, ziet er als volgt uit:
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   SAP HANA starten op **hanadb3** : het kan worden gebruikt als een stand-by-knoop punt.  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Status nadat SAP HANA is gestart op **hanadb3**.  
   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>Volgende stappen

* [Azure Virtual Machines planning en implementatie voor SAP][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP][dbms-guide]
* Meer informatie over het instellen van hoge Beschik baarheid en het plannen van nood herstel van SAP 
* HANA op Azure (grote instanties), Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood geval op Azure](hana-overview-high-availability-disaster-recovery.md).
* Zie [hoge Beschik baarheid van SAP Hana op azure virtual machines (vm's)][sap-hana-ha] voor meer informatie over het opzetten van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure-vm's.
