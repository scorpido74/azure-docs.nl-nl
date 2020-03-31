---
title: Upgrade van het besturingssysteem voor de SAP HANA op Azure (Large Instances)| Microsoft Documenten
description: Upgrade van het besturingssysteem voor SAP HANA uitvoeren op Azure (Grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a0a5d39a7cb2162186291ea534a623ef45c40d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675616"
---
# <a name="operating-system-upgrade"></a>Upgrade van het besturingssysteem
Dit document beschrijft de details over upgrades van het besturingssysteem op de HANA Large Instances.

>[!NOTE]
>De OS-upgrade is de verantwoordelijkheid van de klant, Microsoft operations support kan u begeleiden naar de belangrijkste gebieden om op te letten tijdens de upgrade. U moet uw leverancier van het besturingssysteem ook raadplegen voordat u van plan bent voor een upgrade.

Tijdens de inrichting van HLI-eenheden installeert het Microsoft-operationele team het besturingssysteem.
In de loop van de tijd bent u verplicht om het besturingssysteem (Voorbeeld: patchen, afstemmen, upgraden, enz.) op de HLI-eenheid te onderhouden.

Voordat u grote wijzigingen in het besturingssysteem uitvoert (bijvoorbeeld SP1 upgraden naar SP2), moet u contact opnemen met het Microsoft Operations-team door een ondersteuningsticket te openen om te raadplegen.

Neem deel aan je ticket:

* Uw HLI-abonnements-ID.
* Je servernaam.
* Het patchniveau dat u van plan bent toe te passen.
* De datum waarop u deze wijziging plant. 

We raden u aan dit ticket ten minste één week voor de gewenste upgradedatum te openen omdat het Operations-team controleert of een firmware-upgrade nodig is op uw serverblade.


Voor de ondersteuningsmatrix van de verschillende SAP HANA-versies met de verschillende Linux-versies, zie [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Bekende problemen

De volgende zijn de weinige veel voorkomende bekende problemen tijdens de upgrade:
- Op SKU Type II klasse SKU wordt de software foundation software (SFS) verwijderd na de OS-upgrade. U moet de compatibele SFS opnieuw installeren na de os-upgrade.
- Ethernet-kaartstuurprogramma's (ENIC en FNIC) rolden terug naar de oudere versie. U moet de compatibele versie van de stuurprogramma's opnieuw installeren na de upgrade.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA Large Instance (Type I) aanbevolen configuratie

De configuratie van het besturingssysteem kan in de loop van de tijd afwijken van de aanbevolen instellingen als gevolg van patching, systeemupgrades en wijzigingen die door klanten zijn aangebracht. Daarnaast identificeert Microsoft updates die nodig zijn voor bestaande systemen om ervoor te zorgen dat ze optimaal zijn geconfigureerd voor de beste prestaties en veerkracht. In de volgende instructies worden aanbevelingen gegeven die betrekking hebben op netwerkprestaties, systeemstabiliteit en optimale HANA-prestaties.

### <a name="compatible-enicfnic-driver-versions"></a>Compatibele eNIC/fNIC driver versies
  Om de juiste netwerkprestaties en systeemstabiliteit te hebben, wordt geadviseerd ervoor te zorgen dat de OS-specifieke geschikte versie van eNIC- en fNIC-stuurprogramma's worden geïnstalleerd zoals afgebeeld in de volgende compatibiliteitstabel. Servers worden geleverd aan klanten met compatibele versies. Houd er rekening mee dat in sommige gevallen, tijdens het patchen van het besturingssysteem/kernel, stuurprogramma's kunnen worden teruggedraaid naar de standaardstuurprogrammaversies. Controleer of de juiste stuurprogrammaversie post-OS/Kernel-patchbewerkingen uitvoert.
       
      
  |  Os-leverancier    |  Versie van OS-pakket     |  Firmwareversie  |  eNIC Driver |  fNIC Driver | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3u           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3u           |  2.3.0.44    |   1.6.0.36   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3u           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Opdrachten voor de upgrade van de bestuurder en voor het reinigen van oude rpm-pakketten
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Opdrachten om te bevestigen
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLI's GRUB update storing
SAP op Azure HANA Large Instances (Type I) kan na een upgrade in een niet-opstartbare status zijn. De onderstaande procedure lost dit probleem op.
#### <a name="execution-steps"></a>Uitvoeringsstappen


*   Opdracht `multipath -ll` uitvoeren.
*   Haal de LUN-id met een geschatte grootte van ongeveer 50G of gebruik de opdracht:`fdisk -l | grep mapper`
*   Bestand `/etc/default/grub_installdevice` bijwerken `/dev/mapper/<LUN ID>`met regel . Voorbeeld: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>LUN ID varieert van server tot server.


### <a name="disable-edac"></a>EDAC uitschakelen 
   De EDAC-module (Error Detection And Correction) helpt bij het opsporen en corrigeren van geheugenfouten. De onderliggende hardware voor SAP HANA op Azure Large Instances (Type I) werkt echter al dezelfde functie. Het hebben van dezelfde functie ingeschakeld op de hardware en besturingssysteem (OS) niveaus kan leiden tot conflicten en kan leiden tot incidentele, ongeplande shutdowns van de server. Daarom wordt aanbevolen om de module uit te schakelen van het besturingssysteem.

#### <a name="execution-steps"></a>Uitvoeringsstappen

* Controleer of edac-module is ingeschakeld. Als een uitvoer wordt geretourneerd in onderstaande opdracht, betekent dit dat de module is ingeschakeld. 
```
lsmod | grep -i edac 
```
* De modules uitschakelen door de volgende regels aan het bestand toe te voegen`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Een reboot is vereist om wijzigingen in te voeren. Voer `lsmod` de opdracht uit en controleer of de module daar niet aanwezig is in uitvoer.


### <a name="kernel-parameters"></a>Kernelparameters
   Zorg ervoor dat `transparent_hugepage`de `numa_balancing` `processor.max_cstate`juiste `ignore_ce` `intel_idle.max_cstate` instelling voor , , en worden toegepast.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=nooit
* numa_balancing=uitschakelen
* mce=ignore_ce


#### <a name="execution-steps"></a>Uitvoeringsstappen

* Deze parameters toevoegen `GRB_CMDLINE_LINUX` aan de regel in het bestand`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Maak een nieuw grubbestand.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Reboot systeem.


## <a name="next-steps"></a>Volgende stappen
- Raadpleeg [Back-up en herstel](hana-overview-high-availability-disaster-recovery.md) voor de klasse SKU van het BE-back-uptype I.
- Verwijs [OS Backup voor Type II SKU's van revisie 3-stempels](os-backup-type-ii-skus.md) voor de klasse SKU van type II.
