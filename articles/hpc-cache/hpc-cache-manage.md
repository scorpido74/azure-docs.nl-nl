---
title: Azure HPC-cache beheren en bijwerken
description: Azure HPC-cache beheren en bijwerken met de Azure-portal
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 1/29/2020
ms.author: rohogue
ms.openlocfilehash: 57d6a2024cd6fd979426ca5de5e261f110f6156f
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537947"
---
# <a name="manage-your-cache-from-the-azure-portal"></a>Uw cache beheren vanuit de Azure-portal

Op de pagina cacheoverzicht in de Azure-portal worden projectdetails, cachestatus en basisstatistieken voor uw cache weergegeven. Het heeft ook besturingselementen om de cache te stoppen of te starten, de cache te verwijderen, gegevens door te spoelen naar langdurige opslag en software bij te werken.

Als u de overzichtspagina wilt openen, selecteert u uw cachebron in de Azure-portal. Laad bijvoorbeeld de pagina **Alle bronnen** en klik op de cachenaam.

![schermafbeelding van de overzichtspagina van een Azure HPC-cache-instantie](media/hpc-cache-overview.png)

Met de knoppen boven aan de pagina u de cache beheren:

* **Start** en [**Stop**](#stop-the-cache) - Cachebewerking opschorten
* [**Flush**](#flush-cached-data) - Schrijft gewijzigde gegevens in opslagdoelen
* [**Upgrade**](#upgrade-cache-software) - Werkt de cachesoftware bij
* **Vernieuwen** - Herlaadt de overzichtspagina
* [**Delete**](#delete-the-cache) - Vernietigt de cache permanent

Lees hieronder meer over deze opties.

## <a name="stop-the-cache"></a>De cache stoppen

U de cache stoppen om de kosten tijdens een inactieve periode te verlagen. Er worden geen kosten in rekening gebracht voor uptime terwijl de cache is gestopt, maar er worden wel kosten in rekening gebracht voor de toegewezen schijfopslag in de cache. (Zie de [prijspagina](https://aka.ms/hpc-cache-pricing) voor meer informatie.)

Een gestopte cache reageert niet op clientverzoeken. U moet clients loskoppelen voordat u de cache stopt.

Met de knop **Stoppen** wordt een actieve cache opgeschort. De knop **Stoppen** is beschikbaar wanneer de status van een cache **gezond** of **gedegradeerd**is.

![schermafbeelding van de bovenste knoppen met Stop gemarkeerd en een pop-upbericht waarin de stopactie wordt beschreven en wordt gevraagd 'wilt u doorgaan?' met ja (standaard) en nee-knoppen](media/stop-cache.png)

Nadat u op Ja hebt geklikt om het stoppen van de cache te bevestigen, spoelt de cache automatisch de inhoud ervan door naar de opslagdoelen. Dit proces kan enige tijd duren, maar het zorgt voor consistentie van de gegevens. Ten slotte wordt de cachestatus gewijzigd **in Gestopt**.

Als u een gestopte cache opnieuw wilt activeren, klikt u op de knop **Start.** Er is geen bevestiging nodig.

![schermafbeelding van de bovenste knoppen met Start gemarkeerd](media/start-cache.png)

## <a name="flush-cached-data"></a>Gegevens in de cache spoelen

De knop **Spoelen** op de overzichtspagina vertelt de cache om onmiddellijk alle gewijzigde gegevens die in de cache zijn opgeslagen, te schrijven naar de back-end opslagdoelen. De cache slaat routinematig gegevens op voor de opslagdoelen, dus het is niet nodig om dit handmatig te doen, tenzij u ervoor wilt zorgen dat het back-endopslagsysteem up-to-date is. U **Flush** bijvoorbeeld gebruiken voordat u een opslagmomentopname maakt of de grootte van de gegevensset controleert.

> [!NOTE]
> Tijdens het spoelproces kan de cache clientaanvragen niet dienen. Toegang tot de cache wordt opgeschort en hervat nadat de bewerking is voltooid.

![screenshot van de bovenste knoppen met Flush gemarkeerd en een pop-up bericht waarin de flush actie en de vraag 'wil je doorgaan?' met ja (standaard) en nee-knoppen](media/hpc-cache-flush.png)

Wanneer u de cacheflushbewerking start, accepteert de cache geen clientaanvragen meer en wordt de cachestatus op de overzichtspagina gewijzigd in **Doorspoelen.**

Gegevens in de cache worden opgeslagen in de juiste opslagdoelen. Afhankelijk van hoeveel gegevens moeten worden gespoeld, kan het proces enkele minuten of meer dan een uur duren.

Nadat alle gegevens zijn opgeslagen in opslagdoelen, begint de cache automatisch opnieuw met het aannemen van clientaanvragen. De cachestatus keert terug naar **Gezond**.

## <a name="upgrade-cache-software"></a>Cachesoftware upgraden

Als er een nieuwe softwareversie beschikbaar is, wordt de knop **Upgrade** actief. U ziet ook een bericht boven aan de pagina over het bijwerken van software.

![schermafbeelding van de bovenste rij knoppen met de knop Bijwerken ingeschakeld](media/hpc-cache-upgrade-button.png)

Clienttoegang wordt niet onderbroken tijdens een software-upgrade, maar de prestaties in de cache vertragen. Plan om software te upgraden tijdens niet-piekgebruiksuren of in een geplande onderhoudsperiode.

De software-update kan enkele uren duren. Caches die zijn geconfigureerd met een hogere doorvoer, nemen langer in beslag dan caches met kleinere piekdoorvoerwaarden.

Wanneer een software-upgrade beschikbaar is, heb je een week of zo om het handmatig toe te passen. De einddatum wordt vermeld in het upgradebericht. Als u gedurende die tijd niet upgradet, past Azure de update automatisch toe op uw cache. De timing van de automatische upgrade is niet configureerbaar. Als u zich zorgen maakt over de impact op de cacheprestaties, moet u de software zelf upgraden voordat de periode verstrijkt.

Als uw cache wordt gestopt wanneer de einddatum verstrijkt, wordt de software automatisch geüupgradet wanneer deze de volgende keer wordt gestart. (De update start mogelijk niet onmiddellijk, maar het begint in het eerste uur.)

Klik **op de** knop Bijwerken om de software-update te starten. De cachestatus verandert in **Upgraden** totdat de bewerking is voltooid.

## <a name="delete-the-cache"></a>De cache verwijderen

Met de knop **Verwijderen** wordt de cache vernietigd. Wanneer u een cache verwijdert, worden alle bronnen vernietigd en worden er geen accountkosten meer in rekening gebracht.

De back-endopslagvolumes die als opslagdoelen worden gebruikt, worden niet beïnvloed wanneer u de cache verwijdert. U ze later toevoegen aan een toekomstige cache of ze apart buiten gebruik stellen.

> [!NOTE]
> Azure HPC Cache schrijft niet automatisch gewijzigde gegevens van de cache naar de back-endopslagsystemen voordat de cache wordt verwijderd.
>
> Als u ervoor wilt zorgen dat alle gegevens in de cache naar langdurige opslag zijn geschreven, [stopt u de cache](#stop-the-cache) voordat u deze verwijdert. Zorg ervoor dat de status **Gestopt** wordt weergegeven voordat u op de knop Verwijderen klikt.

## <a name="cache-metrics-and-monitoring"></a>Cachestatistieken en -bewaking

De overzichtspagina toont grafieken voor een aantal basiscachestatistieken - cachedoorvoer, bewerkingen per seconde en latentie.

![schermafbeelding van drie regelgrafieken met de hierboven genoemde statistieken voor een voorbeeldcache](media/hpc-cache-overview-stats.png)

Deze grafieken maken deel uit van de ingebouwde monitoring- en analysetools van Azure. Extra hulpprogramma's en waarschuwingen zijn beschikbaar op de pagina's onder de kop **Controle** in de zijbalk van de portal. Meer informatie in het portalgedeelte van de [Azure Monitoring-documentatie](../azure-monitor/insights/monitor-azure-resource.md#monitoring-in-the-azure-portal).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure-hulpprogramma's voor statistieken en statistieken](../azure-monitor/index.yml)
* [Hulp krijgen bij uw Azure HPC-cache](hpc-cache-support-ticket.md)
