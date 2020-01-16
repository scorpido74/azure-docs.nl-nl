---
title: Planning voor de migratie van klassiek naar Azure Resource Manager
description: Planning voor de migratie van IaaS-resources van klassiek naar Azure Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 78492a2c-2694-4023-a7b8-c97d3708dcb7
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/01/2017
ms.author: kasing
ms.openlocfilehash: d5245f73520a77648fc590520185a2cf66bffd02
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980888"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planning voor de migratie van IaaS-resources van klassiek naar Azure Resource Manager
Hoewel Azure Resource Manager veel fantastische functies biedt, is het van essentieel belang om uw migratie traject te plannen om ervoor te zorgen dat alles goed gaat. Bij het plannen van de tijd zorgt u ervoor dat er geen problemen optreden tijdens het uitvoeren van migratie activiteiten.

> [!NOTE]
> De volgende richt lijnen zijn sterk bijgedragen aan het Azure-team voor klanten advisering en Cloud oplossingen die samen werken met klanten bij het migreren van grote omgevingen. Zo blijft dit document worden bijgewerkt wanneer er nieuwe patronen worden weer gegeven. Controleer daarom van tijd tot tijd om te zien of er nieuwe aanbevelingen zijn.

Er zijn vier algemene fasen van de migratie traject:<br>

![Migratie fasen](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plan

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en-afwegingen

Afhankelijk van de grootte van uw technische vereisten, geografi en operationele procedures, kunt u overwegen:

1. Waarom is Azure Resource Manager gewenst voor uw organisatie?  Wat zijn de zakelijke redenen voor een migratie?
2. Wat zijn de technische redenen voor Azure Resource Manager?  Wat (indien van toepassing) extra Azure-Services wilt u gebruiken?
3. Welke toepassing (of sets met virtuele machines) is opgenomen in de migratie?
4. Welke scenario's worden ondersteund met de migratie-API?  Bekijk de [niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Ondersteunen uw operationele teams nu toepassingen/Vm's in zowel het klassieke als het Azure Resource Manager?
6. Hoe (indien van toepassing) Azure Resource Manager uw VM-implementatie-, beheer-, bewakings-en rapportage processen wijzigen?  Moeten uw implementatie scripts worden bijgewerkt?
7. Wat is het communicatie plan om belanghebbenden (eind gebruikers, eigen aars van toepassingen en eigen aars van infra structuur) te waarschuwen?
8. Moet er een onderhouds periode zijn waarin de toepassing niet beschikbaar is voor eind gebruikers en voor toepassings eigenaren, afhankelijk van de complexiteit van de omgeving?  Zo ja, hoe lang?
9. Wat is het trainings plan om ervoor te zorgen dat belanghebbenden kennis en ervaring hebben in Azure Resource Manager?
10. Wat is het programma voor het beheer of het plannen van het project voor de migratie?
11. Wat zijn de tijd lijnen voor de Azure Resource Manager migratie en andere gerelateerde technologie wegen kaarten?  Zijn ze optimaal uitgelijnd?

### <a name="patterns-of-success"></a>Patronen van slagen

Succes volle klanten hebben gedetailleerde plannen waar de voor gaande vragen worden besproken, gedocumenteerd en geregeld.  Zorg ervoor dat de migratie plannen breed worden gecommuniceerd naar sponsors en belanghebbenden.  Geef kennis over uw migratie opties; Lees deze migratie documenten hieronder wordt sterk aanbevolen.

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Valk uilen om te voor komen

- Kan niet plannen.  De technologie stappen van deze migratie zijn bewezen en de uitkomst is voorspelbaar.
- Veronderstelling dat het door het platform ondersteunde migratie-API voor alle scenario's wordt verwerkt. Lees de [niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) om te begrijpen welke scenario's worden ondersteund.
- Geen potentiële toepassings onderbreking voor eind gebruikers plannen.  Plan voldoende buffers om eind gebruikers te waarschuwen voor mogelijk niet-beschik bare toepassings tijd.


## <a name="lab-test"></a>Test omgeving

**Uw omgeving repliceren en een test migratie uitvoeren**
  > [!NOTE]
  > De exacte replicatie van uw bestaande omgeving wordt uitgevoerd door gebruik te maken van een door de Community bijgedragen hulp programma dat niet officieel wordt ondersteund door Microsoft Ondersteuning. Daarom is het een **optionele** stap, maar dit is de beste manier om problemen op te sporen zonder uw productie omgevingen te raken. Als het gebruik van een door de Community bijgedragen hulp programma geen optie is, leest u de aanbevelingen voor het valideren/voorbereiden/afbreken van de droge uitvoering.
  >

  Het uitvoeren van een test omgeving voor uw exacte scenario (compute, netwerken en opslag) is de beste manier om een soepele migratie te garanderen. Dit helpt ervoor te zorgen:

- Een geheel afzonderlijke Lab of een bestaande niet-productie omgeving om te testen. We raden u aan een volledig apart Lab te maken dat herhaaldelijk kan worden gemigreerd en kan worden gewijzigd.  Hieronder vindt u een lijst met scripts voor het verzamelen/onthydraten van meta gegevens uit de echte abonnementen.
- Het is een goed idee om het lab in een afzonderlijk abonnement te maken. De reden hiervoor is dat het lab herhaaldelijk wordt gescheurd en een afzonderlijk, geïsoleerd abonnement heeft, waardoor de kans dat iets echt per ongeluk wordt verwijderd, wordt gereduceerd.

  Dit kunt u doen met behulp van het hulp programma AsmMetadataParser. [Lees hier meer over dit hulp programma](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Patronen van slagen

De volgende problemen zijn in veel van de grotere migraties ontdekt. Dit is geen limitatieve lijst en u moet de niet- [ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) raadplegen voor meer details.  U kunt deze technische problemen wellicht wel of niet tegen komen, maar als u deze wel oplost voordat u een migratie uitvoert, zorgt u ervoor dat u een soepeler ervaring hebt.

- **Een droge uitvoering valideren/voorbereiden/afbreken** : dit is misschien de belangrijkste stap om te zorgen voor een klassieke Azure Resource Manager migratie. De migratie-API bestaat uit drie belang rijke stappen: valideren, voorbereiden en door voeren. Met validering wordt de status van uw klassieke omgeving gelezen en wordt het resultaat van alle problemen geretourneerd. Omdat er echter enkele problemen in de Azure Resource Manager stack kunnen bestaan, wordt met validate niet alles onderschept. De volgende stap in het migratie proces helpt u bij het voorbereiden van deze problemen. Met de voor bereiding worden de meta gegevens van het klassieke naar Azure Resource Manager verplaatst, maar wordt de verplaatsing niet doorgevoerd en wordt niets aan de klassieke kant verwijderd of gewijzigd. Bij een droge uitvoering moet de migratie worden voor bereid en vervolgens worden afgebroken (**niet door voeren**) de migratie wordt voor bereid. Het doel van het valideren/voorbereiden/afbreken van een droge uitvoering is om alle meta gegevens in de Azure Resource Manager stack te bekijken, te bekijken (*programmatisch of in portal*), en te controleren of alles juist wordt gemigreerd en technische problemen kunnen samen werken.  Het biedt ook een idee van de duur van de migratie, zodat u de uitval tijd dienovereenkomstig kunt plannen.  Een validering/voor bereiding/afbreken veroorzaakt geen uitval tijd van de gebruiker; Daarom is het niet-verstoort van het gebruik van de toepassing.
  - De onderstaande items moeten worden opgelost voordat de droge uitvoering kan worden uitgevoerd, maar bij een droge uitvoer test worden deze voorbereidings stappen ook veilig verwijderd als ze worden gemist. Tijdens de migratie van een onderneming hebben we vastgesteld dat de droge uitvoering een veilige en onwaardevole manier is om de gereedheid voor de migratie te garanderen.
  - Wanneer de voor bereiding wordt uitgevoerd, wordt het besturings vlak (Azure Management Operations) vergrendeld voor het hele virtuele netwerk, zodat er geen wijzigingen kunnen worden aangebracht in de VM-meta gegevens tijdens het valideren/voorbereiden/afbreken.  Maar anders wordt een toepassings functie (RD, VM-gebruik, enzovoort) niet beïnvloed.  Gebruikers van de virtuele machines weten niet dat de droge uitvoering wordt uitgevoerd.

- **Express route-circuits en VPN**. Momenteel kunnen Express route gateways met autorisatie koppelingen niet zonder downtime worden gemigreerd. Zie [ExpressRoute-circuits en gekoppelde virtuele netwerken van het klassieke naar het Resource Manager-implementatie model migreren](../../expressroute/expressroute-migration-classic-resource-manager.md)voor de tijdelijke oplossing.

- **VM-extensies** : virtuele-machine uitbreidingen zijn mogelijk een van de grootste obstakels om actieve vm's te migreren. Herstel van VM-extensies kan tot 1-2 dagen duren, dus plan daarom dienovereenkomstig.  Er is een werkende Azure-agent nodig om de VM-extensie status van actieve Vm's te rapporteren. Als de status terugkeert als beschadigd voor een actieve virtuele machine, wordt de migratie gestopt. Het is niet nodig om de migratie in te scha kelen voor de agent zelf, maar als er uitbrei dingen bestaan op de VM, zijn er voor de migratie zowel een werk agent als een uitgaande Internet verbinding (met DNS) vereist.
  - Als de verbinding met een DNS-server tijdens de migratie is verbroken, worden alle VM-extensies uitgezonderd BGInfo versie 1.\* moet eerst worden verwijderd van elke VM voordat de migratie wordt voor bereid en vervolgens opnieuw aan de virtuele machine wordt toegevoegd na Azure Resource Manager migratie.  **Dit geldt alleen voor virtuele machines die worden uitgevoerd.**  Als de toewijzing van Vm's is gestopt, hoeven VM-extensies niet te worden verwijderd.

  > [!NOTE]
  > Veel uitbrei dingen zoals de bewaking van Azure Diagnostics en Security Center worden na de migratie opnieuw geïnstalleerd, dus het is geen probleem.

  - Zorg er bovendien voor dat netwerk beveiligings groepen geen beperking voor uitgaande internet toegang hebben. Dit kan gebeuren bij sommige configuraties van netwerk beveiligings groepen. Uitgaande internet toegang (en DNS) is vereist voor het migreren van VM-extensies naar Azure Resource Manager.
  - Er bestaan twee versies van de BGInfo-extensie en deze worden versie 1 en 2 genoemd.  

      - Als de virtuele machine de BGInfo-extensie van versie 1 gebruikt, kunt u deze extensie ongewijzigd laten. Deze uitbrei ding wordt overgeslagen door de migratie-API. De BGInfo-extensie kan na de migratie worden toegevoegd.
      - Als de virtuele machine de BGInfo-extensie van de JSON-versie 2 gebruikt, is de virtuele machine gemaakt met behulp van de Azure Portal. De migratie-API bevat deze uitbrei ding in de migratie naar Azure Resource Manager, op voor verstand dat de agent werkt en uitgaande internet toegang (en DNS) heeft.

  - **Herstel optie 1**. Als u weet dat uw Vm's geen uitgaande internet toegang, een werkende DNS-service en werkende Azure-agents op de virtuele machines hebben, verwijdert u alle VM-extensies als onderdeel van de migratie voordat u het voor bereidt en installeert u de VM-extensies na de migratie opnieuw.
  - **Herstel optie 2**. Als VM-extensies te groot zijn voor een hindernis, is een andere optie het afsluiten/toewijzen van alle Vm's vóór migratie. Migreer de opgeheven Vm's en start ze vervolgens opnieuw op de Azure Resource Manager-zijde. Het voor deel hiervan is dat VM-extensies worden gemigreerd. Het nadeel is dat alle open bare virtuele Ip's die zijn gericht verloren gaan (dit kan een niet-starter zijn), en de virtuele machines die worden afgesloten, veroorzaken een veel grotere invloed op de werk toepassingen.

    > [!NOTE]
    > Als een Azure Security Center beleid is geconfigureerd voor de actieve Vm's die worden gemigreerd, moet het beveiligings beleid worden gestopt voordat de extensies worden verwijderd. anders wordt de extensie voor beveiligings bewaking automatisch opnieuw geïnstalleerd op de VM nadat deze is verwijderd.

- **Beschikbaarheids sets** : voor een virtueel netwerk (vNet) dat naar Azure Resource Manager moet worden gemigreerd, moet de klassieke implementatie (d.w.z. Cloud service) virtuele machines in één beschikbaarheidsset zijn, anders moeten de virtuele machines zich in een beschikbaarheidsset bevinden. Als er meer dan één beschikbaarheidsset in de Cloud service is ingesteld, is deze niet compatibel met Azure Resource Manager en stopt de migratie.  Daarnaast kunnen er geen virtuele machines in een beschikbaarheidsset worden opgegeven, en sommige Vm's niet in een beschikbaarheidsset. Om dit op te lossen, moet u uw Cloud service herstellen of anders in een andere volg orde zetten.  Plan dienovereenkomstig, omdat dit mogelijk tijdrovend is.

- **Implementaties van web-en werk rollen** -Cloud Services die web-en werk rollen bevatten, kunnen niet worden gemigreerd naar Azure Resource Manager. Als u de inhoud van uw web-en werk rollen wilt migreren, moet u de code zelf migreren naar de nieuwere PaaS-App Services (deze discussie valt buiten het bereik van dit document). Als u de web-of werk rollen wilt verlaten, maar de klassieke Vm's wilt migreren naar het Resource Manager-implementatie model, moeten de web-en werk rollen eerst worden verwijderd uit het virtuele netwerk voordat de migratie kan worden gestart.  Een typische oplossing is het verplaatsen van de web-of worker-instanties naar een afzonderlijk klassiek virtueel netwerk dat ook is gekoppeld aan een ExpressRoute-circuit. Maak in de eerste aanvraag voor opnieuw implementeren een nieuw klassiek virtueel netwerk, verplaats/Implementeer de web-en werk rollen naar dat nieuwe virtuele netwerk en verwijder vervolgens de implementaties van het virtuele netwerk dat wordt verplaatst. Er zijn geen code wijzigingen vereist. De nieuwe [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md) -mogelijkheid kan worden gebruikt voor het samen voegen van het klassieke virtuele netwerk met de web-en werk rollen en andere virtuele netwerken in dezelfde Azure-regio, zoals het virtuele netwerk dat wordt gemigreerd (nadat de migratie van het virtuele**netwerk is voltooid als gekoppelde virtuele netwerken niet kunnen worden gemigreerd**) Gezien de toevoeging van [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md), implementaties van web-en werk rollen kan nu eenvoudig worden verholpen en wordt de migratie naar Azure Resource Manager niet geblokkeerd.

- **Azure Resource Manager quota's** : Azure-regio's hebben afzonderlijke quota's/limieten voor zowel klassieke als Azure Resource Manager. Hoewel er in een migratie scenario nieuwe hardware niet wordt gebruikt *(we wisselen bestaande vm's uit van klassieke naar Azure Resource Manager)* , moeten Azure Resource Manager quota's nog steeds aanwezig zijn met voldoende capaciteit voordat de migratie kan worden gestart. Hieronder ziet u de belangrijkste limieten die we hebben gezien problemen veroorzaken.  Een quotum ondersteunings ticket openen om de limieten te verhogen.

    > [!NOTE]
    > Deze limieten moeten worden verhoogd in dezelfde regio als uw huidige omgeving om te worden gemigreerd.
    >

  - Netwerkinterfaces
  - Load Balancers
  - Openbare IP-adressen
  - Statische open bare Ip's
  - Cores
  - Netwerkbeveiligingsgroepen
  - Routetabellen

    U kunt de huidige Azure Resource Manager quota's controleren met behulp van de volgende opdrachten met de meest recente versie van Azure PowerShell.



    **Compute** *(kernen, beschikbaarheids sets)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Netwerk** *(virtuele netwerken, statische open bare IP-adressen, open bare IP-adressen, netwerk beveiligings groepen, netwerk interfaces, load balancers, route tabellen)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Opslag** *(opslag account)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Azure Resource Manager-API-beperkings limieten** : als u een grote voldoende omgeving hebt (bijvoorbeeld > 400 Vm's in een VNET) kunt u de standaard limieten voor de API-beperking voor schrijf bewerkingen (momenteel `1200 writes/hour`) in Azure Resource Manager aanraken. Voordat u met de migratie begint, moet u een ondersteunings ticket genereren om deze limiet voor uw abonnement te verg Roten.


- **VM-status time** -out voor inrichting: als een virtuele machine de status `provisioning timed out`heeft, moet deze vooraf-migratie worden opgelost. De enige manier om dit te doen is met downtime door de inrichting van de virtuele machine ongedaan te maken/opnieuw in te richten (verwijderen, de schijf te houden en de virtuele machine opnieuw te maken).

- **RoleStateUnknown-VM-status** : als migratie wordt gestopt vanwege een `role state unknown` fout bericht, inspecteert u de virtuele machine met behulp van de portal en controleert u of deze actief is. Deze fout wordt meestal na een paar minuten verwijderd (er is geen herstel vereist) en is vaak een tijdelijk type dat vaak wordt weer gegeven tijdens een virtuele machine `start`, `stop``restart` bewerkingen. **Aanbevolen procedure:** Voer de migratie na een paar minuten opnieuw uit.

- Het **infrastructuur cluster bestaat niet** . in sommige gevallen kunnen bepaalde vm's niet om verschillende oneven redenen worden gemigreerd. Een van deze bekende gevallen is als de virtuele machine onlangs is gemaakt (in de afgelopen week of dus) en is opgetreden in een Azure-cluster dat nog niet is ingericht voor Azure Resource Manager workloads.  Er wordt een fout bericht weer geven met de melding `fabric cluster does not exist` en de virtuele machine kan niet worden gemigreerd. Als er een aantal dagen wordt gewacht, wordt dit probleem doorgaans opgelost als het cluster binnenkort Azure Resource Manager ingeschakeld. Het is echter een onmiddellijke tijdelijke oplossing om de virtuele machine te `stop-deallocate`, vervolgens door te gaan met de migratie en de VM te starten in Azure Resource Manager na de migratie.

### <a name="pitfalls-to-avoid"></a>Valk uilen om te voor komen

- Doe geen sneltoetsen en laat de migraties valideren/voor bereid/afbreken droge uitvoering weg.
- Als dat niet het geval is, worden uw potentiële problemen duidelijk tijdens de stappen voor het valideren/voorbereiden/afbreken.

## <a name="migration"></a>Migratie

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en-afwegingen

U bent nu klaar omdat u de bekende problemen met uw omgeving hebt doorstaan.

Voor de echte migratie kunt u het volgende overwegen:

1. Het virtuele netwerk (kleinste eenheid van migratie) plannen en plannen met een hogere prioriteit.  Voer eerst de eenvoudige virtuele netwerken en de voortgang met de complexere virtuele netwerken uit.
2. De meeste klanten hebben andere omgevingen dan productie en productie.  Plan de productie als laatste.
3. **(Optioneel)** Een onderhouds downtime plannen met veel buffers als er onverwachte problemen optreden.
4. Communiceer met en stem mee met uw ondersteunings teams als er problemen optreden.

### <a name="patterns-of-success"></a>Patronen van slagen

De technische richt lijnen van de sectie _test omgeving van Lab_ moeten worden overwogen en verholpen vóór een echte migratie.  Bij een adequaat testen is de migratie eigenlijk een niet-gebeurtenis.  Voor productie omgevingen kan het handig zijn om aanvullende ondersteuning te bieden, zoals een vertrouwde micro soft-partner of micro soft premier-Services.

### <a name="pitfalls-to-avoid"></a>Valk uilen om te voor komen

Het niet volledig testen kan leiden tot problemen en vertraging tijdens de migratie.  

## <a name="beyond-migration"></a>Buiten de migratie

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en-afwegingen

Nu u zich in Azure Resource Manager bevindt, maximaliseert u het platform.  Lees het [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie over aanvullende voor delen.

Aandachtspunten:

- De migratie met andere activiteiten bundelen.  De meeste klanten kiezen voor een onderhouds venster voor de toepassing.  Als dat het geval is, kunt u deze downtime gebruiken om andere Azure Resource Manager mogelijkheden zoals versleuteling en migratie naar Managed Disks in te scha kelen.
- Ga naar de technische en zakelijke redenen voor Azure Resource Manager; Schakel de extra services in die alleen beschikbaar zijn op Azure Resource Manager die van toepassing zijn op uw omgeving.
- Modern uw omgeving met PaaS Services.

### <a name="patterns-of-success"></a>Patronen van slagen

Wees doel gericht op welke services u nu wilt inschakelen in Azure Resource Manager.  Veel klanten vinden het onderstaande aantrekkelijk voor hun Azure-omgevingen:

- [Access Control op basis van rollen](../../role-based-access-control/overview.md).
- [Azure Resource Manager sjablonen voor een eenvoudiger en meer bewaakte implementatie](../../azure-resource-manager/templates/overview.md).
- [Tags](../../azure-resource-manager/management/tag-resources.md).
- [Besturings element voor activiteit](../../azure-resource-manager/management/view-activity-logs.md)
- [Azure-beleid](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Valk uilen om te voor komen

Denk eraan dat u dit klassieke Azure Resource Manager hebt gestart voor migratie traject.  Wat zijn de oorspronkelijke zakelijke redenen? Hebt u de zakelijke reden gerealiseerd?


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migratie van klassieke en Resource Manager VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-classic-resource-manager-migration)
* [ExpressRoute-circuits en gekoppelde virtuele netwerken van het klassieke naar het Resource Manager-implementatie model migreren](https://docs.microsoft.com/azure/expressroute/expressroute-migration-classic-resource-manager)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
