---
title: SAP HANA scale-out met stand-by met Azure NetApp Files op SLES | Microsoft Documenten
description: Handleiding voor hoge beschikbaarheid voor SAP NetWeaver op SUSE Linux Enterprise Server met Azure NetApp-bestanden voor SAP-toepassingen
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
ms.date: 04/06/2020
ms.author: radeltch
ms.openlocfilehash: b584341931299e408b596bd6a66d1da4580cfffe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754777"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>Een SAP HANA-scale-outsysteem met stand-by node op Azure VM's implementeren met Azure NetApp-bestanden op SUSE Linux Enterprise Server 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


In dit artikel wordt beschreven hoe u een zeer beschikbaar SAP HANA-systeem implementeert in een scale-outconfiguratie met stand-by op Virtuele Azure-machines (VM's) door [Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/) te gebruiken voor de gedeelde opslagvolumes.  

In de voorbeeldconfiguraties, installatieopdrachten, enzovoort, is de HANA-instantie **03** en is de HANA-systeem-ID **HN1**. De voorbeelden zijn gebaseerd op HANA 2.0 SP4 en SUSE Linux Enterprise Server voor SAP 12 SP4. 

Raadpleeg voordat u begint de volgende SAP-notities en -documenten:

* [Azure NetApp-bestandendocumentatie][anf-azure-doc] 
* SAP Note [1928533] bevat:  
  * Een lijst met Azure VM-formaten die worden ondersteund voor de implementatie van SAP-software
  * Belangrijke capaciteitsinformatie voor Azure VM-formaten
  * Ondersteunde SAP-software en besturingssysteem- en databasecombinaties
  * De vereiste SAP-kernelversie voor Windows en Linux op Microsoft Azure
* SAP Note [2015553]: Bevat vereisten voor SAP-ondersteunde SAP-softwareimplementaties in Azure
* SAP Note [2205917]: Bevat aanbevolen OS-instellingen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [1944799]: Bevat SAP-richtlijnen voor SUSE Linux Enterprise Server voor SAP-toepassingen
* SAP Note [2178632]: Bevat gedetailleerde informatie over alle monitoringstatistieken die voor SAP in Azure worden gerapporteerd
* SAP Note [2191498]: Bevat de vereiste SAP Host Agent-versie voor Linux in Azure
* SAP Note [2243692]: Bevat informatie over SAP-licenties op Linux in Azure
* SAP Note [1984787]: Bevat algemene informatie over SUSE Linux Enterprise Server 12
* SAP Note [1999351]: Bevat aanvullende informatie over probleemoplossing voor de Azure Enhanced Monitoring Extension voor SAP
* SAP Note [1900823]: Bevat informatie over sap HANA-opslagvereisten
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Bevat alle vereiste SAP notes voor Linux
* [Azure Virtual Machines planning en implementatie voor SAP op Linux][planning-guide]
* [Azure Virtual Machines-implementatie voor SAP op Linux][deployment-guide]
* [Azure Virtual Machines DBMS-implementatie voor SAP op Linux][dbms-guide]
* [SUSE SAP HA Best Practice Guides][suse-ha-guide]: Bevat alle benodigde informatie voor het instellen van NetWeaver High Availability en SAP HANA System Replication on-premises (te gebruiken als algemene basislijn; ze bieden veel meer gedetailleerde informatie)
* [SUSE High Availability Extension 12 SP3 Release Notes][suse-ha-12sp3-relnotes]
* [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]


## <a name="overview"></a>Overzicht

Een methode voor het bereiken van HANA hoge beschikbaarheid is door het configureren van host auto failover. Als u hostautomatische failover wilt configureren, voegt u een of meer virtuele machines toe aan het HANA-systeem en configureert u deze als stand-byknooppunten. Wanneer het actieve knooppunt uitvalt, neemt een stand-by knooppunt het automatisch over. In de gepresenteerde configuratie met virtuele Azure-machines bereikt u automatische failover door NFS te gebruiken [op Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)  

> [!NOTE]
> Het stand-by knooppunt heeft toegang nodig tot alle databasevolumes. De HANA volumes moeten worden gemonteerd als NFSv4 volumes. Het verbeterde file lease-gebaseerde vergrendelingsmechanisme in `I/O` het NFSv4-protocol wordt gebruikt voor het schermen. 

> [!IMPORTANT]
> Als u de ondersteunde configuratie wilt bouwen, moet u de HANA-gegevens en logboekvolumes implementeren als NFSv4.1-volumes en deze monteren met behulp van het NFSv4.1-protocol. De HANA host auto-failover configuratie met stand-by node wordt niet ondersteund met NFSv3.

![Overzicht van SAP NetWeaver Hoge beschikbaarheid](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

In het voorgaande diagram, dat de aanbevelingen van het SAP HANA-netwerk volgt, worden drie subnetten weergegeven binnen één virtueel Azure-netwerk: 
* Voor klantcommunicatie
* Voor communicatie met het opslagsysteem
* Voor interne HANA inter-node communicatie

De Azure NetApp-volumes bevinden zich in een afzonderlijk subnet, [gedelegeerd aan Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)  

Voor deze voorbeeldconfiguratie zijn de subnetten:  

  - `client`10.23.0.0/24  
  - `storage`10.23.2.0/24  
  - `hana`10.23.3.0/24  
  - `anf`10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>De Infrastructuur voor Azure NetApp-bestanden instellen 

Voordat u verder gaat met de installatie voor Azure NetApp Files-infrastructuur, moet u vertrouwd raken met de [documentatie van Azure NetApp-bestanden.][anf-azure-doc] 

Azure NetApp-bestanden zijn beschikbaar in verschillende [Azure-regio's.](https://azure.microsoft.com/global-infrastructure/services/?products=netapp) Controleer of uw geselecteerde Azure-regio Azure NetApp-bestanden biedt.  

Zie [Azure NetApp-bestandenbeschikbaarheid per Azure-regio voor][anf-avail-matrix]informatie over de beschikbaarheid van Azure NetApp-bestanden per Azure-regio.  

Voordat u Azure NetApp-bestanden implementeert, moet u onboarding aanvragen bij Azure NetApp-bestanden door naar [Instructies voor Azure NetApp-bestanden][anf-register]te gaan. 

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp-bestandenresources implementeren  

De volgende instructies gaan ervan uit dat u uw [virtuele Azure-netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)al hebt geïmplementeerd. De bronnen en VM's van Azure NetApp Files, waar de Azure NetApp-bestandenbronnen worden gemonteerd, moeten worden geïmplementeerd in hetzelfde virtuele Azure-netwerk of in gepeerde Virtuele Netwerken van Azure.  

1. Als u de resources nog niet hebt geïmplementeerd, vraagt u [onboarding aan bij Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).  

2. Maak een NetApp-account in uw geselecteerde Azure-regio door de instructies in [Een NetApp-account maken](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)te volgen.  

3. Stel een capaciteitsgroep voor Azure NetApp-bestanden in door de instructies in [een capaciteitsgroep voor Azure NetApp-bestanden in te stellen.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)  

   De HANA-architectuur die in dit artikel wordt gepresenteerd, maakt gebruik van één Azure NetApp-capaciteitsgroep op *Ultra-serviceniveau.* Voor HANA-workloads op Azure raden we u aan een Azure NetApp Files *Ultra-* of [Premium-serviceniveau te](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)gebruiken. *Premium*  

4. Delegeren van een subnet aan Azure NetApp Files, zoals beschreven in de instructies in [Een subnet delegeren aan Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet).  

5. Azure NetApp-bestandenvolumes implementeren door de instructies te volgen in [Een NFS-volume maken voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes).  

   Wanneer u de volumes implementeert, moet u de **NFSv4.1-versie** selecteren. Op dit moment vereist toegang tot NFSv4.1 extra whitelisting. Implementeer de volumes in het aangewezen [subnet](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)Azure NetApp Files . 
   
   Houd er rekening mee dat de Azure NetApp Files-bronnen en de Azure VM's zich in hetzelfde azure virtuele netwerk of in gepeerde Azure-virtuele netwerken moeten bevinden. **Bijvoorbeeld, HN1**-data-mnt00001, **HN1**-log-mnt00001, enzovoort, zijn de volumenamen en nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001, enzovoort, zijn de bestandspaden voor de Azure NetApp Files-volumes.  

   * volume **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001 (nfs://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002 (nfs://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**-gedeeld (nfs://10.23.1.4/**HN1**-gedeeld)
   
   In dit voorbeeld gebruikten we een afzonderlijk Azure NetApp Files-volume voor elk HANA-gegevens- en logboekvolume. Voor een meer kostengeoptimaliseerde configuratie op kleinere of niet-productieve systemen is het mogelijk om alle gegevensbevestigingen en alle logboeken op één volume te plaatsen.  

### <a name="important-considerations"></a>Belangrijke overwegingen

Houd tijdens het maken van uw Azure NetApp-bestanden voor SAP NetWeaver op de SUSE-architectuur voor hoge beschikbaarheid rekening met de volgende belangrijke overwegingen:

- De minimale capaciteitspool is 4 tebibytes (TiB).  
- De minimale volumegrootte is 100 gibibytes (GiB).
- Azure NetApp-bestanden en alle virtuele machines waar de Azure NetApp-bestandenvolumes worden gemonteerd, moeten zich in hetzelfde virtuele Azure-netwerk bevinden of in [peered virtuele netwerken](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in dezelfde regio.  
- Het geselecteerde virtuele netwerk moet een subnet hebben dat is gedelegeerd aan Azure NetApp-bestanden.
- De doorvoer van een Azure NetApp Files-volume is een functie van het volumequotum en serviceniveau, zoals gedocumenteerd in [Serviceniveau voor Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) Wanneer u de HANA Azure NetApp-volumes in de maat maakt, moet u ervoor zorgen dat de resulterende doorvoer voldoet aan de HANA-systeemvereisten.  
- Met het [exportbeleid voor](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)Azure NetApp-bestanden u de toegestane clients, het toegangstype (lezen, lezen alleen, enzovoort) beheren. 
- De functie Azure NetApp-bestanden is nog niet zonebewust. Momenteel wordt de functie niet geïmplementeerd in alle beschikbaarheidszones in een Azure-gebied. Wees je bewust van de mogelijke latentie-implicaties in sommige Azure-regio's.  
-  

> [!IMPORTANT]
> Voor SAP HANA-workloads is lage latentie van cruciaal belang. Werk samen met uw Microsoft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp-bestanden in de nabijheid worden geïmplementeerd.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Grootte voor HANA-database op Azure NetApp-bestanden

De doorvoer van een Azure NetApp Files-volume is een functie van de volumegrootte en het serviceniveau, zoals gedocumenteerd in [Serviceniveau voor Azure NetApp-bestanden.](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) 

Wanneer u de infrastructuur voor SAP in Azure ontwerpt, moet u rekening houden met een aantal minimale opslagvereisten van SAP, wat zich vertaalt in minimale doorvoerkenmerken:

- Lees-schrijven op /hana/log van 250 megabyte per seconde (MB/s) met 1 MB I/O-formaten.  
- Leesactiviteit van ten minste 400 MB/s inschakelen voor /hana/gegevens voor 16 MB en 64 MB I/O-formaten.  
- Schrijfactiviteit van ten minste 250 MB/s inschakelen voor /hana/gegevens met 16 MB en 64 MB I/O-formaten. 

De [doorvoerlimieten voor Azure NetApp-bestanden](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels) per volumequotum van 1 TiB zijn:
- Premium Storage tier - 64 MiB/s  
- Ultra-opslaglaag - 128 MiB/s  

Om te voldoen aan de SAP minimale doorvoervereisten voor gegevens en logboek, en de richtlijnen voor /hana/shared, zijn de aanbevolen maten:

| Volume | Grootte van<br>Premium opslaglaag | Grootte van<br>Ultraopslaglaag | Ondersteund NFS-protocol |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/gedeeld | Max (512 GB, 1xRAM) per 4 werkknooppunten | Max (512 GB, 1xRAM) per 4 werkknooppunten | v3 of v4.1 |

De SAP HANA-configuratie voor de lay-out die in dit artikel wordt gepresenteerd, met behulp van Azure NetApp Files Ultra Storage-laag, zou zijn:

| Volume | Grootte van<br>Ultraopslaglaag | Ondersteund NFS-protocol |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/gedeeld | 2 TiB | v3 of v4.1 |

> [!NOTE]
> De aanbevelingen voor het dimensioneren van Azure NetApp Files die hier worden vermeld, zijn bedoeld om te voldoen aan de minimale vereisten die SAP aanbeveelt voor hun infrastructuurproviders. In echte klantimplementaties en werkbelastingscenario's zijn deze groottes mogelijk niet voldoende. Gebruik deze aanbevelingen als uitgangspunt en pas deze aan op basis van de vereisten van uw specifieke werkbelasting.  

> [!TIP]
> U het formaat van Azure NetApp-bestandenvolumes dynamisch wijzigen, zonder dat u de volumes hoeft los te *maken,* de virtuele machines te stoppen of SAP HANA te stoppen. Deze aanpak biedt flexibiliteit om te voldoen aan zowel de verwachte als onvoorziene doorvoervereisten van uw toepassing.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Linux virtuele machines implementeren via de Azure-portal

Eerst moet u de Azure NetApp Files-volumes maken. Voer vervolgens de volgende stappen uit:
1. Maak de [subnetten van het Azure-virtuele netwerk](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) in uw [virtuele Azure-netwerk.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) 
1. Implementeer de VM's. 
1. Maak de extra netwerkinterfaces en koppel de netwerkinterfaces aan de bijbehorende VM's.  

   Elke virtuele machine heeft drie netwerkinterfaces, die overeenkomen met`client`de `storage` `hana`drie Azure virtuele netwerksubnetten ( , en ). 

   Zie [Een virtuele Linux-machine maken in Azure met meerdere netwerkinterfacekaarten](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)voor meer informatie.  

> [!IMPORTANT]
> Voor SAP HANA-workloads is lage latentie van cruciaal belang. Om een lage latentie te bereiken, werkt u samen met uw Microsoft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp-bestanden in de nabijheid worden geïmplementeerd. Wanneer u een [nieuw SAP HANA-systeem invoert](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) dat SAP HANA Azure NetApp Files gebruikt, dient u de benodigde informatie in. 
 
In de volgende instructies wordt ervan uitgegaan dat u de brongroep, het virtuele Azure-netwerk en de drie azure-virtuele netwerksubnetten al hebt gemaakt: `client`, `storage` en `hana`. Wanneer u de VM's implementeert, selecteert u het clientsubnet, zodat de clientnetwerkinterface de primaire interface op de VM's is. U moet ook een expliciete route configureren naar het gedelegeerde subnet van Azure NetApp Files via de subnetgateway voor opslag. 

> [!IMPORTANT]
> Zorg ervoor dat het besturingssysteem dat u selecteert SAP-gecertificeerd is voor SAP HANA op de specifieke VM-typen die u gebruikt. Ga voor een lijst met SAP HANA-gecertificeerde VM-typen en OS-releases voor die typen naar de [SAP HANA-gecertificeerde IaaS-platformssite.](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) Klik op de details van het vermelde VM-type om de volledige lijst met SAP HANA-ondersteunde OS-releases voor dat type te krijgen.  

1. Maak een beschikbaarheidsset voor SAP HANA. Zorg ervoor dat u het domein voor maximale updates instelt.  

2. Maak drie virtuele machines (**hanadb1**, **hanadb2**, **hanadb3**) door het doen van de volgende stappen:  

   a. Gebruik een SLES4SAP-afbeelding in de Azure-galerie die wordt ondersteund voor SAP HANA. We gebruikten een SLES4SAP 12 SP4 afbeelding in dit voorbeeld.  

   b. Selecteer de beschikbaarheidsset die u eerder hebt gemaakt voor SAP HANA.  

   c. Selecteer het subnet van het client Azure-netwerk. Selecteer [Versneld netwerk](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).  

   Wanneer u de virtuele machines implementeert, wordt de naam van de netwerkinterface automatisch gegenereerd. In deze instructies voor eenvoud verwijzen we naar de automatisch gegenereerde netwerkinterfaces, die zijn gekoppeld aan het virtuele netwerksubnet van de client Azure, als **hanadb1-client,** **hanadb2-client**en **hanadb3-client.** 

3. Maak drie netwerkinterfaces, één voor elke `storage` virtuele machine, voor het virtuele netwerksubnet (in dit voorbeeld **hanadb1-storage,** **hanadb2-storage**en **hanadb3-storage).**  

4. Maak drie netwerkinterfaces, een voor elke `hana` virtuele machine, voor het virtuele netwerk subnet (in dit voorbeeld, **hanadb1-hana**, **hanadb2-hana**, en **hanadb3-hana**).  

5. Koppel de nieuw gemaakte virtuele netwerkinterfaces aan de bijbehorende virtuele machines door de volgende stappen uit te voeren:  

    a. Ga naar de virtuele machine in de [Azure-portal.](https://portal.azure.com/#home)  

    b. Selecteer **virtuele machines**in het linkerdeelvenster . Filter op de naam van de virtuele machine (bijvoorbeeld **hanadb1)** en selecteer vervolgens de virtuele machine.  

    c. Selecteer In het deelvenster **Overzicht** de optie **Stoppen** om de virtuele machine uit te wijzen.  

    d. Selecteer **Netwerken**en voeg de netwerkinterface toe. Selecteer in de vervolgkeuzelijst **Netwerkinterface koppelen** de reeds `storage` `hana` gemaakte netwerkinterfaces voor de en subnetten.  
    
    e. Selecteer **Opslaan**. 
 
    f. Herhaal stappen b door e voor de resterende virtuele machines (in ons voorbeeld, **hanadb2** en **hanadb3**).
 
    g. Laat de virtuele machines in gestopte staat voor nu. Vervolgens maken we [versneld netwerken](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) mogelijk voor alle nieuw aangesloten netwerkinterfaces.  

6. Schakel versnelde netwerken in voor de `storage` `hana` extra netwerkinterfaces voor de extra netwerkinterfaces voor de extra netwerkinterfaces voor de en subnetten door de volgende stappen uit te voeren:  

    a. Open [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in de [Azure-portal](https://portal.azure.com/#home).  

    b. Voer de volgende opdrachten uit om versnelde netwerken in te schakelen `storage` voor `hana` de extra netwerkinterfaces, die zijn gekoppeld aan de en subnetten.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. Start de virtuele machines door de volgende stappen uit te voeren:  

    a. Selecteer **virtuele machines**in het linkerdeelvenster . Filter op de naam van de virtuele machine (bijvoorbeeld **hanadb1)** en selecteer deze vervolgens.  

    b. Selecteer **Start**in het deelvenster **Overzicht** .  

## <a name="operating-system-configuration-and-preparation"></a>Configuratie en voorbereiding van het besturingssysteem

De instructies in de volgende secties zijn vooraf bevestigd met een van de volgende:
* **[A]**: Van toepassing op alle knooppunten
* **[1]**: Alleen van toepassing op knooppunt 1
* **[2]**: Alleen van toepassing op knooppunt 2
* **[3]**: Alleen van toepassing op knooppunt 3

Configureer en bereid uw besturingssysteem voor door de volgende stappen uit te voeren:

1. **[A]** Onderhoud de hostbestanden op de virtuele machines. Neem vermeldingen op voor alle subnetten. Voor dit voorbeeld zijn `/etc/hosts` de volgende vermeldingen toegevoegd.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** Dhcp- en cloudconfig-instellingen voor de netwerkinterface voor opslag wijzigen om onbedoelde hostnamewijzigingen te voorkomen.  

    De volgende instructies gaan ervan `eth1`uit dat de interface van het opslagnetwerk . 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Voeg een netwerkroute toe, zodat de communicatie naar de Azure NetApp-bestanden via de opslagnetwerkinterface verloopt.  

    De volgende instructies gaan ervan `eth1`uit dat de interface van het opslagnetwerk .  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    Start de VM opnieuw op om de wijzigingen te activeren.  

3. **[A]** Bereid het besturingssysteem voor op het uitvoeren van SAP HANA op NetApp-systemen met NFS, zoals beschreven in [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]. Maak configuratiebestand */etc/sysctl.d/netapp-hana.conf* voor de NetApp-configuratie-instellingen.  

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

4. **[A]** Maak configuratiebestand */etc/sysctl.d/ms-az.conf* met Microsoft voor Azure-configuratie-instellingen.  

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

4. **[A]** Pas de sunrpc-instellingen aan, zoals aanbevolen in de [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure].  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>De Azure NetApp-bestandenvolumes monteren

1. **[A]** Maak bevestigingspunten voor de HANA-databasevolumes.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** Maak node-specifieke mappen voor /usr/sap op **HN1**-gedeeld.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** Controleer de NFS-domeininstelling. Zorg ervoor dat het domein is geconfigureerd als het standaard Azure **`defaultv4iddomain.com`** NetApp Files-domein, d.w.z. en dat de toewijzing op **niemand**is ingesteld.  

    > [!IMPORTANT]
    > Zorg ervoor dat het NFS-domein in de VM wordt ingesteld `/etc/idmapd.conf` op **`defaultv4iddomain.com`** de standaarddomeinconfiguratie op Azure NetApp-bestanden: . Als er een mismatch is tussen de domeinconfiguratie op de NFS-client (d.w.z. de VM) en de NFS-server, d.w.z. de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes die op de VM's zijn gemonteerd, weergegeven als `nobody`.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`Verifiëren . Het moet worden ingesteld op **Y**. Als u de `nfs4_disable_idmapping` mapstructuur wilt maken waar zich zich bevindt, voert u de opdracht monteren uit. U de map niet handmatig maken onder /sys/modules, omdat de toegang is gereserveerd voor de kernel/drivers.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** Maak de SAP HANA-groep en gebruiker handmatig. De id's voor groepssapsys en gebruiker **hn1**adm moeten worden ingesteld op dezelfde id's, die worden verstrekt tijdens de onboarding. (In dit voorbeeld zijn de id's ingesteld op **1001**.) Als de id's niet correct zijn ingesteld, hebt u geen toegang tot de volumes. De id's voor groepssapsys en gebruikersaccounts **hn1**adm en sapadm moeten hetzelfde zijn op alle virtuele machines.  

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

6. **[A]** Monteer de gedeelde Azure NetApp-bestandenvolumes.  

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

7. **[1]** Monteer de node-specifieke volumes op **hanadb1**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** Monteer de node-specifieke volumes op **hanadb2**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** Monteer de node-specifieke volumes op **hanadb3**.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** Controleer of alle HANA-volumes zijn gemonteerd met NFS-protocolversie **NFSv4**.  

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

## <a name="installation"></a>Installeren  

In dit voorbeeld voor het implementeren van SAP HANA in scale-outconfiguratie met stand-by node met Azure, hebben we HANA 2.0 SP4 gebruikt.  

### <a name="prepare-for-hana-installation"></a>Voorbereiden op HANA-installatie

1. **[A]** Voor de HANA-installatie stelt u het hoofdwachtwoord in. U het hoofdwachtwoord uitschakelen nadat de installatie is voltooid. Uitvoeren `root` als `passwd`opdracht .  

2. **Controleer** of u via SSH inloggen op **hanadb2** en **hanadb3,** zonder dat u om een wachtwoord wordt gevraagd.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** Installeer extra pakketten, die nodig zijn voor HANA 2.0 SP4. Zie SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824)voor meer informatie. 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** Verander eigenaar schap `data` van `log` SAP HANA en mappen naar **hn1**adm.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA-installatie

1. **[1]** Installeer SAP HANA door de instructies in de [SAP HANA 2.0-installatie- en updatehandleiding op te volgen.](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html) In dit voorbeeld installeren we SAP HANA scale-out met master, één werknemer en één stand-by node.  

   a. Start het **hdblcm-programma** vanuit de HANA-installatiesoftwaremap. Gebruik `internal_network` de parameter en geef de adresruimte door voor subnet, dat wordt gebruikt voor de interne HANA internodecommunicatie.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. Voer bij de prompt de volgende waarden in:

     * Voor **Een actie kiezen:** voer **1** in (voor installatie)
     * Voor **extra onderdelen voor installatie**: voer **2, 3 in**
     * Voor installatiepad: druk op Enter (standaard ingesteld op /hana/gedeeld)
     * Voor **lokale hostnaam:** druk op Enter om de standaardinstelling te accepteren
     * Onder **Wilt u hosts toevoegen aan het systeem?**: voer **y**
     * Voor **komma-gescheiden host namen toe te voegen**: voer **hanadb2, hanadb3**
     * Voor **rootgebruikersnaam** [root]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Root-gebruikerswachtwoord:** voer het wachtwoord van de hoofdgebruiker in
     * Voor rollen voor gastheer hanadb2: voer **1** in (voor werknemer)
     * Voor **Host Failover Group** voor host hanadb2 [default]: druk op Enter om de standaardinstelling te accepteren
     * Voor **opslagpartitienummer** voor hosthanadb2 [<<assign automatically>>]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Werknemersgroep** voor host hanadb2 [standaard]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Selecteer rollen** voor host hanadb3: voer **2** in (voor stand-by)
     * Voor **Host Failover Group** voor host hanadb3 [default]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Werknemersgroep** voor host hanadb3 [standaard]: druk op Enter om de standaardinstelling te accepteren
     * Voor **SAP HANA System ID**: voer **HN1** in
     * **Bijvoorbeeld nummer** [00]: voer **03** in
     * Voor **lokale hostworkergroep** [standaard]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Systeemgebruik selecteren / Index invoeren [4]**: voer **4** in (voor aangepast)
     * Voor **locatie van gegevensvolumes** [/hana/data/HN1]: druk op Enter om de standaardinstelling te accepteren
     * Voor **locatie van logboekvolumes** [/hana/log/HN1]: druk op Enter om de standaardinstelling te accepteren
     * Voor **Maximale geheugentoewijzing beperken?** [n]: voer **n** in
     * Voor **certificate host name voor host hanadb1** [hanadb1]: druk op Enter om de standaardinstelling te accepteren
     * Voor **certificate host name voor host hanadb2** [hanadb2]: druk op Enter om de standaardinstelling te accepteren
     * Voor **certificate host name voor host hanadb3** [hanadb3]: druk op Enter om de standaardinstelling te accepteren
     * Voor **systeembeheerder (hn1adm) wachtwoord:** voer het wachtwoord in
     * **Wachtwoord voor systeemdatabasegebruiker (systeem) :** voer het wachtwoord van het systeem in
     * Wachtwoord **voor Systeemdatabasegebruiker (systeem) bevestigen:** voer het wachtwoord van het systeem in
     * Voor **Opnieuw opstarten systeem na machine reboot?** [n]: voer **n** in 
     * Want **Wilt u doorgaan (y/n)**: de samenvatting valideren en als alles er goed uitziet, voert u **y**


2. **[1]** Verify global.ini  

   Geef global.ini weer en zorg ervoor dat de configuratie voor de interne SAP HANA internodecommunicatie aanwezig is. Controleer de **communicatiesectie.** Het moet de adresruimte `hana` voor het `listeninterface` subnet `.internal`hebben, en moet worden ingesteld op . Controleer de **sectie internal_hostname_resolution.** Het moet de IP-adressen voor de HANA `hana` virtuele machines die behoren tot het subnet.  

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

3. **[1]** Voeg hosttoewijzing toe om ervoor te zorgen dat de IP-adressen van de client worden gebruikt voor clientcommunicatie. Voeg `public_host_resolution`sectie toe en voeg de bijbehorende IP-adressen toe uit het subnet van de client.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** Start SAP HANA opnieuw op om de wijzigingen te activeren.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** Controleer of de clientinterface de IP-adressen van het `client` subnet gebruikt voor communicatie.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   Zie SAP Note [2183363 - Configuratie van het interne netwerk VAN SAP HANA](https://launchpad.support.sap.com/#/notes/2183363)voor informatie over het verifiëren van de configuratie.  

6. Als u SAP HANA wilt optimaliseren voor de onderliggende Azure NetApp-bestandenopslag, stelt u de volgende SAP HANA-parameters in:

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**op**
   - `async_write_submit_active`**op**
   - `async_write_submit_blocks`**alle**

   Zie [NetApp SAP-toepassingen op Microsoft Azure met Azure NetApp-bestanden][anf-sap-applications-azure]voor meer informatie. 

   Vanaf SAP HANA 2.0-systemen u `global.ini`de parameters instellen in. Zie SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930)voor meer informatie.  
   
   Voor SAP HANA 1.0-systeemversies SPS12 en eerder kunnen deze parameters tijdens de installatie worden ingesteld, zoals beschreven in SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798).  

7. De opslag die wordt gebruikt door Azure NetApp Files heeft een bestandsgroottebeperking van 16 terabyte (TB). SAP HANA is zich niet impliciet bewust van de opslagbeperking en maakt niet automatisch een nieuw gegevensbestand wanneer de limiet voor bestandsgrootte van 16 TB is bereikt. Als SAP HANA probeert om het bestand te groeien dan 16 TB, die poging zal resulteren in fouten en, uiteindelijk, in een index server crash. 

   > [!IMPORTANT]
   > Stel de volgende parameters in `global.ini`om te voorkomen dat SAP HANA gegevensbestanden buiten de [16 TB-limiet](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits) van het subsysteem voor opslag probeert te laten groeien.  
   > - datavolume_striping = waar
   > - datavolume_striping_size_gb = 15000 Zie SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005)voor meer informatie.
   > Wees je bewust van SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285). 

## <a name="test-sap-hana-failover"></a>Test SAP HANA failover 

1. Een knooppuntcrash simuleren op een SAP HANA-werknemersknooppunt. Ga als volgt te werk: 

   a. Voer voordat u de knooppuntcrash simuleert de volgende opdrachten uit als **hn1**adm om de status van de omgeving vast te leggen:  

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

   b. Als u een knooppuntcrash wilt simuleren, voert u de volgende opdracht als hoofd op het werkknooppunt uit, dat in dit geval **hanadb2** is:  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. Controleer het systeem op failovervoltooiing. Wanneer de failover is voltooid, legt u de status vast, die er als volgt moet uitzien:  

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
   > Wanneer een knooppunt kernelpaniek ondervindt, voorkom dan vertragingen met SAP HANA-failover door op 20 seconden in te stellen `kernel.panic` op *alle* virtuele HANA-machines. De configuratie wordt `/etc/sysctl`uitgevoerd in . Start de virtuele machines opnieuw op om de wijziging te activeren. Als deze wijziging niet wordt uitgevoerd, kan failover 10 of meer minuten duren wanneer een knooppunt kernelpaniek ondervindt.  

2. Dood de naamserver door het volgende te doen:

   a. Controleer voorafgaand aan de test de status van de omgeving door de volgende opdrachten uit te voeren als **hn1**adm:  

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

   b. Voer de volgende opdrachten uit als **hn1**adm op het actieve masterknooppunt, dat in dit geval **hanadb1** is:  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    De stand-by node **hanadb3** zal overnemen als master node. Hier is de resourcestatus nadat de failovertest is voltooid:  

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

   c. Start de HANA-instantie opnieuw op **hanadb1** (dat wil zeggen op dezelfde virtuele machine, waar de naamserver werd gedood). Het **hanadb1-knooppunt** zal zich weer aansluiten bij het milieu en zijn stand-byrol behouden.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Na SAP HANA is begonnen op **hanadb1**, verwachten de volgende status:  

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

   d. Nogmaals, dood de naam server op de momenteel actieve master node (dat wil zeggen, op node **hanadb3**).  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1** zal de rol van master node hervatten. Nadat de failovertest is voltooid, ziet de status er als volgt uit:

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

   e. Start SAP HANA op **hanadb3**, die klaar zal zijn om te dienen als een stand-by node.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   Nadat SAP HANA is gestart op **hanadb3,** ziet de status er als volgt uit:  

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
* Zie [Hoge beschikbaarheid van SAP HANA op Azure Virtual Machines (VM's)][sap-hana-ha]voor meer informatie over het instellen van hoge beschikbaarheid en het plannen van noodherstel van SAP HANA op Azure VM's.
