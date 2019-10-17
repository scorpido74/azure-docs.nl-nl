---
title: HANA back-up en herstel op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: HANA-back-up en herstel uitvoeren op SAP HANA op Azure (grote exemplaren)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/16/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4384d29811d29f06422802abba5d3eb1ea5737e9
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430088"
---
# <a name="backup-and-restore"></a>Back-up maken en terugzetten

>[!IMPORTANT]
>Dit artikel is geen vervanging voor de documentatie over SAP HANA beheer of SAP-opmerkingen. Het is belang rijk dat u beschikt over een solide kennis van en ervaring op het SAP HANA beheer en bewerkingen, met name voor back-up, herstel, hoge Beschik baarheid en herstel na nood gevallen. In dit artikel worden scherm afbeeldingen van SAP HANA Studio weer gegeven. De inhoud, de structuur en de aard van de schermen van SAP-beheer Programma's en de hulpprogram ma's zelf kunnen veranderen van SAP HANA release naar release.

Het is belang rijk dat u de stappen en processen die in uw omgeving worden uitgevoerd en met uw HANA-versies en-releases uitoefent. Sommige processen die in dit artikel worden beschreven, zijn vereenvoudigd voor een beter inzicht in het algemeen. Ze zijn niet bedoeld om te worden gebruikt als gedetailleerde stappen voor de uiteindelijke bewerkings handleidingen. Als u bewerkings handleidingen wilt maken voor uw configuraties, test en oefent u uw processen en documenteert u de processen die aan uw specifieke configuraties zijn gerelateerd. 

Een van de belangrijkste aspecten van het werken met data bases is om ze te beschermen tegen onherstelbare gebeurtenissen. De oorzaak van deze gebeurtenissen kan van invloed zijn op een natuur ramp op eenvoudige gebruikers fouten.

Het maken van een back-up van een Data Base, met de mogelijkheid om deze te herstellen naar een bepaald tijdstip, bijvoorbeeld voordat iemand kritieke gegevens heeft verwijderd, maakt het herstellen mogelijk in een staat die zo dicht mogelijk bij de weg ligt voordat de storing is opgetreden.

Er moeten twee typen back-ups worden uitgevoerd om de mogelijkheid te herstellen:

- Database back-ups: volledige, incrementele of differentiële back-ups
- Back-ups van transactie logboeken

Naast de volledige database back-ups die op toepassings niveau worden uitgevoerd, kunt u back-ups met opslag momentopnamen uitvoeren. Opslag momentopnamen vervangen geen back-ups van transactie Logboeken. Back-ups van transactie logboeken zijn belang rijk om de data base te herstellen naar een bepaald tijdstip of om de logboeken van al doorgevoerde trans acties leeg te maken. Met opslag momentopnamen kunt u het herstel versnellen door snel een roll-voorwaartse installatie kopie van de data base te leveren. 

SAP HANA op Azure (grote exemplaren) biedt twee opties voor back-up en herstel:

- **Doe het zelf (zelf).** Nadat u hebt gecontroleerd of er voldoende schijf ruimte beschikbaar is, voert u volledige data base-en logboek back-ups uit met behulp van een van de volgende schijf back-upmethoden. U kunt rechtstreeks een back-up maken van volumes die zijn gekoppeld aan de HANA grote instantie-eenheden of aan NFS-shares die zijn ingesteld op een virtuele Azure-machine (VM). In het laatste geval hebben klanten een virtuele Linux-machine in azure ingesteld, Azure Storage aan de virtuele machine gekoppeld en de opslag delen via een geconfigureerde NFS-server in die VM. Kopieer de back-ups naar een Azure Storage-account als u de back-up uitvoert op volumes die rechtstreeks aan HANA grote instantie-eenheden zijn gekoppeld. Doe dit nadat u een virtuele Azure-machine hebt ingesteld waarmee NFS-shares die zijn gebaseerd op Azure Storage worden geëxporteerd. U kunt ook een Azure Backup kluis of Azure-koude opslag gebruiken. 

   Een andere optie is het gebruik van een hulp programma voor gegevens beveiliging van derden om de back-ups op te slaan nadat ze zijn gekopieerd naar een Azure-opslag account. De zelf-back-upoptie kan ook nodig zijn voor gegevens die u gedurende langere tijd moet opslaan voor nalevings-en controle doeleinden. In alle gevallen worden de back-ups gekopieerd naar NFS-shares die worden weer gegeven via een VM en Azure Storage.

- **Functionaliteit voor back-up en herstel van infra structuur.** U kunt ook de functionaliteit voor back-up en herstel gebruiken die de onderliggende infra structuur van SAP HANA op Azure (grote instanties) biedt. Deze optie voldoet aan de nood zaak van back-ups en snelle herstel bewerkingen. De rest van deze sectie heeft betrekking op de functionaliteit voor back-up en herstel die wordt aangeboden met HANA grote instanties. In deze sectie wordt ook aandacht besteed aan de relatie die back-up en herstel heeft voor de functionaliteit voor herstel na nood gevallen van HANA grote instanties.

> [!NOTE]
>   De snap shot-technologie die wordt gebruikt door de onderliggende infra structuur van HANA grote instanties, is afhankelijk van SAP HANA moment opnamen. Op dit punt werken SAP HANA moment opnamen niet in combi natie met meerdere tenants van SAP HANA data base-containers voor multi tenants. Als er slechts één Tenant is geïmplementeerd, SAP HANA moment opnamen werken en kunt u deze methode gebruiken.

## <a name="use-storage-snapshots-of-sap-hana-on-azure-large-instances"></a>Opslag momentopnamen van SAP HANA gebruiken op Azure (grote exemplaren)

De onderliggende opslag infrastructuur SAP HANA op Azure (grote exemplaren) ondersteunt de opslag momentopnamen van volumes. Het maken van back-ups en het herstellen van volumes wordt ondersteund, met de volgende overwegingen:

- In plaats van volledige database back-ups worden moment opnamen van opslag volumes op een regel matige basis gemaakt.
- Wanneer een moment opname wordt geactiveerd via/Hana/data en/Hana/Shared, die/usr/sap, volumes bevat, initieert de snap shot-technologie een SAP HANA moment opname voordat de moment opname van de opslag wordt uitgevoerd. Deze SAP HANA moment opname is het installatie punt voor de uiteindelijke logboek herstel na herstel van de moment opname van de opslag. Een HANA-moment opname is alleen geslaagd als u een actief HANA-exemplaar hebt. In een HSR-scenario wordt een moment opname van de opslag niet ondersteund op een actief secundair knoop punt waarop een HANA-moment opname niet kan worden uitgevoerd.
- Nadat de opslag momentopname is uitgevoerd, wordt de moment opname van de SAP HANA verwijderd.
- Back-ups van transactie logboeken worden regel matig gemaakt en opgeslagen in het/Hana/logbackups-volume of in Azure. U kunt het/Hana/logbackups-volume dat de back-ups van het transactie logboek bevat, activeren om een moment opname afzonderlijk te maken. In dat geval hoeft u geen HANA-moment opname uit te voeren.
- Als u een Data Base op een bepaald moment moet herstellen voor een productie-uitval, vraagt u dat Microsoft Azure ondersteuning of SAP HANA in azure herstellen naar een bepaalde moment opname van de opslag. Een voor beeld is een geplande herstel bewerking van een sandbox-systeem naar de oorspronkelijke staat.
- De SAP HANA moment opname die is opgenomen in de moment opname van de opslag, is een offset punt voor het Toep assen van back-ups van transactie logboeken die zijn uitgevoerd en die zijn opgeslagen nadat de opslag momentopname is gemaakt.
- Deze back-ups van transactie logboeken worden opgehaald om de data base terug te zetten naar een bepaald punt in de tijd.

U kunt opslag momentopnamen uitvoeren die zijn gericht op drie soorten volumes:

- Een gecombineerde moment opname via/Hana/data en/Hana/Shared, inclusief/usr/sap. Voor deze moment opname is het maken van een moment opname van een SAP HANA vereist als voor bereiding voor de moment opname van de opslag. Met de moment opname van de SAP HANA zorgt u ervoor dat de data base zich in een consistente status bevindt vanuit een opslag punt van de weer gave. Voor het herstel proces is dit een punt om in te stellen.
- Een afzonderlijke moment opname via/Hana/logbackups.
- Een besturingssysteem partitie.

Zie [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de meest recente momentopname scripts en documentatie. Wanneer u het momentopname script pakket downloadt van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md), krijgt u drie bestanden. Een van de bestanden wordt gedocumenteerd in een PDF-bestand voor de beschik bare functionaliteit. Nadat u de toolset hebt gedownload, volgt u de instructies in ' de hulp middelen voor moment opnamen ophalen '.

## <a name="storage-snapshot-considerations"></a>Overwegingen voor opslag momentopname

>[!NOTE]
>Opslag momentopnamen verbruiken opslag ruimte die is toegewezen aan de HANA grote instantie-eenheden. Houd rekening met de volgende aspecten van het plannen van moment opnamen van opslag en het aantal opslag momentopnamen dat moet worden bewaard. 

De specifieke mechanismen van opslag momentopnamen voor SAP HANA op Azure (grote exemplaren) zijn:

- Een specifieke opslag momentopname op het moment dat deze wordt uitgevoerd, verbruikt weinig opslag ruimte.
- Als de inhoud van de gegevens wordt gewijzigd en de inhoud van SAP HANA gegevens bestanden op het opslag volume wordt gewijzigd, moet de moment opname de oorspronkelijke blok inhoud en de gegevens wijzigingen opslaan.
- Als gevolg hiervan wordt de opslag momentopname groter. Hoe langer de moment opname bestaat, hoe groter de moment opname van de opslag wordt.
- Hoe meer wijzigingen worden aangebracht aan het SAP HANA database volume gedurende de levens duur van een opslag momentopname, hoe groter het ruimte verbruik van de moment opname van de opslag.

SAP HANA op Azure (grote exemplaren) wordt geleverd met vaste volume grootten voor de SAP HANA gegevens-en logboek volumes. Het uitvoeren van moment opnamen van deze volumes Eats in uw volume ruimte. U moet het volgende doen:

- Bepaal wanneer u opslag momentopnamen wilt plannen.
- Bewaak het ruimte verbruik van de opslag volumes. 
- Het aantal moment opnamen dat u opslaat beheren. 

U kunt de opslag momentopnamen uitschakelen wanneer u massa's van gegevens importeert of andere belang rijke wijzigingen doorvoert in de HANA-data base. 


De volgende secties bevatten informatie over het uitvoeren van deze moment opnamen en het toevoegen van algemene aanbevelingen:

- Hoewel de hardware 255 moment opnamen per volume kan hebben, kunt u het beste onder dit nummer blijven. De aanbeveling is 250 of minder.
- Voordat u opslag momentopnamen uitvoert, controleert u de beschik bare ruimte en houdt u deze bij.
- Verlaag het aantal opslag momentopnamen op basis van de beschik bare ruimte. U kunt het aantal moment opnamen verlagen dat u behoudt, of u kunt de volumes uitbreiden. U kunt extra opslag ruimte best Ellen in 1 TB eenheden.
- Tijdens activiteiten zoals het verplaatsen van gegevens naar SAP HANA met SAP platform Migration Tools (R3load) of het herstellen van SAP HANA-data bases van back-ups, moet u opslag momentopnamen op het/Hana/data-volume uitschakelen. 
- Vermijd indien mogelijk opslag momentopnamen tijdens grotere reorganisaties van SAP HANA tabellen.
- Opslag momentopnamen zijn een vereiste om te profiteren van de mogelijkheden voor nood herstel van SAP HANA op Azure (grote exemplaren).

## <a name="prerequisites-for-using-self-service-storage-snapshots"></a>Vereisten voor het gebruik van self-service voor opslag momentopnamen

Zorg ervoor dat perl is geïnstalleerd op het Linux-besturings systeem op de HANA-server voor grote instanties om ervoor te zorgen dat het momentopname script correct wordt uitgevoerd. Perl wordt vooraf geïnstalleerd op uw HANA-eenheid voor grote instanties. Als u de perl-versie wilt controleren, gebruikt u de volgende opdracht:

`perl -v`

![De open bare sleutel wordt gekopieerd door deze opdracht uit te voeren](./media/hana-overview-high-availability-disaster-recovery/perl_screen.png)


## <a name="set-up-storage-snapshots"></a>Opslag momentopnamen instellen

Voer de volgende stappen uit om moment opnamen van opslag met HANA grote instanties in te stellen.
1. Zorg ervoor dat perl is geïnstalleerd op het Linux-besturings systeem op de HANA-server voor grote instanties.
1. Wijzig de/etc/ssh/ssh @ no__t-0config om de regel _Macs HMAC-SHA1_toe te voegen.
1. Maak een SAP HANA back-upaccount op het hoofd knooppunt voor elk SAP HANA exemplaar dat u uitvoert, indien van toepassing.
1. Installeer de SAP HANA HDB-client op alle SAP HANA Large Instances servers.
1. Maak op de eerste SAP HANA Large Instances server van elke regio een open bare sleutel voor toegang tot de onderliggende opslag infrastructuur die het maken van moment opnamen beheert.
1. Kopieer de scripts en het configuratie bestand van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) naar de locatie van **hdbsql** in de SAP Hana-installatie.
1. Wijzig zo nodig het *HANABackupDetails. txt* -bestand voor de juiste klant specificaties.

Down load de nieuwste momentopname scripts en documentatie van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). Zie [micro soft snap shot tools for SAP Hana in azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de eerder vermelde stappen.

### <a name="consideration-for-mcod-scenarios"></a>Overwegingen voor MCOD-scenario's
Als u een [MCOD-scenario](https://launchpad.support.sap.com/#/notes/1681092) uitvoert met meerdere exemplaren van SAP Hana op één Hana grote instantie-eenheid, hebt u afzonderlijke opslag volumes ingericht voor elk van de SAP Hana exemplaren. Zie ' belang rijk te onthouden ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over MDC en andere overwegingen.
 

### <a name="step-1-install-the-sap-hana-hdb-client"></a>Stap 1: Installeer de SAP HANA HDB-client

Het Linux-besturings systeem dat is geïnstalleerd op SAP HANA op Azure (grote exemplaren) bevat de mappen en scripts die nodig zijn om SAP HANA opslag momentopnamen uit te voeren voor back-ups en herstel na nood gevallen. Raadpleeg voor meer recente releases in [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md). 

Het is uw verantwoordelijkheid om de SAP HANA HDB-client te installeren op de HANA grote instantie-eenheden tijdens de installatie van SAP HANA.

### <a name="step-2-change-the-etcsshssh_config"></a>Stap 2: Wijzig de/etc/ssh/ssh @ no__t-0config

Deze stap wordt beschreven in ' communicatie inschakelen met opslag ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).


### <a name="step-3-create-a-public-key"></a>Stap 3: een open bare sleutel maken

Als u toegang tot de opslag momentopname-interfaces van uw HANA-Tenant voor grote instanties wilt inschakelen, stelt u een aanmeldings procedure in met een open bare sleutel. 

Maak op de eerste SAP HANA op de server van Azure (grote instanties) in uw Tenant een open bare sleutel voor toegang tot de opslag infrastructuur. Met een open bare sleutel is een wacht woord niet vereist om u aan te melden bij de opslag momentopname-interfaces. U hoeft ook geen wachtwoord referenties met een open bare sleutel te onderhouden. 

Zie ' communicatie inschakelen met opslag ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het genereren van een open bare sleutel.


### <a name="step-4-create-an-sap-hana-user-account"></a>Stap 4: een SAP HANA-gebruikers account maken

Als u het maken van SAP HANA moment opnamen wilt starten, maakt u een gebruikers account in SAP HANA dat door de scripts van de opslag momentopname kan worden gebruikt. Maak voor dit doel een SAP HANA gebruikers account in SAP HANA Studio. De gebruiker moet worden gemaakt onder de SYSTEMDB en *niet* onder de sid-Data Base voor MDC. In de omgeving met één container wordt de gebruiker gemaakt in de Tenant-data base. Dit account moet beschikken over een **back-upbeheer** en lees machtigingen voor de **catalogus** . 

Zie ' communicatie inschakelen met SAP HANA ' in [github](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)om een gebruikers account in te stellen en te gebruiken.


### <a name="step-5-authorize-the-sap-hana-user-account"></a>Stap 5: het gebruikers account van de SAP HANA autoriseren

In deze stap machtigt u het SAP HANA gebruikers account dat u hebt gemaakt, zodat de scripts tijdens runtime geen wacht woorden hoeven in te dienen. Met de SAP HANA opdracht `hdbuserstore` wordt het maken van een SAP HANA gebruikers sleutel ingeschakeld. De sleutel wordt opgeslagen op een of meer SAP HANA knoop punten. Met de gebruikers sleutel krijgt de gebruiker toegang tot SAP HANA zonder dat er wacht woorden hoeven te worden beheerd in het script proces. Het script proces wordt verderop in dit artikel besproken.

>[!IMPORTANT]
>Voer deze configuratie opdrachten uit met dezelfde gebruikers context als waarop de momentopname opdrachten worden uitgevoerd. Anders werken de opdrachten van de moment opname niet goed.


### <a name="step-6-get-the-snapshot-scripts-configure-the-snapshots-and-test-the-configuration-and-connectivity"></a>Stap 6: de momentopname scripts ophalen, de moment opnamen configureren en de configuratie en connectiviteit testen

Down load de meest recente versie van de scripts van [github](https://github.com/Azure/hana-large-instances-self-service-scripts/tree/master/snapshot_tools_v4.1). De manier waarop de scripts worden geïnstalleerd, wordt gewijzigd met versie 4,1 van de scripts. Zie ' communicatie met SAP HANA inschakelen ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

Zie ' eenvoudige installatie van moment opname-hulpprogram ma's (standaard) ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de exacte volg orde van de opdrachten. We raden u aan de standaard installatie te gebruiken. 

Als u een upgrade wilt uitvoeren van versie 3. x naar 4,1, raadpleegt u ' een bestaande installatie bijwerken ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). Zie "verwijderen van de momentopname hulpprogramma's" in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het installeren van het hulp programma 4,1.

Vergeet niet om de stappen uit te voeren die worden beschreven in ' de installatie van snap shot tools volt ooien ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Het doel van de verschillende scripts en bestanden die zijn geïnstalleerd, wordt beschreven in ' wat zijn deze momentopname hulpprogramma's? ' in de [micro soft snap shot-hulpprogram ma's voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Voordat u de momentopname hulpprogramma's configureert, moet u ervoor zorgen dat u ook de HANA-back-uplocaties en-instellingen correct hebt geconfigureerd. Zie "SAP HANA-configuratie" in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

De configuratie van het hulp programma set snap shots wordt beschreven in ' config file-HANABackupCustomerDetails. txt ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

#### <a name="test-connectivity-with-sap-hana"></a>De connectiviteit met SAP HANA testen

Nadat u alle configuratie gegevens in het *HANABackupCustomerDetails. txt* -bestand hebt geplaatst, controleert u of de configuraties correct zijn voor de Hana-exemplaar gegevens. Gebruik het script `testHANAConnection`, dat onafhankelijk is van een SAP HANA scale-up-of scale-out-configuratie.

Zie ' connectiviteit controleren met SAP HANA-testHANAConnection ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie.

#### <a name="test-storage-connectivity"></a>Opslag connectiviteit testen

De volgende test stap is het controleren van de verbinding met de opslag op basis van de gegevens die u in het configuratie bestand *HANABackupCustomerDetails. txt* hebt geplaatst. Voer vervolgens een test momentopname uit. Voordat u de `azure_hana_backup`-opdracht uitvoert, moet u deze test uitvoeren. Zie ' connectiviteit controleren met Storage-testStorageSnapshotConnection ' ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de volg orde van opdrachten voor deze test.

Na een geslaagde aanmelding bij de interfaces van de virtuele opslag machine wordt het script voortgezet met fase 2 en wordt een moment opname van de test gemaakt. De uitvoer wordt hier weer gegeven voor een scale-out configuratie met drie knoop punten van SAP HANA.

Als de test momentopname met het script wordt uitgevoerd, kunt u de werkelijke opslag momentopnamen plannen. Als dat niet lukt, onderzoekt u de problemen voordat u verdergaat. De test momentopname moet in de buurt blijven totdat de eerste echte moment opnamen worden uitgevoerd.


### <a name="step-7-perform-snapshots"></a>Stap 7: moment opnamen uitvoeren

Wanneer de voorbereidende stappen zijn voltooid, kunt u beginnen met het configureren en plannen van de werkelijke opslag momentopnamen. Het script dat moet worden gepland, werkt met SAP HANA scale-up-en scale-out-configuraties. Voor periodieke en periodieke uitvoering van het back-upscript moet u het script plannen met behulp van het hulp programma cron. 

Zie ' snap shot backup-azure_hana_backup ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de exacte opdracht syntaxis en-functionaliteit. 

Wanneer het script `azure_hana_backup` wordt uitgevoerd, maakt het de moment opname van de opslag in de volgende drie fasen:

1. Er wordt een moment opname van SAP HANA uitgevoerd.
1. Er wordt een moment opname van de opslag uitgevoerd.
1. De SAP HANA moment opname die is gemaakt voordat de opslag momentopname werd uitgevoerd, wordt verwijderd.

Als u het script wilt uitvoeren, roept u het aan vanuit de uitvoer bare HDB-map waarnaar deze is gekopieerd. 

De retentie tijd wordt beheerd met het aantal moment opnamen dat als een para meter wordt verzonden wanneer u het script uitvoert. De hoeveelheid tijd die wordt gedekt door de moment opnamen van de opslag is een functie van de uitvoerings periode en van het aantal moment opnamen dat is verzonden als een para meter wanneer het script wordt uitgevoerd. 

Als het aantal moment opnamen dat wordt bewaard groter is dan het aantal dat wordt genoemd als een para meter in de aanroep van het script, wordt de oudste opslag momentopname van hetzelfde label verwijderd voordat een nieuwe moment opname wordt uitgevoerd. Het nummer dat u opgeeft als laatste para meter van de aanroep is het getal dat u kunt gebruiken om het aantal moment opnamen te bepalen dat wordt bewaard. Met dit aantal kunt u ook de schijf ruimte die wordt gebruikt voor moment opnamen beheren, indirect. 


## <a name="snapshot-strategies"></a>Momentopname strategieën
De frequentie van moment opnamen voor de verschillende typen is afhankelijk van het feit of u de functie voor herstel na nood gevallen in de HANA-versie gebruikt. Deze functionaliteit is afhankelijk van opslag momentopnamen, waarvoor speciale aanbevelingen kunnen worden vereist voor de frequentie-en uitvoerings perioden van de moment opnamen van de opslag. 

In de overwegingen en aanbevelingen die volgen, is de veronderstelling dat u *geen* gebruik maakt van de functionaliteit voor herstel na nood gevallen die door grote instanties wordt aangeboden. In plaats daarvan gebruikt u de opslag momentopnamen om back-ups te maken en kunt u voor de afgelopen 30 dagen herstel naar een bepaald tijdstip aanbieden. Gezien de beperkingen van het aantal moment opnamen en ruimte, moet u rekening houden met de volgende vereisten:

- De herstel tijd voor herstel naar een bepaald tijdstip.
- De gebruikte ruimte.
- De doel stellingen voor herstel punten en herstel tijd voor mogelijke herstel na een nood geval.
- De uiteindelijke uitvoering van HANA Full-Data Base-back-ups op schijven. Wanneer een back-up van de volledige data base wordt uitgevoerd op schijven of de **backint** -interface, mislukt de uitvoering van de opslag momentopnamen. Als u back-ups van de volledige data base boven op de opslag momentopnamen wilt uitvoeren, moet u ervoor zorgen dat de uitvoering van de opslag momentopnamen gedurende deze tijd is uitgeschakeld.
- Het aantal moment opnamen per volume, beperkt tot 250.

<!-- backint is term for a SAP HANA interface and not a spelling error not spelling errors -->

Als u geen gebruik maakt van de functionaliteit voor nood herstel van HANA grote instanties, is de momentopname periode minder vaak. In dergelijke gevallen voert u de gecombineerde moment opnamen op/Hana/data en/Hana/Shared uit, waaronder/usr/sap, in 12-uurs of 24-uurs Peri Oden. Bewaar de moment opnamen voor een maand. Hetzelfde geldt voor de moment opnamen van het back-upvolume van het logboek. Het uitvoeren van back-ups van SAP HANA transactie logboeken voor het back-upvolume van het logboek vindt plaats in peri Oden van vijf tot vijf tien minuten.

Geplande opslag momentopnamen worden het best uitgevoerd met behulp van cron. Gebruik hetzelfde script voor alle back-ups en nood herstel. Wijzig de script invoer zodat deze overeenkomt met de verschillende aangevraagde back-uptijden. Deze moment opnamen zijn allemaal anders gepland in cron, afhankelijk van de uitvoerings tijd. Dit kan elk uur, elke 12 uur, dagelijks of wekelijks zijn. 

In het volgende voor beeld ziet u een cron-schema in/etc/crontab:
```
00 1-23 * * * ./azure_hana_backup --type=hana --prefix=hourlyhana --frequency=15min --retention=46
10 00 * * *  ./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
00,05,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
22 12 * * *  ./azure_hana_backup --type=logs --prefix=dailylogback --frequncy=3min --retention=28
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```
In het vorige voor beeld wordt een samen voeging met een uur gecombineerd met de volumes die de/Hana/data en/hana/shared/SID bevatten, waaronder/usr/sap, locaties. Gebruik dit type moment opname voor een snellere herstel naar een bepaald tijdstip in de afgelopen twee dagen. Er is ook een dagelijkse moment opname op die volumes. U hebt dus twee dagen dekking per uur, plus vier weken aan dekking per dag moment opnamen. Er wordt dagelijks een back-up van het back-upvolume van het transactie logboek gemaakt. Deze back-ups worden vier weken bewaard. 

Zoals u in de derde regel van crontab ziet, wordt de back-up van het HANA-transactie logboek gepland om elke vijf minuten te worden uitgevoerd. De begin tijden van de verschillende cron-taken waarmee opslag momentopnamen worden uitgevoerd, worden gespreid. Op deze manier worden de moment opnamen niet allemaal tegelijk uitgevoerd op een bepaald moment. 

In het volgende voor beeld voert u een gecombineerde moment opname uit die de volumes bedekt die de/Hana/data en/hana/shared/SID bevatten, waaronder/usr/sap, locaties op basis van elk uur. U kunt deze moment opnamen twee dagen blijven gebruiken. De moment opnamen van de back-upvolumes van het transactie logboek worden op een periode van vijf minuten uitgevoerd en worden vier uur bewaard. Net als voorheen wordt de back-up van het HANA-transactie logboek bestand elke vijf minuten uitgevoerd. 

De moment opname van het back-upvolume van het transactie logboek wordt met een vertraging van 2 minuten uitgevoerd nadat de back-up van het transactie logboek is gestart. Onder normale omstandigheden wordt de back-up van het SAP HANA transactie logboek binnen die 2 minuten voltooid. Net als voorheen wordt een back-up van het volume met de opstart-LUN eenmaal per dag met een moment opname van de opslag gemaakt en gedurende vier weken bewaard.

```
10 0-23 * * * ./azure_hana_backup --type=hana ==prefix=hourlyhana --frequency=15min --retention=48
0,5,10,15,20,25,30,35,40,45,50,55 * * * * ./azure_hana_backup --type=logs --prefix=regularlogback --frequency=3min --retention=28
2,7,12,17,22,27,32,37,42,47,52,57 * * * *  ./azure_hana_backup --type=logs --prefix=logback --frequency=3min --retention=48
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

In de volgende afbeelding ziet u de volg orde van het vorige voor beeld. De opstart-LUN is uitgesloten.

![Relatie tussen back-ups en moment opnamen](./media/hana-overview-high-availability-disaster-recovery/backup_snapshot_updated0921.PNG)

SAP HANA voert regel matige schrijf bewerkingen uit op het/Hana/log-volume om de doorgevoerde wijzigingen in de data base te documenteren. SAP HANA schrijft regel matig een opslag punt naar het/Hana/data-volume. Zoals opgegeven in crontab, wordt een back-up van het transactie logboek van SAP HANA elke vijf minuten uitgevoerd. 

U ziet ook dat een moment opname van een SAP HANA elk uur wordt uitgevoerd als gevolg van het activeren van een gecombineerde opslag momentopname via de/Hana/data-en/hana/shared/SID-volumes. Nadat de HANA-moment opname is geslaagd, wordt de gecombineerde opslag momentopname uitgevoerd. Zoals een instructie in crontab, wordt de opslag momentopname op het/Hana/logbackup-volume elke vijf minuten uitgevoerd, ongeveer 2 minuten na de registratie van het HANA-transactie logboek.

> 

>[!IMPORTANT]
> Het gebruik van moment opnamen van opslag voor SAP HANA back-ups is alleen waardevol wanneer de moment opnamen in combi natie met SAP HANA transactie logboek back-ups worden uitgevoerd. Deze back-ups van transactie logboeken moeten de tijds perioden tussen de moment opnamen van de opslag hebben. 

Als u een toezeg ging hebt ingesteld voor gebruikers van een herstel naar een bepaald tijdstip van 30 dagen, moet u het volgende doen:

- Krijg in uitzonderlijke gevallen toegang tot een gecombineerde opslag momentopname via/Hana/data en/hana/shared/SID die 30 dagen oud zijn. 
- U hebt aaneengesloten back-ups van transactie logboeken die de tijd tussen een van de gecombineerde opslag momentopnamen beslaan. Daarom moet de oudste moment opname van het back-upvolume van het transactie logboek 30 dagen oud zijn. Dit is niet het geval als u de back-ups van het transactie logboek kopieert naar een andere NFS-share die zich op Azure Storage bevindt. In dat geval kunt u oude back-ups van transactie logboeken ophalen van die NFS-share.

Wijzig de locatie waarnaar SAP HANA de back-ups van transactie logboeken schrijft om te profiteren van back-ups van opslag en de uiteindelijke opslag replicatie van transactie Logboeken. U kunt deze wijziging aanbrengen in HANA Studio. 

Hoewel SAP HANA automatisch volledige logboek segmenten maakt, geeft u een logboek back-upinterval op die deterministisch moet zijn. Dit geldt met name wanneer u de optie voor nood herstel gebruikt, omdat u meestal logboek back-ups wilt uitvoeren met een deterministische periode. In het volgende geval wordt 15 minuten ingesteld als het back-upinterval van het logboek.

![SAP HANA back-uplogboeken plannen in SAP HANA Studio](./media/hana-overview-high-availability-disaster-recovery/image5-schedule-backup.png)

U kunt ook back-ups kiezen die vaker dan elke 15 minuten zijn. Een meer frequentere instelling wordt vaak gebruikt in combi natie met de functionaliteit voor herstel na nood gevallen van HANA grote instanties. Sommige klanten voeren elke vijf minuten back-ups van transactie Logboeken uit.

Als er nog geen back-up van de data base is gemaakt, is de laatste stap het uitvoeren van een database back-up op basis van een bestand om één back-upitem te maken dat in de back-upcatalogus moet bestaan. Anders kunnen SAP HANA de opgegeven logboek back-ups niet initialiseren.

![Maak een back-up op basis van een bestand om één back-upvermelding te maken](./media/hana-overview-high-availability-disaster-recovery/image6-make-backup.png)


Nadat de eerste geslaagde opslag momentopnamen zijn uitgevoerd, verwijdert u de moment opname van de test die u in stap 6 hebt uitgevoerd. Zie ' test moment opnamen verwijderen-removeTestStorageSnapshot ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie. 


### <a name="monitor-the-number-and-size-of-snapshots-on-the-disk-volume"></a>Het aantal en de grootte van moment opnamen op het schijf volume bewaken

Op een specifiek opslag volume kunt u het aantal moment opnamen en het opslag verbruik van deze moment opnamen bewaken. De `ls` opdracht wordt niet weer gegeven in de map met moment opnamen of bestanden. De Linux-besturingssysteem opdracht `du` bevat details over deze opslag momentopnamen, omdat deze zijn opgeslagen op dezelfde volumes. Gebruik de opdracht met de volgende opties:

- `du –sh .snapshot`: deze optie biedt een totaal van alle moment opnamen in de map met moment opnamen.
- `du –sh --max-depth=1`: met deze optie worden alle moment opnamen vermeld die zijn opgeslagen in de map **. snap shot** en de grootte van elke moment opname.
- `du –hc`: deze optie biedt de totale grootte die wordt gebruikt door alle moment opnamen.

Gebruik deze opdrachten om ervoor te zorgen dat de moment opnamen die worden gemaakt en opgeslagen niet alle opslag op de volumes verbruiken.

>[!NOTE]
>De moment opnamen van de opstart-LUN worden niet weer gegeven met de vorige opdrachten.

### <a name="get-details-of-snapshots"></a>Details van moment opnamen ophalen
Gebruik het script `azure_hana_snapshot_details` voor meer informatie over moment opnamen. U kunt dit script uitvoeren op een van beide locaties als er een actieve server is op de locatie voor nood herstel. Het script biedt de volgende uitvoer, onderverdeeld op elk volume dat moment opnamen bevat: 
   * De grootte van het totale aantal moment opnamen in een volume
   * De volgende details in elke moment opname in dat volume: 
      - Naam van moment opname 
      - Tijd maken 
      - Grootte van de moment opname
      - Frequentie van de moment opname
      - HANA-back-up-ID die is gekoppeld aan deze moment opname, indien van toepassing

Zie ' List snap shots-azure_hana_snapshot_details ' in [micro soft snap shot tools for SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor de syntaxis van de opdracht en uitvoer. 



### <a name="reduce-the-number-of-snapshots-on-a-server"></a>Het aantal moment opnamen op een server verminderen

Zoals eerder uitgelegd, kunt u het aantal specifieke labels van moment opnamen die u opslaat, beperken. De laatste twee para meters van de opdracht voor het initiëren van een moment opname zijn het label en het aantal moment opnamen dat u wilt behouden.

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=28
```

In het vorige voor beeld is het momentopname label **dailyhana**. Het aantal moment opnamen met dit label dat moet worden bewaard, is **28**. Wanneer u reageert op het gebruik van schijf ruimte, wilt u mogelijk het aantal opgeslagen moment opnamen verlagen. Een eenvoudige manier om het aantal moment opnamen te verminderen tot 15, bijvoorbeeld om het script uit te voeren waarbij de laatste para meter is ingesteld op **15**:

```
./azure_hana_backup --type=hana --prefix=dailyhana --frequency=15min --retention=15
```

Als u het script uitvoert met deze instelling, is het aantal moment opnamen, dat de nieuwe opslag momentopname bevat, 15. De 15 meest recente moment opnamen worden bewaard en de 15 oudere moment opnamen worden verwijderd.

 >[!NOTE]
 > Dit script beperkt het aantal moment opnamen alleen als er moment opnamen zijn die meer dan één uur oud zijn. Het script verwijdert geen moment opnamen die kleiner zijn dan één uur oud. Deze beperkingen zijn gerelateerd aan de optionele functionaliteit voor herstel na nood gevallen.

Als u een set met moment opnamen niet meer wilt onderhouden met het back-upvoorvoegsel **dailyhana** in de syntaxis voorbeelden, voert u het script uit met **0** als het Bewaar nummer. Alle moment opnamen die overeenkomen met dit label worden vervolgens verwijderd. Het verwijderen van alle moment opnamen kan invloed hebben op de mogelijkheden van de functionaliteit voor herstel na nood gevallen van HANA grote instanties.

Een tweede optie voor het verwijderen van specifieke moment opnamen is het gebruik van het script `azure_hana_snapshot_delete`. Dit script is ontworpen om een moment opname of set met moment opnamen te verwijderen met behulp van de HANA-back-up-ID zoals gevonden in HANA Studio of via de naam van de moment opname zelf. Op dit moment is de back-upid alleen gekoppeld aan de moment opnamen die zijn gemaakt voor het **Hana** -momentopname type. Momentopname back-ups van het type **Logboeken** en **opstarten** voeren geen SAP Hana moment opname uit, dus er is geen back-upid voor deze moment opnamen. Als de naam van de moment opname wordt ingevoerd, zoekt deze naar alle moment opnamen op de verschillende volumes die overeenkomen met de opgegeven naam van de moment opname. 

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->

Zie ' een moment opname verwijderen-azure_hana_snapshot_delete ' in [micro soft snap shot tools voor SAP Hana op Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md)voor meer informatie over het script.

Voer het script uit als **hoofdmap**van de gebruiker.

>[!IMPORTANT]
>Als er gegevens zijn die alleen bestaan op de moment opname die u wilt verwijderen, gaan de gegevens na de moment opname verloren.


## <a name="file-level-restore-from-a-storage-snapshot"></a>Herstel op bestands niveau vanuit een moment opname van een opslag

<!-- hana, logs and boot are no spelling errors as Acrolinx indicates, but terms of parameter values -->
Voor de momentopname typen **Hana** en **logs**, hebt u rechtstreeks toegang tot de moment opnamen op de volumes in de map **. snap shot** . Er is een submap voor elk van de moment opnamen. Kopieer elk bestand in de staat waarin het zich bevond in het punt van de moment opname van die submap in de daad werkelijke mapstructuur. 

In de huidige versie van het script is er *geen* herstel script voor het terugzetten van de moment opname als self-service. Het terugzetten van de moment opname kan worden uitgevoerd als onderdeel van de scripts voor herstel na nood geval tijdens een failover. Als u een gewenste moment opname van de bestaande beschik bare moment opnamen wilt herstellen, moet u contact opnemen met het micro soft Operations-team door een service aanvraag te openen.

>[!NOTE]
>Het herstellen van één bestand werkt niet voor moment opnamen van de opstart-LUN onafhankelijk van het type van de HANA grote instantie-eenheden. De map **. snap shot** wordt niet weer gegeven in de opstart-LUN. 
 

## <a name="recover-to-the-most-recent-hana-snapshot"></a>Herstellen naar de meest recente HANA-moment opname

In een scenario met productie kan het proces van het herstellen van een opslag momentopname worden gestart als een klant incident met Microsoft Azure ondersteuning. Het is zeer urgent als gegevens in een productie systeem zijn verwijderd en de enige manier om deze op te halen, het herstellen van de productie database.

In een andere situatie is het mogelijk dat herstel naar een bepaald tijdstip weinig urgentie en geplande dagen van tevoren zijn. U kunt dit herstel plannen met SAP HANA op Azure in plaats van een vlag met hoge prioriteit te verhogen. U kunt bijvoorbeeld plannen om de SAP-software bij te werken door een nieuw uitbreidings pakket toe te passen. Vervolgens moet u terugkeren naar een moment opname die de status voor de upgrade van het pakket aangeeft.

Voordat u de aanvraag verzendt, moet u de voor bereiding doen. De SAP HANA op het Azure-team kan vervolgens de aanvraag afhandelen en de herstelde volumes opgeven. Daarna herstelt u de HANA-Data Base op basis van de moment opnamen.

Voor de mogelijkheden van het ophalen van een moment opname met de nieuwe set hulp middelen raadpleegt u "een moment opname herstellen" in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een moment opname van de opslag](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Voer de volgende stappen uit om de aanvraag voor te bereiden.

1. Bepaal welke moment opname moet worden hersteld. Alleen het Hana/gegevens volume wordt hersteld, tenzij u anders een instructie geeft. 

1. Sluit het HANA-exemplaar af.

   ![Het HANA-exemplaar afsluiten](./media/hana-overview-high-availability-disaster-recovery/image7-shutdown-hana.png)

1. Ontkoppel de gegevens volumes op elk HANA-database knooppunt. Als de gegevens volumes nog steeds aan het besturings systeem zijn gekoppeld, mislukt het herstellen van de moment opname.

   ![De gegevens volumes op elk HANA-database knooppunt ontkoppelen](./media/hana-overview-high-availability-disaster-recovery/image8-unmount-data-volumes.png)

1. Open een ondersteunings aanvraag voor Azure en neem instructies op over het herstel van een specifieke moment opname:

   - Tijdens de herstel bewerking: SAP HANA op de Azure-service wordt u mogelijk gevraagd om een telefonische oproep te nemen om te coördineren, te controleren en te bevestigen dat de juiste opslag momentopname is hersteld. 

   - Nadat de herstel bewerking is SAP HANA op de Azure-service, ontvangt u een melding wanneer de moment opname van de opslag is teruggezet.

1. Nadat het herstel proces is voltooid, koppelt u alle gegevens volumes opnieuw.

   ![Alle gegevens volumes opnieuw koppelen](./media/hana-overview-high-availability-disaster-recovery/image9-remount-data-volumes.png)



Een andere mogelijkheid om bijvoorbeeld SAP HANA gegevens bestanden te verkrijgen die zijn hersteld vanuit een moment opname van een opslag, wordt beschreven in stap 7 in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een opslag momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md).

Als u wilt herstellen vanuit een back-up van moment opnamen, raadpleegt u [hand matige herstel handleiding voor SAP Hana op Azure vanuit een opslag momentopname](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 

>[!Note]
>Als uw moment opname is hersteld door micro soft Operations, hoeft u stap 7 niet uit te voeren.


### <a name="recover-to-another-point-in-time"></a>Herstellen naar een ander tijdstip
Als u naar een bepaald tijdstip wilt herstellen, raadpleegt u ' de data base herstellen naar het volgende tijdstip ' in [hand matige herstel handleiding voor SAP Hana op Azure vanuit een moment opname van de opslag](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20Guide.md). 


## <a name="next-steps"></a>Volgende stappen
- Zie [principes van herstel na nood gevallen en voor bereiding](hana-concept-preparation.md).
