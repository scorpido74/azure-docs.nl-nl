---
title: Advertenties invoegen aan de clientzijde | Microsoft Documenten
description: In dit artikel wordt uitgelegd hoe u advertenties in uw media invoegen aan de clientzijde.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 65c9c747-128e-497e-afe0-3f92d2bf7972
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 274ee09ae98dd229b255e58261f462e322be9f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77565737"
---
# <a name="inserting-ads-on-the-client-side"></a>Advertenties invoegen aan de clientzijde
Dit artikel bevat informatie over het invoegen van verschillende soorten advertenties aan de clientzijde.

Zie [Standaarden voor ondertiteling en advertentie-ondersteuning](media-services-live-streaming-with-onprem-encoders.md#cc_and_ads)in live streamingvideo's voor informatie over ondertiteling en advertentieondersteuning .

> [!NOTE]
> Azure Media Player biedt momenteel geen ondersteuning voor Advertenties.
> 
> 

## <a name="inserting-ads-into-your-media"></a><a id="insert_ads_into_media"></a>Advertenties invoegen in uw media
Azure Media Services biedt ondersteuning voor het invoegen van advertenties via het Windows Media Platform: Player Frameworks. Spelersframeworks met advertentieondersteuning zijn beschikbaar voor Windows 8-, Silverlight-, Windows Phone 8- en iOS-apparaten. Elk spelerskader bevat voorbeeldcode die je laat zien hoe je een spelertoepassing implementeert. Er zijn drie verschillende soorten advertenties die u invoegen in uw media:list.

* **Lineair** – full frame-advertenties die de hoofdvideo onderbreken.
* **Niet-lineaire** - overlay advertenties die worden weergegeven als de belangrijkste video wordt afgespeeld, meestal een logo of andere statische afbeelding geplaatst in de speler.
* **Companion** – advertenties die buiten de speler worden weergegeven.

Advertenties kunnen op elk moment in de tijdlijn van de hoofdvideo worden geplaatst. Je moet de speler vertellen wanneer hij de advertentie moet afspelen en welke advertenties moet worden afgespeeld. Dit gebeurt met behulp van een set standaard XML-gebaseerde bestanden: Video Ad Service Template (VAST), Digital Video Multiple Ad Playlist (VMAP), Media Abstract Sequencing Template (MAST) en Digital Video Player Ad Interface Definition (VPAID). ENORME bestanden geven aan welke advertenties moeten worden weergegeven. VMAP-bestanden geven aan wanneer verschillende advertenties moeten worden afgespeeld en bevatten VAST XML. MAST-bestanden zijn een andere manier om advertenties te sequencen die ook VAST XML kunnen bevatten. VPAID-bestanden definiëren een interface tussen de videospeler en de advertentie- of advertentieserver.

Elke speler kader werkt anders en elk zal worden behandeld in zijn eigen artikel. In dit artikel worden de basismechanismen beschreven die worden gebruikt om advertenties in te voegen. Toepassingen voor videospelers vragen advertenties aan bij een advertentieserver. De advertentieserver kan op verschillende manieren reageren:

* Een VAST-bestand retourneren
* Een VMAP-bestand retourneren (met ingesloten VAST)
* Een MAST-bestand retourneren (met ingesloten VAST)
* Een VAST-bestand retourneren met VPAID-advertenties

### <a name="using-a-video-ad-service-template-vast-file"></a>Een VAST-bestand (Video Ad Service Template) gebruiken
Een VAST-bestand geeft aan welke advertenties of advertenties moeten worden weergegeven. De volgende XML is een voorbeeld van een VAST-bestand voor een lineaire advertentie:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="115571748">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://www.myserver.com/tracking-resource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <TrackingEvents>
                  <Tracking event="start"><![CDATA[http://www.myserver.com/start-tracking-resource]]></Tracking>
                  <Tracking event="midpoint"><![CDATA[http://www.myserver.com/midpoint-tracking-resource]]></Tracking>
                  <Tracking event="complete"><![CDATA http://www.myserver.com/complete-tracking-resource]]></Tracking>
                  <Tracking event="expand"><![CDATA[http://www.myserver.com/expand-tracking-resource]]></Tracking>
                </TrackingEvents>
                <VideoClicks>
                  <ClickThrough id="Atlas Redirect"><![CDATA[http://www.myserver.com/click-resource]]></ClickThrough>
                  <ClickTracking id="Spare"></ClickTracking>
                </VideoClicks>
                <MediaFiles>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_200_4x3.wmv]]>
                  </MediaFile>
                  <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                    <![CDATA[http://www.myserver.com/media/myad_300_4x3.wmv]]>
                  </MediaFile>
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
          <Extensions>
            <Extension type="Atlas">
            </Extension>
          </Extensions>
        </InLine>
      </Ad>
    </VAST>
```

De lineaire advertentie wordt beschreven door het element <**Linear**>. Het geeft de duur van de advertentie, het bijhouden van gebeurtenissen, klik door, klik tracking, en een aantal **MediaFile** elementen. Trackinggebeurtenissen worden opgegeven in de <**TrackingEvents**> element en laat een advertentieserver verschillende gebeurtenissen bijhouden die plaatsvinden tijdens het bekijken van de advertentie. In dit geval worden de gebeurtenissen start, middelpunt, voltooiing en uitvouwing bijgehouden. De begingebeurtenis vindt plaats wanneer de advertentie wordt weergegeven. De gebeurtenis in het middelpunt treedt op wanneer ten minste 50% van de tijdlijn van de advertentie is bekeken. De volledige gebeurtenis vindt plaats wanneer de advertentie tot het einde is uitgevoerd. De gebeurtenis Uitvouwen vindt plaats wanneer de gebruiker de videospeler uitbreidt naar volledig scherm. Klikthroughs worden opgegeven met een <**ClickThrough**> element binnen een <**VideoClicks**> element en geeft een URI op aan een resource om weer te geven wanneer de gebruiker op de advertentie klikt. ClickTracking is opgegeven in een <**ClickTracking**> element, ook binnen het <**VideoClicks**> element en geeft een trackingbron op die de speler kan aanvragen wanneer de gebruiker op de advertentie klikt. De **MediaFile** <MediaFile->-elementen geven informatie op over een specifieke codering van een advertentie. Wanneer er meer dan één <**MediaFile**> element is, kan de videospeler de beste codering voor het platform kiezen.

Lineaire advertenties kunnen in een bepaalde volgorde worden weergegeven. Voeg hiervoor extra `<Ad>` elementen toe aan het VAST-bestand en geeft de volgorde op met behulp van het kenmerk sequentie. Het volgende voorbeeld illustreert dit:

```xml
    <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
      <Ad id="1" sequence="0">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad1trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:32</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
      <Ad id="2" sequence="1">
        <InLine>
          <AdSystem version="2.0 alpha">Atlas</AdSystem>
          <AdTitle>Unknown</AdTitle>
          <Description>Unknown</Description>
          <Survey></Survey>
          <Error></Error>
          <Impression id="Atlas"><![CDATA[http://myserver.com/Impression/Ad2trackingResource]]></Impression>
          <Creatives>
            <Creative id="video" sequence="0" AdID="">
              <Linear>
                <Duration>00:00:30</Duration>
                <MediaFiles>
                  <!-- ... -->
                </MediaFiles>
              </Linear>
            </Creative>
          </Creatives>
        </InLine>
      </Ad>
    </VAST>
```

Niet-lineaire advertenties worden `<Creative>` ook in een element gespecificeerd. In het volgende `<Creative>` voorbeeld wordt een element weergegeven dat een niet-lineaire advertentie beschrijft.

```xml
    <Creative id="video" sequence="1" AdID="">
      <NonLinearAds>
        <NonLinear width="216" height="121" minSuggestedDuration="00:00:15">
          <StaticResource creativeType="image/png"><![CDATA[http://myserver/images/image.png]]></StaticResource>
          <StaticResource creativeType="image/jpg"><![CDATA[http://myserver/images/image.jpg]]></StaticResource>
        </NonLinear>
        <TrackingEvents>
             <Tracking event="acceptInvitation"><![CDATA[http://myserver/tracking/trackingID]></Tracking>
             <Tracking event="collapse"><![CDATA[http://myserver/tracking/trackingID2]]></Tracking>
         </TrackingEvents>
       </NonLinearAds>
    </Creative>
```

Het> element <**NonLinearAds** kan een of meer <**niet-lineaire**>-elementen bevatten, die elk een niet-lineaire advertentie kunnen beschrijven. Het element <**niet-lineaire**> geeft de bron voor de niet-lineaire advertentie op. De bron kan een <**StaticResource->,** een <**IFrameResource->** of een <**HTMLResource->** zijn. \<**StaticResource**> beschrijft een niet-HTML-bron en definieert een kenmerk creativeType dat aangeeft hoe de resource wordt weergegeven:

Image/gif, image/jpeg, image/png – de bron wordt weergegeven in een HTML-<**img**> tag.

Toepassing/x-javascript – de bron wordt weergegeven in een HTML-<**script**> tag.

Toepassing/x-shockwave-flash – de bron wordt weergegeven in een Flash-speler.

**IFrameResource** beschrijft een HTML-bron die kan worden weergegeven in een IFrame. **HTMLResource** beschrijft een stukje HTML-code dat in een webpagina kan worden ingevoegd. **TrackingEvents** geven trackinggebeurtenissen en de URI op die moet worden aangevraagd wanneer de gebeurtenis plaatsvindt. In dit voorbeeld worden de gebeurtenissen acceptInvitation en samenvouwen bijgehouden. Zie IAB.NET/VAST voor meer informatie over het **nonlinearads-element** en zijn kinderen. Houd er rekening mee dat het element **TrackingEvents** zich bevindt in het element **NietLinearAds** in plaats van het element **Niet-lineair.**

Begeleidende advertenties worden `<CompanionAds>` gedefinieerd binnen een element. Het `<CompanionAds>` element kan een `<Companion>` of meer elementen bevatten. Elk `<Companion>` element beschrijft een begeleidende `<StaticResource>` `<IFrameResource>`advertentie `<HTMLResource>` en kan een , , of die zijn opgegeven op dezelfde manier als in een niet-lineaire advertentie bevatten. Een VAST-bestand kan meerdere begeleidende advertenties bevatten en de speler-toepassing kan de meest geschikte advertentie kiezen om weer te geven. Zie [VAST 3.0 voor](https://www.iab.net/media/file/VASTv3.0.pdf)meer informatie over VAST.

### <a name="using-a-digital-video-multiple-ad-playlist-vmap-file"></a>Een VMAP-bestand (Digital Video Multiple Ad Playlist) gebruiken
Met een VMAP-bestand u opgeven wanneer er advertentieblokken optreden, hoe lang elke onderbreking is, hoeveel advertenties binnen een onderbreking kunnen worden weergegeven en welke typen advertenties tijdens een onderbreking kunnen worden weergegeven. Het volgende in een voorbeeld VMAP-bestand dat één advertentieeinde definieert:

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <VAST version="2.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="oxml.xsd">
              <Ad id="115571748">
                <InLine>
                  <AdSystem version="2.0 alpha">Atlas</AdSystem>
                  <AdTitle>Unknown</AdTitle>
                  <Description>Unknown</Description>
                  <Survey></Survey>
                  <Error></Error>
                  <Impression id="Atlas"><![CDATA[https://view.atdmt.com/000/sview/115571748/direct;ai.201582527;vt.2/01/634364885739970673]]></Impression>
                  <Creatives>
                    <Creative id="video" sequence="0" AdID="">
                      <Linear>
                        <Duration>00:00:32</Duration>
                        <MediaFiles>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_200" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="200" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_200_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_300" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="300" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_300_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_500" maintainAspectRatio="true" scalable="true"  delivery="progressive" bitrate="500" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_1_000_500_4x3.wmv]]>
                          </MediaFile>
                          <MediaFile apiFramework="Windows Media" id="windows_progressive_700" maintainAspectRatio="true" scalable="true" delivery="progressive" bitrate="700" width="400" height="300" type="video/x-ms-wmv">
                            <![CDATA[http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv]]>
                          </MediaFile>
                        </MediaFiles>
                      </Linear>
                    </Creative>
                  </Creatives>
                </InLine>
              </Ad>
            </VAST>
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Een VMAP-bestand `<VMAP>` begint met een `<AdBreak>` element dat een of meer elementen bevat, waarbij elk een advertentieonderbreking wordt gedefinieerd. Elk advertentieeinde geeft een breuktype, pauze-ID en tijdverschuiving op. Het kenmerk breakType geeft het type advertentie aan dat tijdens de pauze kan worden afgespeeld: lineair, niet-lineair of weergegeven. Geef advertenties weer aan UITGEBREIDE begeleidende advertenties. Meer dan één advertentietype kan worden opgegeven in een lijst met gescheiden komma's (geen spaties). De breakID is een optionele id voor de advertentie. De timeOffset geeft aan wanneer de advertentie moet worden weergegeven. Het kan op een van de volgende manieren worden opgegeven:

1. Tijd – in hh:mm:ss of hh:mm:ss.mmm-formaat waarbij .mmm milliseconden is. De waarde van dit kenmerk geeft de tijd op vanaf het begin van de videotijdlijn tot het begin van het advertentieeinde.
2. Percentage – in n%-indeling waarbij n het percentage is van de videotijdlijn dat moet worden afgespeeld voordat de advertentie wordt afgespeeld
3. Begin/einde – geeft aan dat een advertentie moet worden weergegeven voor of nadat de video is weergegeven
4. Positie : hiermee geeft u de volgorde van advertentieblokken op wanneer de timing van de advertentieblokken onbekend is, zoals in live streaming. De volgorde van elk advertentieeinde wordt opgegeven in de #n-indeling waarbij n een geheel getal 1 of hoger is. 1 betekent dat de advertentie moet worden afgespeeld bij de eerste gelegenheid, 2 betekent dat de advertentie moet worden afgespeeld bij de tweede kans en ga zo maar door.

Binnen `<AdBreak>` het element kan er een <**AdSource**> element. Het element <**AdSource**> bevat de volgende kenmerken:

1. Id – geeft een id voor de advertentiebron op
2. allowMultipleAds – een Booleaanse waarde die aangeeft of meerdere advertenties kunnen worden weergegeven tijdens het advertentieeinde
3. followRedirects – een optionele Booleaanse waarde die aangeeft of de videospeler omleidingen binnen een advertentiereactie moet honoreren

Het **AdSource** <AdSource->-element biedt de speler een inline advertentiereactie of een verwijzing naar een advertentiereactie. Het kan een van de volgende elementen bevatten:

* `<VASTAdData>`geeft aan dat een VAST-advertentieantwoord is ingesloten in het VMAP-bestand
* `<AdTagURI>`een URI die verwijst naar een advertentiereactie van een ander systeem
* `<CustomAdData>`-een willekeurige tekenreeks die een niet-VAST-antwoord vertegenwoordigt

In dit voorbeeld wordt een in-line `<VASTAdData>` advertentieantwoord opgegeven met een element dat een VAST-advertentieantwoord bevat. Zie [VMAP](https://www.iab.net/guidelines/508676/digitalvideo/vsuite/vmap)voor meer informatie over de andere elementen.

Het <**AdBreak**> element kan ook één <**TrackingEvents**> element bevatten. Met het element <**TrackingEvents**> u het begin of einde van een advertentieeinde bijhouden of dat er een fout is opgetreden tijdens het advertentieeinde. Het> element <**TrackingEvents** bevat een of meer <**Tracking**>-elementen, die elk een trackinggebeurtenis en een tracking URI aangeven. De mogelijke tracking gebeurtenissen zijn:

1. breakStart – volgt het begin van een advertentieonderbreking
2. breakEnd – volg de voltooiing van een advertentieonderbreking
3. fout – houdt een fout bij die is opgetreden tijdens het advertentieeinde

In het volgende voorbeeld wordt een VMAP-bestand weergegeven waarin trackinggebeurtenissen worden opgegeven

```xml
    <vmap:VMAP xmlns:vmap="http://www.iab.net/vmap-1.0" version="1.0">
      <vmap:AdBreak breakType="linear" breakId="mypre" timeOffset="start">
        <vmap:AdSource allowMultipleAds="true" followRedirects="true" id="1">
          <vmap:VASTData>
            <!--Inline VAST -->
          </vmap:VASTData>
        </vmap:AdSource>
        <vmap:TrackingEvents>
          <vmap:Tracking event="breakStart">
            http://MyServer.com/breakstart.gif
          </vmap:Tracking>
          <vmap:Tracking event="breakend">
            http://MyServer.com/breakend.gif
          </vmap:Tracking>
          <vmap:Tracking event="error">
            http://MyServer.com/error.gif
          </vmap:Tracking>
        </vmap:TrackingEvents>
      </vmap:AdBreak>
    </vmap:VMAP>
```

Zie voor meer informatie over de <**TrackingEvents**> element en zijn kinderenhttp://iab.net/VMAP.pdf

### <a name="using-a-media-abstract-sequencing-template-mast-file"></a>Een MAST-bestand (Media Abstract Sequencing Template) gebruiken
Met een MAST-bestand u triggers opgeven die bepalen wanneer een advertentie wordt weergegeven. Het volgende is een voorbeeld-MAST-bestand dat triggers bevat voor een preroll-advertentie, een midroll-advertentie en een postroll-advertentie.

```xml
    <MAST xsi:schemaLocation="http://openvideoplayer.sf.net/mast http://openvideoplayer.sf.net/mast/mast.xsd" xmlns="http://openvideoplayer.sf.net/mast" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance">
      <triggers>
        <trigger id="preroll" description="preroll every item"  >
          <startConditions>
            <condition type="event" name="OnItemStart" />
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="midroll" description="midroll at 15 sec."  >
          <startConditions>
            <condition type="property" name="Position" value="00:00:15.0" operator="GEQ" />
          </startConditions>
          <endConditions>
            <condition type="event" name="OnItemEnd"/>
            <!--This 'resets' the trigger for the next clip-->
          </endConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>

        <trigger id="postroll" description="postroll"  >
          <startConditions>
            <condition type="event" name="OnItemEnd"/>
          </startConditions>
          <sources>
            <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
              <sources />
            </source>
          </sources>
        </trigger>
      </triggers>
    </MAST>
```


Een MAST-bestand begint met een **MAST-element** dat één **triggers-element** bevat. Het `<triggers>` element bevat een of meer **triggerelementen** die bepalen wanneer een advertentie moet worden afgespeeld.

Het **triggerelement** bevat een element **startVoorwaarden** dat aangeeft wanneer een advertentie moet worden afgespeeld. Het element **startConditions** bevat `<condition>` een of meer elementen. Wanneer `<condition>` elke evalueert om waar te zijn wordt een `<condition>` trigger gestart of ingetrokken, afhankelijk van of het is opgenomen in een **beginVoorwaarden** of **eindVoorwaarden** element respectievelijk. Wanneer `<condition>` meerdere elementen aanwezig zijn, worden ze behandeld als een impliciete OR, elke voorwaarde die wordt geëvalueerd om waar te zijn, zal ervoor zorgen dat de trigger wordt gestart. `<condition>`elementen kunnen worden genest. Wanneer `<condition>` onderliggende elementen vooraf zijn ingesteld, worden ze behandeld als een impliciete EN, moeten alle voorwaarden worden geëvalueerd om waar te zijn voor de trigger om te starten. Het `<condition>` element bevat de volgende kenmerken die de voorwaarde definiëren:

1. **type** – geeft het type voorwaarde, gebeurtenis of eigenschap aan
2. **naam** – de naam van de eigenschap of gebeurtenis die tijdens de evaluatie moet worden gebruikt
3. **waarde** – de waarde waarop een eigenschap wordt beoordeeld
4. **operator** – de te gebruiken bewerking tijdens de evaluatie: EQ (gelijk), NEQ (niet gelijk), GTR (groter), GEQ (groter of gelijk), LT (minder dan), LEQ (minder dan of gelijk), MOD (modulo)

**eindeVoorwaarden** bevatten `<condition>` ook elementen. Wanneer een voorwaarde wordt geëvalueerd om waar te zijn, wordt de trigger gereset. Het `<trigger>` element bevat `<sources>` ook een element `<source>` dat een of meer elementen bevat. De `<source>` elementen definiëren de URI voor het advertentieantwoord en het type advertentiereactie. In dit voorbeeld wordt een URI gegeven aan een VAST-antwoord.

```xml
    <trigger id="postroll" description="postroll"  >
      <startConditions>
        <condition/>
      </startConditions>
      <sources>
        <source uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" format="vast">
          <sources />
        </source>
      </sources>
    </trigger>
```

### <a name="using-video-player-ad-interface-definition-vpaid"></a>VideoPlayer-Ad-interfacedefinitie (VPAID) gebruiken
VPAID is een API om uitvoerbare advertentieblokken te laten communiceren met een videospeler. Dit maakt zeer interactieve advertentie-ervaringen mogelijk. De gebruiker kan communiceren met de advertentie en de advertentie kan reageren op acties van de kijker. Een advertentie kan bijvoorbeeld knoppen weergeven waarmee de gebruiker meer informatie of een langere versie van de advertentie kan weergeven. De videospeler moet de VPAID API ondersteunen en de uitvoerbare advertentie moet de API implementeren. Wanneer een speler een advertentie van een advertentieserver aanvraagt, kan de server reageren met een VAST-antwoord dat een VPAID-advertentie bevat.

Een uitvoerbare advertentie wordt gemaakt in code die moet worden uitgevoerd in een runtime-omgeving zoals Adobe Flash™ of JavaScript die kan worden uitgevoerd in een webbrowser. Wanneer een advertentieserver een VAST-antwoord retourneert met een VPAID-advertentie, moet de waarde van het kenmerk apiFramework in het `<MediaFile>` element "VPAID" zijn. Dit kenmerk geeft aan dat de opgenomen advertentie een VPAID-uitvoerbare advertentie is. Het typekenmerk moet worden ingesteld op het MIME-type van het uitvoerbare, zoals "application/x-shockwave-flash" of "application/x-javascript". In het volgende `<MediaFile>` XML-fragment wordt het element weergegeven van een VAST-antwoord dat een VPAID-uitvoerbare advertentie bevat.

```xml
    <MediaFiles>
       <MediaFile id="1" delivery="progressive" type="application/x-shockwaveflash"
                  width="640" height="480" apiFramework="VPAID">
           <!-- CDATA wrapped URI to executable ad -->
       </MediaFile>
    </MediaFiles>
```

Een uitvoerbare advertentie kan worden `<AdParameters>` geïnitialiseerd met behulp van het element in de `<Linear>` of `<NonLinear>` elementen in een VAST-antwoord. Zie [VAST 3.0 voor](https://www.iab.net/media/file/VASTv3.0.pdf)meer informatie over het `<AdParameters>` element. Zie [VPAID 2.0](https://www.iab.net/media/file/VPAID_2.0_Final_04-10-2012.pdf)voor meer informatie over de VPAID API.

## <a name="implementing-a-windows-or-windows-phone-8-player-with-ad-support"></a>Een Windows- of Windows Phone 8 Player implementeren met advertentieondersteuning
Het Microsoft Media Platform: Player Framework voor Windows 8 en Windows Phone 8 bevat een verzameling voorbeeldtoepassingen die u laten zien hoe u een videospelertoepassing implementeert met behulp van het framework. U het Player Framework en de voorbeelden downloaden van [Player Framework voor Windows 8 en Windows Phone 8.](https://playerframework.codeplex.com)

Wanneer u de oplossing Microsoft.PlayerFramework.Xaml.Samples opent, ziet u een aantal mappen binnen het project. De map Adverteren bevat de voorbeeldcode die relevant is voor het maken van een videospeler met advertentieondersteuning. In de map Adverteren bevindt zich een aantal XAML/cs-bestanden die elk laten zien hoe u advertenties op een andere manier invoegen. In de volgende lijst worden elk overzicht beschreven:

* AdPodPage.xaml Laat zien hoe je een advertentiepod weergeeft.
* AdSchedulingPage.xaml laat zien hoe u advertenties plant.
* FreeWheelPage.xaml Laat zien hoe u de FreeWheel-plug-in gebruiken om advertenties te plannen.
* MastPage.xaml Laat zien hoe u advertenties plant met een MAST-bestand.
* ProgrammaticAdPage.xaml Laat zien hoe u advertenties programmatisch plannen in een video.
* ScheduleClipPage.xaml Laat zien hoe u een advertentie plant zonder EEN VAST-bestand.
* VastLinearCompanionPage.xaml Laat zien hoe je een lineaire en begeleidende advertentie invoegt.
* VastNonLinearPage.xaml Laat zien hoe u een niet-lineaire advertentie invoegt.
* VmapPage.xaml Laat zien hoe u advertenties opgeeft met een VMAP-bestand.

Elk van deze samples maakt gebruik van de Klasse MediaPlayer gedefinieerd door het spelerskader. De meeste voorbeelden maken gebruik van plug-ins die ondersteuning toevoegen voor verschillende advertentieresponsindelingen. Het voorbeeld ProgrammaticAdPage werkt programmatisch samen met een instantie MediaPlayer.

### <a name="adpodpage-sample"></a>Voorbeeld van AdPodPage
In dit voorbeeld wordt de AdSchedulerPlugin gebruikt om te bepalen wanneer een advertentie moet worden weergegeven. In dit voorbeeld is een mid-roll advertentie gepland om te worden afgespeeld na vijf seconden. De advertentiepod (een groep advertenties die in volgorde moet worden weergegeven) is opgegeven in een VAST-bestand dat is geretourneerd van een advertentieserver. De URI naar het VAST-bestand is opgegeven in het `<RemoteAdSource>` element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">

        <mmppf:MediaPlayer.Plugins>
            <ads:AdSchedulerPlugin>
                <ads:AdSchedulerPlugin.Advertisements>

                    <ads:MidrollAdvertisement Time="00:00:05">
                        <ads:MidrollAdvertisement.Source>
                            <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_adpod.xml" Type="vast"/>
                        </ads:MidrollAdvertisement.Source>
                    </ads:MidrollAdvertisement>

                </ads:AdSchedulerPlugin.Advertisements>
            </ads:AdSchedulerPlugin>
            <ads:AdHandlerPlugin/>
        </mmppf:MediaPlayer.Plugins>
    </mmppf:MediaPlayer>
```

Zie [Adverteren in het Player Framework op Windows 8 en Windows Phone 8](https://playerframework.codeplex.com/wikipage?title=Advertising&referringTitle=Windows%208%20Player%20Documentation) voor meer informatie over de AdSchedulerPlugin

### <a name="adschedulingpage"></a>Advertentieplanningpagina
In dit voorbeeld wordt ook gebruik gemaakt van de AdSchedulerPlugin. Het plant drie advertenties, een pre-roll advertentie, een mid-roll advertentie en een post-roll advertentie. De URI naar de VAST voor `<RemoteAdSource>` elke advertentie is opgegeven in een element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:PrerollAdvertisement>
                                <ads:PrerollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PrerollAdvertisement.Source>
                            </ads:PrerollAdvertisement>

                            <ads:MidrollAdvertisement Time="00:00:15">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                            <ads:PostrollAdvertisement>
                                <ads:PostrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml" Type="vast"/>
                                </ads:PostrollAdvertisement.Source>
                            </ads:PostrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="freewheelpage"></a>Freewheelpage
In dit voorbeeld wordt de FreeWheelPlugin gebruikt die een eigenschap Bron opgeeft dat een URI opgeeft die verwijst naar een SmartXML-bestand dat advertentie-inhoud en informatie over advertentieplanning opgeeft.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:FreeWheelPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/freewheel.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="mastpage"></a>MastPage MastPage
Dit voorbeeld maakt gebruik van de MastSchedulerPlugin waarmee u een MAST-bestand gebruiken. Het kenmerk Bron geeft de locatie van het MAST-bestand aan.
```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:MastSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/mast.xml" />
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="programmaticadpage"></a>ProgrammaticAdPage
Dit voorbeeld werkt programmatisch samen met de MediaPlayer. Het ProgrammaticAdPage.xaml-bestand maakt een instantiatevan mediaplayer:

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4"/>
```

Het ProgrammaticAdPage.xaml.cs-bestand maakt een AdHandlerPlugin, voegt een TimelineMarker toe om op te geven wanneer een advertentie moet worden weergegeven en voegt vervolgens een handler toe voor de gebeurtenis MarkerReached die een RemoteAdSource laadt die een URI opgeeft aan een VAST-bestand en vervolgens de advertentie afspeelt.

```csharp
    public sealed partial class ProgrammaticAdPage : Microsoft.PlayerFramework.Samples.Common.LayoutAwarePage
        {
            AdHandlerPlugin adHandler;

            public ProgrammaticAdPage()
            {
                this.InitializeComponent();
                adHandler = new AdHandlerPlugin();
                player.Plugins.Add(new AdHandlerPlugin());
                player.Markers.Add(new TimelineMarker() { Time = TimeSpan.FromSeconds(5), Type = "myAd" });
                player.MarkerReached += pf_MarkerReached;
            }

            async void pf_MarkerReached(object sender, TimelineMarkerRoutedEventArgs e)
            {
                if (e.Marker.Type == "myAd")
                {
                    var adSource = new RemoteAdSource() { Type = VastAdPayloadHandler.AdType, Uri = new Uri("http://smf.blob.core.windows.net/samples/win8/ads/vast_linear.xml") };
                    //var adSource = new AdSource() { Type = DocumentAdPayloadHandler.AdType, Payload = SampleAdDocument };
                    var progress = new Progress<AdStatus>();
                    try
                    {
                        await player.PlayAd(adSource, progress, CancellationToken.None);
                    }
                    catch { /* ignore */ }
                }
            }
```

### <a name="scheduleclippage"></a>Clippagina plannen
In dit voorbeeld wordt de AdSchedulerPlugin gebruikt om een midroll-advertentie te plannen door een wmv-bestand op te geven dat de advertentie bevat.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.cloudapp.net/html5/media/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:AdSource Type="clip">
                                        <ads:AdSource.Payload>
                                            <ads:ClipAdPayload MediaSource="http://smf.blob.core.windows.net/samples/ads/media/XBOX_HD_DEMO_700_2_000_700_4x3.wmv" MimeType="video/x-ms-wmv" />
                                        </ads:AdSource.Payload>
                                    </ads:AdSource>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearcompanionpage"></a>VastLinearCompanionPagina
In dit voorbeeld wordt uitgelegd hoe u de AdSchedulerPlugin gebruiken om een lineaire advertentie in het middensegment te plannen met een begeleidende advertentie. Het `<RemoteAdSource>` element geeft de locatie van het VAST-bestand aan.

```xml
    <mmppf:MediaPlayer Grid.Row="1"  x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_companions.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vastlinearnonlinearpage"></a>VastLinearNonLinearPage
In dit voorbeeld wordt de AdSchedulerPlugin gebruikt om een lineaire en een niet-lineaire advertentie te plannen. De locatie van het `<RemoteAdSource>` VAST-bestand wordt opgegeven met het element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:AdSchedulerPlugin>
                        <ads:AdSchedulerPlugin.Advertisements>

                            <ads:MidrollAdvertisement Time="00:00:05">
                                <ads:MidrollAdvertisement.Source>
                                    <ads:RemoteAdSource Uri="http://smf.blob.core.windows.net/samples/win8/ads/vast_linear_nonlinear.xml" Type="vast"/>
                                </ads:MidrollAdvertisement.Source>
                            </ads:MidrollAdvertisement>

                        </ads:AdSchedulerPlugin.Advertisements>
                    </ads:AdSchedulerPlugin>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

### <a name="vmappage"></a>VMAPPage
In dit voorbeeld wordt de VmapSchedulerPlugin gebruikt om advertenties te plannen met behulp van een VMAP-bestand. De URI naar het VMAP-bestand is `<VmapSchedulerPlugin>` opgegeven in het kenmerk Bron van het element.

```xml
    <mmppf:MediaPlayer x:Name="player" Source="http://smf.blob.core.windows.net/samples/videos/bigbuck.mp4">
                <mmppf:MediaPlayer.Plugins>
                    <ads:VmapSchedulerPlugin Source="http://smf.blob.core.windows.net/samples/win8/ads/vmap.xml"/>
                    <ads:AdHandlerPlugin/>
                </mmppf:MediaPlayer.Plugins>
            </mmppf:MediaPlayer>
```

## <a name="implementing-an-ios-video-player-with-ad-support"></a>Een iOS-videospeler implementeren met advertentieondersteuning
Het Microsoft Media Platform: Player Framework voor iOS bevat een verzameling voorbeeldtoepassingen die u laten zien hoe u een videospelertoepassing implementeert met behulp van het framework. U het Player Framework en de voorbeelden downloaden van [Azure Media Player Framework.](https://github.com/Azure/azure-media-player-framework) De GitHub-pagina heeft een link naar een Wiki met aanvullende informatie over het spelerskader en een inleiding tot het spelersvoorbeeld: [Azure Media Player Wiki](https://github.com/Azure/azure-media-player-framework/wiki/How-to-use-Azure-media-player-framework).

### <a name="scheduling-ads-with-vmap"></a>Advertenties plannen met VMAP
In het volgende voorbeeld ziet u hoe u advertenties plant met een VMAP-bestand.

```csharp
    // How to schedule an Ad using VMAP.
    //First download the VMAP manifest

    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVMAP.xml"]])
            {
                [self logFrameworkError];
            }
            else
            {
                // Schedule a list of ads using the downloaded VMAP manifest
                if (![framework scheduleVMAPWithManifest:manifest])
                {
                    [self logFrameworkError];
                }
            }
```

### <a name="scheduling-ads-with-vast"></a>Advertenties plannen met VAST
In het volgende voorbeeld ziet u hoe u een late bindende VAST-advertentie plant.


```csharp
    //Example:3 How to schedule a late binding VAST ad.
    // set the start time for the ad
    adLinearTime.startTime = 13;
    adLinearTime.duration = 0;
    // Specify the URI of the VAST file
    NSString *vastAd1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml";
    // Create an AdInfo object
     AdInfo *vastAdInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URL to VAST file
    vastAdInfo1.clipURL = [NSURL URLWithString:vastAd1];
    // set running time of ad
    vastAdInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    vastAdInfo1.mediaTime.clipBeginMediaTime = 0;
    vastAdInfo1.mediaTime.clipEndMediaTime = 10;
    vastAdInfo1.policy = @"policy for late binding VAST";
    // specify ad type
    vastAdInfo1.type = AdType_Midroll;
    vastAdInfo1.appendTo=-1;
    adIndex = 0;
    // schedule ad
    if (![framework scheduleClip:vastAdInfo1 atTime:adLinearTime forType:PlaylistEntryType_VAST andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

   In het volgende voorbeeld ziet u hoe u een vroege bindende VAST-advertentie plant.

```csharp
    //Example:4 Schedule an early binding VAST ad
    //Download the VAST file
    if (![framework.adResolver downloadManifest:&manifest withURL:[NSURL URLWithString:@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/vast/PlayerTestVAST.xml"]])
    {
        [self logFrameworkError];
    }
    else
    {
        adLinearTime.startTime = 7;
        adLinearTime.duration = 0;

        // Create AdInfo instance
        AdInfo *vastAdInfo2 = [[[AdInfo alloc] init] autorelease];
        vastAdInfo2.mediaTime = [[[MediaTime alloc] init] autorelease];
        vastAdInfo2.policy = @"policy for early binding VAST";
        // specify ad type
        vastAdInfo2.type = AdType_Midroll;
        vastAdInfo2.appendTo=-1;
        // schedule ad
        if (![framework scheduleVASTClip:vastAdInfo2 withManifest:manifest atTime:adLinearTime andGetClipId:&adIndex])
        {
            [self logFrameworkError];
        }
    }
```

In het volgende voorbeeld ziet u hoe u een advertentie invoegt met Behulp van Rough Cut Editing (RCE)

```csharp
    //Example:1 How to use RCE.
    // specify manifest for ad content
    NSString *secondContent=@"http://wamsblureg001orig-hs.cloudapp.net/6651424c-a9d1-419b-895c-6993f0f48a26/The%20making%20of%20Microsoft%20Surface-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // specify ad length
    mediaTime.currentPlaybackPosition = 0;
    mediaTime.clipBeginMediaTime = 0;
    mediaTime.clipEndMediaTime = 80;
    // append ad content
    if (![framework appendContentClip:[NSURL URLWithString:secondContent] withMediaTime:mediaTime andGetClipId:&clipId])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een advertentiepod plant.

```csharp
    //Example:5 Schedule an ad Pod.
    // Set start time for ad
    adLinearTime.startTime = 23;
    adLinearTime.duration = 0;

    // Specify URL to content
    NSString *adpodSt1=@"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    // Create an AdInfo instance
    AdInfo *adpodInfo1 = [[[AdInfo alloc] init] autorelease];
    // set URI to ad content
    adpodInfo1.clipURL = [NSURL URLWithString:adpodSt1];
    // Set ad running time
    adpodInfo1.mediaTime = [[[MediaTime alloc] init] autorelease];
    adpodInfo1.mediaTime.clipBeginMediaTime = 0;
    adpodInfo1.mediaTime.clipEndMediaTime = 17;
    adpodInfo1.policy = @"policy for ad pod 1";
    // Set ad type
    adpodInfo1.type = AdType_Midroll;
    adpodInfo1.appendTo=-1;
    adIndex = 0;
    // Schedule ad
    if (![framework scheduleClip:adpodInfo1 atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een niet-plakkerige midroll-advertentie plant. Een niet-kleverige advertentie wordt slechts één keer afgespeeld, ongeacht het zoeken dat de kijker uitvoert.

```csharp
    //Example:6 Schedule a single non sticky mid roll Ad
    // specify URL to content
    NSString *oneTimeAd=@"http://wamsblureg001orig-hs.cloudapp.net/5389c0c5-340f-48d7-90bc-0aab664e5f02/Windows%208_%20You%20and%20Me%20Together-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";

    // create an AdInfo instance
    AdInfo *oneTimeInfo = [[[AdInfo alloc] init] autorelease];
    // set URL of ad
    oneTimeInfo.clipURL = [NSURL URLWithString:oneTimeAd];
    oneTimeInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    oneTimeInfo.mediaTime.clipBeginMediaTime = 0;
    oneTimeInfo.mediaTime.clipEndMediaTime = -1;
    oneTimeInfo.policy = @"policy for one-time ad";
    // set ad start time
    adLinearTime.startTime = 43;
    adLinearTime.duration = 0;
    // set ad type
    oneTimeInfo.type = AdType_Midroll;
    // non sticky ad
    oneTimeInfo.deleteAfterPlayed = YES;
    // schedule ad
    if (![framework scheduleClip:oneTimeInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een plakkerige midroll-advertentie plant. Elke keer dat het opgegeven punt op de videotijdlijn wordt bereikt, wordt een plakkerige advertentie weergegeven.

```csharp
    //Example:7 Schedule a single sticky mid roll Ad
    NSString *stickyAd=@"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *stickyAdInfo = [[[AdInfo alloc] init] autorelease];
    // set URI to ad
    stickyAdInfo.clipURL = [NSURL URLWithString:stickyAd];
    stickyAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    stickyAdInfo.mediaTime.clipBeginMediaTime = 0;
    stickyAdInfo.mediaTime.clipEndMediaTime = 15;
    stickyAdInfo.policy = @"policy for sticky mid-roll ad";
    // set ad start time
    adLinearTime.startTime = 64;
    adLinearTime.duration = 0;
    // set ad type
    stickyAdInfo.type = AdType_Midroll;
    stickyAdInfo.deleteAfterPlayed = NO;
    // schedule ad
    if (![framework scheduleClip:stickyAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een postroll-advertentie plant.

```csharp
    //Example:8 Schedule Post Roll Ad
    NSString *postAdURLString=@"http://wamsblureg001orig-hs.cloudapp.net/aa152d7f-3c54-487b-ba07-a58e0e33280b/wp-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    // create AdInfo instance
    AdInfo *postAdInfo = [[[AdInfo alloc] init] autorelease];
    postAdInfo.clipURL = [NSURL URLWithString:postAdURLString];
    postAdInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    postAdInfo.mediaTime.clipBeginMediaTime = 0;
    // set ad length
    postAdInfo.mediaTime.clipEndMediaTime = 45;
    postAdInfo.policy = @"policy for post-roll ad";
    // set ad type
    postAdInfo.type = AdType_Postroll;
    adLinearTime.duration = 0;
    if (![framework scheduleClip:postAdInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een preroll-advertentie plant.

```csharp
    //Example:9 Schedule Pre Roll Ad
    NSString *adURLString = @"http://wamsblureg001orig-hs.cloudapp.net/2e4e7d1f-b72a-4994-a406-810c796fc4fc/The%20Surface%20Movement-m3u8-aapl.ism/Manifest(format=m3u8-aapl)";
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 40; //You could play a portion of an Ad. Yeh!
    adInfo.mediaTime.clipEndMediaTime = 59;
    adInfo.policy = @"policy for pre-roll ad";
    adInfo.appendTo = -1;
    adInfo.type = AdType_Preroll;
    adLinearTime.duration = 0;
    // schedule ad
    if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```

In het volgende voorbeeld ziet u hoe u een overlayadvertentie voor het middensegment plant.

```csharp
    // Example10: Schedule a Mid Roll overlay Ad
    NSString *adURLString = @"https://portalvhdsq3m25bf47d15c.blob.core.windows.net/asset-e47b43fd-05dc-4587-ac87-5916439ad07f/Windows%208_%20Cliffjumpers.mp4?st=2012-11-28T16%3A31%3A57Z&se=2014-11-28T16%3A31%3A57Z&sr=c&si=2a6dbb1e-f906-4187-a3d3-7e517192cbd0&sig=qrXYZBekqlbbYKqwovxzaVZNLv9cgyINgMazSCbdrfU%3D";
    //Create AdInfo instance
    AdInfo *adInfo = [[[AdInfo alloc] init] autorelease];
    adInfo.clipURL = [NSURL URLWithString:adURLString];
    adInfo.mediaTime = [[[MediaTime alloc] init] autorelease];
    adInfo.mediaTime.currentPlaybackPosition = 0;
    adInfo.mediaTime.clipBeginMediaTime = 0;
    // specify ad length
    adInfo.mediaTime.clipEndMediaTime = 20;
    adInfo.policy = @"policy for mid-roll overlay ad";
    adInfo.appendTo = -1;
    // specify ad type
    adInfo.type = AdType_Midroll;
    // specify ad start time & duration
    adLinearTime.startTime = 300;
    adLinearTime.duration = 20;
    // schedule ad            if (![framework scheduleClip:adInfo atTime:adLinearTime forType:PlaylistEntryType_Media andGetClipId:&adIndex])
    {
        [self logFrameworkError];
    }
```


## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
