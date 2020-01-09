---
title: Azure Monitor voor bekende problemen-machines (preview) | Microsoft Docs
description: In dit artikel worden bekende problemen met Azure Monitor voor VM's beschreven, een oplossing in azure die de status, detectie van toepassings afhankelijkheden en prestatie bewaking van het Azure VM-besturings systeem combineert.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/02/2019
ms.openlocfilehash: b59e2d1897557b47bcfeafbc17141f869e2f192e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75450676"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Bekende problemen met Azure Monitor voor virtuele machines (preview)

In dit artikel worden bekende problemen met Azure Monitor voor VM's beschreven, een oplossing in azure die de status, detectie van toepassings onderdelen en prestatie bewaking van het Azure VM-besturings systeem combineert. 

## <a name="health"></a>Gezondheid 
Hieronder vindt u bekende problemen met de huidige versie van de status functie:

- Als een virtuele machine van Azure wordt verwijderd of verwijderd, wordt deze ergens anders weer gegeven in de lijst weergave van de virtuele machine. Bovendien op de status van een virtuele machine verwijderd of verwijderde klikt, wordt de **diagnostische gegevens over gezondheid** weergeven en start vervolgens een lus laden. Als u de naam van de verwijderde virtuele machine selecteert, wordt een deelvenster geopend met een bericht weergegeven dat de virtuele machine is verwijderd.
- Wijzigingen in de configuratie, zoals het bijwerken van een drempelwaarde, wordt er maximaal 30 minuten duren, zelfs als de portal of Workload Monitor API kan ze onmiddellijk bijwerken. 
- De status diagnostiek werkt sneller dan de andere weer gaven. De informatie wordt mogelijk vertraagd wanneer u hiertussen schakelt. 
- Voor Linux-Vm's is de titel van de pagina die de status criteria voor één VM-weer gave bevat, de gehele domein naam van de virtuele machine in plaats van de door de gebruiker gedefinieerde VM-naam. 
- Nadat u de bewaking voor een virtuele machine hebt uitgeschakeld met een van de ondersteunde methoden en u deze opnieuw implementeert, moet u deze implementeren in dezelfde werk ruimte. Als u een andere werk ruimte kiest en de status van die virtuele machine probeert weer te geven, wordt mogelijk inconsistent gedrag weer gegeven.
- Nadat u de oplossings onderdelen uit uw werk ruimte hebt verwijderd, kunt u de status van uw Azure-Vm's blijven zien. de prestaties en het toewijzen van gegevens wanneer u navigeert naar een van de weer gaven in de portal. De gegevens worden uiteindelijk niet meer weer gegeven in de weer gave prestaties en kaart na enige tijd. de status weergave gaat echter door met het weer geven van de integriteits status voor uw Vm's. De optie **nu uitproberen** kan alleen worden weer gegeven voor de prestaties en alleen kaart weergaven.

## <a name="next-steps"></a>Volgende stappen
Als u inzicht wilt krijgen in de vereisten en methoden voor het inschakelen van de controle van uw virtuele machines, raadpleegt u [Azure monitor voor VM's inschakelen](vminsights-enable-overview.md).
