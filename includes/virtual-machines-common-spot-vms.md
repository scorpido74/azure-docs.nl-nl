---
title: bestand opnemen
description: bestand opnemen
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 06/15/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 76af1084626944d8399edd2c7ec5dec92e25f3ed
ms.sourcegitcommit: dfa5f7f7d2881a37572160a70bac8ed1e03990ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378620"
---
Met behulp van spot Vm's kunt u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kosten besparing. Op elk moment dat Azure de capaciteit nodig heeft, verwijdert de Azure-infra structuur spot Vm's. Daarom zijn de virtuele machines geschikt voor werk belastingen die onderbrekingen kunnen afhandelen, zoals batch verwerkings taken, ontwikkel-en test omgevingen, grootschalige werk belastingen en meer.

De hoeveelheid beschik bare capaciteit kan variëren op basis van grootte, regio, tijd van de dag en meer. Bij het implementeren van spot-Vm's worden de Vm's door Azure toegewezen als er capaciteit beschikbaar is, maar er is geen SLA voor deze Vm's. Een spot-VM biedt geen garanties voor hoge Beschik baarheid. Op elk moment dat Azure de capaciteit nodig heeft, worden virtuele machines met de Azure-infra structuur met een kennisgeving van 30 seconden verwijderd. 


## <a name="eviction-policy"></a>Verwijderings beleid

Vm's kunnen worden verwijderd op basis van de capaciteit of de maximale prijs die u hebt ingesteld. Voor virtuele machines wordt het verwijderings beleid zo ingesteld dat de *toewijzing* ongedaan wordt gemaakt, waardoor de verwijderde vm's naar de status stopped-disallocated worden verplaatst, zodat u de verwijderde vm's op een later tijdstip opnieuw kunt implementeren. Het opnieuw toewijzen van spot-Vm's is echter afhankelijk van de beschik bare steun capaciteit. De toegewezen Vm's worden geteld voor uw vCPU-quotum en er worden kosten in rekening gebracht voor de onderliggende schijven. 

Gebruikers kunnen zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Hiermee wordt u op de hoogte gesteld als uw Vm's worden verwijderd en u 30 seconden hebt om taken te volt ooien en afsluit taken uit te voeren vóór de verwijdering. 


| Optie | Resultaat |
|--------|---------|
| De maximale prijs is ingesteld op >= de huidige prijs. | VM wordt geïmplementeerd als de capaciteit en het quotum beschikbaar zijn. |
| De maximale prijs is ingesteld op < van de huidige prijs. | De virtuele machine is niet geïmplementeerd. Er wordt een fout bericht weer gegeven dat de maximum prijs >= huidige prijs moet zijn. |
| Een virtuele machine voor stoppen/toewijzing opnieuw starten als de maximum prijs >= de huidige prijs | Als er sprake is van capaciteit en quotum, wordt de VM geïmplementeerd. |
| Een virtuele machine voor stoppen/toewijzing opnieuw starten als de maximum prijs < de huidige prijs is | Er wordt een fout bericht weer gegeven dat de maximum prijs >= huidige prijs moet zijn. | 
| De prijs voor de virtuele machine is voltooid en is nu > de maximum prijs. | De virtuele machine wordt verwijderd. U krijgt een 30s-melding vóór de werkelijke verwijdering. | 
| Nadat de prijs voor de virtuele machine is verwijderd, wordt deze weer < de maximum prijs. | De virtuele machine wordt niet automatisch opnieuw gestart. U kunt de virtuele machine zelf opnieuw opstarten en er worden kosten in rekening gebracht voor de huidige prijs. |
| Als de maximum prijs is ingesteld op`-1` | De virtuele machine wordt om prijs redenen niet verwijderd. De maximale prijs is de huidige prijs, tot de prijs voor standaard-Vm's. Er worden nooit kosten in rekening gebracht boven de standaard prijs.| 
| De maximum prijs wijzigen | U moet de toewijzing van de virtuele machine ongedaan maken om de maximale prijs te wijzigen. U kunt de toewijzing van de virtuele machine ongedaan maken, een nieuwe maximum prijs instellen en vervolgens de virtuele machine bijwerken. |

## <a name="limitations"></a>Beperkingen

De volgende VM-grootten worden niet ondersteund voor spot-Vm's:
 - B-serie
 - Promotie versies van elke grootte (zoals dv2, NV, NC, H promotie grootten)

Spot-Vm's kunnen worden geïmplementeerd in elke regio, met uitzonde ring van Microsoft Azure-China 21Vianet.

Sommige abonnements kanalen worden niet ondersteund:

<a name="channel"></a>

| Azure-kanalen               | Beschik baarheid van Azure spot Vm's       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Ja                               |
| Betalen naar gebruik                | Ja                               |
| Cloud serviceprovider (CSP) | [Neem contact op met uw partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Voordelen                     | Niet beschikbaar                     |
| Gesponsorde                    | Ja                               |
| Gratis proefversie                   | Niet beschikbaar                     |



## <a name="pricing"></a>Prijzen

Prijzen voor spot Vm's zijn variabel, op basis van de regio en de SKU. Zie prijzen voor VM'S voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 


Met variabele prijzen kunt u een maximum prijs instellen, in Amerikaanse dollars (USD), met Maxi maal vijf decimalen. De waarde `0.98765` is bijvoorbeeld een maximum prijs van $0,98765 USD per uur. Als u de maximale prijs instelt op `-1` , wordt de VM niet verwijderd op basis van de prijs. De prijs voor de virtuele machine is de huidige prijs voor steun of de prijs voor een standaard-VM, die ooit kleiner is, zolang er capaciteit en quota beschikbaar zijn.


##  <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Eenmaal gemaakt is dit een plek-VM die gelijk is aan de normale standaard-VM?

**A:** Ja, behalve als er geen SLA is voor de spot-Vm's, en ze kunnen op elk gewenst moment worden verwijderd.


**V:** Wat te doen wanneer u weggaat, maar nog steeds capaciteit nodig heeft?

**A:** U wordt aangeraden standaard Vm's te gebruiken in plaats van op de plek waar u de capaciteit direct nodig hebt.


**V:** Hoe wordt het quotum beheerd voor de plaats van virtuele machines?

**A:** Spot Vm's hebben een afzonderlijke quotum groep. Het steun quotum wordt gedeeld tussen Vm's en scale-set-exemplaren. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) voor meer informatie.


**V:** Kan ik een extra quotum voor plaats vragen?

**A:** Ja, u kunt de aanvraag indienen om uw quotum voor de begeleide Vm's te verhogen via het [standaard quotum aanvraag proces](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests).


**V:** Waar kan ik vragen plaatsen?

**A:** U kunt uw vraag met `azure-spot` op [Q&A](https://docs.microsoft.com/answers/topics/azure-spot.html)plaatsen en labelen. 



