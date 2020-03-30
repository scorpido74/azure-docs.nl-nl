---
title: Azure Media Services - Getimede metagegevens signaleren in live streaming
description: Deze specificatie schetst methoden voor het signaleren van getimede metagegevens bij het innemen en streamen naar Azure Media Services. Dit omvat ondersteuning voor generieke getimede metadatasignalen (ID3) en SCTE-35-signalering voor het inbrengen van advertenties en het signaleren van de toestand van de splice-toestand.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: johndeu
ms.assetid: 265b94b1-0fb8-493a-90ec-a4244f51ce85
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: johndeu
ms.openlocfilehash: 551fb0cb9f3745a62d5d84f2c4878bbbbe5ad9a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137319"
---
# <a name="signaling-timed-metadata-in-live-streaming"></a>Getimede metagegevens signaleren in live streaming 

Laatst bijgewerkt: 2019-08-22

### <a name="conformance-notation"></a>Conformance Notatie

De trefwoorden "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY" en "OPTIONAL" in dit document moeten worden geïnterpreteerd zoals beschreven in RFC 2119

## <a name="1-introduction"></a>1. Inleiding 

Om het inbrengen van advertenties of aangepaste metadata gebeurtenissen op een clientspeler te signaleren, maken omroepen vaak gebruik van getimede metadata ingebed in de video. Om deze scenario's mogelijk te maken, biedt Media Services ondersteuning voor het transport van getimede metagegevens van het innamepunt van het live streamingkanaal naar de clienttoepassing.
Deze specificatie geeft een overzicht van verschillende modi die worden ondersteund door Media Services voor getimede metadata binnen live streaming signalen.

1. [SCTE-35] signalering die voldoet aan de normen van [SCTE-35], [SCTE-214-1], [SCTE-214-3] en [RFC8216]

2. [SCTE-35] signalering die voldoet aan de verouderde [Adobe-Primetime]-specificatie voor RTMP-advertentiesignalering.
   
3. Een algemene getimede metagegevenssignaleringsmodus voor berichten die **NIET** [SCTE-35] zijn en [ID3v2] of andere aangepaste schema's kunnen dragen die door de ontwikkelaar van de toepassing zijn gedefinieerd.

## <a name="11-terms-used"></a>1.1 Gebruikte termen

| Termijn                | Definitie                                                                                                                                                                                                                                    |
| ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Advertentie-onderbreking            | Een locatie of een tijdstip waarop een of meer advertenties kunnen worden gepland voor weergave; hetzelfde als gebruiks- en plaatsingsmogelijkheden.                                                                                                                     |
| Advertentiebeslissingsservice | externe service die bepaalt welke advertentie(s) en duur aan de gebruiker worden weergegeven. De services worden doorgaans geleverd door een partner en zijn buiten het bereik van dit document.                                                                    |
| Cue                 | Indicatie van de tijd en parameters van de komende advertentie-pauze. Houd er rekening mee dat cues kunnen duiden op een in behandeling zijnde overstap naar een advertentieeinde, in afwachting van overschakeling naar de volgende advertentie in een advertentieonderbreking en in afwachting van een overstap van een advertentieonderbreking naar de hoofdinhoud.           |
| Verpakker            | Het Azure Media Services "Streaming Endpoint" biedt dynamische verpakkingsmogelijkheden voor DASH en HLS en wordt in de media-industrie aangeduid als een "Verpakker".                                                                              |
| Presentatietijd   | De tijd dat een gebeurtenis wordt gepresenteerd aan een kijker. De tijd vertegenwoordigt het moment op de mediatijdlijn dat een kijker de gebeurtenis zou zien. De presentatietijd van een scte-35-splice_info() is bijvoorbeeld het splice_time(). |
| Aankomsttijd        | Het tijdstip waarop een gebeurtenisbericht binnenkomt. De tijd is meestal verschillend van de presentatietijd van de gebeurtenis, omdat gebeurtenisberichten worden verzonden vóór de presentatietijd van de gebeurtenis.                                                    |
| Schaars spoor        | mediatrack die niet continu is en tijd is gesynchroniseerd met een bovenliggende of controletrack.                                                                                                                                                  |
| Oorsprong              | De Azure Media Streaming Service                                                                                                                                                                                                             |
| Kanaalgootsteen        | De Live streamingservice van Azure Media                                                                                                                                                                                                        |
| HLS                 | Apple HTTP Live Streaming protocol                                                                                                                                                                                                            |
| Dash                | Dynamische adaptieve streaming via HTTP                                                                                                                                                                                                          |
| Glad              | Vloeiend streamingprotocol                                                                                                                                                                                                                     |
| MPEG2-TS            | MPEG 2 Transportstromen                                                                                                                                                                                                                      |
| RTMP                | Real-time multimediaprotocol                                                                                                                                                                                                                 |
| uimsbf              | Unsigned integer, most significant bit first.                                                                                                                                                                                                 |

---

## <a name="12-normative-references"></a>1.2 Normatieve referenties

De volgende documenten bevatten bepalingen die, door verwijzing in deze tekst, bepalingen van dit document vormen. Alle documenten zijn onderworpen aan herziening door de normalisatie-instellingen, en lezers worden aangemoedigd om de mogelijkheid van de toepassing van de meest recente edities van de hieronder genoemde documenten te onderzoeken. Lezers worden er ook aan herinnerd dat nieuwere edities van de naar de verwijzing genoemde documenten mogelijk niet compatibel zijn met deze versie van de getimede metagegevensspecificatie voor Azure Media Services.


| Standard          | Definitie                                                                                                                                                                                                     |
| ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Adobe-Primetime] | [Specificatie 1.2 voor het inbrengen van het digitale programma van Primetime Digitaal programma](https://www.adobe.com/content/dam/acom/en/devnet/primetime/PrimetimeDigitalProgramInsertionSignalingSpecification.pdf)                       |
| [Adobe-Flash-AS]  | [NASLAGNA FLASH ActionScript-taal](https://help.adobe.com/archive/en_US/as2/flashlite_2.x_3.x_aslr.pdf)                                                                                                   |
| [AMF0]            | ["Actieberichtindeling AMF0"](https://download.macromedia.com/pub/labs/amf/amf0_spec_121207.pdf)                                                                                                              |
| [DASH-IF-IOP]     | DASH Industry Forum Interop Guidance v 4.2[https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html)    |
| [HLS-TMD]         | Getimede metagegevens voor HTTP Live Streaming -[https://developer.apple.com/streaming](https://developer.apple.com/streaming)                                                                                        |
| [CMAF-ID3]        | [Getimede metagegevens in de common media-toepassingsindeling (CMAF)](https://github.com/AOMediaCodec/id3-emsg)                                                                                                        |
| [ID3v2]           | ID3 Tag versie 2.4.0[http://id3.org/id3v2.4.0-structure](http://id3.org/id3v2.4.0-structure)                                                                                                                |
| [ISO-14496-12]    | ISO/IEC 14496-12: Deel 12 ISO-bestandsbestandsindeling, FourthEdition 2012-07-15                                                                                                                                 |
| [MPEGDASH]        | Informatietechnologie - Dynamische adaptieve streaming via HTTP (DASH) - Deel 1: Beschrijving van de mediapresentatie en segmentindelingen. mei 2014. Gepubliceerd. Url:https://www.iso.org/standard/65274.html         |
| [MPEGCMAF]        | Informatietechnologie -- Multimedia applicatie formaat (MPEG-A) -- Deel 19: Common media application format (CMAF) voor gesegmenteerde media. januari 2018. Gepubliceerd. Url:https://www.iso.org/standard/71975.html |
| [MPEGCENC]        | Informatietechnologie -- MPEG-systeemtechnologieën -- Deel 7: Algemene versleuteling in ISO-bestandsbestanden. februari 2016. Gepubliceerd. Url:https://www.iso.org/standard/68042.html                   |
| [MS-SSTR]         | ["Microsoft Smooth Streaming Protocol", 15 mei 2014](https://docs.microsoft.com/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251)                                                     |
| [MS-SSTR-Inname]  | [Azure Media Services gefragmenteerde MP4 Live Inest-specificatie](https://docs.microsoft.com/azure/media-services/media-services-fmp4-live-ingest-overview)                                                      |
| [RFC8216]         | R. Pantos, Ed. W. May. HTTP Live Streaming. augustus 2017. Informatieve. [https://tools.ietf.org/html/rfc8216](https://tools.ietf.org/html/rfc8216)                                                            |
| [RFC4648]         | De base16-, base32- en base64-gegevenscoderingen -[https://tools.ietf.org/html/rfc4648](https://tools.ietf.org/html/rfc4648)                                                                                     |
| [RTMP]            | ["Adobe's Real-Time Messaging Protocol", 21 december 2012](https://www.adobe.com/devnet/rtmp.html)                                                                                                            |
| [SCTE-35-2019]    | SCTE 35: 2019 - Digital Program Insertion Cueing Message for Cable -https://www.scte.org/SCTEDocs/Standards/ANSI_SCTE%2035%202019r1.pdf                                                                       |
| [SCTE-214-1]      | SCTE 214-1 2016 – MPEG DASH voor IP-gebaseerde kabelservices deel 1: MPD-beperkingen en -extensies                                                                                                                 |
| [SCTE-214-3]      | SCTE 214-3 2015 MPEG DASH voor IP-gebaseerde kabelservices deel 3: DASH/FF-profiel                                                                                                                                  |
| [SCTE-224]        | SCTE 224 2018r1 – Interface voor het plannen van gebeurtenissen en meldingsinterface                                                                                                                                                  |
| [SCTE-250]        | Api voor gebeurtenis- en signaleringsbeheer (ESAM)                                                                                                                                                                      |

---


## <a name="2-timed-metadata-ingest"></a>2. Getimede metadata ingest

Azure Media Services ondersteunt real-time in-band metadata voor zowel [RTMP] als Smooth Streaming [MS-SSTR-Ingest] protocollen. Realtime metagegevens kunnen worden gebruikt om aangepaste gebeurtenissen te definiëren, met uw eigen unieke aangepaste schema's (JSON, Binary, XML), evenals door de industrie gedefinieerde indelingen zoals ID3 of SCTE-35 voor advertentiesignalering in een broadcaststream. 

In dit artikel vindt u de details voor het verzenden van aangepaste getimede metagegevenssignalen met behulp van de ondersteunde innameprotocollen van Azure Media Services. The article also explains how the manifests for HLS, DASH, and Smooth Streaming are decorated with the timed metadata signals, as well as how it is carried in-band when the content is delivered using CMAF (MP4 fragments) or Transport Stream (TS) segments for HLS. 

Veelvoorkomende gebruiksscenario's voor getimede metagegevens zijn:

 - SCTE-35-advertentiesignalen om advertentieonderbrekingen te activeren in een live-gebeurtenis of lineaire uitzending
 - Aangepaste ID3-metagegevens die gebeurtenissen kunnen activeren bij een clienttoepassing (browser, iOS of Android)
 - Aangepaste gedefinieerde JSON-, binaire of XML-metagegevens om gebeurtenissen op een clienttoepassing te activeren
 - Telemetrie van een live encoder, IP-camera of drone
 - Gebeurtenissen van een IP-camera zoals Motion, gezichtsherkenning, enz.
 - Geografische positie-informatie van een actiecamera, drone of bewegend apparaat
 - Song teksten
 - Programmagrenzen op een lineaire live feed
 - Afbeeldingen of augmented metadata die worden weergegeven in een live feed
 - Sportuitslagen of spelklokinformatie
 - Interactieve advertentiepakketten die naast de video in de browser worden weergegeven
 - Quizzen of peilingen
  
Azure Media Services Live Events en Packager kunnen deze getimede metagegevenssignalen ontvangen en converteren naar een stroom metagegevens die clienttoepassingen kunnen bereiken met behulp van op standaarden gebaseerde protocollen zoals HLS en DASH.


## <a name="21-rtmp-timed-metadata"></a>2.1 RTMP-getimede metagegevens

Het [RTMP]-protocol maakt het mogelijk om getimede metadatasignalen te sturen voor verschillende scenario's, waaronder aangepaste metagegevens en SCTE-35-advertentiesignalen. 

Advertentiesignalen (cue-berichten) worden verzonden als [AMF0]-signalen die zijn ingesloten in de [RTMP]-stream. De cue-berichten kunnen ergens vóór de werkelijke gebeurtenis worden verzonden of [SCTE35] advertentiesplice-signaal moet plaatsvinden. Om dit scenario te ondersteunen, wordt de werkelijke presentatietijdstempel van de gebeurtenis verzonden binnen het actiebericht. Zie [AMF0].

De volgende [AMF0]-opdrachten worden ondersteund door Azure Media Services voor RTMP-inname:

- **onUserDataEvent** - gebruikt voor aangepaste metagegevens of [ID3v2] getimede metadata
- **onAdCue** - voornamelijk gebruikt voor het signaleren van een advertentie plaatsing kans in de live stream. Twee vormen van de cue worden ondersteund, een eenvoudige modus en een "SCTE-35" modus. 
- **onCuePoint** - ondersteund door bepaalde on-premises hardware-encoders, zoals de Elemental Live-encoder, om [SCTE35]-berichten te signaleren. 
  

In de volgende tabel wordt het formaat beschreven van de payload van het AMF-bericht dat Media Services zal innemen voor zowel "eenvoudige" als [SCTE35] berichtmodi.

De naam van het [AMF0]-bericht kan worden gebruikt om meerdere gebeurtenisstromen van hetzelfde type te onderscheiden.  Voor zowel [SCTE-35]-berichten als voor de 'eenvoudige' modus moet de naam van het AMF-bericht 'onAdCue' zijn, zoals vereist in de [Adobe-Primetime]-specificatie.  Velden die niet hieronder worden vermeld, worden bij inname genegeerd door Azure Media Services.

## <a name="211-rtmp-with-custom-metadata-using-onuserdataevent"></a>2.1.1 RTMP met aangepaste metadata met behulp van "onUserDataEvent"

Als u aangepaste metagegevensfeeds wilt leveren van uw upstream-encoder, IP-camera, Drone of apparaat met behulp van het RTMP-protocol, gebruikt u het opdrachttype 'onUserDataEvent' [AMF0] van gegevens.

De opdracht **'onUserDataEvent'-gegevensbericht** moet een berichtpayload met de volgende definitie bevatten die door Media Services moet worden vastgelegd en verpakt in de in-band bestandsindeling, evenals de manifesten voor HLS, DASH en Smooth Streaming.
Het wordt aanbevolen om getimede metadataberichten niet vaker dan eens per 0,5 seconden (500 ms) te verzenden of er kunnen stabiliteitsproblemen met de live stream optreden. Elk bericht kan metagegevens van meerdere frames samenvoegen als u metagegevens op frameniveau moet leveren. Als u multibitrate streams verzendt, is het raadzaam om de metagegevens ook op één bitrate te verstrekken om de bandbreedte te verminderen en interferentie met video-/audioverwerking te voorkomen. 

De payload voor de **"onUserDataEvent"** moet een [MPEGDASH] EventStream XML-formaat bericht. Dit maakt het gemakkelijk om in aangepaste gedefinieerde schema's die kunnen worden uitgevoerd in 'emsg' payloads in-band voor CMAF [MPEGCMAF] inhoud die wordt geleverd via HLS of DASH protocollen. Elk DASH Event Stream-bericht bevat een schemaIdUri dat fungeert als een URN-berichtschema-id en de payload van het bericht definieert. Sommige regelingen zoalshttps://aomedia.org/emsg/ID3" " voor [ID3v2], of **urn:scte:scte35:2013:bin** voor [SCTE-35] worden gestandaardiseerd door brancheconsortia voor interoperabiliteit. Elke toepassingsprovider kan zijn eigen aangepaste schema definiëren met behulp van een URL die hij beheert (eigendomdomein) en kan een specificatie opgeven op die URL als ze dat willen. Als een speler een handler heeft voor het gedefinieerde schema, dan is dat het enige onderdeel dat de payload en het protocol moet begrijpen.

Het schema voor de gebeurtenis-gebeurtenis [MPEG-DASH] wordt gedefinieerd als (uittreksel uit DASH ISO-IEC-23009-1-3e editie). Houd er rekening mee dat op dit moment slechts één "EventType" per "EventStream" wordt ondersteund. Alleen het eerste **gebeurteniselement** wordt verwerkt als er meerdere gebeurtenissen zijn opgegeven in de **EventStream.**

```xml
  <!-- Event Stream -->
  <xs:complexType name="EventStreamType">
    <xs:sequence>
      <xs:element name="Event" type="EventType" minOccurs="0" maxOccurs="unbounded"/>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute ref="xlink:href"/>
    <xs:attribute ref="xlink:actuate" default="onRequest"/>
    <xs:attribute name="schemeIdUri" type="xs:anyURI" use="required"/>
    <xs:attribute name="value" type="xs:string"/>
    <xs:attribute name="timescale" type="xs:unsignedInt"/>
  </xs:complexType>
  <!-- Event  -->
  <xs:complexType name="EventType">
    <xs:sequence>
      <xs:any namespace="##other" processContents="lax" minOccurs="0" maxOccurs="unbounded"/>
    </xs:sequence>
    <xs:attribute name="presentationTime" type="xs:unsignedLong" default="0"/>
    <xs:attribute name="duration" type="xs:unsignedLong"/>
    <xs:attribute name="id" type="xs:unsignedInt"/>
    <xs:attribute name="contentEncoding" type="ContentEncodingType"/>
    <xs:attribute name="messageData" type="xs:string"/>
    <xs:anyAttribute namespace="##other" processContents="lax"/>
  </xs:complexType>
```


### <a name="example-xml-event-stream-with-id3-schema-id-and-base64-encoded-data-payload"></a>Voorbeeld XML-gebeurtenisstroom met ID3-schema-id en base64-gecodeerde gegevenspayload.  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="https://aomedia.org/emsg/ID3">
         <Event contentEncoding="Base64">
          -- base64 encoded ID3v2 full payload here per [CMAF-TMD] --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-base64-encoded-binary-data"></a>Voorbeeldgebeurtenisstroom met aangepaste schema-id en basis64-gecodeerde binaire gegevens  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:binary">
         <Event contentEncoding="Base64">
          -- base64 encoded custom binary data message --
         </Event>
   <EventStream>
```

### <a name="example-event-stream-with-custom-schema-id-and-custom-json"></a>Voorbeeld gebeurtenisstream met aangepaste schema-id en aangepaste JSON  
```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <EventStream schemeIdUri="urn:example.org:custom:JSON">
         <Event>
          [
            {"key1" : "value1"},
            {"key2" : "value2"}
          ]
         </Event>
   <EventStream>
```

### <a name="built-in-supported-scheme-id-uris"></a>Ingebouwde ondersteunde URL's van de systeem-id
| Schema-ID URI                 | Beschrijving                                                                                                                                                                                                                                          |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| https:\//aomedia.org/emsg/ID3 | Beschrijft hoe [ID3v2] metadata kan worden uitgevoerd als getimede metadata in een CMAF-compatibele [MPEGCMAF] gefragmenteerde MP4. Zie voor meer informatie de [getimede metagegevens in de Common Media Application Format (CMAF)](https://github.com/AOMediaCodec/id3-emsg) |

### <a name="event-processing-and-manifest-signaling"></a>Gebeurtenisverwerking en manifestsignalering

Na ontvangst van een geldige **gebeurtenis "onUserDataEvent"** zoekt Azure Media Services naar een geldige XML-payload die overeenkomt met het EventStreamType (gedefinieerd in [MPEGDASH] ), de XML-payload ontleden en converteren naar een [MPEGCMAF] MP4-fragment 'emsg' versie 1 voor opslag in het live archief en verzending naar de Media Services Packager.   De Verpakker detecteert de 'emsg'-box in de livestream en:

- a) "dynamisch verpakken" in TS-segmenten voor levering aan HLS-klanten in overeenstemming met de HLS-specificatie van getimede metadata [HLS-TMD], of
- b) het door te geven voor levering in CMAF-fragmenten via HLS of DASH, of 
- (c) het om te zetten in een spaarzaam spoorsignaal voor levering via Smooth Streaming [MS-SSTR].

Naast het in-band 'emsg'-formaat CMAF- of TS PES-pakketten voor HLS, bevatten de manifesten voor DASH (MPD) en Smooth Streaming een verwijzing naar de in-band eventstreams (ook bekend als schaarse streamtrack in Smooth Streaming). 

Individuele gebeurtenissen of hun gegevenspayloads worden NIET rechtstreeks uitgevoerd in de HLS-, DASH- of Smooth-manifesten. 

### <a name="additional-informational-constraints-and-defaults-for-onuserdataevent-events"></a>Aanvullende informatieve beperkingen en standaardinstellingen voor onUserDataEvent-gebeurtenissen

- Als de tijdschaal niet is ingesteld in het element EventStream, wordt de RTMP 1 kHz-tijdschaal standaard gebruikt
- Levering van een onUserDataEvent bericht is beperkt tot eens per 500ms max. Als u vaker gebeurtenissen verzendt, kan dit gevolgen hebben voor de bandbreedte en de stabiliteit van de live feed

## <a name="212-rtmp-ad-cue-signaling-with-onadcue"></a>2.1.2 RTMP-advertentiesignaal met "onAdCue"

Azure Media Services kan luisteren en reageren op verschillende [AMF0]-berichttypen die kunnen worden gebruikt om verschillende realtime gesynchroniseerde metagegevens in de live stream te signaleren.  De [Adobe-Primetime]-specificatie definieert twee cue-typen die de "eenvoudige" en "SCTE-35"-modus worden genoemd. Voor de "eenvoudige" modus ondersteunt Media Services één AMF-bericht genaamd "onAdCue" met behulp van een payload die overeenkomt met de onderstaande tabel die is gedefinieerd voor het signaal "Eenvoudige modus".  

De volgende sectie toont RTMP "eenvoudige" modus" payload, die kan worden gebruikt om een fundamentele "spliceOut" advertentiesignaal dat zal worden uitgevoerd door naar de client manifest voor HLS, DASH, en Microsoft Smooth Streaming signaal. Dit is erg handig voor scenario's waarin de klant geen complex SCTE-35-gebaseerd advertentiesignalerings- of invoegsysteem heeft en een basis-on-premises encoder gebruikt om het cue-bericht via een API in te sturen. Typisch zal de on-premises encoder een REST-gebaseerde API ondersteunen om dit signaal te activeren, dat ook de videostream "smaakt" door een IDR-frame in de video in te voegen en een nieuwe GOP te starten.

## <a name="213--rtmp-ad-cue-signaling-with-onadcue---simple-mode"></a>2.1.3 RTMP-signaal voor advertentiesignalen met "onAdCue" - Eenvoudige modus

| Veldnaam | Veldtype | Vereist? | Beschrijvingen                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| type       | Tekenreeks     | Vereist  | Het bericht over de gebeurtenis.  Wordt "SpliceOut" om een eenvoudige modus splice aan te wijzen.                                                                                                                                                                                                         |
| id         | Tekenreeks     | Vereist  | Een unieke id die de splice of het segment beschrijft. Identificeert dit exemplaar van het bericht                                                                                                                                                                                       |
| duur   | Aantal     | Vereist  | De duur van de splice. Eenheden zijn fractionele seconden.                                                                                                                                                                                                                           |
| elapsed    | Aantal     | Optioneel  | Wanneer het signaal wordt herhaald om de aflijn te ondersteunen, is dit veld de hoeveelheid presentatietijd die is verstreken sinds het begin van de splice. Eenheden zijn fractionele seconden. Bij gebruik van de eenvoudige modus mag deze waarde de oorspronkelijke duur van de splice niet overschrijden. |
| tijd       | Aantal     | Vereist  | Zal de tijd van de splice, in presentatie tijd. Eenheden zijn fractionele seconden.                                                                                                                                                                                                |

---
 
#### <a name="example-mpeg-dash-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Voorbeeld mpeg DASH-manifestuitvoer bij gebruik van de eenvoudige Adobe RTMP-modus

Zie voorbeeld [3.3.2.1 MPEG DASH .mpd EventStream met adobe-eenvoudige modus](#3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode)

Zie voorbeeld [3.3.3.1 DASH manifest met enkele periode en Adobe eenvoudige modus](#3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals)

#### <a name="example-hls-manifest-output-when-using-adobe-rtmp-simple-mode"></a>Voorbeeld van HLS-manifestuitvoer bij het gebruik van de eenvoudige Adobe RTMP-modus

Zie voorbeeld [3.2.2 HLS-manifest met de eenvoudige Adobe-modus en DE TAG EXT-X-CUE](#322-apple-hls-with-adobe-primetime-ext-x-cue-legacy)

## <a name="214-rtmp-ad-cue-signaling-with-onadcue---scte-35-mode"></a>2.1.4 RTMP-signaal signalen voor advertentiesignalen met "onAdCue" - SCTE-35-modus

Wanneer u werkt met een meer geavanceerde broadcast productie workflow die vereist dat de volledige SCTE-35 payload bericht moet worden uitgevoerd door naar de HLS of DASH manifest, is het het beste om de "SCTE-35 Mode" van de [Adobe-Primetime] specificatie te gebruiken.  Deze modus ondersteunt in-band SCTE-35-signalen die rechtstreeks naar een on-premises live-encoder worden verzonden, die vervolgens de signalen in de RTMP-stream codeert met behulp van de "SCTE-35-modus" die is opgegeven in de [Adobe-Primetime]-specificatie. 

Meestal kunnen SCTE-35-berichten alleen worden weergegeven in MPEG-2-transportstream -ingangen (TS) op een on-premises encoder. Neem contact op met uw encoderfabrikant voor meer informatie over het configureren van een transportstream die SCTE-35 bevat en schakel deze in om door te geven aan RTMP in de Adobe SCTE-35-modus.

In dit scenario moet de volgende payload worden verzonden vanaf de on-premises encoder met behulp van het berichttype **"onAdCue"** [AMF0].

| Veldnaam | Veldtype | Vereist? | Beschrijvingen                                                                                                                                                                                                                                                                                                                                                                                                        |
| ---------- | ---------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue        | Tekenreeks     | Vereist  | Het bericht over de gebeurtenis.  Voor [SCTE-35]-berichten moet dit de basis64-gecodeerde binaire splice_info_section() zijn die de basis64-gecodeerde binaire splice_info_section() zijn om berichten naar HLS-, Smooth- en Dash-clients te kunnen verzenden.                                                                                                                                                                                                                               |
| type       | Tekenreeks     | Vereist  | Een URN of URL die het berichtschema identificeert. Voor [SCTE-35]-berichten **moet** dit **"scte35"** zijn om berichten te verzenden naar HLS-, Smooth- en Dash-clients, in overeenstemming met [Adobe-Primetime]. Optioneel kan de URN "urn:scte:scte35:2013:bin" ook worden gebruikt om een [SCTE-35] bericht te signaleren.                                                                                                        |
| id         | Tekenreeks     | Vereist  | Een unieke id die de splice of het segment beschrijft. Hiermee identificeert u dit exemplaar van het bericht.  Berichten met gelijkwaardige semantiek hebben dezelfde waarde.                                                                                                                                                                                                                                                       |
| duur   | Aantal     | Vereist  | De duur van het evenement of advertentiesplice-segment, indien bekend. Indien onbekend, **moet** de waarde 0 zijn.                                                                                                                                                                                                                                                                                                                    |
| elapsed    | Aantal     | Optioneel  | Wanneer het advertentiesignaal [SCTE-35] wordt herhaald om af te stemmen, is dit veld de hoeveelheid presentatietijd die is verstreken sinds het begin van de splice. Eenheden zijn fractionele seconden. In de modus [SCTE-35] kan deze waarde de oorspronkelijke opgegeven duur van de splice of het segment overschrijden.                                                                                                                   |
| tijd       | Aantal     | Vereist  | De presentatietijd van het evenement of de advertentiesplice.  De presentatietijd en -duur **moeten** overeenkomen met streamtoegangspunten (SAP) van type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I. Voor HLS-uitgang **moeten** tijd en duur worden afgestemd op segmentgrenzen. De presentatietijd en duur van verschillende gebeurtenisberichten binnen dezelfde gebeurtenisstream mogen elkaar niet overlappen. Eenheden zijn fractionele seconden. |

---

#### <a name="example-mpeg-dash-mpd-manifest-with-scte-35-mode"></a>Voorbeeld MPEG DASH .mpd-manifest met SCTE-35-modus
Zie [voorbeeld DASH-manifest van punt 3.3.3.2 met SCTE-35](#3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling)

#### <a name="example-hls-manifest-m3u8-with-scte-35-mode-signal"></a>Voorbeeld HLS-manifest .m3u8 met SCTE-35-modussignaal
Zie [voorbeeld HLS-manifest van punt 3.2.1.1 met SCTE-35](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35)

## <a name="215-rtmp-ad-signaling-with-oncuepoint-for-elemental-live"></a>2.1.5 RTMP-adsignalering met "onCuePoint" voor Elemental Live

De on-premises encoder van Elemental Live ondersteunt advertentiemarkeringen in het RTMP-signaal. Azure Media Services ondersteunt momenteel alleen het advertentiemarkeringstype 'onCuePoint' voor RTMP.  Dit kan worden ingeschakeld in de Adobe RTMP-groepsinstellingen in de elemental media live-encoder-instellingen of API door de "**ad_markers**" in te stellen op "onCuePoint".  Raadpleeg de Elemental Live documentatie voor meer informatie. Als u deze functie inschakelt in de RTMP-groep, worden SCTE-35-signalen doorgegeven aan de Adobe RTMP-uitvoer die door Azure Media Services moeten worden verwerkt.

Het berichttype 'onCuePoint' wordt gedefinieerd in [Adobe-Flash-AS] en heeft de volgende payloadstructuur wanneer deze wordt verzonden vanuit de Elemental Live RTMP-uitvoer.


| Eigenschap   | Beschrijving                                                                                                                                                                                                                     |
| ---------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name       | De naam MOET '**scte35**' van Elemental Live zijn.                                                                                                                                                                              |
| tijd       | De tijd in seconden waarop het actiepunt zich voordeed in het videobestand tijdens de tijdlijn                                                                                                                                           |
| type       | Het type actiepunt moet worden ingesteld op "**gebeurtenis**".                                                                                                                                                                             |
| parameters | Een associatieve array van naam/waardetekenreeksen met de informatie uit het SCTE-35-bericht, inclusief id en duur. Deze waarden worden ontleed door Azure Media Services en opgenomen in de manifestdecoratietag. |


Wanneer deze modus van advertentiemarkering wordt gebruikt, is de HLS-manifestuitvoer vergelijkbaar met de modus 'Eenvoudig' van Adobe.


#### <a name="example-mpeg-dash-mpd-single-period-adobe-simple-mode-signals"></a>Voorbeeld MPEG DASH MPD, enkele periode, Adobe Simple-modussignalen

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T18:58:03Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T17:44:47Z" minBufferTime="PT7S">
    <Period start="PT0S">
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35" timescale="10000000">
            <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
            <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
            <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
            <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
            <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
            <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
            <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
            <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
            <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
            <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
            <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D400C" maxWidth="256" maxHeight="144" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495318000000" d="64000000" r="34"/>
                    <S d="43000000"/>
                    <S d="21000000"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495318000000" type="0" wallClockTime="2020-01-07T17:59:10.957Z" presentationTime="1583495318000000"/>
            <Representation id="1_V_video_3750956353252827751" bandwidth="149952" width="256" height="144"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="scte35"/>
            <Label>ambient</Label>
            <SegmentTemplate timescale="10000000" presentationTimeOffset="1583486678426666" media="QualityLevels($Bandwidth$)/Fragments(ambient=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(ambient=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="1583495254426666" d="64000000" r="35"/>
                    <S d="43093334"/>
                    <S d="20906666"/>
                    <!-- ... Truncated for brevity of sample-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="1583495254426666" type="0" wallClockTime="2020-01-07T17:59:04.600Z" presentationTime="1583495254426666"/>
            <Representation id="5_A_ambient_9125670592623055209" bandwidth="96000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>
~~~

#### <a name="example-hls-playlist-adobe-simple-mode-signals-using-ext-x-cue-tag-truncated--for-brevity"></a>Voorbeeld HLS-afspeellijst, Adobe Simple-modussignalen met EXT-X-CUE-tag (afgekapt "..." voor beknoptheid)

In het volgende voorbeeld wordt de uitvoer van de dynamische verpakker van Media Services voor een RTMP-stream weergegeven met adobe-signalen in de "eenvoudige" modus en de oudere [Adobe-Primetime] EXT-X-CUE-tag.  

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:7
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T17:44:47Z
#EXTINF:6.400000,no-desc
Fragments(video=1583486742000000,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583486806000000,format=m3u8-aapl-v8)
...
#EXTINF:6.166667,no-desc
Fragments(video=1583487638000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667
#EXTINF:0.233333,no-desc
Fragments(video=1583487699666666,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=0.233333
#EXTINF:6.400000,no-desc
Fragments(video=1583487702000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=6.633333
#EXTINF:6.400000,no-desc
Fragments(video=1583487766000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=13.033333
#EXTINF:6.400000,no-desc
Fragments(video=1583487830000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=19.433333
#EXTINF:6.400000,no-desc
Fragments(video=1583487894000000,format=m3u8-aapl-v8)
#EXT-X-CUE:ID=95766,TYPE="SpliceOut",DURATION=30.000000,TIME=158348769.966667,ELAPSED=25.833333
#EXTINF:4.166667,no-desc
Fragments(video=1583487958000000,format=m3u8-aapl-v8)
#EXTINF:2.233333,no-desc
Fragments(video=1583487999666666,format=m3u8-aapl-v8)
#EXTINF:6.400000,no-desc
Fragments(video=1583488022000000,format=m3u8-aapl-v8)
...

~~~

### <a name="216-cancellation-and-updates"></a>2.1.6 Annulering en updates

Berichten kunnen worden geannuleerd of bijgewerkt door meerdere berichten met dezelfde presentatietijd en id te verzenden. De presentatietijd en id identificeren de gebeurtenis op unieke wijze en het laatste bericht dat is ontvangen voor een specifieke presentatietijd die voldoet aan de preroll-beperkingen, is het bericht waarop wordt gereageerd. De bijgewerkte gebeurtenis vervangt eerder ontvangen berichten. De pre-roll beperking is vier seconden. Berichten die ten minste vier seconden voor de presentatietijd zijn ontvangen, worden uitgevoerd.

## <a name="22-fragmented-mp4-ingest-smooth-streaming"></a>2.2 Gefragmenteerde MP4-inname (vloeiende streaming)

Raadpleeg [MS-SSTR-Ingest] voor vereisten voor live streaming. In de volgende secties vindt u informatie over de inname van getimede presentatiemetagegevens.  Getimede presentatiemetadata wordt ingenomen als een spaarzame track, die wordt gedefinieerd in zowel de Live Server Manifest Box (zie MS-SSTR) als de Movie Box ('moov').  

Elk schaars fragment bestaat uit een Movie Fragment Box ('moof') en Media Data Box ('mdat'), waar het vak 'mdat' de binaire boodschap is.

Om framenauwkeurige invoeging van advertenties te bereiken, moet de encoder het fragment splitsen op het tijdstip waarop de keu moet worden ingevoegd.  Er moet een nieuw fragment worden gemaakt dat begint met een nieuw IDR-frame of Stream Access Points (SAP) van type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I. Hierdoor kan de Azure Media Packager een HLS-manifest en een DASH-manifest met meerdere perioden correct genereren wanneer de nieuwe periode begint bij de framenauwkeurige weergavetijd met geconditioneerde presentatie.

### <a name="221-live-server-manifest-box"></a>2.2.1 Manifestvak liveserver

Het schaarse nummer **moet** worden gedeclareerd in het vak Live Server Manifest met een ** \<textstream-vermelding\> ** en **moet** de volgende kenmerken hebben ingesteld:

| **Kenmerknaam** | **Veldtype** | **Vereist?** | **Beschrijving**                                                                                                                                                                                                              |
| ------------------ | -------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| systemBitrate systemBitrate systemBitrate systemBit      | Aantal         | Vereist      | **MOET** "0" zijn, met vermelding van een track met onbekende, variabele bitrate.                                                                                                                                                          |
| bovenliggendeTrackName    | Tekenreeks         | Vereist      | **Moet** de naam van het bovenliggende spoor zijn, waarop de schaarse tracktijdcodes zijn uitgelijnd. Het bovenliggende spoor kan geen schaars spoor zijn.                                                                             |
| manifestOutput     | Booleaans        | Vereist      | **MOET** "waar" zijn, om aan te geven dat het schaarse spoor zal worden ingebed in het Manifest van de Smooth-client.                                                                                                                        |
| Subtype            | Tekenreeks         | Vereist      | **MOET** de vier karaktercode "DATA" zijn.                                                                                                                                                                                  |
| Schema             | Tekenreeks         | Vereist      | **Moet** een URN of URL zijn die het berichtschema identificeert. Voor [SCTE-35]-berichten **moet** dit "urn:scte:scte35:2013:bin" zijn om berichten naar HLS-, Smooth- en Dash-clients te verzenden in overeenstemming met [SCTE-35]. |
| trackName          | Tekenreeks         | Vereist      | **Moet** de naam van de schaarse track. De trackName kan worden gebruikt om meerdere gebeurtenisstromen met hetzelfde schema te onderscheiden. Elke unieke gebeurtenisstream **MOET** een unieke tracknaam hebben.                                |
| Tijdschaal          | Aantal         | Optioneel      | **Moet** de tijdschaal van het bovenliggende spoor zijn.                                                                                                                                                                               |

---

### <a name="222-movie-box"></a>2.2.2 Filmbox

De Movie Box ('moov') volgt de Live Server Manifest Box als onderdeel van de stream header voor een schaars spoor.

Het vak 'moov' **MOET** een **TrackHeaderBox ('tkhd')** bevatten zoals gedefinieerd in [ISO-14496-12] met de volgende beperkingen:

| **Veldnaam** | **Veldtype**          | **Vereist?** | **Beschrijving**                                                                                                    |
| -------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------ |
| duur       | 64-bits niet-ondertekend geheel getal | Vereist      | **MOET** 0 zijn, aangezien de track box nul monsters heeft en de totale duur van de monsters in de track box 0 is. |

---

De 'moov'-box **MOET** een **HandlerBox ('hdlr')** bevatten zoals gedefinieerd in [ISO-14496-12] met de volgende beperkingen:

| **Veldnaam** | **Veldtype**          | **Vereist?** | **Beschrijving**       |
| -------------- | ----------------------- | ------------- | --------------------- |
| handler_type   | 32-bits ongesigneerd geheel | Vereist      | **MOET** worden 'meta'. |

---

Het vak 'stsd' **moet** een metaDataSampleEntry-vak bevatten met een coderingsnaam zoals gedefinieerd in [ISO-14496-12].  Voor SCTE-35-berichten **moet** bijvoorbeeld de codeernaam 'scte' zijn.

### <a name="223-movie-fragment-box-and-media-data-box"></a>2.2.3 Film Fragment Box en Media Data Box

Schaarse trackfragmenten bestaan uit een Movie Fragment Box ('moof') en een Media Data Box ('mdat').

> [!NOTE]
> Om framenauwkeurige invoeging van advertenties te bereiken, moet de encoder het fragment splitsen op het tijdstip waarop de keu moet worden ingevoegd.  Er moet een nieuw fragment worden gemaakt dat begint met een nieuw IDR-frame of Stream Access Points (SAP) van type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I
> 

Het vak MovieFragmentBox ('moof') **moet** een **vak TrackFragmentExtendedHeaderBox ('uuid')** bevatten zoals gedefinieerd in [MS-SSTR] met de volgende velden:

| **Veldnaam**         | **Veldtype**          | **Vereist?** | **Beschrijving**                                                                                           |
| ---------------------- | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------- |
| fragment_absolute_time | 64-bits niet-ondertekend geheel getal | Vereist      | **Moet** de aankomsttijd van het evenement. Met deze waarde wordt het bericht uitgelijnd met het bovenliggende spoor.           |
| fragment_duration      | 64-bits niet-ondertekend geheel getal | Vereist      | **Moet** de duur van het evenement. De duur kan nul zijn om aan te geven dat de duur onbekend is. |

---


Het vak MediaDataBox ('mdat') **moet** de volgende indeling hebben:

| **Veldnaam**          | **Veldtype**                   | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| ----------------------- | -------------------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versie                 | 32-bits ongesigneerd geheel (uimsbf) | Vereist      | Hiermee bepaalt u de notatie van de inhoud van het vak 'mdat'. Niet-herkende versies worden genegeerd. Momenteel is de enige ondersteunde versie 1.                                                                                                                                                                                                                                                                                                                                                                      |
| id                      | 32-bits ongesigneerd geheel (uimsbf) | Vereist      | Hiermee identificeert u dit exemplaar van het bericht. Berichten met gelijkwaardige semantiek hebben dezelfde waarde; dat wil zeggen dat het verwerken van een gebeurtenisberichtvak met dezelfde id voldoende is.                                                                                                                                                                                                                                                                                                                            |
| presentation_time_delta | 32-bits ongesigneerd geheel (uimsbf) | Vereist      | De som van de fragment_absolute_time, opgegeven in de TrackFragmentExtendedHeaderBox, en de presentation_time_delta **moet** de presentatietijd van de gebeurtenis zijn. De presentatietijd en -duur **moeten** overeenkomen met streamtoegangspunten (SAP) van type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I. Voor HLS-uitgang **moeten** tijd en duur worden afgestemd op segmentgrenzen. De presentatietijd en duur van verschillende gebeurtenisberichten binnen dezelfde gebeurtenisstream **mogen** elkaar niet overlappen. |
| message                 | bytematrix                       | Vereist      | Het bericht over de gebeurtenis. Voor [SCTE-35]-berichten is het bericht de binaire splice_info_section(). Voor [SCTE-35]-berichten **moet** dit de splice_info_section() zijn om berichten naar HLS-, Smooth- en Dash-clients te kunnen verzenden in overeenstemming met [SCTE-35]. Voor [SCTE-35] berichten, de binaire splice_info_section() is de payload van de 'mdat' vak, en het is **niet** base64 gecodeerd.                                                                                                                     |

---


### <a name="224-cancellation-and-updates"></a>2.2.4 Annulering en updates

Berichten kunnen worden geannuleerd of bijgewerkt door meerdere berichten met dezelfde presentatietijd en id te verzenden.  De presentatietijd en id identificeren de gebeurtenis op unieke wijze. Het laatste bericht dat voor een specifieke presentatietijd is ontvangen en dat voldoet aan preroll-beperkingen, is het bericht waarop wordt gereageerd. Het bijgewerkte bericht vervangt eerder ontvangen berichten.  De pre-roll beperking is vier seconden. Berichten die ten minste vier seconden voor de presentatietijd zijn ontvangen, worden uitgevoerd. 


## <a name="3-timed-metadata-delivery"></a>3 Getimede metadata levering

Gebeurtenisstroomgegevens zijn ondoorzichtig voor Media Services. Media Services geeft slechts drie informatiestukken door tussen het innameeindpunt en het eindpunt van de klant. De volgende eigenschappen worden aan de klant geleverd, in overeenstemming met [SCTE-35] en/of het streamingprotocol van de klant:

1.  Schema – een URN of URL die het schema van het bericht identificeert.
2.  Presentatietijd – de presentatietijd van de gebeurtenis op de mediatijdlijn.
3.  Duur – de duur van het evenement.
4.  ID – een optionele unieke id voor de gebeurtenis.
5.  Bericht – de gebeurtenisgegevens.

## <a name="31-microsoft-smooth-streaming-manifest"></a>3.1 Microsoft Smooth Streaming Manifest  

Raadpleeg de schaarse track handling [MS-SSTR] voor meer informatie over het opmaken van een spaarzame berichttrack. Voor [SCTE35]-berichten zal Smooth Streaming de base64-gecodeerde splice_info_section() uitzetten in een schaars fragment.
Het StreamIndex **MOET** een subtype "GEGEVENS" hebben en de CustomAttributes **MOETEN** een kenmerk met name="Schema" en Value="urn:scte:scte35:2013:bin" bevatten.

#### <a name="smooth-client-manifest-example-showing-base64-encoded-scte35-splice_info_section"></a>Voorbeeld van vloeiend clientmanifest met basis64-gecodeerd [SCTE35] splice_info_section()
~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" TimeScale="10000000" IsLive="true" Duration="0"
  LookAheadFragmentCount="2" DVRWindowLength="6000000000">

  <StreamIndex Type="video" Name="video" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(video={start time})">
    <QualityLevel Index="0" Bitrate="230000"
      CodecPrivateData="250000010FC3460B50878A0B5821FF878780490800704704DC0000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <QualityLevel Index="1" Bitrate="305000"
      CodecPrivateData="250000010FC3480B50878A0B5821FF87878049080894E4A7640000010E5A67F840" FourCC="WVC1"
      MaxWidth="364" MaxHeight="272"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="audio" Name="audio" Subtype="" Chunks="0" TimeScale="10000000"
    Url="QualityLevels({bitrate})/Fragments(audio={start time})">
    <QualityLevel Index="0" Bitrate="96000" CodecPrivateData="1000030000000000000000000000E00042C0"
      FourCC="WMAP" AudioTag="354" Channels="2" SamplingRate="44100" BitsPerSample="16" PacketSize="4459"/>
    <c t="0" d="20000000" r="300" />
  </StreamIndex>
  <StreamIndex Type="text" Name="scte35-sparse-stream" Subtype="DATA" Chunks="0" TimeScale="10000000"
    ParentStreamIndex="video" ManifestOutput="true" 
    Url="QualityLevels({bitrate})/Fragments(captions={start time})">
    <QualityLevel Index="0" Bitrate="0" CodecPrivateData="" FourCC="">
      <CustomAttributes>
        <Attribute Name="Scheme" Value="urn:scte:scte35:2013:bin"/>
      </CustomAttributes>
    </QualityLevel>
    <!-- The following <c> and <f> fragments contains the base64-encoded [SCTE35] splice_info_section() message -->
    <c t="600000000" d="300000000">    <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTUwMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
    <c t="1200000000" d="400000000">      <f>PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0idXRmLTgiPz48QWNxdWlyZWRTaWduYWwgeG1sbnM9InVybjpjYWJsZWxhYnM6bWQ6eHNkOnNpZ25hbGluZzozLjAiIGFjcXVpc2l0aW9uUG9pbnRJZGVudGl0eT0iRVNQTl9FYXN0X0FjcXVpc2l0aW9uX1BvaW50XzEiIGFjcXVpc2l0aW9uU2lnbmFsSUQ9IjRBNkE5NEVFLTYyRkExMUUxQjFDQTg4MkY0ODI0MDE5QiIgYWNxdWlzaXRpb25UaW1lPSIyMDEyLTA5LTE4VDEwOjE0OjI2WiI+PFVUQ1BvaW50IHV0Y1BvaW50PSIyMDEyLTA5LTE4VDEwOjE0OjM0WiIvPjxTQ1RFMzVQb2ludERlc2NyaXB0b3Igc3BsaWNlQ29tbWFuZFR5cGU9IjUiPjxTcGxpY2VJbnNlcnQgc3BsaWNlRXZlbnRJRD0iMzQ0NTY4NjkxIiBvdXRPZk5ldHdvcmtJbmRpY2F0b3I9InRydWUiIHVuaXF1ZVByb2dyYW1JRD0iNTUzNTUiIGR1cmF0aW9uPSJQVDFNMFMiIGF2YWlsTnVtPSIxIiBhdmFpbHNFeHBlY3RlZD0iMTAiLz48L1NDVEUzNVBvaW50RGVzY3JpcHRvcj48U3RyZWFtVGltZXM+PFN0cmVhbVRpbWUgdGltZVR5cGU9IkhTUyIgdGltZVZhbHVlPSI1MTYyMDAwMDAwMDAiLz48L1N0cmVhbVRpbWVzPjwvQWNxdWlyZWRTaWduYWw+</f>
    </c>
  </StreamIndex>
</SmoothStreamingMedia>
~~~

## <a name="32-apple-hls-manifest-decoration"></a>3.2 Apple HLS Manifest Decoratie

Azure Media Services ondersteunt de volgende HLS-manifesttags voor het signaleren van informatie over advertentiegebruik tijdens een live- of on-demand-gebeurtenis. 

- EXT-X-DATERANGE zoals gedefinieerd in Apple HLS [RFC8216]
- EXT-X-CUE zoals gedefinieerd in [Adobe-Primetime] - deze modus wordt beschouwd als 'legacy'. Klanten moeten de EXT-X-DATERANGE tag waar mogelijk adopteren.

De gegevensuitvoer naar elke tag is afhankelijk van de gebruikte innamesignaalmodus. De RTMP-inname met de Adobe Simple-modus bevat bijvoorbeeld niet het volledige SCTE-35-bestandvermogen met basis64.

## <a name="321-apple-hls-with-ext-x-daterange-recommended"></a>3.2.1 Apple HLS met EXT-X-DATERANGE (aanbevolen)

De Apple HTTP Live Streaming [RFC8216] specificatie maakt het mogelijk om [SCTE-35] berichten te signaleren. De berichten worden ingevoegd in de segmentafspeellijst in een EXT-X-DATERANGE-tag per sectie [RFC8216] getiteld "SCTE-35 in kaart brengen in EXT-X-DATERANGE".  De clienttoepassingslaag kan de M3U-afspeellijst ontlasten en M3U-tags verwerken of de gebeurtenissen ontvangen via het Apple-spelersframework.  

De **aanbevolen** benadering in Azure Media Services (versie 3 API) is om [RFC8216] te volgen en de EXT-X_DATERANGE-tag te gebruiken voor [SCTE35] advertentievoorziening in het manifest.

## <a name="3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35"></a>3.2.1.1 Voorbeeld HLS-manifest .m3u8 met EXT-X-DATERANGE-signalering van SCTE-35

In het volgende voorbeeld van HLS-manifestoutput van de dynamische verpakker mediaservices wordt het gebruik van de EXT-X-DATERANGE-tag van [RFC8216] weergegeven die de SCTE-35-gebeurtenissen in de stream signaleert. Bovendien bevat deze stream de "legacy" EXT-X-CUE tag voor [Adobe-Primetime].

~~~
#EXTM3U
#EXT-X-VERSION:8
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:2
#EXT-X-INDEPENDENT-SEGMENTS
#EXT-X-PROGRAM-DATE-TIME:2020-01-07T19:40:50Z
#EXTINF:1.501500,no-desc
Fragments(video=22567545,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22702680,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22837815,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=22972950,format=m3u8-aapl-v8)
#EXTINF:1.501500,no-desc
Fragments(video=23108085,format=m3u8-aapl-v8)
#EXTINF:1.234567,no-desc
Fragments(video=23243220,format=m3u8-aapl-v8)
#EXTINF:0.016689,no-desc
Fragments(video=23354331,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.000022
#EXTINF:0.250244,no-desc
Fragments(video=23355833,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=0.250267
#EXTINF:0.850856,no-desc
Fragments(video=23378355,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.101122
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:10.610Z",SCTE35-IN=0xFC30200000000005DD00FFF00F05000003EA7F4FFE0165E4D3000101010000607CE85A
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=0.000000,TIME=260.610344,CUE="/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo="
#EXTINF:0.650644,no-desc
Fragments(video=23454932,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.751767
#EXTINF:0.050044,no-desc
Fragments(video=23513490,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=1.801811
#EXTINF:1.451456,no-desc
Fragments(video=23517994,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=3.253267
#EXTINF:1.501500,no-desc
Fragments(video=23648625,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=4.754767
#EXTINF:1.501500,no-desc
Fragments(video=23783760,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=6.256267
#EXTINF:1.501500,no-desc
Fragments(video=23918895,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=7.757767
#EXTINF:1.501500,no-desc
Fragments(video=24054030,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=9.259267
#EXTINF:1.501500,no-desc
Fragments(video=24189165,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=10.760767
#EXTINF:1.501500,no-desc
Fragments(video=24324300,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=12.262267
#EXTINF:1.501500,no-desc
Fragments(video=24459435,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=13.763767
#EXTINF:1.501500,no-desc
Fragments(video=24594570,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=15.265267
#EXTINF:1.501500,no-desc
Fragments(video=24729705,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=16.766767
#EXTINF:1.501500,no-desc
Fragments(video=24864840,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=18.268267
#EXTINF:1.501500,no-desc
Fragments(video=24999975,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=19.769767
#EXTINF:1.501500,no-desc
Fragments(video=25135110,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=21.271267
#EXTINF:1.501500,no-desc
Fragments(video=25270245,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=22.772767
#EXTINF:1.501500,no-desc
Fragments(video=25405380,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=24.274267
#EXTINF:1.501500,no-desc
Fragments(video=25540515,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=25.775767
#EXTINF:1.501500,no-desc
Fragments(video=25675650,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=27.277267
#EXTINF:1.501500,no-desc
Fragments(video=25810785,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=28.778767
#EXTINF:1.501500,no-desc
Fragments(video=25945920,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=30.280267
#EXTINF:1.501500,no-desc
Fragments(video=26081055,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=31.781767
#EXTINF:1.501500,no-desc
Fragments(video=26216190,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=33.283267
#EXTINF:1.501500,no-desc
Fragments(video=26351325,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=34.784767
#EXTINF:1.501500,no-desc
Fragments(video=26486460,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=36.286267
#EXTINF:1.501500,no-desc
Fragments(video=26621595,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=37.787767
#EXTINF:1.501500,no-desc
Fragments(video=26756730,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=39.289267
#EXTINF:1.501500,no-desc
Fragments(video=26891865,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=40.790767
#EXTINF:1.501500,no-desc
Fragments(video=27027000,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=42.292267
#EXTINF:1.501500,no-desc
Fragments(video=27162135,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=43.793767
#EXTINF:1.501500,no-desc
Fragments(video=27297270,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=45.295267
#EXTINF:1.501500,no-desc
Fragments(video=27432405,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=46.796767
#EXTINF:1.501500,no-desc
Fragments(video=27567540,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=48.298267
#EXTINF:1.501500,no-desc
Fragments(video=27702675,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=49.799767
#EXTINF:1.501500,no-desc
Fragments(video=27837810,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=51.301267
#EXTINF:1.501500,no-desc
Fragments(video=27972945,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=52.802767
#EXTINF:1.501500,no-desc
Fragments(video=28108080,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=54.304267
#EXTINF:1.501500,no-desc
Fragments(video=28243215,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=55.805767
#EXTINF:1.501500,no-desc
Fragments(video=28378350,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=57.307267
#EXTINF:1.501500,no-desc
Fragments(video=28513485,format=m3u8-aapl-v8)
#EXT-X-DATERANGE:ID="1002",START-DATE="2020-01-07T19:45:09.509Z",SCTE35-OUT=0xFC30250000000005DD00FFF01405000003EA7FEFFE016461B8FE00526363000101010000F20D5E37
#EXT-X-CUE:ID="1002",TYPE="scte35",DURATION=59.993278,TIME=259.509244,CUE="/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==",ELAPSED=58.808767
#EXTINF:1.501500,no-desc
Fragments(video=28648620,format=m3u8-aapl-v8)

~~~


## <a name="322-apple-hls-with-adobe-primetime-ext-x-cue-legacy"></a>3.2.2 Apple HLS met Adobe Primetime EXT-X-CUE (legacy)

Er is ook een "legacy"-implementatie in Azure Media Services (versie 2 en 3 API) die gebruik maakt van de EXT-X-CUE-tag zoals gedefinieerd in [Adobe-Primetime] "SCTE-35-modus". In deze modus wordt in Deze Modus base64-gecodeerd [SCTE-35] splice_info_section() in de EXT-X-CUE-tag ingesloten.  

De "legacy" EXT-X-CUE tag wordt gedefinieerd als hieronder en kan ook normatief worden verwezen in de [Adobe-Primetime] specificatie. Dit mag alleen worden gebruikt voor oudere SCTE35-signalering waar nodig, anders wordt de aanbevolen tag gedefinieerd in [RFC8216] als EXT-X-DATERANGE. 

| **Kenmerknaam** | **Type**                      | **Vereist?**                             | **Beschrijving**                                                                                                                                                                                                                                                                          |
| ------------------ | ----------------------------- | ----------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Cue                | geciteerde tekenreeks                 | Vereist                                  | Het bericht gecodeerd als een base64-gecodeerde tekenreeks zoals beschreven in [RFC4648]. Voor [SCTE-35] berichten is dit de basis64-gecodeerde splice_info_section().                                                                                                                                      |
| TYPE               | geciteerde tekenreeks                 | Vereist                                  | Een URN of URL die het berichtschema identificeert. Voor [SCTE-35]-berichten neemt het type de speciale waarde "scte35".                                                                                                                                                                          |
| Id                 | geciteerde tekenreeks                 | Vereist                                  | Een unieke id voor de gebeurtenis. Als de id niet is opgegeven wanneer het bericht wordt ingenomen, genereert Azure Media Services een unieke id.                                                                                                                                              |
| Duur           | decimale drijvende puntnummer | Vereist                                  | De duur van het evenement. Indien onbekend, **moet** de waarde 0 zijn. Eenheden zijn factieseconden.                                                                                                                                                                                           |
| Verstreken            | decimale drijvende puntnummer | Optioneel, maar vereist voor schuifvenster | Wanneer het signaal wordt herhaald om een schuivend presentatievenster te ondersteunen, **moet** dit veld de hoeveelheid presentatietijd zijn die is verstreken sinds het begin van de gebeurtenis. Eenheden zijn fractionele seconden. Deze waarde mag de oorspronkelijke opgegeven duur van de splice of het segment overschrijden. |
| TIME               | decimale drijvende puntnummer | Vereist                                  | De presentatietijd van het evenement. Eenheden zijn fractionele seconden.                                                                                                                                                                                                                        |


De toepassingslaag van DE HLS-speler gebruikt het TYPE om de indeling van het bericht te identificeren, het bericht te decoderen, de benodigde tijdconversies toe te passen en de gebeurtenis te verwerken.  De gebeurtenissen worden tijd gesynchroniseerd in de segmentafspeellijst van het bovenliggende nummer, volgens de gebeurtenistijdstempel.  Ze worden ingevoegd vóór het dichtstbijzijnde segment (#EXTINF tag).

### <a name="323-hls-m3u8-manifest-example-using-legacy-adobe-primetime-ext-x-cue"></a>3.2.3 HLS .m3u8 manifest voorbeeld met 'Legacy' Adobe Primetime EXT-X-CUE

In het volgende voorbeeld ziet u HLS-manifestdecoratie met behulp van de Adobe Primetime EXT-X-CUE-tag.  De parameter "CUE" bevat alleen de eigenschappen TYPE en Duur, wat betekent dat dit een RTMP-bron was met adobe-signaalmodus "eenvoudige" modus.  Als dit een SCTE-35-modussignaal was, zou de tag de basis64 gecodeerde binaire SCTE-35-payload bevatten zoals te zien is in het [voorbeeld 3.2.1.1](#3211-example-hls-manifest-m3u8-showing-ext-x-daterange-signaling-of-scte-35).

~~~
#EXTM3U
#EXT-X-VERSION:4
#EXT-X-PLAYLIST-TYPE:VOD
#EXT-X-ALLOW-CACHE:NO
#EXT-X-MEDIA-SEQUENCE:0
#EXT-X-TARGETDURATION:11
#EXT-X-PROGRAM-DATE-TIME:2019-12-10T09:18:14Z
#EXTINF:10.010000,no-desc
Fragments(video=4011540820,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011550830,format=m3u8-aapl)
#EXTINF:10.010000,no-desc
Fragments(video=4011560840,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000
#EXTINF:8.008000,no-desc
Fragments(video=4011570850,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=0.593000
#EXTINF:4.170000,no-desc
Fragments(video=4011578858,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=4.763000
#EXTINF:9.844000,no-desc
Fragments(video=4011583028,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=14.607000
#EXTINF:10.010000,no-desc
Fragments(video=4011592872,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=24.617000
#EXTINF:10.010000,no-desc
Fragments(video=4011602882,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=34.627000
#EXTINF:10.010000,no-desc
Fragments(video=4011612892,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=44.637000
#EXTINF:10.010000,no-desc
Fragments(video=4011622902,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=54.647000
#EXTINF:10.010000,no-desc
Fragments(video=4011632912,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=64.657000
#EXTINF:10.010000,no-desc
Fragments(video=4011642922,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=74.667000
#EXTINF:10.010000,no-desc
Fragments(video=4011652932,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=84.677000
#EXTINF:10.010000,no-desc
Fragments(video=4011662942,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=94.687000
#EXTINF:10.010000,no-desc
Fragments(video=4011672952,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=104.697000
#EXTINF:10.010000,no-desc
Fragments(video=4011682962,format=m3u8-aapl)
#EXT-X-CUE:ID=4011578265,TYPE="SpliceOut",DURATION=119.987000,TIME=4011578.265000,ELAPSED=114.707000
#EXTINF:10.010000,no-desc
Fragments(video=4011692972,format=m3u8-aapl)
#EXTINF:8.008000,no-desc
Fragments(video=4011702982,format=m3u8-aapl)

~~~

### <a name="324-hls-message-handling-for-legacy-adobe-primetime-ext-x-cue"></a>3,2,4 HLS-berichtafhandeling voor "Legacy" Adobe Primetime EXT-X-CUE

Gebeurtenissen worden gesignaleerd in de segmentafspeellijst van elke video- en audiotrack. De positie van de EXT-X-CUE-tag **moet** altijd vlak voor het eerste HLS-segment (voor splice out of segment start) of onmiddellijk na het laatste HLS-segment (voor splice in of segment einde) waarnaar de TIJD en DUUR attributen verwijzen, zoals vereist door [Adobe-Primetime].

Wanneer een schuivend presentatievenster is ingeschakeld, **moet** de EXT-X-CUE-tag vaak genoeg worden herhaald dat de splice of het segment altijd volledig wordt beschreven in de segmentafspeellijst en moet het **kenmerk** ELAPSED worden gebruikt om aan te geven hoe lang de splice of het segment actief is geweest, zoals vereist door [Adobe-Primetime].

Wanneer een schuivend presentatievenster is ingeschakeld, worden de EXT-X-CUE-tags uit de segmentafspeellijst verwijderd wanneer de mediatijd waarnaar ze verwijzen uit het schuivende presentatievenster is gerold.

## <a name="33-dash-manifest-decoration-mpd"></a>3.3 DASH Manifest Decoratie (MPD)

[MPEGDASH] biedt drie manieren om gebeurtenissen te signaleren:

1.  Gebeurtenissen gesignaleerd in de MPD EventStream
2.  Gebeurtenissen gesignaleerd in de band met behulp van de Event Message Box ('emsg')
3.  Een combinatie van zowel 1 als 2

Gebeurtenissen die worden gesignaleerd in de MPD EventStream zijn handig voor VOD-streaming omdat clients toegang hebben tot alle gebeurtenissen, onmiddellijk wanneer de MPD wordt gedownload. Het is ook handig voor SSAI-signalering, waarbij de downstream SSAI-leverancier de signalen van een MPD-manifest met meerdere perioden moet ontleden en advertentie-inhoud dynamisch moet invoegen.  De in-band ('emsg')-oplossing is handig voor live streaming waarbij clients de MPD niet opnieuw hoeven te downloaden, of er geen SSAI manifeste manipulatie plaatsvindt tussen de client en de oorsprong. 

Standaardgedrag azure Media Services voor DASH is het signaleren van zowel de MPD EventStream als de in-band via het Gebeurtenisberichtvak ('emsg').

Cue-berichten die via [RTMP] of [MS-SSTR-Ingest] worden ingenomen, worden toegewezen aan DASH-gebeurtenissen, met behulp van in-band 'emsg'-vakken en/of in-MPD EventStreams. 

In-band SCTE-35 signalering voor DASH volgt de definitie en eisen die zijn gedefinieerd in [SCTE-214-3] en ook in [DASH-IF-IOP] sectie 13.12.2 ('SCTE35 Events'). 

Voor in-band [SCTE-35] vervoer, het event bericht vak ('emsg') maakt gebruik van de regelingId = "urn:scte:scte35:2013:bin". Voor MPD manifest decoratie gebruikt het EventStream schemeId "urn:scte:scte35:2014:xml+bin".  Deze indeling is een XML-weergave van de gebeurtenis die een binaire base64-gecodeerde uitvoer van het volledige SCTE-35-bericht bevat dat is aangekomen. 

Normatieve referentiedefinities voor het vervoer van [SCTE-35] cue-berichten in DASH zijn beschikbaar in [SCTE-214-1] sec 6.7.4 (MPD) en [SCTE-214-3] sec 7.3.2 (Vervoer van SCTE 35-berichten).

### <a name="331-mpeg-dash-mpd-eventstream-signaling"></a>3.3.1 MPEG DASH (MPD) EventStream-signalering

Manifest (MPD) decoratie van gebeurtenissen zal worden gesignaleerd in de MPD met behulp van de EventStream element, die wordt weergegeven binnen de periode element. Het gebruikte schemaId is "urn:scte:scte35:2014:xml+bin".

> [!NOTE]
> Voor beknoptheidsdoeleinden [SCTE-35] maakt het gebruik van de base64-gecodeerde sectie in Signal.Binary element (in plaats van de Signal.SpliceInfoSection element) als een alternatief voor het vervoer van een volledig ontleed cue bericht.
> Azure Media Services gebruikt deze 'xml+bin'-benadering voor signalering in het MPD-manifest.
> Dit is ook de aanbevolen methode die wordt gebruikt in de [DASH-IF-IOP] - zie sectie met de titel ['Ad insertion event streams' van de DASH IF IOP-richtlijn](https://dashif-documents.azurewebsites.net/DASH-IF-IOP/master/DASH-IF-IOP.html#ads-insertion-event-streams)
> 

Het element EventStream heeft de volgende kenmerken:

| **Kenmerknaam** | **Type**                | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                                                                                                         |
| ------------------ | ----------------------- | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| scheme_id_uri      | tekenreeks                  | Vereist      | Hiermee wordt het schema van het bericht aangegeven. Het schema is ingesteld op de waarde van het kenmerk Scheme in het vak Live Server Manifest. De waarde **moet** een URN of URL zijn die het berichtschema identificeert; De ondersteunde uitvoerschemeId moet "urn:scte:scte35:2014:xml+bin" per [SCTE-214-1] sec 6.7.4 (MPD) zijn, omdat de service op dit moment alleen "xml+bin" ondersteunt voor beknoptheid in de MPD. |
| waarde              | tekenreeks                  | Optioneel      | Een extra tekenreekswaarde die door de eigenaren van het schema wordt gebruikt om de semantiek van het bericht aan te passen. Om meerdere gebeurtenisstromen met hetzelfde schema te differentiëren, **moet** de waarde worden ingesteld op de naam van de gebeurtenisstream (trackName voor [MS-SSTR-Ingest] of AMF-berichtnaam voor [RTMP] inname).                                                                         |
| Tijdschaal          | 32-bits ongesigneerd geheel | Vereist      | De tijdschaal, in teken per seconde.                                                                                                                                                                                                                                                                                                                                                     |


### <a name="332-example-event-streams-for-mpeg-dash"></a>3.3.2 Voorbeeldgebeurtenisstreams voor MPEG DASH

#### <a name="3321-example-mpeg-dash-mpd-manifest-signaling-of-rtmp-streaming-using-adobe-simple-mode"></a>3.3.2.1 Voorbeeld MPEG DASH .mpd manifest signaal van RTMP streaming met behulp van Adobe eenvoudige modus

In het volgende voorbeeld wordt een fragment EventStream van de dynamische verpakker van Media Services weergegeven voor een RTMP-stream met behulp van Adobe 'eenvoudige' signaalmodus.

~~~ xml
<!-- Example EventStream element using "urn:com:adobe:dpi:simple:2015" Adobe simple signaling per [Adobe-Primetime] -->
    <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="10000000">
        <Event presentationTime="1583497601000000" duration="300000000" id="1085900"/>
        <Event presentationTime="1583500901666666" duration="300000000" id="1415966"/>
        <Event presentationTime="1583504202333333" duration="300000000" id="1746033"/>
        <Event presentationTime="1583507502666666" duration="300000000" id="2076066"/>
        <Event presentationTime="1583510803333333" duration="300000000" id="2406133"/>
        <Event presentationTime="1583514104000000" duration="300000000" id="2736200"/>
        <Event presentationTime="1583517404666666" duration="300000000" id="3066266"/>
        <Event presentationTime="1583520705333333" duration="300000000" id="3396333"/>
        <Event presentationTime="1583524006000000" duration="300000000" id="3726400"/>
        <Event presentationTime="1583527306666666" duration="300000000" id="4056466"/>
        <Event presentationTime="1583530607333333" duration="300000000" id="4386533"/>
    </EventStream>
~~~

#### <a name="3322-example-mpeg-dash-mpd-manifest-signaling-of-an-rtmp-stream-using-adobe-scte-35-mode"></a>3.3.2.2 Voorbeeld MPEG DASH .mpd-manifestsignalering van een RTMP-stream met adobe SCTE-35-modus

In het volgende voorbeeld wordt een fragment EventStream van de dynamische verpakker van Media Services weergegeven voor een RTMP-stream met adobe SCTE-35-modussignalering.

~~~ xml
<!-- Example EventStream element using xml+bin style signaling per [SCTE-214-1] -->

      <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
        <Event presentationTime="2595092444" duration="11011000" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
            </Signal>
        </Event>
        <Event presentationTime="2606103444" id="1002">
            <Signal xmlns="http://www.scte.org/schemas/35/2016">
                <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
            </Signal>
        </Event>
      </EventStream>
~~~

> [!IMPORTANT]
> Houd er rekening mee dat presentatieTijd de presentatietijd is van de gebeurtenis [SCTE-35] die is vertaald als relatief ten opzichte van de begintijd van de periode, niet de aankomsttijd van het bericht.
> [MPEGDASH] definieert de Event@presentationTime als "Geeft de presentatietijd van de gebeurtenis ten opzichte van het begin van de periode op.
> De waarde van de presentatietijd in seconden is de verdeling van EventStream@timescale de waarde van dit kenmerk en de waarde van het kenmerk.
> Als deze niet aanwezig is, is de waarde van de presentatietijd 0.

#### <a name="3331-example-mpeg-dash-manifest-mpd-with-single-period-eventstream-using-adobe-simple-mode-signals"></a>3.3.3.1 Voorbeeld MPEG DASH manifest (MPD) met single-period, EventStream, met adobe-signalen voor de eenvoudige modus

In het volgende voorbeeld wordt de uitvoer van de dynamische verpakker van Media Services voor een bronRTMP-stream weergegeven met behulp van de advertentiesignaalmethode van Adobe in de modus "eenvoudige" modus. De uitvoer is een manifest met één periode met een EventStream met behulp van de schemeId Uri ingesteld op "urn:com:adobe:dpi:simple:2015" en waarde eigenschap ingesteld op "simplesignal".
Elk eenvoudig signaal wordt geleverd in @presentationTime @durationeen @id gebeurteniselement met de , en eigenschappen die worden ingevuld op basis van de binnenkomende eenvoudige signalen.

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="static" mediaPresentationDuration="PT28M1.680S" minBufferTime="PT3S">
    <Period>
        <EventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal" timescale="1000">
            <Event presentationTime="4011578265" duration="119987" id="4011578265"/>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.4D4028" maxWidth="1920" maxHeight="1080" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460740" type="0" wallClockTime="2020-01-25T19:35:54.740Z" presentationTime="4011460740"/>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460740" d="2002" r="57"/>
                    <S d="1401"/>
                    <S d="601"/>
                    <S d="2002"/>

                     <!--> ... video segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="1_V_video_14759481473095519504" bandwidth="6000000" width="1920" height="1080"/>
            <Representation id="1_V_video_1516803357996956148" bandwidth="4000000" codecs="avc1.4D401F" width="1280" height="720"/>
            <Representation id="1_V_video_5430608182379669372" bandwidth="2600000" codecs="avc1.4D401F" width="960" height="540"/>
            <Representation id="1_V_video_3780180650986497347" bandwidth="1000000" codecs="avc1.4D401E" width="640" height="360"/>
            <Representation id="1_V_video_13759117363700265707" bandwidth="699000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_6140004908920393176" bandwidth="400000" codecs="avc1.4D4015" width="480" height="270"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.4D400D" width="320" height="180"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.2">
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="simplesignal"/>
            <ProducerReferenceTime id="4011460761" type="0" wallClockTime="2020-01-25T19:35:54.761Z" presentationTime="4011460761"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="1000" presentationTimeOffset="4011460740" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="4011460761" d="1984"/>
                    <S d="2005" r="1"/>
                    <S d="2006"/>

                    <!--> ... audio segments truncated for example brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="48000"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~

#### <a name="3332-example-mpeg-dash-manifest-mpd-with-multi-period-eventstream-using-adobe-scte35-mode-signaling"></a>3.3.3.2 Voorbeeld MPEG DASH manifest (MPD) met multi-period, EventStream, met adobe SCTE35-modus signalering

In het volgende voorbeeld wordt de uitvoer van de dynamische verpakker van Media Services voor een bronRTMP-stream weergegeven met behulp van de signalering in de Adobe SCTE35-modus.
In dit geval is het uitvoermanifest een MEERjarige DASH .mpd @schemeIdUri met een EventStream-element en eigenschap ingesteld op "urn:scte:scte35:2014:xml+bin" en een @value eigenschap die is ingesteld op "scte35". Elk gebeurteniselement in de EventStream bevat het volledige basis64-gecodeerde binaire SCTE35-signaal 

~~~ xml
<?xml version="1.0" encoding="utf-8"?>
<MPD xmlns="urn:mpeg:dash:schema:mpd:2011" 
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" profiles="urn:mpeg:dash:profile:isoff-live:2011" type="dynamic" publishTime="2020-01-07T19:42:44Z" minimumUpdatePeriod="PT0S" timeShiftBufferDepth="PT58M56S" availabilityStartTime="2020-01-07T19:40:50Z" minBufferTime="PT4S">
    <Period start="PT2M48.168S" id="main-content_0">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="15135120" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="15135120" d="135135" r="59"/>
                    <S d="111111"/>
                    <S d="1502"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="15135120" type="0" wallClockTime="2020-01-07T19:40:50Z" presentationTime="15135120"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="7416208" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="7417856" d="133120"/>
                    <S d="132096" r="1"/>
                    
                    <!--> ... aduio segments truncated for sample brevity </-->

                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="7417856" type="0" wallClockTime="2020-01-07T19:40:50.037Z" presentationTime="7417856"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M19.509S" id="scte-35_0">
        <EventStream schemeIdUri="urn:scte:scte35:2014:xml+bin" value="scte35" timescale="10000000">
            <Event presentationTime="2595092444" duration="11011000" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAlAAAAAAXdAP/wFAUAAAPqf+/+AWRhuP4AUmNjAAEBAQAA8g1eNw==</Binary>
                </Signal>
            </Event>
            <Event presentationTime="2606103444" id="1002">
                <Signal xmlns="http://www.scte.org/schemas/35/2016">
                    <Binary>/DAgAAAAAAXdAP/wDwUAAAPqf0/+AWXk0wABAQEAAGB86Fo=</Binary>
                </Signal>
            </Event>
        </EventStream>
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23355832" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23355833" d="22522"/>
                    <S d="76577"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23355833" type="0" wallClockTime="2020-01-07T19:42:21.341Z" presentationTime="23355833"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11444358" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11446272" d="49152"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11446272" type="0" wallClockTime="2020-01-07T19:42:21.384Z" presentationTime="11446272"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
    <Period start="PT4M20.610S" id="main-content_1">
        <AdaptationSet id="1" group="1" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="video" mimeType="video/mp4" codecs="avc1.640020" maxWidth="1280" maxHeight="720" startWithSAP="1">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <SegmentTemplate timescale="90000" presentationTimeOffset="23454931" media="QualityLevels($Bandwidth$)/Fragments(video=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(video=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="23454932" d="58558"/>
                    <S d="4504"/>
                    <S d="130631"/>
                    <S d="135135" r="12"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="23454932" type="0" wallClockTime="2020-01-07T19:42:22.442Z" presentationTime="23454932"/>
            <Representation id="1_V_video_5322324134428436312" bandwidth="3500000" width="1280" height="720"/>
            <Representation id="1_V_video_16981495139092747609" bandwidth="2200000" width="960" height="540"/>
            <Representation id="1_V_video_1384718563016940751" bandwidth="1350000" codecs="avc1.64001F" width="704" height="396"/>
            <Representation id="1_V_video_4425970933904124207" bandwidth="850000" codecs="avc1.64001E" width="512" height="288"/>
            <Representation id="1_V_video_11952982975776937431" bandwidth="550000" codecs="avc1.640016" width="384" height="216"/>
            <Representation id="1_V_video_10673801877453424365" bandwidth="200000" codecs="avc1.640015" width="340" height="192"/>
        </AdaptationSet>
        <AdaptationSet id="2" group="5" profiles="ccff" bitstreamSwitching="false" segmentAlignment="true" contentType="audio" mimeType="audio/mp4" codecs="mp4a.40.5" lang="en">
            <InbandEventStream schemeIdUri="urn:mpeg:dash:event:2012" value="1"/>
            <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
            <Label>audio</Label>
            <SegmentTemplate timescale="44100" presentationTimeOffset="11492916" media="QualityLevels($Bandwidth$)/Fragments(audio=$Time$,format=mpd-time-csf)" initialization="QualityLevels($Bandwidth$)/Fragments(audio=i,format=mpd-time-csf)">
                <SegmentTimeline>
                    <S t="11495424" d="28672"/>
                    <S d="1024"/>
                    <S d="131072"/>
                    <S d="132096"/>
                    <S d="133120"/>
                    <S d="132096" r="1"/>
                    <S d="133120"/>
                </SegmentTimeline>
            </SegmentTemplate>
            <ProducerReferenceTime id="11495424" type="0" wallClockTime="2020-01-07T19:42:22.499Z" presentationTime="11495424"/>
            <Representation id="5_A_audio_17504386117102112482" bandwidth="128000" audioSamplingRate="44100"/>
        </AdaptationSet>
    </Period>
</MPD>

~~~
### <a name="334-mpeg-dash-in-band-event-message-box-signaling"></a>3.3.4 MPEG DASH In-band Event Message Message Ing

Een in-band gebeurtenisstream vereist dat de MPD een InbandEventStream-element heeft op het niveau van de aanpassingsset.  Dit element heeft een verplicht kenmerk iduri en een optioneel tijdschaalkenmerk, dat ook in het gebeurtenisberichtvak ('emsg') wordt weergegeven.  Gebeurtenisberichtvakken met schema-id's die niet zijn gedefinieerd in de **MPD, mogen** niet aanwezig zijn.

Voor in-band [SCTE-35] vervoer, **moeten** de signalen de schemeId = "urn:scte:scte35:2013:bin" gebruiken.
Normatieve definities van het vervoer van [SCTE-35] in-band berichten worden gedefinieerd in [SCTE-214-3] sec 7.3.2 (Vervoer van SCTE 35 cue berichten).

De volgende details geven de specifieke waarden aan die de klant in de emsg mag verwachten in overeenstemming met [SCTE-214-3]:

| **Veldnaam**          | **Veldtype**          | **Vereist?** | **Beschrijving**                                                                                                                                                                                                                                                                                        |
| ----------------------- | ----------------------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| scheme_id_uri           | tekenreeks                  | Vereist      | Hiermee wordt het schema van het bericht aangegeven. Het schema is ingesteld op de waarde van het kenmerk Scheme in het vak Live Server Manifest. De waarde **moet** een URN zijn die het berichtschema identificeert. Voor [SCTE-35] berichten **moet** dit "urn:scte:scte35:2013:bin" zijn in overeenstemming met [SCTE-214-3]          |
| Waarde                   | tekenreeks                  | Vereist      | Een extra tekenreekswaarde die door de eigenaren van het schema wordt gebruikt om de semantiek van het bericht aan te passen. Om meerdere gebeurtenisstromen met hetzelfde schema te differentiëren, wordt de waarde ingesteld op de naam van de gebeurtenisstream (trackName voor Vloeiend innemen of AMF-berichtnaam voor RTMP-inname). |
| Tijdschaal               | 32-bits ongesigneerd geheel | Vereist      | De tijdschaal, in teken per seconde, van de tijds- en duurvelden binnen het vak 'emsg'.                                                                                                                                                                                                            |
| Presentation_time_delta | 32-bits ongesigneerd geheel | Vereist      | De mediapresentatietijd delta van de presentatietijd van de gebeurtenis en de vroegste presentatietijd in dit segment. De presentatietijd en -duur **moeten** overeenkomen met streamtoegangspunten (SAP) van type 1 of 2, zoals gedefinieerd in [ISO-14496-12] bijlage I.                                  |
| event_duration          | 32-bits ongesigneerd geheel | Vereist      | De duur van de gebeurtenis, of 0xFFFFF OM een onbekende duur aan te geven.                                                                                                                                                                                                                              |
| Id                      | 32-bits ongesigneerd geheel | Vereist      | Hiermee identificeert u dit exemplaar van het bericht. Berichten met gelijkwaardige semantiek hebben dezelfde waarde. Als de id niet is opgegeven wanneer het bericht wordt ingenomen, genereert Azure Media Services een unieke id.                                                                                        |
| Message_data            | bytematrix              | Vereist      | Het bericht over de gebeurtenis. Voor [SCTE-35]-berichten zijn de berichtgegevens de binaire splice_info_section() in overeenstemming met [SCTE-214-3]                                                                                                                                                                        |


#### <a name="example-inbandevenstream-entity-for-adobe-simple-mode"></a>Voorbeeld van de entiteit InBandEvenStream voor de eenvoudige Adobe-modus
~~~ xml

      <InbandEventStream schemeIdUri="urn:com:adobe:dpi:simple:2015" value="amssignal"/>
~~~

### <a name="335-dash-message-handling"></a>3.3.5 DASH-berichtafhandeling

Evenementen worden gesignaleerd in-band, binnen de 'emsg' box, voor zowel video-en audio tracks.  De signalering vindt plaats voor alle segmentaanvragen waarvoor de presentation_time_delta 15 seconden of minder is. 

Wanneer een schuivend presentatievenster is ingeschakeld, worden gebeurtenisberichten uit de MPD verwijderd wanneer de som van de tijd en duur van het gebeurtenisbericht kleiner is dan de tijd van de mediagegevens in het manifest.  Met andere woorden, de gebeurtenisberichten worden uit het manifest verwijderd wanneer de mediatijd waarnaar ze verwijzen uit het schuifpresentatievenster is gerold.

## <a name="4-scte-35-ingest-implementation-guidance-for-encoder-vendors"></a>4. SCTE-35 Implementatierichtlijnen voor encoderleveranciers

De volgende richtlijnen zijn veelvoorkomende problemen die van invloed kunnen zijn op de implementatie van deze specificatie door een encoderleverancier.  De onderstaande richtlijnen zijn verzameld op basis van feedback van partners uit de echte wereld om het gemakkelijker te maken om deze specificatie voor anderen te implementeren. 

[SCTE-35] berichten worden ingenomen in binaire indeling met behulp van de regeling **"urn: scte: scte35:2013:bin"** voor [MS-SSTR-Inest] en het type **"scte35"** voor [RTMP] inname. Om de omzetting van [SCTE-35] timing, die is gebaseerd op MPEG-2 transport stream presentatie tijdstempels (PTS) te vergemakkelijken, wordt een mapping tussen PTS (pts_time + pts_adjustment van de splice_time()) en de media tijdlijn verstrekt door de gebeurtenis presentatie tijd ( het veld fragment_absolute_time voor Vloeiend innemen en het tijdveld voor RTMP-inname). De mapping is noodzakelijk omdat de 33-bits PTS-waarde ongeveer elke 26,5 uur overrolt.

Smooth Streaming [MS-SSTR-Inest] vereist dat de Media Data Box ('mdat') **MOET** de **splice_info_section()** bevatten die is gedefinieerd in [SCTE-35]. 

Voor RTMP-inname wordt het cue-kenmerk van het AMF-bericht ingesteld op de basis64-gecodeerde **splice_info_section()** die is gedefinieerd in [SCTE-35].  

Wanneer de berichten de hierboven beschreven indeling hebben, worden ze verzonden naar HLS-, Smooth- en DASH-clients zoals hierboven gedefinieerd.  

Wanneer u uw implementatie test met het Azure Media Services-platform, begint u eerst met het testen met een 'pass-through' LiveEvent, voordat u overgaat tot het testen op een coderingsliveevent.

---

## <a name="change-history"></a>Wijzigingsoverzicht

| Date     | Wijzigingen                                                                                                             |
| -------- | ------------------------------------------------------------------------------------------------------------------- |
| 07/2/19  | Herziene RTMP-inname voor SCTE35-ondersteuning, RTMP "onCuePoint" toegevoegd voor Elemental Live                                  |
| 08/22/19 | Bijgewerkt om OnUserDataEvent toe te voegen aan RTMP voor aangepaste metagegevens                                                          |
| 1/08/20  | Fixed error on RTMP Simple and RTMP SCTE35 mode. Gewijzigd van "onCuePoint" naar "onAdCue". Tabel Met eenvoudige modus bijgewerkt. |

## <a name="next-steps"></a>Volgende stappen
Leerpaden van Media Services weergeven.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
