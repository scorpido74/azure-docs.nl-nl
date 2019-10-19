---
title: Bekende problemen met Azure Monitor voor VM's (preview) | Microsoft Docs
description: In dit artikel worden bekende problemen met Azure Monitor voor VM's beschreven, een oplossing in azure die de status, detectie van toepassings afhankelijkheden en prestatie bewaking van het Azure VM-besturings systeem combineert.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 04/02/2019
ms.openlocfilehash: f6719a8c28571faceb6ebad0567d13a4edc60fe6
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553758"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>Bekende problemen met Azure Monitor voor VM's (preview-versie)

In dit artikel worden bekende problemen met Azure Monitor voor VM's beschreven, een oplossing in azure die de status, detectie van toepassings onderdelen en prestatie bewaking van het Azure VM-besturings systeem combineert. 

## <a name="health"></a>Gezondheid 
Hieronder vindt u bekende problemen met de huidige versie van de status functie:

- Als een virtuele machine van Azure wordt verwijderd of verwijderd, wordt deze ergens anders weer gegeven in de lijst weergave van de virtuele machine. Daarnaast wordt door het klikken op de status van een verwijderde of verwijderde virtuele machine de weer gave **status diagnostiek** geopend en wordt vervolgens een load-lus gestart. Als u de naam van de verwijderde VM selecteert, wordt er een deel venster geopend met een bericht dat de virtuele machine is verwijderd.
- Wijzigingen in de configuratie, zoals het bijwerken van een drempel waarde, nemen tot 30 minuten in beslag, zelfs als de portal of de API voor workload-bewaking deze direct kan bijwerken. 
- De status diagnostiek werkt sneller dan de andere weer gaven. De informatie wordt mogelijk vertraagd wanneer u hiertussen schakelt. 
- Voor Linux-Vm's is de titel van de pagina die de status criteria voor één VM-weer gave bevat, de gehele domein naam van de virtuele machine in plaats van de door de gebruiker gedefinieerde VM-naam. 
- Nadat u de bewaking voor een virtuele machine hebt uitgeschakeld met een van de ondersteunde methoden en u deze opnieuw implementeert, moet u deze implementeren in dezelfde werk ruimte. Als u een andere werk ruimte kiest en de status van die virtuele machine probeert weer te geven, wordt mogelijk inconsistent gedrag weer gegeven.
- Nadat u de oplossings onderdelen uit uw werk ruimte hebt verwijderd, kunt u de status van uw Azure-Vm's blijven zien. de prestaties en het toewijzen van gegevens wanneer u navigeert naar een van de weer gaven in de portal. De gegevens worden uiteindelijk niet meer weer gegeven in de weer gave prestaties en kaart na enige tijd. de status weergave gaat echter door met het weer geven van de integriteits status voor uw Vm's. De optie **nu uitproberen** kan alleen worden weer gegeven voor de prestaties en alleen kaart weergaven.

## <a name="next-steps"></a>Volgende stappen
Als u inzicht wilt krijgen in de vereisten en methoden voor het inschakelen van de controle van uw virtuele machines, raadpleegt u [Azure monitor voor VM's inschakelen](vminsights-enable-overview.md).
