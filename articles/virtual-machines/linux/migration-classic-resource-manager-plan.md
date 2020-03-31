---
title: Planning voor migratie van klassiek naar Azure Resource Manager
description: Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager
services: virtual-machines-linux
author: tanmaygore
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: ff829e9ffbd6d6ae0766998e62634ac873afc748
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066663"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager"></a>Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager

> [!IMPORTANT]
> Vandaag de dag gebruikt ongeveer 90% van de IaaS VM's [Azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) Vanaf 28 februari 2020 zijn klassieke VM's afgeschaft en op 1 maart 2023 volledig met pensioen gegaan. [Meer informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe het u beïnvloedt.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Hoewel Azure Resource Manager veel geweldige functies biedt, is het van cruciaal belang om uw migratiereis te plannen om ervoor te zorgen dat alles soepel verloopt. Als u tijd besteedt aan planning, zorgt u ervoor dat u geen problemen ondervindt tijdens het uitvoeren van migratieactiviteiten.

> [!NOTE]
> De volgende richtlijnen werden sterk bijgedragen door het Azure Customer Advisory-team en cloudsolution-architecten die met klanten werken aan het migreren van grote omgevingen. Als zodanig zal dit document blijven worden bijgewerkt als nieuwe patronen van succes ontstaan, dus kom terug van tijd tot tijd om te zien of er nieuwe aanbevelingen.

Er zijn vier algemene fasen van de migratiereis:

![Migratiefasen](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Plannen

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en afwegingen

Afhankelijk van de grootte van uw technische vereisten, geografische gebieden en operationele praktijken, u overwegen:

1. Waarom is Azure Resource Manager gewenst voor uw organisatie?  Wat zijn de zakelijke redenen voor een migratie?
2. Wat zijn de technische redenen voor Azure Resource Manager?  Welke (eventuele) aanvullende Azure-services wilt u gebruiken?
3. Welke toepassing (of sets van virtuele machines) is opgenomen in de migratie?
4. Welke scenario's worden ondersteund met de migratie-API?  Bekijk de [niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations).
5. Ondersteunen uw operationele teams nu toepassingen/VM's in zowel Classic als Azure Resource Manager?
6. Hoe wijzigt Azure Resource Manager (of helemaal niet) uw VM-implementatie-, beheer-, monitoring- en rapportageprocessen?  Moeten uw implementatiescripts worden bijgewerkt?
7. Wat is het communicatieplan om belanghebbenden (eindgebruikers, eigenaren van toepassingen en eigenaren van infrastructuur) te waarschuwen?
8. Afhankelijk van de complexiteit van de omgeving, moet er een onderhoudsperiode zijn waarin de toepassing niet beschikbaar is voor eindgebruikers en voor toepassingseigenaren?  Zo ja, voor hoe lang?
9. Wat is het trainingsplan om ervoor te zorgen dat belanghebbenden deskundig en bedreven zijn in Azure Resource Manager?
10. Wat is het programmamanagement- of projectmanagementplan voor de migratie?
11. Wat zijn de tijdlijnen voor de azure resource manager-migratie en andere gerelateerde technologieroutekaarten?  Zijn ze optimaal uitgelijnd?

### <a name="patterns-of-success"></a>Patronen van succes

Succesvolle klanten hebben gedetailleerde plannen waarbij de voorgaande vragen worden besproken, gedocumenteerd en bestuurd.  Zorg ervoor dat de migratieplannen breed worden gecommuniceerd aan sponsors en belanghebbenden.  Rust jezelf uit met kennis over je migratiemogelijkheden; het lezen van dit onderstaande migratiedocument wordt ten zeerste aanbevolen.

* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [CLI gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Communitytools voor het helpen bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Valkuilen te vermijden

- Niet te plannen.  De technologische stappen van deze migratie zijn bewezen en het resultaat is voorspelbaar.
- Ervan uitgaande dat het platform migratie-API ondersteunt, rekening zal houden met alle scenario's. Lees de [niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) om te begrijpen welke scenario's worden ondersteund.
- Geen planning van mogelijke uitval van toepassingen voor eindgebruikers.  Plan voldoende buffer om eindgebruikers adequaat te waarschuwen voor mogelijk niet-beschikbare toepassingstijd.


## <a name="lab-test"></a>Labtest

**Uw omgeving repliceren en een testmigratie doen**
  > [!NOTE]
  > Exacte replicatie van uw bestaande omgeving wordt uitgevoerd met behulp van een door de community bijgedragen tool die niet officieel wordt ondersteund door Microsoft Support. Daarom is het een **optionele** stap, maar het is de beste manier om problemen te achterhalen zonder uw productieomgevingen aan te raken. Als het gebruik van een door de community bijgedragen tool geen optie is, lees dan hieronder de aanbeveling Droogrennen valideren/voorbereiden/afbreken.
  >

  Het uitvoeren van een labtest van uw exacte scenario (rekenkracht, netwerken en opslag) is de beste manier om een soepele migratie te garanderen. Dit zal helpen ervoor te zorgen:

- Een geheel apart lab of een bestaande niet-productieomgeving om te testen. Wij raden een geheel apart lab dat herhaaldelijk kan worden gemigreerd en kan destructief worden gewijzigd.  Scripts voor het verzamelen/hydrateren van metadata van de echte abonnementen staan hieronder vermeld.
- Het is een goed idee om het lab te maken in een apart abonnement. De reden is dat het lab herhaaldelijk zal worden afgebroken, en met een aparte, geïsoleerde abonnement zal de kans dat iets echt zal krijgen per ongeluk verwijderd.

  Dit kan worden bereikt met behulp van de AsmMetadataParser tool. [Lees hier meer over deze tool](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Patronen van succes

De volgende waren problemen ontdekt in veel van de grotere migraties. Dit is geen uitputtende lijst en u moet verwijzen naar de [niet-ondersteunde functies en configuraties](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#unsupported-features-and-configurations) voor meer details. U deze technische problemen al dan niet tegenkomen, maar als u deze oplost voordat u een migratie probeert, zorgt dit voor een soepelere ervaring.

- **Doe een Droogloop valideren/voorbereiden/afbreken** - Dit is misschien wel de belangrijkste stap om het succes van de migratie van Classic naar Azure Resource Manager te garanderen. De migratie-API heeft drie hoofdstappen: Valideren, voorbereiden en vastleggen. Validate leest de status van uw klassieke omgeving en geeft een resultaat van alle problemen. Omdat er echter problemen kunnen bestaan in de Azure Resource Manager-stack, zal Validate niet alles opvangen. De volgende stap in het migratieproces, Prepare zal helpen deze problemen bloot te leggen. Voorbereiden verplaatst de metagegevens van Classic naar Azure Resource Manager, maar zal de verplaatsing niet vastleggen en verwijdert of wijzigt niets aan de klassieke kant. De droge run omvat het voorbereiden van de migratie, dan afbreken (**niet plegen**) de migratie voor te bereiden. Het doel van het valideren/voorbereiden/afbreken van dry run is om alle metagegevens in de Azure Resource Manager-stack te bekijken, te onderzoeken *(programmatisch of in Portal)* en te controleren of alles correct migreert en technische problemen doormaakt.  Het geeft u ook een gevoel van migratieduur, zodat u downtime dienovereenkomstig plannen.  Een valideren/voorbereiden/afbreken veroorzaakt geen uitvaltijd van de gebruiker; daarom is het niet storend voor het gebruik van toepassingen.
  - De onderstaande items moeten worden opgelost voor de droge run, maar een droge run test zal ook veilig spoelen deze voorbereiding stappen als ze worden gemist. Tijdens bedrijfsmigratie hebben we de dry run een veilige en waardevolle manier gevonden om migratiegereedheid te garanderen.
  - Wanneer de voorbereiding wordt uitgevoerd, wordt het besturingsvlak (Azure-beheerbewerkingen) vergrendeld voor het hele virtuele netwerk, zodat er geen wijzigingen kunnen worden aangebracht in VM-metagegevens tijdens het valideren/voorbereiden/afbreken.  Maar anders wordt elke toepassingsfunctie (RD, VM-gebruik, enz.) niet beïnvloed.  Gebruikers van de VM's zullen niet weten dat de droge run wordt uitgevoerd.

- **Express Route Circuits en VPN**. Momenteel kunnen Express Route Gateways met autorisatiekoppelingen niet worden gemigreerd zonder downtime. Zie [ExpressRoute-circuits migreren en bijbehorende virtuele netwerken van de klassieker naar het implementatiemodel Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md)voor de tijdelijke oplossing.

- **VM-extensies** - Virtual Machine-extensies zijn mogelijk een van de grootste obstakels voor het migreren van met VM's. Herstel van VM-extensies kan meer dan 1-2 dagen duren, dus plan dienovereenkomstig.  Er is een werkende Azure-agent nodig om de VM-extensiestatus van het uitvoeren van VM's te rapporteren. Als de status zo slecht terugkomt voor een draaiende VM, zal dit migratie stoppen. De agent zelf hoeft niet in werkende staat om migratie mogelijk te maken, maar als er extensies op de VM bestaan, dan is zowel een werkende agent als uitgaande internetverbinding (met DNS) nodig om migratie vooruit te helpen.
  - Als de verbinding met een DNS-server verloren gaat tijdens de migratie, gaan alle VM-extensies behalve BGInfo v1. \* moeten eerst uit elke vm worden verwijderd voordat de migratie wordt voorbereid en vervolgens weer aan de VM worden toegevoegd na de migratie van Azure Resource Manager.  **Dit is alleen voor VM's die worden uitgevoerd.**  Als de VM's worden gestopt met deallocatie, hoeven VM-extensies niet te worden verwijderd. **Let op:** Veel extensies zoals Azure diagnostics en security center monitoring zullen zichzelf opnieuw installeren na migratie, dus het verwijderen ervan is geen probleem.
  - Zorg er bovendien voor dat netwerkbeveiligingsgroepen de uitgaande internettoegang niet beperken. Dit kan gebeuren met sommige configuraties van netwerkbeveiligingsgroepen. Uitgaande internettoegang (en DNS) is nodig om VM-extensies te migreren naar Azure Resource Manager.
  - Er zijn twee versies van de BGInfo extensie: v1 en v2.  Als de VM is gemaakt met behulp van de Azure-portal of PowerShell, heeft de VM waarschijnlijk de v1-extensie. Deze extensie hoeft niet te worden verwijderd en wordt overgeslagen (niet gemigreerd) door de migratie-API. Als de klassieke VM echter is gemaakt met de nieuwe Azure-portal, wordt waarschijnlijk de op JSON gebaseerde v2-versie van BGInfo gebruikt, die kan worden gemigreerd naar Azure Resource Manager, op voorwaarde dat de agent werkt en uitgaande internettoegang (en DNS) heeft.
  - **Hersteloptie 1**. Als u weet dat uw VM's geen uitgaande internettoegang, een werkende DNS-service en werkende Azure-agents op de VM's hebben, verwijdert u vervolgens alle VM-extensies als onderdeel van de migratie voordat u zich voorbereidt en installeert u de VM-extensies opnieuw na migratie.
  - **Hersteloptie 2**. Als VM-extensies een te grote hindernis zijn, is een andere optie om alle VM's vóór migratie af te sluiten/uit te wijzen. Migreer de deal-vm's en start ze opnieuw op de azure resource manager-kant. Het voordeel hier is dat VM-extensies zullen migreren. Het nadeel is dat alle publieke geconfronteerd met virtuele IP's zal verloren gaan (dit kan een niet-starter), en uiteraard de VM's zal afsluiten waardoor een veel grotere impact op werkende toepassingen.

    > [!NOTE]
    > Als een Azure Security Center-beleid is geconfigureerd tegen de lopende VM's die worden gemigreerd, moet het beveiligingsbeleid worden gestopt voordat extensies worden verwijderd, anders wordt de beveiligingscontroleextensie automatisch opnieuw geïnstalleerd op de VM nadat deze is verwijderd.

- **Beschikbaarheidssets** - Om een virtueel netwerk (vNet) te migreren naar Azure Resource Manager, moet de Classic-implementatie (d.w.z. cloudservice) die VM's bevatten, allemaal in één beschikbaarheidsset zijn ingesteld of mogen de VM's niet allemaal in een beschikbaarheidsset zijn ingesteld. Als er meer dan één beschikbaarheid is ingesteld in de cloudservice, is het niet compatibel met Azure Resource Manager en wordt de migratie gestopt.  Bovendien kunnen sommige VM's niet in een beschikbaarheidsset staan en sommige VM's niet in een beschikbaarheidsset. Om dit op te lossen, moet u uw cloudservice herstellen of herschikken.  Plan dienovereenkomstig als dit kan tijdrovend zijn.

- **Web-/Worker-rolimplementaties** - Cloudservices die web- en werknemersrollen bevatten, kunnen niet migreren naar Azure Resource Manager. De web-/werknemersrollen moeten eerst uit het virtuele netwerk worden verwijderd voordat de migratie kan worden gestart.  Een typische oplossing is om web/worker rolexemplaren gewoon te verplaatsen naar een afzonderlijk virtueel Classic-netwerk dat ook is gekoppeld aan een ExpressRoute-circuit, of om de code te migreren naar nieuwere PaaS App Services (deze discussie valt buiten het bereik van dit document). Maak in de eerste aanvraag een nieuw virtueel netwerk voor klassieke implementaties, verplaatst/reimplementeert de web-/werknemersrollen naar dat nieuwe virtuele netwerk en verwijdert u vervolgens de implementaties van het virtuele netwerk dat wordt verplaatst. Er zijn geen codewijzigingen vereist. De nieuwe [Virtual Network Peering-mogelijkheid](../../virtual-network/virtual-network-peering-overview.md) kan worden gebruikt om samen te kijken op het klassieke virtuele netwerk met de web/werknemerrollen en andere virtuele netwerken in dezelfde Azure-regio, zoals het virtuele netwerk dat wordt gemigreerd (nadat de migratie van**virtuele netwerken is voltooid omdat virtuele netwerken met één peer-ed kunnen worden gemigreerd),** waardoor dezelfde mogelijkheden kunnen worden geboden zonder prestatieverlies en geen latentie/bandbreedtesancties. Gezien de toevoeging van [Virtual Network Peering](../../virtual-network/virtual-network-peering-overview.md)kunnen web-/worker-rolimplementaties nu eenvoudig worden beperkt en de migratie naar Azure Resource Manager niet blokkeren.

- **Azure Resource Manager-quota** - Azure-regio's hebben afzonderlijke quota/limieten voor zowel Classic als Azure Resource Manager. Hoewel in een migratiescenario nieuwe hardware niet wordt verbruikt *(we wisselen bestaande VM's om van Classic naar Azure Resource Manager),* moeten Azure Resource Manager-quota nog steeds met voldoende capaciteit zijn ingevoerd voordat de migratie kan beginnen. Hieronder staan de belangrijkste limieten die we hebben gezien problemen veroorzaken.  Open een quotaondersteuningsticket om de limieten te verhogen.

    > [!NOTE]
    > Deze limieten moeten worden verhoogd in dezelfde regio als uw huidige omgeving om te worden gemigreerd.
    >

  - Netwerkinterfaces
  - Load balancers
  - Openbare IP-adressen
  - Statische openbare IP's
  - Kernen
  - Netwerkbeveiligingsgroepen
  - Routetabellen

    U uw huidige Azure Resource Manager-quota controleren met behulp van de volgende opdrachten met de nieuwste versie van Azure CLI.

    **Compute** *(Cores, Beschikbaarheidssets)*

    ```azurecli
    az vm list-usage -l <azure-region> -o jsonc
    ```

    **Netwerk** *(virtuele netwerken, statische openbare IP's, openbare IP's, netwerkbeveiligingsgroepen, netwerkinterfaces, load balancers, routetabellen)*

    ```azurecli
    az network list-usages -l <azure-region> -o jsonc
    ```

    **Opslag** *(opslagaccount)*

    ```azurecli
    az storage account show-usage
    ```

- **Azure Resource Manager API-beperkingslimieten** - Als u een omgeving hebt die groot genoeg is (bijv. > 400 VM's in een VNET), u de standaard API-beperkingslimieten voor schrijft (momenteel **1200 schrijft/uur)** in Azure Resource Manager bereiken. Voordat u met de migratie begint, moet u een ondersteuningsticket verhogen om deze limiet voor uw abonnement te verhogen.

- **VM-status inrichten** - Als een VM de status heeft van het inrichten van **een time-out,** moet dit worden opgelost vóóv de migratie. De enige manier om dit te doen is met downtime door de VM te deprovisioneren/opnieuw te inrichten (de schijf verwijderen, de schijf behouden en de VM opnieuw maken).

- **RoleStateUnknown VM-status** - Als de migratie wordt gestopt als gevolg van een foutbericht waarin een **rolstatus staat,** controleert u de VM met behulp van de portal en controleert u of deze wordt uitgevoerd. Deze fout zal meestal verdwijnen op zijn eigen (geen herstel vereist) na een paar minuten en is vaak een voorbijgaand type vaak gezien tijdens een Virtual Machine **start,** **stop**, **opnieuw opstarten.** **Aanbevolen praktijk:** probeer migratie na een paar minuten opnieuw.

- **Fabric Cluster bestaat niet** - In sommige gevallen kunnen bepaalde VM's om verschillende oneven redenen niet worden gemigreerd. Een van deze bekende gevallen is als de VM onlangs is gemaakt (in de afgelopen week of zo) en toevallig een Azure-cluster dat nog niet is uitgerust voor Azure Resource Manager-workloads land.  U krijgt een foutmelding dat zegt **dat fabriccluster niet bestaat** en dat de VM niet kan worden gemigreerd. Wachten een paar dagen zal meestal dit specifieke probleem op te lossen als het cluster zal binnenkort Azure Resource Manager ingeschakeld. Een directe oplossing is `stop-deallocate` echter de VM, ga vervolgens verder met migratie en start de VM na het migreren opnieuw in Azure Resource Manager.

### <a name="pitfalls-to-avoid"></a>Valkuilen te vermijden

- Neem geen snelkoppelingen en laat de migraties over de droogloop niet weg.
- De meeste, zo niet alle, van uw potentiële problemen zullen opduiken tijdens de stappen valideren/voorbereiden/afbreken.

## <a name="migration"></a>Migratie

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en afwegingen

Nu bent u klaar omdat u hebt gewerkt door de bekende problemen met uw omgeving.

Voor de echte migraties u overwegen:

1. Plan en plan het virtuele netwerk (kleinste eenheid migratie) met toenemende prioriteit.  Doe de eenvoudige virtuele netwerken eerst, en de vooruitgang met de meer gecompliceerde virtuele netwerken.
2. De meeste klanten hebben niet-productie- en productieomgevingen.  De productie als laatste plannen.
3. **(OPTIONEEL)** Plan een onderhoudsstilstand met veel buffer in het geval er zich onverwachte problemen voordoen.
4. Communiceer met en stem af met uw ondersteuningsteams voor het geval er problemen ontstaan.

### <a name="patterns-of-success"></a>Patronen van succes

De technische richtlijnen van de sectie Lab Test hierboven moeten worden overwogen en beperkt voorafgaand aan een echte migratie.  Met adequate testen, de migratie is eigenlijk een non-event.  Voor productieomgevingen kan het handig zijn om extra ondersteuning te hebben, zoals een vertrouwde Microsoft-partner of Microsoft Premier-services.

### <a name="pitfalls-to-avoid"></a>Valkuilen te vermijden

Niet volledig testen kan problemen en vertraging in de migratie veroorzaken.  

## <a name="beyond-migration"></a>Beyond Migratie

### <a name="technical-considerations-and-tradeoffs"></a>Technische overwegingen en afwegingen

Nu u zich in Azure Resource Manager bevindt, maximaliseert u het platform.  Lees het [overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie over extra voordelen.

Dingen om te overwegen:

- Bundeling van de migratie met andere activiteiten.  De meeste klanten kiezen voor een applicatie onderhoudsvenster.  Als dat het zo is, u deze downtime gebruiken om andere Azure Resource Manager-mogelijkheden in te schakelen, zoals versleuteling en migratie naar beheerde schijven.
- Bekijk opnieuw de technische en zakelijke redenen voor Azure Resource Manager; de extra services inschakelen die alleen beschikbaar zijn in Azure Resource Manager en die van toepassing zijn op uw omgeving.
- Moderniseer uw omgeving met PaaS-services.

### <a name="patterns-of-success"></a>Patronen van succes

Wees doelgericht op welke services u nu wilt inschakelen in Azure Resource Manager.  Veel klanten vinden onderstaande aantrekkelijke voor hun Azure-omgevingen:

- [Op rollen gebaseerd toegangscontrole .](../../role-based-access-control/overview.md)
- [Azure Resource Manager-sjablonen voor eenvoudigere en meer gecontroleerde implementatie.](../../azure-resource-manager/templates/overview.md)
- [Tags](../../azure-resource-manager/management/tag-resources.md).
- [Activiteitscontrole](../../azure-resource-manager/management/view-activity-logs.md)
- [Azure-beleid](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Valkuilen te vermijden

Onthoud waarom u deze migratiereis van Classic naar Azure Resource Manager bent gestart.  Wat waren de oorspronkelijke zakelijke redenen? Heb je de zakelijke reden bereikt?


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van platformondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [PowerShell gebruiken om IaaS-resources te migreren van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Communitytools voor het helpen bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](../windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Bekijk de meest voorkomende migratiefouten](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Bekijk de meest gestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
