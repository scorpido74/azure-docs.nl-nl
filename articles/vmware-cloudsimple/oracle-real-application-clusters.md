---
title: Azure VMware-oplossing door CloudSimple - Optimaliseer uw CloudSimple Private Cloud voor Oracle RAC
description: Beschrijft hoe u een nieuw cluster implementeert en een VM optimaliseert voor de installatie en configuratie en configuratie van Oracle Real Application Clusters (RAC)
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016014"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimaliseer uw CloudSimple Private Cloud voor het installeren van Oracle RAC

U Oracle Real Application Clusters (RAC) implementeren in uw CloudSimple Private Cloud-omgeving. In deze handleiding wordt beschreven hoe u een nieuw cluster implementeert en een VM voor de Oracle RAC-oplossing optimaliseert. Nadat u de stappen in dit onderwerp hebt voltooid, u Oracle RAC installeren en configureren.

## <a name="storage-policy"></a>Opslagbeleid

Een succesvolle implementatie van Oracle RAC vereist een voldoende aantal knooppunten in het cluster.  In het vSAN-opslagbeleid worden fouten in het verdragen (FTT) toegepast op gegevensschijven die worden gebruikt voor het opslaan van de database, logboek en opnieuw gebruiken van schijven.  Het vereiste aantal knooppunten om fouten effectief te tolereren is 2N+1 waarbij N de waarde van FTT is.

Voorbeeld: Als de gewenste FTT 2 is, moet het totale aantal knooppunten in het cluster 2*2+1 = 5 zijn.

## <a name="overview-of-deployment"></a>Overzicht van implementatie

In de volgende secties wordt beschreven hoe u uw CloudSimple Private Cloud-omgeving instelt voor Oracle RAC.

1. Aanbevolen procedures voor schijfconfiguratie
2. CloudSimple Private Cloud vSphere-cluster implementeren
3. Netwerken instellen voor Oracle RAC
4. VSAN-opslagbeleid instellen
5. Oracle VM's maken en gedeelde VM-schijven maken
6. VM-to-host-affiniteitsregels instellen

## <a name="best-practices-for-disk-configuration"></a>Aanbevolen procedures voor schijfconfiguratie

Oracle RAC virtuele machines hebben meerdere schijven, die worden gebruikt voor specifieke functie.  Gedeelde schijven worden gemonteerd op alle virtuele machines, die worden gebruikt door Oracle RAC Cluster.  Besturingssysteem en software installatie schijven zijn alleen gemonteerd op de individuele virtuele machines.  

![Overzicht van oracle RAC virtuele machineschijven](media/oracle-vm-disks-overview.png)

In het volgende voorbeeld worden de schijven gebruikt die in de onderstaande tabel zijn gedefinieerd.

| Schijf                                      | Doel                                       | Gedeelde schijf |
|-------------------------------------------|-----------------------------------------------|-------------|
| OS                                        | Besturingssysteemschijf                         | Nee          |
| Raster                                      | Locatie installeren voor Oracle Grid-software     | Nee          |
| Database                                  | Locatie voor Oracle-databasesoftware installeren | Nee          |
| ORAHOME ORAHOME                                   | Basislocatie voor Oracle-databasebinaries    | Nee          |
| DATA1, DATA2, DATA3, DATA4                | Schijf waar Oracle-databasebestanden worden opgeslagen   | Ja         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Logschijven opnieuw gebruiken                                | Ja         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Stemschijven                                  | Ja         |
| FRA1, FRA2                                | Schijven snel herstelgebied                      | Ja         |

![Oracle virtual machine disk configuration Oracle virtual machine disk configuration Oracle virtual machine disk configuration Oracle virtual](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Configuratie van virtuele machines

* Elke virtuele machine is geconfigureerd met vier SCSI-controllers.
* Het type SCSI-controller is ingesteld op VMware-paravirtueel.
* Er worden meerdere virtuele schijven (.vmdk) gemaakt.
* Schijven zijn gemonteerd op verschillende SCSI-controllers.
* Het type delen met meerdere typen typen is ingesteld voor gedeelde clusterschijven.
* vSAN-opslagbeleid is gedefinieerd om een hoge beschikbaarheid van schijven te garanderen.

### <a name="operating-system-and-software-disk-configuration"></a>Configuratie van besturingssysteem en softwareschijf

Elke virtuele Oracle-machine is geconfigureerd met meerdere schijven voor het hostbesturingssysteem, swap, software-installatie en andere OS-functies.  Deze schijven worden niet gedeeld tussen de virtuele machines.  

* Drie schijven voor elke virtuele machine zijn geconfigureerd als virtuele schijven en gemonteerd op Oracle RAC virtuele machines.
    * Besturingssysteemschijf
    * Schijf voor het opslaan van Oracle Grid installeert bestanden
    * Schijf voor het opslaan van Oracle-databaseinstallatiebestanden
* Schijven kunnen worden geconfigureerd als **Thin Provisioned**.
* Elke schijf is gemonteerd op de eerste SCSI-controller (SCSI0).  
* Delen is ingesteld op **Niet delen.**
* Redundantie wordt gedefinieerd op de opslag met behulp van vSAN-beleid.  

![Oracle RAC-gegevensschijfgroepconfiguratie](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Configuratie van gegevensschijf

Gegevensschijven worden voornamelijk gebruikt voor het opslaan van databasebestanden.  

* Vier schijven zijn geconfigureerd als virtuele schijven en gemonteerd op alle Oracle RAC virtuele machines.
* Elke schijf is gemonteerd op een andere SCSI-controller.
* Elke virtuele schijf is geconfigureerd als **Dikke Voorziening Eager Zeroed**.  
* Delen is ingesteld op **Multi-writer**.  
* De schijven moeten zijn geconfigureerd als een ASM-schijfgroep (Automatic Storage Management).  
* Redundantie wordt gedefinieerd op de opslag met behulp van vSAN-beleid.  
* ASM redundantie is ingesteld op **externe** redundantie.

![Oracle RAC-gegevensschijfgroepconfiguratie](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Configuratie van logboekschijven opnieuw gebruiken

Redo-logboekbestanden worden gebruikt voor het opslaan van een kopie van de wijzigingen die in de database zijn aangebracht.  De logbestanden worden gebruikt wanneer gegevens moeten worden hersteld na eventuele fouten.

* Logschijven opnieuw gebruiken, moeten als meerdere schijfgroepen worden geconfigureerd.  
* Zes schijven worden gemaakt en gemonteerd op alle Oracle RAC virtuele machines.
* Schijven zijn gemonteerd op verschillende SCSI-controllers
* Elke virtuele schijf is geconfigureerd als **Dikke Voorziening Eager Zeroed**.
* Delen is ingesteld op **Multi-writer**.  
* De schijven moeten worden geconfigureerd als twee ASM-schijfgroepen.
* Elke ASM-schijfgroep bevat drie schijven, die op verschillende SCSI-controllers staan.  
* ASM redundantie is ingesteld op **normale** redundantie.
* Er worden vijf redo-logboekbestanden gemaakt in beide ASM Redo-logboekgroep

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC opnieuw aanmelden schijf groep configuratie](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle-stemschijfconfiguratie

Stemschijven bieden quorumschijffunctionaliteit als extra communicatiekanaal om een split-brain situatie te voorkomen.

* Vijf schijven worden gemaakt en gemonteerd op alle Oracle RAC virtuele machines.
* Schijven zijn gemonteerd op één SCSI-controller
* Elke virtuele schijf is geconfigureerd als **Dikke Voorziening Eager Zeroed**.
* Delen is ingesteld op **Multi-writer**.  
* De schijven moeten zijn geconfigureerd als een ASM-schijfgroep.  
* ASM redundantie is ingesteld op **Hoge** redundantie.

![Oracle RAC stemschijfgroep configuratie](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle snel herstelgebied schijfconfiguratie (optioneel)

Het snelle herstelgebied (FRA) is bestandssysteem dat wordt beheerd door oracle ASM-schijfgroep.  FRA biedt een gedeelde opslaglocatie voor back-up- en herstelbestanden. Oracle maakt gearchiveerde logboeken en flashbacklogs in het gebied van snel herstel. Oracle Recovery Manager (RMAN) kan optioneel zijn back-upsets en afbeeldingskopieën opslaan in het gebied van snel herstel en het wordt gebruikt bij het herstellen van bestanden tijdens mediaherstel.

* Twee schijven worden gemaakt en gemonteerd op alle Oracle RAC virtuele machines.
* Schijven zijn gemonteerd op verschillende SCSI-controller
* Elke virtuele schijf is geconfigureerd als **Dikke Voorziening Eager Zeroed**.
* Delen is ingesteld op **Multi-writer**.  
* De schijven moeten zijn geconfigureerd als een ASM-schijfgroep.  
* ASM redundantie is ingesteld op **externe** redundantie.

![Oracle RAC stemschijfgroep configuratie](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>CloudSimple Private Cloud vSphere-cluster implementeren

Als u een vSphere-cluster op uw private cloud wilt implementeren, volgt u dit proces:

1. Maak vanuit de CloudSimple-portal [een Private Cloud.](create-private-cloud.md) CloudSimple maakt een standaard vCenter-gebruiker met de naam 'cloudowner' in de nieuw gemaakte Private Cloud. Zie [Het machtigingsmodel Privécloud leren voor](learn-private-cloud-permissions.md)meer informatie over het standaard-privécloud-gebruikers- en machtigingsmodel .  Met deze stap wordt het primaire beheercluster voor uw Private Cloud gesmaakt.

2. Breid vanuit de CloudSimple-portal [de Private Cloud uit](expand-private-cloud.md) met een nieuw cluster.  Dit cluster wordt gebruikt om Oracle RAC te implementeren.  Selecteer het aantal knooppunten op basis van de gewenste fouttolerantie (minimaal drie knooppunten).

## <a name="set-up-networking-for-oracle-rac"></a>Netwerken instellen voor Oracle RAC

1. Maak in uw Private Cloud [twee VLAN's,](create-vlan-subnet.md)één voor het openbare Oracle-netwerk en één voor het privénetwerk van Oracle en wijs geschikte subnet-CIDR's toe.
2. Nadat de VLAN's zijn gemaakt, maakt u de [gedistribueerde poortgroepen in het VCenter Private Cloud](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Stel een [virtuele DHCP- en DNS-serverin stel](dns-dhcp-setup.md) op uw beheercluster voor de Oracle-omgeving.
4. [DNS-forwarding configureren op de DNS-server](on-premises-dns-setup.md#create-a-conditional-forwarder) die is geïnstalleerd in de Private Cloud.

## <a name="set-up-vsan-storage-policies"></a>VSAN-opslagbeleid instellen

vSAN-beleid definieert de fouten die moeten worden getolereerd en schijfstriping voor de gegevens die zijn opgeslagen op de VM-schijven.  Het gemaakte opslagbeleid moet worden toegepast op de VM-schijven tijdens het maken van de VM.

1. [Meld u aan bij de vSphere-client](https://docs.azure.cloudsimple.com/vsphere-access) van uw Private Cloud.
2. Selecteer in het bovenste menu **Beleid en profielen**.
3. Selecteer in het linkermenu **vm-opslagbeleid** en selecteer vervolgens **Een VM-opslagbeleid maken**.
4. Voer een betekenisvolle naam in voor het beleid en klik op **VOLGENDE**.
5. Selecteer in de sectie **Beleidsstructuur** de optie **Regels inschakelen voor vSAN-opslag** en klik op **VOLGENDE**.
6. Selecteer **geen** voor**de** tolerantie voor siteramp en beschikbaarheid in de sectie beschikbaarheid van **vSAN.** >  Selecteer de optie **RAID - Mirroring** voor de gewenste FTT als u fouten wilt verdragen.
    ![vSAN-instellingen](media/oracle-rac-storage-wizard-vsan.png).
7. Selecteer **in** de sectie Geavanceerd het aantal schijfstrepen per object. Selecteer **Dikke ingerichte spatie**voor objectspaanderreservering . Selecteer **Objectcontrolesom uitschakelen**. Klik **op VOLGENDE**.
8. Volg de instructies op het scherm om de lijst met compatibele vSAN-gegevenswinkels te bekijken, de instellingen te controleren en de installatie af te ronden.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle VM's maken en gedeelde VM-schijven maken voor Oracle

Als u een VM voor Oracle wilt maken, kloont u een bestaande vm of maakt u een nieuwe vm.  In deze sectie wordt beschreven hoe u een nieuwe virtuele machine maakt en vervolgens kloont om een tweede te maken na het installeren van het basisbesturingssysteem.  Nadat de VM's zijn gemaakt, u er een toevoegen van schijven aan maken.  Oracle-cluster maakt gebruik van gedeelde schijven voor het opslaan, gegevens, logboeken en opnieuw gebruiken logboeken.

### <a name="create-vms"></a>VM's maken

1. Klik in vCenter op het pictogram **Hosts en Clusters.** Selecteer het cluster dat u voor Oracle hebt gemaakt.
2. Klik met de rechtermuisknop op het cluster en selecteer **Nieuwe virtuele machine**.
3. Selecteer **Nieuwe virtuele machine maken** en klik op **Volgende**.
4. Geef de machine een naam, selecteer de locatie van de Oracle VM en klik op **Volgende**.
5. Selecteer de clusterbron en klik op **Volgende**.
6. Selecteer het vSAN-gegevensarchief voor het cluster en klik op **Volgende**.
7. Houd de standaard ESXi 6.5 compatibiliteitsselectie en klik op **Volgende**.
8. Selecteer het gastbesturingssysteem van de ISO voor de VM die u maakt en klik op **Volgende**.
9. Selecteer de grootte van de harde schijf die nodig is voor het installeren van het besturingssysteem.
10. Als u de toepassing op een ander apparaat wilt installeren, klikt u op **Nieuw apparaat toevoegen**.
11. Selecteer netwerkopties en wijs de gedistribueerde poortgroep toe die is gemaakt voor het openbare netwerk.
12. Als u extra netwerkinterfaces wilt toevoegen, klikt u op **Nieuw apparaat toevoegen** en selecteert u de gedistribueerde poortgroep die is gemaakt voor het privénetwerk.
13. Selecteer voor nieuw DC/DVD-station het iso-bestand van de gegevenswinkel dat de ISO bevat voor de voorkeursinstallatie van het besturingssysteem. Selecteer het bestand dat u eerder hebt geüpload naar de map ISO's en sjablonen en klik op **OK**.
14. Controleer de instellingen en klik op **OK** om de nieuwe virtuele machine te maken.
15. Stroom aan op de VM. Installeer het besturingssysteem en eventuele updates die nodig zijn

Nadat het besturingssysteem is geïnstalleerd, u een tweede VM klonen. Klik met de rechtermuisknop op de VM-vermelding en selecteer de optie en kloon.

### <a name="create-shared-disks-for-vms"></a>Gedeelde schijven maken voor VM's

Oracle gebruikt gedeelde schijf om de gegevens op te slaan, logboekbestanden op te slaan en opnieuw te gebruiken.  U een gedeelde schijf op vCenter maken en deze op beide VM's monteren.  Voor hogere prestaties plaatst u de gegevensschijven op verschillende SCSI-controllers In de volgende stappen wordt weergegeven hoe u een gedeelde schijf op vCenter maakt en deze vervolgens aan een virtuele machine koppelt. vCenter Flash-client wordt gebruikt voor het wijzigen van de VM-eigenschappen.

#### <a name="create-disks-on-the-first-vm"></a>Schijven maken op de eerste virtuele machine

1. Klik in vCenter met de rechtermuisknop op een van de Oracle VM's en selecteer **Instellingen bewerken**.
2. Selecteer in de sectie Nieuw apparaat de optie **SCSI-controller** en klik op **Toevoegen**.
3. Selecteer in de sectie Nieuw apparaat de optie **Nieuwe harde schijf** en klik op **Toevoegen**.
4. De eigenschappen van nieuwe harde schijf uitbreiden.
5. Geef de grootte van de harde schijf op.
6. Geef het VM-opslagbeleid op als het vSAN-opslagbeleid dat u eerder hebt gedefinieerd.
7. Selecteer de locatie als map in het vSAN datastore. De locatie helpt bij het browsen en koppelen van de schijven aan een tweede VM.
8. Voor schijfinrichting selecteert u **Dikke voorziening met een te grote nulpunt**.
9. Geef Voor delen **multischrijver**op.
10. Selecteer voor het knooppunt van het virtuele apparaat de nieuwe SCSI-controller die in stap 2 is gemaakt.

    ![Schijven maken op de eerste virtuele machine](media/oracle-rac-new-hard-disk.png)

Herhaal stap 2 – 10 voor alle nieuwe schijven die nodig zijn voor de Oracle-gegevens, logboeken en opnieuw gebruiken logboekbestanden.

#### <a name="attach-disks-to-second-vm"></a>Schijven aan tweede VM koppelen

1. Klik in vCenter met de rechtermuisknop op een van de Oracle VM's en selecteer **Instellingen bewerken**.
2. Selecteer in de sectie Nieuw apparaat de optie **SCSI-controller** en klik op **Toevoegen**.
3. Selecteer **bestaande harde schijf** in de sectie Nieuw apparaat en klik op **Toevoegen**.
4. Blader naar de locatie waar de schijf is gemaakt voor de eerste vm en selecteer het VMDK-bestand.
5. Geef het VM-opslagbeleid op als het vSAN-opslagbeleid dat u eerder hebt gedefinieerd.
6. Voor schijfinrichting selecteert u **Dikke voorziening met een te grote nulpunt**.
7. Geef Voor delen **multischrijver**op.
8. Selecteer voor het knooppunt van het virtuele apparaat de nieuwe SCSI-controller die in stap 2 is gemaakt.

    ![Schijven maken op de eerste virtuele machine](media/oracle-rac-existing-hard-disk.png)

Herhaal stap 2 – 7 voor alle nieuwe schijven die nodig zijn voor de Oracle-gegevens, logboeken en opnieuw aandoenlogboekbestanden.

## <a name="set-up-vm-host-affinity-rules"></a>Vm-hostaffiniteitsregels instellen

VM-to-host-affiniteitsregels zorgen ervoor dat de VM op de gewenste host wordt uitgevoerd.  U regels op vCenter definiëren om ervoor te zorgen dat de Oracle VM op de host wordt uitgevoerd met voldoende resources en om te voldoen aan specifieke licentievereisten.

1. Escaleer in de CloudSimple-portal [de bevoegdheden](escalate-private-cloud-privileges.md) van de cloudowner-gebruiker.
2. [Log in bij de vSphere-client](https://docs.azure.cloudsimple.com/vsphere-access) van uw Private Cloud.
3. Selecteer in de vSphere-client het cluster waarin Oracle VM's zijn geïmplementeerd en klik op **Configureren.**
4. Selecteer onder Configureren **vm/hostgroepen**.
5. Klik **+** op .
6. Een VM-groep toevoegen. Selecteer **VM-groep** als type. Voer de naam van de groep in. Selecteer de VM's en klik op **OK** om de groep te maken.
6. Een hostgroep toevoegen. Selecteer **Hostgroep** als type. Voer de naam van de groep in. Selecteer de hosts waar de VM's worden uitgevoerd en klik op **OK** om de groep te maken.
7. Als u een regel wilt maken, klikt u op **VM/Host-regels**.
8. Klik **+** op .
9. Voer een naam in voor de regel en schakel **Inschakelen in**.
10. Selecteer Virtuele machines **voor**het regeltype als host .
11. Selecteer de VM-groep die de Oracle VM's bevat.
12. Selecteer **Moet worden uitgevoerd op hosts in deze groep**.
13. Selecteer de hostgroep die u hebt gemaakt.
14. Klik op **OK** om de regel te maken.

## <a name="references"></a>Verwijzingen

* [Over vSAN-beleid](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware Multi-Writer-kenmerk voor gedeelde VMDK's](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
