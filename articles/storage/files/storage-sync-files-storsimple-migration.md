---
title: Migreren van StorSimple naar Azure File Sync
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: fauhse
ms.subservice: files
description: StorSimple is een einde product en Azure File Sync is de oplossing voor de migratie naar. Meer informatie over het migratie concept en het bereiken van AzureFiles@microsoft.com voor aangepaste migratie-Help.
ms.openlocfilehash: 1cc88080522a62085d9a515223512ef25c20a9e4
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895082"
---
# <a name="storsimple-migration-to-azure-file-sync"></a>Migratie naar Azure File Sync StorSimple

StorSimple is een stopgezet micro soft-product. Uitgebreide ondersteuning voor dit product en de Cloud service gaat door tot het einde van 2022.
Het is belang rijk om te beginnen met het plannen van een migratie van StorSimple direct.

De standaard en strategische Azure service StorSimple-apparaten voor de lange termijn kunnen worden gemigreerd naar, is Azure File Sync. Het is een algemeen beschik bare Azure-service met een superset van functies over StorSimple.

## <a name="full-cloud-side-migration-with-limited-downtime"></a>Volledige migratie aan de Cloud zijde met beperkte downtime
In dit artikel wordt het concept beschreven van de manier waarop een migratie wordt begonnen.
Het is belang rijk te weten dat klanten hun migratie van StorSimple uit te voeren en op Azure File Sync geen eigen verdere procedure hoeven te doen.

> [!IMPORTANT]
> Micro soft streeft ernaar klanten te helpen bij hun migratie. E-mail AzureFiles@microsoft. com voor een aangepast migratie plan en voor hulp tijdens de migratie.

## <a name="migration-approach"></a>Migratie benadering
De migratie naar Azure File Sync begint aan de Cloud zijde met minimale gevolgen voor on-premises en beperkte uitval tijd.
Het onderstaande concept is gericht op StorSimple 8000-serie toestellen.
Als u behoefte hebt aan een migratie van de StorSimple 7000-serie, omvat de eerste stap een gratis upgrade naar een overeenkomend apparaat van 8000 Series uitgeleend van micro soft.
Neem contact op met AzureFiles@microsoft.com en wij helpen u bij het organiseren van een passend aantal uitleen apparaten.

### <a name="general-approach"></a>Algemene benadering
![Houdt](media/storage-sync-files-storsimple-migration/storsimple-docs-overview-concept.png "Het illustreren van migratie aan de Cloud zijde via een tijdelijk virtueel apparaat en Windows Server naar een nieuwe on-premises Windows-Server die het on-premises StorSimple-apparaat vervangt")

1. Maak een volume kloon van uw on-premises StorSimple-apparaat en koppel dit aan een virtueel StorSimple-apparaat.
2. Verbind het virtuele apparaat via iSCSI met een tijdelijke Azure VM.
3. Installeer Azure File Sync op de tijdelijke Windows Server-VM: er moet ook een specifieke register sleutel voor deze migratie worden ingesteld voordat de synchronisatie op de server is geconfigureerd.
    * Afhankelijk van het aantal shares op uw StorSimple-volume, implementeert u net zoveel Azure-bestands shares. (We raden u aan om een Azure-bestands share per opslag account te implementeren.)
    * Configureer de synchronisatie tussen de afzonderlijke share op de Cloud-VM van Windows Server en een Azure-bestands share. (1:1-toewijzing)
    * Voeg eventueel een lokale server toe als een on-premises prestatie cache, waarbij Cloud lagen zijn ingeschakeld. Deze stap is nodig als u uw on-premises StorSimple wilt vervangen door een uitgebreidere, lokale cache, die wordt ondersteund door Windows Server en de Cloud lagen van Azure File Sync. Uw on-premises Windows Server kan een fysieke machine of cluster of een virtuele machine zijn. Het hoeft niet zoveel opslag ruimte te hebben als de grootte van de gegevensset. Er is alleen voldoende opslag ruimte nodig om de meest gebruikte bestanden lokaal in de cache op te slaan.

## <a name="minimizing-downtime"></a>Downtime minimaliseren
Na stap 3 hierboven is het on-premises StorSimple-apparaat nog steeds actief voor gebruikers en toepassingen. De set bestanden die zijn gesynchroniseerd vanaf de eerste volume kloon is dus iets verouderd op het moment dat de synchronisatie is voltooid.
De aanpak voor het minimaliseren van de uitval tijd is het herhalen van de synchronisatie vanaf het volume-kloon proces, zodat de synchronisatie sneller en sneller wordt uitgevoerd met elke iteratie, die op zijn beurt wordt ingeschakeld door de wijzigingen tussen volume klonen minder of minder.
U kunt dit proces herhalen totdat de synchronisatie vanaf een volume kloon kan worden voltooid in de tijd die u acceptabel vindt voor uitval tijd.
Als dit het geval is, blokkeert u gebruikers en toepassingen om wijzigingen aan te brengen in uw StorSimple-apparaat. De downtime wordt gestart.
Neem een andere volume kloon en laat deze synchroniseren met de server (s) die zijn verbonden.
Toegang tot uw gebruikers en toepassingen tot uw nieuwe, Azure File Sync back-up, Windows Server.
Overweeg het implementeren/aanpassen van een DFS-naam ruimte om de Uitsnij periode van het oude StorSimple-apparaat naar uw nieuwe Windows Server transparant te maken voor apps en gebruikers.
De migratie is voltooid.

## <a name="migration-goal"></a>Migratie doel
Nadat de migratie is voltooid, kunnen de inrichting van het tijdelijke StorSimple-virtuele apparaat en de Azure VM ongedaan worden gemaakt.

Daarnaast kan het on-premises StorSimple-apparaat worden ontprovisiond, omdat uw gebruikers en toepassingen al toegang hebben tot de Windows-Server.
Waar u mee bent, wordt weer gegeven in de onderstaande afbeelding. Een standaard Azure File Sync implementatie bevat een aantal Azure-bestands shares en Windows-servers die via Azure File Sync met hen zijn verbonden. Houd er rekening mee dat een enkele server op hetzelfde moment verschillende lokale mappen kan verbinden met verschillende bestands shares.
Daarnaast kan een Azure-bestands share worden gesynchroniseerd met een groot aantal verschillende servers, in het geval dat u gegevens in de cache van filialen nodig hebt. Controleer ook of u uw Cloud-laag beleid kunt optimaliseren voor efficiënter gebruik van uw on-premises opslag ruimte.

![Houdt](media/storage-sync-files-storsimple-migration/storsimple-docs-goal.PNG "Een afbeelding met het doel nadat de migratie is voltooid. Er wordt een aantal bestands shares weer gegeven dat wordt gesynchroniseerd met een on-premises Windows-Server met gebruikers en toepassingen die toegang hebben tot bestanden in de Cloud of op de Windows-Server.")

## <a name="next-steps"></a>Volgende stappen
Meer vertrouwd zijn met Azure Files en Azure File Sync. Het is belang rijk om inzicht te krijgen in het Azure File Sync terminologie-en implementatie patroon voor een geslaagde migratie. In dit overzichts artikel vindt u meer gedetailleerde informatie over elke stap. Zorg ervoor dat u contact opneemt met micro soft voor aangepaste Help tijdens het plannen en uitvoeren van de migratie.

> [!IMPORTANT]
> Micro soft streeft ernaar klanten te helpen bij hun migratie. E-mail AzureFiles@microsoft. com voor een aangepast migratie plan en voor hulp tijdens de migratie.

## <a name="additional-resources"></a>Aanvullende bronnen
Azure File Sync, als doel service, heeft twee fundamentele documenten die wij u raden als u geen ervaring hebt met Azure File Sync.
* [Azure File Sync-overzicht](storage-sync-files-planning.md)
* [Azure File Sync-implementatie handleiding](storage-sync-files-deployment-guide.md)

Azure Files is een opslag service in Azure en biedt bestands shares als een service. U hoeft niet te betalen voor of onderhoud aan een virtuele machine of gekoppelde VM-opslag.
* [Azure Files-overzicht](storage-files-introduction.md)
* [Azure Files-een Azure-bestands share implementeren](storage-how-to-create-file-share.md)