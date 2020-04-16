---
title: bestand opnemen
description: bestand opnemen
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/23/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 4063751a71cd9cecc424dfe3daddaecfd9ea4071
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421910"
---
Met behulp van Spot VM's u profiteren van onze ongebruikte capaciteit tegen een aanzienlijke kostenbesparing. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's van Spot v.s. Daarom zijn Spot VM's ideaal voor workloads die onderbrekingen kunnen verwerken, zoals batchverwerkingstaken, dev/testomgevingen, grote compute workloads en meer.

De hoeveelheid beschikbare capaciteit kan variëren op basis van grootte, regio, tijdstip en meer. Bij het implementeren van Spot VM's wijst Azure de VM's toe als er capaciteit beschikbaar is, maar er is geen SLA voor deze VM's. Een Spot VM biedt geen hoge beschikbaarheidsgaranties. Op elk moment waarop Azure de capaciteit terug nodig heeft, wordt spotvm's met 30 seconden verwijderd. 


## <a name="eviction-policy"></a>Uitzettingsbeleid

VM's kunnen worden uitgezet op basis van de capaciteit of de maximale prijs die u instelt. Voor virtuele machines is het uitzettingsbeleid ingesteld op *Deallocate* waarmee uw verwijderde VM's naar de gestopte status worden verplaatst, zodat u de uitgezette VM's op een later tijdstip opnieuw implementeren. Het herverdelen van Spot VM's is echter afhankelijk van de beschikbare spotcapaciteit. De deallocated VM's tellen mee voor uw spot vCPU-quotum en u wordt in rekening gebracht voor uw onderliggende schijven. 

Gebruikers kunnen zich aanmelden om in-VM-meldingen te ontvangen via [Azure Scheduled Events](../articles/virtual-machines/linux/scheduled-events.md). Dit zal u op de hoogte stellen als uw VM's worden uitgezet en u 30 seconden hebt om taken te voltooien en afsluittaken uit te voeren voorafgaand aan de uitzetting. 


| Optie | Resultaat |
|--------|---------|
| Max prijs is ingesteld op >= de huidige prijs. | VM wordt geïmplementeerd als er capaciteit en quotum beschikbaar zijn. |
| Max prijs is ingesteld op < de huidige prijs. | De VM is niet geïmplementeerd. U krijgt een foutmelding dat de maximale prijs moet worden >= huidige prijs. |
| Een VM opnieuw starten/toewijzen als de maximumprijs >is = de huidige prijs | Als er capaciteit en quotum is, wordt de VM geïmplementeerd. |
| Een VM opnieuw starten/toewijzen als de maximumprijs < de huidige prijs is | U krijgt een foutmelding dat de maximale prijs moet worden >= huidige prijs. | 
| Prijs voor de VM is gestegen en is nu > de max prijs. | De VM wordt uitgezet. Je krijgt een 30s melding voor de daadwerkelijke uitzetting. | 
| Na uitzetting gaat de prijs voor de VM terug naar < de max prijs. | De VM wordt niet automatisch opnieuw gestart. U de VM zelf opnieuw starten en deze worden in rekening gebracht tegen de huidige prijs. |
| Als de maximale prijs is ingesteld op`-1` | De VM wordt om prijsredenen niet uitgezet. De max prijs zal de huidige prijs, tot de prijs voor standaard VM's. U wordt nooit boven de standaardprijs in rekening gebracht.| 
| Het veranderen van de maximale prijs | U moet de VM deallocaten om de maximale prijs te wijzigen. Deallocate de VM, stel t een nieuwe max prijs, dan bij te werken de VM. |

## <a name="limitations"></a>Beperkingen

De volgende VM-formaten worden niet ondersteund voor spot-VM's:
 - B-serie
 - Promo versies van elke grootte (zoals Dv2, NV, NC, H promo maten)

Spot VM's kunnen momenteel geen tijdelijke OS-schijven gebruiken.

Spot VM's kunnen worden geïmplementeerd in elke regio, behalve Microsoft Azure China 21Vianet.

## <a name="pricing"></a>Prijzen

De prijzen voor spotVM's zijn variabel, gebaseerd op regio en SKU. Zie VM-prijzen voor [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/)voor meer informatie. 


Met variabele prijzen hebt u de optie om een maximale prijs in te stellen, in Amerikaanse dollars (USD), met maximaal 5 decimalen. De waarde `0.98765`is bijvoorbeeld een maximale prijs van $ 0,98765 USD per uur. Als u de maximale `-1`prijs instelt, wordt de VM niet uitgezet op basis van de prijs. De prijs voor de VM zal de huidige prijs voor spot of de prijs voor een standaard VM, die ooit minder is, zolang er capaciteit en quotum beschikbaar is.


##  <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V:** Eenmaal gemaakt, is een Spot VM hetzelfde als gewone standaard VM?

**A:** Ja, behalve dat er geen SLA is voor Spot VM's en ze kunnen op elk gewenst moment worden uitgezet.


**V:** Wat te doen als je wordt uitgezet, maar nog steeds capaciteit nodig?

**A:** We raden u aan standaard VM's te gebruiken in plaats van Spot VM's als u meteen capaciteit nodig hebt.


**V:** Hoe wordt quota beheerd voor Spot VM's?

**A:** Spot VM's hebben een aparte quotumgroep. Spotquota worden gedeeld tussen VM's en schaalset-instanties. Zie [Azure-abonnement- en servicelimieten, quota en beperkingen](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits) voor meer informatie.


**V:** Kan ik een extra quotum voor Spot aanvragen?

**A:** Ja, u de aanvraag indienen om uw quotum voor spotVM's te verhogen via de [standaardquotaaanvraagprocedure.](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)


**V:** Welke kanalen ondersteunen Spot VM's?

**A:** Zie de onderstaande tabel voor de beschikbaarheid van Spot VM.

<a name="channel"></a>

| Azure-kanalen               | Beschikbaarheid azure spot VM's       |
|------------------------------|-----------------------------------|
| Enterprise Agreement         | Ja                               |
| Betalen naar gebruik                | Ja                               |
| Cloud Service Provider (CSP) | [Neem contact op met uw partner](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| Microsoft-klantovereenkomst | Ja                               |
| Voordelen                     | Niet beschikbaar                     |
| Gesponsorde                    | Niet beschikbaar                     |
| Gratis proefversie                   | Niet beschikbaar                     |


**V:** Waar kan ik vragen plaatsen?

**A:** U uw vraag `azure-spot` plaatsen en taggen bij [Q&A.](https://docs.microsoft.com/answers/topics/azure-spot.html) 



