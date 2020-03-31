---
title: Bewaken van de azure site recovery process server
description: In dit artikel wordt beschreven hoe u de server voor azure-herstelproces van de site controleren die wordt gebruikt voor vm/fysieke serverherstel van vMware
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: 54c161c40c881d7626f79fc9bfe1ec1c160480ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257067"
---
# <a name="monitor-the-process-server"></a>De processerver controleren

In dit artikel wordt beschreven hoe u de [server voor het siteherstelproces](site-recovery-overview.md) controleren.

- De processerver wordt gebruikt wanneer u noodherstel van on-premises Vm's vmware en fysieke servers instelt op Azure.
- Standaard wordt de processerver uitgevoerd op de configuratieserver. Het is standaard geïnstalleerd wanneer u de configuratieserver implementeert.
- Optioneel u extra, scale-out processervers implementeren om grotere aantallen gerepliceerde machines en grotere volumes replicatieverkeer te schalen en te verwerken.

[Meer informatie](vmware-physical-azure-config-process-server-overview.md) over de rol en implementatie van processervers.

## <a name="monitoring-overview"></a>Bewakingsoverzicht

Aangezien de processerver zoveel rollen heeft, met name in gerepliceerde gegevenscaches, compressie en overdracht naar Azure, is het belangrijk om de status van de processerver voortdurend te controleren.

Er zijn een aantal situaties die vaak van invloed zijn op de prestaties van de processerver. Problemen die van invloed zijn op de prestaties hebben een trapsgewijs effect op de VM-status, waardoor uiteindelijk zowel de processerver als de gerepliceerde machines in een kritieke status worden geduwd. Situaties zijn onder andere:

- Grote aantallen VM's gebruiken een processerver, die de aanbevolen beperkingen nadert of overschrijdt.
- VM's die de processerver gebruiken, hebben een hoge churn rate.
- Netwerkdoorvoer tussen VM's en de processerver is niet voldoende om replicatiegegevens naar de processerver te uploaden.
- Netwerkdoorvoer tussen de processerver en Azure is niet voldoende om replicatiegegevens van de processerver naar Azure te uploaden.

Al deze problemen kunnen van invloed zijn op de doelstelling van het herstelpunt (RPO) van VM's. 

**Hoe komt dat?** Omdat het genereren van een herstelpunt voor een VM vereist dat alle schijven op de VM een gemeenschappelijk punt hebben. Als één schijf een hoge churn rate heeft, replicatie traag is of de processerver niet optimaal is, heeft dit invloed op hoe efficiënt herstelpunten worden gemaakt.

## <a name="monitor-proactively"></a>Proactief bewaken

Om problemen met de processerver te voorkomen, is het belangrijk om:

- Begrijp specifieke vereisten voor processervers met behulp van [capaciteits- en dimensioneringsrichtlijnen](site-recovery-plan-capacity-vmware.md#capacity-considerations)en zorg ervoor dat processervers worden geïmplementeerd en uitgevoerd volgens aanbevelingen.
- Controleer waarschuwingen en los problemen op wanneer deze zich voordoen, om processervers efficiënt te laten werken.


## <a name="process-server-alerts"></a>Serverwaarschuwingen verwerken

De processerver genereert een aantal statuswaarschuwingen, samengevat in de volgende tabel.

**Waarschuwingstype** | **Details**
--- | ---
![Goed][green] | Processerver is verbonden en gezond.
![Waarschuwing][yellow] | CPU-gebruik > 80% voor de laatste 15 minuten
![Waarschuwing][yellow] | Geheugengebruik > 80% voor de laatste 15 minuten
![Waarschuwing][yellow] | Cache map vrije ruimte < 30% voor de laatste 15 minuten
![Waarschuwing][yellow] | Site recovery controleert in- en uitgaande gegevens om de vijf minuten en schat dat gegevens in de processervercache niet binnen 30 minuten naar Azure kunnen worden geüpload.
![Waarschuwing][yellow] | Processerverservices worden de laatste 15 minuten niet uitgevoerd
![Kritiek][red] | CPU-gebruik > 95% voor de laatste 15 minuten
![Kritiek][red] | Geheugengebruik > 95% voor de laatste 15 minuten
![Kritiek][red] | Cache map vrije ruimte < 25% voor de laatste 15 minuten
![Kritiek][red] | Site Recovery controleert in- en uitgaande gegevens om de vijf minuten en schat dat gegevens in de processervercache niet binnen 45 minuten naar Azure kunnen worden geüpload.
![Kritiek][red] | Gedurende 15 minuten geen hartslag van de processerver.

![Tabeltoets](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> De algehele status van de processerver is gebaseerd op de slechtste gegenereerde waarschuwing.



## <a name="monitor-process-server-health"></a>De status van processerver controleren

U de status van uw processervers als volgt controleren: 

1. Als u de replicatiestatus en -status van een gerepliceerde machine en van de processerver wilt controleren, klikt u in vault > **gerepliceerde items**op de machine die u wilt controleren.
2. In **replicatiestatus**u de status vm-status controleren. Klik op de status om in te zoomen op foutgegevens.

    ![Serverstatus verwerken in VM-dashboard](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. In **Process Server Health**u de status van de processerver controleren. Inzoomen op details.

    ![Servergegevens verwerken in VM-dashboard](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. De status kan ook worden gecontroleerd met behulp van de grafische weergave op de VM-pagina.
    - Een scale-out processerver wordt in het oranje gemarkeerd als er waarschuwingen aan zijn gekoppeld en rood als er kritieke problemen zijn. 
    - Als de processerver wordt uitgevoerd in de standaardimplementatie op de configuratieserver, wordt de configuratieserver dienovereenkomstig gemarkeerd.
    - Als u wilt inzoomen, klikt u op de configuratieserver of processerver. Let op eventuele problemen en eventuele herstelaanbevelingen.

U ook processervers in de kluis controleren onder **Site Recovery Infrastructure**. Klik **in De infrastructuur voor siteherstel beheren**op **Configuratieservers**. Selecteer de configuratieserver die aan de processerver is gekoppeld en zoom in op processervergegevens.


## <a name="next-steps"></a>Volgende stappen

- Als u problemen hebt met processervers, volgt u onze [richtlijnen voor het oplossen van problemen](vmware-physical-azure-troubleshoot-process-server.md)
- Als u meer hulp nodig hebt, plaatst u uw vraag in het [Azure Site Recovery-forum.](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr) 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
