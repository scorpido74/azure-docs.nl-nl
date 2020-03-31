---
title: Veelgestelde vragen over lokaal vastgemaakte volumes van StorSimple| Microsoft Documenten
description: Biedt antwoorden op veelgestelde vragen over lokaal vastgemaakte volumes van StorSimple.
services: storsimple
documentationcenter: NA
author: manuaery
manager: syadav
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2017
ms.author: manuaery
ms.openlocfilehash: aa69d8b07d31b5cf0386e34c113475cbf4191891
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60319544"
---
# <a name="storsimple-locally-pinned-volumes-frequently-asked-questions-faq"></a>StorSimple lokaal vastgepinde volumes: veelgestelde vragen (FAQ)
## <a name="overview"></a>Overzicht
De volgende vragen en antwoorden die u zou kunnen hebben wanneer u een StorSimple lokaal vastgemaakt volume maakt, een gelaagd volume omzet naar een lokaal vastgemaakt volume (en vice versa), of een back-up maken en een lokaal vastgemaakt volume herstellen.

Vragen en antwoorden zijn ingedeeld in de volgende categorieën

* Een lokaal vastgemaakt volume maken
* Een back-up maken van een lokaal vastgemaakte
* Een gelaagd volume converteren naar een lokaal vastgemaakt volume
* Een lokaal vastgemaakt volume herstellen
* Niet meer dan een lokaal vastgemaakt volume

## <a name="questions-about-creating-a-locally-pinned-volume"></a>Vragen over het maken van een lokaal vastgemaakt volume
**V:** Wat is de maximale grootte van een lokaal vastgemaakt volume dat ik kan maken op de apparaten uit de 8000-serie?

**A** Op apparaten met StorSimple 8000-serie Update 3.0 u lokaal vastgemaakte volumes tot 8,5 TB of gelaagde volumes tot 200 TB op het 8100-apparaat inrichten. Op een groter 8600-apparaat kunt u lokaal vastgemaakte volumes inrichten tot 22,5 TB en gelaagde volumes tot maximaal 500 TB.

**V:** Ik heb onlangs een upgrade van mijn 8100-apparaat naar Update 3.0 en als ik probeer om een lokaal vastgemaakt volume te maken, de maximale beschikbare grootte is slechts 6 TB en niet 8,5 TB. Waarom kan ik geen volume van 8,5 TB maken?

**A** Als uw apparaat update 3.0 uitvoert, u lokaal vastgemaakte volumes tot 8,5 TB of gelaagde volumes tot 200 TB op het 8100-apparaat inrichten. Als uw apparaat al gelaagde volumes heeft, is de beschikbare ruimte voor het maken van een lokaal vastgemaakt volume proportioneel lager dan deze maximumlimiet. Als er bijvoorbeeld al ongeveer 106 TB gelaagde volumes zijn ingericht op uw 8100-apparaat (dat is de helft van de gelaagde capaciteit), wordt de maximale grootte van een lokaal volume dat u maken op het 8100-apparaat navenant teruggebracht tot 4 TB (ongeveer de helft van de maximale lokaal vastgemaakte volumecapaciteit).

Omdat sommige lokale ruimte op het apparaat wordt gebruikt om de werkset met gelaagde volumes te hosten, wordt de beschikbare ruimte voor het maken van een lokaal vastgemaakt volume verminderd als het apparaat gelaagde volumes heeft. Omgekeerd vermindert het maken van een lokaal vastgemaakt volume proportioneel de beschikbare ruimte voor gelaagde volumes. In de volgende tabellen worden de beschikbare gelaagde capaciteit op de 8100- en 8600-apparaten samengevat wanneer lokaal vastgemaakte volumes worden gemaakt.

#### <a name="update-30"></a>Update 3.0 

| Lokaal vastgemaakte volumes ingerichte capaciteit | Beschikbare capaciteit voor gelaagde volumes - 8100 | Beschikbare capaciteit voor gelaagde volumes - 8600 |
| --- | --- | --- |
| 0 |200 TB |500 TB |
| 1 TB |176,5 TB |477,8 TB |
| 4 TB |105,9 TB |411,1 TB |
| 8,5 TB |0 TB |311,1 TB |
| 10 TB |N.v.t. |277,8 TB |
| 15 TB |N.v.t. |166,7 TB |
| 22,5 TB |N.v.t. |0 TB |

**V:** Waarom is lokaal vastgemaakte volumecreatie een langdurige bewerking?

**A.** Lokaal vastgemaakte volumes zijn dik ingericht. Als u ruimte wilt maken op de lokale lagen van het apparaat, kunnen sommige gegevens van bestaande gelaagde volumes tijdens het inrichtingsproces naar de cloud worden gepusht. En aangezien dit afhankelijk is van de grootte van het volume dat wordt ingericht, de bestaande gegevens op uw apparaat en de beschikbare bandbreedte naar de cloud, kan de tijd die nodig is om een lokaal volume te maken enkele uren zijn.

**V:** Hoe lang duurt het om een lokaal vastgemaakt volume te maken?

**A.** Omdat lokaal vastgemaakte volumes dik zijn ingericht, kunnen sommige bestaande gegevens van gelaagde volumes tijdens het inrichtingsproces naar de cloud worden geduwd. Daarom is de tijd die nodig is om een lokaal vastgemaakt volume te maken afhankelijk van meerdere factoren, waaronder de grootte van het volume, de gegevens op uw apparaat en de beschikbare bandbreedte. Op een vers geïnstalleerd apparaat dat geen volumes heeft, is de tijd om een lokaal vastgemaakt volume te maken ongeveer 10 minuten per terabyte aan gegevens. Het maken van lokale volumes kan echter enkele uren duren op basis van de hierboven uiteengezette factoren op een apparaat dat in gebruik is.

**V:** Ik wil een lokaal vastgemaakt volume maken. Zijn er best practices waar ik me bewust van moet zijn?

**A.** Lokaal vastgemaakte volumes zijn geschikt voor workloads die te allen tijde lokale garanties van gegevens vereisen en gevoelig zijn voor cloudlatencies. Houd rekening met het gebruik van lokale volumes voor een van uw workloads, houd rekening met het volgende:

* Lokaal vastgemaakte volumes zijn dik ingericht en het maken van lokale volumes heeft invloed op de beschikbare ruimte voor gelaagde volumes. Daarom raden we u aan om te beginnen met kleinere volumes en op te schalen naarmate uw opslagbehoefte toeneemt.
* Het inrichten van lokale volumes is een langdurige bewerking waarbij bestaande gegevens van gelaagde volumes naar de cloud worden geduwd. Als gevolg hiervan u minder prestaties ervaren op deze volumes.
* Het inrichten van lokale volumes is een tijdrovende operatie. De werkelijke tijd is afhankelijk van meerdere factoren: de grootte van het volume dat wordt ingericht, gegevens op uw apparaat en beschikbare bandbreedte. Als u geen back-up van uw bestaande volumes naar de cloud hebt gemaakt, verloopt het maken van volumes trager. We raden u aan cloudsnapshots van uw bestaande volumes te maken voordat u een lokaal volume indient.
* U bestaande gelaagde volumes converteren naar lokaal vastgemaakte volumes, en deze conversie omvat het inrichten van ruimte op het apparaat voor het resulterende lokaal vastgemaakte volume (naast het neerhalen van gelaagde gegevens, indien van toepassing, uit de cloud). Nogmaals, dit is een langdurige operatie die afhankelijk is van factoren die we hierboven hebben besproken. We raden u aan een back-up van uw bestaande volumes te maken voordat u deze conversie uitvoert, omdat het proces nog trager verloopt als er geen back-up van bestaande volumes wordt ondersteund. Uw apparaat kan ook minder prestaties ervaren tijdens dit proces.

Meer informatie over het [maken van een lokaal vastgemaakt volume](storsimple-8000-manage-volumes-u2.md#add-a-volume)

**V:** Kan ik meerdere lokaal vastgemaakte volumes tegelijk maken?

**A.** Ja, maar alle lokaal vastgemaakte volumecreatie- en uitbreidingstaken worden achtereenvolgens verwerkt.

Lokaal vastgemaakte volumes zijn dik ingericht en dit vereist het creëren van lokale ruimte op het apparaat (wat kan resulteren in bestaande gegevens van gelaagde volumes die tijdens het inrichtingsproces naar de cloud worden geduwd). Als er een inrichtingstaak wordt uitgevoerd, worden daarom andere lokale taken voor het maken van volume in de wachtrij geplaatst totdat die taak is voltooid.

Als een bestaand lokaal volume wordt uitgebreid of een gelaagd volume wordt geconverteerd naar een lokaal vastgemaakt volume, wordt de creatie van een nieuw lokaal vastgemaakt volume in de wachtrij geplaatst totdat de vorige taak is voltooid. Het uitbreiden van de grootte van een lokaal vastgemaakt volume omvat de uitbreiding van de bestaande lokale ruimte voor dat volume. Conversie van een gelaagd naar lokaal vastgemaakt volume omvat ook het maken van lokale ruimte voor het resulterende lokaal vastgemaakte volume. In beide bewerkingen is het creëren of uitbreiden van de lokale ruimte een langlopende taak.

U deze taken bekijken in het blade **van De** StorSimple Device Manager. De taak die actief wordt verwerkt, wordt voortdurend bijgewerkt om de voortgang van ruimtevoorzieningen weer te geven. De resterende lokaal vastgemaakte volumetaken worden gemarkeerd als uitgevoerd, maar de voortgang ervan is vastgelopen en ze worden opgehaald in de volgorde waarin ze in de wachtrij stonden.

**V:** Ik heb een lokaal vastgemaakt volume verwijderd. Waarom zie ik de teruggewonnen ruimte niet weerspiegeld in de beschikbare ruimte wanneer ik een nieuw volume probeer te maken?

**A.** Als u een lokaal vastgemaakt volume verwijdert, wordt de beschikbare ruimte voor nieuwe volumes mogelijk niet onmiddellijk bijgewerkt. De StorSimple Device Manager Service werkt de beschikbare lokale ruimte ongeveer elk uur bij. We raden u aan een uur te wachten voordat u het nieuwe volume probeert te maken.

**V:** Worden lokaal vastgemaakte volumes ondersteund op het cloudtoestel?

**A.** Lokaal vastgemaakte volumes worden niet ondersteund op het cloudtoestel (8010- en 8020-apparaten voorheen aangeduid als het virtuele StorSimple-apparaat).

**V:** Kan ik de Azure PowerShell-cmdlets gebruiken om lokaal vastgemaakte volumes te maken en te beheren?

**A.** Nee, u geen lokaal vastgemaakte volumes maken via Azure PowerShell-cmdlets (elk volume dat u maakt via Azure PowerShell is gelaagd). We raden u ook aan de Azure PowerShell-cmdlets niet te gebruiken om eigenschappen van een lokaal vastgemaakt volume aan te passen, omdat dit het ongewenste effect heeft van het wijzigen van het volumetype naar gelaagd.

## <a name="questions-about-backing-up-a-locally-pinned-volume"></a>Vragen over het maken van een back-up van een lokaal vastgemaakt volume
**V:** Worden lokale momentopnamen van lokaal vastgemaakte volumes ondersteund?

**A.** Ja, u lokale momentopnamen maken van uw lokaal vastgemaakte volumes. We raden u echter ten zeerste aan om regelmatig een back-up te maken van uw lokaal vastgemaakte volumes met cloudsnapshots om ervoor te zorgen dat uw gegevens worden beschermd in het geval van een ramp.

Houd er rekening mee dat lokale momentopnamen van lokaal vastgemaakte volumes ook naar de cloud kunnen worden uitgevoerd en dat ze niet gegarandeerd in de lokale laag van het apparaat blijven.

**V:** Zijn er richtlijnen voor het beheren van lokale momentopnamen voor lokaal vastgemaakte volumes?

**A.** Frequente lokale momentopnamen naast een hoge mate van gegevensverloop in het lokaal vastgemaakte volume kunnen ertoe leiden dat lokale ruimte op het apparaat snel wordt verbruikt en dat gegevens van gelaagde volumes naar de cloud worden geduwd. We raden u daarom aan het aantal lokale momentopnamen te minimaliseren.

**V:** Ik kreeg een waarschuwing waarin staat dat mijn lokale momentopnamen van lokaal vastgemaakte volumes ongeldig kunnen worden gemaakt. Wanneer kan dit gebeuren?

**A.** Frequente lokale momentopnamen naast een hoge mate van gegevensverloop in het lokaal vastgemaakte volume kunnen ervoor zorgen dat lokale ruimte op het apparaat snel wordt verbruikt. Als de lokale lagen van het apparaat intensief worden gebruikt, kan een uitgebreide cloudstoring ertoe leiden dat het apparaat vol raakt en dat binnenkomende schrijfbewerkingen naar het volume kunnen leiden tot het ongeldig maken van de momentopnamen (omdat er geen ruimte is om de momentopnamen bij te werken om te verwijzen naar de oudere blokken van gegevens die zijn overschreven). In een dergelijke situatie worden de schrijfbewerkingen naar het volume weergegeven, maar kunnen de lokale momentopnamen ongeldig zijn. Er is geen invloed op uw bestaande cloudsnapshots.

De waarschuwing is om u te informeren dat een dergelijke situatie zich kan voordoen en ervoor te zorgen dat u hetzelfde tijdig aanpakt door uw lokale momentopnamenschema's te bekijken om minder frequente lokale momentopnamen te maken of oudere lokale momentopnamen te verwijderen die niet langer nodig zijn.

Als de lokale momentopnamen ongeldig zijn, ontvangt u een informatiewaarschuwing waarin u wordt gewaarschuwd dat de lokale momentopnamen voor het specifieke back-upbeleid zijn ongeldig gemaakt naast de lijst met tijdstempels van de lokale momentopnamen die ongeldig zijn gemaakt. Deze momentopnamen worden automatisch verwijderd en u ze niet meer bekijken in het blade **Back-upcatalogi** in de Azure-portal.

## <a name="questions-about-converting-a-tiered-volume-to-a-locally-pinned-volume"></a>Vragen over het converteren van een gelaagd volume naar een lokaal vastgemaakt volume
**V:** Ik observeer wat traagheid op het apparaat terwijl ik een gelaagd volume omzet naar een lokaal vastgemaakt volume. Waarom gebeurt dit?

**A.** Het conversieproces omvat twee stappen:

1. Het inrichten van ruimte op het apparaat voor het lokaal vastgemaakte volume dat binnenkort moet worden geconverteerd.
2. Het downloaden van gelaagde gegevens uit de cloud om lokale garanties te garanderen.

Beide stappen zijn langlopende bewerkingen die afhankelijk zijn van de grootte van het volume dat wordt geconverteerd, gegevens op het apparaat en beschikbare bandbreedte. Aangezien sommige gegevens van bestaande gelaagde volumes naar de cloud kunnen worden gelekt als onderdeel van het inrichtingsproces, kan uw apparaat gedurende deze periode minder prestaties ervaren. Bovendien kan het conversieproces trager verlopen als:

* Bestaande volumes zijn niet geback-upt naar de cloud; daarom raden we u aan een back-up van uw volumes te maken voordat u een conversie start.
* Er is beleid voor bandbreedtebeperking toegepast, waardoor de beschikbare bandbreedte voor de cloud kan worden beperkt. we raden u daarom aan een speciale verbinding van 40 Mbps of meer met de cloud te hebben.
* Het conversieproces kan enkele uren duren vanwege de meerdere factoren die hierboven zijn uitgelegd; daarom raden we u aan deze bewerking uit te voeren tijdens niet-pieken of in een weekend om de impact op eindgebruikers te voorkomen.

Meer informatie over het [converteren van een gelaagd volume naar een lokaal vastgemaakt volume](storsimple-8000-manage-volumes-u2.md#change-the-volume-type)

**V:** Kan ik de volumeconversie-bewerking annuleren?

**A.** Nee, u de conversiebewerking niet annuleren nadat deze is gestart. Zoals besproken in de vorige vraag, moet u zich bewust zijn van de mogelijke prestatieproblemen die u tijdens het proces tegenkomen en de aanbevolen procedures volgen die hierboven worden vermeld wanneer u uw conversie plant.

**V:** Wat gebeurt er met mijn volume als de conversiebewerking mislukt?

**A.** Volumeconversie kan mislukken als gevolg van problemen met de cloudconnectiviteit. Het apparaat kan uiteindelijk het conversieproces stoppen na een reeks mislukte pogingen om gelaagde gegevens uit de cloud naar beneden te halen. In een dergelijk scenario blijft het volumetype het bronvolumetype vóór de conversie en:

* Er wordt een kritieke waarschuwing gegenereerd om u op de hoogte te stellen van de volumeconversiefout. Meer informatie over [waarschuwingen met betrekking tot lokaal vastgemaakte volumes](storsimple-8000-manage-alerts.md#locally-pinned-volume-alerts)
* Als u een gelaagd volume converteert naar een lokaal vastgemaakt volume, blijft het volume eigenschappen van een gelaagd volume vertonen, omdat gegevens zich mogelijk nog steeds in de cloud bevinden. We raden u aan de verbindingsproblemen op te lossen en vervolgens de conversiebewerking opnieuw te proberen.
* Op dezelfde manier, wanneer de conversie van een lokaal vastgemaakt naar een gelaagd volume mislukt, hoewel het volume wordt gemarkeerd als een lokaal vastgemaakt volume, zal het functioneren als een gelaagd volume (omdat gegevens naar de cloud kunnen zijn gelekt). Het blijft echter ruimte innemen op de lokale lagen van het apparaat. Deze ruimte is niet beschikbaar voor andere lokaal vastgemaakte volumes. We raden u aan deze bewerking opnieuw te proberen om ervoor te zorgen dat de volumeconversie is voltooid en de lokale ruimte op het apparaat kan worden teruggevorderd.

## <a name="questions-about-restoring-a-locally-pinned-volume"></a>Vragen over het herstellen van een lokaal vastgemaakt volume
**V:** Worden lokaal vastgemaakte volumes direct hersteld?

**A.** Ja, lokaal vastgemaakte volumes worden direct hersteld. Zodra de metagegevens voor het volume uit de cloud worden gehaald als onderdeel van de herstelbewerking, wordt het volume online gebracht en kan het door de host worden geopend. Lokale garanties voor de volumegegevens zullen echter pas aanwezig zijn als alle gegevens uit de cloud zijn gedownload en u minder prestaties op deze volumes ervaren gedurende de duur van het herstel.

**V:** Hoe lang duurt het om een lokaal vastgemaakt volume te herstellen?

**A.** Lokaal vastgemaakte volumes worden direct hersteld en online gebracht zodra de gegevensmetagegevens van het volume uit de cloud worden opgehaald, terwijl de volumegegevens op de achtergrond blijven worden gedownload. Dit laatste deel van de herstelbewerking - het terugkrijgen van de lokale garanties voor de volumegegevens - is een langdurige bewerking en kan enkele uren duren voordat alle gegevens weer lokaal zijn gemaakt. De tijd die nodig is om hetzelfde te voltooien, is afhankelijk van meerdere factoren, zoals de grootte van het volume dat wordt hersteld en de beschikbare bandbreedte. Als het oorspronkelijke volume dat wordt hersteld is verwijderd, wordt extra tijd genomen om de lokale ruimte op het apparaat te maken als onderdeel van de herstelbewerking.

**V:** Ik moet mijn bestaande lokaal vastgemaakte volume herstellen naar een oudere momentopname (genomen toen het volume is gelaagd). Wordt het volume in dit geval als gelaagd hersteld hersteld?

**A.** Nee, het volume wordt hersteld als een lokaal vastgemaakt volume. Hoewel de momentopname dateert van het tijdstip waarop het volume is gelaagd, terwijl bestaande volumes worden hersteld, gebruikt StorSimple altijd het type volume op de schijf zoals deze momenteel bestaat.

**V:** Ik heb onlangs mijn lokaal vastgemaakte volume uitgebreid, maar ik moet de gegevens nu herstellen naar een tijd waarin het volume kleiner was. Wordt het formaat van het huidige volume opnieuw hersteld en moet ik de grootte van het volume uitbreiden zodra het herstel is voltooid?

**A.** Ja, het herstel wijzigt het formaat van het volume en u moet de grootte van het volume verlengen nadat het herstel is voltooid.

**V:** Kan ik het type volume wijzigen tijdens het herstellen?

**A.** Nee, u het volumetype niet wijzigen tijdens het herstellen.

* Verwijderde volumes worden hersteld als het type dat in de momentopname is opgeslagen.
* Bestaande volumes worden hersteld op basis van hun huidige type, ongeacht het type dat in de momentopname is opgeslagen (zie de vorige twee vragen).

**V:** Ik moet mijn lokaal vastgemaakte volume herstellen, maar ik heb een onjuist momentopnamepunt gekozen. Kan ik de huidige herstelbewerking annuleren?

**A.** Ja, u een doorlopende herstelbewerking annuleren. De status van het volume wordt aan het begin van het herstel teruggerold naar de status. Echter, alle schrijft die zijn gemaakt om het volume, terwijl het herstel aan de gang is verloren gaan.

**V:** Ik ben begonnen met een herstelbewerking op een van mijn lokaal vastgemaakte volumes en nu zie ik een momentopname in mijn achterstandscatalogus die ik me niet herinner op het maken. Waar wordt dit voor gebruikt?

**A.** Dit is de tijdelijke momentopname die wordt gemaakt voorafgaand aan de herstelbewerking en wordt gebruikt voor terugdraaien in het geval het herstel wordt geannuleerd of mislukt. Verwijder deze momentopname niet; het wordt automatisch verwijderd wanneer het herstel is voltooid. Dit gedrag kan optreden als uw hersteltaak alleen lokaal vastgemaakte volumes of een mix van lokaal vastgemaakte en gelaagde volumes heeft. Als de hersteltaak alleen gelaagde volumes bevat, treedt dit gedrag niet op.

**V:** Kan ik een lokaal vastgemaakt volume klonen?

**A.** Ja, dat is mogelijk. Het lokaal vastgemaakte volume wordt echter standaard gekloond als een gelaagd volume. Meer informatie over het [klonen van een lokaal vastgemaakt volume](storsimple-8000-clone-volume-u2.md)

## <a name="questions-about-failing-over-a-locally-pinned-volume"></a>Vragen over het niet over een lokaal vastgemaakt volume
**V:** Ik moet falen over mijn apparaat naar een ander fysiek apparaat. Worden mijn lokaal vastgemaakte volumes niet meer als lokaal vastgemaakt of gelaagd?

**A.** De lokaal vastgemaakte volumes zijn mislukt als Lokaal vastgemaakt als het doelapparaat wordt uitgevoerd StorSimple 8000 serie update 3 of hoger.

Meer informatie over [failover en DR van lokaal vastgemaakte volumes in verschillende versies](storsimple-8000-device-failover-disaster-recovery.md#device-failover-across-software-versions)

**V:** Worden lokaal vastgemaakte volumes direct hersteld tijdens disaster recovery (DR)?

**A.** Ja, lokaal vastgemaakte volumes worden direct hersteld tijdens failover. Zodra de metagegevens voor het volume uit de cloud worden gehaald als onderdeel van de failover-bewerking, wordt het volume online gebracht op het doelapparaat en kan het worden geopend door de host. Ondertussen blijven de volumegegevens op de achtergrond worden gedownload en u minder prestaties ondervinden op deze volumes gedurende de duur van de failover.

**V:** Ik zie de failovertaak voltooid, hoe kan ik de voortgang bijhouden van lokaal vastgemaakt volume dat wordt hersteld op het doelapparaat?

**A.** Tijdens een failoverbewerking wordt de failovertaak gemarkeerd als voltooid zodra alle volumes in de failoverset direct zijn hersteld en online zijn gebracht op het doelapparaat. Dit omvat alle lokaal vastgemaakte volumes die mogelijk zijn mislukt. Lokale garanties voor de gegevens zijn echter alleen beschikbaar wanneer alle gegevens voor het volume zijn gedownload. U deze voortgang bijhouden voor elk lokaal vastgemaakt volume dat is mislukt door de bijbehorende hersteltaken te controleren die zijn gemaakt als onderdeel van de failover. Deze afzonderlijke hersteltaken worden alleen gemaakt voor lokaal vastgemaakte volumes.

**V:** Kan ik het type volume wijzigen tijdens failover?

**A.** Nee, u het volumetype niet wijzigen tijdens een failover. Als u niet naar een ander fysiek apparaat gaat waarop de StorSimple 8000-serie update 3 wordt uitgevoerd, zijn de volumes mislukt op basis van het volumetype dat in de momentopname is opgeslagen.

**V:** Kan ik falen boven een volumecontainer met lokaal vastgemaakte volumes aan het cloudtoestel?

**A.** Ja, dat is mogelijk. De lokaal vastgemaakte volumes worden niet meer dan trapsgewijs weergegeven. Meer informatie over [failover en DR van lokaal vastgemaakte volumes in verschillende versies](storsimple-8000-device-failover-disaster-recovery.md#common-considerations-for-device-failover)

