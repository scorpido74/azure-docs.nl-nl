---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 8/22/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: b2a7bbef2c421281780c0191fa32381468899bbf
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70020326"
---
Azure werkt periodiek het platform bij om de betrouw baarheid, prestaties en beveiliging van de host-infra structuur voor virtuele machines te verbeteren. Het doel van deze updates is het bereiken van patches voor software onderdelen in de hostomgeving voor het upgraden van netwerk onderdelen of het buiten gebruik stellen van hardware. 

Updates zijn zelden van invloed op de gehoste Vm's. Wanneer de updates een effect hebben, kiest Azure de methode van de minst meest impact op updates:

- Als de update niet opnieuw moet worden opgestart, wordt de virtuele machine onderbroken terwijl de host wordt bijgewerkt, of wordt de virtuele machine Live gemigreerd naar een al bijgewerkte host.

- Als het onderhoud opnieuw moet worden opgestart, ontvangt u een melding van het geplande onderhoud. Azure biedt ook een tijd venster waarin u het onderhoud zelf kunt starten, op het moment dat u voor u werkt. Het venster voor zelf onderhoud is doorgaans 35 dagen, tenzij het onderhoud urgent is. Azure is investeren in technologieën om het aantal cases te verminderen waarvoor gepland platform onderhoud vereist dat de Vm's opnieuw worden opgestart. 

Op deze pagina wordt beschreven hoe Azure beide soorten onderhoud uitvoert. Zie [de beschik baarheid van vm's voor Windows](../articles/virtual-machines/windows/manage-availability.md) of het bijbehorende artikel voor [Linux](../articles/virtual-machines/linux/manage-availability.md)beheren voor meer informatie over niet-geplande gebeurtenissen (uitval).

Binnen een VM kunt u meldingen ontvangen over aanstaande onderhoud door [gebruik te maken van Scheduled Events voor Windows](../articles/virtual-machines/windows/scheduled-events.md) of voor [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Voor instructies voor het beheren van gepland onderhoud raadpleegt u het [verwerken van geplande onderhouds meldingen voor Linux](../articles/virtual-machines/linux/maintenance-notifications.md) of het bijbehorende artikel voor [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Onderhoud waarvoor geen herstart nodig is

Zoals eerder vermeld, zijn de meeste platform updates niet van invloed op virtuele machines van klanten. Wanneer een niet-impact update niet mogelijk is, kiest Azure het update mechanisme dat het minst invloed heeft op virtuele machines van de klant. 

De meeste onderhoud van niet-nul-impact pauzeert de virtuele machine korter dan 10 seconden. In bepaalde gevallen maakt Azure gebruik van geheugen behoud van onderhouds mechanismen. Deze mechanismen pauzeren de virtuele machine tot 30 seconden en bewaren het geheugen in het RAM. De virtuele machine wordt vervolgens hervat en de klok wordt automatisch gesynchroniseerd. 

Het onderhoud van geheugen-behoud werkt meer dan 90 procent van de Azure-Vm's. Het werkt niet voor G, M, N en H-serie. Azure maakt steeds meer gebruik van Live-migratie technologieën en verbetert de onderhouds mechanismen van het geheugen om de onderbrekings duur te verminderen.  

Voor deze onderhouds bewerkingen die niet opnieuw moeten worden opgestart, wordt één fout domein tegelijk toegepast. Deze worden gestopt als er waarschuwings status signalen worden ontvangen. 

Deze typen updates kunnen van invloed zijn op sommige toepassingen. Wanneer de virtuele machine Live naar een andere host wordt gemigreerd, kan het even duren voordat de virtuele machine wordt onderbroken door bepaalde gevoelige workloads. Voor de voor bereiding op het onderhoud van de virtuele machine en het verminderen van de impact tijdens het onderhoud van Azure, kunt [u Scheduled Events voor Windows](../articles/virtual-machines/windows/scheduled-events.md) of [Linux](../articles/virtual-machines/linux/scheduled-events.md) gebruiken voor dergelijke toepassingen. Azure biedt ook volledige controle over een platform onderhoud die niet gelijk is aan nul op met [Azure toegewezen hosts](../articles/virtual-machines/windows/dedicated-hosts.md) en [geïsoleerde vm's](../articles/security/fundamentals/isolation-choices.md). De onderhouds beheer functie is beschikbaar als preview-versie en u kunt toegang aanvragen via een [aanmeldings formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u). Het biedt u de mogelijkheid om updates van platformen die niet gelijk zijn aan nul over te slaan en de updates als batch toe te passen op een tijdstip van uw keuze binnen een 35-daags venster.

### <a name="live-migration"></a>Livemigratie

Livemigratie is een bewerking waarvoor geen herstart vereist is en waarmee het geheugen voor de virtuele machine wordt bewaard. Dit veroorzaakt een pauze ring of blok keren, meestal niet meer dan 5 seconden. Met uitzonde ring van G, M, N en H-serie, kunnen alle IaaS-Vm's (Infrastructure as a Service) in aanmerking komen voor Livemigratie. In aanmerking komende Vm's vertegenwoordigen meer dan 90 procent van de IaaS-Vm's die zijn geïmplementeerd op de Azure-vloot. 

De Livemigratie van het Azure-platform wordt gestart in de volgende scenario's:
- Gepland onderhoud
- Hardwarestoring
- Toewijzings optimalisaties

Bij sommige scenario's gepland onderhoud wordt gebruikgemaakt van Livemigratie, en u kunt Scheduled Events gebruiken om vooraf te weten wanneer Livemigratie wordt gestart.

Livemigratie kan ook worden gebruikt om Vm's te verplaatsen wanneer Azure Machine Learning algoritmen een dreigende hardwarestoring of als u de VM-toewijzingen wilt optimaliseren. Zie de [tolerantie van Azure-Vm's verbeteren met voorspellende machine learning en Livemigratie](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)voor meer informatie over voorspellende modellen die exemplaren van gedegradeerde hardware detecteren. Live-migratie meldingen worden weer gegeven in de Azure Portal in de monitor en Service Health logboeken, evenals in Scheduled Events als u deze services gebruikt.

## <a name="maintenance-that-requires-a-reboot"></a>Onderhoud waarvoor opnieuw opstarten is vereist

In de zeldzame gevallen waarin Vm's opnieuw moeten worden opgestart voor gepland onderhoud, wordt u hiervan op de hoogte gebracht. Gepland onderhoud bestaat uit twee fasen: de self-service fase en een geplande onderhouds fase.

Tijdens de *self-service fase*, die doorgaans vier weken duurt, start u het onderhoud op uw vm's. Als onderdeel van de self-service kunt u een query uitvoeren op elke virtuele machine om de status en het resultaat van uw laatste onderhouds aanvraag te bekijken.

Wanneer u self-service onderhoud start, wordt uw VM opnieuw geïmplementeerd op een al bijgewerkt knoop punt. Omdat de VM opnieuw wordt opgestart, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerk interface, bijgewerkt.

Als er een fout optreedt tijdens self-service onderhoud, wordt de bewerking gestopt, wordt de VM niet bijgewerkt en krijgt u de mogelijkheid om het selfservice onderhoud opnieuw uit te voeren. 

Wanneer de self-service fase eindigt, begint de *geplande onderhouds fase* . Tijdens deze fase kunt u nog steeds een query uitvoeren voor de onderhouds fase, maar kunt u het onderhoud zelf niet starten.

Zie voor meer informatie over het beheren van onderhoud waarvoor opnieuw opstarten is vereist voor het [verwerken van geplande onderhouds meldingen voor Linux](../articles/virtual-machines/linux/maintenance-notifications.md) of het bijbehorende artikel voor [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Beschik baarheid van overwegingen tijdens gepland onderhoud 

Als u van plan bent te wachten tot de geplande onderhouds fase, zijn er enkele dingen die u moet overwegen om de hoogst mogelijke Beschik baarheid van uw Vm's te hand haven. 

#### <a name="paired-regions"></a>Gekoppelde regio's

Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografische omgeving. Samen maken ze een regio paar. Tijdens de geplande onderhouds fase worden in azure alleen de Vm's in één regio van een regio paar bijgewerkt. Tijdens het bijwerken van de virtuele machine in Noord-Centraal VS, werkt Azure de virtuele machine in Zuid-Centraal VS bijvoorbeeld niet bij. Tegelijkertijd met US - oost kan er echter wel onderhoud plaatsvinden in andere regio's, zoals Europa - noord. Meer informatie over het werken met regio paren kan u helpen uw Vm's beter te verdelen over regio's. Zie [Azure Region-paren](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie.

#### <a name="availability-sets-and-scale-sets"></a>Beschikbaarheids sets en schaal sets

Bij het implementeren van een werk belasting op virtuele machines in azure, kunt u de virtuele machines binnen een beschikbaarheidsset maken om hoge Beschik baarheid voor uw toepassing te bieden. Met behulp van beschikbaarheids sets kunt u ervoor zorgen dat tijdens een storing of onderhouds gebeurtenis die opnieuw moet worden opgestart, ten minste één virtuele machine beschikbaar is.

Binnen een beschikbaarheidsset worden afzonderlijke Vm's verdeeld over Maxi maal 20 Update domeinen (UDs). Tijdens gepland onderhoud wordt slechts één UD bijgewerkt op een bepaald moment. De UDs is niet noodzakelijkerwijs opeenvolgend bijgewerkt. 

Virtuele-machine *schaal sets* vormen een Azure Compute-resource die u kunt gebruiken om een set identieke vm's te implementeren en te beheren als één resource. De schaalset wordt automatisch geïmplementeerd in de UDs, zoals virtuele machines in een beschikbaarheidsset. Net als bij beschikbaarheids sets, wanneer u schaal sets gebruikt, wordt slechts één UD bijgewerkt op een wille keurig tijdstip tijdens gepland onderhoud.

Zie [de beschik baarheid van uw vm's voor Windows](../articles/virtual-machines/windows/manage-availability.md) of het bijbehorende artikel voor [Linux](../articles/virtual-machines/linux/manage-availability.md)beheren voor meer informatie over het instellen van uw vm's voor maximale Beschik baarheid.
