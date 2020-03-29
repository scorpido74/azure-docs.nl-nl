---
title: Overzicht van Microsoft Azure StorSimple Virtual Array
description: Beschrijft de StorSimple Virtual Array, een geïntegreerde opslagoplossing die opslagtaken beheert tussen een on-premises virtuele array en Microsoft Azure-cloudopslag.
author: alkohli
ms.assetid: 169c639b-1124-46a5-ae69-ba9695525b77
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 32781a83aec996b23f161f5fe695f39a0de38685
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273874"
---
# <a name="introduction-to-the-storsimple-virtual-array"></a>Inleiding tot de StorSimple Virtual Array

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

## <a name="overview"></a>Overzicht

De Microsoft Azure StorSimple Virtual Array is een geïntegreerde opslagoplossing die opslagtaken beheert tussen een on-premises virtuele array die wordt uitgevoerd in een hypervisor en Microsoft Azure-cloudopslag. De virtuele array is een efficiënte, kosteneffectieve en eenvoudig beheerde bestandsserver- of iSCSI-serveroplossing die veel van de problemen en kosten elimineert die verband houden met bedrijfsopslag en gegevensbescherming. De virtuele array is bijzonder geschikt voor de opslag van zelden geopende archiefgegevens.

Dit artikel geeft een overzicht van de virtuele array - hier zijn enkele andere bronnen:

* Zie Aanbevolen procedures voor Aanbevolen Procedures voor Aanbevolen Procedures [voor Virtuele array.](storsimple-ova-best-practices.md)
* Voor een overzicht van de StorSimple 8000-serie apparaten, ga naar [StorSimple 8000-serie: een hybride cloudoplossing.](storsimple-overview.md)
* Ga voor informatie over de StorSimple 5000/7000-serie apparaten naar [StorSimple Online Help.](http://onlinehelp.storsimple.com/)

De virtuele array ondersteunt het smb-protocol (iSCSI- of Server Message Block). Het draait op uw bestaande hypervisor-infrastructuur en biedt gelaagdheid naar de cloud, cloudback-up, snelle herstel, item-level recovery en disaster recovery-functies.

In de volgende tabel worden de belangrijke kenmerken van de StorSimple Virtual Array samengevat.

| Functie | StorSimple Virtual Array |
| --- | --- |
| Installatievereisten |Maakt gebruik van virtualisatie-infrastructuur (Hyper-V of VMware) |
| Beschikbaarheid |Eén knooppunt |
| Totale capaciteit (inclusief cloud) |Tot 64 TB bruikbare capaciteit per virtuele array |
| Lokale capaciteit |390 GB tot 6,4 TB bruikbare capaciteit per virtuele array (moet 500 GB tot 8 TB schijfruimte inrichten) |
| Native protocollen |iSCSI of SMB |
| Beoogde hersteltijd (RTO) |iSCSI: minder dan 2 minuten, ongeacht de grootte |
| Recovery Point Objective (RPO) |Dagelijkse back-ups en on-demand back-ups |
| Opslaglaaglaag |Gebruikt heatmapping om te bepalen welke gegevens moeten worden gelaagd in of uit |
| Ondersteuning |Virtualisatie-infrastructuur ondersteund door de leverancier |
| Prestaties |Varieert afhankelijk van de onderliggende infrastructuur |
| Datamobiliteit |Kan herstellen naar hetzelfde apparaat of item-level recovery uitvoeren (bestandsserver) |
| Opslaglagen |Lokale hypervisoropslag en cloud |
| Grootte van delen |Gelaagd: tot 20 TB; lokaal vastgepind: tot 2 TB |
| Volumegrootte |Gelaagd: 500 GB tot 5 TB; lokaal vastgemaakt: 50 GB tot 200 GB <br> Maximale lokale reservering voor gelaagde volumes is 200 GB. |
| Momentopnamen |Crashconsistent |
| Herstel op itemniveau |Ja, ik heb het niet eens gebruikers kunnen herstellen van aandelen |

## <a name="why-use-storsimple"></a>Waarom StorSimple gebruiken?

StorSimple verbindt gebruikers en servers binnen enkele minuten met Azure-opslag, zonder wijzigingen in de toepassing.

In de volgende tabel worden enkele van de belangrijkste voordelen beschreven die de StorSimple Virtual Array-oplossing biedt.

| Functie | Voordeel |
| --- | --- |
| Transparante integratie |De virtuele array ondersteunt het iSCSI- of het SMB-protocol. De gegevensverplaatsing tussen de lokale laag en de cloudlaag is naadloos en transparant voor de gebruiker. |
| Lagere opslagkosten |Met StorSimple biedt u voldoende lokale opslag om te voldoen aan de huidige vraag naar de meest gebruikte hot data. Naarmate de opslagbehoeften toenemen, worden koudegegevens van StorSimple in kosteneffectieve cloudopslag verwerkt. De gegevens worden gededuplicate en gecomprimeerd voordat ze naar de cloud worden verzonden om de opslagvereisten en -kosten verder te verminderen. |
| Vereenvoudigd opslagbeheer |StorSimple biedt gecentraliseerd beheer in de cloud met StorSimple Device Manager om meerdere apparaten te beheren. |
| Verbeterde herstel en naleving van rampen |StorSimple faciliteert sneller disaster recovery door de metadata onmiddellijk te herstellen en de gegevens zo nodig te herstellen. Dit betekent dat normale bewerkingen kunnen doorgaan met minimale verstoring. |
| Datamobiliteit |Gegevens die zijn gelaagd naar de cloud, kunnen vanaf andere sites worden geopend voor herstel- en migratiedoeleinden. Houd er rekening mee dat u gegevens alleen herstellen naar de oorspronkelijke virtuele array. U gebruikt echter functies voor noodherstel om de volledige virtuele array te herstellen naar een andere virtuele array. |

## <a name="storsimple-workload-summary"></a>Overzicht van de StorSimple-werkbelasting

Hieronder vindt u een overzicht van ondersteunde StorSimple-workloads.

|Scenario     |Workload     |Ondersteund      |Beperkingen               | Toepasselijke versies|
|-------------|-------------|---------------|---------------------------|--------------------|
|Extern office/filiaal (ROBO)  |Het delen van bestanden     |Ja      |Zie [maximale limieten voor bestandsserver](storsimple-ova-limits.md).<br></br>Zie [systeemvereisten voor ondersteunde MKB-versies.](storsimple-ova-system-requirements.md)| Alle versies     |
|Cloudarchivering  |Archivering van bestanden     |Ja      |Zie [maximale limieten voor bestandsserver](storsimple-ova-limits.md).<br></br>Zie [systeemvereisten voor ondersteunde MKB-versies.](storsimple-ova-system-requirements.md)| Alle versies     |

De StorSimple Virtual Array is het meest geschikt voor zelden toegankelijke gegevens. Hoewel de virtuele array een lokale cache heeft om de prestaties te verbeteren, moeten gebruikers ervan uitgaan dat de bestanden van het apparaat op de laagste opslaglaag (de cloud) worden geleverd. Elke virtuele array kan schrijven en lezen naar Azure-opslag met ongeveer 100 Mbps. Die koppeling wordt gedeeld over alle aanvragen die in het apparaat komen en kan een knelpunt worden, zoals in het onderstaande diagram wordt weergegeven.

![Cloudarchivering](./media/storsimple-ova-overview/cloud-archiving.png)

Wanneer meerdere gelijktijdige gebruikers toegang hebben tot de virtuele array, delen ze allemaal de verbinding met Azure, wat leidt tot lagere prestaties. Er is geen gegarandeerde prestaties per gebruiker en het apparaat verwerkt individuele aanvragen wanneer ze aankomen.

StorSimple Virtual Array is niet geschikt voor workloads die een hoge beschikbaarheid vereisen. De virtuele array is een apparaat met één knooppunt dat downtime ervaart wanneer software-updates worden geïnstalleerd. Beheerders moeten plannen voor een onderhoudsvenster van 30 minuten 3-4 keer per jaar.

## <a name="workflows"></a>Werkstromen

De StorSimple Virtual Array is bijzonder geschikt voor de volgende workflows:

* [Cloudgebaseerd opslagbeheer](#cloud-based-storage-management)
* [Locatieonafhankelijke back-up](#location-independent-backup)
* [Gegevensbescherming en herstel na noodgevallen](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Cloudgebaseerd opslagbeheer
U de StorSimple Device Manager-service die wordt uitgevoerd in de Azure-portal gebruiken om gegevens te beheren die op meerdere apparaten en op meerdere locaties zijn opgeslagen. Dit is vooral handig in gedistribueerde branchescenario's. Houd er rekening mee dat u afzonderlijke exemplaren van de StorSimple Device Manager-service moet maken om virtuele arrays en fysieke StorSimple-apparaten te beheren. Houd er ook rekening mee dat de virtuele array nu de nieuwe Azure-portal gebruikt in plaats van de klassieke Azure-portal.

![cloudgebaseerd opslagbeheer](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Locatieonafhankelijke back-up
Met de virtuele array bieden cloudmomentopnamen een locatieonafhankelijke point-in-time-kopie van een volume of aandeel. Cloudsnapshots zijn standaard ingeschakeld en kunnen niet worden uitgeschakeld. Alle volumes en aandelen maken tegelijkertijd een back-up via één dagelijks back-upbeleid en u extra ad-hocback-ups maken wanneer dat nodig is.

### <a name="data-protection-and-disaster-recovery"></a>Gegevensbescherming en herstel na noodgevallen
De virtuele array ondersteunt de volgende scenario's voor gegevensbescherming en noodherstel:

* **Volume- of sharerestore** : gebruik de herstelals nieuwe werkstroom om een volume of aandeel te herstellen. Gebruik deze aanpak om het volledige volume of aandeel te herstellen.
* **Item-level recovery** – Aandelen bieden vereenvoudigde toegang tot recente back-ups. U eenvoudig een afzonderlijk bestand herstellen vanuit een speciale *.backup-map* die beschikbaar is in de cloud. Deze herstelmogelijkheid is door de gebruiker gestuurd en er is geen administratieve interventie vereist.
* **Herstel na noodgevallen** : gebruik de failovermogelijkheid om alle volumes of aandelen te herstellen naar een nieuwe virtuele array. U maakt de nieuwe virtuele array en registreert deze met de StorSimple Device Manager-service en mislukt vervolgens over de oorspronkelijke virtuele array. De nieuwe virtuele array neemt dan de ingerichte resources aan.

## <a name="storsimple-virtual-array-components"></a>StorSimple Virtual Array-componenten

De virtuele array bevat de volgende componenten:

* [Virtuele array](#virtual-array) – Een hybride cloudopslagapparaat op basis van een virtuele machine die is ingericht in uw gevirtualiseerde omgeving of hypervisor.
* [StorSimple Device Manager-service](#storsimple-device-manager-service) : een uitbreiding van de Azure-portal waarmee u een of meer StorSimple-apparaten beheren vanuit één webinterface die u vanaf verschillende geografische locaties openen. U de StorSimple Device Manager-service gebruiken om services te maken en te beheren, apparaten en waarschuwingen te bekijken en te beheren en volumes, shares en bestaande momentopnamen te beheren.
* [Lokale webgebruikersinterface](#local-web-user-interface) : een webgebaseerde gebruikersinterface die wordt gebruikt om het apparaat zo te configureren dat het verbinding kan maken met het lokale netwerk en het apparaat vervolgens kan registreren met de StorSimple Device Manager-service. 
* [Command-line interface](#command-line-interface) – Een Windows PowerShell-interface die u gebruiken om een ondersteuningssessie op de virtuele array te starten.
  In de volgende secties worden elk van deze componenten gedetailleerder beschreven en wordt uitgelegd hoe de oplossing gegevens rangschikt, opslag toewijst en opslagbeheer en gegevensbescherming vergemakkelijkt.

### <a name="virtual-array"></a>Virtuele array

De virtuele array is een opslagoplossing met één knooppunt die primaire opslag biedt, de communicatie met cloudopslag beheert en helpt de veiligheid en vertrouwelijkheid te garanderen van alle gegevens die op het apparaat zijn opgeslagen.

De virtuele array is beschikbaar in één model dat beschikbaar is om te downloaden. De virtuele array heeft een maximale capaciteit van 6,4 TB op het apparaat (met een onderliggende opslagvereiste van 8 TB) en 64 TB inclusief cloudopslag.

De virtuele array heeft de volgende functies:

* Het is kosteneffectief. Het maakt gebruik van uw bestaande virtualisatie-infrastructuur en kan worden ingezet op uw bestaande Hyper-V- of VMware-hypervisor.
* Het bevindt zich in het datacenter en kan worden geconfigureerd als een iSCSI-server of een bestandsserver.
* Het is geïntegreerd met de cloud.
* Back-ups worden opgeslagen in de cloud, wat disaster recovery kan vergemakkelijken en het herstel op artikelniveau (ILR) kan vereenvoudigen.
* U updates toepassen op de virtuele array, net zoals u ze zou toepassen op een fysiek apparaat.

> [!NOTE]
> Een virtuele array kan niet worden uitgebreid. Daarom is het belangrijk om voldoende opslagruimte in te richten wanneer u de virtuele array maakt.

### <a name="storsimple-device-manager-service"></a>StorSimple-apparaatbeheerfunctie

Microsoft Azure StorSimple biedt een webgebaseerde gebruikersinterface, de StorSimple Device Manager-service, waarmee u StorSimple-opslag centraal beheren. U de StorSimple Device Manager-service gebruiken om de volgende taken uit te voeren:

* Beheer meerdere StorSimple Virtual Arrays vanuit één service.
* Beveiligingsinstellingen configureren en beheren voor StorSimple Virtual Arrays. (Versleuteling in de cloud is afhankelijk van Microsoft Azure API's.)
* Configureer de referenties en eigenschappen van het opslagaccount.
* Volumes of aandelen configureren en beheren.
* Back-ups maken en gegevens over volumes of aandelen herstellen.
* Controleer de prestaties.
* Controleer systeeminstellingen en identificeer mogelijke problemen.

U gebruikt de StorSimple Device Manager-service om dagelijks beheer van uw virtuele array uit te voeren.

Ga voor meer informatie naar [De StorSimple Device Manager-service gebruiken om uw StorSimple-apparaat te beheren.](storsimple-virtual-array-manager-service-administration.md)

### <a name="local-web-user-interface"></a>Lokale webgebruikersinterface

De virtuele array bevat een webgebaseerde gebruikersinterface die wordt gebruikt voor eenmalige configuratie en registratie van het apparaat met de StorSimple Device Manager-service. U het gebruiken om de virtuele array af te sluiten en opnieuw op te starten, diagnostische tests uit te voeren, software bij te werken, het wachtwoord van de apparaatbeheerder te wijzigen, systeemlogboeken weer te geven en contact op te nemen met Microsoft Support om een serviceaanvraag in te dienen.

Ga voor informatie over het gebruik van de webgebaseerde gebruikersinterface naar [De webgebaseerde gebruikersinterface gebruiken om uw StorSimple Virtual Array te beheren.](storsimple-ova-web-ui-admin.md)

### <a name="command-line-interface"></a>Command-line-interface

Met de meegeleverde Windows PowerShell-interface u een ondersteuningssessie starten met Microsoft Support, zodat u problemen oplossen en oplossen die u mogelijk op uw virtuele array tegenkomt.

## <a name="storage-management-technologies"></a>Opslagbeheertechnologieën

Naast de virtuele array en andere componenten maakt de StorSimple-oplossing gebruik van de volgende softwaretechnologieën om snelle toegang tot belangrijke gegevens te bieden, het opslagverbruik te verminderen en gegevens te beschermen die zijn opgeslagen op uw virtuele array:

* [Automatische opslaglaag](#automatic-storage-tiering) 
* [Lokaal vastgemaakte aandelen en volumes](#locally-pinned-shares-and-volumes)
* Ontdubbeling en compressie voor gegevens die zijn gelaagd of een back-up maken naar de cloud 
* [Geplande en on-demand back-ups](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Automatische opslaglaag
De virtuele array maakt gebruik van een nieuw tieringmechanisme om opgeslagen gegevens in de virtuele array en de cloud te beheren. Er zijn slechts twee lagen: de lokale virtuele array en Azure-cloudopslag. De StorSimple Virtual Array rangschikt automatisch gegevens in de lagen op basis van een heatmap, die het huidige gebruik, de leeftijd en relaties met andere gegevens bijhoudt. Gegevens die het meest actief zijn (het heetst) worden lokaal opgeslagen, terwijl minder actieve en inactieve gegevens automatisch naar de cloud worden gemigreerd. (Alle back-ups worden opgeslagen in de cloud.) StorSimple past gegevens- en opslagtoewijzingen aan en herschikt deze naarmate de gebruikspatronen veranderen. Sommige informatie kan bijvoorbeeld na verloop van tijd minder actief worden. Als het steeds minder actief wordt, wordt het gelaagd naar de cloud. Als diezelfde gegevens weer actief worden, wordt deze gelaagd in de opslagarray.

Gegevens voor een bepaald gedifferentieerd aandeel of volume worden gegarandeerd in de eigen lokale niveauruimte (ongeveer 10% van de totale ingerichte ruimte voor dat aandeel of dat volume). Hoewel dit de beschikbare opslag op de virtuele array voor dat aandeel of dat volume vermindert, zorgt het ervoor dat tiering voor één aandeel of volume niet wordt beïnvloed door de gelaagde behoeften van andere aandelen of volumes. Een zeer drukke workload op één aandeel of volume kan dus niet alle andere workloads naar de cloud dwingen.

Gelaagde volumes die voor iSCSI zijn gemaakt, hebben een maximale lokale reservering van 200 GB, ongeacht de grootte van het volume.

![automatische opslaglaag](./media/storsimple-ova-overview/automatic-storage-tiering.png)

> [!NOTE]
> U een volume opgeven als lokaal vastgemaakt, in welk geval de gegevens op de virtuele array blijven en nooit worden gelaagd naar de cloud. Ga voor meer informatie naar [Lokaal vastgemaakte aandelen en volumes](#locally-pinned-shares-and-volumes).


### <a name="locally-pinned-shares-and-volumes"></a>Lokaal vastgemaakte aandelen en volumes

U geschikte aandelen en volumes maken als lokaal vastgemaakt. Deze mogelijkheid zorgt ervoor dat gegevens die vereist zijn door kritieke toepassingen in de virtuele array blijven en nooit worden gelaagd naar de cloud. Lokaal vastgemaakte aandelen en volumes hebben de volgende functies:

* Ze zijn niet onderhevig aan problemen met latencies in de cloud of connectiviteit.
* Ze profiteren nog steeds van storSimple cloud back-up en disaster recovery functies.

U een lokaal vastgemaakt aandeel of volume herstellen als gelaagd of een gelaagd aandeel of volume als lokaal vastgemaakt. 

Ga voor meer informatie over lokaal vastgemaakte volumes naar [De StorSimple Device Manager-service gebruiken om volumes te beheren.](storsimple-virtual-array-manage-volumes.md)

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Ontdubbeling en compressie voor gegevens die zijn gelaagd of een back-up maken naar de cloud

StorSimple gebruikt ontdubbeling en gegevenscompressie om de opslagvereisten in de cloud verder te verminderen. Ontdubbeling vermindert de totale hoeveelheid gegevens die is opgeslagen door redundantie in de opgeslagen gegevensset te elimineren. Als de informatie verandert, negeert StorSimple de ongewijzigde gegevens en legt alleen de wijzigingen vast. Bovendien vermindert StorSimple de hoeveelheid opgeslagen gegevens door dubbele informatie te identificeren en te verwijderen.

> [!NOTE]
> Gegevens die zijn opgeslagen op de virtuele array worden niet gededuplicateof gecomprimeerd. Alle ontdubbeling en compressie vindt plaats vlak voordat de gegevens naar de cloud worden verzonden.

### <a name="scheduled-and-on-demand-backups"></a>Geplande en on-demand back-ups

Met de functies voor gegevensbescherming van StorSimple u on-demand back-ups maken. Bovendien zorgt een standaard back-upschema ervoor dat er dagelijks een back-up van gegevens wordt gemaakt. Back-ups worden genomen in de vorm van incrementele momentopnamen, die worden opgeslagen in de cloud. Momentopnamen, die alleen de wijzigingen opnemen sinds de laatste back-up, kunnen snel worden gemaakt en hersteld. Deze momentopnamen kunnen van cruciaal belang zijn in scenario's voor noodherstel, omdat ze secundaire opslagsystemen (zoals tapeback-up) vervangen en u in staat stellen gegevens naar uw datacenter te herstellen of indien nodig naar alternatieve locaties.

## <a name="managing-personal-information"></a>Persoonlijke informatie beheren

De StorSimple Device Manager voor virtuele series verzamelt persoonlijke gegevens in twee belangrijke instanties:
 - Gebruikersinstellingen waarschuwen wanneer e-mailadressen van gebruikers zijn geconfigureerd. Deze informatie kan door de beheerder worden gewist. 
 - Gebruikers die toegang hebben tot de gegevens die zich op de aandelen bevinden. Een lijst met gebruikers die toegang hebben tot de share-gegevens wordt weergegeven en kan worden geëxporteerd. Deze lijst wordt ook verwijderd wanneer de aandelen worden verwijderd.

Bekijk het Privacybeleid [van Microsoft in het Trust Center](https://www.microsoft.com/trustcenter)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [voorbereiden van de virtuele arrayportal.](storsimple-virtual-array-deploy1-portal-prep.md)
