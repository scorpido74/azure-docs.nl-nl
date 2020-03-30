---
title: Failback tijdens herstel na noodgevallen met Azure Site Recovery | Microsoft Documenten
description: In dit artikel vindt u een overzicht van verschillende typen failback en waarschuwingen die moeten worden overwogen, terwijl deze niet on-premises worden uitgevoerd tijdens herstel tijdens noodherstel met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281832"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Failback van virtuele VMware-machines na herstel na noodgeval naar Azure

Nadat u niet meer naar Azure bent gegaan als onderdeel van uw herstelproces voor noodgevallen, u niet meer naar uw on-premises site gaan. Er zijn twee verschillende typen failback die mogelijk zijn met Azure Site Recovery: 

- Terug naar de oorspronkelijke locatie 
- Terug naar een alternatieve locatie mislukt

Als u faalde ten opzichte van een virtuele VMware-machine, u niet terug naar dezelfde on-premises virtuele machine als deze nog bestaat. In dit scenario worden alleen de wijzigingen teruggerepliceerd. Dit scenario staat bekend als **oorspronkelijke locatieherstel**. Als de on-premises virtuele machine niet bestaat, is het scenario een **alternatieve locatieherstel**.

> [!NOTE]
> U alleen niet terug naar de oorspronkelijke vCenter- en configuratieserver. U geen nieuwe configuratieserver implementeren en deze niet meer gebruiken. U ook geen nieuw vCenter toevoegen aan de bestaande configuratieserver en failback in het nieuwe vCenter.

## <a name="original-location-recovery-olr"></a>Herstel van oorspronkelijke locatie (OLR)
Als u ervoor kiest om terug te keren naar de oorspronkelijke virtuele machine, moet aan de volgende voorwaarden worden voldaan:

* Als de virtuele machine wordt beheerd door een vCenter-server, moet de ESX-host van het hoofddoel toegang hebben tot de datastore van de virtuele machine.
* Als de virtuele machine zich op een ESX-host bevindt, maar niet wordt beheerd door vCenter, moet de harde schijf van de virtuele machine zich in een gegevensarchief bevindt waartoe de host van het hoofddoel toegang heeft.
* Als uw virtuele machine zich op een ESX-host bevindt en geen vCenter gebruikt, moet u de detectie van de ESX-host van het hoofddoel voltooien voordat u opnieuw beschermt. Dit geldt ook als u fysieke servers niet terughaalt.
* U niet terug naar een virtueel opslaggebiednetwerk (vSAN) of een schijf die is gebaseerd op onbewerkte apparaattoewijzing (RDM) als de schijven al bestaan en zijn aangesloten op de on-premises virtuele machine.

> [!IMPORTANT]
> Het is belangrijk om disk.enableUUID= TRUE in te schakelen, zodat tijdens failback de Azure Site Recovery-service de oorspronkelijke VMDK kan identificeren op de virtuele machine waarop de wijzigingen in behandeling worden geschreven. Als deze waarde niet is ingesteld als WAAR, probeert de service de bijbehorende on-premises VMDK op basis van de beste inspanning te identificeren. Als de juiste VMDK niet wordt gevonden, wordt een extra schijf gemaakt en worden de gegevens daarop geschreven.

## <a name="alternate-location-recovery-alr"></a>Alternatieve locatieherstel (ALR)
Als de on-premises virtuele machine niet bestaat voordat de virtuele machine opnieuw wordt beschermd, wordt het scenario een alternatieve locatieherstel genoemd. Met de reprotect-workflow wordt de on-premises virtuele machine opnieuw gemaakt. Dit zal ook leiden tot een volledige gegevens te downloaden.

* Wanneer u niet terugkeert naar een alternatieve locatie, wordt de virtuele machine hersteld naar dezelfde ESX-host waarop de hoofddoelserver is geïmplementeerd. Het gegevensarchief dat wordt gebruikt om de schijf te maken, is dezelfde gegevensarchief die is geselecteerd bij het opnieuw beveiligen van de virtuele machine.
* U alleen niet terugnaar een vmfs (virtual machine file system) of vSAN datastore. Als u een RDM hebt, werken reprotect en failback niet.
* Reprotect omvat een grote initiële gegevensoverdracht die wordt gevolgd door de wijzigingen. Dit proces bestaat omdat de virtuele machine niet op locatie bestaat. De volledige gegevens moeten worden gerepliceerd. Deze reprotect zal ook meer tijd in beslag nemen dan een oorspronkelijke locatie herstel.
* U niet terugnaar RDM-schijven. Alleen nieuwe virtuele machineschijven (VMDK's) kunnen worden gemaakt op een VMFS/vSAN-gegevensarchief.

> [!NOTE]
> Een fysieke machine, wanneer deze niet is overgegaan naar Azure, kan alleen worden mislukt als virtuele VMware-machine. Dit volgt dezelfde workflow als het alternatieve locatieherstel. Zorg ervoor dat u ten minste één hoofddoelserver en de benodigde ESX/ESXi-hosts ontdekt waaru niet meer moet omgeven.

## <a name="next-steps"></a>Volgende stappen

Volg de stappen om de [failback-bewerking](vmware-azure-failback.md)uit te voeren .

