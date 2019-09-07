---
title: Overzicht voor ontwikkelaars | Microsoft Docs
description: Informatie over de functies van de Batch-service en de API's ervan, vanuit het standpunt van ontwikkelaars.
services: batch
documentationcenter: .net
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: 416b95f8-2d7b-4111-8012-679b0f60d204
ms.service: batch
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-compute
ms.date: 08/29/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bd630fec16ddfb269ead5f1f62af882f52501a86
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390486"
---
# <a name="develop-large-scale-parallel-compute-solutions-with-batch"></a>Grootschalige parallelle rekenoplossingen ontwikkelen met Batch

In dit overzicht van de kernonderdelen van de Azure Batch-service worden de primaire servicefuncties en resources besproken die Batch-ontwikkelaars kunnen gebruiken voor het bouwen van grootschalige parallelle rekenoplossingen.

Of u nu een gedistribueerde reken kundige toepassing of service ontwikkelt die directe [rest API][batch_rest_api] -aanroepen uitgeeft of een van de [batch-sdk's](batch-apis-tools.md#azure-accounts-for-batch-development)gebruikt, u maakt gebruik van veel van de resources en functies die in dit artikel worden besproken.

> [!TIP]
> Zie [Basisbeginselen van Azure Batch](batch-technical-overview.md) voor een introductie op hoger niveau van de Batch-service. Zie ook de meest recente [Batch-service-updates](https://azure.microsoft.com/updates/?product=batch).
>
>

## <a name="batch-service-workflow"></a>Werkstroom van de Batch-service

De volgende werkstroom op hoog niveau is gangbaar voor bijna alle toepassingen en services die gebruikmaken van de Batch-service voor de verwerking van parallelle workloads:

1. Upload de **gegevens bestanden** die u wilt verwerken naar een [Azure Storage][azure_storage] -account. Batch bevat ingebouwde ondersteuning voor toegang tot Azure-blobopslag en uw taken kunnen deze bestanden downloaden naar [rekenknooppunten](#compute-node) wanneer de taken worden uitgevoerd.
2. Upload de **toepassingsbestanden** die uw taken gaan uitvoeren. Deze bestanden kunnen binaire bestanden of scripts en ervan afhankelijke elementen zijn, en worden uitgevoerd door de taken in uw jobs. Uw taken kunnen deze bestanden downloaden uit uw opslagaccount, maar u kunt ook de functie voor [toepassingspakketten](#application-packages) van Batch voor het beheren en implementeren van toepassingen gebruiken.
3. Maak een [pool](#pool) van rekenknooppunten. Wanneer u een pool maakt, geeft u het aantal rekenknooppunten voor de pool, de grootte en het besturingssysteem op. Wanneer elke taak in de job wordt uitgevoerd, wordt deze voor uitvoering toegewezen aan een van de knooppunten in uw pool.
4. Maak een [job](#job). Een job beheert een verzameling van taken. U koppelt elke job aan een specifieke pool waar de taken van die job worden uitgevoerd.
5. Voeg [taken](#task) toe aan de job. Elke taak voert de toepassing of het script uit die door u is geüpload voor het verwerken van de uit uw opslagaccount gedownloade gegevensbestanden. Wanner een taak is voltooid, kunt u de uitvoer ervan uploaden naar Azure Storage.
6. Bewaak de voortgang van de taak en haal de taakuitvoer op uit Azure Storage.

In de volgende secties worden de bronnen van de batch besproken waarmee u uw gedistribueerde reken kundige scenario kunt uitvoeren.

> [!NOTE]
> U hebt een [Batch-account](#account) nodig om de Batch-service te kunnen gebruiken. Voor de meeste batch-oplossingen wordt ook een gekoppeld [Azure Storage][azure_storage] account gebruikt voor opslag en ophalen van bestanden.
>
>

## <a name="batch-service-resources"></a>Batch-serviceresources

Sommige van de volgende resources (accounts, rekenknooppunten, pools, jobs en taken) zijn vereist zijn voor alle oplossingen die gebruikmaken van de Batch-service. Andere resources, zoals taakplanningen en toepassingspakketten, zijn nuttige, maar optionele functies.

* [Account](#account)
* [Rekenknooppunt](#compute-node)
* [Pool](#pool)
* [Job](#job)
  * [Jobplanningen](#scheduled-jobs)
* [Taak](#task)
  * [Begintaak](#start-task)
  * [Jobbeheertaak](#job-manager-task)
  * [Jobvoorbereidingstaken en jobvrijgevingstaken](#job-preparation-and-release-tasks)
  * Taken met meerdere instanties (MPI)
  * [Taakafhankelijkheden](#task-dependencies)
* [Toepassingspakketten](#application-packages)

## <a name="account"></a>Account

Een Batch-account is een uniek geïdentificeerde entiteit in de Batch-service. Alle verwerkingen zijn gekoppeld aan een Batch-account.

U kunt een Azure Batch-account maken met de [Azure Portal](batch-account-create-portal.md) of via een programma, zoals met de [Batch Management .NET-bibliotheek](batch-management-dotnet.md). Wanneer u het account maakt, kunt u een gekoppeld Azure-opslagaccount maken waarin u taakgerelateerde invoer- en uitvoergegevens of toepassingen kunt opslaan.

U kunt in één Batch-account meerdere Batch-workloads uitvoeren of uw workloads verdelen over Batch-accounts in hetzelfde abonnement maar in verschillende Azure-regio's.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

## <a name="azure-storage-account"></a>Azure Storage-account

Bij de meeste Batch-oplossingen wordt gebruikgemaakt van Azure Storage om resourcebestanden en uitvoerbestanden op te slaan. Uw Batch-taken (inclusief standaardtaken, begintaken, jobvoorbereidingstaken en jobvrijgevingstaken) geven bijvoorbeeld gewoonlijk bronbestanden op die zich in een opslagaccount bevinden.

Batch biedt ondersteuning voor de volgende typen Azure Storage-account:

* Accounts voor algemeen gebruik v2 (GPv2-accounts) 
* Accounts voor algemeen gebruik v1 (GPv1-accounts)
* Blob-opslagaccounts (momenteel ondersteund voor toepassingen in de configuratie van de virtuele machine)

Zie [Overzicht van Azure-opslagaccount](../storage/common/storage-account-overview.md) voor meer informatie over opslagaccounts.

U kunt een opslagaccount koppelen aan uw Batch-account. Dit kunt u doen tijdens het maken van het Batch-account of later. Denk bij het kiezen van een opslagaccount aan de kosten- en prestatievereisten. Het GPv2-account en het Blob Storage-account bieden, vergeleken met een GPv1-account, bijvoorbeeld hogere [limieten voor capaciteit en schaalbaarheid](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). (Neem contact op met Azure-ondersteuning om een hogere opslaglimiet aan te vragen.) Met deze accountopties kunnen de prestaties worden verbeterd van Batch-oplossingen die een groot aantal parallelle taken bevatten die worden gelezen of geschreven vanuit het opslagaccount.

## <a name="compute-node"></a>Rekenknooppunt

Een rekenknooppunt is een virtuele machine (VM) of cloudservice-VM van Azure die aan een specifiek deel van de workload van uw toepassing is toegewezen. De grootte van een knooppunt bepaalt het aantal CPU-kernen, de geheugencapaciteit en de grootte van het lokale bestandssysteem die aan het knooppunt worden toegewezen. U kunt Pools van Windows-of Linux-knoop punten maken met behulp van Azure Cloud Services, installatie kopieën van de [azure virtual machines Marketplace][vm_marketplace]of aangepaste installatie kopieën die u voorbereidt. Zie de volgende sectie ([Pool](#pool)) voor meer informatie over deze opties.

Knooppunten kunnen elk uitvoerbaar bestand of script uitvoeren dat wordt ondersteund door de besturingssysteemomgeving van het knooppunt. Uitvoer bare bestanden of \*scripts zijn. \*exe-, \*. cmd-,. bat-en Power shell-scripts voor Windows--en binaire bestanden, shell en python-scripts voor Linux.

Alle rekenknooppunten in Batch omvatten ook:

* Een standaard[mapstructuur](#files-and-directories) en daaraan gekoppelde [omgevingsvariabelen](#environment-settings-for-tasks) die beschikbaar zijn voor verwijzing door taken.
* **Firewall**instellingen die zijn geconfigureerd om de toegang te beheren.
* [Externe toegang](#connecting-to-compute-nodes) voor Windows-knooppunten (Remote Desktop Protocol (RDP)) en Linux-knooppunten (Secure Shell (SSH)).

## <a name="pool"></a>Pool

Een pool is een verzameling knooppunten waarop uw toepassing wordt uitgevoerd. De pool kan door u handmatig worden gemaakt of automatisch door de Batch-service worden gemaakt wanneer u het werk opgeeft dat moet worden uitgevoerd. U kunt een pool maken en beheren die voldoet aan de resourcevereisten van uw toepassing. Een pool kan alleen worden gebruikt door de Batch-account waarin deze is gemaakt. Een Batch-account kan meer dan één pool hebben.

Azure Batch-groepen worden gebouwd boven op het kernrekenplatform van Azure. Deze bieden grootschalige toewijzing, installatie van toepassingen, gegevensdistributie, statuscontrole en flexibele aanpassing van het aantal rekenknooppunten binnen een pool ([vergroten/verkleinen](#scaling-compute-resources)).

Aan elk knooppunt dat aan een pool wordt toegevoegd, wordt een unieke naam en een uniek IP-adres toegewezen. Wanneer een knooppunt uit een pool wordt verwijderd, gaan alle wijzigingen in het besturingssysteem of de bestanden verloren. De naam en het IP-adres van het verwijderde knooppunt worden vrijgegeven voor toekomstig gebruik. Wanneer een knooppunt een pool verlaat, is de levensduur ervan beëindigd.

Wanneer u een pool maakt, kunt u de volgende kenmerken opgeven:

* Besturingssysteem en versie van rekenknooppunt
* Type rekenknooppunt en beoogd aantal knooppunten
* Grootte van de rekenknooppunten
* Beleid voor vergroten/verkleinen
* Taakplanningsbeleid
* Communicatiestatus voor rekenknooppunten
* Begintaken voor rekenknooppunten
* Toepassingspakketten
* Netwerkconfiguratie

Deze instellingen worden nader beschreven in de volgende secties.

> [!IMPORTANT]
> Batch-accounts hebben een standaardquotum dat het aantal kernen in een Batch-account beperkt. Het aantal kernen komt overeen met het aantal rekenknooppunten. U vindt de standaardquota en instructies voor het [verhogen van een quotum](batch-quota-limit.md#increase-a-quota) in [Quotas and limits for the Azure Batch service](batch-quota-limit.md) (Quota en limieten voor de Azure Batch-service). Als een pool niet het beoogde aantal knooppunten bereikt, ligt dit mogelijk aan het kernquotum.
>

### <a name="compute-node-operating-system-and-version"></a>Besturingssysteem en versie van rekenknooppunt

Wanneer u een Batch-pool maakt, kunt u de configuratie van de Virtuele Azure-machine opgeven en het type besturingssysteem dat u op elk rekenknooppunt in de pool wilt uitvoeren. De volgende twee typen configuraties zijn beschikbaar in Batch:

* De **virtuele-machine configuratie**, waarmee wordt aangegeven dat de pool bestaat uit virtuele Azure-machines. Deze virtuele machines kunnen worden gemaakt met Linux- of Windows-installatiekopieën.

    Wanneer u een pool maakt op basis van de virtuele-machineconfiguratie, moet u niet alleen de grootte van de knooppunten opgeven en de bron van de installatiekopieën waarmee u ze hebt gemaakt, maar ook de **verwijzing naar de installatiekopie van de virtuele machine** en de Batch-**knooppuntagent-SKU** die op de knooppunten moet worden geïnstalleerd. Zie [Linux-rekenknooppunten in Azure Batch-pools inrichten](batch-linux-nodes.md) voor meer informatie over het opgeven van deze pooleigenschappen. U kunt desgewenst een of meer lege gegevensschijven koppelen aan pool-VM's die zijn gemaakt op basis van Marketplace-installatiekopieën of gegevensschijven opnemen in aangepaste installatiekopieën die worden gebruikt voor het maken van de virtuele machines. Wanneer u gegevens schijven inneemt, moet u de schijven koppelen en Format teren vanuit een VM om ze te gebruiken.

* De **configuratie**van de Cloud Services, waarmee wordt aangegeven dat de groep bestaat uit Azure Cloud Services knoop punten. Cloud Services biedt *alleen*Windows-reken knooppunten.

    Beschikbare besturingssystemen voor Cloud Services-configuratiepools worden weergegeven in de [Azure-compatibiliteitsmatrix voor releases van gastbesturingssystemen en SDK’s](../cloud-services/cloud-services-guestos-update-matrix.md). Wanneer u een pool maakt die Cloud Services-knooppunten bevat, moet u de knooppuntgrootte en het bijbehorende *type besturingssysteem* opgeven. Cloud Services wordt sneller geïmplementeerd in azure dan virtuele machines waarop Windows wordt uitgevoerd. Als u pools met Windows-rekenknooppunten wilt, zult u merken dat Cloud Services prestatievoordelen biedt wat de implementatietijd betreft.

    * Het *type besturingssysteem* is ook bepalend voor de versies van .NET die samen met het besturingssysteem zijn geïnstalleerd.
    * Net als bij werkrollen in Cloud Services kan het *type besturingssysteem* worden opgegeven (zie [Overzicht van Cloud Services](../cloud-services/cloud-services-choose-me.md) voor meer informatie over werkrollen).
    * Net als bij werkrollen verdient het aanbeveling om `*` op te geven voor de *versie van het besturingssysteem*, zodat de knooppunten automatisch worden bijgewerkt en er geen werk vereist is om tegemoet te komen aan nieuwe versies. Er wordt voornamelijk voor een specifieke versie van een besturingssysteem gekozen om ervoor te zorgen dat toepassingen compatibel blijven, en om compatibiliteitstests met eerdere versies te kunnen uitvoeren alvorens toe te staan dat de versie mag worden bijgewerkt. Na de validatie kan de *versie van het besturingssysteem* voor de pool worden bijgewerkt en kan de nieuwe installatiekopie van het besturingssysteem worden geïnstalleerd. Elke actieve taak wordt onderbroken en opnieuw in de wachtrij geplaatst.

Wanneer u een pool maakt, moet u afhankelijk van het besturingssysteem van de basisinstallatiekopie van uw VHD de juiste **nodeAgentSkuId** selecteren. U kunt een toewijzing van beschik bare SKU-Id's van de knoop punt agent ophalen voor de bijbehorende verwijzingen naar de installatie kopie van het besturings systeem door de [lijst ondersteunde sku's-bewerking van knoop punt agent](https://docs.microsoft.com/rest/api/batchservice/list-supported-node-agent-skus)

#### <a name="custom-images-for-virtual-machine-pools"></a>Aangepaste installatiekopieën voor VM-pools

Als u een aangepaste installatiekopie wilt maken, moet u de kopie voorbereiden door deze te generaliseren. Zie [How to create an image of a virtual machine or VHD ](../virtual-machines/linux/capture-image.md) (Een installatiekopie van een virtuele machine of VHD maken) voor meer informatie over het maken van aangepaste Linux-installatiekopieën van virtuele Azure-machines. Zie [Create a managed image of a generalized VM in Azure](../virtual-machines/windows/capture-image-resource.md) (Een beheerde VM-installatiekopie maken van een gegeneraliseerde VM in Azure) voor meer informatie over het maken van aangepaste Windows-installatiekopieën op basis van Azure VM's. 

Zie [Use a custom image to create a pool of virtual machines](batch-custom-images.md) (Een aangepaste installatiekopie gebruiken om een pool van virtuele machines te maken) voor gedetailleerde vereisten en stappen.

#### <a name="container-support-in-virtual-machine-pools"></a>Ondersteuning voor containers in virtuele machine-pools

Bij het maken van een virtuele machine-pool met de Batch-API's, kunt u de pool instellen voor het uitvoeren van taken in Docker-containers. Momenteel moet u de pool maken met behulp van een installatiekopie die ondersteuning biedt voor Docker-containers. Gebruik Windows Server 2016 Datacenter met de Containers-installatiekopie van Azure Marketplace of geef een aangepaste VM-installatiekopie op die Docker Community Edition of Enterprise Edition en alle vereiste stuurprogramma's bevat. De poolinstellingen moeten een [containerconfiguratie](/rest/api/batchservice/pool/add) bevatten die containerinstallatiekopieën naar de virtuele machines kopieert wanneer de pool wordt gemaakt. Taken die worden uitgevoerd op de pool kunnen vervolgens verwijzen naar de containerinstallatiekopieën en uitvoeringsopties voor containers.

Zie voor meer informatie [Docker-containertoepassingen uitvoeren op Azure Batch](batch-docker-container-workloads.md).

## <a name="compute-node-type-and-target-number-of-nodes"></a>Type rekenknooppunt en beoogd aantal knooppunten

Wanneer u een pool maakt, kunt u opgeven welke typen rekenknooppunten u wilt gebruiken en het beoogde aantal. Er zijn twee soorten rekenknooppunten:

* **Toegewezen rekenknooppunten.** Toegewezen rekenknooppunten zijn gereserveerd voor uw workloads. Ze zijn duurder dan de knooppunten met lage prioriteit, maar ze worden gegarandeerd nooit verschoven.

* **Rekenknooppunten met lage prioriteit.** Knooppunten met lage prioriteit profiteren van de overtollige capaciteit in Azure om uw Batch-workloads uit te voeren. Knoop punten met een lage prioriteit zijn minder duur per uur dan toegewezen knoop punten, en zorgen ervoor dat werk belastingen een aanzienlijke reken kracht vereisen. Zie voor meer informatie [VM's met lage prioriteit gebruiken met Batch](batch-low-pri-vms.md).

    Rekenknooppunten met lage prioriteit kunnen worden verschoven wanneer Azure onvoldoende overtollige capaciteit heeft. Als een knooppunt wordt verschoven tijdens het uitvoeren van taken, worden de taken opnieuw ingepland en uitgevoerd zodra er weer een rekenknooppunt beschikbaar is. Knooppunten met lage prioriteit zijn een goede optie voor workloads waarbij de voltooiingstijd van de taak flexibel is en het werk over veel knooppunten wordt verdeeld. Voordat u besluit om knoop punten met lage prioriteit voor uw scenario te gebruiken, moet u ervoor zorgen dat het werk dat verloren gaat als gevolg van pre-verval, mini maal en eenvoudig opnieuw kan worden gemaakt.

U kunt zowel rekenknooppunten met lage prioriteit als toegewezen rekenknooppunten hebben in dezelfde groep. Elk type knooppunt, &mdash; met lage prioriteit of toegewezen &mdash; is ingesteld op een eigen doel. Hier kunt u het gewenste aantal knooppunten opgeven.

Het aantal rekenknooppunten wordt aangeduid als *beoogd* omdat de pool in sommige gevallen mogelijk niet het gewenste aantal knooppunten bereikt. Een pool kan het doel bijvoorbeeld mogelijk niet bereiken als het eerst het [quotum voor kernen](batch-quota-limit.md) voor uw Batch-account bereikt. Het is ook mogelijk dat de groep het doel niet bereikt als u een formule voor automatisch schalen hebt toegepast op de groep die het maximum aantal knoop punten beperkt.

Zie [Batch-prijzen](https://azure.microsoft.com/pricing/details/batch/) voor informatie over prijzen voor rekenknooppunten met lage prioriteit en toegewezen rekenknooppunten.

### <a name="size-of-the-compute-nodes"></a>Grootte van de rekenknooppunten

Wanneer u een Azure Batch-groep maakt, kunt u kiezen uit bijna alle reeksen en grootten virtuele machines die beschikbaar zijn in Azure. Azure biedt verscheidene VM-grootten voor verschillende workloads, met inbegrip van VM-grootten die speciaal zijn bedoeld voor [HPC](../virtual-machines/linux/sizes-hpc.md) of [GPU](../virtual-machines/linux/sizes-gpu.md). 

Zie [Choose a VM size for compute nodes in an Azure Batch pool](batch-pool-vm-sizes.md) (Een VM-grootte voor rekenknooppunten in een Azure Batch-groep kiezen) voor meer informatie.

### <a name="scaling-policy"></a>Beleid voor vergroten/verkleinen

Voor dynamische workloads kunt u een formule voor automatisch [schalen](#scaling-compute-resources) schrijven en Toep assen op een groep. De Batch-service evalueert de formule periodiek en past het aantal knooppunten in de pool aan op basis van verschillende pool-, job- en taakparameters die u kunt opgeven.

### <a name="task-scheduling-policy"></a>Taakplanningsbeleid

De configuratieoptie [Maximumaantal taken per knooppunt](batch-parallel-node-tasks.md) bepaalt het maximumaantal taken dat parallel kan worden uitgevoerd op elk rekenknooppunt in de pool.

Bij de standaardconfiguratie wordt er op een knooppunt één taak tegelijk uitgevoerd, maar er zijn scenario's waarin het nuttig is om op een knooppunt twee of meer taken tegelijk uit te voeren. Zie het [voorbeeldscenario](batch-parallel-node-tasks.md#example-scenario) in het artikel over [gelijktijdige knooppunttaken](batch-parallel-node-tasks.md) om te bekijken hoe u kunt profiteren van meerdere taken per knooppunt.

U kunt ook een *opvul type*opgeven dat bepaalt of batch de taken gelijkmatig verspreid over alle knoop punten in een pool, of dat elk knoop punt met het maximum aantal taken wordt gebundeld voordat taken aan een ander knoop punt worden toegewezen.

### <a name="communication-status-for-compute-nodes"></a>Communicatiestatus voor rekenknooppunten

In de meeste gevallen functioneren taken onafhankelijk en hoeven ze niet met elkaar te communiceren. Maar mogelijk hebt u ook toepassingen waarin taken moeten communiceren, bijvoorbeeld [MPI-scenario's](batch-mpi.md).

U kunt een pool zodanig configureren dat **communicatie tussen knooppunten** in de pool is toegestaan. Op die manier kunnen knopen in een pool tijdens runtime communiceren. Wanneer communicatie tussen knooppunten is ingeschakeld, kunnen knooppunten in de configuratie voor Cloud Services-pools met elkaar communiceren op poorten die groter zijn dan 1100. Voor virtuele-machineconfiguratiepools is verkeer op geen enkele poort beperkt.

Het inschakelen van de communicatie tussen knoop punten is ook van invloed op de plaatsing van het knoop punt in clusters, waardoor het maximum aantal knoop punten in een groep kan worden beperkt vanwege implementatie beperkingen. Als voor uw toepassing geen communicatie tussen knooppunten is vereist, kan de Batch-service een potentieel groter aantal knooppunten aan de pool toewijzen vanuit vele verschillende clusters en datacenters, om meer parallelle verwerkingskracht mogelijk te maken.

### <a name="start-tasks-for-compute-nodes"></a>Begintaken voor rekenknooppunten

De optionele *begintaak* wordt in elk knooppunt uitgevoerd wanneer dat knooppunt aan de pool wordt toegevoegd en telkens wanneer dat knooppunt opnieuw wordt opgestart of er een nieuwe installatiekopie van wordt gemaakt. De begintaak is vooral handig voor de voorbereiding van rekenknooppunten voor het uitvoeren van taken, zoals het installeren van de toepassingen die door de taken worden uitgevoerd.

### <a name="application-packages"></a>Toepassingspakketten

U kunt [toepassingspakketten](#application-packages) opgeven die moeten worden geïmplementeerd in de rekenknooppunten in de groep. Toepassingspakketten bieden vereenvoudigde implementatie en versies van de toepassingen die de taken uitvoeren. Toepassingspakketten die u voor een groep van toepassingen opgeeft, worden geïnstalleerd op elk knooppunt dat lid wordt van de groep, en elke keer dat er een knooppunt opnieuw wordt opgestart of er een installatiekopie wordt hersteld.

> [!NOTE]
> Toepassingspakketten worden ondersteund in alle Batch-pools die na 5 juli 2017 zijn gemaakt. De pakketten worden ondersteund in Batch-pools die zijn gemaakt tussen 10 maart 2016 en 5 juli 2017, maar alleen als de pool is gemaakt met behulp van een cloudservice-configuratie. Batch-pools die zijn gemaakt vóór 10 maart 2016 bieden geen ondersteuning voor toepassingspakketten. Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over het gebruiken van toepassingspakketten om toepassingen te implementeren naar Batch-knooppunten.
>
>

### <a name="network-configuration"></a>Netwerkconfiguratie

U kunt het subnet van een [virtueel netwerk (VNet)](../virtual-network/virtual-networks-overview.md) van Azure opgeven waarin de rekenknooppunten van de pool moeten worden gemaakt. Zie het gedeelte 'Netwerkconfiguratie pool' voor meer informatie.

## <a name="job"></a>Taak

Een job is een verzameling taken. Deze beheert hoe de berekening door de taken op rekenknooppunten in een pool wordt uitgevoerd.

* De job bepaalt de **pool** waarin het werk wordt uitgevoerd. U kunt voor elke job een nieuwe pool maken of één groep gebruiken voor een groot aantal jobs. U kunt een pool maken voor elke job die aan een jobplanning is gekoppeld, maar ook voor alle jobs die aan een jobplanning zijn gekoppeld.
* U kunt desgewenst een **jobprioriteit** opgeven. Wanneer er een job wordt verzonden met een hogere prioriteit dan de jobs die momenteel worden uitgevoerd, worden de taken van de job met hogere prioriteit in de wachtrij ingevoegd vóór de taken van de jobs met lagere prioriteit. Taken met lagere prioriteit die al worden uitgevoerd, kunnen niet worden verschoven.
* Met **beperkingen** voor jobs kunt u bepaalde limieten aan uw taken stellen:

    U kunt een **maximale kloktijd** instellen, zodat als de uitvoering van een job langer duurt dan de maximale kloktijd die is opgegeven, de job en alle bijbehorende taken worden beëindigd.

    Batch kan dit detecteren en vervolgens de mislukte taken opnieuw uitvoeren. U kunt het **maximumaantal nieuwe pogingen voor een taak** opgegeven als beperking. U kunt er ook voor kiezen om *altijd* of *nooit* een poging te doen om een taak opnieuw uit te voeren. Als u een taak opnieuw probeert uit te voeren, wordt de taak opnieuw in de wachtrij geplaatst om opnieuw te worden uitgevoerd.

* De clienttoepassing kan taken toevoegen aan een taak, maar u kunt ook een [jobbeheertaak](#job-manager-task) opgeven. Een jobbeheertaak bevat de benodigde informatie om de vereiste taken voor een job te maken, waarbij de jobbeheertaak wordt uitgevoerd op een van de rekenknooppunten binnen de pool. De jobbeheertaak wordt specifiek door Batch verwerkt en wordt in de wachtrij geplaatst zodra de job is gemaakt en opnieuw opgestart als deze mislukt. Een jobbeheertaak is *vereist* voor jobs die door een [jobplanning](#scheduled-jobs) zijn gemaakt, omdat dit de enige manier is om de taken te definiëren voordat de job wordt geïnstantieerd.
* Standaard blijven jobs in de actieve status wanneer alle taken binnen de job zijn voltooid. U kunt dit gedrag wijzigen zodat de job automatisch wordt beëindigd wanneer alle taken binnen de job zijn voltooid. Stel de eigenschap **onAllTasksComplete** van de taak ([onAllTasksComplete][net_onalltaskscomplete] in batch .net) in op *terminatejob* om de taak automatisch te beëindigen wanneer alle taken de status voltooid hebben.

    De batch-service beschouwt een taak *zonder taken om alle taken te* kunnen volt ooien. Daarom wordt deze optie meestal gebruikt met een [Jobbeheertaak](#job-manager-task). Als u de automatische beëindiging van een job wilt gebruiken zonder jobbeheer, moet u eerst de eigenschap **onAllTasksComplete** van een nieuwe job instellen op *noaction*. Vervolgens stelt u de eigenschap in op *terminatejob* als u klaar bent met taken toevoegen aan de job.

### <a name="job-priority"></a>Jobprioriteit

Aan jobs die u in Batch maakt, kunt u een prioriteit toewijzen. De Batch-service gebruikt de prioriteit van de job om de volgorde van de jobplanning binnen een account te bepalen (verwar dit niet met een [geplande job](#scheduled-jobs)). De prioriteitswaarden gaan van -1000 tot 1000, waarbij -1000 de laagste prioriteit is en 1000 de hoogste. Als u de prioriteit van een taak wilt bijwerken, roept u de [Update de eigenschappen van een taak][rest_update_job] bewerking aan (batch rest) of wijzigt u de eigenschap [eigenschap cloudjob. Priority][net_cloudjob_priority] (batch .net).

Binnen hetzelfde account hebben jobs met hogere prioriteit in de planning voorrang op jobs met lagere prioriteit. Een job met hogere prioriteit in het ene account heeft geen planningsvoorrang op een andere job met een lagere prioriteitswaarde in een ander account.

De jobplanning tussen pools verloopt onafhankelijk. Tussen verschillende pools is er geen garantie dat een job met hogere prioriteit eerst wordt gepland als de gekoppelde pool over te weinig niet-actieve knooppunten beschikt. Binnen dezelfde pool hebben jobs met dezelfde prioriteit evenveel kans om gepland te worden.

### <a name="scheduled-jobs"></a>Geplande jobs

Met [taak schema's][rest_job_schedules] kunt u terugkerende taken maken binnen de batch-service. Een jobplanning geeft aan wanneer u jobs moet uitvoeren en bevat de specificaties voor de uit te voeren jobs. U kunt de duur van de planning opgeven (hoelang en wanneer de planning geldt) en hoe vaak er in dat tijdsbestek jobs moeten worden gemaakt.

## <a name="task"></a>Taak

Een taak is een rekeneenheid die aan een job is gekoppeld. Deze wordt uitgevoerd op een knooppunt. Taken worden toegewezen aan een knooppunt om te worden uitgevoerd of in een wachtrij geplaatst tot een knooppunt beschikbaar is. Eenvoudig gesteld: een taak voert een of meer programma's of scripts uit op een rekenknooppunt om het benodigde werk uit te voeren.

Wanneer u een taak maakt, kunt u het volgende opgeven:

* De **opdrachtregel** van de taak. Dit is de opdrachtregel die uw toepassing of script op het rekenknooppunt uitvoert.

    Het is belang rijk te weten dat de opdracht regel niet onder een shell wordt uitgevoerd. Daarom kan deze niet op systeemeigen wijze profiteren van shell-functies zoals [omgevingsvariabele](#environment-settings-for-tasks)-uitbreiding (dit omvat het `PATH`). Als u wilt profiteren van dergelijke functies, start u de shell op de opdrachtregel (bijvoorbeeld door het starten van `cmd.exe` op Windows-knooppunten of `/bin/sh` op Linux):

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Als uw taken toepassingen of scripts moeten uitvoeren die zich niet in het `PATH` van het knooppunt bevinden of verwijzen naar omgevingsvariabelen, start u de shell expliciet in de opdrachtregel van de taak.
* **Bronbestanden** die de te verwerken gegevens bevatten. In een Azure Storage-account worden deze bestanden automatisch vanuit Blob Storage naar het knooppunt gekopieerd vóórdat de opdrachtregel van de taak wordt uitgevoerd. Zie de secties [Begintaak](#start-task) en [Bestanden en mappen](#files-and-directories) voor meer informatie.
* De **omgevingsvariabelen** die zijn vereist voor de toepassing. Zie de sectie [Omgevingsinstellingen voor taken](#environment-settings-for-tasks) voor meer informatie.
* De **beperkingen** waaronder de taak moet worden uitgevoerd. Beperkingen zijn bijvoorbeeld de maximale duur dat de taak mag worden uitgevoerd, het maximumaantal keren dat een taak opnieuw moet worden uitgevoerd indien deze mislukt en de maximale duur dat bestanden in de werkmap van de taak behouden blijven.
* **Toepassingspakketten** voor het implementeren in het rekenknooppunt waarop de taak staat gepland voor uitvoering. [Toepassingspakketten](#application-packages) bieden vereenvoudigde implementatie en versies van de toepassingen die de taken uitvoeren. Toepassingspakketten op taakniveau zijn met name nuttig in omgevingen met gedeelde groepen. Verschillende jobs worden uitgevoerd op één groep en de groep wordt niet verwijderd wanneer een job is voltooid. Als de job minder taken dan knooppunten in de groep heeft, kunnen toepassingspakketten van taken gegevensoverdracht minimaliseren omdat uw toepassing alleen wordt geïmplementeerd op de knooppunten die taken uitvoeren.
* Een verwijzing naar een **containerinstallatiekopie** in Docker Hub of een persoonlijk register en extra instellingen voor het maken van een Docker-container waarin de taak wordt uitgevoerd op het knooppunt. U geeft deze informatie alleen op als de pool is ingesteld met een containerconfiguratie.

> [!NOTE]
> De maximale levensduur van een taak, van het toevoegen aan de job tot de voltooiing, bedraagt 180 dagen. Voltooide taken blijven gedurende zeven dagen aanwezig; gegevens voor taken die niet binnen de maximale levensduur zijn voltooid, zijn niet toegankelijk.

Naast de taken die u definieert om een berekening op een knooppunt uit te voeren, stelt de Batch-service ook de volgende speciale taken beschikbaar:

* [Begintaak](#start-task)
* [Jobbeheertaak](#job-manager-task)
* [Jobvoorbereidingstaken en jobvrijgevingstaken](#job-preparation-and-release-tasks)
* Taken met meerdere instanties (MPI)
* [Taakafhankelijkheden](#task-dependencies)

### <a name="start-task"></a>Begintaak

Door een **begintaak** aan een pool te koppelen, kunt u de besturingsomgeving van de knooppunten ervan voorbereiden. U kunt bijvoorbeeld acties uitvoeren als het installeren van de toepassingen die de taken uitvoeren en het starten van achtergrondprocessen. Zolang deze in de pool blijft, wordt de begintaak uitgevoerd telkens wanneer een knooppunt wordt gestart, ook wanneer het knooppunt voor het eerst aan de pool wordt toegevoegd en wanneer deze opnieuw wordt opgestart of er een installatiekopie wordt hersteld.

Een belangrijk voordeel van de begintaak is dat deze alle informatie bevat die nodig is voor het configureren van een rekenknooppunt en het installeren van de toepassingen die vereist zijn voor het uitvoeren van de taak. Het verhogen van het aantal knooppunten in een pool is daarom net zo eenvoudig als het opgeven van een nieuw aantal doelknooppunten. De begintaak biedt de Batch-service de informatie die nodig is om nieuwe knooppunten te configureren en deze voor te bereiden op het accepteren van taken.

Net als bij elke Azure Batch taak kunt u een lijst met **resource bestanden** in [Azure Storage][azure_storage]opgeven, naast een **opdracht regel** die moet worden uitgevoerd. De Batch-service kopieert eerst de resourcebestanden naar het knooppunt vanuit Azure Storage en voert vervolgens de opdrachtregel uit. Voor een pool-begintaak bevat de lijst met bestanden meestal de taaktoepassing en de bijbehorende afhankelijkheden.

De begintaak kan echter ook referentiegegevens bevatten die worden gebruikt door alle taken die op het rekenknooppunt worden uitgevoerd. De opdrachtregel van een begintaak kan bijvoorbeeld een `robocopy`bewerking uitvoeren voor het kopiëren van toepassingsbestanden (die zijn opgegeven als resourcebestanden en zijn gedownload naar het knooppunt) vanuit de [werkmap](#files-and-directories) van de begintaak naar de [gedeelde map](#files-and-directories), en vervolgens een MSI of `setup.exe` uitvoeren.

Het is doorgaans wenselijk dat de Batch-service wacht tot de begintaak is voltooid voordat het knooppunt als gereed wordt beschouwd om er taken aan toe te wijzen, maar dit kunt u configureren.

Als een begintaak op een rekenknooppunt mislukt, wordt de status van het knooppunt bijgewerkt om de fout aan te geven en is het knooppunt niet beschikbaar om taken toe te wijzen. Een begintaak kan mislukken als er een probleem optreedt bij het kopiëren van de bronbestanden van de begintaak uit de opslag, of als het proces dat door de opdrachtregel ervan wordt uitgevoerd een andere afsluitcode dan nul retourneert.

Als u de begintaak voor een bestaande pool toevoegt of bijwerkt, moet u de rekenknooppunten voor de begintaak opnieuw toepassen op de knooppunten.

>[!NOTE]
> Batch beperkt de totale grootte van een begintaak, inclusief bronbestanden en omgevingsvariabelen. Als u de grootte van een begintaak wilt beperken, kunt u dat op twee manieren doen:
>
> 1. U kunt toepassingspakketten gebruiken om toepassingen of gegevens te distribueren naar elk knooppunt in de Batch-pool. Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over toepassingspakketten.
> 2. U kunt handmatig een ZIP-archief maken dat uw toepassingsbestanden bevat. Upload het ZIP-archief als een blob naar Azure Storage. Geef het ZIP-archief op als bronbestand voor de begintaak. Voordat u de opdrachtregel voor de begintaak uitvoert, moet u het archief uitpakken vanaf de opdrachtregel. 
>
>    Daarvoor kunt u elk gewenst hulpprogramma voor archivering gebruiken. U moet het hulpprogramma dat u gebruikt om het archief uit te pakken, als bronbestand voor de begintaak opnemen.
>
>

### <a name="job-manager-task"></a>Jobbeheertaak

Doorgaans gebruikt u een **jobbeheertaak** voor het beheren en/of volgen van de jobuitvoering. Bijvoorbeeld om de taken voor een job te maken en te verzenden, om te bepalen welke extra taken er moeten worden uitgevoerd en om te bepalen wanneer het werk is voltooid. Een jobbeheertaak is echter niet beperkt tot deze activiteiten. Het is een volledig zelfstandige taak die alle acties kan uitvoeren die voor de job zijn vereist. Een jobbeheertaak kan bijvoorbeeld een bestand downloaden dat als een parameter is opgegeven, de inhoud van dat bestand analyseren en op basis van die inhoud aanvullende taken verzenden.

Een jobbeheertaak wordt vóór alle andere taken gestart. Deze biedt de volgende functies:

* Een jobbeheertaak wordt automatisch door de Batch-service verzonden als een taak wanneer de job wordt gemaakt.
* De uitvoering van een jobbeheertaak wordt gepland voor de andere taken in een job.
* Het hieraan gekoppelde knooppunt is het laatste dat moeten worden verwijderd uit een pool wanneer de pool wordt verkleind.
* De beëindiging ervan kan worden gekoppeld aan de beëindiging van alle taken in de job.
* Een jobbeheertaak krijgt de hoogste prioriteit wanneer deze opnieuw moet worden gestart. Als een niet-actief knooppunt niet beschikbaar is, kan de Batch-service een van de andere actieve taken in de pool beëindigen om ruimte te maken voor het uitvoeren van de jobbeheertaak.
* Een jobbeheertaak in de ene job heeft geen hogere prioriteit dan de taken van andere jobs. Tussen jobs worden alleen prioriteiten op jobniveau in acht genomen.

### <a name="job-preparation-and-release-tasks"></a>Jobvoorbereidingstaken en jobvrijgevingstaken

Batch biedt jobvoorbereidingstaken om de job in te stellen voordat deze wordt uitgevoerd. Jobvrijgevingstaken zijn er voor onderhoud of opschoning nadat de job is uitgevoerd.

* **Jobvoorbereidingstaak**: Een jobvoorbereidingstaak wordt uitgevoerd op alle rekenknooppunten die zijn gepland om taken uit te voeren, voordat een van de andere taken van de job wordt uitgevoerd. U kunt een jobvoorbereidingstaak gebruiken om bijvoorbeeld gegevens te kopiëren die door alle taken worden gedeeld, maar uniek zijn voor de job.
* **Jobvrijgevingstaak**: Wanneer een job is voltooid, wordt er een jobvrijgevingstaak uitgevoerd op elk knooppunt in de pool dat ten minste één taak heeft uitgevoerd. U kunt een jobvrijgevingstaak gebruiken om bijvoorbeeld gegevens te verwijderen die door de jobvoorbereidingstaak zijn gekopieerd of om diagnostische logboekgegevens te comprimeren en te uploaden.

Met zowel jobvoorbereidingstaken als jobvrijgevingstaken kunt u een opdrachtregel opgeven die moet worden uitgevoerd wanneer de taak wordt gestart. Ze bieden functies zoals het downloaden van bestanden, uitvoering met verhoogde bevoegdheden, aangepaste omgevingsvariabelen, maximale uitvoeringsduur, aantal pogingen en bewaartijd voor bestanden.

Zie [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Jobvoorbereidings- en jobvrijgevingstaken uitvoeren op Azure Batch-rekenknooppunten) voor meer informatie over jobvoorbereidings- en jobvrijgevingstaken.

### <a name="multi-instance-task"></a>Taak met meerdere instanties

Een [taak met meerdere instanties](batch-mpi.md) is een taak die is geconfigureerd om op meer dan één rekenknooppunt tegelijk te worden uitgevoerd. Bij taken met meerdere instanties kunt u High Performance Computing-scenario's inschakelen, waarvoor een groep rekenknooppunten samen moet worden toegewezen om één workload te verwerken (zoals Message Passing Interface (MPI)).

Zie [Taken met meerdere instanties gebruiken om Message Passing Interface (MPI)-toepassingen uit te voeren in Azure Batch](batch-mpi.md) voor gedetailleerde informatie over het uitvoeren van MPI-jobs in Batch met behulp van de Batch .NET-bibliotheek.

### <a name="task-dependencies"></a>Taakafhankelijkheden

Zoals de naam al aangeeft, kunt u met [taakafhankelijkheden](batch-task-dependencies.md) opgeven dat een taak afhangt van de voltooiing van andere taken voordat deze wordt uitgevoerd. Deze functie biedt ondersteuning voor situaties waarin een 'downstream'-taak gebruikmaakt van de uitvoer van een 'upstream'-taak, of wanneer een 'upstream'-taak initialisaties uitvoert die zijn vereist voor een 'downstream'-taak. Als u deze functie wilt gebruiken, moet u eerst taakafhankelijkheden inschakelen in uw Batch-job. Voor elke taak die afhankelijk is van een ander (of veel andere), geeft u de taken op waarvan de taak afhankelijk is.

Bij taakafhankelijkheden kunt u scenario's zoals de volgende configureren:

* *taakB* hangt af van *taakA* (*taakB* kan pas worden uitgevoerd nadat *taakA* is voltooid).
* *taakC* is afhankelijk van *taakA* én *taakB*.
* *taakD* is afhankelijk van een bereik van taken, zoals taken *1* t/m *10*, voordat deze wordt uitgevoerd.

Bekijk [taak afhankelijkheden in azure batch](batch-task-dependencies.md) en het voor beeld van de GitHub [-code in de opslag][github_sample_taskdeps] plaats [Azure-batch-samples][github_samples] voor meer gedetailleerde informatie over deze functie.

## <a name="environment-settings-for-tasks"></a>Omgevingsinstellingen voor taken

Elke taak die wordt uitgevoerd door de Batch-service heeft toegang tot de omgevingsvariabelen die zijn ingesteld op de rekenknooppunten. Hieronder vallen omgevings variabelen die zijn gedefinieerd door de batch-service (door de[service gedefinieerde][msdn_env_vars]) en aangepaste omgevings variabelen die u voor uw taken kunt definiëren. De toepassingen en scripts die door uw taken worden uitgevoerd, hebben tijdens de uitvoering toegang tot deze omgevingsvariabelen.

U kunt aangepaste omgevingsvariabelen instellen op het niveau van de taak of de job door voor deze entiteiten de eigenschap voor *omgevingsinstellingen* in te vullen. Zie bijvoorbeeld het [onderdeel een taak toevoegen aan een taak][rest_add_task] (batch rest API) of de eigenschappen [CloudTask. EnvironmentSettings][net_cloudtask_env] en [eigenschap cloudjob. CommonEnvironmentSettings][net_job_env] in batch .net.

Uw client toepassing of service kan de omgevings variabelen van een taak, zowel door service gedefinieerde als aangepaste, ophalen met behulp van de bewerking [informatie over een taak ophalen][rest_get_task_info] (batch rest) of door de eigenschap [CloudTask. EnvironmentSettings][net_cloudtask_env] te openen ( Batch .NET). Processen die op een rekenknooppunt worden uitgevoerd, kunnen ook toegang krijgen tot deze en andere omgevingsvariabelen in het knooppunt, bijvoorbeeld met behulp van de vertrouwde syntaxis van `%VARIABLE_NAME%` (Windows) of `$VARIABLE_NAME` (Linux).

U kunt een volledige lijst met alle door de service gedefinieerde omgevings variabelen vinden in de [omgevings variabelen van het reken knooppunt][msdn_env_vars].

## <a name="files-and-directories"></a>Bestanden en mappen

Elke taak heeft een *werkmap* waaronder nul of meer bestanden en mappen worden gemaakt. Deze werkmap kan worden gebruikt voor het opslaan van het programma dat wordt uitgevoerd door de taak, de gegevens die erdoor worden verwerkt en de uitvoer van de verwerking die wordt uitgevoerd. Alle bestanden en mappen van een taak zijn het eigendom van de taakgebruiker.

De Batch-service geeft een deel van het bestandssysteem in een knooppunt weer als de *hoofdmap*. Taken hebben toegang tot de hoofdmap door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_ROOT_DIR`. Zie [Omgevingsinstellingen voor taken](#environment-settings-for-tasks) voor meer informatie over het gebruik van omgevingsvariabelen.

De hoofdmap bevat de volgende mapstructuur:

![Mapstructuur rekenknooppunt][1]

* **toepassingen**: Bevat informatie over de details van toepassings pakketten die op het reken knooppunt zijn geïnstalleerd. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_APP_PACKAGE`.

* **fsmounts**: De map bevat alle bestands systemen die op een reken knooppunt zijn gekoppeld. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_MOUNTS_DIR`. Zie [een virtueel bestands systeem koppelen aan een batch-pool](virtual-file-mount.md)voor meer informatie.

* **shared**: Deze map biedt lees-/schrijftoegang tot *alle* taken die op een knooppunt worden uitgevoerd. Taken die in het knooppunt worden uitgevoerd, kunnen in deze map bestanden maken, lezen, bijwerken en verwijderen. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_SHARED_DIR`.

* **startup**: Deze map wordt door een begintaak gebruikt als werkmap. Alle bestanden die door de begintaak naar het knooppunt zijn gedownload, worden hier opgeslagen. De begintaak kan in deze map bestanden maken, lezen, bijwerken en verwijderen. Taken kunnen toegang krijgen tot deze map door te verwijzen naar de omgevingsvariabele `AZ_BATCH_NODE_STARTUP_DIR`.

* **vluchtig**: Deze directory is voor interne doel einden. Er is geen garantie dat bestanden in deze map of de map zelf in de toekomst aanwezig zullen zijn.

* **werk items**: Deze map bevat de mappen voor taken en hun taken op het reken knooppunt.

* **Tasks**: In de **werk items** -map wordt een directory gemaakt voor elke taak die op het knoop punt wordt uitgevoerd. Het is toegankelijk door te verwijzen naar `AZ_BATCH_TASK_DIR` de omgevings variabele.

    Binnen elk taakmap maakt de Batch-service een werkmap (`wd`) waarvan het unieke pad wordt opgegeven door de omgevingsvariabele `AZ_BATCH_TASK_WORKING_DIR`. Deze map biedt lees-/schrijftoegang tot de taak. De taak kan in deze map bestanden maken, lezen, bijwerken en verwijderen. Deze map wordt behouden op basis van de *RetentionTime*-beperking die is opgegeven voor de taak.

    `stdout.txt` en `stderr.txt`: Deze bestanden worden naar de taakmap geschreven bij het uitvoeren van de taak.

> [!IMPORTANT]
> Wanneer een knooppunt uit de pool wordt verwijderd, worden *alle* bestanden verwijderd die in het knooppunt zijn opgeslagen.
>
>

## <a name="application-packages"></a>Toepassingspakketten

Met de functie voor [toepassingspakketten](batch-application-packages.md) kunt u toepassingen in de rekenknooppunten in uw pools eenvoudig beheren en implementeren. U kunt eenvoudig meerdere versies van de toepassingen die door uw taken worden uitgevoerd uploaden en beheren, zoals de bijbehorende binaire bestanden en ondersteuningsbestanden. Vervolgens kunt u automatisch een of meer van deze toepassingen implementeren in de rekenknooppunten in uw pool.

U kunt toepassingspakketten opgeven op het niveau van de groep en de taak. Wanneer u toepassingspakketten voor de groep opgeeft, wordt de toepassing geïmplementeerd in elk knooppunt van de groep. Wanneer u toepassingspakketten voor de taak opgeeft, wordt de toepassing alleen geïmplementeerd in knooppunten die ten minste één van de taken van de job moeten uitvoeren, net voordat de opdrachtregel van de taak wordt uitgevoerd.

Batch regelt de samenwerking met Azure Storage om uw toepassingspakketten op te slaan en in rekenknooppunten te implementeren, zodat uw code en beheeroverhead kunnen worden vereenvoudigd.

Zie [Deploy applications to compute nodes with Batch application packages](batch-application-packages.md) (Toepassingen implementeren naar rekenknooppunten met Batch-toepassingspakketten) voor meer informatie over de functie Toepassingspakketten.

> [!NOTE]
> Als u toepassingspakketten van een groep toevoegt aan een *bestaande* groep, moeten de rekenknooppunten opnieuw worden opgestart zodat de toepassingspakketten in de knooppunten kunnen worden geïmplementeerd.
>
>

## <a name="pool-and-compute-node-lifetime"></a>Levensduur van pool en rekenknooppunt

Bij het ontwerp van uw Azure Batch-oplossing moet u een ontwerpbeslissing maken over hoe en wanneer pools worden gemaakt en hoelang rekenknooppunten binnen die pools beschikbaar blijven.

Aan het ene uiteinde van het spectrum kunt u een pool maken voor elke job wanneer deze wordt verzonden en kunt u de pool verwijderen zodra taken zijn uitgevoerd. Hiermee maximaliseert u het gebruik omdat de knooppunten alleen worden toegewezen wanneer deze echt nodig zijn en worden afgesloten wanneer ze inactief zijn. Hoewel dit betekent dat de job moet wachten tot de knooppunten zijn toegewezen, is het belangrijk om te weten dat taken voor knooppunten worden gepland zodra ze afzonderlijk beschikbaar zijn, toegewezen zijn en de begintaak is voltooid. Batch wacht *niet* tot alle knooppunten in een pool beschikbaar zijn alvorens taken aan de knooppunten toe te wijzen. Hiermee zorgt u voor maximaal gebruik van alle beschikbare knooppunten.

Als, aan het andere uiteinde van het spectrum, het onmiddellijk starten van jobs de hoogste prioriteit heeft, kunt u een pool voortijdig maken en de knooppunten ervan beschikbaar stellen voordat er jobs worden verzonden. In dit scenario kunnen taken onmiddellijk worden gestart, maar blijven knooppunten mogelijk inactief terwijl wordt gewacht tot er taken worden toegewezen.

Een gecombineerde benadering wordt meestal gebruikt voor het verwerken van een variabele, maar continue workload. U kunt een pool hebben waarnaar meerdere taken worden verzonden en het aantal knooppunten omhoog of omlaag schalen volgens de jobbelasting (zie [Rekenresources vergroten/verkleinen](#scaling-compute-resources) in de volgende sectie). U kunt dit reactief doen, op basis van de huidige workload, of proactief als de workload kan worden voorspeld.

## <a name="virtual-network-vnet-and-firewall-configuration"></a>Configuratie van virtuele netwerken (VNet) en firewalls 

Wanneer u een pool rekenknooppunten inricht in Batch kunt u deze pool koppelen aan een subnet van een virtueel netwerk van [Azure (VNet)](../virtual-network/virtual-networks-overview.md). Het gebruik van een Azure VNet vereist dat de Batch-client-API gebruikmaakt van Azure Active Directory-verificatie (AD). Azure Batch-ondersteuning voor Azure AD wordt beschreven in [Oplossingen van Batch-service verifiëren met Active Directory](batch-aad-auth.md).  

### <a name="vnet-requirements"></a>Vereisten voor VNet

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

Zie [Een pool van virtuele machines maken met uw virtuele netwerk](batch-virtual-network.md) voor meer informatie over het instellen van een Batch-pool in een VNet.

## <a name="scaling-compute-resources"></a>Rekenresources vergroten/verkleinen

Door [automatisch te vergroten/verkleinen](batch-automatic-scaling.md) kunt u het aantal rekenknooppunten in een pool dynamisch laten aanpassen door de Batch-service op basis van de huidige workload en het resourcegebruik van uw rekenscenario. Zo kunt u de totale kosten van het uitvoeren van uw toepassing verlagen door alleen de benodigde resources te gebruiken en de resources die u niet nodig hebt, vrij te geven.

U schakelt automatische vergroting/verkleining in door een [formule voor automatisch vergroten/verkleinen](batch-automatic-scaling.md#automatic-scaling-formulas) te schrijven en die formule te koppelen aan een pool. De Batch-service gebruikt deze formule om het doelaantal knooppunten in de pool te bepalen voor het volgende interval voor vergroten/verkleinen (een interval dat u kunt configureren). U kunt de instellingen voor automatisch vergroten/verkleinen voor een pool opgeven wanneer u deze maakt of op een later moment voor een pool inschakelen. U kunt de instellingen voor automatisch vergroten/verkleinen ook bijwerken in een pool waarvoor vergroten/verkleinen is ingeschakeld.

Een voor beeld: als u een taak wilt uitvoeren, moet u een groot aantal taken verzenden. U kunt aan de pool een formule voor vergroten/verkleinen toewijzen die het aantal knooppunten in de pool aanpast op basis van het huidige aantal taken in de wachtrij en de snelheid waarmee de taken in de job worden voltooid. De Batch-service evalueert periodiek de formule en vergroot/verkleint de pool op basis van de workload en uw andere formule-instellingen. De service voegt naar behoefte knooppunten toe, bijvoorbeeld wanneer er veel taken in de wachtrij staan. Ook worden er knooppunten verwijderd wanneer er geen taken actief zijn of in de wachtrij staan.

Een formule voor vergroten/verkleinen kan op de volgende metrische gegevens worden gebaseerd:

* **Metrische gegevens voor tijd** zijn gebaseerd op statistieken die om de vijf minuten worden verzameld binnen het opgegeven aantal uren.
* **Metrische gegevens voor resources** zijn gebaseerd op CPU-gebruik, bandbreedtegebruik, geheugengebruik en het aantal knooppunten.
* **Metrische gegevens voor taken** zijn gebaseerd op de taakstatus, zoals *Actief* (in de wachtrij) *Wordt uitgevoerd* of *Voltooid*.

Wanneer met automatisch vergroten/verkleinen het aantal rekenknooppunten in een groep vermindert, moet u bedenken hoe taken moeten worden afgehandeld die worden uitgevoerd op het moment van de verkleining. Hiervoor voorziet Batch in een *optie voor knooppunttoewijzing* die u kunt opnemen in uw formules. U kunt bijvoorbeeld opgeven dat actieve taken worden gestopt en vervolgens opnieuw in de wachtrij worden geplaatst om te worden uitgevoerd op een ander knooppunt, of moeten worden voltooid voordat het knooppunt uit de pool wordt verwijderd.

Zie [Automatically scale compute nodes in an Azure Batch pool](batch-automatic-scaling.md) (Rekenknooppunten in een Azure Batch-pool automatisch vergroten/verkleinen) voor meer informatie over het automatisch vergroten/verkleinen van een toepassing.

> [!TIP]
> Als u het gebruik van rekenresources wilt maximaliseren, stelt u het doelaantal knooppunten aan het einde van een job in op nul, maar staat u toe dat actieve taken mogen worden voltooid.
>
>

## <a name="security-with-certificates"></a>Beveiliging met certificaten

Normaal gesp roken moet u certificaten gebruiken bij het versleutelen of ontsleutelen van gevoelige gegevens voor taken, zoals de sleutel voor een [Azure Storage-account][azure_storage]. Ter ondersteuning hiervan installeert u certificaten op knooppunten. Versleutelde geheimen worden via opdrachtregelparameters doorgegeven aan taken of worden ingesloten in een van de taakresources. De geïnstalleerde certificaten kunnen dan worden gebruikt om ze te ontsleutelen.

U gebruikt de methode voor het toevoegen van een [certificaat][rest_add_cert] (batch rest) of [methode certificateoperations. CreateCertificate][net_create_cert] (batch .net) om een certificaat toe te voegen aan een batch-account. Daarna kunt u het certificaat aan een nieuwe of bestaande pool koppelen. Wanneer een certificaat aan een pool is gekoppeld, wordt het door de Batch-service in elk knooppunt in de pool geïnstalleerd. De Batch-service installeert de juiste certificaten wanneer het knooppunt wordt gestart, voordat er een taak wordt gestart (met inbegrip van begintaken en jobbeheertaken).

Als u certificaten toevoegt aan een *bestaande* groep, moeten de rekenknooppunten opnieuw worden opgestart zodat de certificaten op de knooppunten kunnen worden toegepast.

## <a name="error-handling"></a>Foutafhandeling

Soms is het nodig om in uw Batch-oplossing taak- en toepassingsfouten af te handelen.

### <a name="task-failure-handling"></a>Afhandeling van taakfouten

Taakfouten kunnen worden onderverdeeld in deze categorieën:

* **Voorverwerkingsfouten**

    Als een taak niet kan worden gestart, wordt een voorverwerkingsfout voor de taak ingesteld.  

    Voorverwerkingsfouten kunnen optreden doordat bestanden zijn verplaatst, doordat het opslagaccount niet langer beschikbaar is of doordat een ander probleem ertoe heeft geleid dat bestanden niet naar het knooppunt kunnen worden gekopieerd.

* **Fouten bij uploaden van bestand**

    Als bestanden die voor een taak zijn opgegeven om welke reden dan ook niet kunnen worden geüpload, wordt een fout bij uploaden van bestand voor de taak ingesteld.

    Fouten bij uploaden van bestand kunnen optreden als de SAS die is opgegeven om toegang te krijgen tot Azure Storage ongeldig is of geen schrijfmachtigingen biedt, als het opslagaccount niet meer beschikbaar is of als er een ander probleem is opgetreden waardoor bestanden niet van het knooppunt kunnen worden gekopieerd.    

* **Toepassingsfouten**

    Het proces dat door de opdrachtregel van de taak wordt opgegeven, kan ook mislukken. Het proces wordt als mislukt beschouwd wanneer het proces dat door de taak wordt uitgevoerd, een andere afsluitcode dan nul retourneert (zie *Taakafsluitcodes* in de volgende sectie).

    Voor toepassingsfouten kunt u Batch configureren om automatisch een opgegeven aantal pogingen te doen om de taak opnieuw uit te voeren.

* **Beperkingsfouten**

    U kunt de beperking *maxWallClockTime* instellen om te bepalen hoelang een job of taak maximaal kan worden uitgevoerd. Deze maximale kloktijd kan handig zijn om vastgelopen taken te beëindigen.

    Wanneer de maximale hoeveelheid tijd wordt overschreden, wordt de taak als *voltooid* gemarkeerd, maar wordt de afsluitcode ingesteld op `0xC000013A` en wordt het veld *schedulingError* gemarkeerd als `{ category:"ServerError", code="TaskEnded"}`.

### <a name="debugging-application-failures"></a>Foutopsporing van toepassingsfouten

* `stderr` en `stdout`

    Bij het uitvoeren van een toepassing kan deze een diagnostische uitvoer produceren die handig is voor het oplossen van problemen. Zoals eerder is vermeld in [Bestanden en mappen](#files-and-directories), verzendt de Batch-service standaarduitvoer en standaardfoutuitvoer naar de bestanden `stdout.txt` en `stderr.txt` in de taakmap in het rekenknooppunt. U kunt Azure Portal of een van de Batch-SDK's gebruiken om deze bestanden te downloaden. U kunt deze en andere bestanden bijvoorbeeld ophalen om problemen op te lossen met behulp van [ComputeNode. GetNodeFile][net_getfile_node] en [CloudTask. GetNodeFile][net_getfile_task] in de batch .net-bibliotheek.

* **Taakafsluitcodes**

    Zoals eerder vermeld, wordt een taak door de Batch-service gemarkeerd als mislukt als het proces dat door de taak wordt uitgevoerd, een afsluitcode retourneert die niet nul is. Wanneer een taak een proces uitvoert, vult Batch de *retourcode van het proces* als afsluitcode-eigenschap van de taak in. Houd voor ogen dat de afsluitcode van een taak **niet** wordt bepaald door de Batch-service. De afsluitcode van een taak wordt bepaald door het proces zelf of door het besturingssysteem waarop het proces wordt uitgevoerd.

### <a name="accounting-for-task-failures-or-interruptions"></a>Verklaring voor mislukte taken of onderbrekingen van taken

Van tijd tot tijd kunnen taken mislukken of worden onderbroken. In de taaktoepassing zelf kan een fout optreden, het knooppunt waarin de taak wordt uitgevoerd, kan opnieuw zijn opgestart of het knooppunt is mogelijk uit de pool verwijderd bij een vergroot-/verkleinbewerking (indien het beleid voor het ongedaan maken van de toewijzing van een knooppunt is ingesteld om knooppunten onmiddellijk te verwijderen zonder te wachten tot taken zijn voltooid). In alle gevallen moet de taak door Batch automatisch opnieuw in de wachtrij worden geplaatst voor uitvoering in een ander knooppunt.

Het is ook mogelijk dat er een regel matig probleem optreedt waardoor een taak niet meer reageert of te lang duurt om uit te voeren. U kunt de maximale uitvoeringsinterval voor een taak instellen. Als de maximale uitvoeringsinterval wordt overschreden, onderbreekt de Batch-service de taaktoepassing.

### <a name="connecting-to-compute-nodes"></a>Verbinding maken met rekenknooppunten

U kunt extra foutopsporing en probleemoplossing uitvoeren door u op afstand aan te melden bij een rekenknooppunt. U kunt via Azure Portal een RDP-bestand (Remote Desktop Protocol) downloaden voor Windows-knooppunten en SSH-verbindingsinformatie (Secure Shell) verkrijgen voor Linux-knooppunten. U kunt dit ook doen met behulp van de batch-Api's, bijvoorbeeld met [batch .net][net_rdpfile] of [batch python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh).

> [!IMPORTANT]
> Als u via RDP of SSH verbinding wilt maken met een knooppunt, moet u in het knooppunt eerst een gebruiker maken. Hiervoor gebruikt u de Azure Portal, [voegt u een gebruikers account toe aan een knoop punt][rest_create_user] met behulp van de Batch-rest API, roept u de methode [ComputeNode. CreateComputeNodeUser][net_create_user] aan in batch .net of roept u de [add_user][py_add_user] -methode aan in de batch python-module.
>
>

Zie [Configure or disable remote access to compute nodes in an Azure Batch pool](pool-endpoint-configuration.md) (Externe toegang tot rekenknooppunten in een Azure Batch-pool configureren of uitschakelen) als u RDP- of SSH-toegang tot rekenknooppunten wilt beperken of uitschakelen.

### <a name="troubleshooting-problematic-compute-nodes"></a>Problemen met problematische rekenknooppunten oplossen

In situaties waarin een aantal taken mislukken, kan uw Batch-clienttoepassing of -service de metagegevens van de mislukte taken onderzoeken om een knooppunt te identificeren dat zich niet normaal gedraagt. Elk knooppunt in een pool krijgt een unieke id en het knooppunt waarin een taak wordt uitgevoerd, is opgenomen in de metagegevens van de taak. Als u eenmaal een probleemknooppunt hebt geïdentificeerd, kunt u verschillende acties uitvoeren:

* **Het knoop punt opnieuw opstarten** ([rest][rest_reboot] | [.net][net_reboot])

    Soms kunnen latente problemen zoals vastgelopen of gecrashte processen worden opgelost door het knooppunt opnieuw op te starten. Als uw pool een begin taak gebruikt of als uw taak een taak voorbereidings taak gebruikt, worden deze uitgevoerd wanneer het knoop punt opnieuw wordt opgestart.
* **De installatie kopie van het knoop punt** terugzetten ([rest][rest_reimage] | [.net][net_reimage])

    Hiermee wordt het besturingssysteem opnieuw geïnstalleerd in het knooppunt. Net als bij het opnieuw opstarten van een knooppunt worden begintaken en jobvoorbereidingstaken opnieuw uitgevoerd nadat de installatiekopie van het knooppunt is teruggezet.
* **Het knoop punt uit de pool verwijderen** ([rest][rest_remove] | [.net][net_remove])

    Soms is het nodig om het knooppunt volledig uit de pool te verwijderen.
* **Taak planning op het knoop punt uitschakelen** ([rest][rest_offline] | [.net][net_offline])

    Hierdoor wordt het knooppunt effectief offline geplaatst, zodat er geen taken meer aan worden toegewezen, maar het knooppunt wel actief en in de pool blijft. Zo kunt u verder onderzoek verrichten naar de oorzaak van de fouten zonder verlies van gegevens van de mislukte taak en zonder dat het knooppunt extra taakfouten veroorzaakt. U kunt bijvoorbeeld de taakplanning in het knooppunt uitschakelen en u vervolgens [extern aanmelden](#connecting-to-compute-nodes) om de gebeurtenislogboeken van het knooppunt te onderzoeken of een andere probleemoplossing uitvoeren. Nadat u klaar bent met het onderzoek, kunt u het knoop punt weer online plaatsen door de taak planning in te scha kelen ([rest][rest_online] | [.net][net_online]) of door een van de andere acties uit te voeren die eerder zijn besproken.

> [!IMPORTANT]
> Met elk van de acties die in deze sectie zijn besproken (opnieuw opstarten, installatiekopie terugzetten, verwijderen en taakplanning uitschakelen), kunt u opgeven hoe taken die momenteel in het knooppunt worden uitgevoerd, worden afgehandeld wanneer u de actie uitvoert. Als u bijvoorbeeld het plannen van taken op een knoop punt met behulp van de batch .NET-client bibliotheek uitschakelt, kunt u een [DisableComputeNodeSchedulingOption][net_offline_option] Enum-waarde opgeven om op te geven of actieve taken moeten worden **beëindigd** en opnieuw **in de wachtrij** moet worden geplaatst planning op andere knoop punten of toestaan dat taken worden voltooid voordat de actie wordt uitgevoerd (**TaskCompletion**).
>
>

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Batch-API's en -hulpprogramma's](batch-apis-tools.md) die beschikbaar zijn voor het bouwen van Batch-oplossingen.
* Lees de basisbeginselen van het ontwikkelen van een voor Batch geschikte toepassing met behulp van de [clientbibliotheek Batch .NET](quick-run-dotnet.md) of [Python](quick-run-python.md). Deze snelstarts leiden u stapsgewijs door een voorbeeldtoepassing die gebruikmaakt van de Batch-service voor het uitvoeren van een workload op meerdere rekenknooppunten. U vindt er ook informatie over het gebruik van Azure Storage voor het faseren en ophalen van een workloadbestand.
* Down load en Installeer [batch Explorer][batch_labs] voor gebruik tijdens het ontwikkelen van uw batch-oplossingen. Gebruik Batch Explorer bij het maken en bewaken van en opsporen van fouten in Azure Batch-toepassingen. 
* Zie community-resources, waaronder [stack overflow](https://stackoverflow.com/questions/tagged/azure-batch), de [batch-Community opslag plaats](https://github.com/Azure/Batch)en het [Azure batch-forum][batch_forum] op MSDN. 

[1]: ./media/batch-api-basics/node-folder-structure.png

[azure_storage]: https://azure.microsoft.com/services/storage/
[batch_forum]: https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurebatch
[cloud_service_sizes]: ../cloud-services/cloud-services-sizes-specs.md
[msmpi]: https://msdn.microsoft.com/library/bb524831.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_sample_taskdeps]:  https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies
[batch_labs]: https://azure.github.io/BatchExplorer/
[batch_net_api]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[msdn_env_vars]: https://msdn.microsoft.com/library/azure/mt743623.aspx
[net_cloudjob_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobmanagertask.aspx
[net_cloudjob_priority]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.priority.aspx
[net_cloudpool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_cloudtask_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.environmentsettings.aspx
[net_create_cert]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.certificateoperations.createcertificate.aspx
[net_create_user]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.createcomputenodeuser.aspx
[net_getfile_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getnodefile.aspx
[net_getfile_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.getnodefile.aspx
[net_job_env]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.commonenvironmentsettings.aspx
[net_multiinstancesettings]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.multiinstancesettings.aspx
[net_onalltaskscomplete]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.onalltaskscomplete.aspx
[net_rdp]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.getrdpfile.aspx
[net_reboot]: https://msdn.microsoft.com/library/azure/mt631495.aspx
[net_reimage]: https://msdn.microsoft.com/library/azure/mt631496.aspx
[net_remove]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.removefrompoolasync.aspx
[net_offline]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.disableschedulingasync.aspx
[net_online]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.enableschedulingasync.aspx
[net_offline_option]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.disablecomputenodeschedulingoption.aspx
[net_rdpfile]: https://msdn.microsoft.com/library/azure/Mt272127.aspx
[vnet]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_netconf

[py_add_user]: https://docs.microsoft.com/azure/python/

[batch_rest_api]: https://msdn.microsoft.com/library/azure/Dn820158.aspx
[rest_add_job]: https://msdn.microsoft.com/library/azure/mt282178.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_cert]: https://msdn.microsoft.com/library/azure/dn820169.aspx
[rest_add_task]: https://msdn.microsoft.com/library/azure/dn820105.aspx
[rest_create_user]: https://msdn.microsoft.com/library/azure/dn820137.aspx
[rest_get_task_info]: https://msdn.microsoft.com/library/azure/dn820133.aspx
[rest_job_schedules]: https://msdn.microsoft.com/library/azure/mt282179.aspx
[rest_multiinstance]: https://msdn.microsoft.com/library/azure/mt637905.aspx
[rest_multiinstancesettings]: https://msdn.microsoft.com/library/azure/dn820105.aspx#multiInstanceSettings
[rest_update_job]: https://msdn.microsoft.com/library/azure/dn820162.aspx
[rest_rdp]: https://msdn.microsoft.com/library/azure/dn820120.aspx
[rest_reboot]: https://msdn.microsoft.com/library/azure/dn820171.aspx
[rest_reimage]: https://msdn.microsoft.com/library/azure/dn820157.aspx
[rest_remove]: https://msdn.microsoft.com/library/azure/dn820194.aspx
[rest_offline]: https://msdn.microsoft.com/library/azure/mt637904.aspx
[rest_online]: https://msdn.microsoft.com/library/azure/mt637907.aspx

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/
