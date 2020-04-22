---
title: Azure Media Player Changelog
description: Azure Media Player changelog.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 15f8a3ac8c2777b3a878de92db495e559f64ad20
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726548"
---
# <a name="changelog"></a>Changelog #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (Officiële update 22 februari 2019) ##

### <a name="bug-fixes-224"></a>Bug fixes 2.2.4 ###

- [Bug Fix] [AMP] [Toegankelijkheid] Een bereikbaar fantoomtabblad verwijderd wanneer het foutscherm wordt weergegeven
- [Bug Fix] [AMP] Fixed the hotkey 'M' for IE11 and Edge
- [Bug Fix] [AMP] Een uitzondering voor CEA708-bijschriften opgelost
- [Bug Fix] [AMP] Fixed a video freeze issue for the Edge browser Fixed an video freeze issue for the Edge browser Fixed a video freeze issue for the Edge browser Fixed a

### <a name="changes-224"></a>Wijzigingen 2.2.4 ###

- [Wijzigen] [AMP] Wanneer een fragment decryptie fout optreedt, de speler opnieuw huidige en verschillende fragmenten om de weergave te herstellen
- [Wijzigen] [AMP] AMP toleranter gemaakt ten opzichte van overlappende video- of audiofragmenten

## <a name="223-official-update-january-9-2019"></a>2.2.3 (Officiële update 9 januari 2019) ##

### <a name="features"></a>Functies ###

- [Functie] [HLS] Het audiotrackmenu voor safari HLS-weergave toegevoegd

### <a name="bug-fixes-223"></a>Bug fixes 2.2.3 ###

- [Bug Fix] [AMP] [Toegankelijkheid] Tijdens live uitzendingen kan de knop 'live' niet worden geselecteerd met behulp van het toetsenbord
- [Bug Fix] [AMP] Fixed false positives 0x0400003 errors due to failed MSE test Fixed false positives 0x0400003 errors due to failed MSE test Fixed false positives 0x0400003 errors due to failed MSE test Fixed false
- [Bug Fix] [AMP] Probleem opgelost waarbij de video kan bevriezen bij het starten van een live stream

### <a name="changes-223"></a>Wijzigingen 2.2.3 ###

- [Wijzigen] [AMP] Meer informatie toegevoegd in het logboek om betere diagnostiek mogelijk te maken
- [Wijzigen] [AMP] Wanneer er meer dan één bitrate beschikbaar is op dezelfde schermresolutie, zijn alle bitrates beschikbaar voor selectie

## <a name="222-official-update"></a>2.2.2 (officiële update) ##

### <a name="bug-fixes-222"></a>Bug fixes 2.2.2 ###

- [Bug Fix] [AMP] Wanneer de speler een tijdelijke netwerkstoring ondervindt, wordt het afspelen onmiddellijk gestopt
- [Bug Fix] [AMP] [Toegankelijkheid] Het foutdialoogvenster is niet toegankelijk via het toetsenbord
- [Bug Fix] [AMP] Oneindige spinner weergegeven bij het afspelen van audio alleen asset in plaats van niet-ondersteunde fout

### <a name="changes-222"></a>Wijzigingen 2.2.2 ###

- [Wijzigen] [AMP] gelokaliseerde tekenreeksen toegevoegd voor advertentie-gebruikersinterface

## <a name="221-official-update"></a>2.2.1 (officiële update) ##

### <a name="features-221"></a>Functies 2.2.1 ###

- [Functie] [CMAF] Ondersteuning toegevoegd voor HLS CMAF

### <a name="bug-fixes"></a>Opgeloste fouten ###

- [Bug Fix] [AMP] onduidelijke timers in retry logica waardoor afspeelfouten
- [Bug Fix] [AMP] [Firefox] beëindigde gebeurtenis wordt niet afgevuurd op Firefox en Chrome wanneer gestopt met het live-programma
- [Bug Fix] [AMP] Besturingselementen die worden weergegeven na setsource, zelfs wanneer besturingselementen zijn ingesteld op false in de spelersopties

### <a name="changes"></a>Wijzigingen ###

- [Wijzigen] [Live captioning] De API-naam voor CEA-bijschriften gewijzigd van 608 naar 708. Zie [CEA708 Captions Settings](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings) voor meer informatie-->

## <a name="220-official-release"></a>2.2.0 (officiële versie) ##

### <a name="features-220"></a>Functies 2.2.0 ###

- [Functie] [Azurehtml5JS] [Flash] [LiveCaptions] CEA 708 ondertiteling ondersteuning in Azurehtml5JS en FlashSS tech voor duidelijke en AES-inhoud.

### <a name="bug-fixes-220"></a>Bug fixes 2.2.0 ###

- [Bug Fix] Flash-versiedetectie werkt niet in Chrome/Edge

### <a name="changes-220"></a>Wijzigingen 2.2.0 ###

- [Wijzigen] [AMP] [Heuristiek] Heuristische profielnaam gewijzigd van QuickStartive naar LowLatency
- [Wijzigen] [Flash] Wijzig in Flash-player voor versiedetectie om het afspelen van AES-inhoud mogelijk te maken met de nieuwe Adobe Flash-update.

## <a name="219-official-hotfix"></a>2.1.9 (Officiële Hotfix) ##

### <a name="bug-fixes-219"></a>Bug fixes 2.1.9 ###

- [Bug Fix] [Live] Uitzondering die zich voordoet wanneer live streams overgaan naar video on demand/live archieven

### <a name="changes-219"></a>Wijzigingen 2.1.9 ###

- [Wijzigen] [Flash] [AES] Gewijzigde Flash-technologie logica om geen sharedbytearrays te gebruiken voor AES-decryptie, omdat Adobe het gebruik vanaf Flash 30 heeft geblokkeerd. Let op: afspelen werkt pas als Adobe een nieuwe versie van Flash implementeert vanwege een bug in de V30. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer details

## <a name="218-official-update"></a>2.1.8 (officiële update) ##

### <a name="bug-fixes-218"></a>Bug fixes 2.1.8 ###

- [Bug fix] Spinner toont af en toe geen post zoeken en pre-play
- [Bug Fix] Speler wordt niet gedempt wanneer de optie gedempt is ingeschakeld
- [Bug Fix] Volumeschuifregelaar wordt weergegeven wanneer besturingselementen zijn ingesteld op false
- [Bug Fix] Af en toe afspelen wordt herhaald wanneer de gebruiker naar de live rand gaat
- [Bug Fix] [Firefox] Speler gooit af en toe JavaScript-uitzondering op belasting
- [Bug Fix] [Toegankelijkheid] Knop Afspelen/ Pauzeren/Volume verliest focusoverzicht wanneer deze is geselecteerd met toetsenbordbesturingselementen
- [Bug Fix] Vaste geheugenlekkage op speler wordt verwijderd
- [Bug Fix] Bellen src() na speler fouten uit niet reset de bron
- [Bug Fix] [Live] AMP is constant geladen wanneer de gebruiker op de live-knop klikt nadat de uitzending is beëindigd
- [Bug Fix] [Chrome] Speler hangt vast en afspelen mislukt wanneer browser geminimaliseerd naar achtergrond.

### <a name="changes-218"></a>Wijzigingen 2.1.8 ###

- [Wijzigen] Bijgewerkt 0x0600001 errror om weer te geven wanneer AES-inhoud wordt afgespeeld met Flash 30 als het niet wordt ondersteund op dit moment. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer details
- [Wijzigen] Extra nieuwe pogingen toegevoegd voor live scenario's wanneer manifestaanvragen 404 of lege manifesten retourneren.

## <a name="217-official-update"></a>2.1.7 (officiële update) ##

### <a name="features-217"></a>Functies 2.1.7 ###

- [Functie] [AzureHtml5JS] De optie voor extra configuratie om verouderde gegevens in de mediabronbuffer door te spoelen

### <a name="bug-fixes-217"></a>Bug fixes 2.1.7 ###

- [Bug Fix] [Toegankelijkheid] [Schermlezer] Verwijderde de lege koptekst die de speler heeft opgenomen wanneer de titel niet is ingesteld
- [Bug Fix] [UWA] AMP gooit uitzondering bij afspelen in Universele Windows App
- [Bug Fix] [OSX] setActiveTextTrack() werkt niet in Safari op OSx
- [Bug Fix] [Live] Klikken naar de live rand na het verwijderen en opnieuw initialiseren van de speler levert uitzondering
- [Bug Fix] [Huid] Huidige tijd afgekapt voor bepaalde activa
- [Bug Fix] [DRM] fix opgenomen om afspelen te ondersteunen in browsers die meerdere CENC DRM ondersteunen

### <a name="changes-217"></a>Wijzigingen 2.1.7 ###

- [Wijzigen] [Monsters] [Toegankelijkheid] Taaltag toegevoegd aan alle voorbeelden

## <a name="216-official-update"></a>2.1.6 (officiële update) ##

### <a name="bug-fixes-216"></a>Bug fixes 2.1.6 ###

- [Bug Fix] AMP die onjuiste duur voor specifiek actief weergeeft
- [Bug Fix] [FairPlay-HLS] Fairplay-fouten die niet worden doorgegeven aan de gebruikersinterface
- [Bug Fix] Aangepaste Heuristische eigenschappen worden genegeerd in AMP 2.1.5.

### <a name="changes-216"></a>Wijzigingen 2.1.6 ###

- [Wijzigen] [FairPlayDRM] Verwijderde de time-out voor zowel Cert aanvraag en licentie aanvraag voor FairPlay om pariteit te houden met PlayReady en Widevine implementaties
- [Wijzigen] Misc Heuristische verbeteringen om wazige inhoud te bestrijden

### <a name="features-216"></a>Functies 2.1.6 ###

- [Functie] Ondersteuning mpd-time-cmaf formaat toegevoegd

## <a name="215-official-hotfix"></a>2.1.5 (Officiële Hotfix) ##

### <a name="bug-fixes-215"></a>Bug fixes 2.1.5 ###

- [Bug Fix] [Bijschriften] VTT styling niet correct weergegeven door speler
- [Bug Fix] [Toegankelijkheid] Live knop heeft geen aria label

## <a name="214-official-update"></a>2.1.4 (officiële update) ##

### <a name="bug-fixes-214"></a>Bug fixes 2.1.4 ###

- [Bug Fix] [Toegankelijkheid] [Focus] Gebruikers kunnen zich niet concentreren op aangepaste knoppen die rechts van de knop volledig scherm in de bedieningsbalk zijn toegevoegd
- [Bug Fix] [IE11] [Volumebalk] Tabbentot volume pop-up maakt het hele videoscherm knipperen in IE11, terwijl in full-screen modus
- [Bug Fix] [Huid| Flush] Ruimte weergegeven tussen de regelbalk en de pop-up van de volumebalk
- [Bug Fix] [AMP] [Bijschriften] Oude embedded tracks worden niet gewist wanneer de bron wordt gewijzigd op een bestaande speler
- [Bug Fix] [Toegankelijkheid] [Verteller] Schermlezer leest volumeregeling verkeerd
- [Bug Fix] [Knippert] Play Event af en toe niet vuur van Flash tech
- [Bug Fix] [AMP] [Focus] Afspelen/pauzeren vereist twee klikken wanneer de speler focus heeft en in de modus volledig scherm staat
- [Bug Fix] [AMP] [Huid] Onjuiste duur wordt weergegeven op de voortgangsbalk voor een specifiek actief
- [Bug Fix] [Advertenties] [Ad Butler] VAST parser verwerkt vast bestand niet dat geen voortgangsgebeurtenis heeft
- [Bug Fix] [SDN] [AMP 2.1.1] Probleem opgelost voor ondersteuning voor Hive SDN-plug-in
- [Bug Fix] [Toegankelijkheid] Verteller leest "Midnight Mute Button" wanneer de gebruiker de volumeknop scherp heeft

### <a name="changes-214"></a>Wijzigingen 2.1.4 ###

- [Wijzigen] [Toegankelijkheid] [Ondersteunende technologie] Buttons hebben nu aria-live-accommodatie om de ervaring met ondersteunende technologie te verbeteren
- [Wijzigen] [Toegankelijkheid] [Knop Volume| Verteller]Verbeterde toegankelijkheid van de volumeknop door de tabbenfunctionaliteit en het schuifregelaargedrag aan te passen. Deze wijzigingen maken het gemakkelijker voor toetsenbordgebruikers om het volume van de speler aan te passen
- [Wijzigen] Time-out van het inactiviteitscontextmenu van 3 naar 5 seconden
- [Wijzigen] [Toegankelijkheid] [Helderheid] Verbeterde contrastverhouding voor helderheid op vervolgkeuzemenu's in instellingen voor bijschriften

## <a name="213-official-update"></a>2.1.3 (officiële update) ##

### <a name="bug-fixes-213"></a>Bug fixes 2.1.3 ###

- [Bug Fix] [Plug-ins| Titel Overlay] Titel Overlay plugin gooit JS uitzonderingen met AMP v2. X+
- [Bug Fix] Gebeurtenis Bronset wordt verzonden naar javascript-console, zelfs wanneer logboekregistratie is uitgeschakeld
- [Bug Fix] [Huid] Spelerstijdtips worden buiten de context van de speler weergegeven wanneer u over een van beide eindduurbalkzweeft
- [Bug Fix] [Toegankelijkheid] [Schermlezer] Verteller leest 'Regiolandmark' of 'Landmark van de videospelerregio' wanneer de kijker zich op de speler heeft gericht
- [Bug Fix] [AMP] Kan spelersoverzicht niet uitschakelen via CSS
- [Bug Fix] [Toegankelijkheid] Kan niet tab om zich op volledige speler te concentreren wanneer de gebruiker in volledig-schermwijze is
- [Bug Fix] [Huid] [Live] Skin reageert niet op gelokaliseerde LIVE-tekst in het Japans
- [Bug Fix] [Huid] Duur en huidige tijd worden afgesneden wanneer stream > 60 min -[Bug Fix][iPhone| Live]-speler toont tekst voor huidige tijd/duur in de controlebalk
- [Bug Fix] [AMP] Bellende spelersheuristiek API's levert JavaScript-uitzonderingen op
- [Bug Fix] [Native Html5|iOS] Videotag eigenschap "playsinline" niet verspreiden naar speler
- [Bug Fix] [iOS|iframe] Speler kan niet volledig scherm op iPhone invoeren als de speler in een iframe wordt geladen
- [Bug Fix] [AMP] [Heuristiek] AMP werkt altijd met hybride profiel, ongeacht de spelersopties
- [Bug Fix] [AMP| Win8.1]throws when hosted in Win8.1 app with a webview Win8.1]throws when hosted in Win8.1 app with a webview Win8.1]throws when hosted in Win8.1 app with a webview Win8

### <a name="changes-213"></a>Wijzigingen 2.1.3 ###

- [Wijzigen] [AMP] Cdn-eindpuntgegevens toegevoegd in de gebeurtenis FragmentDownloadComplete
- [Wijzigen] [AMP] [Live] Verbeterde en geoptimaliseerde latentie voor live streaming

## <a name="212-official-hotfix"></a>2.1.2 (Officiële Hotfix) ##

### <a name="bug-fixes-212"></a>Bug fixes 2.1.2 ####

- [Bug Fix] [Toegankelijkheid] [Windows Verteller] Verteller leest 'Voortgang middernacht' wanneer de gebruiker context heeft van de voortgangsbalk en de huidige tijd 0:00 is
- [Bug Fix] [Skin]logo grootte is hard-gecodeerd in JavaScript-code
- [Toegankelijkheid] [Sneltoetsen] Sneltoetsen zijn niet ingeschakeld wanneer op de speler wordt geklikt.

### <a name="changes-212"></a>Wijzigingen 2.1.2 ####

- [Wijzigen] [Logging] Log manifest URL wanneer speler niet te laden manifest

### <a name="features-212"></a>Functies 2.1.2 ###

- [Wijzigen] [Prestaties] [Optimalisatie] Verbeterde laad- en opstarttijden van spelers

## <a name="211-official-update"></a>2.1.1 (officiële update) ##

### <a name="bug-fixes-211"></a>Bug fixes 2.1.1 ####

- [Bug Fix] [iOS] Autoplay instellen op valse opbrengst oneindige spinner in Safari voor iOS
- [Bug Fix] Op zoek naar een tijd groter dan de inhoud duur levert oneindige spinner
- [Bug Fix] Sneltoetsen vereisen meerdere toetsenbordtabbladen om de context van de speler aan het werk te krijgen
- [Bug Fix] Video bevriest voor een paar seconden na het wijzigen van de speler in bepaalde activa
- [Bug Fix] Oneindige spinner (na zoeken voltooit) wanneer de gebruiker meerdere zoekt snel
- [Bug Fix] De besturingsbalk is niet verborgen tijdens inactiviteit
- [Bug Fix] Als u een webapp opent met AMP, kan de webpagina twee keer worden geladen
- [Bug Fix] Oneindig tijdens het afspelen van inhoud bepaalde activa via Flash Tech
- [Bug Fix] Meer opties menu niet wordt weergegeven met 3rd party plugins
- [Bug Fix] [Huid| Tube][Live] Twee live iconen worden weergegeven wanneer de speler zich aan de live rand van een programma bevindt
- [Bug Fix] [Huid] Logo kan niet worden uitgeschakeld
- [Bug Fix] [DD+ inhoud] Continue spinner verschijnt voor de assets met Dolby Digital audio track
- [Bug Fix] Nieuwste AMP bevriest bij het schakelen van audiotaaltracks tijdens livestream
- [Bug Fix] vaste achtergrond verdwijning voor spinner
- [Bug Fix] Oneindige spinner in AES flash token statische monsters bug fixes

### <a name="changes-211"></a>Wijzigingen 2.1.1 ####

- [Wijzigen] Toegevoegd foutcode voor Widevine Https-vereiste: vanaf Chrome v58 moet widevine-inhoud worden geladen/afgespeeld via het `https://` protocol, anders mislukt het afspelen.
- [Wijzigen] Aria-label toegevoegd voor het laden van spinner, zodat ondersteunende technologie kan vertellen "video laden" wanneer de inhoud wordt geladen  

## <a name="210-official-release"></a>2.1.0 (officiële versie) ##

### <a name="features-210"></a>Functies 2.1.0 ###

- [Functie] [AzureHtml5JS] VOD-advertentieondersteuning voor pre-mid-post-rolls
- [Functie] [Beta] [AzureHtml5JS] Live advertentieondersteuning voor pre-mid-post-rolls
- [Functie] Nieuwe skin optie toegevoegd - AMP-flush
- [Functie] Verbeterde aria-labels toegevoegd voor een betere integratie met schermlezers/ondersteunende technologie
- [Functie] [Huid] Skin toont nu alle pictogrammen en knoppen duidelijk in hoog contrast modus

### <a name="bug-fixes-210"></a>Bug fixes 2.1.0 ###

- [Bug Fix] Aantal toegankelijkheids- en gebruikersinterface-fixes
- [Bug Fix] AMP laadt niet correct in IE9

### <a name="changes-210"></a>Wijzigingen 2.1.0 ###

- [Wijzigen] Geherstructureerde DOM-elementen in de speler om advertenties te laten werken
- [Wijzigen] Overgestapt van CSS naar SCSS voor huidontwikkeling
- [Wijzigen] [Monsters] Voorbeeld toegevoegd voor VOD-advertenties
- [Wijzigen] [Monsters] Voorbeeld toegevoegd voor afspeelsnelheid
- [Wijzigen] [Monsters] Toegevoegd monster voor Flush Skin

## <a name="200-beta-release"></a>2.0.0 (bètaversie) ##

- [Change]bijgewerkt naar VJS5
- [feature] Nieuwe vloeiende API toegevoegd voor responsiviteitsvloeistof voor spelers
- [Functie] Afspeelsnelheid
- [Wijzigen] Overgestapt van CSS naar SCSS voor de huid

## <a name="183-official-hotfix-update"></a>1.8.3 (officiële Hotfix-update) ##

### <a name="bug-fixes-183"></a>Bug fixes 1.8.3 ###

- [Bug Fix] [AzureHtml5JS] Bepaalde assets met negatieve DTS worden niet afgespeeld in Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (officiële Hotfix-update) ##

### <a name="bug-fixes-182"></a>Bug fixes 1.8.2 ###

- [Bug Fix] [AzureHtml5JS] Hogere audiobitrates worden niet afgespeeld via AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (officiële update) ##

### <a name="bug-fixes-181"></a>Bug fixes 1.8.1 ###

- [Bug Fix] [iOS] Bijschriften/ondertitels die niet worden weergegeven in native player
- [Bug Fix] [AMP] CDN-backed streaming URL's toegevoegd met authenticatie tokens niet afgespeeld
- [Bug Fix] [FairPlay] FairPlay-foutcode ontbreekt Tech ID (Bits [31-28] van de ErrorCode) zie Foutcodes voor meer details
- [Bug Fix] [Safari] [PlayReady] PlayReady-inhoud in Safari levert oneindige spinner op

### <a name="changes-181"></a>Wijzigingen 1.8.1 ###

- [Wijzigen] [Html5] Native Html5-technologielogboeken wijzigen om gebeurtenissen van VideoTag te bevatten

## <a name="180-official-update"></a>1.8.0 (officiële update) ##

### <a name="features-180"></a>Functies 1.8.0 ###

- [Kenmerken] [DRM] FairPlay-ondersteuning toegevoegd (zie [beveiligde inhoud](azure-media-player-protected-content.md) voor meer informatie)

### <a name="bug-fixes-180"></a>Bug fixes 1.8.0 ###

- [Bug Fix] [AMP] Zoeken door gebruikers leidt niet tot een wachtgebeurtenis wanneer het netwerk wordt beperkt
- [Bug Fix] [Knippert] Het selecteren van kwaliteit in flash tech gooit uitzondering
- [Bug Fix] [AMP] Dynamisch selecteren van kwaliteit wordt wel weergegeven in contextmenu
- [Bug Fix] [Huid] Het is moeilijk om het laatste menu-item van contextmenu's te selecteren

### <a name="changes-180"></a>Wijzigingen 1.8.0 ###

- [Wijzigen] Speler bijgewerkt naar huidige Chrome EME-vereisten
- [Wijzigen] Standaard techOrder gewijzigd om nieuwe tech- html5FairPlayHLS (zie [Beveiligde inhoud](azure-media-player-protected-content.md) voor meer info)
- [Wijzigen] [AzureHtml5JS] MPEG-Dash afspelen ingeschakeld in Safari
- [Wijzigen] [Monsters] Gewijzigde Multi-DRM monsters om FairPlay tegemoet te komen

## <a name="174-official-hotfix-update"></a>1.7.4 (officiële Hotfix-update) ##

### <a name="bug-fixes-174"></a>Bug fixes 1.7.4 ###

- [Bug Fix] [Chrome] Blauwe omtrek wordt weergegeven rond zoekgreep wanneer de gebruiker context van de speler heeft
- [Bug Fix] [IE9] JavaScript-uitzondering gegooid wanneer speler geladen is in IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (officiële Hotfix-update) ##

### <a name="bug-fixes-173"></a>Bug fixes 1.7.3 ###

- [Bug Fix] [AzureHtml5JS] Speler timing uit in beperkte netwerken

### <a name="changes-173"></a>Wijzigingen 1.7.3 ###

- [Wijzigen] Webcrypto on Edge inschakelen voor het decoderen van AES-inhoud
- [Wijzigen] AMP-heuristiek optimaliseren om rekening te houden met gecachede brokken
- [Wijzigen] [AzureHtml5JS] Optimaliseer heuristisch door bandbreedteschattinglatentie te verminderen

## <a name="172-official-hotfix-update"></a>1.7.2 (officiële Hotfix-update) ##

### <a name="features-172"></a>Functies 1.7.2 ###
<!---API needs onboarding. Removed link to API until remedied.--->
- [Functie] [AzureHtml5JS| Firefox] Widevine-weergave inschakelen met EME voor Firefox 47+
- [Functie] Gebeurtenis toevoegen voor het verwijderen van spelers
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Bug fixes 1.7.2 ###

- [Bug Fix] Gecodeerde Akamai CDN URL-queryparameters die niet correct zijn gedecodeerd
- [Bug Fix] Uitzondering wordt gegooid op manifestSpeelbaarVensterLengte()
- [Bug Fix] Viewer kan niet altijd op afspelen op de video klikken nadat de video is beëindigd om opnieuw te bekijken
- [Bug Fix] Responsieve grootte niet in overeenstemming met snelle venstergrootte veranderingen
- [Bug Fix] [Rand| IE] Responsieve maatvoering die niet van kracht wordt op paginabelasting voor width=x, height=auto
- [Bug Fix] [Android| Chrome] Chrome vraagt machtigingen voor het afspelen van DRM-inhoud wanneer inhoud niet is versleuteld
- [Bug Fix] [Toegankelijkheid] [Rand] Toetsenbordbesturingselementen selecteren contextmenu-items niet correct
- [Bug Fix] [Toegankelijkheid] Ontbrekende weergegeven rand in hoog contrastmodus
- [Bug Fix] [Knippert] Mouse up event listener niet verwijderd nadat speler verwijderen oorzaken uitzondering
- [Bug Fix] [Knippert] Url van het ontschepen van manifesten met gecodeerde spaties
- [Bug Fix] [iOS] Typefout bij het evalueren van tech.featuresVolumeControl

### <a name="changes-172"></a>Wijzigingen 1.7.2 ###

- [Wijzigen] [DRM] Drm-controles na setbron verplaatst om alleen te controleren wanneer inhoud is versleuteld
- [Wijzigen] [AES] Verwijderde ongedefinieerde hoeveelheid type/vlakte uit Key delivery request
- [Wijzigen] [Toegankelijkheid] Windows verteller leest nu "Media Player" wanneer de context is op de speler in plaats van eigenschappen

## <a name="171-official-hotfix-update"></a>1.7.1 (officiële Hotfix-update) ##

### <a name="features-171"></a>Functies 1.7.1 ###

- [Functie] Toegevoegde optie voor hybride heuristisch profiel (dit profiel is standaard ingesteld)

### <a name="bug-fixes-171"></a>Bug fixes 1.7.1 ###

- [Bug Fix] Responsief ontwerp werkt niet volgens html5-standaard (width=100%, height=auto)
- [Bug Fix] Percentagewaarden voor breedte en hoogte die zich niet gedragen zoals verwacht in v1.7.0

## <a name="170-official-update"></a>1.7.0 (officiële update) ##

### <a name="features-170"></a>Functies 1.7.0 ###
<!---API needs onboarding. Removed link until remedied.--->
- [Functie] [AzureHtml5JS] [Knippert] CurrentMediaTime() toegevoegd om de encodermediatijd van de huidige tijd binnen enkele seconden te krijgen
- [Functie] [Knippert] Geïmplementeerde downloadtelemetrie-API's met videoBufferData() en audioBufferData()<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- [Functie] [Knippert] Added 'downloadbitratechanged' event
- [Functie] Laadtijd verbeterd in vergelijking met oudere versies van de speler
- [Functie] Fouten worden aangemeld bij JavaScript-console

### <a name="bug-fixes-170"></a>Bug fixes 1.7.0 ###

- [Bug Fix] Gecodeerde poster-URL met querytekenreeksparameters die niet worden weergegeven in de speler
- [Bug Fix] Uitzondering gegooid wanneer er geen tech geladen en API-versterker. Player.poster() wordt genoemd
- [Bug Fix] Uitzondering gegooid wanneer functies proberen om toegang te krijgen tot speler na verwijderd
- [Bug Fix] [Toegankelijkheid] Ontbrekende overzicht op focus op voortgangsbalk zoeken hoofd
- [Bug Fix] [Toegankelijkheid] Contextmenu's hebben een schaduw in de modus met hoog contrast
- [Bug Fix] [iOS] native player WebVTT captions afspelen werkt niet
- [Bug Fix] [AzureHtml5JS] Fout 0x0100002 moet worden weergegeven bij het afspelen van HTTP-stream op HTTPS-site die in plaats daarvan oneindige spinner oplevert als gevolg van gemengde inhoud
- [Bug Fix] [AzureHtml5JS] Ontbrekend eindsegment waardoor de fout van de lusstatuscontrole wordt weergegeven met een waargenomen oneindige bufferstatus
- [Bug Fix] [AzureHtml5JS] Onjuiste naam van audiotrack in menu wanneer manifestforlabel=false wordt gebruikt en drie lettertaalcodes worden gebruikt
- [Bug Fix] [AzureHtml5JS| Chrome] Waargenomen oneindige bufferstatus aan het einde van inhoud veroorzaakt door floating point onnauwkeurigheid in duur met JavaScript in Chrome
- [Bug Fix] [Knippert] Niet-fatale intermitterende fout tijdelijk weergegeven wanneer flash-speler gemaakt
- [Bug Fix] [Knippert] Afspelen mislukt wanneer video- en audiostreams verschillende tijdschalen gebruiken als gevolg van afronding van onnauwkeurigheid die niet werkt met "Fragment url (...) is niet in staat om FLVTags te genereren"
- [Bug Fix] [Knippert] Problemen met het ontschepen van manifesturl's met gecodeerde spaties
- [Bug Fix] [Knippert] Ontbrekende controle om te bepalen of Flash player versie >= 11.4 die een fout in het afspelen veroorzaakt in plaats van terug te vallen naar de volgende tech in de techOrder
- [Bug Fix] [Knippert] [AES] Problemen met het accepteren van AES-tokens met underscores erin
- [Bug Fix] [SilverlightSS| OSX] "/" het vooraf opstellen van een manifest in plaats van het protocol (HTTP of HTTPS) wordt herkend als een lokaal bestand dat oneindige spinner oplevert

### <a name="changes-170"></a>Wijzigingen 1.7.0 ###

- [Wijzigen] [Knippert] Samengevoegde SWF-scripts ("MSAdaptiveStreamingPlugin-osmf2.0.swf" en "StrobeMediaPlayback.2.0.swf") in één SWF genaamd "StrobeMediaPlayback.2.0.swf"
- [Wijzigen] [Knippert] Bijgewerkte foutcode propagatie om preciezere foutcodes te krijgen (ex. 404s nu resulteren in 0x30200194 in plaats van generieke fout 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (officiële Hotfix-update) ##

### <a name="bug-fixes-163"></a>Bug fixes 1.6.3 ###

- [Bug Fix] JavaScript runtime uitzondering wanneer de hotkeys event handler wordt uitgevoerd na het verwijderen van de speler
- [Bug Fix] [Android] [AzureHtml5JS] Geen afspelen op mobiel apparaat met behulp van een mobiel netwerk
- [Bug Fix] Bijgewerkt Forge te draaien als webworker vrij te maken UI

## <a name="162-official-hotfix-update"></a>1.6.2 (officiële Hotfix-update) ##

### <a name="features-162"></a>Functies 1.6.2 ###

- [Functie] Extra talen toegevoegd voor lokalisatie (zie documentatie voor meer details)

### <a name="bug-fixes-162"></a>Bug fixes 1.6.2 ###

- [Bug Fix] [IE9-10] Door te klikken op gebieden rond het browservenster van de speler als gevolg van ie9/IE10-bug die de window.blur() minimaliseert.
- [Bug Fix] [Knippert] Het accepteren van AES-tokens met underscores

## <a name="161-official-hotfix-update"></a>1.6.1 (officiële Hotfix-update) ##

### <a name="bug-fixes-161"></a>Bug fixes 1.6.1 ###

- [Bug Fix] [Flitsen| Edge,IE][SilverlightSS| IE] Kan zich niet concentreren op andere UI-elementen voor ingangen of andere in IE / Edge
- [Bug Fix] AES-weergave mislukt wanneer het niet gedefinieerd wordt
- [Bug Fix] [Android] [AzureHtml5JS| Chrome] Continue spinner speelt geen inhoud af bij health check-lus
- [Bug Fix] [IE9] console.log() die niet wordt ondersteund door IE 9 en uitzondering maakt

## <a name="160-official-update"></a>1.6.0 (officiële update) ##

### <a name="features-160"></a>Functies 1.6.0 ###

- [Feature] 33% vermindering van de grootte van azuremediaplayer.min.js
- [Functie] [AzureHtml5JS| Edge][Ongetest] Ondersteuning voor DD+ audiostreams in Edge (geen codec-schakelen na de eerste keuze). App moet op dit moment de juiste audiostream selecteren.
- [Functie] Hot key besturingselementen (zie documenten voor meer details)
- [Functie] Voortgangstijdtip zweeft voor tijd nauwkeurig zoeken
- [Functie] Toestaan voor synchronisatiedetectie van plug-ins als setupDone-methode bestaat in plugin

### <a name="bug-fixes-160"></a>Bug fixes 1.6.0 ###

- [Bug Fix] Geheugenlogboek niet doorspoelen op getMemoryLog (true)
- [Bug Fix] Bitrate selectievak reset op muis verplaatsen waardoor probleem selecteren van lagere bitrates door middel van muis besturingselement
- [Bug Fix] Mac Office in app loopt vast bij het uitvoeren van DRM-controle
- [Bug Fix] CSS-klassen worden gemakkelijk per ongeluk overschreven
- [Bug Fix] [Chrome] Identificatie bijwerken vanuit de gebruikersagenttekenreeksbrowser is Edge
- [Bug Fix] [AzureHtml5JS] Knop Bijschriften wordt niet weergegeven in de gereedschapsbalk in Edge(Win10) of Chrome(Mac)
- [Bug Fix] [Android] [AzureHtml5JS| Chrome] Ongeldige Uitzondering op endOfStream() oproep op korte video's
- [Bug Fix] [Firefox] Verwijdering van DRM-waarschuwing veroorzaakt door Firefox bij het controleren van browsermogelijkheden
- [Bug Fix] [Html5] Ondertiteling/bijschriften niet weergegeven met progressieve mp4-inhoud
- [Bug Fix] [Knippert] Berichten met overeenkomende tijdstempels zijn in omgekeerde volgorde geregistreerd
- [Bug Fix] [Toegankelijkheid] [Chrome| Firefox] Tab en selecteer besturingselementen selecteren automatisch eerste menu-item
- [Bug Fix] [Toegankelijkheid] Tab om de volumeknop te regelen

### <a name="changes-160"></a>Wijzigingen 1.6.0 ###

- [Wijzigen] AES-decryptietijd gebruiken op selectie op kwaliteitsniveau
- [Wijzigen] URL-rewriter bijwerken om HLS v4 v4 v4 v4 te gebruiken voor multi-audiostreams
- [Wijzigen] NativeControlsForTouch als standaard instellen op false (moet onwaar zijn om correct te werken)

## <a name="150-official-update"></a>1.5.0 (officiële update) ##

### <a name="features-150"></a>Functies 1.5.0 ###

- [Functie] Verbeteringen voor algemene webbeveiliging (preventie van injectie, XSS, enz.)
- [Functie] SDN plugin integratie haken voor sourceset event en options.sdn
- [Functie] Robuustheid smeting van 5XX- en 4XX-fouten tijdens het afspelen

### <a name="bug-fixes-150"></a>Bug fixes 1.5.0 ###

- [Bug Fix] CSS-minification bijwerken om html-entiteitslettertypecodes te gebruiken voor knoppen in plaats van Unicode
- [Bug Fix] [AzureHtml5JS] Multi-DRM-inhoud selecteert altijd het token van het eerste element van protectionInfo waardoor de tweede DRM mislukt
- [Bug Fix] [AzureHtml5JS] Op zoek naar nooit voltooit bij het zoeken in een gebied met ontbrekende segmenten.
- [Bug Fix] [AzureHtml5JS| Edge] EME inschakelen in Edge-update voor PlayReady-weergave
- [Bug Fix] [AzureHtml5JS| Firefox] Update EME check om Firefox v42+ (met MSE) te laten terugvallen naar Silverlight voor beschermde inhoud
- [Bug Fix] [Knippert] Update error.message van getal naar gedetailleerde tekenreeks

### <a name="changes-150"></a>Wijzigingen 1.5.0 ###

- [Wijzigen] Posters werken momenteel alleen als absolute URL's.

## <a name="140-official-update"></a>1.4.0 (officiële update) ##

### <a name="features-140"></a>Functies 1.4.0 ###

- [Functie] [AzureHtml5JS| Ondersteuning voor Chrome] Simple Widevine DRM
- [Functie] [AzureHtml5JS] Robuustheid smeting van 404/412 fouten tijdens het afspelen

### <a name="bug-fixes-140"></a>Bug fixes 1.4.0 ###

- [Bug Fix] [Knippert] Verbetering voor parametervalidatie

## <a name="130-official-update"></a>1.3.0 (officiële update) ##

### <a name="features-130"></a>Functies 1.3.0 ###

- [Functie] [AzureHtml5JS] [Knippert] Audioschakelen van dezelfde codec Multi-Audio-inhoud

### <a name="bug-fixes-130"></a>Bug fixes 1.3.0 ###

- [Bug Fix] [AzureHtml5JS| Chrome] Intermitterende oneindige spinner
- [Bug Fix] [AzureHtml5JS| IE][Windows Phone] Uitzondering waardoor Windows Phone problemen heeft met afspelen
- [Bug Fix] [Knippert] Automatisch afspelen ingesteld op false mislukt voor extra exemplaren
- [Bug Fix] Problemen met het formaat van het uI-menu

## <a name="120-official-update"></a>1.2.0 (officiële update) ##

### <a name="features-120"></a>Functies 1.2.0 ###

- [Functie] [AzureHtml5JS| Firefox] Ondersteuning wanneer MSE is ingeschakeld
- [Functie] Niet langer nodig app om paden voor fallback tech binaries (swf, xap) bieden. Pad is relatief ten opzichte van het Azure Media Player-script.

### <a name="bug-fixes-120"></a>Bug fixes 1.2.0 ###

- [Bug Fix] [AzureHtml5JS| Chrome] Speler drifts achter live edge wanneer speler op de achtergrond
- [Bug Fix] [AzureHtml5JS| Edge] Volledig scherm werkt niet
- [Bug Fix] [AzureHtml5JS] Logboekregistratie is niet goed ingeschakeld wanneer u opties instelt
- [Bug Fix] [Flash] Zowel 'buffering' als buffering-icoon tonen tijdens wachtgebeurtenis
- [Bug Fix] Afspelen laten doorgaan als het oorspronkelijke bandbreedteverzoek mislukt
- [Bug Fix] Player kan niet laden wanneer deze is geïnitialiseerd met ongedefinieerde opties
- [Bug Fix] Bij een poging om de speler te verwijderen nadat deze al is verwijderd, treedt een vdata-uitzondering op
- [Bug Fix] Pictogrammen voor kwaliteitsbalk die verkeerd zijn toegewezen

## <a name="111-official-hotfix-update"></a>1.1.1 (officiële Hotfix-update) ##

### <a name="bug-fixes-111"></a>Bug fixes 1.1.1 ###

- [Bug Fix] Oudere IE full screen probleem
- [Bug Fix] Plug-ins niet langer overschreven

## <a name="110-official-update"></a>1.1.0 (officiële update) ##

### <a name="features-110"></a>Functies 1.1.0 ###

- [Functie] UI-lokalisatietekenreeksen bijwerken

### <a name="bug-fixes-110"></a>Bug fixes 1.1.0 ###

- [Bug Fix] Big Play Button heeft niet genoeg contrast
- [Bug Fix] Visuele tabfocus-indicator
- [Bug Fix] Selecteer nu het menu Bitrate met behulp van de juiste resolutiegegevens
- [Bug Fix] Meer opties menu nu dynamisch formaat
- [Bug Fix] Verschillende ui-problemen

## <a name="100-official-release"></a>1.0.0 (Officiële versie) ##

### <a name="features-100"></a>Functies 1.0.0 ###

- [Functie] Basistoegankelijkheidstests voor tabbediening, focusbediening, schermlezer, gebruikersinterface met hoog contrast
- [Functie] Bijgewerkte gebruikersinterface
- [Functie] Dev-logboekregistratie
- [Functie] API voor het dynamisch instellen van ondertiteling/ondertitelingtracks
- [Functie] Basislokalisatiefuncties
- [Functie] Foutcodeconsolidatie voor alle technici
- [Functie] Nieuwe foutcode voor wanneer plug-ins (zoals Flash of Silverlight) niet zijn geïnstalleerd
- [Functie] [AzureHtml5JS] Geïmplementeerde diagnostische basisgebeurtenissen

### <a name="bug-fixes-100"></a>Bug fixes 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Bug Fix] [AzureHtml5JS] Live afspelen bevriezen op MPD-updates wanneer er kleine onnauwkeurigheden in de tijdstempel
- [Bug Fix] [AzureHtml5JS] Verschillende problemen met afspelen met live opgelost
- [Bug Fix] [AzureHtml5JS] Buffers spoelen wanneer de vaartijd heuristiek is ingeschakeld en ga naar een scherm met een hogere resolutie
- [Bug Fix] [AzureHtml5JS] Chrome toont nu de afgelopen gebeurtenis op de juiste manier. Gekoppeld aan eerder bekend probleem van *Chrome zal niet goed verzenden â&euro;œendedâ&euro;gebeurtenis bij het gebruik van AzureHtml5JS. Er is een probleem in de onderliggende browser.*
- [Bug Fix] [AzureHtml5JS] Uitgeschakeld Safari voor deze technologie om *het afspelen probleem met OSX Yosemite met AzureHtml5JS tech aan te pakken. Er zijn MSE-implementatieproblemen. Tijdelijke mitigatie:&euro;kracht â œflashSSâ&euro;, â&euro;&euro;œsilverlightSSâ als tech order voor deze user agents*
- [Bug Fix] [FlashSS] loadstart geactiveerd nadat er een fout is opgetreden

## <a name="020-beta"></a>0.2.0 (bèta) ##

### <a name="features-020"></a>Functies 0.2.0 ###

- [Functie] Voltooide tests voor PlayReady en AES voor on demand en live - zie compatibiliteitsmatrix
- [Functie] Omgaan met discontinuïteiten
- [Functie] Ondersteuning voor tijdstempels groter dan 2^53
- [Functie] URL-queryparameter blijft aan houden bij de manifestaanvraag
- [Functie] [Niet getest] Ondersteuning `QuickStart` voor `HighQuality` en heuristiek profielen
- [Functie] [Niet getest] Videostream-informatie voor bitrates, breedte en hoogte blootstellen op AzureHtml5JS en FlashSS
- [Functie] [Niet getest] Bitrate selecteren op AzureHtml5JS en FlashSS (zie API-documentatie)

### <a name="bug-fixes-020"></a>Bug fixes 0.2.0 ###

- [Bug Fix] grote afspeelknop nu zichtbaar op WP8.1
- [Bug Fix] heeft meerdere problemen met het afspelen van live opgelost
- De knop Dempen van [Bug Fix] werkt nu op de gebruikersinterface
- [Bug Fix] bijgewerkte ui-laadervaring voor autoplay-modus
- [Bug Fix] AMD loader probleem en definiëren methode conflicten
- [Bug Fix] WP 8.1 Cordova App laden probleem
- [Bug Fix] Beveiligde inhoud query's platform / tech ondersteund ProtectionType om de juiste tech voor het afspelen te selecteren.  Fixes eerder bekend probleem van '_PlayReady content op Chrome (desktop) / Safari 8 (op OSX Yosemite) momenteel niet terugval naar Silverlight speler_'
- [Bug Fix] niet-gevangen uitzondering op WinServer 2012 R2 als gevolg van Media Foundation niet standaard geïnstalleerd op die machine.  Probeer HTML-videotag API's te gebruiken, die niet zijn geïmplementeerd, waardoor er een fout wordt gemaakt. Huidige mitigatie is om die fout te vangen en terug te keren vals in plaats van het gooien van de fout.
- [Bug Fix] altijd de init segment na zoeken of http niet om glitches te voorkomen tijdens het afspelen
- [Bug Fix] schakel het bijhouden van gesimuleerde voortgang en timeupdates uit wanneer er een fout is opgetreden.
- [Bug Fix] verwijder het menu met de rechtermuisknop
- [Bug Fix] Foutbericht [AzureHtml5JS] wordt niet weergegeven wanneer ongeldige tokenset voor PlayReady-inhoud niet wordt weergegeven
- [Bug Fix] [AzureHtml5JS] gaan fullscreen tijdens live afspelen was geen rekening met venstergrootte heuristiek
- [Bug Fix] [Knippert] Verwijderde Strobe Media Player weergegeven berichten, zodat alleen Azure Media Player-berichten worden weergegeven
- [Bug Fix] [SilverlightSS] niet krijgen 'gezocht' evenement wanneer we zoeken na duur of minder dan 0

## <a name="010-beta-release"></a>0.1.0 (bètaversie) ##

Eerste pre-release

## <a name="next-steps"></a>Volgende stappen ##

- [Snel start azure mediaspeler](azure-media-player-quickstart.md)
