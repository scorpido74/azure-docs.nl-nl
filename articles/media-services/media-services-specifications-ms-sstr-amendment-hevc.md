---
title: Wijziging van het Smooth Streaming Protocol (MS-SSTR) voor HEVC - Azure
description: Deze specificatie beschrijft het protocol en de indeling voor gefragmenteerde MP4-gebaseerde live streaming met HEVC in Azure Media Services. Alleen de wijzigingen die nodig zijn om HEVC te leveren zijn in dit artikel opgegeven, behalve als "(Geen wijziging)" aangeeft dat tekst alleen voor verduidelijking wordt gekopieerd.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: f27d85de-2cb8-4269-8eed-2efb566ca2c6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: johndeu
ms.openlocfilehash: be4009d418f2f8f3dff755e2e990efee593f070b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514218"
---
# <a name="smooth-streaming-protocol-ms-sstr-amendment-for-hevc"></a>Smooth Streaming Protocol (MS-SSTR) Wijziging voor HEVC 

## <a name="1-introduction"></a>1 Inleiding 

Dit artikel bevat gedetailleerde wijzigingen die moeten worden toegepast op de specificatie van het Smooth Streaming Protocol [MS-SSTR] om vloeiende streaming van HEVC-gecodeerde video mogelijk te maken. In deze specificatie geven we alleen een overzicht van de wijzigingen die nodig zijn om de HEVC-videocodec te leveren. Het artikel volgt hetzelfde nummeringsschema als de [MS-SSTR]-specificatie. De lege koppen die in het artikel worden gepresenteerd, worden verstrekt om de lezer te oriënteren op hun positie in de [MS-SSTR] specificatie.  "(Geen wijziging)" geeft aan dat tekst alleen voor verduidelijkingsdoeleinden wordt gekopieerd.

Het artikel bevat technische implementatievereisten voor het signaleren van HEVC-videocodec (met behulp van 'hev1' of 'hvc1'-formaattracks) in een Smooth Streaming-manifest en normatieve verwijzingen worden bijgewerkt om te verwijzen naar de huidige MPEG-normen die omvatten HEVC, Common Encryption van HEVC, en vaknamen voor ISO Base Media File Format zijn bijgewerkt om in overeenstemming te zijn met de nieuwste specificaties. 

De specificatie van het Smooth Streaming Protocol [MS-SSTR] wordt beschreven in het draadformaat dat wordt gebruikt om live en on-demand digitale media, zoals audio en video, op de volgende manieren te leveren: van een encoder tot een webserver, van een server naar een andere server en van een server naar een HTTP-client.
Het gebruik van een MPEG-4 ([[MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787)-gebaseerde datastructuur levering via HTTP maakt naadloos schakelen in de buurt van real time tussen de verschillende kwaliteitsniveaus van gecomprimeerde media-inhoud. Het resultaat is een constante afspeelervaring voor de UITEINDELIJKe HTTP-clientgebruiker, zelfs als de omstandigheden voor netwerk- en videoweergave voor de clientcomputer of het apparaat veranderen.

## <a name="11-glossary"></a>1.1 Woordenlijst 

De volgende termen zijn gedefinieerd in *[MS-GLOS]*:

>   **guid (globally unique identifier) universeel unieke id (UUID)**

De volgende voorwaarden zijn specifiek voor dit document:

>  **samenstellingstijd:** De tijd dat een monster bij de klant wordt gepresenteerd, zoals gedefinieerd in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695).
> 
>   **CENC**: Common Encryption, zoals gedefinieerd in [ISO/IEC 23001-7] Second Edition.
> 
>   **decodetijd:** De tijd die een monster moet decoderen op de client, zoals gedefinieerd in [[ISO/IEC 14496-12:2008]](https://go.microsoft.com/fwlink/?LinkId=183695).

**fragment:** Een onafhankelijk downloadbare eenheid **van media** die bestaat uit een of meer **monsters**.

>   **HEVC:** Videocodering met hoge efficiëntie, zoals gedefinieerd in [ISO/IEC 23008-2]
> 
>   **manifest:** Metagegevens over de **presentatie** waarmee een client verzoeken om media kan **indienen.** **media:** Gecomprimeerde audio-, video- en tekstgegevens die door de client worden gebruikt om een **presentatie**af te spelen. **mediaformaat:** Een goed gedefinieerde indeling voor het weergeven van audio of video als een gecomprimeerd **voorbeeld.**
> 
>   **presentatie:** De set van alle **streams** en gerelateerde metadata die nodig zijn om een enkele film af te spelen. **verzoek:** Een HTTP-bericht dat van de client naar de server wordt verzonden, zoals gedefinieerd in [[RFC2616]-antwoord:](https://go.microsoft.com/fwlink/?LinkId=90372) **response:** een HTTP-bericht dat van de server naar de client wordt verzonden, zoals gedefinieerd in [[RFC2616]](https://go.microsoft.com/fwlink/?LinkId=90372)
> 
>   **monster:** De kleinste fundamentele eenheid (zoals een frame) waarin **media** worden opgeslagen en verwerkt.
> 
>   **KAN, MOET, MOET, MOET NIET, MAG NIET:** Deze termen (in alle hoofdletters) worden gebruikt zoals beschreven in [[RFC2119]](https://go.microsoft.com/fwlink/?LinkId=90317) Alle verklaringen van optioneel gedrag gebruiken MEI, MOET of NIET.

## <a name="12-references"></a>1.2 Referenties

>   Verwijzingen naar Microsoft Open Specifications documentatie bevatten geen publicatiejaar, omdat links zijn naar de nieuwste versie van de documenten, die regelmatig worden bijgewerkt. Verwijzingen naar andere documenten omvatten een publicatiejaar waarin er een beschikbaar is.

### <a name="121-normative-references"></a>1.2.1 Normatieve referenties 

>  [MS-SSTR] Smooth Streaming Protocol *v20140502*[https://msdn.microsoft.com/library/ff469518.aspx](https://msdn.microsoft.com/library/ff469518.aspx)
> 
>   [ISO/IEC 14496-12] Internationale Organisatie voor Normalisatie, "Informatietechnologie -- Codering van audiovisuele objecten -- Deel 12: ISO Base Media File Format", ISO/IEC 14496-12:2014, Edition 4, Plus Corrigendum 1, Amendementen 1 & 2.
>   <https://standards.iso.org/ittf/PubliclyAvailableStandards/c061988_ISO_IEC_14496-12_2012.zip>
> 
>   [ISO/IEC 14496-15] International Organization for Standardization, "Information technology -- Coding of audio-visual objects -- Part 15: Carriage of NAL unit structured video in the ISO Base Media File Format", ISO 14496-15:2015, Edition 3.
>   <https://www.iso.org/iso/home/store/catalogue_tc/catalogue_detail.htm?csnumber=65216>
> 
>   [ISO/IEC 23008-2] Informatietechnologie -- High efficiency coding en media delivery in heterogene omgevingen -- Deel 2: High efficiency video coding: 2013 of nieuwste editie<https://standards.iso.org/ittf/PubliclyAvailableStandards/c035424_ISO_IEC_23008-2_2013.zip>
> 
>   [ISO/IEC 23001-7] Informatietechnologie — MPEG-systeemtechnologieën — Deel 7: Algemene versleuteling in ISO-bestandsbestanden, CENC Edition 2:2015<https://www.iso.org/iso/catalogue_detail.htm?csnumber=65271>
> 
>   [RFC-6381] IETF RFC-6381, "De 'Codecs' en 'Profiles' Parameters voor "Bucket" Media Types"<https://tools.ietf.org/html/rfc6381>
> 
>   [MPEG4-RA] De MP4-registratieautoriteit, "MP4REG",[http://www.mp4ra.org](https://go.microsoft.com/fwlink/?LinkId=327787)
> 
>   [RFC2119] Bradner, S., "Trefwoorden voor gebruik in RFC's om vereisteniveaus aan te geven", BCP 14, RFC 2119, maart 1997,[https://www.rfc-editor.org/rfc/rfc2119.txt](https://go.microsoft.com/fwlink/?LinkId=90317)

### <a name="122-informative-references"></a>1.2.2 Informatieve referenties 

>   [MS-GLOS] Microsoft Corporation, "*Windows Protocollen Master Woordenlijst*."
> 
>   [RFC3548] Josefsson, S., Ed., "The Base16, Base32, and Base64 Data Encodings", RFC 3548, juli 2003,[https://www.ietf.org/rfc/rfc3548.txt](https://go.microsoft.com/fwlink/?LinkId=90432)
> 
>   [RFC5234] Crocker, D., Ed., and Overell, P., "Augmented BNF for Syntax Specifications: ABNF", STD 68, RFC 5234, januari 2008,[https://www.rfc-editor.org/rfc/rfc5234.txt](https://go.microsoft.com/fwlink/?LinkId=123096)


## <a name="13-overview"></a>Overzicht 1.3 

>   Hieronder worden alleen wijzigingen in de smooth streaming-specificatie opgegeven die vereist zijn voor de levering van HEVC. Ongewijzigde sectiekoppen worden weergegeven om de locatie te behouden in de standaardspecificatie vloeiend streamen [MS-SSTR].

## <a name="14-relationship-to-other-protocols"></a>1.4 Relatie tot andere protocollen 

## <a name="15-prerequisitespreconditions"></a>1.5 Voorwaarden/Randvoorwaarden 

## <a name="16-applicability-statement"></a>1.6 Toepasselijkheidsverklaring 

## <a name="17-versioning-and-capability-negotiation"></a>1.7 Onderhandelingen over versies en mogelijkheden 

## <a name="18-vendor-extensible-fields"></a>1.8 Velden met leveranciersuitbreidbaar 

>   De volgende methode moet worden gebruikt om streams te identificeren met behulp van het HEVC-videoformaat:
> 
>   * **Aangepaste beschrijvende codes voor media-indelingen:** Deze mogelijkheid wordt geleverd door het veld **FourCC,** zoals gespecificeerd in punt *2.2.2.5*.
>   Uitvoerders kunnen ervoor zorgen dat extensies niet in conflict komen door extensiecodes te registreren met de MPEG4-RA, zoals gespecificeerd in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695)

## <a name="19-standards-assignments"></a>1.9 Standaardtoewijzingen 

## <a name="2-messages"></a>2 Berichten 

## <a name="21-transport"></a>2.1 Vervoer

## <a name="22-message-syntax"></a>2.2 Berichtsyntaxis 

### <a name="221-manifest-request"></a>2.2.1 Manifest verzoek 

### <a name="222-manifest-response"></a>2.2.2 Manifestrespons 

#### <a name="2221-smoothstreamingmedia"></a>2.2.2.1 SmoothStreamingMedia 

>   **MinorVersion (variabele):** De secundaire versie van het manifestreactiebericht. MOET worden ingesteld op 2. (Geen wijziging)
> 
>   **Tijdschaal (variabele):** De tijdschaal van het kenmerk Duur, opgegeven als het aantal stappen in één seconde. De standaardwaarde is 
> 1. (Geen wijziging)
> 
>    De aanbevolen waarde is 90000 voor het weergeven van de exacte duur van videoframes en fragmenten met fractionele framerate video (bijvoorbeeld 30/1.001 Hz).

#### <a name="2222-protectionelement"></a>2.2.2.2 ProtectionElement 

De ProtectionElement moet aanwezig zijn wanneer Common Encryption (CENC) is toegepast op video- of audiostreams. Hevc gecodeerde streams moeten voldoen aan Common Encryption 2nd Edition [ISO/IEC 23001-7]. Alleen segmentgegevens in VCL NAL-eenheden moeten worden versleuteld.

#### <a name="2223-streamelement"></a>2.2.2.3 StreamElement 

>   **StreamTimeScale (variabele):** De tijdschaal voor duur- en tijdwaarden in deze stream, opgegeven als het aantal stappen in één seconde. Een waarde van 90000 wordt aanbevolen voor HEVC-streams. Een waarde die overeenkomt met de golfvorm sample frequentie (bijvoorbeeld 48000 of 44100) wordt aanbevolen voor audio streams.

##### <a name="22231-streamprotectionelement"></a>2.2.2.3.1 StreamProtectionElement

#### <a name="2224-urlpattern"></a>2.2.2.4 UrlPatroon 

#### <a name="225-trackelement"></a>2.2.5 TrackElement 

>   **FourCC (variabel):** Een code met vier tekens die aangeeft welke media-indeling voor elk voorbeeld wordt gebruikt. Het volgende waardenbereik is gereserveerd met de volgende semantische betekenissen:
> 
> * "hev1": Videosamples voor dit spoor gebruiken HEVC-video, met behulp van de 'hev1'-monsterbeschrijvingsindeling die is opgegeven in [ISO/IEC-14496-15].
>
> * "hvc1": Videomonsters voor dit spoor gebruiken HEVC-video, met behulp van het 'hvc1'-monsterbeschrijvingsformaat dat is opgegeven in [ISO/IEC-14496-15].
> 
>   **CodecPrivateData (variabele):** Gegevens die parameters specificeert die specifiek zijn voor de media-indeling en gemeenschappelijk zijn voor alle voorbeelden in het spoor, weergegeven als een reeks hex-gecodeerde bytes. De notatie en semantische betekenis van bytesequentie varieert als volgt met de waarde van het veld **FourCC:**
> 
>   * Wanneer een TrackElement HEVC-video beschrijft, moet het **FourCC-veld** **"hev1"** of **"hvc1"**
> 
>   Het veld **CodecPrivateData** moet een hexgecodeerde tekenreeksweergave bevatten van de volgende bytereeks, gespecificeerd in ABNF [[RFC5234]:](https://go.microsoft.com/fwlink/?LinkId=123096) (geen wijziging ten opzichte van MS-SSTR)
> 
>   * %x00 %x00 %x00 %x01 SPSField %x00 %x00 %x00 %x00 %x01 PPSField
> 
>   * SPSField bevat de Sequence Parameter Set (SPS).
> 
>   * PPSField bevat de Slice Parameter Set (PPS).
> 
>   Opmerking: De Video Parameter Set (VPS) is niet opgenomen in CodecPrivateData, maar moet worden opgenomen in de bestandsheader van opgeslagen bestanden in het vak 'hvcC'. Systemen die smooth streaming protocol gebruiken, moeten aanvullende decoderingsparameters (bijvoorbeeld HEVC-laag) signaleren met behulp van het aangepaste kenmerk 'codecs'.

##### <a name="22251-customattributeselement"></a>2.2.2.5.1 CustomAttributesElement 

#### <a name="226-streamfragmentelement"></a>2.2.6 StreamFragmentElement 

>   Het veld **MajorVersion van SmoothStreamingMedia** MOET worden ingesteld op 2 en het veld **MinorVersion** moet worden ingesteld op 2. (Geen wijziging)

##### <a name="22261-trackfragmentelement"></a>2.2.2.6.1 TrackFragmentElement 

### <a name="223-fragment-request"></a>2.2.3 Fragmentaanvraag 

>   **Opmerking**: De standaard media-indeling die wordt aangevraagd voor **MinorVersion** 2 en 'hev1' of 'hvc1' is 'iso8' merk ISO Base Media File Format opgegeven in [ISO/IEC 14496-12] ISO Base Media File Format Fourth Edition, en [ISO/IEC 23001-7] Common Encryption Second Edition.

### <a name="224-fragment-response"></a>2.2.4 Reactie op fragmenten 

#### <a name="2241-moofbox"></a>2.2.4.1 MoofBox 

#### <a name="2242-mfhdbox"></a>2.2.4.2 MfhdBox 

#### <a name="2243-trafbox"></a>2.2.4.3 TrafBox 

#### <a name="2244-tfxdbox"></a>2.2.4.4 TfxdBox 

>   De **TfxdBox** is afgeschaft en de functie is vervangen door de Track Fragment Decode Time Box ('tfdt') die is opgegeven in [ISO/IEC 14496-12] sectie 8.8.12.
> 
>   **Opmerking**: Een client kan de duur van een fragment berekenen door de voorbeeldduur op te tellen die wordt vermeld in het vak Track Run ('trun') of het aantal monsters te vermenigvuldigen dat de standaardmonsterduur wordt weergegeven. De baseMediaDecodeTime in 'tfdt' plus fragmentduur is gelijk aan de URL-tijdparameter voor het volgende fragment.
> 
>   Een Producer Reference Time Box ('prft') moet worden ingevoegd voorafgaand aan een Movie Fragment Box ('moof') indien nodig, om de UTC-tijd aan te geven die overeenkomt met de Track Fragment Decode Time van het eerste monster waarnaar wordt verwezen door de Movie Fragment Box, zoals gespecificeerd in [ISO/IEC 14496-12] sectie 8.16.5.

#### <a name="2245-tfrfbox"></a>2.2.4.5 TfrfBox 

>   De **TfrfBox** wordt afgeschaft en de functie wordt vervangen door het Track Fragment Decode Time Box ('tfdt') dat is opgegeven in [ISO/IEC 14496-12] punt 8.8.12.
> 
>   **Opmerking**: Een client kan de duur van een fragment berekenen door de voorbeeldduur op te tellen die wordt vermeld in het vak Track Run ('trun') of het aantal monsters te vermenigvuldigen dat de standaardmonsterduur wordt weergegeven. De baseMediaDecodeTime in 'tfdt' plus fragmentduur is gelijk aan de URL-tijdparameter voor het volgende fragment. Kijk vooruit adressen zijn afgeschaft omdat ze live streaming vertragen.

#### <a name="2246-tfhdbox"></a>2.2.4.6 TfhdBox 

>   De **TfhdBox** en de bijbehorende velden bevatten standaardwaarden voor per voorbeeldmetagegevens in het fragment. De syntaxis van het veld **TfhdBox** is een strikte subset van de syntaxis van het vak Track Fragment Header gedefinieerd in [[ISO/IEC-14496-12]](https://go.microsoft.com/fwlink/?LinkId=183695) sectie 8.8.7.
> 
>   **BaseDataOffset (8 bytes):** De verschuiving in bytes vanaf het begin van het veld **MdatBox** naar het voorbeeldveld in het veld **MdatBox.** Om deze beperking aan te geven, moet de standaard-base-is-moof-vlag (0x020000) worden ingesteld.

#### <a name="2247-trunbox"></a>2.2.4.7 TrunBox 

>   De **TrunBox** en de bijbehorende velden bevatten per metagegevens van het voorbeeld voor het gevraagde fragment. De syntaxis van **TrunBox** is een strikte subset van het Version 1 Track Fragment Run Box gedefinieerd in [[ISO/IEC-14496-](https://go.microsoft.com/fwlink/?LinkId=183695)*12]* sectie 8.8.8.
> 
>   **SampleCompositionTimeOffset (4 bytes):** De verschuiving tijdvan de monstersamenstellingstijd van elk monster wordt aangepast zodat de presentatietijd van het eerste gepresenteerde monster in het fragment gelijk is aan de decodetijd van het eerste gedecodeerde monster. Er moeten negatieve compensaties voor de samenstelling van de videomonsters worden gebruikt;
> 
>   zoals gedefinieerd in [[ISO/IEC-14496-12].](https://go.microsoft.com/fwlink/?LinkId=183695)
> 
>   Opmerking: Dit voorkomt een videosynchronisatiefout die wordt veroorzaakt door videoachterblijvende audio die gelijk is aan de grootste vertraging voor het verwijderen van de gedecodeerde beeldbuffer, en behoudt de presentatietiming tussen alternatieve fragmenten die verschillende verwijderingsvertragingen kunnen hebben.
> 
>   De syntaxis van de velden die in deze sectie zijn gedefinieerd, gespecificeerd in ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) blijft hetzelfde, behalve als volgt:
> 
>   SampleCompositionTimeOffset = SIGNED_INT32

#### <a name="2248-mdatbox"></a>2.2.4.8 MdatBox 

#### <a name="2249-fragment-response-common-fields"></a>2.2.4.9 Algemene antwoorden op fragmentrespons 

### <a name="225-sparse-stream-pointer"></a>2.2.5 Schaarse streamaanwijzer 

### <a name="226-fragment-not-yet-available"></a>2.2.6 Fragment nog niet beschikbaar 

### <a name="227-live-ingest"></a>2.2.7 Live Inest 

#### <a name="2271-filetype"></a>2.2.7.1 Bestandstype 

>   **FileType (variabele):** hiermee geeft u het subtype en het beoogde gebruik van het MPEG-4-bestand[([MPEG4-RA])](https://go.microsoft.com/fwlink/?LinkId=327787) en kenmerken op hoog niveau op.
> 
>   **MajorBrand (variabel):** Het belangrijkste merk van het mediabestand. MOET worden ingesteld op "isml."
> 
>   **MinorVersion (variabele):** De secundaire versie van het mediabestand. MOET worden ingesteld op 1.
> 
>   **CompatibleBrands (variabel):** Hiermee geeft u de ondersteunde merken van MPEG-4 op.
>   Moet "ccff" en "iso8" bevatten.
> 
>   De syntaxis van de velden die in deze sectie zijn gedefinieerd, gespecificeerd in ABNF [[RFC5234],](https://go.microsoft.com/fwlink/?LinkId=123096) is als volgt:

    FileType = MajorBrand MinorVersion CompatibleBrands
    MajorBrand = STRING_UINT32
    MinorVersion = STRING_UINT32
    CompatibleBrands = "ccff" "iso8" 0\*(STRING_UINT32)

**Opmerking**: De compatibiliteitsmerken 'ccff' en 'iso8' geven aan dat fragmenten voldoen aan "Common Container File Format" en Common Encryption [ISO/IEC 23001-7] en ISO Base Media File Format Edition 4 [ISO/IEC 14496-12].

#### <a name="2272-streammanifestbox"></a>2.2.7.2 StreamManifestBox 

##### <a name="22721-streamsmil"></a>2.2.7.2.1 StreamSMIL 

#### <a name="2273-liveservermanifestbox"></a>2.2.7.3 LiveServerManifestBox 

##### <a name="22731-livesmil"></a>2.2.7.3.1 LiveSMIL 

#### <a name="2274-moovbox"></a>2.2.7.4 MoovBox 

#### <a name="2275-fragment"></a>2.2.7.5 Fragment 

##### <a name="22751-track-fragment-extended-header"></a>2.2.7.5.1 Uitgebreide koptekst voor trackfragment 

### <a name="228-server-to-server-ingest"></a>2.2.8 Server-to-Server-ingest 

## <a name="3-protocol-details"></a>3 Protocoldetails 


## <a name="31-client-details"></a>3.1 Klantgegevens 

### <a name="311-abstract-data-model"></a>3.1.1 Abstract gegevensmodel 

#### <a name="3111-presentation-description"></a>3.1.1.1 Presentatiebeschrijving 

>   Het gegevenselement Presentatiebeschrijving bevat alle metagegevens voor de presentatie.
> 
>   Presentatiemetagegevens: een set metagegevens die gebruikelijk is voor alle streams in de presentatie. Presentatiemetagegevens omvatten de volgende gebieden, omschreven in punt *2.2.2.1:*
> 
> * **MajorVersion**
> * **MinorVersie**
> * **Tijdschaal**
> * **Duur**
> * **IsLive (IsLive)**
> * **LookaheadCount**
> * **DVRWindowLengte**
> 
>   De aanbiedingsvormen die HEVC-streams bevatten, moeten worden vastgesteld:

    MajorVersion = 2
    MinorVersion = 2

>   LookaheadCount = 0 (Let op: Dozen afgeschaft)
> 
>   Presentaties MOETEN ook instellen:

    TimeScale = 90000

>   Verzameling van stroom: een verzameling gegevenselementen van streambeschrijving, zoals omschreven in punt *3.1.1.1.2*.
> 
>   Beschrijving van de bescherming: gegevenselementen van de beschrijving van het beveiligingssysteem metagegevens, zoals omschreven in punt *3.1.1.1.1*.

##### <a name="31111-protection-system-metadata-description"></a>3.1.1.1.1 Beschrijving van metagegevens van beveiligingssysteem 

>   Het gegevenselement Metagegevensbeschrijving van het beveiligingssysteem bevat metagegevens die specifiek zijn voor één contentbeveiligingssysteem. (Geen wijziging)
> 
>   Beschrijving van de beveiligingsheader: metagegevens voor inhoudsbescherming die betrekking hebben op één contentbeveiligingssysteem. Beschrijving van de protection header omvat de volgende velden, omschreven in punt *2.2.2.2*:
> 
>   * **SystemID**
>   * **ProtectionheaderContent**

##### <a name="31112-stream-description"></a>3.1.1.1.2 Streambeschrijving 

###### <a name="311121-track-description"></a>3.1.1.1.2.1 Spoorbeschrijving 

###### <a name="3111211-custom-attribute-description"></a>3.1.1.1.2.1.1 Aangepaste kenmerkbeschrijving 

##### <a name="3113-fragment-reference-description"></a>3.1.1.3 Beschrijving van fragmentverwijzingen 

###### <a name="31131-track-specific-fragment-reference-description"></a>3.1.1.3.1 Beschrijving van de referentie van fragmenten 

#### <a name="3112-fragment-description"></a>3.1.1.2 Fragmentbeschrijving 

##### <a name="31121-sample-description"></a>3.1.1.2.1 Steekproefbeschrijving 

### <a name="312-timers"></a>3.1.2 Timers 

### <a name="313-initialization"></a>3.1.3 Initialisatie 

### <a name="314-higher-layer-triggered-events"></a>3.1.4 Geactiveerde gebeurtenissen met een hogere laag 

#### <a name="3141-open-presentation"></a>3.1.4.1 Open presentatie 

#### <a name="3142-get-fragment"></a>3.1.4.2 Fragment krijgen 

#### <a name="3143-close-presentation"></a>3.1.4.3 Presentatie sluiten 

### <a name="315-processing-events-and-sequencing-rules"></a>3.1.5 Verwerkingsgebeurtenissen en sequencingregels 

#### <a name="3151-manifest-request-and-manifest-response"></a>3.1.5.1 Manifest verzoek en manifestantwoord 

#### <a name="3152-fragment-request-and-fragment-response"></a>3.1.5.2 Fragmentaanvraag en fragmentrespons

## <a name="32-server-details"></a>3.2 Servergegevens

## <a name="33-live-encoder-details"></a>3.3 Live Encoder Details 

## <a name="4-protocol-examples"></a>4 Voorbeelden van protocol 

## <a name="5-security"></a>5 Beveiliging 

## <a name="51-security-considerations-for-implementers"></a>5.1 Veiligheidsoverwegingen voor uitvoerders

>   Als de inhoud die met dit protocol wordt vervoerd een hoge commerciële waarde heeft, moet een Content Protection System worden gebruikt om onbevoegd gebruik van de inhoud te voorkomen. De **ProtectionElement** kan worden gebruikt om metadata met betrekking tot het gebruik van een Content Protection System. Beveiligde audio- en video-inhoud moet worden versleuteld zoals gespecificeerd door MPEG Common Encryption Second Edition: 2015 [ISO/IEC 23001-7].
> 
>   **Opmerking:** Voor HEVC-video worden alleen slicegegevens in VCL-NALs versleuteld. Slice-headers en andere NALs zijn toegankelijk voor presentatietoepassingen voordat ze worden decryptie. in een beveiligd videopad is versleutelde informatie niet beschikbaar voor presentatietoepassingen.

## <a name="52-index-of-security-parameters"></a>5.2 Index van beveiligingsparameters 


| **Beveiligingsparameter**  | **Section**         |
|-------------------------|---------------------|
| ProtectionElement       | *2.2.2.2*           |
| Algemene versleutelingsvakken | *[ISO/IEC 23001-7]* |

## <a name="53-common-encryption-boxes"></a>5.3 Algemene versleutelingsvakken

De volgende vakken kunnen aanwezig zijn in fragmentreacties wanneer gemeenschappelijke versleuteling wordt toegepast en zijn gespecificeerd in [ISO/IEC 23001-7] of [ISO/IEC 14496-12]:

1.  Beveiligingssysteem specifieke header box ('pssh')

2.  Voorbeeldversleutelingsvak ('senc')

3.  Voorbeeld van hulpinformatieoffsetbox ('saio')

4.  Voorbeeld van hulpinformatie ('saiz')

5.  Voorbeeldgroepbeschrijvingsvak ('sgpd')

6.  Voorbeeld naar groepsvak ('sbgp')

---

## <a name="media-services-learning-paths"></a>Media Services-leertrajecten
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png
