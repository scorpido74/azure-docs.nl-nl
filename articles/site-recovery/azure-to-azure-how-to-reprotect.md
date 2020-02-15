---
title: Azure-Vm's opnieuw beveiligen naar de primaire regio met Azure Site Recovery | Microsoft Docs
description: Hierin wordt beschreven hoe u Azure-Vm's na een failover opnieuw beveiligt, de secundaire naar de primaire regio met behulp van Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 1f1547d1fef797f8dc4c86c94c405d26c4ef9cec
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77209253"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Failover van virtuele Azure-machines naar de primaire regio opnieuw beveiligen

Wanneer u virtuele Azure-machines van de ene regio naar de andere gebruikt via [Azure site Recovery](site-recovery-overview.md), wordt de virtuele machine in de secundaire regio opgestart, in een [niet](site-recovery-failover.md) - **beveiligde** status. Als u een failback wilt uitvoeren voor de virtuele machines naar de primaire regio, moet u het volgende doen:

- Beveilig de virtuele machines in de secundaire regio opnieuw zodat ze repliceren naar de primaire regio.
- Nadat de beveiliging is voltooid en de virtuele machines zijn gerepliceerd, kunt u deze failover uitvoeren van de secundaire naar de primaire regio.

## <a name="prerequisites"></a>Vereisten
1. De VM-failover van de primaire naar de secundaire regio moet worden doorgevoerd.
2. De primaire doel site moet beschikbaar zijn en u moet toegang hebben tot of resources kunnen maken in die regio.

## <a name="reprotect-a-vm"></a>Een virtuele machine opnieuw beveiligen

1. Klik in **kluis** > **gerepliceerde items**met de rechter muisknop op de virtuele machine waarvoor een failover is uitgevoerd en selecteer **opnieuw beveiligen**. De richting voor opnieuw beveiligen moet van secundair naar primair worden weer gegeven.

   ![Opnieuw beveiligen](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

2. Bekijk de resource groep-, netwerk-, opslag-en beschikbaarheids sets. Klik vervolgens op **OK**. Als er resources zijn gemarkeerd als nieuw, worden ze gemaakt als onderdeel van het proces voor opnieuw beveiligen.
3. De taak voor opnieuw beveiligen zaait de doel site met de meest recente gegevens. Nadat dit is voltooid, vindt er Delta replicatie plaats. Daarna kunt u een failover uitvoeren naar de primaire site. Met de optie aanpassen kunt u het opslag account selecteren of het netwerk dat u wilt gebruiken tijdens het opnieuw beveiligen.

   ![Optie aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Instellingen voor opnieuw beveiligen aanpassen

U kunt de volgende eigenschappen van de doel-VM aanpassen tijdens het opnieuw beveiligen.

![Aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Eigenschap |Opmerkingen  |
|---------|---------|
|Doel resource groep     | Wijzig de doel resource groep waarin de virtuele machine is gemaakt. Als onderdeel van de herbeveiliging wordt de doel-VM verwijderd. U kunt een nieuwe resource groep kiezen voor het maken van de virtuele machine na een failover.        |
|Virtueel netwerk van doel     | Het doelnet werk kan niet worden gewijzigd tijdens de taak beveiliging opnieuw beveiligen. Als u het netwerk wilt wijzigen, voert u de netwerk toewijzing opnieuw uit.         |
|Doel opslag (secundaire virtuele machine maakt geen gebruik van beheerde schijven)     | U kunt het opslag account dat door de virtuele machine wordt gebruikt, wijzigen na een failover.         |
|Beheerde replica schijven (secundaire virtuele machine maakt gebruik van beheerde schijven)    | Site Recovery maakt met replica beheerde schijven in de primaire regio om de beheerde schijven van de secundaire virtuele machine te spie gelen.         |
|Cache opslag     | U kunt een cache-opslag account opgeven dat moet worden gebruikt tijdens de replicatie. Standaard wordt er een nieuwe cache opslag account gemaakt, als deze nog niet bestaat.         |
|Beschikbaarheidsset     |Als de virtuele machine in de secundaire regio deel uitmaakt van een beschikbaarheidsset, kunt u een beschikbaarheidsset kiezen voor de doel-VM in de primaire regio. Site Recovery zoekt standaard naar de bestaande beschikbaarheidsset in de primaire regio en gebruikt deze. Tijdens het aanpassen kunt u een nieuwe beschikbaarheidsset opgeven.         |

### <a name="what-happens-during-reprotection"></a>Wat gebeurt er tijdens het opnieuw beveiligen?

Standaard gebeurt het volgende:

1. Er wordt een cache-opslag account gemaakt in de regio waarin de virtuele machine waarvoor een failover is uitgevoerd, is gestart.
2. Als het doel-opslag account (het oorspronkelijke opslag account in de primaire regio) niet bestaat, wordt er een nieuwe gemaakt. De toegewezen opslag accountnaam is de naam van het opslag account dat wordt gebruikt door de secundaire virtuele machine, met het achtervoegsel ' ASR '.
3. Als uw virtuele machine beheerde schijven gebruikt, worden beheerde replica schijven in de primaire regio gemaakt voor het opslaan van de gegevens die worden gerepliceerd vanaf de schijven van de secundaire virtuele machine.
4. Als de beschik baarheid van de doel groep niet bestaat, wordt er een nieuwe gemaakt als onderdeel van de taak voor opnieuw beveiligen als dat nodig is. Als u de instellingen voor opnieuw beveiligen hebt aangepast, wordt de geselecteerde set gebruikt.

Wanneer u een taak voor opnieuw beveiligen start en de doel-VM bestaat, gebeurt het volgende:

1. De doel-VM is uitgeschakeld als deze wordt uitgevoerd.
2. Als de virtuele machine beheerde schijven gebruikt, wordt er een kopie van de oorspronkelijke schijven gemaakt met het achtervoegsel '-ASRReplica '. De oorspronkelijke schijven worden verwijderd. De '-ASRReplica-kopieën worden gebruikt voor replicatie.
3. Als de virtuele machine gebruikmaakt van niet-beheerde schijven, worden de gegevens schijven van de doel-VM losgekoppeld en gebruikt voor replicatie. Er wordt een kopie van de besturingssysteem schijf gemaakt en aangesloten op de VM. De oorspronkelijke besturingssysteem schijf wordt losgekoppeld en gebruikt voor replicatie.
4. Alleen wijzigingen tussen de bron schijf en de doel schijf worden gesynchroniseerd. De verschillen worden berekend door de schijven te vergelijken en vervolgens te worden overgedragen. Hieronder ziet u de geschatte tijd voor het volt ooien van de herbeveiliging.
5. Nadat de synchronisatie is voltooid, wordt de Delta replicatie gestart en wordt er een herstel punt gemaakt in overeenstemming met het replicatie beleid.

Wanneer u een taak voor opnieuw beveiligen start en de doel-VM en schijven niet bestaan, gebeurt het volgende:
1. Als de virtuele machine gebruikmaakt van beheerde schijven, worden replica schijven gemaakt met het achtervoegsel '-ASRReplica '. De '-ASRReplica-kopieën worden gebruikt voor replicatie.
2. Als de virtuele machine gebruikmaakt van niet-beheerde schijven, worden er replica schijven gemaakt in het doel-opslag account.
3. De gehele schijven worden gekopieerd van de regio waarvoor een failover is uitgevoerd naar de nieuwe doel regio.
4. Nadat de synchronisatie is voltooid, wordt de Delta replicatie gestart en wordt er een herstel punt gemaakt in overeenstemming met het replicatie beleid.

#### <a name="estimated-time-to-do-the-reprotection"></a>Geschatte tijd voor het uitvoeren van de herbeveiliging 

In de meeste gevallen repliceert Azure Site Recovery de volledige gegevens niet naar de bron regio. Hieronder ziet u de voor waarden die bepalen hoeveel gegevens er zouden worden gerepliceerd:

1.  Als de gegevens van de bron-VM worden verwijderd, beschadigd of ontoegankelijk zijn vanwege een bepaalde reden, zoals een wijziging/verwijdering van een resource groep, treedt er tijdens het opnieuw beveiligen een volledig Site Recovery op omdat er geen gegevens beschikbaar zijn in de bron regio die moet worden gebruikt.
2.  Als de gegevens van de bron-VM toegankelijk zijn, worden alleen verschillen berekend door de schijven te vergelijken en vervolgens te worden overgedragen. Controleer de onderstaande tabel om de geschatte tijd op te halen.

|Voorbeeld situatie | Benodigde tijd voor opnieuw beveiligen |
|---|---|
|Bron regio heeft 1 VM met 1 TB standaard schijf<br/>-Slechts 127 GB gegevens worden gebruikt en de rest van de schijf is leeg<br/>-Schijf type is standaard met 60 MiB/S door Voer<br/>-Geen gegevens gewijzigd na failover| Geschatte tijd: 45 minuten: 1,5 uur<br/> -Tijdens opnieuw beveiligen vult Site Recovery de controlesom in van alle gegevens die 127 GB/45 MB ~ 45 minuten zullen duren<br/>-Er is enige overhead tijd nodig om Site Recovery automatisch te schalen: ong. 20-30 minuten<br/>-Geen uitgangs kosten |
|Bron regio heeft 1 VM met 1 TB standaard schijf<br/>-Alleen 127 GB gegevens worden gebruikt en de rest van de schijf is leeg<br/>-Schijf type is standaard met 60 MiB/S door Voer<br/>-45 GB gegevens wijzigingen na failover| Geschatte tijd: 1 uur – 2 uur<br/>-Tijdens opnieuw beveiligen vult Site Recovery de controlesom in van alle gegevens die 127 GB/45 MB ~ 45 minuten zullen duren<br/>-Overdrachts tijd voor het Toep assen van wijzigingen van 45 GB van 45 GB/45 MBps ~ 17 minuten<br/>-De kosten voor uitgaand verkeer zijn van 45 GB gegevens wijzigingen, niet voor de controlesom |
 
## <a name="next-steps"></a>Volgende stappen

Nadat de virtuele machine is beveiligd, kunt u een failover initiëren. Met de failover wordt de virtuele machine in de secundaire regio afgesloten en wordt de virtuele machine in de primaire regio gemaakt en opgestart, met een kleine uitval tijd tijdens dit proces. U kunt het beste een geschikte tijd kiezen voor dit proces en u voert een testfailover uit voordat u een volledige failover naar de primaire site initieert. [Meer informatie](site-recovery-failover.md) over Azure site Recovery failover.
