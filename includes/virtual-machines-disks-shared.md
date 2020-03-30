---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 02/18/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: a14ae76e15c1adb59917e61fbcbdaa34a7efa2d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77472010"
---
Azure shared disks (preview) is een nieuwe functie voor Azure managed disks waarmee een Azure managed disk tegelijkertijd aan meerdere virtuele machines (VM's) kan worden gekoppeld. Als u een beheerde schijf aan meerdere VM's koppelt, u nieuwe nieuwe toepassingen implementeren of bestaande geclusterde toepassingen migreren naar Azure.

## <a name="how-it-works"></a>Hoe werkt het?

VM's in het cluster kunnen lezen of schrijven naar uw bijgevoegde schijf op basis van de reservering die is gekozen door de geclusterde toepassing met Behulp van [SCSI Persistent Reservations](https://www.t10.org/members/w_spc3.htm) (SCSI PR). SCSI PR is een bekende industriestandaard die wordt gebruikt door applicaties die on-premises worden uitgevoerd op Storage Area Network (SAN). Als u SCSI PR op een beheerde schijf inschakelt, u deze toepassingen naar Azure migreren.

Beheerde schijven met gedeelde schijven ingeschakeld bieden gedeelde blokopslag die toegankelijk is voor meerdere VM's, dit wordt weergegeven als logische eenheidsnummers (LUN's). LUN's worden vervolgens vanaf een doel (schijf) aan een initiator (VM) gepresenteerd. Deze Lunsen zien eruit als direct-attached-storage (DAS) of een lokaal station naar de VM.

Beheerde schijven met ingeschakelde gedeelde schijven bieden niet native een volledig beheerd bestandssysteem dat toegankelijk is met Behulp van SMB/NFS. U moet een clusterbeheer gebruiken, zoals Het Failovercluster (Windows Server Failover cluster) of pacemaker, dat clusterknooppuntcommunicatie en schrijfvergrendeling verwerkt.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-shared-limitations](virtual-machines-disks-shared-limitations.md)]

## <a name="disk-sizes"></a>Schijfformaten

[!INCLUDE [virtual-machines-disks-shared-sizes](virtual-machines-disks-shared-sizes.md)]

## <a name="sample-workloads"></a>Voorbeeldworkloads

### <a name="windows"></a>Windows

De meeste Windows-gebaseerde clustering bouwen aan WSFC, die alle kerninfrastructuur voor clusterknooppuntcommunicatie verwerkt, zodat uw toepassingen kunnen profiteren van parallelle toegangspatronen. WSFC maakt zowel CSV- als niet-CSV-opties mogelijk, afhankelijk van uw versie van Windows Server. Zie voor meer informatie [een failovercluster maken](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster).

Enkele populaire toepassingen die worden uitgevoerd op WSFC zijn:

- SQL Server Failoverclusterinstanties (FCI)
- Scale-out bestandsserver (SoFS)
- Bestandsserver voor algemeen gebruik (IW-werkbelasting)
- Extern bureaublad-servergebruikersprofielschijf (RDS UPD)
- SAP ASCS/SCS

### <a name="linux"></a>Linux

Linux-clusters kunnen gebruikmaken van clustermanagers zoals [Pacemaker](https://wiki.clusterlabs.org/wiki/Pacemaker). Pacemaker bouwt voort op [Corosync,](http://corosync.github.io/corosync/)waardoor clustercommunicatie mogelijk is voor toepassingen die worden geïmplementeerd in zeer beschikbare omgevingen. Enkele veelvoorkomende geclusterde bestandssystemen zijn [ocfs2](https://oss.oracle.com/projects/ocfs2/) en [gfs2.](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/global_file_system_2/ch-overview-gfs2) U reserveringen en registraties manipuleren met behulp van hulpprogramma's zoals [fence_scsi](http://manpages.ubuntu.com/manpages/eoan/man8/fence_scsi.8.html) en [sg_persist.](https://linux.die.net/man/8/sg_persist)

## <a name="persistent-reservation-flow"></a>Permanente reserveringsstroom

In het volgende diagram wordt een geclusterde databasetoepassing met meerdere knooppunten weergegeven waarmee SCSI PR wordt gebruikt om failover van het ene knooppunt naar het andere mogelijk te maken.

![Twee knooppuntcluster. Een toepassing die op het cluster wordt uitgevoerd, verwerkt de toegang tot de schijf](media/virtual-machines-disks-shared-disks/shared-disk-updated-two-node-cluster-diagram.png)

De stroom is als volgt:

1. De geclusterde toepassing die wordt uitgevoerd op zowel Azure VM1 als VM2 registreert de intentie om de schijf te lezen of te schrijven.
1. De toepassingsinstantie op VM1 neemt vervolgens exclusieve reservering om naar de schijf te schrijven.
1. Deze reservering wordt afgedwongen op uw Azure-schijf en de database kan nu uitsluitend naar de schijf schrijven. Alle schrijfbewerkingen van de toepassingsinstantie op VM2 zullen niet slagen.
1. Als de toepassingsinstantie op VM1 uitvalt, kan de instantie op VM2 nu een databasefailover en overname van de schijf starten.
1. Deze reservering wordt nu afgedwongen op de Azure-schijf en de schijf accepteert geen schrijfbewerkingen van VM1 meer. Het accepteert alleen schrijft van VM2.
1. De geclusterde toepassing kan de databasefailover voltooien en aanvragen van VM2 weergeven.

Het volgende diagram illustreert een andere veelvoorkomende geclusterde werkbelasting die bestaat uit meerdere knooppunten die gegevens van de schijf lezen voor het uitvoeren van parallelle processen, zoals het trainen van machine learning-modellen.

![Vier knooppunt VM cluster, elk knooppunt registreert intentie om te schrijven, toepassing neemt exclusieve reservering om goed te behandelen schrijfresultaten](media/virtual-machines-disks-shared-disks/shared-disk-updated-machine-learning-trainer-model.png)

De stroom is als volgt:

1. De geclusterde toepassing die op alle VM's wordt uitgevoerd, registreert de intentie om de schijf te lezen of te schrijven.
1. De toepassingsinstantie op VM1 neemt een exclusieve reservering om naar de schijf te schrijven terwijl het openen van leest naar de schijf van andere VM's.
1. Deze reservering wordt afgedwongen op uw Azure-schijf.
1. Alle knooppunten in het cluster kunnen nu vanaf de schijf worden gelezen. Slechts één knooppunt schrijft resultaten terug naar de schijf, namens alle knooppunten in het cluster.