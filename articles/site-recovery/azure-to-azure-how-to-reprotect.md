---
title: Azure VM's opnieuw beveiligen naar het primaire gebied met Azure Site Recovery | Microsoft Documenten
description: Beschrijft hoe u Azure VM's opnieuw beschermen na een failover, het secundaire naar primaire gebied, met Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 73747b8331054cdc3bfa1f4073ccf2cdb62ab326
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283239"
---
# <a name="reprotect-failed-over-azure-vms-to-the-primary-region"></a>Opnieuw beveiligen is mislukt ten opzichte van Azure VM's naar het primaire gebied

Wanneer u [niet meer dan](site-recovery-failover.md) Azure VM's van de ene regio naar de andere met Azure Site [Recovery](site-recovery-overview.md), de VM's opstarten in de secundaire regio, in een **onbeveiligde** status. Als u de VM's naar het primaire gebied wilt terugsturen naar het primaire gebied, doet u de volgende taken:

1. Bescherm de VM's in het secundaire gebied opnieuw, zodat ze beginnen te repliceren naar het primaire gebied.
1. Nadat de beveiliging is voltooid en de VM's zijn gerepliceerd, u mislukken van het secundaire naar primaire gebied.

## <a name="prerequisites"></a>Vereisten

- De VM-failover van het primaire naar secundaire gebied moet worden vastgelegd.
- De primaire doelsite moet beschikbaar zijn en u moet toegang hebben tot bronnen in die regio of deze kunnen maken.

## <a name="reprotect-a-vm"></a>Een virtuele machine opnieuw beveiligen

1. Klik in > **gevault-gerepliceerde items**met de rechtermuisknop op de mislukte over-vm en selecteer **Opnieuw beveiligen**. **Vault** De herbeschermingsrichting moet van secundair naar primair worden weergegeven.

   ![Opnieuw beveiligen](./media/site-recovery-how-to-reprotect-azure-to-azure/reprotect.png)

1. Bekijk de resourcegroep, netwerk-, opslag- en beschikbaarheidssets. Klik vervolgens op **OK**. Als er resources zijn gemarkeerd als nieuw, worden ze gemaakt als onderdeel van het herbeschermingsproces.
1. De taak voor herbescherming zaait de doelsite met de nieuwste gegevens. Nadat de taak is voltooid, vindt deltareplicatie plaats. Vervolgens u niet meer naar de primaire site gaan. U het opslagaccount of het netwerk selecteren dat u wilt gebruiken tijdens de reprotect, met behulp van de optie Aanpassen.

   ![Optie Aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customize.png)

### <a name="customize-reprotect-settings"></a>Instellingen voor opnieuw beveiligen aanpassen

U de volgende eigenschappen van de doel-VM aanpassen tijdens herbeveiliging.

![Aanpassen](./media/site-recovery-how-to-reprotect-azure-to-azure/customizeblade.png)

|Eigenschap |Opmerkingen  |
|---------|---------|
|Doelgroepgroep | Wijzig de doelgroepwaarin de VM is gemaakt. Als onderdeel van herbescherming wordt de doel-VM verwijderd. U een nieuwe resourcegroep kiezen waaronder u de VM na failover wilt maken. |
|Virtueel netwerk targeten | Het doelnetwerk kan niet worden gewijzigd tijdens de taak opnieuw beveiligen. Als u het netwerk wilt wijzigen, moet u de netwerktoewijzing opnieuw uitvoeren. |
|Doelopslag (Secundaire VM maakt geen beheerde schijven) | U het opslagaccount wijzigen dat de VM gebruikt na een failover. |
|Replica beheerde schijven (Secundaire VM maakt gebruik van beheerde schijven) | Siteherstel maakt replicabeheerde schijven in het primaire gebied om de beheerde schijven van de secundaire virtuele machine te spiegelen. |
|Cacheopslag | U een cacheopslagaccount opgeven dat tijdens de replicatie moet worden gebruikt. Standaard wordt een nieuw cacheopslagaccount gemaakt als dit niet bestaat. |
|Beschikbaarheidsset | Als de VM in het secundaire gebied deel uitmaakt van een beschikbaarheidsset, u een beschikbaarheidsset kiezen voor de doel-vm in de primaire regio. Siteherstel probeert standaard de bestaande beschikbaarheidsset in het primaire gebied te vinden en deze te gebruiken. Tijdens het aanpassen u een nieuwe beschikbaarheidsset opgeven. |

### <a name="what-happens-during-reprotection"></a>Wat gebeurt er tijdens de herbescherming?

Standaard treedt het volgende op:

1. Er wordt een cacheopslagaccount gemaakt in het gebied waar de mislukte vm wordt uitgevoerd.
1. Als het doelopslagaccount (het oorspronkelijke opslagaccount in het primaire gebied) niet bestaat, wordt een nieuw account gemaakt. De toegewezen opslagaccountnaam is de naam van het opslagaccount `asr`dat wordt gebruikt door de secundaire virtuele machine, die is vastgelegd met .
1. Als uw vm beheerde schijven gebruikt, worden replicabeheerde schijven gemaakt in het primaire gebied om de gegevens op te slaan die zijn gerepliceerd vanaf de schijven van de secundaire virtuele machine.
1. Als de doelbeschikbaarheidsset niet bestaat, wordt indien nodig een nieuwe set gemaakt als onderdeel van de taak reprotect. Als u de instellingen voor herbeveiliging hebt aangepast, wordt de geselecteerde set gebruikt.

Wanneer u een taak voor herbescherming activeert en de doel-vm bestaat, treedt het volgende op:

1. De VM aan de doelzijde is uitgeschakeld als deze wordt uitgevoerd.
1. Als de VM beheerde schijven gebruikt, wordt een `-ASRReplica` kopie van de oorspronkelijke schijf gemaakt met een achtervoegsel. De oorspronkelijke schijven worden verwijderd. De `-ASRReplica` kopieën worden gebruikt voor replicatie.
1. Als de VM onbeheerde schijven gebruikt, worden de gegevensschijven van de doel-VM losgemaakt en gebruikt voor replicatie. Een kopie van de OS-schijf wordt gemaakt en gekoppeld aan de VM. De oorspronkelijke OS-schijf wordt losgemaakt en gebruikt voor replicatie.
1. Alleen wijzigingen tussen de bronschijf en de doelschijf worden gesynchroniseerd. De differentiëlen worden berekend door zowel de schijven te vergelijken als vervolgens over te dragen. Bekijk hieronder de geschatte tijd om de herbescherming te voltooien.
1. Nadat de synchronisatie is voltooid, wordt de deltareplicatie gestart en wordt een herstelpunt gemaakt in overeenstemming met het replicatiebeleid.

Wanneer u een taak voor herbescherming activeert en de doel-VM en -schijven niet bestaan, treedt het volgende op:

1. Als de VM beheerde schijven gebruikt, `-ASRReplica` worden replicaschijven gemaakt met achtervoegsel. De `-ASRReplica` kopieën worden gebruikt voor replicatie.
1. Als de VM onbeheerde schijven gebruikt, worden replicaschijven gemaakt in het doelopslagaccount.
1. De volledige schijven worden gekopieerd van de mislukte bovenregio naar het nieuwe doelgebied.
1. Nadat de synchronisatie is voltooid, wordt de deltareplicatie gestart en wordt een herstelpunt gemaakt in overeenstemming met het replicatiebeleid.

#### <a name="estimated-time-to-do-the-reprotection"></a>Geschatte tijd om de herbescherming te doen

In de meeste gevallen repliceert Azure Site Recovery de volledige gegevens niet naar het brongebied.
De volgende voorwaarden bepalen hoeveel gegevens worden gerepliceerd:

1. Als de bron-VM-gegevens om de een of andere reden worden verwijderd, beschadigd of ontoegankelijk, zoals een wijziging/verwijdering van de brongroep, gebeurt tijdens de herbescherming een volledige eerste replicatie omdat er geen gegevens beschikbaar zijn over het brongebied om te gebruiken.
1. Als de bron-VM-gegevens toegankelijk zijn, worden alleen differentiëlen berekend door zowel de schijven te vergelijken als vervolgens over te dragen. Bekijk de onderstaande tabel om de geschatte tijd te krijgen.

|Voorbeeldsituatie | Tijd die nodig is om opnieuw te beschermen |
|---|---|
|Bronregio heeft 1 VM met standaardschijf van 1 TB.<br/>Er worden slechts gegevens van 127 GB gebruikt en de rest van de schijf is leeg.<br/>Het schijftype is standaard met een doorvoersnelheid van 60 MiB/S.<br/>Geen gegevens veranderen na failover.| Geschatte tijd: 45 minuten – 1,5 uur.<br/>Tijdens de herbescherming zal Site Recovery de checksum van alle gegevens die 127 GB/ 45 GB in beslag nemen, ongeveer 45 minuten, vullen.<br/>Enige overheadtijd is vereist voor siteherstel om automatisch te schalen, ongeveer 20-30 minuten.<br/>Geen Aanklachten van Uitgang. |
|Bronregio heeft 1 VM met standaardschijf van 1 TB.<br/>Er worden slechts gegevens van 127 GB gebruikt en de rest van de schijf is leeg.<br/>Het schijftype is standaard met een doorvoersnelheid van 60 MiB/S.<br/>gegevens van 45 GB worden gewijzigd na een failover.| Geschatte tijd: 1 uur – 2 uur.<br/>Tijdens de herbescherming zal Site Recovery de checksum van alle gegevens die 127 GB/ 45 GB in beslag nemen, ongeveer 45 minuten, vullen.<br/>Overdrachtstijd om wijzigingen van 45 GB toe te passen, dat is 45 GB/ 45 MBps, ongeveer 17 minuten.<br/>Uitgaande kosten zou zijn voor 45 GB gegevens wijzigingen, niet voor de checksum. |

Wanneer de VM opnieuw wordt beveiligd nadat deze niet is teruggezet naar het primaire gebied (d.w.z. als de VM opnieuw wordt beveiligd van primaire regio naar DR-regio), worden de doel-VM en bijbehorende NIC(s) verwijderd.

Wanneer de VM wordt opnieuw beveiligd van het DR-gebied naar het primaire gebied, verwijderen we de voormalige primaire VM en bijbehorende NIC(s) niet.

## <a name="next-steps"></a>Volgende stappen

Nadat de VM is beveiligd, u een failover starten. De failover schakelt de VM in de secundaire regio uit en maakt en start de VM in de primaire regio, met korte downtime tijdens dit proces. We raden u aan een geschikt tijdstip voor dit proces te kiezen en een testfailover uit te voeren voordat u een volledige failover naar de primaire site initieert. [Meer informatie](site-recovery-failover.md) over failover van Azure Site Recovery.
