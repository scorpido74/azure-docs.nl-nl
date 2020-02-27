---
title: Back-upgids voor SAP HANA op Azure Virtual Machines | Microsoft Docs
description: De back-upgids voor SAP HANA biedt twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/05/2018
ms.author: hermannd
ms.openlocfilehash: 8de83cbb7060e6ca5390720a4a241be71bb9dc92
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617433"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Back-upgids voor SAP HANA op Azure Virtual Machines

## <a name="getting-started"></a>Aan de slag

In de back-upgids voor SAP HANA die op virtuele machines van Azure worden uitgevoerd, worden alleen specifieke onderwerpen van Azure beschreven. Raadpleeg de SAP HANA documentatie (Zie _SAP Hana back-updocumentatie_ verderop in dit artikel) voor algemene SAP Hana back-up van gerelateerde items.

De focus van dit artikel is op twee belang rijke back-upmogelijkheden voor SAP HANA op virtuele machines van Azure:

- HANA-back-up naar het bestands systeem in een virtuele machine van Azure Linux (Zie [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md))
- HANA-back-up op basis van moment opnamen van opslag met behulp van de Azure Storage BLOB snap shot-functie hand matig of Azure Backup Service ( [SAP Hana Zie back-ups op basis van opslag momentopnamen](sap-hana-backup-storage-snapshots.md))

SAP HANA biedt een back-upapi waarmee back-uptools van derden rechtstreeks kunnen worden geïntegreerd met SAP HANA. (Dat valt niet binnen het bereik van deze hand leiding.) Er is op dit moment geen directe integratie van SAP HANA met Azure Backup-Service beschikbaar.

SAP HANA wordt officieel ondersteund op verschillende typen virtuele Azure-machines, zoals Azure M-series. Voor een volledige lijst met SAP HANA gecertificeerde Azure-Vm's raadpleegt u [gecertificeerde IaaS-platforms zoeken](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Dit artikel wordt bijgewerkt als nieuwe aanbiedingen voor SAP HANA op Azure beschikbaar worden.

Er is ook een SAP HANA hybride oplossing beschikbaar op Azure, waarbij SAP HANA niet-gevirtualiseerd op fysieke servers wordt uitgevoerd. Deze SAP HANA Azure Backup Guide bevat echter een zuivere Azure-omgeving waarin SAP HANA in een Azure-VM wordt uitgevoerd, en niet SAP HANA worden uitgevoerd op &quot;grote instanties.&quot; Zie [SAP Hana (grote instanties) overzicht en architectuur op Azure](hana-overview-architecture.md) voor meer informatie over deze back-upoplossing op &quot;grote instanties&quot; gebaseerd op opslag momentopnamen.

Algemene informatie over SAP-producten die worden ondersteund in azure, vindt u in [SAP Note 1928533](https://launchpad.support.sap.com/#/notes/1928533).

De volgende drie cijfers geven een overzicht van de SAP HANA back-upopties die momenteel gebruikmaken van systeem eigen Azure-mogelijkheden en er worden ook drie mogelijke back-upscenario's weer gegeven. In de verwante artikelen [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md) en [SAP Hana back-up op basis van moment opnamen van opslag](sap-hana-backup-storage-snapshots.md) , worden deze opties uitvoeriger beschreven, met inbegrip van de grootte-en prestatie overwegingen voor SAP Hana back-ups met meerdere terabytes.

![In deze afbeelding ziet u twee mogelijkheden voor het opslaan van de huidige status van de virtuele machine](media/sap-hana-backup-guide/image001.png)

In deze afbeelding ziet u de mogelijkheid om de huidige status van de virtuele machine op te slaan via Azure Backup Service of hand matige moment opname van VM-schijven. Met deze aanpak moet er één worden&#39;gebruikt voor het beheren van SAP Hana back-ups. De uitdaging van het schijf momentopname scenario is consistentie van het bestands systeem en een toepassings consistente schijf status. Het consistentie onderwerp wordt beschreven in de sectie _SAP Hana gegevens consistentie bij het maken van moment opnamen van opslag_ verderop in dit artikel. De mogelijkheden en beperkingen van Azure Backup service met betrekking tot SAP HANA back-ups worden ook verderop in dit artikel besproken.

![In deze afbeelding ziet u de opties voor het maken van een back-up van een SAP HANA-bestand in de VM](media/sap-hana-backup-guide/image002.png)

In deze afbeelding ziet u opties voor het maken van een back-up van SAP HANA-bestanden in de virtuele machine en vervolgens het opslaan van de HANA-back-upbestanden op een andere manier. Het maken van een HANA-back-up vergt meer tijd dan een back-upoplossing op basis van moment opnamen, maar heeft voor delen met betrekking tot integriteit en consistentie. Verderop in dit artikel vindt u meer informatie.

![In deze afbeelding ziet u een potentieel toekomstig SAP HANA back-upscenario](media/sap-hana-backup-guide/image003.png)

In deze afbeelding ziet u een potentieel toekomstig SAP HANA back-upscenario. Als SAP HANA back-ups van een secundaire replicatie mag maken, worden er aanvullende opties voor back-upstrategieen toegevoegd. Momenteel is het niet mogelijk op basis van een bericht in de wiki SAP HANA:

_&quot;is het mogelijk om back-ups te maken aan de secundaire zijde?_

_Nee, op dit moment kunt u alleen back-ups maken van gegevens en logboeken aan de primaire zijde. Als automatische logboek back-up is ingeschakeld, wordt de back-up van de logboeken automatisch geschreven na de overname van de secundaire server.&quot;_

## <a name="sap-resources-for-hana-backup"></a>SAP-bronnen voor HANA-back-ups

### <a name="sap-hana-backup-documentation"></a>SAP HANA back-updocumentatie

- [Inleiding tot SAP HANA beheer](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [De strategie voor back-up en herstel plannen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [HANA-back-ups plannen met ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Gegevens back-ups plannen (SAP HANA cockpit)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Veelgestelde vragen over SAP HANA back-up in [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Veelgestelde vragen over SAP HANA data base-en opslag momentopnamen in [SAP-notitie 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Niet-geschikte netwerk bestands systemen voor back-up en herstel in [SAP-notitie 1820529](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Waarom SAP HANA back-up?

Azure Storage biedt Beschik baarheid en betrouw baarheid van de doos (Zie [Inleiding tot Microsoft Azure Storage](../../../storage/common/storage-introduction.md) voor meer informatie over Azure Storage).

De minimale voor &quot;back-up&quot; is afhankelijk van de Azure-Sla's, waarbij de SAP HANA gegevens en logboek bestanden op Azure-Vhd's die zijn gekoppeld aan de SAP HANA Server-VM. Deze aanpak heeft betrekking op VM-fouten, maar geen mogelijke schade aan de SAP HANA gegevens en logboek bestanden, of logische fouten zoals het verwijderen van gegevens of bestanden per ongeluk. Back-ups zijn ook vereist om te voldoen aan het beleid. Kortom, er is altijd SAP HANA back-ups nodig.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>De juistheid van SAP HANA back-up controleren
Wanneer u opslag momentopnamen gebruikt, wordt het uitvoeren van een test herstel op een ander systeem aanbevolen. Deze benadering biedt een manier om ervoor te zorgen dat een back-up correct is en dat interne processen voor back-up en herstel naar verwachting werken. Hoewel dit een belang rijke hindernis is, is het veel eenvoudiger om in de cloud te komen door tijdelijk vereiste bronnen te bieden voor dit doel.

Houd er wel zeker van dat u een eenvoudige herstel bewerking uitvoert en controleert of HANA actief is en niet voldoende is. In het ideale geval moet er een tabel consistentie controle worden uitgevoerd om er zeker van te zijn dat de herstelde data base prima is. SAP HANA biedt verschillende soorten consistentie controles die worden beschreven in [SAP Note 1977584](https://launchpad.support.sap.com/#/notes/1977584).

Informatie over de consistentie controle van de tabel vindt u ook op de SAP-website op [consistentie controles van tabellen en catalogi](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Voor back-ups van standaard bestanden is het niet nodig om een test herstel uit te kunnen zetten. Er zijn twee SAP HANA-hulpprogram ma's waarmee u kunt controleren welke back-up kan worden gebruikt voor herstel: hdbbackupdiag en hdbbackupcheck. Zie [hand matig controleren of een herstel kan worden](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) uitgevoerd voor meer informatie over deze hulpprogram ma's.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Voor-en nadelen van HANA-back-ups versus opslag momentopname

SAP heeft&#39;voor keur voor een Hana-back-up en een moment opname van de opslag. De lijst bevat de voor-en nadelen, waardoor er een kan worden bepaald welke gegevens moeten worden gebruikt, afhankelijk van de situatie en de beschik bare opslag technologie (Zie [uw strategie voor back-up en herstel plannen](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

Op Azure moet u zich bewust zijn van het feit dat de functie&#39;voor de moment opname van de Azure Blob de bestandssysteem consistentie van het-garantie bestand (Zie het gebruik van BLOB- [moment opnamen met Power shell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). In de volgende sectie, _SAP Hana consistentie van gegevens bij het maken van moment opnamen van opslag_, worden enkele aandachtspunten met betrekking tot deze functie besproken.

Daarnaast moet één inzicht hebben in de facturerings implicaties bij het werken met Blob-moment opnamen, zoals beschreven in dit artikel: inzicht in de [manier waarop moment opnamen worden samengevoegd](/rest/api/storageservices/understanding-how-snapshots-accrue-charges). dit hebben&#39;t als duidelijk wanneer u virtuele Azure-schijven gebruikt.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>SAP HANA consistentie van gegevens bij het maken van moment opnamen van opslag

Consistentie van het bestands systeem en de toepassing is een complex probleem bij het maken van moment opnamen van opslag. De eenvoudigste manier om problemen te voor komen, is om SAP HANA af te sluiten of misschien zelfs de hele virtuele machine. Een afsluiting kan worden doable met een demonstratie of prototype, of zelfs op een ontwikkel systeem, maar dit is geen optie voor een productie systeem.

In azure moet u er ook&#39;voor zorgen dat de functie voor de moment opname van de Azure Blob de bestandssysteem consistentie van het systeem garandeert. Het werkt echter goed met behulp van de functie voor moment opnamen van SAP HANA, zolang er slechts één virtuele schijf betrokken is. Maar zelfs met één schijf moeten extra items worden gecontroleerd. [SAP Note 2039883](https://launchpad.support.sap.com/#/notes/2039883) heeft belang rijke informatie over het SAP Hana van back-ups via opslag momentopnamen. Er wordt bijvoorbeeld vermeld dat, met het bestands systeem XFS, het nodig is om **XFS\_te blok keren** voordat een opslag momentopname wordt gestart om consistentie te garanderen (zie [xfs\_bevriezen (8)-Linux-man pagina](https://linux.die.net/man/8/xfs_freeze) voor meer informatie over **xfs\_bevriezen**).

Het onderwerp van consistentie wordt nog steeds lastiger in gevallen waarin één bestands systeem meerdere schijven/volumes omvat. U kunt bijvoorbeeld mdadm of LVM en striping gebruiken. De SAP-opmerking hierboven vermelde statussen:

_&quot;maar houd er rekening mee dat het opslag systeem de I/O-consistentie moet garanderen tijdens het maken van een opslag momentopname per SAP HANA gegevens volume, d.w.z. snapshotting van een SAP HANA servicespecifieke gegevens volume moet een Atomic-bewerking zijn.&quot;_

Ervan uitgaande dat er een XFS-bestands systeem is met vier virtuele schijven van Azure, bieden de volgende stappen een consistente moment opname die het HANA-gegevens gebied vertegenwoordigt:

- Voor bereiding van HANA-moment opname
- Het bestands systeem blok keren (gebruik bijvoorbeeld **xfs\_bevriezen**)
- Alle benodigde BLOB-moment opnamen maken op Azure
- De blok kering van het bestands systeem opheffen
- De HANA-moment opname bevestigen

Aanbeveling is de bovenstaande procedure in alle gevallen aan de kluis zijde te gebruiken, ongeacht het bestands systeem. Of als het één schijf of striping is, via mdadm of LVM op meerdere schijven.

Het is belang rijk om de HANA-moment opname te bevestigen. Als gevolg van de &quot;copy-on-write, is voor&quot; SAP HANA mogelijk geen extra schijf ruimte nodig tijdens deze modus voor het voorbereiden van de moment opname. &#39;Het is ook niet mogelijk om nieuwe back-ups te starten totdat de moment opname van SAP Hana is bevestigd.

Azure Backup-Service gebruikt Azure VM-extensies om de consistentie van het bestands systeem te maken. Deze VM-extensies zijn niet beschikbaar voor zelfstandig gebruik. Er moet nog steeds SAP HANA consistentie worden beheerd. Zie het gerelateerde artikel [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md) voor meer informatie.

### <a name="sap-hana-backup-scheduling-strategy"></a>Strategie voor het plannen van back-ups SAP HANA

In het artikel SAP HANA de [strategie voor back-up en herstel plannen,](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) een basis plan voor het uitvoeren van back-ups:

- Opslag momentopname (dagelijks)
- Gegevens back-up volt ooien met behulp van de bestands-of bacint-indeling (één keer per week)
- Automatische back-ups van Logboeken

Optioneel kan er volledig worden zonder moment opnamen van de opslag. ze kunnen worden vervangen door HANA Delta back-ups, zoals incrementele of differentiële back-ups (Zie [Delta back-ups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

De HANA-beheer handleiding bevat een lijst met voor beelden. Hiermee wordt voorgesteld dat één herstel SAP HANA naar een bepaald tijdstip wordt gedaan met behulp van de volgende reeks back-ups:

1. Volledige gegevens back-up
2. Differentiële back-up
3. Incrementele back-up 1
4. Incrementele back-up 2
5. Logboek back-ups

Met betrekking tot een exacte planning zoals wanneer en hoe vaak een specifiek back-uptype moet plaatsvinden, is het niet mogelijk om een algemene richt lijn te geven, dat is te specifiek voor de klant en is afhankelijk van het aantal gegevens wijzigingen in het systeem. Een basis aanbeveling van SAP Side, die als algemene richt lijn kan worden gezien, is om één keer per week een volledige HANA-back-up te maken.
Zie de SAP HANA documentatie [logboek back-ups](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)voor logboek back-ups.

SAP raadt u ook aan enkele housekeeping van de back-upcatalogus te doen, zodat deze oneindig groeit (Zie [housekeeping voor back-upcatalogus en back-upopslag](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Configuratie bestanden SAP HANA

Zoals vermeld in de veelgestelde vragen over [SAP Note 1642148](https://launchpad.support.sap.com/#/notes/1642148), maken de SAP Hana configuratie bestanden geen deel uit van een standaard Hana-back-up. Ze zijn niet essentieel voor het herstellen van een systeem. De HANA-configuratie kan na het herstellen hand matig worden gewijzigd. Als er tijdens het herstel proces dezelfde aangepaste configuratie moet worden verkregen, is het nood zakelijk om een back-up te maken van de HANA-configuratie bestanden.

Als standaard HANA-back-ups naar een toegewezen HANA-back-upbestandssysteem gaan, kan de configuratie bestanden ook naar hetzelfde back-upbestand worden gekopieerd en vervolgens alles kopiëren naar de uiteindelijke opslag bestemming zoals cool Blob Storage.

### <a name="sap-hana-cockpit"></a>SAP HANA cockpit

SAP HANA cockpit biedt de mogelijkheid om SAP HANA te bewaken en te beheren via een browser. Ook is het mogelijk om SAP HANA back-ups te verwerken en kan daarom worden gebruikt als alternatief voor SAP HANA Studio en ABAP DBACOCKPIT (Zie [SAP Hana cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) voor meer informatie).

![In deze afbeelding ziet u het scherm SAP HANA cockpit-database beheer](media/sap-hana-backup-guide/image004.png)

In deze afbeelding ziet u het scherm SAP HANA cockpit-database beheer en de back-uptegel aan de linkerkant. Voor het weer geven van de back-uptegel zijn de juiste gebruikers machtigingen vereist voor het aanmeldings account.

![Back-ups kunnen worden bewaakt in SAP HANA cockpit terwijl ze actief zijn](media/sap-hana-backup-guide/image005.png)

U kunt back-ups in SAP HANA cockpit controleren wanneer ze actief zijn en, zodra deze is voltooid, alle back-upgegevens beschikbaar zijn.

![Een voor beeld van het gebruik van Firefox op een Azure SLES 12 VM met gnome desktop](media/sap-hana-backup-guide/image006.png)

De vorige scherm afbeeldingen zijn gemaakt op basis van een Azure Windows-VM. Dit is een voor beeld van het gebruik van Firefox op een Azure SLES 12 VM met gnome desktop. Hier ziet u de optie voor het definiëren van SAP HANA back-upschemas in SAP HANA cockpit. Zoals u ziet, wordt de datum/tijd voorgesteld als voor voegsel voor de back-upbestanden. In SAP HANA Studio wordt het standaard voorvoegsel &quot;volledige\_DATA\_back-up&quot; bij het uitvoeren van een volledige back-up van een bestand. U wordt aangeraden een uniek voor voegsel te gebruiken.

### <a name="sap-hana-backup-encryption"></a>Back-upversleuteling SAP HANA

SAP HANA biedt versleuteling van gegevens en Logboeken. Als SAP HANA gegevens en logboek niet zijn versleuteld, worden de back-ups ook niet versleuteld. Het is de klant in staat te stellen om een vorm van een oplossing van derden te gebruiken voor het versleutelen van de SAP HANA back-ups. Zie [gegevens en logboek volume versleuteling](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) voor meer informatie over SAP Hana versleuteling.

Op Microsoft Azure kan een klant de IaaS-functie voor VM-versleuteling gebruiken om te versleutelen. Een voor beeld: een toegewezen gegevens schijven die zijn gekoppeld aan de virtuele machine, die worden gebruikt voor het opslaan van SAP HANA back-ups, en het maken van kopieën van deze schijven.

Azure Backup-service kan versleutelde Vm's/schijven verwerken (Zie [back-ups maken van versleutelde virtuele machines en deze herstellen met Azure backup](../../../backup/backup-azure-vms-encryption.md)).

Een andere mogelijkheid is om de SAP HANA virtuele machine en de bijbehorende schijven te onderhouden zonder versleuteling, en de SAP HANA back-upbestanden op te slaan in een opslag account waarvoor versleuteling is ingeschakeld (Zie [Azure Storage service versleuteling voor gegevens op rest](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Setup testen

### <a name="test-virtual-machine-on-azure"></a>Virtuele machine testen op Azure

Om onze tests uit te voeren, werd een SAP HANA installatie in een Azure GS5-VM gebruikt voor de volgende tests voor back-up/herstel. De principes zijn hetzelfde als voor virtuele machines uit de M-serie.

![In deze afbeelding ziet u een deel van de Azure Portal-overzicht van de HANA-test-VM](media/sap-hana-backup-guide/image007.png)

In deze afbeelding ziet u een deel van de Azure Portal overzicht van de HANA-test-VM.

### <a name="test-backup-size"></a>Grootte van de back-up testen

![Deze afbeelding is overgenomen van de back-upconsole in HANA Studio en toont de grootte van het back-upbestand van 229 GB voor de HANA-Index Server](media/sap-hana-backup-guide/image008.png)

Een dummy tabel is gevuld met gegevens voor het verkrijgen van een totale gegevens back-up van meer dan 200 GB om realistische prestatie gegevens af te leiden. De afbeelding is overgenomen van de back-upconsole in HANA Studio en toont de grootte van het back-upbestand van 229 GB voor de HANA-Index Server. Voor de tests is het standaard back-upvoorvoegsel ' COMPLETE_DATA_BACKUP ' in SAP HANA Studio gebruikt. In echte productie systemen moet een nuttig voor voegsel worden gedefinieerd. De datum/tijd wordt voorgesteld door SAP HANA cockpit.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Test programma voor het rechtstreeks kopiëren van bestanden naar Azure Storage

Als u SAP HANA back-upbestanden rechtstreeks wilt overdragen naar Azure Blob-opslag of Azure-bestands shares, is het blobxfer-hulp programma gebruikt omdat het beide doelen ondersteunt. het kan eenvoudig worden geïntegreerd in Automation-scripts vanwege de opdracht regel interface. Het hulp programma blobxfer is beschikbaar op [github](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Schatting van de grootte van de back-up testen

Het is belang rijk om de back-upgrootte van SAP HANA te schatten. Deze schatting helpt de prestaties te verbeteren door de maximale bestands grootte voor back-ups voor een aantal back-upbestanden te definiëren als gevolg van parallellisme tijdens het kopiëren van een bestand. (Deze details worden verderop in dit artikel beschreven.) Er moet ook worden besloten of u een volledige back-up of een Delta back-up wilt maken (incrementeel of differentieel).

Gelukkig is er een eenvoudige SQL-instructie die de grootte van de back-upbestanden schat: **selecteer \* van M\_back-up\_grootte\_schattingen** (Zie [de benodigde ruimte in het bestands systeem voor een gegevens back-up schatten](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![De uitvoer van deze SQL-instructie komt bijna exact overeen met de werkelijke grootte van de volledige gegevens back-up op schijf](media/sap-hana-backup-guide/image009.png)

Voor het test systeem komt de uitvoer van deze SQL-instructie overeen met bijna precies de werkelijke grootte van de volledige gegevens back-up op schijf.

### <a name="test-hana-backup-file-size"></a>De grootte van HANA-back-upbestanden testen

![Met de HANA Studio-back-upconsole kan één de maximale bestands grootte van HANA-back-upbestanden worden beperkt](media/sap-hana-backup-guide/image010.png)

Met de HANA Studio-back-upconsole kan één de maximale bestands grootte van HANA-back-upbestanden beperken. In de voorbeeld omgeving maakt deze functie het mogelijk om meerdere kleinere back-upbestanden op te halen in plaats van een 1 230-GB-back-up. Kleinere bestanden hebben een grote invloed op de prestaties (Zie het gerelateerde artikel [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Samenvatting

Op basis van de test resultaten bevatten de volgende tabellen voor-en nadelen van oplossingen om een back-up te maken van een SAP HANA-data base die wordt uitgevoerd op virtuele machines van Azure.

**Back-ups maken van SAP HANA naar het bestands systeem en back-upbestanden kopiëren na de laatste back-upbestemming**

|Oplossing                                           |Professionals                                 |Nadelen                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|HANA-back-ups op VM-schijven blijven                      |Geen extra beheer taken     |Eats van lokale VM-schijf ruimte           |
|Blobxfer-hulp programma voor het kopiëren van back-upbestanden naar Blob Storage |Parallelle uitvoering van het kopiëren van meerdere bestanden, keuze voor het gebruik van cool Blob Storage | Extra onderhoud van hulp middelen en aangepaste scripts | 
|BLOB Copy via Power shell of CLI                    |Er kan geen extra hulp programma worden uitgevoerd via Azure Power shell of CLI |hand matig proces, de klant moet het uitvoeren van scripts en het beheer van gekopieerde blobs voor herstel|
|Kopiëren naar NFS-share                                  |Naverwerking van back-upbestanden op een andere VM zonder invloed op de HANA-server|Langzaam kopieer proces|
|Blobxfer kopiëren naar Azure File-Service                |Geen ruimte op lokale VM-schijven.|Geen ondersteuning voor directe schrijf bewerkingen door HANA-back-up, grootte beperking van bestands share momenteel op 5 TB|
|Azure Backup Agent                                 | Is de voorkeurs oplossing         | Momenteel niet beschikbaar in Linux    |



**Back-SAP HANA op basis van opslag momentopnamen**

|Oplossing                                           |Professionals                                 |Nadelen                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Azure Backup-Service                               | Maakt VM-back-ups op basis van BLOB-moment opnamen | Wanneer u herstel op bestands niveau niet gebruikt, moet u een nieuwe virtuele machine maken voor het herstel proces. Dit impliceert vervolgens de nood zaak van een nieuwe SAP HANA licentie sleutel|
|Hand matige BLOB-moment opnamen                              | Flexibiliteit voor het maken en herstellen van specifieke VM-schijven zonder de unieke VM-ID te wijzigen|Alle hand matige werkzaamheden, die door de klant moeten worden uitgevoerd|

## <a name="next-steps"></a>Volgende stappen
* [SAP HANA Azure Backup op bestands niveau](sap-hana-backup-file-level.md) wordt de back-upoptie op basis van bestanden beschreven.
* Met [SAP Hana back-up op basis van opslag momentopnamen](sap-hana-backup-storage-snapshots.md) wordt de back-upoptie gebaseerd op opslag momentopnamen beschreven.
* Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie over het tot stand brengen van een hoge Beschik baarheid en het plannen van nood herstel van SAP Hana op Azure (grote exemplaren).
