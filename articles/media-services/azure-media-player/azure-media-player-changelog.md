---
title: Azure Media Player wijzigingen logboek
description: Azure Media Player wijzigingen logboek.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.openlocfilehash: 15f8a3ac8c2777b3a878de92db495e559f64ad20
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81726548"
---
# <a name="changelog"></a>Wijzigingenlogboek #

## <a name="224-official-update-february-22-2019"></a>2.2.4 (officiële update Februari 22 2019) ##

### <a name="bug-fixes-224"></a>Bug corrigeert 2.2.4 ###

- [Fout oplossing] LEREN Toegankelijkheids Een bereikbaar fantoom tabblad verwijderd wanneer het fout scherm wordt weer gegeven
- [Fout oplossing] LEREN De sneltoets ' voor IE11 en Edge is opgelost
- [Fout oplossing] LEREN Er is een uitzonde ring voor CEA708 bijschriften opgelost
- [Fout oplossing] LEREN Een probleem met het blok keren van video voor de Edge-browser opgelost

### <a name="changes-224"></a>De wijzigingen 2.2.4 ###

- Kunt LEREN Wanneer er een fout optreedt bij het decoderen van een fragment, worden de huidige en diverse fragmenten opnieuw geprobeerd om het afspelen te herstellen
- Kunt LEREN Meer tolerante video-of audio fragmenten van elkaar afnemen

## <a name="223-official-update-january-9-2019"></a>2.2.3 (officiële update januari 9 2019) ##

### <a name="features"></a>Functies ###

- Hulp HLS Het menu audio nummer voor het afspelen van Safari HLS toegevoegd

### <a name="bug-fixes-223"></a>Bug corrigeert 2.2.3 ###

- [Fout oplossing] LEREN Toegankelijkheids Tijdens het afspelen van Live broadcasts kan de knop ' live ' niet worden geselecteerd met behulp van het toetsen bord
- [Fout oplossing] LEREN Gecorrigeerde fout-positieven 0x0400003 fouten vanwege een mislukte MSE-test
- [Fout oplossing] LEREN Er is een probleem opgelost waarbij de video kan worden geblokkeerd bij het starten van een live stream

### <a name="changes-223"></a>Wijzigingen 2.2.3 ###

- Kunt LEREN Meer informatie toegevoegd aan het logboek om betere diagnostische gegevens mogelijk te maken
- Kunt LEREN Als er meer dan één bitrate beschikbaar is op dezelfde scherm resolutie, zijn alle bitsnelheden beschikbaar voor selectie

## <a name="222-official-update"></a>2.2.2 (officiële update) ##

### <a name="bug-fixes-222"></a>Bug corrigeert 2.2.2 ###

- [Fout oplossing] LEREN Wanneer er een tijdelijke netwerk storing optreedt in een speler, stopt deze onmiddellijk met afspelen
- [Fout oplossing] LEREN Toegankelijkheids Het fout bericht venster is niet toegankelijk via het toetsen bord
- [Fout oplossing] LEREN Oneindig kring veld wordt weer gegeven bij het afspelen van alleen audio-assets in plaats van een niet-ondersteunde fout

### <a name="changes-222"></a>Wijzigingen 2.2.2 ###

- Kunt [AMP] gelokaliseerde teken reeksen toegevoegd voor de aankondigings-UI

## <a name="221-official-update"></a>2.2.1 (officiële update) ##

### <a name="features-221"></a>Functies 2.2.1 ###

- Hulp [CMAF] Er is ondersteuning toegevoegd voor HLS CMAF

### <a name="bug-fixes"></a>Opgeloste fouten ###

- [Fout oplossing] [AMP] niet-versleutelde timers in de logica poging tot het uitvoeren van fouten
- [Fout oplossing] LEREN [Firefox] beëindigde gebeurtenis wordt niet geactiveerd op Firefox en Chrome wanneer het Live-programma wordt gestopt
- [Fout oplossing] LEREN Besturings elementen die worden weer gegeven na setsource, zelfs wanneer besturings elementen zijn ingesteld op False in opties voor speler

### <a name="changes"></a>Gewijzigde ###

- Kunt [Live ondertiteling] Gewijzigde API-naam voor CEA bijschriften van 608 tot 708. Zie [CEA708 Captions Settings](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.cea708captionssettings) (Engelstalig) voor meer informatie.-->

## <a name="220-official-release"></a>2.2.0 (officiële versie) ##

### <a name="features-220"></a>2.2.0 functies ###

- Hulp [Azurehtml5JS] Flits [LiveCaptions] CEA 708 ondertiteling ondersteuning in Azurehtml5JS en belicht de technische technologie voor heldere en AES-inhoud.

### <a name="bug-fixes-220"></a>Fout oplossingen 2.2.0 ###

- [Fout oplossing] Flash-versie detectie werkt niet in Chrome/Edge

### <a name="changes-220"></a>2.2.0 wijzigingen ###

- Kunt LEREN Methodiek De naam van het heuristische profiel is gewijzigd van Quick Start naar LowLatency
- Kunt Flits Wijzigen in Flash Player voor versie detectie om het afspelen van AES-inhoud met de nieuwe Adobe Flash-Update in te scha kelen.

## <a name="219-official-hotfix"></a>2.1.9 (officiële hotfix) ##

### <a name="bug-fixes-219"></a>Fout oplossingen 2.1.9 ###

- [Fout oplossing] Galerie Uitzonde ring wanneer Live streams overgaat naar video op aanvraag/Live-archieven

### <a name="changes-219"></a>2.1.9 wijzigingen ###

- Kunt Flits KIEST Gewijzigde Flash-technische logica om sharedbytearrays te gebruiken voor AES-ontsleuteling, omdat Adobe het gebruik niet heeft geblokkeerd op Flash 30. Houd er rekening mee dat afspelen alleen werkt wanneer Adobe een nieuwe versie van Flash implementeert vanwege een fout in V30. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer informatie

## <a name="218-official-update"></a>2.1.8 (officiële update) ##

### <a name="bug-fixes-218"></a>Fout oplossingen 2.1.8 ###

- [Fout oplossing] Kring veld wordt af en toe niet weer gegeven
- [Fout oplossing] De speler wordt niet gedempt wanneer de optie dempen is ingeschakeld
- [Fout oplossing] Volume schuifregelaar wordt weer gegeven wanneer besturings elementen zijn ingesteld op ONWAAR
- [Fout oplossing] Af en toe herhalen wanneer de gebruiker naar de Live Edge gaat
- [Fout oplossing] Firefox Speler genereert af en toe java script-uitzonde ring bij laden
- [Fout oplossing] Toegankelijkheids Knop afspelen/onderbreken/volume focus niet verplaatsen als deze is geselecteerd met toetsenbord besturings elementen
- [Fout oplossing] Lekkage van opgelost geheugen in de speler is verwijderd
- [Fout oplossing] Src () aanroepen na fout in de speler, wordt de bron niet opnieuw ingesteld
- [Fout oplossing] Galerie Het AMP bevindt zich in een constante laad status wanneer de gebruiker op de knop Live klikt nadat de uitzending is beëindigd
- [Fout oplossing] Chrome Speler loopt vast en afspelen mislukt wanneer de browser op de achtergrond is geminimaliseerd.

### <a name="changes-218"></a>2.1.8 wijzigingen ###

- Kunt 0x0600001 fout bijgewerkt om weer te geven wanneer AES-inhoud wordt afgespeeld met Flash 30, omdat deze op dit moment niet wordt ondersteund. Zie [bekende problemen](azure-media-player-known-issues.md) voor meer informatie
- Kunt Er zijn extra nieuwe pogingen toegevoegd voor Live-scenario's wanneer het manifest vraagt naar 404 of lege manifesten retourneert.

## <a name="217-official-update"></a>2.1.7 (officiële update) ##

### <a name="features-217"></a>2.1.7 functies ###

- Hulp [AzureHtml5JS] Configuratie optie toegevoegd voor het leegmaken van verouderde gegevens in de media bron buffer

### <a name="bug-fixes-217"></a>Fout oplossingen 2.1.7 ###

- [Fout oplossing] Toegankelijkheids [Scherm lezer] De lege koptekst verwijderd die is opgenomen in de speler wanneer de titel niet is ingesteld
- [Fout oplossing] [UWA] AMP genereert uitzonde ring bij afspelen in universele Windows-app
- [Fout oplossing] [OSX] setActiveTextTrack () werkt niet in Safari op OSx
- [Fout oplossing] Galerie Klikken op de Live rand na het weglaten en opnieuw initialiseren van de uitzonde ring voor het leveren van spelers
- [Fout oplossing] Huid Huidige tijd afgekapt voor bepaalde activa
- [Fout oplossing] [DRM] is opgenomen om ondersteuning te bieden voor afspelen in browsers die ondersteuning bieden voor meerdere CENC DRM

### <a name="changes-217"></a>2.1.7 wijzigingen ###

- Kunt Voor beelden Toegankelijkheids De taal code is toegevoegd aan alle voor beelden

## <a name="216-official-update"></a>2.1.6 (officiële update) ##

### <a name="bug-fixes-216"></a>Fout oplossingen 2.1.6 ###

- [Fout oplossing] AMP met onjuiste duur voor specifieke Asset weer geven
- [Fout oplossing] [FairPlay-HLS] Fairplay-fouten die niet worden door gegeven aan de gebruikers interface
- [Fout oplossing] Aangepaste heuristische eigenschappen worden genegeerd in AMP 2.1.5.

### <a name="changes-216"></a>2.1.6 wijzigingen ###

- Kunt [FairPlayDRM] De time-out voor de certificaat aanvraag en de licentie aanvraag voor FairPlay is verwijderd om pariteit met PlayReady-en Widevine-implementaties te kunnen blijven gebruiken
- Kunt Diverse heuristische verbeteringen voor het bestrijden van wazige inhoud

### <a name="features-216"></a>2.1.6 functies ###

- Hulp Ondersteuning voor mpd-time-CMAF toegevoegd

## <a name="215-official-hotfix"></a>2.1.5 (officiële hotfix) ##

### <a name="bug-fixes-215"></a>Bug herstelt 2.1.5 ###

- [Fout oplossing] Bijschriften De VTT-opmaak is niet correct weer gegeven door de speler
- [Fout oplossing] Toegankelijkheids Knop Live heeft geen Aria-label

## <a name="214-official-update"></a>2.1.4 (officiële update) ##

### <a name="bug-fixes-214"></a>Fout oplossingen 2.1.4 ###

- [Fout oplossing] Toegankelijkheids Focus Gebruikers kunnen niet op het tabblad klikken om de focus te richten op aangepaste knoppen die aan de rechter kant van de knop volledig scherm op de besturings balk worden geplaatst
- [Fout oplossing] [IE11] [Volume balk] Met tab naar volume pop-up wordt het hele video scherm Flash in IE11 in de modus volledig scherm.
- [Fout oplossing] [Weer gave | Leegmaken] ruimte tussen de controle balk en de pop-up van de volume balk
- [Fout oplossing] LEREN Bijschriften Oude Inge sloten sporen worden niet gewist wanneer de bron wordt gewijzigd op een bestaande speler
- [Fout oplossing] Toegankelijkheids Narrator Scherm lezer leest volume regeling onjuist
- [Fout oplossing] [Knipperend] Gebeurtenis afspelen kan af en toe niet worden gestart vanuit Flash Tech
- [Fout oplossing] LEREN Focus Play/Pause vereist twee klikken wanneer de focus van de speler in de modus volledig scherm wordt weer gegeven
- [Fout oplossing] LEREN Huid Er wordt een onjuiste duur weer gegeven op de voortgangs balk voor een specifiek activum
- [Fout oplossing] Vaandel [Ad Butler] De vaste parser verwerkt geen VAST bestand dat geen voortgangs gebeurtenis heeft
- [Fout oplossing] Sdn [AMP 2.1.1] Probleem opgelost met de ondersteuning van Hive SDN-invoeg toepassingen
- [Fout oplossing] Toegankelijkheids Met Verteller wordt ' Midnight demping button ' gelezen wanneer de gebruiker de focus op de knop volume heeft

### <a name="changes-214"></a>Wijzigingen 2.1.4 ###

- Kunt Toegankelijkheids [Ondersteunende technologie] Knoppen hebben nu Aria-Live-eigenschap om de ervaring met ondersteunende technologie te verbeteren
- Kunt Toegankelijkheids [Volume knop | Verteller] verbeterde toegankelijkheid van de knop volume door de functionaliteit voor tabbladen en de schuif regelaar te wijzigen. Met deze wijzigingen kunnen toetsenbord gebruikers eenvoudiger het volume van de speler wijzigen
- Kunt Verhoogde time-out voor context menu van inactiviteit van 3 tot 5 seconden
- Kunt Toegankelijkheids Sterkte Verbeterde contrast verhouding helderheid in vervolg keuzemenu's in instellingen voor bijschriften

## <a name="213-official-update"></a>2.1.3 (officiële update) ##

### <a name="bug-fixes-213"></a>Bug herstelt 2.1.3 ###

- [Fout oplossing] [Plugins | Titel overlay] de invoeg toepassing voor titel-overlay genereert JS-uitzonde ringen met AMP v2. X +
- [Fout oplossing] De gebeurtenis source set wordt verzonden naar Java script-console, zelfs wanneer logboek registratie is uitgeschakeld
- [Fout oplossing] Huid Tijd tips van de speler worden buiten de context van de speler weer gegeven wanneer u de muis aanwijzer over de balk voor de eind duur houdt
- [Fout oplossing] Toegankelijkheids [Scherm lezer] Verteller leest ' regio oriëntatie punt ' of ' land oriëntatie van de video speler ' wanneer de focus op de speler is gericht op de viewer
- [Fout oplossing] LEREN Kan het overzicht van de speler niet uitschakelen via CSS
- [Fout oplossing] Toegankelijkheids Kan niet op het tabblad de focus op de hele speler weer gegeven wanneer de gebruiker in de modus volledig scherm wordt uitgevoerd
- [Fout oplossing] Huid Galerie De weer gave reageert niet op gelokaliseerde LIVE-tekst in het Japans
- [Fout oplossing] Huid De duur en huidige tijd worden afgekapt wanneer stream > 60 min-[bug fix] [iPhone | Live] Player geeft tekst weer voor huidige tijd/duur in de controle balk
- [Fout oplossing] LEREN Api's voor het aanroepen van de Player heuristiek leveren java script-uitzonde ringen op
- [Fout oplossing] [Systeem eigen Html5 | iOS] Videotag-eigenschap ' playsinline ' wordt niet door gegeven aan speler
- [Fout oplossing] [iOS | iframe] Kan geen fullscreen op iPhone invoeren als de speler is geladen in een IFRAME
- [Fout oplossing] LEREN Methodiek AMP werkt altijd met hybride profiel, ongeacht de opties voor de speler
- [Fout oplossing] [AMP | Win 8.1] treedt op wanneer gehost in een win 8.1-app met een webweergave

### <a name="changes-213"></a>Wijzingen 2.1.3 ###

- Kunt LEREN CDN-eindpunt gegevens toegevoegd aan de gebeurtenis FragmentDownloadComplete
- Kunt LEREN Galerie Verbeterde en geoptimaliseerde live streaming-latentie

## <a name="212-official-hotfix"></a>2.1.2 (officiële hotfix) ##

### <a name="bug-fixes-212"></a>Fout oplossingen 2.1.2 ####

- [Fout oplossing] Toegankelijkheids [Windows Verteller] Met Verteller wordt ' Progress middernacht ' gelezen wanneer de gebruiker context van de voortgangs balk heeft en de huidige tijd 0:00 is
- [Fout oplossing] De grootte van het logo van [Skin] is vastgelegd in Java script-code
- Toegankelijkheids Sneltoetsen Er zijn geen sneltoetsen ingeschakeld wanneer u op de speler klikt.

### <a name="changes-212"></a>Wijzigingen 2.1.2 ####

- Kunt Userenv Manifest-URL registreren wanneer speler het manifest niet kan laden

### <a name="features-212"></a>Functies 2.1.2 ###

- Kunt Nemen Gedaan Verbeterde werk belasting en opstart tijden van spelers

## <a name="211-official-update"></a>2.1.1 (officiële update) ##

### <a name="bug-fixes-211"></a>Oplossingen voor oplossingen 2.1.1 ####

- [Fout oplossing] O's Instellen Autoplay op False resulteert in een oneindig kring veld in Safari voor iOS
- [Fout oplossing] Het zoeken naar een tijd die groter is dan de duur van de inhoud resulteert in een oneindig kring veld
- [Fout oplossing] Voor sneltoetsen zijn meerdere tabbladen nodig om de context van de speler te laten werken
- [Fout oplossing] Video wordt gedurende enkele seconden geblokkeerd na het wijzigen van de grootte van de speler in bepaalde assets
- [Fout oplossing] Oneindig kring veld (nadat de zoek actie is voltooid) wanneer de gebruiker meerdere keren snel zoekt
- [Fout oplossing] De controle balk is niet verborgen tijdens inactiviteit
- [Fout oplossing] Als u een webapp opent die als host fungeert voor AMP, kan de webpagina twee maal worden geladen
- [Fout oplossing] Oneindig tijdens het afspelen van inhoud van bepaalde activa via Flash Tech
- [Fout oplossing] Het menu meer opties wordt niet weer gegeven met invoeg toepassingen van derden
- [Fout oplossing] [Weer gave | Buis] [Live] Er worden twee live-pictogrammen weer gegeven wanneer de speler de Live rand van een programma heeft
- [Fout oplossing] Huid Logo kan niet worden uitgeschakeld
- [Fout oplossing] [DD + inhoud] Doorlopend kring veld wordt weer gegeven voor de activa met Dolby Digital audio track
- [Fout oplossing] Laatste AMP wordt geblokkeerd wanneer tijdens het Livestream wisselen van audio taal sporen
- [Fout oplossing] vaste achtergrond weer geven voor kring veld
- [Fout oplossing] Oneindig kring veld in AES Flash-Token statische voor beelden van problemen met oplossingen

### <a name="changes-211"></a>Wijzigingen 2.1.1 ####

- Kunt Fout code toegevoegd voor Widevine https-vereiste: vanaf Chrome v58 moeten Widevine-inhoud worden geladen/afgespeeld via het protocol `https://` , anders mislukt het afspelen.
- Kunt Het label Aria is toegevoegd voor het laden van de kring veld zodat de ondersteunende technologie kan voorzien in de tekst ' video laden ' wanneer de inhoud wordt geladen  

## <a name="210-official-release"></a>2.1.0 (officiële versie) ##

### <a name="features-210"></a>2.1.0 functies ###

- Hulp [AzureHtml5JS] VOD AD-ondersteuning voor pre-mid-post-Rolls
- Hulp Betaversie [AzureHtml5JS] Live AD-ondersteuning voor pre-mid-post-Rolls
- Hulp Nieuwe weer gave-optie toegevoegd-AMP-leegmaken
- Hulp Verbeterde Aria-labels toegevoegd voor een betere integratie met scherm lezers/ondersteunende technologie
- Hulp Huid In de weer gave worden nu alle pictogrammen en knoppen duidelijk in de modus Hoog contrast weer gegeven

### <a name="bug-fixes-210"></a>Fout oplossingen 2.1.0 ###

- [Fout oplossing] Aantal toegankelijkheids-en UI-correcties
- [Fout oplossing] AMP wordt niet correct geladen in IE9

### <a name="changes-210"></a>2.1.0 wijzigingen ###

- Kunt Gestructureerd DOM-elementen in Media Player voor het werken met advertenties
- Kunt Overgeschakeld van CSS naar SCSS voor weer gave-ontwikkeling
- Kunt Voor beelden Voor beeld voor VOD Ads toegevoegd
- Kunt Voor beelden Voor beeld toegevoegd voor afspeel snelheid
- Kunt Voor beelden Voor beeld voor flush weergave toegevoegd

## <a name="200-beta-release"></a>2.0.0 (bèta versie) ##

- [Wijzigen] bijgewerkt naar VJS5
- hulp Nieuwe vloeistof-API voor een reactie snelheid van een speler toegevoegd
- Hulp Afspeel snelheid
- Kunt Overgeschakeld van CSS naar SCSS voor weer gave

## <a name="183-official-hotfix-update"></a>1.8.3 (officiële hotfix-update) ##

### <a name="bug-fixes-183"></a>Fout oplossingen 1.8.3 ###

- [Fout oplossing] [AzureHtml5JS] Bepaalde activa met een negatieve DTS worden niet afgespeeld in Chrome

## <a name="182-official-hotfix-update"></a>1.8.2 (officiële hotfix-update) ##

### <a name="bug-fixes-182"></a>Fout oplossingen 1.8.2 ###

- [Fout oplossing] [AzureHtml5JS] Hogere audio-bitsnelheden worden niet afgespeeld via AzureHtml5JS

## <a name="181-official-update"></a>1.8.1 (officiële update) ##

### <a name="bug-fixes-181"></a>Fout oplossingen 1.8.1 ###

- [Fout oplossing] O's Bijschriften/ondertitels die niet in de systeem eigen speler worden weer gegeven
- [Fout oplossing] LEREN Url's voor CDN-ondersteunde streaming worden toegevoegd met verificatie tokens die niet worden afgespeeld
- [Fout oplossing] Fairplay FairPlay-fout code ontbreekt technische ID (bits [31-28] van de error code) Raadpleeg de fout codes voor meer informatie
- [Fout oplossing] Safari PlayReady Inhoud van PlayReady in Safari voor oneindige kring veld capaciteit

### <a name="changes-181"></a>1.8.1 wijzigingen ###

- Kunt Html5 Systeem eigen Html5-technische uitgebreide logboeken wijzigen zodat gebeurtenissen van VideoTag worden opgenomen

## <a name="180-official-update"></a>1.8.0 (officiële update) ##

### <a name="features-180"></a>1.8.0 functies ###

- Functies Digital Ondersteuning voor FairPlay toegevoegd (Zie [beveiligde inhoud](azure-media-player-protected-content.md) voor meer informatie)

### <a name="bug-fixes-180"></a>Fout oplossingen 1.8.0 ###

- [Fout oplossing] LEREN Bij een gebruikers zoek actie wordt geen wacht gebeurtenis geactiveerd wanneer het netwerk wordt beperkt
- [Fout oplossing] [Knipperend] Selecteren van kwaliteit in Flash tech genereert uitzonde ring
- [Fout oplossing] LEREN Dynamisch selecteren van de kwaliteit wordt weer gegeven in het context menu
- [Fout oplossing] Huid Het is moeilijk om het laatste menu-item van context menu's te selecteren

### <a name="changes-180"></a>1.8.0 wijzigingen ###

- Kunt Speler bijgewerkt naar huidige Chrome EME-vereisten
- Kunt De standaard techOrder is gewijzigd voor nieuwe tech-html5FairPlayHLS (Zie [beveiligde inhoud](azure-media-player-protected-content.md) voor meer informatie)
- Kunt [AzureHtml5JS] Ingeschakelde MPEG-Dash Play in Safari
- Kunt Voor beelden Gewijzigde multi-DRM-voor beelden voor FairPlay

## <a name="174-official-hotfix-update"></a>1.7.4 (officiële hotfix-update) ##

### <a name="bug-fixes-174"></a>Fout oplossingen 1.7.4 ###

- [Fout oplossing] Chrome Er wordt een blauwe contour weer gegeven rond de zoek greep wanneer de gebruiker context van de speler heeft
- [Fout oplossing] IE9 Java script-uitzonde ring opgetreden bij het laden van de speler in IE9

## <a name="173-official-hotfix-update"></a>1.7.3 (officiële hotfix-update) ##

### <a name="bug-fixes-173"></a>Fout oplossingen 1.7.3 ###

- [Fout oplossing] [AzureHtml5JS] Time-out voor speler in beperkte netwerken

### <a name="changes-173"></a>1.7.3 wijzigingen ###

- Kunt Webcrypto inschakelen voor Edge voor het ontsleutelen van AES-inhoud
- Kunt De AMP-heuristiek optimaliseren voor het account voor segmenten in de cache
- Kunt [AzureHtml5JS] Optimaliseer de heuristiek door de latentie van de bandbreedte schatting te verlagen

## <a name="172-official-hotfix-update"></a>1.7.2 (officiële hotfix-update) ##

### <a name="features-172"></a>1.7.2 functies ###
<!---API needs onboarding. Removed link to API until remedied.--->
- Hulp [AzureHtml5JS | Firefox] Widevine afspelen inschakelen met EME voor Firefox 47 +
- Hulp Gebeurtenis toevoegen voor het afstoten van de speler
<!-- ([disposing](index.html#static-amp.eventname.disposing)) -->

### <a name="bug-fixes-172"></a>Fout oplossingen 1.7.2 ###

- [Fout oplossing] De para meters van de versleutelde Akamai CDN URL zijn niet juist gedecodeerd
- [Fout oplossing] Er is een uitzonde ring opgetreden in manifestPlayableWindowLength ()
- [Fout oplossing] Viewer kan niet altijd klikken op afspelen op de video nadat de video is gestopt om opnieuw te kijken
- [Fout oplossing] Verg Roten/verkleinen is niet conform de wijzigingen in de snelle venster grootte
- [Fout oplossing] [EDGE | IE] reactie grootte die niet van kracht wordt op het laden van pagina's voor breedte = x, hoogte = auto
- [Fout oplossing] [Android | Chrome] Chrome om machtigingen te vragen voor het afspelen van DRM-inhoud wanneer inhoud niet is versleuteld
- [Fout oplossing] Toegankelijkheids Edge Besturings elementen voor het toetsen bord selecteren geen juiste items in het context menu
- [Fout oplossing] Toegankelijkheids De weer gegeven rand in de modus Hoog contrast ontbreekt
- [Fout oplossing] [Knipperend] De gebeurtenis-listener voor de muis is niet verwijderd nadat het verwijderen van de speler uitzonde ring veroorzaakt
- [Fout oplossing] [Knipperend] Certificaat voor het parseren van manifest-URL met gecodeerde ruimten oplossen
- [Fout oplossing] O's Type fout bij het evalueren van tech. featuresVolumeControl

### <a name="changes-172"></a>1.7.2 wijzigingen ###

- Kunt Digital De DRM-controles zijn verplaatst na de ingestelde bron om alleen te controleren wanneer inhoud is versleuteld
- Kunt KIEST Niet-gedefinieerde hoofd tekst van het type/onbewerkte bericht van de sleutel leverings aanvraag is verwijderd
- Kunt Toegankelijkheids Windows Verteller leest nu ' Media Player ' wanneer context is op speler in plaats van eigenschappen

## <a name="171-official-hotfix-update"></a>1.7.1 (officiële hotfix-update) ##

### <a name="features-171"></a>1.7.1 functies ###

- Hulp Er is een optie toegevoegd voor het hybride heuristische Profiel (dit profiel is standaard ingesteld)

### <a name="bug-fixes-171"></a>Fout oplossingen 1.7.1 ###

- [Fout oplossing] Een responsief ontwerp werkt niet zoals per HTML5-standaard (breedte = 100%, hoogte = automatisch)
- [Fout oplossing] Percentage waarden voor de breedte en hoogte werken niet zoals verwacht in v 1.7.0

## <a name="170-official-update"></a>1.7.0 (officiële update) ##

### <a name="features-170"></a>1.7.0 functies ###
<!---API needs onboarding. Removed link until remedied.--->
- Hulp [AzureHtml5JS] [Knipperend] CurrentMediaTime () is toegevoegd om de encoder media tijd van de huidige tijd in seconden op te halen
- Hulp [Knipperend] Het downloaden van telemetrie-Api's met videoBufferData () en audioBufferData () is geïmplementeerd<!-- (see [BufferData](index.html#amp.bufferdata) for more details) -->
- Hulp [Knipperend] De gebeurtenis downloadbitratechanged is toegevoegd
- Hulp De laad tijd is verbeterd vergeleken met oudere versies van de speler
- Hulp Fouten worden geregistreerd in Java script-console

### <a name="bug-fixes-170"></a>Fout oplossingen 1.7.0 ###

- [Fout oplossing] Gecodeerde post-URL met query teken reeks parameters die niet worden weer gegeven in de speler
- [Fout oplossing] Er is een uitzonde ring opgetreden bij een technisch geladen en API amp. Player. poster () wordt aangeroepen
- [Fout oplossing] Uitzonde ring opgetreden bij het openen van de speler na verwijdering
- [Fout oplossing] Toegankelijkheids Overzicht ontbreekt bij focus op voortgangs balk
- [Fout oplossing] Toegankelijkheids Context menu's hebben een schaduw in de modus Hoog contrast
- [Fout oplossing] [iOS] systeem eigen speler WebVTT bijschriften niet werken
- [Fout oplossing] [AzureHtml5JS] Fout 0x0100002 moet worden weer gegeven bij het afspelen van een HTTP-stroom op een HTTPS-site die in plaats daarvan oneindig kring veld levert als resultaat van gemengde inhoud
- [Fout oplossing] [AzureHtml5JS] Ontbrekend eind segment waardoor een fout met de status van een lus wordt weer gegeven met een oneindige buffer status
- [Fout oplossing] [AzureHtml5JS] Onjuiste naam voor het audio spoor in het menu wanneer useManifestForLabel = False en taal codes van drie letters worden gebruikt
- [Fout oplossing] [AzureHtml5JS | Chrome] Er is een oneindige buffer status waargenomen aan het einde van de inhoud, veroorzaakt door drijvende-komma onnauw keurigheid in de duur van Java script in Chrome
- [Fout oplossing] [Knipperend] Niet-fatale terugkerende fout wordt tijdelijk weer gegeven wanneer Flash Player is gemaakt
- [Fout oplossing] [Knipperend] Afspelen mislukt wanneer video-en audio gegevensstromen verschillende tijd schalen gebruiken als gevolg van het afronden van de fout ' fragment-URL (...) kan FLVTags niet genereren
- [Fout oplossing] [Knipperend] Problemen met het parseren van manifest-url's met gecodeerde ruimten
- [Fout oplossing] [Knipperend] Er ontbreekt een controle om te bepalen of de Flash Player-versie >= 11,4, waardoor er een fout wordt weer gegeven in plaats van terugvallen op de volgende tech in de techOrder
- [Fout oplossing] [Knipperend] KIEST Problemen met het accepteren van AES-tokens met onderstrepings tekens
- [Fout oplossing] [Silverlighty | OSX] "//" het voor voegsel van een manifest in plaats van het Protocol (HTTP of HTTPS) wordt herkend als een lokaal bestand dat een oneindig kring veld oplevert

### <a name="changes-170"></a>1.7.0 wijzigingen ###

- Kunt [Knipperend] Samengevoegde SWF-scripts (' MSAdaptiveStreamingPlugin-osmf 2.0. swf ' en ' StrobeMediaPlayback. 2.0. swf ') in één SWF met de naam ' StrobeMediaPlayback. 2.0. swf '
- Kunt [Knipperend] De fout code is bijgewerkt, zodat er meer precieze fout codes kunnen worden weer gegeven (bijvoorbeeld 404s result nu in 0x30200194 in plaats van algemene fout 0x30200000)

## <a name="163-official-hotfix-update"></a>1.6.3 (officiële hotfix-update) ##

### <a name="bug-fixes-163"></a>Fout oplossingen 1.6.3 ###

- [Fout oplossing] Java Script runtime-uitzonde ring bij het uitvoeren van de gebeurtenis-handler voor sneltoetsen na het afstoten van de speler
- [Fout oplossing] Android [AzureHtml5JS] Geen afspelen op mobiel apparaat met mobiel netwerk
- [Fout oplossing] Bijgewerkt om te worden uitgevoerd als webworker om gebruikers interface vrij te maken

## <a name="162-official-hotfix-update"></a>1.6.2 (officiële hotfix-update) ##

### <a name="features-162"></a>1.6.2 functies ###

- Hulp Extra talen toegevoegd voor lokalisatie (Zie de documentatie voor meer informatie)

### <a name="bug-fixes-162"></a>Fout oplossingen 1.6.2 ###

- [Fout oplossing] [IE9-10] Klikken op gebieden rond het geminimaliseerde browser venster vanwege een IE9/IE10-fout die minimaliseert op Window. blur ()
- [Fout oplossing] [Knipperend] Geen AES-tokens accepteren met onderstrepings tekens

## <a name="161-official-hotfix-update"></a>1.6.1 (officiële hotfix-update) ##

### <a name="bug-fixes-161"></a>Fout oplossingen 1.6.1 ###

- [Fout oplossing] [Knipperend | Edge, IE] [Silverlighty | IE] kan geen focus krijgen op andere elementen van de gebruikers interface voor invoer in IE/Edge
- [Fout oplossing] AES-afspelen mislukt als vervalsing niet gedefinieerd
- [Fout oplossing] Android [AzureHtml5JS | Chrome] doorlopend kring veld speelt geen inhoud af bij een status controle lus
- [Fout oplossing] [IE9] console. log () wordt niet ondersteund door IE 9, waardoor uitzonde ring wordt veroorzaakt

## <a name="160-official-update"></a>1.6.0 (officiële update) ##

### <a name="features-160"></a>1.6.0 functies ###

- [Functie] 33% grootte reductie van azuremediaplayer. min. js
- Hulp [AzureHtml5JS | Edge] [niet-geteste] ondersteuning voor DD + audio-streams in Edge (geen codec-switches na de eerste keuze). De app moet op dit moment de juiste audio stroom selecteren.
- Hulp Besturings elementen voor sneltoetsen (Zie docs voor meer informatie)
- Hulp Tip voor voortgangs tijd voor tijd nauw keurig zoeken
- Hulp Asynchrone detectie van invoeg toepassingen toestaan als de methode setupDone in de invoeg toepassing bestaat

### <a name="bug-fixes-160"></a>Fout oplossingen 1.6.0 ###

- [Fout oplossing] Het geheugen logboek wordt niet leeg gemaakt op getMemoryLog (true)
- [Fout oplossing] Selectie vakje voor bitrate wordt opnieuw ingesteld bij muis verplaatsen, waardoor het probleem wordt veroorzaakt door lagere bitsnelheden te selecteren via muis
- [Fout oplossing] Mac Office in app loopt vast bij het uitvoeren van DRM-controle
- [Fout oplossing] CSS-klassen kunnen per ongeluk worden overschreven
- [Fout oplossing] Chrome Het bijwerken van de id van de teken reeks browser van de agent is Edge
- [Fout oplossing] [AzureHtml5JS] De knop bijschriften wordt niet weer gegeven in de werk balk in Edge (Win10) of Chrome (Mac)
- [Fout oplossing] Android [AzureHtml5JS | Chrome] InvalidStateError-uitzonde ring op endOfStream ()-aanroep op korte Video's
- [Fout oplossing] Firefox Verwijderen van DRM-waarschuwing, veroorzaakt door Firefox bij het controleren van browser mogelijkheden
- [Fout oplossing] Html5 Ondertitel/bijschriften die niet worden weer gegeven met progressieve MP4-inhoud
- [Fout oplossing] [Knipperend] Berichten met overeenkomende tijds tempels zijn in omgekeerde volg orde geregistreerd
- [Fout oplossing] Toegankelijkheids [Chrome | Het tabblad Firefox] en selecteer besturings elementen automatisch eerste menu-item selecteren
- [Fout oplossing] Toegankelijkheids Tabblad om de knop volume te best uren

### <a name="changes-160"></a>1.6.0 wijzigingen ###

- Kunt AES-ontsleutelings tijd gebruiken op kwaliteits niveau selecteren
- Kunt URL ReWriter bijwerken voor het gebruik van HLS v4 vóór HLS v3 voor multi-audio-streams
- Kunt Stel nativeControlsForTouch in op False als standaard (moet onwaar zijn om correct te werken)

## <a name="150-official-update"></a>1.5.0 (officiële update) ##

### <a name="features-150"></a>1.5.0 functies ###

- Hulp Verbeteringen voor algemene webbeveiliging (ter voorkoming van injectie, XSS, enz.)
- Hulp Integratie hooks van de SDN-invoeg toepassing voor de bronset-gebeurtenis en opties. Sdn
- Hulp Robuuste verwerking van 5XX-en 4XX-fouten tijdens afspelen

### <a name="bug-fixes-150"></a>Fout oplossingen 1.5.0 ###

- [Fout oplossing] CSS-minificatie bijwerken om HTML-entiteit lettertype codes voor knoppen te gebruiken in plaats van Unicode
- [Fout oplossing] [AzureHtml5JS] Multi-DRM-inhoud selecteert altijd het token van het eerste element van protectionInfo waardoor de tweede DRM niet kan worden uitgevoerd
- [Fout oplossing] [AzureHtml5JS] Zoek bewerkingen worden nooit voltooid wanneer ze in een gebied met ontbrekende segmenten zoeken.
- [Fout oplossing] [AzureHtml5JS | Edge] voor het inschakelen van de rand van de EME voor het afspelen van PlayReady
- [Fout oplossing] [AzureHtml5JS | Firefox] EME-controle bijwerken om Firefox V42 + (met MSE) toe te staan om te terugval op Silverlight voor beveiligde inhoud
- [Fout oplossing] [Knipperend] Update fout. bericht van nummer naar gedetailleerde teken reeks

### <a name="changes-150"></a>1.5.0 wijzigingen ###

- Kunt Posters werken momenteel alleen als absolute Url's.

## <a name="140-official-update"></a>1.4.0 (officiële update) ##

### <a name="features-140"></a>1.4.0 functies ###

- Hulp [AzureHtml5JS | Chrome] eenvoudige Widevine DRM-ondersteuning
- Hulp [AzureHtml5JS] Robuuste verwerking van 404/412 fouten tijdens afspelen

### <a name="bug-fixes-140"></a>Fout oplossingen 1.4.0 ###

- [Fout oplossing] [Knipperend] Uitbrei ding voor parameter validatie

## <a name="130-official-update"></a>1.3.0 (officiële update) ##

### <a name="features-130"></a>1.3.0 functies ###

- Hulp [AzureHtml5JS] [Knipperend] Audio switching van dezelfde codec multi-audio-inhoud

### <a name="bug-fixes-130"></a>Fout oplossingen 1.3.0 ###

- [Fout oplossing] [AzureHtml5JS | Chrome] oneindig afwisselend kring veld
- [Fout oplossing] [AzureHtml5JS | IE] [Windows Phone] uitzonde ring waardoor Windows Phone afspeel problemen ondervindt
- [Fout oplossing] [Knipperend] AutoPlay is ingesteld op False voor extra exemplaren
- [Fout oplossing] Problemen met de grootte van UI-menu's

## <a name="120-official-update"></a>1.2.0 (officiële update) ##

### <a name="features-120"></a>1.2.0 functies ###

- Hulp [AzureHtml5JS | Firefox] ondersteuning wanneer MSE is ingeschakeld
- Hulp Het is niet langer nodig dat de app paden opgeeft voor binaire terugval bestanden (SWF, XAP). Het pad is relatief ten opzichte van het Azure Media Player script.

### <a name="bug-fixes-120"></a>Fout oplossingen 1.2.0 ###

- [Fout oplossing] [AzureHtml5JS | Chrome] Player wordt achter de Live rand geplaatst wanneer de speler op de achtergrond
- [Fout oplossing] [AzureHtml5JS | Edge] het volledige scherm werkt niet
- [Fout oplossing] [AzureHtml5JS] Logboek registratie is niet correct ingeschakeld bij het instellen in opties
- [Fout oplossing] Flits Pictogram voor buffering en buffering weer geven tijdens wachtende gebeurtenis
- [Fout oplossing] Afspelen toestaan om door te gaan als de eerste bandbreedte aanvraag mislukt
- [Fout oplossing] De speler kan niet worden geladen bij het initialiseren met niet-gedefinieerde opties
- [Fout oplossing] Wanneer u de Player probeert te verwijderen nadat deze al is verwijderd, treedt er een vdata-uitzonde ring op
- [Fout oplossing] De pictogrammen van de kwaliteits balk zijn onjuist toegewezen

## <a name="111-official-hotfix-update"></a>1.1.1 (officiële hotfix-update) ##

### <a name="bug-fixes-111"></a>Fout oplossingen 1.1.1 ###

- [Fout oplossing] Probleem met verouderd scherm van Internet Explorer
- [Fout oplossing] Invoeg toepassingen worden niet meer overschreven

## <a name="110-official-update"></a>1.1.0 (officiële update) ##

### <a name="features-110"></a>1.1.0 functies ###

- Hulp Gelokaliseerde teken reeksen van gebruikers interface bijwerken

### <a name="bug-fixes-110"></a>Fout oplossingen 1.1.0 ###

- [Fout oplossing] Er is onvoldoende contrast op de knop Big Play
- [Fout oplossing] Focus indicator van visueel element
- [Fout oplossing] Selecteer het menu bitrate nu met de juiste oplossings gegevens
- [Fout oplossing] Meer opties menu nu dynamisch formaat
- [Fout oplossing] Diverse problemen met de gebruikers interface

## <a name="100-official-release"></a>1.0.0 (officiële versie) ##

### <a name="features-100"></a>1.0.0 functies ###

- Hulp Basis toegankelijkheids tests voor tabblad besturings element, focus beheer, scherm lezer, gebruikers interface met hoog contrast
- Hulp Bijgewerkte gebruikers interface
- Hulp Ontwikkelaar logboek registratie
- Hulp API voor het dynamisch instellen van bijschriften/ondertitels sporen
- Hulp Basis functies voor lokalisatie
- Hulp Fout code consolidatie over techers
- Hulp Nieuwe fout code voor wanneer invoeg toepassingen (zoals Flash of Silverlight) niet zijn geïnstalleerd
- Hulp [AzureHtml5JS] Geïmplementeerde basis gebeurtenissen voor diagnostische gegevens

### <a name="bug-fixes-100"></a>Fout oplossingen 1.0.0 ###
<!---What is that actually supposed to say?--->
- [Fout oplossing] [AzureHtml5JS] Live afspelen bevriezen op MPD-updates wanneer er kleine onnauws in de tijds tempel zijn
- [Fout oplossing] [AzureHtml5JS] Meerdere problemen met live afspelen
- [Fout oplossing] [AzureHtml5JS] Flush buffers wanneer de heuristiek van de venster grootte is ingeschakeld en naar een scherm met een hogere resolutie gaat
- [Fout oplossing] [AzureHtml5JS] Chrome toont nu de beëindigde gebeurtenis. Gekoppeld aan het voor gaande bekende probleem van *Chrome verzendt niet correct&euro;œendedâ&euro;-gebeurtenis wanneer AzureHtml5JS wordt gebruikt. Er is een probleem in de onderliggende browser.*
- [Fout oplossing] [AzureHtml5JS] Safari is uitgeschakeld voor deze technische, om het probleem op te lossen *met OSX Yosemite met AzureHtml5JS tech. Er zijn problemen met de implementatie van MSE. Tijdelijke beperking: dwing&euro;â œflashSSâ&euro;, â&euro;œsilverlightSSâ&euro;als technische volg orde voor deze gebruikers agenten*
- [Fout oplossing] [Knipperend] loadstart gestart nadat de fout is opgetreden

## <a name="020-beta"></a>0.2.0 (bèta) ##

### <a name="features-020"></a>0.2.0 functies ###

- Hulp Voltooide tests voor PlayReady en AES voor on-demand en live-Zie compatibiliteits matrix
- Hulp Verwerkingen afhandelen
- Hulp Ondersteuning voor tijds tempels die groter zijn dan 2 ^ 53
- Hulp URL-query parameter blijft in de manifest-aanvraag
- Hulp [Niet getest] Ondersteuning voor `QuickStart` en `HighQuality` heuristische profielen
- Hulp [Niet getest] Video gegevensstroom informatie weer geven voor de bitsnelheid, breedte en hoogte van AzureHtml5JS en de flits
- Hulp [Niet getest] Selecteer de bitsnelheid op AzureHtml5JS en de flits (Zie API-documentatie)

### <a name="bug-fixes-020"></a>Fout oplossingen 0.2.0 ###

- [Bug fix] de knop voor het afspelen van grote hoeveel heden is nu zichtbaar op WP 8.1
- [Bug fix] problemen met meerdere live afspelen opgelost
- [Bug fix] de knop uitschakelen nu in de gebruikers interface werkt
- [Bug fix] bijgewerkte gebruikers interface voor het laden van de modus voor automatisch afspelen
- [Fout oplossing] Probleem met AMD loader en het definiëren van methode conflicten
- [Fout oplossing] Probleem met het laden van WP 8,1 Cordova-apps
- [Fout oplossing] Beveiligde inhouds query's platform/tech ondersteund beveiligings type hebben om de juiste technologie te selecteren voor afspelen.  Hiermee wordt het eerder bekende probleem van '_PlayReady-inhoud op Chrome (Desktop)/Safari 8 (op OSX Yosemite) momenteel niet teruggevallen op Silverlight-speler_'
- [Bug fix] niet-onderschepte uitzonde ring op WinServer 2012 R2, omdat Media Foundation standaard niet op die machine is geïnstalleerd.  Probeer HTML-video Tags Api's te gebruiken die niet zijn geïmplementeerd, waardoor er een fout optreedt. De huidige beperking is om deze fout te ondervangen en ONWAAR te retour neren in plaats van de fout te activeren.
- [Bug fix] het init-segment altijd ophalen na een zoek-of HTTP-fout om fouten tijdens het afspelen te voor komen
- [Bug fix] het bijhouden van gesimuleerde voortgang en timeupdates uitschakelen wanneer er een fout is opgetreden.
- [Fout correctie] Klik met de rechter muisknop op het menu verwijderen
- [Fout oplossing] [AzureHtml5JS] fout bericht wordt niet weer gegeven als ongeldig token is ingesteld voor PlayReady-inhoud
- [Fout oplossing] [AzureHtml5JS] tijdens het afspelen op volledig scherm is er geen heuristiek van de venster grootte in het account opgenomen
- [Fout oplossing] [Knipperend] Verwijderde stroboscopisch Media Player berichten weer gegeven zodat alleen Azure Media Player berichten worden weer gegeven
- [Fout oplossing] [Silverlight] de ' gezochte ' gebeurtenis niet ophalen wanneer wij de duur van de zoek opdracht overschrijden of minder dan 0

## <a name="010-beta-release"></a>0.1.0 (bèta versie) ##

Initiële voorlopige versie

## <a name="next-steps"></a>Volgende stappen ##

- [Snelstartgids Azure Media Player](azure-media-player-quickstart.md)
