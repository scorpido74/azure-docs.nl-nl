---
title: Disaster recovery principes en voorbereiding op SAP HANA op Azure (Large Instances) | Microsoft Documenten
description: Disaster recovery principes en voorbereiding op SAP HANA op Azure (Large Instances)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33d52f871de75a7f7d34016b040e44d6f1623fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101264"
---
# <a name="disaster-recovery-principles"></a>Principes voor noodherstel

HANA Large Instances bieden een disaster recovery-functionaliteit tussen HANA Large Instance-stempels in verschillende Azure-regio's. Als u bijvoorbeeld HANA Large Instance-eenheden implementeert in de regio US West van Azure, u de HANA Large Instance-eenheden in de regio VS-Oost gebruiken als noodhersteleenheden. Zoals eerder vermeld, wordt disaster recovery niet automatisch geconfigureerd, omdat u hiervoor moet betalen voor een andere HANA Large Instance-eenheid in de DR-regio. De noodherstel-installatie werkt voor zowel scale-up als scale-out setups. 

In de tot nu toe geïmplementeerde scenario's gebruiken klanten de eenheid in de DR-regio om niet-productiesystemen uit te voeren die een geïnstalleerd HANA-exemplaar gebruiken. De HANA Large Instance-eenheid moet dezelfde SKU hebben als de SKU die voor productiedoeleinden wordt gebruikt. In de volgende afbeelding ziet u hoe de schijfconfiguratie tussen de servereenheid in het Azure-productiegebied en het gebied voor noodherstel eruit ziet:

![DR-installatieconfiguratie vanuit schijfoogpunt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Zoals in deze overzichtsafbeelding wordt weergegeven, moet u vervolgens een tweede set schijfvolumes bestellen. De doelschijfvolumes zijn even groot als de productievolumes voor de productieinstantie in de noodhersteleenheden. Deze schijfvolumes zijn gekoppeld aan de HANA Large Instance-servereenheid in de noodherstelsite. De volgende volumes worden gerepliceerd van het productiegebied naar de DR-site:

- /hana/data
- /hana/logbackups 
- /hana/shared (inclusief /usr/sap)

Het /hana/log-volume wordt niet gerepliceerd omdat het SAP HANA-transactielogboek niet nodig is op de manier waarop het herstel van die volumes wordt uitgevoerd. 

De basis van de aangeboden disaster recovery-functionaliteit is de opslagreplicatiefunctionaliteit die wordt aangeboden door de HANA Large Instance-infrastructuur. De functionaliteit die wordt gebruikt aan de opslagzijde is geen constante stroom van wijzigingen die op een asynchrone manier worden gerepliceerd als er wijzigingen in het opslagvolume plaatsvinden. In plaats daarvan is het een mechanisme dat zich baseert op het feit dat snapshots van deze volumes regelmatig worden gemaakt. De delta tussen een reeds gerepliceerde momentopname en een nieuwe momentopname die nog niet is gerepliceerd, wordt vervolgens overgebracht naar de rampherstelsite naar doelschijfvolumes.  Deze momentopnamen worden opgeslagen op de volumes en, als er een failover voor noodherstel is, moeten deze volumes worden hersteld.  

De eerste overdracht van de volledige gegevens van het volume moet worden voordat de hoeveelheid gegevens kleiner wordt dan de delta's tussen momentopnamen. Als gevolg hiervan bevatten de volumes in de DR-site elk van de volumemomentopnamen die op de productielocatie worden uitgevoerd. Uiteindelijk u dat DR-systeem gebruiken om naar een eerdere status te gaan om verloren gegevens te herstellen, zonder het productiesysteem terug te draaien.

Als er een MCOD-implementatie is met meerdere onafhankelijke SAP HANA-exemplaren op één HANA Large Instance-eenheid, wordt verwacht dat alle SAP HANA-exemplaren opslag worden gerepliceerd naar de DR-kant.

In gevallen waarin u HANA-systeemreplicatie gebruikt als functionaliteit met hoge beschikbaarheid in uw productiesite en op opslag gebaseerde replicatie gebruikt voor de DR-site, worden de volumes van beide knooppunten van de primaire site naar de DR-instantie gerepliceerd. U moet extra opslagruimte (dezelfde grootte als primaire knooppunt) op dr-site aanschaffen om replicatie van zowel primaire als secundaire naar de DR mogelijk te maken. 



>[!NOTE]
>De co-replicatiefunctionaliteit voor opslagvan HANA Large Instance is het spiegelen en repliceren van opslagmomentopnamen. Als u geen opslagmomentopnamen uitvoert zoals geïntroduceerd in het gedeelte Back-up en herstel van dit artikel, kan er geen replicatie plaatsvinden naar de site voor noodherstel. Opslagmomentopnameuitvoering is een vereiste voor opslagreplicatie naar de noodherstelsite.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>Voorbereiding van het scenario voor noodherstel
In dit scenario wordt een productiesysteem uitgevoerd op HANA Large Instances in het Azure-productiegebied. Voor de volgende stappen gaan we ervan uit dat de SID van dat HANA-systeem 'PRD' is en dat u een niet-productiesysteem hebt dat draait op HANA Large Instances in de DR Azure-regio. Voor de laatste, laten we aannemen dat de SID is "TST." De volgende afbeelding toont deze configuratie:

![Start van DR-installatie](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Als de serverinstantie nog niet is besteld met de extra opslagvolumeset, koppelt SAP HANA op Azure Service Management de extra set volumes als doel voor de productiereplica aan de HANA Large Instance-eenheid waarop u de TST uitvoert HANA bijvoorbeeld. Voor dat doel, moet u de SID van uw productie HANA instantie. Nadat SAP HANA op Azure Service Management de bevestiging van deze volumes bevestigt, moet u deze volumes aan de HANA Large Instance-eenheid bevestigen.

![DR setup volgende stap](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

De volgende stap is dat u het tweede SAP HANA-exemplaar installeert op de HANA Large Instance-eenheid in de DR Azure-regio, waar u het exemplaar TST HANA uitvoert. De nieuw geïnstalleerde SAP HANA exemplaar moet dezelfde SID hebben. De gebruikers die zijn gemaakt, moeten dezelfde UID- en groeps-id hebben als de productie-instantie. Lees [Back-up en herstel](hana-backup-restore.md) voor meer informatie. Als de installatie is geslaagd, moet u:

- Voer stap 2 uit van de voorbereiding van de opslagmomentopname die is beschreven in [Back-upmaken en herstellen](hana-backup-restore.md).
- Maak een openbare sleutel voor de DR-eenheid van hana-eenheid voor grote instanties als u dit nog niet hebt gedaan. Zie stap 3 van de voorbereiding van de opslagmomentopname die wordt beschreven in [Back-upmaken en herstellen](hana-backup-restore.md).
- Onderhoud de *HANABackupCustomerDetails.txt* met het nieuwe HANA-exemplaar en test of connectiviteit in opslag correct werkt.  
- Stop de nieuw geïnstalleerde SAP HANA-instantie op de HANA Large Instance-eenheid in de DR Azure-regio.
- Monteer deze PRD-volumes en neem contact op met SAP HANA voor Azure Service Management. De volumes kunnen niet aan het apparaat worden vastgemonteerd omdat ze niet toegankelijk zijn terwijl ze fungeren als opslagreplicatiedoel.  

![DR-installatiestap voordat replicatie wordt ingesteld](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Het operations-team stelt de replicatierelatie vast tussen de PRD-volumes in het productie-Azure-gebied en de PRD-volumes in de DR Azure-regio.

>[!IMPORTANT]
>Het /hana/log-volume wordt niet gerepliceerd omdat het niet nodig is om de gerepliceerde SAP HANA-database te herstellen naar een consistente status in de rampherstelsite.

Stel vervolgens het back-upschema voor opslagmomentopnamen in of pas deze aan om bij uw RTO en RPO te komen in de rampcase. Als u de doelstelling herstelpunt wilt minimaliseren, stelt u de volgende replicatieintervallen in de HANA-service voor grote instanties in:
- Voor de volumes waarop de gecombineerde momentopname betrekking heeft (momentopnametype **hana),** stel u in om elke 15 minuten te repliceren naar de equivalente opslagvolumedoelen in de rampherstelsite.
- Voor het back-upvolume van het transactielogboek **(snapshottypelogboeken)** stel u in om elke 3 minuten te repliceren naar de equivalente opslagvolumedoelen in de noodherstelsite.

Als u de doelstelling voor herstelpunten wilt minimaliseren, stelt u het volgende in:
- Voer elke 30 minuten tot 1 uur een momentopname van **hana-type** opslag uit (zie 'Stap 7: Momentopnamen uitvoeren').
- Voer elke 5 minuten SAP HANA-transactielogboekbacks uit.
- Voer elke 5-15 minuten een momentopname van **het logboektype** opslag uit. Met deze intervalperiode bereikt u een RPO van ongeveer 15-25 minuten.

Met deze instelling kunnen de volgorde van transactielogboekback-ups, opslagmomentopnamen en de replicatie van het back-upvolume van het HANA-transactielogboek en /hana/-gegevens en /hana/shared (inclusief /usr/sap) eruit zien als de gegevens in deze afbeelding:

 ![Relatie tussen een back-upmomentopname voor een transactielogboek en een snap-spiegel op een tijdas](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

Om een nog betere RPO in de noodherstelcase te bereiken, u de HANA-transactielogboekback-ups van SAP HANA op Azure (Large Instances) kopiëren naar de andere Azure-regio. Voer de volgende stappen uit om deze verdere RPO-reductie te bereiken:

1. Maak zo vaak mogelijk een back-up van het HANA-transactielogboek naar /hana/logbackups.
1. Gebruik rsync om de back-ups van het transactielogboek te kopiëren naar de virtuele Azure-machines met share-host van NFS. De VM's bevinden zich in virtuele Azure-netwerken in het Azure-productiegebied en in de DR-regio's. U moet beide virtuele Azure-netwerken verbinden met het circuit dat de productie HANA Large Instances met Azure verbindt. Bekijk de afbeeldingen in de [sectie Netwerkoverwegingen voor herstel na noodgevallen met HANA Large Instances.](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) 
1. Bewaar de back-ups van het transactielogboek in de regio in de VM die is gekoppeld aan de geëxporteerde NFS-opslag.
1. In een ramp failover geval, vul de transactie log back-ups die u vindt op de / hana / logbackups volume met meer recent genomen transactie log back-ups op de NFS aandeel in de ramp recovery site. 
1. Start een back-up van een transactielogboek om te herstellen naar de nieuwste back-up die mogelijk wordt opgeslagen in de DR-regio.

Wanneer HANA Large Instance-bewerkingen de replicatierelatie-instelling bevestigen en u de back-ups van de opslagmomentopname voor uitvoeren start, wordt de gegevensreplicatie gestart.

![DR-installatiestap voordat replicatie wordt ingesteld](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

Naarmate de replicatie vordert, worden de momentopnamen op de PRD-volumes in de DR Azure-regio's niet hersteld. Ze worden alleen opgeslagen. Als de volumes in een dergelijke status zijn gemonteerd, vertegenwoordigen ze de status waarin u deze volumes hebt losgeontkoppeld nadat de PRD SAP HANA-instantie in de servereenheid in de DR Azure-regio is geïnstalleerd. Ze vertegenwoordigen ook de opslagback-ups die nog niet zijn hersteld.

Als er een failover is, u er ook voor kiezen om te herstellen naar een oudere opslagmomentopname in plaats van de nieuwste opslagmomentopname.

## <a name="next-steps"></a>Volgende stappen

- Verwijzen [Naar de failoverprocedure na noodgevallen](hana-failover-procedure.md).
