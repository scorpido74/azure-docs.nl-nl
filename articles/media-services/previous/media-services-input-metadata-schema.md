---
title: Metagegevensschema azure Media Services | Microsoft Documenten
description: In dit artikel vindt u een overzicht van het metagegevensschema van Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: a81d6edfd887dc935a53742b7bc1492651c9bda5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887115"
---
# <a name="input-metadata"></a>Invoermetaalen 

Een coderingstaak is gekoppeld aan een invoerelement (of elementen) waarop u bepaalde coderingstaken wilt uitvoeren.  Na voltooiing van een taak wordt een uitvoeractief geproduceerd.  Het uitvoerelement bevat video, audio, miniaturen, manifest, enz. Het uitvoerelement bevat ook een bestand met metagegevens over het invoerelement. De naam van het XML-bestand &lt;metagegevens heeft de volgende indeling: asset_id&gt;_metadata.xml (bijvoorbeeld 41114ad3-eb5e-4c57-8d92-5354e2b7d4a4_metadata.xml), waarbij &lt;asset_id&gt; de AssetId-waarde van het invoerelement is.  

Media Services scant geen input-assets om metadata te genereren. Invoermetagegevens worden alleen gegenereerd als een artefact wanneer een invoerelement wordt verwerkt in een taak. Vandaar dat dit artefact is geschreven naar de output Asset. Verschillende tools worden gebruikt om metadata te genereren voor invoerassets en uitvoerassets. Daarom hebben de invoermetagegevens een iets ander schema dan de uitvoermetagegevens.

Als u het metagegevensbestand wilt onderzoeken, u een **SAS-locator** maken en het bestand downloaden naar uw lokale computer. U een voorbeeld vinden over het maken van een SAS-locator en het downloaden van een bestand [met behulp van de Media Services .NET SDK Extensions](media-services-dotnet-get-started.md).  

In dit artikel worden de elementen en typen van het&lt;&gt;XML-schema besproken waarop de invoermetada (asset_id _metadata.xml) is gebaseerd.  Zie [Uitvoermetagegevens](media-services-output-metadata-schema.md)voor informatie over het bestand met metagegevens over het uitvoerelement.  

Aan het einde van dit artikel vindt u de [schemacode](media-services-input-metadata-schema.md#code) een [XML-voorbeeld.](media-services-input-metadata-schema.md#xml)  
 

## <a name="assetfiles-element-root-element"></a><a name="AssetFiles"></a>Element AssetFiles (hoofdelement)
Bevat een verzameling Elements van [AssetFile](media-services-input-metadata-schema.md#AssetFile)voor de coderingstaak.  

Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

| Name | Beschrijving |
| --- | --- |
| **AssetFile (AssetFile)**<br /><br /> minOccurs="1" maxOccurs="unbounded" |Een enkel kindelement. Zie [Element AssetFile](media-services-input-metadata-schema.md#AssetFile)voor meer informatie . |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element AssetFile
 Bevat kenmerken en elementen die een assetbestand beschrijven.  

 Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Naam**<br /><br /> Vereist |**xs:tekenreeks** |Asset bestandsnaam. |
| **Grootte**<br /><br /> Vereist |**xs:lang** |Grootte van het assetbestand in bytes. |
| **Duur**<br /><br /> Vereist |**xs:duur** |Inhoud afspelen duur. Voorbeeld: Duration="PT25M37.757S". |
| **Aantal streams**<br /><br /> Vereist |**xs:int** |Aantal streams in het assetbestand. |
| **FormatNames**<br /><br /> Vereist |**xs: tekenreeks** |Namen opmaken. |
| **FormatVerboseNamen**<br /><br /> Vereist |**xs: tekenreeks** |Formaat verbose namen. |
| **Starttime** |**xs:duur** |Begintijd van inhoud. Voorbeeld: StartTime="PT2.669S". |
| **OverallBitRate** |**xs: int** |Gemiddelde bitrate van het assetbestand in kbps. |

> [!NOTE]
> De volgende vier onderliggende elementen moeten in een reeks worden weergegeven.  
> 
> 

### <a name="child-elements"></a>Onderliggende elementen
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Programma's**<br /><br /> minOccurs="0" | |Verzameling van alle [elementen Programma's](media-services-input-metadata-schema.md#Programs) wanneer het assetbestand in MPEG-TS-indeling staat. |
| **VideoTracks**<br /><br /> minOccurs="0" | |Elk fysiek assetbestand kan nul of meer video's bevatten die zijn verbonden tot een geschikte containerindeling. Dit element bevat een verzameling van alle [VideoTracks](media-services-input-metadata-schema.md#VideoTracks) die deel uitmaken van het assetbestand. |
| **Audiotracks**<br /><br /> minOccurs="0" | |Elk fysiek assetbestand kan nul of meer audiotracks bevatten die zijn verweven in een geschikte containerindeling. Dit element bevat een verzameling van alle [AudioTracks](media-services-input-metadata-schema.md#AudioTracks) die deel uitmaken van het assetbestand. |
| **Metagegevens**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetagegevensType](media-services-input-metadata-schema.md#MetadataType) |De metagegevens van het assetbestand worden weergegeven als key\value strings. Bijvoorbeeld:<br /><br /> **&lt;Metagegevenskey="taal" value="eng" /&gt;** |

## <a name="tracktype"></a><a name="TrackType"></a>TrackType TrackType
Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Id**<br /><br /> Vereist |**xs:int** |Zero-based index van deze audio- of videotrack.<br /><br /> Dit is niet noodzakelijkerwijs dat de TrackID zoals gebruikt in een MP4-bestand. |
| **Codec** |**xs:tekenreeks** |Codec-tekenreeks voor videotrack. |
| **CodecLongName** |**xs: tekenreeks** |Audio of video track codec lange naam. |
| **TimeBase TimeBase**<br /><br /> Vereist |**xs:tekenreeks** |Tijdbasis. Voorbeeld: TimeBase="1/48000" |
| **Aantal frames** |**xs:int** |Aantal frames (aanwezig voor videotracks). |
| **Starttime** |**xs: duur** |Starttijd bijhouden. Voorbeeld: StartTime="PT2.669S" |
| **Duur** |**xs:duur** |Volg de duur. Voorbeeld: Duration="PTSampleFormat M37.757S". |

> [!NOTE]
> De volgende twee onderliggende elementen moeten in een reeks worden weergegeven.  
> 
> 

### <a name="child-elements"></a>Onderliggende elementen
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Disposition**<br /><br /> minOccurs="0" maxOccurs="1" |[StreamDispositionType](media-services-input-metadata-schema.md#StreamDispositionType) |Bevat presentatie-informatie (bijvoorbeeld of een bepaalde audiotrack voor slechtziende kijkers is). |
| **Metagegevens**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[MetagegevensType](media-services-input-metadata-schema.md#MetadataType) |Algemene sleutel-/waardetekenreeksen die kunnen worden gebruikt om verschillende informatie vast te houden. Bijvoorbeeld key="taal" en value="eng". |

## <a name="audiotracktype-inherits-from-tracktype"></a><a name="AudioTrackType"></a>AudioTrackType (overerving van TrackType)
 **AudioTrackType** is een wereldwijd complex type dat overneemt van [TrackType.](media-services-input-metadata-schema.md#TrackType)  

 Het type vertegenwoordigt een specifieke audiotrack in het assetbestand.  

 Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Voorbeeldopmaak** |**xs:tekenreeks** |Voorbeeldindeling. |
| **Kanaalindeling** |**xs: tekenreeks** |Kanaalindeling. |
| **Kanalen**<br /><br /> Vereist |**xs:int** |Aantal (0 of meer) van audiokanalen. |
| **Bemonsteringsfrequentie**<br /><br /> Vereist |**xs:int** |Audiosamplingsnelheid in samples/sec of Hz. |
| **Bitrate** |**xs:int** |Gemiddelde audiobitsnelheid in bits per seconde, berekend op basis van het assetbestand. Alleen de elementaire stroom lading wordt geteld, en de verpakking overhead is niet opgenomen in deze telling. |
| **Bitspersample** |**xs:int** |Bits per voorbeeld voor het formaattype wFormatTag. |

## <a name="videotracktype-inherits-from-tracktype"></a><a name="VideoTrackType"></a>VideoTrackType (overerving van TrackType)
**VideoTrackType** is een wereldwijd complex type dat overneemt van [TrackType](media-services-input-metadata-schema.md#TrackType).  

Het type vertegenwoordigt een specifieke videotrack in het assetbestand.  

Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Fourcc**<br /><br /> Vereist |**xs:tekenreeks** |Videocodec FourCC-code. |
| **Profiel** |**xs: tekenreeks** |Het profiel van de videotrack. |
| **Niveau** |**xs: tekenreeks** |Het niveau van de videotrack. |
| **Pixelopmaak** |**xs: tekenreeks** |De pixelindeling van videotrack. |
| **Width**<br /><br /> Vereist |**xs:int** |Gecodeerde videobreedte in pixels. |
| **Height**<br /><br /> Vereist |**xs:int** |Gecodeerde videohoogte in pixels. |
| **DisplayAspectRatioNumerator**<br /><br /> Vereist |**xs: dubbel** |Beeldverhouding van de videoweergave. |
| **DisplayAspectRatioNoemer**<br /><br /> Vereist |**xs:dubbel** |De noemer van de beeldverhouding van de videoweergave. |
| **DisplayAspectRatioNoemer**<br /><br /> Vereist |**xs: dubbel** |Videosample-beeldverhoudingsmeter. |
| **SampleAspectRatioNumerator** |**xs: dubbel** |Videosample-beeldverhoudingsmeter. |
| **SampleAspectRatioNumerator** |**xs:dubbel** |De noemer van de videosample-beeldverhouding. |
| **Framerate**<br /><br /> Vereist |**xs:decimal** |Gemeten videoframesnelheid in .3f-indeling. |
| **Bitrate** |**xs:int** |Gemiddelde videobitsnelheid in kilobits per seconde, berekend op basis van het assetbestand. Alleen het elementair stroomlaadvermogen wordt geteld en de verpakking overhead is niet inbegrepen. |
| **MaxGOPBitrate MaxGOPBitrate** |**xs: int** |Max GOP gemiddelde bitrate voor deze video track, in kilobits per seconde. |
| **HasbFrames** |**xs:int** |Videotrack aantal B-frames. |

## <a name="metadatatype"></a><a name="MetadataType"></a>MetagegevensType
**MetadataType** is een globaal complex type dat metagegevens van een assetbestand omschrijft als sleutel-/waardetekenreeksen. Bijvoorbeeld key="taal" en value="eng".  

Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Sleutel**<br /><br /> Vereist |**xs:tekenreeks** |De sleutel in het sleutel/waardepaar. |
| **value**<br /><br /> Vereist |**xs:tekenreeks** |De waarde in het sleutel/waardepaar. |

## <a name="programtype"></a><a name="ProgramType"></a>ProgramType ProgramType
**ProgramType** is een wereldwijd complex type dat een programma beschrijft.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **ProgramId ProgramId**<br /><br /> Vereist |**xs:int** |Programma-id |
| **Aantalprogramma's**<br /><br /> Vereist |**xs:int** |Aantal programma's. |
| **PmtPid PmtPid**<br /><br /> Vereist |**xs:int** |Program Map Tables (PMT's) bevatten informatie over programma's.  Zie [PMt](https://en.wikipedia.org/wiki/MPEG_transport_stream#PMT)voor meer informatie. |
| **PcrPid (PcrPid)**<br /><br /> Vereist |**xs: int** |Gebruikt door decoder. Zie [PCR](https://en.wikipedia.org/wiki/MPEG_transport_stream#PCR) voor meer informatie |
| **StartPTS** |**xs: lang** |Start presentatie tijdstempel. |
| **EndPTS** |**xs: lang** |Einde presentatie tijdstempel. |

## <a name="streamdispositiontype"></a><a name="StreamDispositionType"></a>StreamDispositionType
**StreamDispositionType** is een globaal complex type dat de stream beschrijft.  

Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Standaard**<br /><br /> Vereist |**xs: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de standaardpresentatie is. |
| **Dub**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat dit de nagesynchroniseerde presentatie is. |
| **Origineel**<br /><br /> Vereist |**xs: int** |Stel dit kenmerk in op 1 om aan te geven dat dit de oorspronkelijke presentatie is. |
| **Opmerking**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat deze track commentaar bevat. |
| **Tekst**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat dit nummer songteksten bevat. |
| **Karaoke**<br /><br /> Vereist |**xs:int** |Stel dit attribuut in op 1 om aan te geven dat dit de karaoketrack vertegenwoordigt (achtergrondmuziek, geen zang). |
| **Gedwongen**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat dit de geforceerde presentatie is. |
| **Slechthorend**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat deze track is voor mensen die slechthorend zijn. |
| **Slechtzienden**<br /><br /> Vereist |**xs:int** |Stel dit kenmerk in op 1 om aan te geven dat deze track voor slechtzienden is. |
| **CleanEffects (CleanEffects)**<br /><br /> Vereist |**xs: int** |Stel dit kenmerk in op 1 om aan te geven dat deze track schone effecten heeft. |
| **Bijgevoegde Pic**<br /><br /> Vereist |**xs: int** |Stel dit kenmerk in op 1 om aan te geven dat deze track foto's heeft. |

## <a name="programs-element"></a><a name="Programs"></a>Het element Programma's
Wikkelelement met meerdere **programma-elementen.**  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Programma**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[ProgramType ProgramType](media-services-input-metadata-schema.md#ProgramType) |Voor assetbestanden die in MPEG-TS-indeling zijn, bevat u informatie over programma's in het assetbestand. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks-element
 Wikkelelement met meerdere **VideoTrack-elementen.**  

 Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Type | Beschrijving |
| --- | --- | --- |
| **VideoTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[VideoTrackType (overerving van TrackType)](media-services-input-metadata-schema.md#VideoTrackType) |Bevat informatie over videotracks in het assetbestand. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Element AudioTracks
 Wikkelelement met meerdere **AudioTrack-elementen.**  

 Zie een XML-voorbeeld aan het einde van dit artikel: [XML-voorbeeld](media-services-input-metadata-schema.md#xml).  

### <a name="elements"></a>Elementen
| Name | Type | Beschrijving |
| --- | --- | --- |
| **AudioTrack**<br /><br /> minOccurs="0" maxOccurs="unbounded" |[AudioTrackType (overerving van TrackType)](media-services-input-metadata-schema.md#AudioTrackType) |Bevat informatie over audiotracks in het assetbestand. |

## <a name="schema-code"></a><a name="code"></a>Schemacode
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.0"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata"  
               elementFormDefault="qualified">  

      <xs:complexType name="MetadataType">  
        <xs:attribute name="key"   type="xs:string" use="required"/>  
        <xs:attribute name="value" type="xs:string" use="required"/>  
      </xs:complexType>  

      <xs:complexType name="ProgramType">  
        <xs:attribute name="ProgramId" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Program Id</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfPrograms" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>Number of programs</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PmtPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pmt pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="PcrPid" type="xs:int" use="required">  
          <xs:annotation>  
            <xs:documentation>pcr pid</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="StartPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>start pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="EndPTS" type="xs:long">  
          <xs:annotation>  
            <xs:documentation>end pts</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="StreamDispositionType">  
        <xs:attribute name="Default"          type="xs:int" use="required" />  
        <xs:attribute name="Dub"              type="xs:int" use="required" />  
        <xs:attribute name="Original"         type="xs:int" use="required" />  
        <xs:attribute name="Comment"          type="xs:int" use="required" />  
        <xs:attribute name="Lyrics"           type="xs:int" use="required" />  
        <xs:attribute name="Karaoke"          type="xs:int" use="required" />  
        <xs:attribute name="Forced"           type="xs:int" use="required" />  
        <xs:attribute name="HearingImpaired"  type="xs:int" use="required" />  
        <xs:attribute name="VisualImpaired"   type="xs:int" use="required" />  
        <xs:attribute name="CleanEffects"     type="xs:int" use="required" />  
        <xs:attribute name="AttachedPic"      type="xs:int" use="required" />  
      </xs:complexType>  

      <xs:complexType name="TrackType" abstract="true">  
        <xs:sequence>  
          <xs:element name="Disposition" type="StreamDispositionType" minOccurs="0" maxOccurs="1"/>  
          <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded"/>  
        </xs:sequence>  
        <xs:attribute name="Id" use="required">  
          <xs:annotation>  
            <xs:documentation>zero-based index of this video track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="Codec" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec string</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="CodecLongName" type="xs:string">  
          <xs:annotation>  
            <xs:documentation>video track codec long name</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="TimeBase"  type="xs:string" use="required">  
          <xs:annotation>  
            <xs:documentation>Time base. Example: TimeBase="1/48000"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="NumberOfFrames">  
          <xs:annotation>  
            <xs:documentation>number of frames</xs:documentation>  
          </xs:annotation>  
          <xs:simpleType>  
            <xs:restriction base="xs:int">  
              <xs:minInclusive value="0"/>  
            </xs:restriction>  
          </xs:simpleType>  
        </xs:attribute>  
        <xs:attribute name="StartTime" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track start time. Example: StartTime="PT2.669S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
        <xs:attribute name="Duration" type="xs:duration">  
          <xs:annotation>  
            <xs:documentation>Track duration. Example: Duration="PT25M37.757S"</xs:documentation>  
          </xs:annotation>  
        </xs:attribute>  
      </xs:complexType>  

      <xs:complexType name="VideoTrackType">  
        <xs:annotation>  
          <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="FourCC" type="xs:string" use="required">  
              <xs:annotation>  
                <xs:documentation>video codec FourCC code</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Profile" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>profile</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Level" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>level</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="PixelFormat" type="xs:string">  
              <xs:annotation>  
                <xs:documentation>Video track's pixel format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Width" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video width in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Height" use="required">  
              <xs:annotation>  
                <xs:documentation>encoded video height in pixels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioNumerator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="DisplayAspectRatioDenominator" use="required">  
              <xs:annotation>  
                <xs:documentation>video display aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioNumerator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio numerator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SampleAspectRatioDenominator">  
              <xs:annotation>  
                <xs:documentation>video sample aspect ratio denominator</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:double">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="FrameRate" use="required">  
              <xs:annotation>  
                <xs:documentation>measured video frame rate in .3f format</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:decimal">  
                  <xs:minInclusive value="0"/>  
                  <xs:fractionDigits value="3"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="MaxGOPBitrate">  
              <xs:annotation>  
                <xs:documentation>Max GOP average bitrate for this video track, in kilobits per second</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="HasBFrames" type="xs:int">  
              <xs:annotation>  
                <xs:documentation>video track number of B frames</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:complexType name="AudioTrackType">  
        <xs:annotation>  
          <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
        </xs:annotation>  
        <xs:complexContent>  
          <xs:extension base="TrackType">  
            <xs:attribute name="SampleFormat"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>sample format</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="ChannelLayout"  type="xs:string">  
              <xs:annotation>  
                <xs:documentation>channel layout</xs:documentation>  
              </xs:annotation>  
            </xs:attribute>  
            <xs:attribute name="Channels" use="required">  
              <xs:annotation>  
                <xs:documentation>number of audio channels</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="SamplingRate" use="required">  
              <xs:annotation>  
                <xs:documentation>audio sampling rate in samples/sec or Hz</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="Bitrate">  
              <xs:annotation>  
                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
            <xs:attribute name="BitsPerSample">  
              <xs:annotation>  
                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
              </xs:annotation>  
              <xs:simpleType>  
                <xs:restriction base="xs:int">  
                  <xs:minInclusive value="0"/>  
                </xs:restriction>  
              </xs:simpleType>  
            </xs:attribute>  
          </xs:extension>  
        </xs:complexContent>  
      </xs:complexType>  

      <xs:element name="AssetFiles">  
        <xs:annotation>  
          <xs:documentation>Collection of AssetFile entries for the encoding job</xs:documentation>  
        </xs:annotation>  
        <xs:complexType>  
          <xs:sequence>  
            <xs:element name="AssetFile" minOccurs="1" maxOccurs="unbounded">  
              <xs:annotation>  
                <xs:documentation>asset file</xs:documentation>  
              </xs:annotation>  
              <xs:complexType>  
                <xs:sequence>  
                  <xs:element name="Programs" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>This is the collection of all programs when file is MPEG-TS</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Program" type="ProgramType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" type="VideoTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" type="AudioTrackType" minOccurs="0" maxOccurs="unbounded" />  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="Metadata" type="MetadataType" minOccurs="0" maxOccurs="unbounded" />  
                </xs:sequence>  
                <xs:attribute name="Name" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>the media asset file name</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="Size" use="required">  
                  <xs:annotation>  
                    <xs:documentation>size of file in bytes</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:long">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
                <xs:attribute name="Duration" type="xs:duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration. Example: Duration="PT25M37.757S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="NumberOfStreams" type="xs:int" use="required">  
                  <xs:annotation>  
                    <xs:documentation>number of streams in asset file</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatNames" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="FormatVerboseName" type="xs:string" use="required">  
                  <xs:annotation>  
                    <xs:documentation>format verbose names</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="StartTime" type="xs:duration">  
                  <xs:annotation>  
                    <xs:documentation>content start time. Example: StartTime="PT2.669S"</xs:documentation>  
                  </xs:annotation>  
                </xs:attribute>  
                <xs:attribute name="OverallBitRate">  
                  <xs:annotation>  
                    <xs:documentation>average bitrate of the asset file in kbps</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:int">  
                      <xs:minInclusive value="0"/>  
                    </xs:restriction>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  


## <a name="xml-example"></a><a name="xml"></a>XML-voorbeeld
Het volgende is een voorbeeld van het metagegevensbestand Invoer.  

    <?xml version="1.0" encoding="utf-8"?>  
    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2014/07/mediaencoder/inputmetadata">  
      <AssetFile Name="bear.mp4" Size="1973733" Duration="PT12.678S" NumberOfStreams="2" FormatNames="mov,mp4,m4a,3gp,3g2,mj2" FormatVerboseName="QuickTime / MOV" StartTime="PT0S" OverallBitRate="1245">  
        <VideoTracks>  
          <VideoTrack Id="1" Codec="h264" CodecLongName="H.264 / AVC / MPEG-4 AVC / MPEG-4 part 10" TimeBase="1/29970" NumberOfFrames="375" StartTime="PT0.034S" Duration="PT12.645S" FourCC="avc1" Profile="High" Level="4.1" PixelFormat="yuv420p" Width="512" Height="384" DisplayAspectRatioNumerator="4" DisplayAspectRatioDenominator="3" SampleAspectRatioNumerator="1" SampleAspectRatioDenominator="1" FrameRate="29.656" Bitrate="1043" HasBFrames="1">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Video Media Handler" />  
          </VideoTrack>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="aac" CodecLongName="AAC (Advanced Audio Coding)" TimeBase="1/44100" NumberOfFrames="546" StartTime="PT0S" Duration="PT12.678S" SampleFormat="fltp" ChannelLayout="stereo" Channels="2" SamplingRate="44100" Bitrate="156" BitsPerSample="0">  
            <Disposition Default="1" Dub="0" Original="0" Comment="0" Lyrics="0" Karaoke="0" Forced="0" HearingImpaired="0" VisualImpaired="0" CleanEffects="0" AttachedPic="0" />  
            <Metadata key="creation_time" value="2010-03-10 16:11:56" />  
            <Metadata key="language" value="eng" />  
            <Metadata key="handler_name" value="Mainconcept MP4 Sound Media Handler" />  
          </AudioTrack>  
        </AudioTracks>  
        <Metadata key="major_brand" value="mp42" />  
        <Metadata key="minor_version" value="0" />  
        <Metadata key="compatible_brands" value="mp42mp41" />  
        <Metadata key="creation_time" value="2010-03-10 16:11:53" />  
        <Metadata key="comment" value="Courtesy of National Geographic.  Used by Permission." />  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

