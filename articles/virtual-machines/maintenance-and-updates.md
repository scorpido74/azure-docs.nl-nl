---
title: Onderhoud en updates
description: Overzicht van onderhoud en updates voor virtuele machines die in Azure worden uitgevoerd.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/18/2019
ms.author: shants
ms.openlocfilehash: eaf7616b3bd69828829342b4dca9247c009d3475
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79250229"
---
# <a name="maintenance-for-virtual-machines-in-azure"></a>Onderhoud voor virtuele machines in Azure

Azure werkt zijn platform regelmatig bij om de betrouwbaarheid, prestaties en beveiliging van de hostinfrastructuur voor virtuele machines te verbeteren. Het doel van deze updates behelst zowel het uitvoeren van softwarepatches voor onderdelen in de hostomgeving als het upgraden van netwerkonderdelen of het buiten gebruik stellen van hardware. 

Updates hebben zelden invloed op de gehoste VM's. Wanneer updates wel effect hebben, kiest Azure de minst impactvolle methode voor updates:

- Als de update geen herstart vereist, wordt de VM onderbroken terwijl de host wordt bijgewerkt of wordt de VM live gemigreerd naar een reeds bijgewerkte host. 
- Als onderhoud een herstart vereist, wordt u op de hoogte gesteld van het geplande onderhoud. Azure biedt ook een tijdvenster waarin u het onderhoud zelf starten, op een tijdstip dat voor u werkt. Het zelfonderhoudsvenster is meestal 30 dagen, tenzij het onderhoud dringend is. Azure investeert in technologieën om het aantal gevallen te verminderen waarin gepland platformonderhoud vereist dat de VM's opnieuw worden opgestart. Zie Geplande onderhoudsmeldingen afhandelen met de Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) of [portal](maintenance-notifications-portal.md)voor instructies voor het beheren van gepland onderhoud.

Op deze pagina wordt beschreven hoe Azure beide typen onderhoud uitvoert. Zie [De beschikbaarheid van VM's voor Windows](./windows/manage-availability.md) of het bijbehorende artikel voor [Linux](./linux/manage-availability.md)beheren voor meer informatie over ongeplande gebeurtenissen (uitval).

Binnen een VM u meldingen ontvangen over aankomend onderhoud door Geplande gebeurtenissen voor Windows of voor [Linux](./linux/scheduled-events.md)te [gebruiken.](./windows/scheduled-events.md)



## <a name="maintenance-that-doesnt-require-a-reboot"></a>Onderhoud waarvoor geen herstart nodig is

De meeste platformupdates hebben geen invloed op de VM's van klanten. Wanneer een no-impact update niet mogelijk is, kiest Azure het updatemechanisme dat het minst impact heeft op de VM's van klanten. 

De meeste onderhoudswerkzaamheden zonder nuleffect worden de VM minder dan 10 seconden onderbroken. In bepaalde gevallen maakt Azure gebruik van geheugenbehoudende onderhoudsmechanismen. Deze mechanismen pauzeren de VM tot 30 seconden en behouden het geheugen in RAM. De VM wordt vervolgens hervat en de klok wordt automatisch gesynchroniseerd. 

Geheugenbehoud werkt voor meer dan 90 procent van de Azure VM's. Het werkt niet voor G, M, N en H-serie. Azure maakt steeds meer gebruik van technologieën voor live migratie en verbetert geheugenbehoudende onderhoudsmechanismen om de pauzeduur te verkorten.  

Deze onderhoudsbewerkingen waarvoor geen herstart nodig is, worden één foutdomein tegelijk toegepast. Ze stoppen als ze waarschuwingssignalen voor de gezondheid ontvangen. 

Dit soort updates kan van invloed zijn op sommige toepassingen. Wanneer de VM live is gemigreerd naar een andere host, kunnen sommige gevoelige workloads een lichte prestatiedegradatie laten zien in de paar minuten voorafgaand aan de VM-pauze. Als u zich wilt voorbereiden op VM-onderhoud en de impact tijdens Azure-onderhoud wilt verminderen, probeert u Geplande gebeurtenissen voor Windows of [Linux](./linux/scheduled-events.md) voor dergelijke toepassingen te [gebruiken.](./windows/scheduled-events.md) 

Er is ook een functie, onderhoudscontrole, in openbare preview die kan helpen bij het beheren van onderhoud dat geen herstart vereist. U moet [Azure Dedicated Hosts](./linux/dedicated-hosts.md) of een geïsoleerde [VM](../security/fundamentals/isolation-choices.md)gebruiken. Onderhoudscontrole geeft u de mogelijkheid om platformupdates over te slaan en de updates naar keuze toe te passen binnen een rollend venster van 35 dagen. Zie [Updates beheren met onderhoudsbeheer en Azure CLI](maintenance-control-cli.md)voor meer informatie.


### <a name="live-migration"></a>Livemigratie

Live migratie is een bewerking waarvoor geen reboot nodig is en die het geheugen voor de VM behoudt. Het veroorzaakt een pauze of bevriezing, meestal niet langer dan 5 seconden. Met uitzondering van de G-, M-, N- en H-serie komen alle VM's voor infrastructuur als service (IaaS) in aanmerking voor live migratie. In aanmerking komende VM's vertegenwoordigen meer dan 90 procent van de IaaS VM's die worden geïmplementeerd in de Azure-vloot. 

Het Azure-platform start live migratie in de volgende scenario's:
- Gepland onderhoud
- Hardwarestoring
- Toewijzingsoptimalisaties

Sommige scenario's voor gepland onderhoud maken gebruik van live migratie en u Geplande gebeurtenissen gebruiken om van tevoren te weten wanneer live migratiebewerkingen worden gestart.

Live migratie kan ook worden gebruikt om VM's te verplaatsen wanneer Azure Machine Learning-algoritmen een dreigende hardwarefout voorspellen of wanneer u VM-toewijzingen wilt optimaliseren. Zie [Azure VM-tolerantie verbeteren met voorspellende machine learning en live migratie](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure)voor meer informatie over voorspellende modellering die gevallen van gedegradeerde hardware detecteert. Meldingen met livemigratie worden weergegeven in de Azure-portal in de logboeken Monitor- en Servicestatus en in Geplande gebeurtenissen als u deze services gebruikt.

## <a name="maintenance-that-requires-a-reboot"></a>Onderhoud dat een herstart vereist

In het zeldzame geval dat VM's opnieuw moeten worden opgestart voor gepland onderhoud, wordt u van tevoren op de hoogte gesteld. Gepland onderhoud kent twee fasen: de selfservicefase en een geplande onderhoudsfase.

Tijdens de *selfservicefase*, die doorgaans vier weken duurt, start u het onderhoud aan uw VM's. Als onderdeel van de selfservice u elke virtuele machine opvragen om de status en het resultaat van uw laatste onderhoudsaanvraag te zien.

Wanneer u zelfserviceonderhoud start, wordt uw VM opnieuw geïmplementeerd op een reeds bijgewerkt knooppunt. Omdat de VM opnieuw wordt opgestart, gaat de tijdelijke schijf verloren en worden dynamische IP-adressen die zijn gekoppeld aan de virtuele netwerkinterface bijgewerkt.

Als er een fout optreedt tijdens selfserviceonderhoud, wordt de bewerking gestopt, wordt de VM niet bijgewerkt en krijgt u de optie om het selfserviceonderhoud opnieuw te proberen. 

Wanneer de selfservicefase is afgelopen, begint de *geplande onderhoudsfase.* Tijdens deze fase u nog steeds vragen stellen voor de onderhoudsfase, maar u het onderhoud niet zelf starten.

Zie **Geplande onderhoudsmeldingen afhandelen** met de Azure [CLI,](maintenance-notifications-cli.md) [PowerShell](maintenance-notifications-powershell.md) of [portal](maintenance-notifications-portal.md)voor meer informatie over het beheren van onderhoud waarvoor een herstart vereist is. 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Beschikbaarheidsoverwegingen tijdens gepland onderhoud 

Als u besluit te wachten tot de geplande onderhoudsfase, zijn er een paar dingen die u moet overwegen om de hoogste beschikbaarheid van uw VM's te behouden. 

#### <a name="paired-regions"></a>Gekoppelde regio's

Elke Azure-regio is gekoppeld aan een andere regio binnen dezelfde geografische omgeving. Samen maken ze een regiopaar. Tijdens de geplande onderhoudsfase werkt Azure alleen de VM's bij in één regio van een regiopaar. Tijdens het updaten van de VM in North Central US werkt Azure bijvoorbeeld geen VM tegelijkertijd bij in South Central US. Tegelijkertijd met VS - oost kan er echter wel onderhoud plaatsvinden in andere regio's, zoals Europa - noord. Als u begrijpt hoe regioparen werken, u uw VM's beter verdelen over regio's. Zie [Azure-regioparen](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)voor meer informatie.

#### <a name="availability-sets-and-scale-sets"></a>Beschikbaarheidssets en schaalsets

Wanneer u een werkbelasting implementeert op Azure VM's, u de VM's maken binnen een *beschikbaarheidsset* om uw toepassing met een hoge beschikbaarheid te bieden. Met behulp van beschikbaarheidssets u ervoor zorgen dat tijdens een storing of onderhoudsgebeurtenissen waarvoor een herstart vereist is, ten minste één VM beschikbaar is.

Binnen een beschikbaarheidsset zijn afzonderlijke VM's verspreid over maximaal 20 updatedomeinen. Tijdens gepland onderhoud wordt slechts één updatedomein op een bepaald moment bijgewerkt. Updatedomeinen worden niet noodzakelijkerwijs opeenvolgend bijgewerkt. 

Virtuele *machineschaalsets* zijn een Azure-compute resource die u gebruiken om een set identieke VM's als één resource te implementeren en te beheren. De schaalset wordt automatisch geïmplementeerd in ud's, zoals VM's in een beschikbaarheidsset. Net als bij beschikbaarheidssets wordt bij het gebruik van schaalsets slechts één UD op een bepaald moment bijgewerkt tijdens gepland onderhoud.

Zie [De beschikbaarheid van uw VM's voor Windows](./windows/manage-availability.md) of het bijbehorende artikel voor [Linux](./linux/manage-availability.md)beheren voor meer informatie over het instellen van uw VM's voor hoge beschikbaarheid.

#### <a name="availability-zones"></a>Beschikbaarheidszones

Beschikbaarheidszones zijn unieke fysieke locaties binnen een Azure-gebied. Elke zone bestaat uit een of meer datacenters die zijn voorzien van een onafhankelijke stroomvoorziening, koeling en netwerken. Om voor tolerantie te zorgen, is er een minimum van drie afzonderlijke zones in alle ingeschakelde regio's. 

Een beschikbaarheidszone is een combinatie van een foutdomein en een updatedomein. Als u drie of meer VM's maakt in drie zones in een Azure-gebied, worden uw VM's effectief verdeeld over drie foutdomeinen en drie updatedomeinen. Het Azure-platform herkent deze verdeling over updatedomeinen om ervoor te zorgen dat virtuele machines in verschillende zones niet op hetzelfde moment worden bijgewerkt.

Elke infrastructuurupdate rolt zone per zone uit, binnen één regio. Maar, u implementatie gaande in zone 1, en verschillende inzet gaan in Zone 2, op hetzelfde moment. Implementaties zijn niet allemaal geserialiseerd. Maar één implementatie rolt slechts één zone tegelijk uit om het risico te verminderen.

## <a name="next-steps"></a>Volgende stappen 

U de [Azure CLI,](maintenance-notifications-cli.md) [Azure PowerShell](maintenance-notifications-powershell.md) of de [portal](maintenance-notifications-portal.md) gebruiken om gepland onderhoud te beheren. 