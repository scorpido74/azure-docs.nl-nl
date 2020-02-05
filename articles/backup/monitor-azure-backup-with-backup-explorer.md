---
title: Uw back-ups controleren met backup Explorer
description: Een artikel waarin wordt beschreven hoe u back-up Explorer kunt gebruiken voor het uitvoeren van real-time bewaking van back-ups in kluizen, abonnementen, regio's en tenants
ms.reviewer: dcurwin
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 331d8aeeb828dedb6700a94fafa074c179bef7ab
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992181"
---
# <a name="monitor-your-backups-with-backup-explorer"></a>Uw back-ups controleren met backup Explorer

Als organisaties een back-up van meer en meer machines naar de Cloud maken, is het belang rijk dat u een centrale locatie hebt voor het weer geven van operationele informatie over back-ups over een groot goed, om efficiënt back-ups te controleren.

De werkmap van de back-up Explorer is een ingebouwde Azure Monitor werkmap waarmee back-uptoepassingen een enkel glas venster kunnen hebben voor het uitvoeren van operationele bewakings activiteiten met betrekking tot het maken van een back-up van het hele back-upbestand op Azure (die tenants, locaties, abonnementen, resource groepen en kluizen), allemaal vanaf een centrale locatie. In ruime mate biedt het de volgende mogelijkheden:

* **Op schaal perspectief** : een geaggregeerde weer gave van de back-upitems, taken, waarschuwingen, beleids regels en resources die niet zijn geconfigureerd voor back-up op de volledige back-up. 
* **Analyse van inzoomen** : u kunt ervoor kiezen om gedetailleerde informatie over elk van de entiteiten te verkrijgen: de taken, waarschuwingen, beleids regels en back-upitems, allemaal vanaf één locatie.
* **Interfaces waarvoor actie** kan worden uitgevoerd: Zodra u een probleem hebt geïdentificeerd, kunt u kiezen om acties uit te voeren door naadloos naar het back-upitem of de Azure-resource te navigeren.

De bovenstaande mogelijkheden worden out-of-the-box door systeem eigen integratie geboden met Azure resource Graph en Azure Monitor werkmappen.

> [!NOTE]
> * Backup Explorer is momenteel alleen beschikbaar voor Azure VM-gegevens.
> * De back-up Verkenner is bedoeld als operationeel dash board voor het weer geven van informatie over uw back-ups in de afgelopen 7 dagen (maximum).
> * Het is momenteel niet mogelijk om de back-upverkenner-sjabloon aan te passen. Het is ook niet aanbevolen om aangepaste automatiserings functies te schrijven op Azure-resource grafiek gegevens.

## <a name="getting-started"></a>Aan de slag

U kunt de back-up Verkenner openen door naar een van uw Recovery Services-kluizen te gaan en te klikken op de koppeling **back-up Verkenner** op de pagina **overzicht** .

![Snelle koppeling van kluis](media/backup-azure-monitor-with-backup-explorer/vault-quick-link.png)

Als u op de koppeling klikt, wordt de back-upverkenner geopend, met daarin een geaggregeerde weer gave over alle kluizen en abonnementen waartoe u toegang hebt. Als u een Azure Lighthouse-account gebruikt, kunt u gegevens weer geven over alle tenants waartoe u toegang hebt (Zie de sectie hieronder voor meerdere Tenant weergaven) voor meer informatie.

![Landings pagina Explorer](media/backup-azure-monitor-with-backup-explorer/explorer-landing-page.png)

## <a name="backup-explorer-use-cases"></a>Use cases in back-up Verkenner

De back-up Verkenner bestaat uit meerdere tabbladen. elk tabblad bevat gedetailleerde informatie over een specifiek type back-upartefact, bijvoorbeeld back-upitems, taken, beleids regels, enzovoort. Deze sectie bevat een kort overzicht van elk van de tabbladen. De Video's bieden voorbeeld cases voor elk van de tabbladen, samen met een beschrijving van de verschillende beschik bare besturings elementen voor de gebruiker.

### <a name="summary"></a>Samenvatting

Op het tabblad samen vatting kunt u een kort overzicht krijgen van de algemene voor waarden van uw back-up. U kunt informatie weer geven, zoals het aantal items dat wordt beveiligd, het aantal items waarvoor de beveiliging niet is ingeschakeld, hoeveel taken zijn geslaagd in de afgelopen 24 uur, enzovoort. 

Elk van de andere tabbladen vertegenwoordigt een afzonderlijke entiteit – bijvoorbeeld: Back-upitems, back-uptaken, back-upwaarschuwingen en back-upbeleid. U kunt ook informatie weer geven over machines waarvoor geen back-up is geconfigureerd. Als u op een van deze tabbladen klikt, wordt informatie weer gegeven die specifiek is voor die entiteit.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYd]

### <a name="backup-items"></a>Back-upitems

U kunt elk van uw back-upitems, gefilterd op abonnement, kluis en andere para meters weer geven. Als u op de naam van een back-upitem klikt, kunt u de Azure-Blade voor dat back-upitem openen. U kunt bijvoorbeeld zien dat de laatste back-up is mislukt voor item ' X ' in de tabel. Klik op X om de Blade back-up voor dit item te openen, waar u een back-upbewerking op aanvraag kunt activeren.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQYc]

### <a name="jobs"></a>Taken

U kunt de details weer geven van alle taken die in de afgelopen zeven dagen zijn geactiveerd door te navigeren naar het tabblad taken. Hier kunt u filteren op taak bewerking, taak status en fout code (in het geval van mislukte taken).

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nOrh]

### <a name="alerts"></a>Waarschuwingen

U kunt details weer geven van alle waarschuwingen die in de afgelopen zeven dagen zijn geactiveerd voor uw kluizen door te klikken op het tabblad waarschuwingen. Hier kunt u records filteren op het type waarschuwing (bijvoorbeeld back-upfout, herstel fout), de huidige status van de waarschuwing (bijvoorbeeld actief of opgelost) en de ernst van de waarschuwing (bijvoorbeeld kritiek, waarschuwing, informatie). U kunt ook naar de Azure-VM navigeren door te klikken op de koppeling naar de virtuele machine en de nood zakelijke actie te ondernemen.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nTxe]

### <a name="policies"></a>Beleid

Als u op het tabblad beleid klikt, kunt u belang rijke informatie weer geven over alle back-upbeleiden die zijn gemaakt in uw back-up. U kunt zien hoeveel items zijn gekoppeld aan elk beleid, samen met de Bewaar termijn en de back-upfrequentie die door elk beleid zijn opgegeven.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nLKV]

### <a name="backup-not-enabled"></a>Back-up niet ingeschakeld

Het is belang rijk dat de back-upbeheerder van een organisatie snel kan vaststellen op welke computers in de organisatie nog geen back-up is ingeschakeld, zodat back-up kan worden ingeschakeld voor alle computers die beveiliging nodig hebben. Als u op de tab **-back-up niet ingeschakeld** klikt, kunt u in deze taak.

Op dit tabblad wordt een tabel weer geven met een lijst met items die niet zijn beveiligd. Als uw organisatie de procedure volgt voor het toewijzen van verschillende labels aan productie machines en test machines, of machines voor verschillende functies, die elk een afzonderlijk back-upbeleid nodig hebben, kunt u filteren op Tags gebruiken om informatie weer te geven die specifiek is voor een bepaalde machine klasse. Als u op de naam van een item klikt, wordt u omgeleid naar de Blade **back-up configureren** voor dat item. hier kunt u een back-up maken van dat item met een geschikt back-upbeleid.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4nQXZ]

## <a name="exporting-to-excel"></a>Exporteren naar Excel

Als u op de pijl-omlaag in de rechter bovenhoek van een wille keurige widget (tabel/grafiek) klikt, wordt de inhoud van die widget geëxporteerd als een Excel-werk blad, net als bij bestaande filters die zijn toegepast. Als u meer rijen van een tabel wilt exporteren naar Excel, kunt u het aantal rijen dat wordt weer gegeven op de pagina verhogen door de vervolg keuzelijst **rijen per pagina** boven aan elk tabblad te gebruiken.

## <a name="pinning-to-dashboard"></a>Vastmaken aan dash board

U kunt op het speld pictogram boven aan elke widget klikken om die widget aan uw Azure Portal-dash board vast te maken. Dit helpt u bij het maken van aangepaste Dash boards die zijn aangepast om de belangrijkste informatie die u nodig hebt, weer te geven.

## <a name="cross-tenant-views"></a>Cross-Tenant weergaven

Als u een Azure Lighthouse-gebruiker bent met gedelegeerde toegang tot abonnementen in meerdere Tenant omgevingen, kunt u het standaard abonnements filter gebruiken (door te klikken op het filter pictogram in de rechter bovenhoek van de Azure Portal) om alle abonnementen te selecteren die u wilt Zie gegevens voor. Als u dit doet, wordt de back-upverkenner in staat stellen om informatie te verzamelen over alle kluizen in deze abonnementen. Meer informatie over Azure Lighthouse [vindt u hier](https://docs.microsoft.com/azure/lighthouse/overview).

## <a name="next-steps"></a>Volgende stappen

[Meer informatie over het gebruik van Azure Monitor voor het verkrijgen van inzichten in uw back-upgegevens](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)