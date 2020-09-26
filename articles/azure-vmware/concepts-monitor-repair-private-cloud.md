---
title: 'Concepten: persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen'
description: Meer informatie over hoe de Azure VMware-oplossing VMware ESXi servers bewaakt en herstelt in een persoonlijke cloud van Azure VMware-oplossing.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: bee08304cd14f2aeec6995203638b5a37d9a861c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343905"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Persoonlijke Clouds van Azure VMware-oplossing controleren en herstellen

De Azure VMware-oplossing bewaakt voortdurend de VMware ESXi-servers op een Azure VMware-oplossing privécloud. 

## <a name="what-azure-vmware-solution-monitors"></a>Wat de Azure VMware-oplossing bewaakt

Met de Azure VMware-oplossing wordt het volgende bewaakt voor fout voorwaarden op de host:  

- Processor status 
- Geheugen status 
- Verbinding en energie status 
- Status van hardware-ventilator 
- Netwerk connectiviteits verlies 
- Status van hardwaresysteem bord 
- Er zijn fouten opgetreden op de schijven van een vSAN-host 
- Hardwarematige spanning 
- Status van hardwarematige Tempe ratuur 
- Energie status van hardware 
- Opslag status 
- Verbindingsfout 

> [!NOTE]
> Tenant beheerders van Azure VMware-oplossing mogen de hierboven gedefinieerde VMware vCenter-alarm signalen niet bewerken of verwijderen, aangezien deze worden beheerd door het beheer vlak van de Azure VMware-oplossing in vCenter. Deze waarschuwingen worden gebruikt door de bewaking van Azure VMware-oplossingen om het herstel proces van de Azure VMware-oplossing te activeren.

## <a name="azure-vmware-solution-host-remediation"></a>Herstel van de Azure VMware-oplossing host  

Wanneer de Azure VMware-oplossing een afbraak of fout op een Azure VMware-oplossings knooppunt in de privécloud van een Tenant detecteert, wordt het proces voor het herstellen van hosts geactiveerd. Voor het door voeren van de host moet het defecte knoop punt worden vervangen door een nieuw goedend knoop punt.  

Het proces voor het door voeren van een host begint met het toevoegen van een nieuw ongezond knoop punt in het cluster. Als dat mogelijk is, wordt de defecte host in VMware vSphere onderhouds modus geplaatst. VMware vMotion wordt gebruikt om de virtuele machines van de defecte host te verplaatsen naar andere beschik bare servers in het cluster, waardoor er sprake is van een Livemigratie van geen uitval tijd van werk belastingen. In scenario's waarin de defecte host niet in de onderhouds modus kan worden geplaatst, wordt de host uit het cluster verwijderd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure VMware-oplossing voor persoonlijke Cloud upgrades](concepts-upgrades.md).  
