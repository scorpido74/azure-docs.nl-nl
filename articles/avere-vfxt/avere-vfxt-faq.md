---
title: Veelgestelde vragen - Avere vFXT voor Azure
description: Veelgestelde vragen over Avere vFXT voor Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 12/19/2019
ms.author: rohogue
ms.openlocfilehash: 186b2c048a9de42318e4af287393d731a4eb16f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76153459"
---
# <a name="avere-vfxt-for-azure-faq"></a>Veelgestelde vragen over Avere vFXT for Azure

In dit artikel worden vragen beantwoord die u kunnen helpen beslissen of Avere vFXT voor Azure geschikt is voor uw behoeften. Het geeft basisinformatie over Avere vFXT en legt uit hoe het werkt met andere Azure-componenten en met producten van externe leveranciers.

## <a name="general"></a>Algemeen

### <a name="what-is-avere-vfxt-for-azure"></a>Wat is Avere vFXT for Azure?

Avere vFXT voor Azure is een performevend bestandssysteem dat actieve gegevens in Azure-gegevens caches voor een efficiënte verwerking van kritieke workloads.

### <a name="is-avere-vfxt-a-storage-solution"></a>Is Avere vFXT een opslagoplossing?

Nee. Avere vFXT voor Azure is een *bestandssysteemcache* die wordt gekoppeld aan opslagomgevingen, zoals uw EMC of NetApp NAS, of een Azure blob-container. Avere vFXT stroomlijnt gegevensaanvragen van clients en caches de gegevens die het dient om de prestaties op schaal en in de loop van de tijd te verbeteren. Avere vFXT zelf slaat geen gegevens op. Het heeft geen informatie over de hoeveelheid gegevens die erachter zijn opgeslagen.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Is Avere vFXT een tiering oplossing?

Avere vFXT voor Azure controleert niet automatisch gegevens tussen hot en cool lagen.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Hoe weet ik of een omgeving geschikt is voor Avere vFXT?

De beste manier om na te denken over deze vraag is om te vragen: "Is de werkbelasting cachebaar?" Dat wil zeggen, heeft de werklast een hoge lees-naar-schrijfverhouding? Een voorbeeld is 80/20 of 70/30 leest/schrijft.

Overweeg Avere vFXT voor Azure als u een op bestanden gebaseerde analytische pijplijn hebt die wordt uitgevoerd over een groot aantal virtuele Azure-machines en voldoet aan een of meer van de volgende voorwaarden:

* De algehele prestaties zijn traag of inconsistent vanwege lange bestandstoegangstijden (tientallen milliseconden of seconden, afhankelijk van de vereisten). Deze latentie is onaanvaardbaar voor de klant.

* Gegevens die nodig zijn voor verwerking bevinden zich aan het uiteinde van een WAN-omgeving en het is onpraktisch om die gegevens permanent te verplaatsen. De gegevens bevinden zich mogelijk in een andere Azure-regio of in een klantdatacenter.

* Een aanzienlijk aantal clients vraagt de gegevens op, bijvoorbeeld in een HPC-cluster (high-performance computing). Het grote aantal gelijktijdige aanvragen kan de latentie verhogen.

* De klant wil zijn huidige pijplijn "as is" uitvoeren in azure virtuele machines, en heeft een POSIX-gebaseerde shared storage (of caching) oplossing nodig voor schaalbaarheid. Door Avere vFXT voor Azure te gebruiken, hoeft u de werkpijplijn niet opnieuw te ontwerpen om native calls naar Azure Blob-opslag te voeren.

* Uw HPC-toepassing is gebaseerd op NFSv3-clients. (In sommige omstandigheden kan het smb 2.1-clients gebruiken, maar de prestaties zijn beperkt.)

Met het volgende diagram u deze vraag beantwoorden. Hoe dichter uw workflow zich bovenaan bevindt, hoe groter de kans dat de Avere vFXT voor Azure caching-oplossing geschikt is voor uw omgeving.

![Grafiekdiagram waaruit blijkt dat leeszware lasten met duizenden clients beter geschikt zijn voor Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Op welke schaal van klanten heeft de Avere vFXT oplossing het meest zinvol?

De Avere vFXT-cacheoplossing is gebouwd om honderden, duizenden of tienduizenden rekenkernen te verwerken. Als u een paar machines hebt die licht werken, is Avere vFXT niet de juiste oplossing.

Typische Avere vFXT-klanten draaien veeleisende workloads vanaf ongeveer 1.000 CPU-cores. Deze omgevingen kunnen zo groot zijn als 50.000 cores of meer. Omdat Avere vFXT schaalbaar is, u knooppunten toevoegen om deze workloads te ondersteunen terwijl ze groeien om meer doorvoer of meer IOPS te vereisen.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Hoeveel gegevens kan een Avere vFXT-omgeving opslaan?

Avere vFXT voor Azure is een cache. Het slaat niet specifiek gegevens op. Het maakt gebruik van een combinatie van RAM en SSD's om de gegevens in de cache op te slaan. De gegevens worden permanent opgeslagen op een back-end opslagsysteem (bijvoorbeeld een NetApp NAS-systeem of een blobcontainer). Het Avere vFXT-systeem heeft geen informatie over de hoeveelheid gegevens die erachter zijn opgeslagen. Avere vFXT caches alleen de subset van die gegevens die clients vragen.  

### <a name="what-regions-are-supported"></a>Welke regio's worden ondersteund?

Avere vFXT voor Azure wordt ondersteund in alle regio's, behalve in soevereine regio's (China, Duitsland). Zorg ervoor dat de regio die u wilt gebruiken, de grote hoeveelheid rekenkernen en de VM-exemplaren kan ondersteunen die nodig zijn om het Avere vFXT-cluster te maken.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Hoe krijg ik hulp met Avere vFXT?

Een gespecialiseerde groep ondersteuningsmedewerkers biedt hulp met Avere vFXT voor Azure. Volg de instructies in [Help met uw systeem](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) om een ondersteuningsticket te openen vanaf de Azure-portal.

### <a name="is-avere-vfxt-highly-available"></a>Is Avere vFXT zeer beschikbaar?

Ja, Avere vFXT draait uitsluitend als HA-oplossing.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Ondersteunt Avere vFXT voor Azure ook andere cloudservices?

Ja, klanten kunnen meer dan één cloudprovider gebruiken met het Avere vFXT-cluster. Het ondersteunt AWS S3-standaardbuckets, Standaardbuckets van Google Cloud Services en Azure blob-containers.

> [!NOTE]
> Er zijn softwarekosten van toepassing voor het gebruik van Avere vFXT met AWS- of Google Cloud-opslag. Er zijn geen extra softwarekosten verbonden aan het gebruik van Azure blob-opslag.

## <a name="technical-compute"></a>Technisch: Compute

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>u beschrijven hoe een Avere vFXT-omgeving eruit ziet?

Avere vFXT is een geclusterd toestel gemaakt van meerdere Azure virtuele machines. Een Python-bibliotheek verwerkt het maken, verwijderen en wijzigen van het cluster. Lees [Wat is Avere vFXT voor Azure?](avere-vfxt-overview.md)

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Op wat voor soort Azure virtuele machines draait Avere vFXT?  

Een Avere vFXT voor Azure-cluster maakt gebruik van Microsoft Azure-E32s_v3 virtuele machines.

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.
-->

### <a name="does-the-avere-vfxt-environment-scale"></a>Schaalt de Avere vFXT-omgeving?

Het Avere vFXT-cluster kan zo klein zijn als drie virtuele machineknooppunten of zo groot als 24 knooppunten. Neem contact op met de technische ondersteuning van Azure voor hulp bij de planning als u denkt dat u een cluster van meer dan negen knooppunten nodig hebt. Het grotere aantal knooppunten vereist een grotere implementatiearchitectuur.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>Is de Avere vFXT omgeving "autoscale"?

Nee. U de clustergrootte op en neer schalen, maar het toevoegen of verwijderen van clusterknooppunten is een handmatige stap.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>Kan ik het Avere vFXT-cluster uitvoeren als een virtuele machineschaalset?

Avere vFXT ondersteunt geen implementatie van een virtuele machineschaalset. Verschillende ingebouwde beschikbaarheidsondersteuningsmechanismen zijn alleen ontworpen voor atomaire VM's die deelnemen aan een cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>Kan ik het Avere vFXT-cluster uitvoeren op VM's met lage prioriteit?

Nee, het systeem vereist een onderliggende stabiele set virtuele machines.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>Kan ik het Avere vFXT-cluster in containers uitvoeren?

Nee, Avere vFXT moet worden geïmplementeerd als een onafhankelijke toepassing.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Tellen de Avere vFXT VM's mee voor mijn compute quota?

Ja. Zorg ervoor dat u voldoende quota in de regio hebt om het cluster te ondersteunen.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>Kan ik de Avere vFXT-clustermachines in verschillende beschikbaarheidszones uitvoeren?

Nee. Het hoge beschikbaarheidsmodel in Avere vFXT biedt momenteel geen ondersteuning voor individuele Avere vFXT-clusterleden in verschillende beschikbaarheidszones.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>Kan ik Avere vFXT virtuele machines klonen?

Nee, u moet het ondersteunde Python-script gebruiken om knooppunten toe te voegen of te verwijderen in het Avere vFXT-cluster. Lees Voor meer informatie [het avere vFXT-cluster beheren.](avere-vfxt-manage-cluster.md)

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>Is er een "VM" versie van de software die ik kan draaien in mijn eigen lokale omgeving?

Nee, het systeem wordt aangeboden als een geclusterd toestel en getest op specifieke virtuele machinetypes. Deze beperking helpt klanten te voorkomen dat ze een systeem maken dat de hoge prestatievereisten van een typische Avere vFXT-workflow niet ondersteunt.

## <a name="technical-disks"></a>Technisch: Schijven

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Welke typen schijven worden ondersteund voor de Azure VM's?

Avere vFXT voor Azure kan gebruik maken van 1-TB of 4-TB premium SSD configuraties. De premium SSD-configuratie kan worden geïmplementeerd als meerdere beheerde schijven.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Ondersteunt het cluster onbeheerde schijven?

Nee, het cluster vereist beheerde schijven.

### <a name="does-the-system-support-local-attached-ssds"></a>Ondersteunt het systeem lokale (bijgevoegde) SSD's?

Avere vFXT voor Azure biedt momenteel geen ondersteuning voor lokale SSD's. Schijven die worden gebruikt voor Avere vFXT moeten kunnen worden afgesloten en opnieuw opgestart, maar lokale gekoppelde SSD's in deze configuratie kunnen alleen worden beëindigd.

### <a name="does-the-system-support-ultra-ssds"></a>Ondersteunt het systeem ultra-SSD's?

Nee, het systeem ondersteunt alleen premium SSD-configuraties.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>Kan ik mijn premium SSD's loskoppelen en ze later opnieuw koppelen om cache-inhoud tussen het gebruik te bewaren?

Het loskoppelen en opnieuw bevestigen van SSD's wordt niet ondersteund. Metagegevens of bestandsinhoud op de bron kunnen tussen het gebruik zijn gewijzigd, wat problemen met de integriteit van gegevens kan veroorzaken.

### <a name="does-the-system-encrypt-the-cache"></a>Versleutelt het systeem de cache?

Gegevens worden over de schijven gestreept, maar niet versleuteld. De schijven zelf kunnen echter worden versleuteld. Zie [Beleid beveiligen en gebruiken op virtuele machines in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption)voor meer informatie.

## <a name="technical-networking"></a>Technisch: Netwerken

### <a name="what-network-is-recommended"></a>Welk netwerk wordt aanbevolen?

Als u on-premises opslag gebruikt met Avere vFXT, moet u een 1-Gbps of betere netwerkverbinding hebben tussen uw opslag en het cluster. Als u een kleine hoeveelheid gegevens hebt en bereid bent om gegevens naar de cloud te kopiëren voordat u taken uitvoert, kan vpn-connectiviteit voldoende zijn.

> [!TIP]
> Hoe langzamer de netwerkkoppeling is, hoe langzamer de eerste "koude" leest zal zijn. Langzame leest verhogen de latentie van de werkpijplijn.

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>Kan ik Avere vFXT uitvoeren in een ander virtueel netwerk dan mijn compute cluster?

Ja, u uw Avere vFXT-systeem in een ander virtueel netwerk maken. Lees [Plan uw Avere vFXT-systeem](avere-vfxt-deploy-plan.md) voor meer informatie.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Heeft Avere vFXT een eigen subnet nodig?

Ja. Avere vFXT draait strikt als een HA-cluster (high availability) en vereist meerdere IP-adressen om te werken. Als het cluster zich in een eigen subnet bevindt, vermijdt u het risico van IP-adresconflicten, wat problemen kan veroorzaken voor de installatie en de normale werking. Het subnet van het cluster kan zich binnen een virtueel netwerk bevinden dat door andere bronnen wordt gebruikt, zolang er geen IP-adressen elkaar overlappen.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>Kan ik Avere vFXT draaien op InfiniBand?

Nee, Avere vFXT maakt alleen gebruik van Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Hoe krijg ik toegang tot mijn on-premises NAS-omgeving van Avere vFXT?

De Avere vFXT-omgeving is net als elke andere Azure VM in die zin dat het vereist gerouteerd toegang via een netwerk gateway of VPN naar de klant datacenter (en terug). Overweeg azure ExpressRoute-connectiviteit te gebruiken als deze beschikbaar is in uw omgeving.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Wat zijn de bandbreedtevereisten voor Avere vFXT?

De algemene bandbreedtevereiste is afhankelijk van twee factoren:

* De hoeveelheid gegevens die bij de bron wordt opgevraagd
* De tolerantie van het clientsysteem voor latentie tijdens het laden van gegevens  

Voor latentiegevoelige omgevingen moet u een vezeloplossing gebruiken met een minimale verbindingssnelheid van 1 Gbps. Gebruik ExpressRoute als deze beschikbaar is.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>Kan ik Avere vFXT uitvoeren met openbare IP-adressen?

Nee, Avere vFXT is bedoeld om te worden gebruikt in een netwerkomgeving beveiligd door middel van best practices.

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>Kan ik de toegang tot internet beperken tot het virtuele netwerk van mijn cluster?

In het algemeen u indien nodig extra beveiliging op uw virtuele netwerk configureren, maar sommige beperkingen kunnen de werking van het cluster verstoren.

Het beperken van uitgaande internettoegang vanuit uw virtuele netwerk veroorzaakt bijvoorbeeld problemen voor het cluster, tenzij u ook een regel toevoegt die expliciet toegang tot AzureCloud toestaat. Deze situatie wordt beschreven in [aanvullende documentatie op GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Neem voor hulp bij aangepaste beveiliging contact op met ondersteuning zoals beschreven in [Hulp bij uw systeem.](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt)

## <a name="technical-back-end-storage-core-filers"></a>Technisch: Back-end opslag (core filers)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Hoeveel core filers ondersteunt één Avere vFXT-omgeving?

Een Avere vFXT-cluster ondersteunt maximaal 20 core-filers.

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>Hoe slaat de Avere vFXT-omgeving gegevens op?

Avere vFXT is geen opslag. Het is een cache die gegevens van meerdere opslagdoelen leest en schrijft, de zogenaamde core filers. Gegevens die zijn opgeslagen op premium SSD-schijven in Avere vFXT zijn van voorbijgaande aard en worden uiteindelijk doorgespoeld naar de back-end core filer-opslag.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Welke core filers ondersteunt Avere vFXT?

In het algemeen ondersteunt Avere vFXT voor Azure de volgende systemen als core filers:

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 en 8.1) 
* NetApp ONTAP (geclusterde modus 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) en (7-Modus 7.*, 8.0-8.3)

* Azure blob-containers (alleen lokaal redundante opslag)
* AWS S3-buckets
* Google Cloud-buckets

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Waarom ondersteunt Avere vFXT niet alle NFS-filers?

Hoewel alle NFS-platforms aan dezelfde IETF-normen voldoen, heeft elke implementatie in de praktijk zijn eigen eigenaardigheden. Deze details beïnvloeden hoe Avere vFXT met het opslagsysteem omgaat. De ondersteunde systemen zijn de meest gebruikte platforms in de markt.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Ondersteunt Avere vFXT private object storage (zoals SwiftStack)?

Avere vFXT ondersteunt geen private objectopslag.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>Hoe kan ik een specifiek opslagproduct onder ondersteuning krijgen?

Ondersteuning is gebaseerd op de hoeveelheid vraag in het veld. Als er voldoende op inkomsten gebaseerde verzoeken zijn om een NAS-oplossing te ondersteunen, zullen we dit overwegen. Doe aanvragen via Azure-ondersteuning.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>Kan ik Azure Blob-opslag gebruiken als een core filer?

Ja, Avere vFXT voor Azure kan een block blob-container gebruiken als cloudcore-filer.

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Wat zijn de vereisten voor opslagaccount voor een blobcore-filer?

Uw opslagaccount moet een G2-account (gpv2) voor algemene doeleinden zijn en alleen zijn geconfigureerd voor lokaal redundante opslag. Georedundante opslag en zoneredundante opslag worden niet ondersteund.

Lees [Azure Blob Storage cloud core filer](avere-vfxt-add-storage.md#azure-blob-storage-cloud-core-filer) voor meer informatie over de vereisten voor opslagaccount.

### <a name="can-i-use-archive-blob-storage"></a>Kan ik archiefblobopslag gebruiken?

Nee. De service-level agreement (SLA) voor archiefopslag is niet compatibel met de realtime directory- en bestandstoegangsbehoeften van het Avere vFXT-systeem.

### <a name="can-i-use-cool-blob-storage"></a>Kan ik coole blob-opslag gebruiken?

Cool tier blob-opslag wordt meestal niet aanbevolen voor een Avere vFXT voor Azure core filer. Cool tier biedt lagere opslagkosten, maar hogere bedrijfskosten. (Zie [Blob-prijzen blokkeren](<https://azure.microsoft.com/pricing/details/storage/blobs/>) voor meer informatie.) Als gegevens vaak worden geopend en gewijzigd of verwijderd, u overwegen de laag Hot te gebruiken.

[Access-lagen](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers#cool-access-tier) geven meer informatie over wanneer het zinvol is om Cool tier-opslag te gebruiken als een vFXT-core filer.

### <a name="how-do-i-encrypt-the-blob-container"></a>Hoe versleutel ik de blobcontainer?

U blob-versleuteling configureren in Azure (voorkeur) of op het niveau van de Avere vFXT-kernfiler.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>Kan ik mijn eigen encryptiesleutel gebruiken voor een blob core filer?

Standaard worden gegevens versleuteld via door Microsoft beheerde sleutels voor Azure Blob-, Tabel- en Queue-opslag, plus Azure-bestanden. U uw eigen sleutel voor versleuteling voor Blob-opslag en Azure-bestanden meenemen. Als u ervoor kiest om Avere vFXT-versleuteling te gebruiken, moet u de door Avere gegenereerde sleutel gebruiken en lokaal opslaan.

## <a name="purchasing"></a>Aanschaffen

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Hoe krijg ik Avere vFXT voor Azure-licenties?

Het verkrijgen van een Avere vFXT voor Azure-licentie is eenvoudig via de Azure Marketplace. Meld u aan voor een Azure-account en volg de instructies in [Het Avere vFXT-cluster implementeren](avere-vfxt-deploy.md) om een Avere vFXT-cluster te maken.

### <a name="how-much-does-avere-vfxt-cost"></a>Hoeveel kost Avere vFXT?

In Azure zijn er geen extra licentiekosten voor het gebruik van Avere vFXT-clusters. Klanten zijn verantwoordelijk voor opslag en andere Azure-verbruikskosten.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Kunnen Avere vFXT VM's worden uitgevoerd als lage prioriteit?

Nee, Avere vFXT-clusters vereisen "always on" service. De clusters kunnen worden uitgeschakeld wanneer dit niet nodig is.

## <a name="next-steps"></a>Volgende stappen

Lees deze artikelen om aan de slag te gaan met Avere vFXT voor Azure:

* [Uw Avere vFXT-systeem plannen](avere-vfxt-deploy-plan.md)
* [Implementatieoverzicht](avere-vfxt-deploy-overview.md)
* [Bereid je voor om een Avere vFXT-cluster te maken](avere-vfxt-prereqs.md)
* [Het Avere vFXT-cluster implementeren](avere-vfxt-deploy.md)

Ga voor meer informatie over mogelijkheden en use cases voor Avere vFXT naar [Avere vFXT voor Azure.](https://azure.microsoft.com/services/storage/avere-vfxt/)
