---
title: Metagegevensschema azure Media Services uitvoer | Microsoft Documenten
description: In dit artikel vindt u een overzicht van het metagegevensschema voor uitvoer van Azure Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 1ed84c88-eea5-4a24-9c4f-f2428157d08a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 3f0c6b60e2be625d1f869c3eda4acb9dfd3c6e9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74886809"
---
# <a name="output-metadata"></a>Uitvoermetagegevens
## <a name="overview"></a>Overzicht
Een coderingstaak is gekoppeld aan een invoerelement (of elementen) waarop u bepaalde coderingstaken wilt uitvoeren. Codeer bijvoorbeeld een MP4-bestand in adaptieve bitratesets met H.264 MP4. een miniatuur maken; overlays maken. Na voltooiing van een taak wordt een uitvoeractief geproduceerd.  Het uitvoerelement bevat video, audio, miniaturen, enz. Het uitvoerelement bevat ook een bestand met metagegevens over het uitvoerelement. De naam van het XML-bestand &lt;met&gt;ametjes heeft de volgende indeling: source_file_name _manifest.xml (bijvoorbeeld BigBuckBunny_manifest.xml).  

Media Services scant geen input-assets om metadata te genereren. Invoermetagegevens worden alleen gegenereerd als een artefact wanneer een invoerelement wordt verwerkt in een taak. Vandaar dat dit artefact is geschreven naar de output Asset. Verschillende tools worden gebruikt om metadata te genereren voor invoerassets en uitvoerassets. Daarom hebben de invoermetagegevens een iets ander schema dan de uitvoermetagegevens.

Als u het metagegevensbestand wilt onderzoeken, u een **SAS-locator** maken en het bestand downloaden naar uw lokale computer.  

In dit artikel worden de elementen en typen van het&lt;&gt;XML-schema besproken waarop de uitvoermetada (source_file_name _manifest.xml) is gebaseerd. Zie Invoermetaal voor informatie over het bestand met metagegevens over het invoerelement.  

Aan het einde van dit artikel vindt u de volledige schemacode en het XML-voorbeeld.  

## <a name="assetfiles-root-element"></a><a name="AssetFiles"></a>Hoofdelement AssetFiles
Verzameling van AssetFile-items voor de coderingstaak.  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **AssetFile (AssetFile)**<br/><br/> minOccurs="0" maxOccurs="1" |Een Element AssetFile dat deel uitmaakt van de AssetFiles-verzameling. |

## <a name="assetfile-element"></a><a name="AssetFile"></a>Element AssetFile
U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Naam**<br/><br/> Vereist |**xs:tekenreeks** |De bestandsnaam van mediaasset. |
| **Grootte**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:lang** |Grootte van het assetbestand in bytes. |
| **Duur**<br/><br/> Vereist |**xs:duur** |Inhoud afspelen duur. |

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **Bronnen** |Verzameling van input/bronmediabestanden, die zijn verwerkt om deze AssetFile te produceren. Zie Bronelement voor meer informatie. |
| **VideoTracks**<br/><br/> minOccurs="0" maxOccurs="1" |Elke fysieke AssetFile kan daarin nul of meer video's bevatten die zijn verbonden tot een geschikte containerindeling. Zie Het element VideoTracks voor meer informatie. |
| **Audiotracks**<br/><br/> minOccurs="0" maxOccurs="1" |Elke fysieke AssetFile kan daarin nul of meer audiotracks bevatten die zijn verweven tot een geschikte containerindeling. Dit is de collectie van al die audiotracks. Zie Element AudioTracks voor meer informatie. |

## <a name="sources-element"></a><a name="Sources"></a>Bronnenelement
Verzameling van input/bronmediabestanden, die zijn verwerkt om deze AssetFile te produceren.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **Bron**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Een invoer-/bronbestand dat wordt gebruikt bij het genereren van dit element. Zie Bronelement voor meer informatie. |

## <a name="source-element"></a><a name="Source"></a>Bronelement
Een invoer-/bronbestand dat wordt gebruikt bij het genereren van dit element.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Naam**<br/><br/> Vereist |**xs:tekenreeks** |Invoerbronbestandsnaam. |

## <a name="videotracks-element"></a><a name="VideoTracks"></a>VideoTracks-element
Elke fysieke AssetFile kan daarin nul of meer video's bevatten die zijn verbonden tot een geschikte containerindeling. Het element **VideoTracks** vertegenwoordigt een verzameling van alle videotracks.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **VideoTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Een specifieke videotrack in het bovenliggende AssetFile. Zie VideoTrack-element voor meer informatie. |

## <a name="videotrack-element"></a><a name="VideoTrack"></a>VideoTrack-element
Een specifieke videotrack in het bovenliggende AssetFile.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Zero-based index van deze video track. **Let op:**  Deze **id** is niet noodzakelijkerwijs de TrackID zoals gebruikt in een MP4-bestand. |
| **Fourcc**<br/><br/> Vereist |**xs:tekenreeks** |Videocodec FourCC-code. |
| **Profiel** |**xs:tekenreeks** |H264-profiel (alleen van toepassing op H264-codec). |
| **Niveau** |**xs:tekenreeks** |H264-niveau (alleen van toepassing op H264-codec). |
| **Width**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Gecodeerde videobreedte in pixels. |
| **Height**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Gecodeerde videohoogte in pixels. |
| **DisplayAspectRatioNumerator**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:dubbel** |Beeldverhouding van de videoweergave. |
| **DisplayAspectRatioNoemer**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:dubbel** |De noemer van de beeldverhouding van de videoweergave. |
| **Framesnelheid**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:decimal** |Gemeten videoframesnelheid in .3f-indeling. |
| **Doelframerate**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:decimal** |Vooraf ingestelde doelvideoframesnelheid in .3f-indeling. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Gemiddelde videobitsnelheid in kilobits per seconde, berekend op basis van de AssetFile. Telt alleen de elementaire stroom lading, en omvat niet de verpakking overhead. |
| **TargetBitrate TargetBitrate TargetBitrate TargetBit**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Doel gemiddelde bitrate voor deze video track, zoals gevraagd via de codering preset, in kilobits per seconde. |
| **MaxGOPBitrate MaxGOPBitrate**<br/><br/> minInclusive ="0" |**xs:int** |Max GOP gemiddelde bitrate voor deze video track, in kilobits per seconde. |

## <a name="audiotracks-element"></a><a name="AudioTracks"></a>Element AudioTracks
Elke fysieke AssetFile kan daarin nul of meer audiotracks bevatten die zijn verweven tot een geschikte containerindeling. Het element **AudioTracks** vertegenwoordigt een verzameling van al die audiotracks.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **AudioTrack**<br/><br/> minOccurs="1" maxOccurs="unbounded" |Een specifieke audiotrack in het bovenliggende AssetFile. Zie AudioTrack-element voor meer informatie. |

## <a name="audiotrack-element"></a><a name="AudioTrack"></a>AudioTrack-element
Een specifieke audiotrack in het bovenliggende AssetFile.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **Id**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Zero-based index van deze audio track. **Let op:**  Dit is niet noodzakelijkerwijs de TrackID zoals gebruikt in een MP4-bestand. |
| **Codec** |**xs:tekenreeks** |Audio track codec string. |
| **EncoderVersie** |**xs:tekenreeks** |Optionele encoderversietekenreeks, vereist voor EAC3. |
| **Kanalen**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Aantal audiokanalen. |
| **Bemonsteringsfrequentie**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Audiosamplingsnelheid in samples/sec of Hz. |
| **Bitrate**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Gemiddelde audiobitsnelheid in bits per seconde, berekend op basis van de AssetFile. Telt alleen de elementaire stroom lading, en omvat niet de verpakking overhead. |
| **Bitspersample**<br/><br/> minInclusive ="0"<br/><br/> Vereist |**xs:int** |Bits per voorbeeld voor het formaattype wFormatTag. |

### <a name="child-elements"></a>Onderliggende elementen
| Name | Beschrijving |
| --- | --- |
| **LoudnessMeteringResultParameters**<br/><br/> minOccurs="0" maxOccurs="1" |Luidheid meten resultaat parameters. Zie Element LoudnessMeteringResultParameters voor meer informatie. |

## <a name="loudnessmeteringresultparameters-element"></a><a name="LoudnessMeteringResultParameters"></a>LuidheidMetingResultParameters element
Luidheid meten resultaat parameters.  

U een [XML-voorbeeld XML-voorbeeld](#xml)vinden.  

### <a name="attributes"></a>Kenmerken
| Name | Type | Beschrijving |
| --- | --- | --- |
| **DPLMVersionInformatie** |**xs:tekenreeks** |**Dolby** professionele luidheid meting ontwikkeling kit versie. |
| **Dialoogvenster Normalisatie**<br/><br/> minInclusive="-31" maxInclusive="-1"<br/><br/> Vereist |**xs:int** |DialogEnNormalisatie gegenereerd via DPLM, vereist wanneer LoudnessMetering is ingesteld |
| **Geïntegreerde luidheid**<br/><br/> minInclusive="-70" maxInclusive="10"<br/><br/> Vereist |**xs:float** |Geïntegreerde luidheid |
| **Geïntegreerde LoudnessUnit**<br/><br/> Vereist |**xs:tekenreeks** |Geïntegreerde luidheidseenheid. |
| **Geïntegreerde LoudnessGatingMethod**<br/><br/> Vereist |**xs:tekenreeks** |Gating-id |
| **Geïntegreerde LoudnessSpeechPercentage**<br/><br/> minInclusive ="0" maxInclusive="100" |**xs:float** |Spraakinhoud over het programma, als percentage. |
| **VoorbeeldPiek**<br/><br/> Vereist |**xs:float** |Piek absolute monsterwaarde, sinds reset of sinds het laatst werd gewist, per kanaal.  Eenheden zijn dBFS. |
| **VoorbeeldPiekEenheid**<br/><br/> fixed="dBFS"<br/><br/> Vereist |**xs:anySimpleType** |Voorbeeld piekeenheid. |
| **TruePeak TruePeak TruePeak TruePeak**<br/><br/> Vereist |**xs:float** |Maximale werkelijke piekwaarde, zoals per ITU-R BS.1770-2, sinds reset of sinds het laatst werd gewist, per kanaal. Eenheden zijn dBTP. |
| **TruePeakUnit TruePeakUnit**<br/><br/> fixed="dBTP"<br/><br/> Vereist |**xs:anySimpleType** |Echte piekeenheid. |

## <a name="schema-code"></a>Schemacode
    <?xml version="1.0" encoding="utf-8"?>  
    <xs:schema xmlns:xs="https://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata" version="1.2"  
               xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               targetNamespace="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata"  
               elementFormDefault="qualified">  
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
                  <xs:element name="Sources">  
                    <xs:annotation>  
                      <xs:documentation>Collection of input/source media files, that was processed in order to produce this AssetFile</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="Source" minOccurs="1" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>An input/source file used when generating this asset</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:attribute name="Name" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>input source file name</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="VideoTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>Each physical AssetFile can contain in it zero or more video tracks interleaved into an appropriate container format. This is the collection of all those video tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="VideoTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>A specific video track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
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
                            <xs:attribute name="FourCC" type="xs:string" use="required">  
                              <xs:annotation>  
                                <xs:documentation>video codec FourCC code</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Profile" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 profile (only appliable for H264 codec)</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="Level" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>H264 level (only appliable for H264 codec)</xs:documentation>  
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
                            <xs:attribute name="Framerate" use="required">  
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
                            <xs:attribute name="TargetFramerate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>preset target video frame rate in .3f format</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:decimal">  
                                  <xs:minInclusive value="0"/>  
                                  <xs:fractionDigits value="3"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average video bit rate in kilobits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="TargetBitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>target average bitrate for this video track, as requested via the encoding preset, in kilobits per second</xs:documentation>  
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
                          </xs:complexType>  
                        </xs:element>  
                      </xs:sequence>  
                    </xs:complexType>  
                  </xs:element>  
                  <xs:element name="AudioTracks" minOccurs="0">  
                    <xs:annotation>  
                      <xs:documentation>each physical AssetFile can contain in it zero or more audio tracks interleaved into an appropriate container format. This is the collection of all those audio tracks</xs:documentation>  
                    </xs:annotation>  
                    <xs:complexType>  
                      <xs:sequence>  
                        <xs:element name="AudioTrack" maxOccurs="unbounded">  
                          <xs:annotation>  
                            <xs:documentation>a specific audio track in the parent AssetFile</xs:documentation>  
                          </xs:annotation>  
                          <xs:complexType>  
                            <xs:sequence>  
                              <xs:element name="LoudnessMeteringResultParameters" minOccurs="0" maxOccurs="1">  
                                <xs:annotation>  
                                  <xs:documentation>Loudness Metering Result Parameters</xs:documentation>  
                                </xs:annotation>  
                                <xs:complexType>  
                                  <xs:attribute name="DPLMVersionInformation" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Dolby Professional Loudness Metering Development Kit Version</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="DialogNormalization" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation> DialogNormalization generated through DPLM, required when LoudnessMetering is set</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:int">  
                                        <xs:minInclusive value="-31"/>  
                                        <xs:maxInclusive value="-1"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudness" use="required">  
                                    <xs:annotation>  
                                      <xs:documentation>Integrated loudness</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="-70"/>  
                                        <xs:maxInclusive value="10"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessUnit" use="required" type="xs:string">  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessGatingMethod" use="required" type="xs:string">  
                                    <xs:annotation>  
                                      <xs:documentation>Gating identifier</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="IntegratedLoudnessSpeechPercentage">  
                                    <xs:annotation>  
                                      <xs:documentation>Speech content over the program, as a percentage.</xs:documentation>  
                                    </xs:annotation>  
                                    <xs:simpleType>  
                                      <xs:restriction base="xs:float">  
                                        <xs:minInclusive value="0"/>  
                                        <xs:maxInclusive value="100"/>  
                                      </xs:restriction>  
                                    </xs:simpleType>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Peak absolute sample value, since reset or since it was last cleared, per channel.  Units are dBFS.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="SamplePeakUnit" use="required" fixed="dBFS">  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeak" use="required" type="xs:float">  
                                    <xs:annotation>  
                                      <xs:documentation>Maximum True Peak value, as per ITU-R BS.1770-2, since reset or since it was last cleared, per channel.  Units are dBTP.</xs:documentation>  
                                    </xs:annotation>  
                                  </xs:attribute>  
                                  <xs:attribute name="TruePeakUnit" use="required" fixed="dBTP">  
                                  </xs:attribute>  
                                </xs:complexType>  
                              </xs:element>  
                            </xs:sequence>  
                            <xs:attribute name="Id" use="required">  
                              <xs:annotation>  
                                <xs:documentation>zero-based index of this audio track. Note: this is not necessarily the TrackID as used in an MP4 file</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="Codec" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>audio track codec string</xs:documentation>  
                              </xs:annotation>  
                            </xs:attribute>  
                            <xs:attribute name="EncoderVersion" type="xs:string">  
                              <xs:annotation>  
                                <xs:documentation>optional encoder version string, required for EAC3</xs:documentation>  
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
                            <xs:attribute name="Bitrate" use="required">  
                              <xs:annotation>  
                                <xs:documentation>average audio bit rate in bits per second, as calculated from the AssetFile. Counts only the elementary stream payload, and does not include the packaging overhead</xs:documentation>  
                              </xs:annotation>  
                              <xs:simpleType>  
                                <xs:restriction base="xs:int">  
                                  <xs:minInclusive value="0"/>  
                                </xs:restriction>  
                              </xs:simpleType>  
                            </xs:attribute>  
                            <xs:attribute name="BitsPerSample" use="required">  
                              <xs:annotation>  
                                <xs:documentation>Bits per sample for the wFormatTag format type</xs:documentation>  
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
                <xs:attribute name="Duration" use="required">  
                  <xs:annotation>  
                    <xs:documentation>content play back duration</xs:documentation>  
                  </xs:annotation>  
                  <xs:simpleType>  
                    <xs:restriction base="xs:duration"/>  
                  </xs:simpleType>  
                </xs:attribute>  
              </xs:complexType>  
            </xs:element>  
          </xs:sequence>  
        </xs:complexType>  
      </xs:element>  
    </xs:schema>  



## <a name="xml-example"></a><a name="xml"></a>XML-voorbeeld

De volgende XML is een voorbeeld van het metagegevensbestand Uitvoer.  

    <AssetFiles xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema"   
                xmlns="http://schemas.microsoft.com/windowsazure/mediaservices/2013/05/mediaencoder/metadata">  
      <AssetFile Name="BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4" Size="4646283" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.2" Width="1280" Height="720" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="4250" TargetBitrate="3400" MaxGOPBitrate="5514"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4" Size="3166728" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="2846" TargetBitrate="2250" MaxGOPBitrate="3630"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4" Size="2205095" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.1" Width="960" Height="540" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1932" TargetBitrate="1500" MaxGOPBitrate="2513"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4" Size="1508567" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="1271" TargetBitrate="1000" MaxGOPBitrate="1527"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4" Size="1057155" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="3.0" Width="640" Height="360" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="843" TargetBitrate="650" MaxGOPBitrate="1086"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4" Size="699262" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <VideoTracks>  
          <VideoTrack Id="0" FourCC="AVC1" Profile="Main" Level="1.3" Width="320" Height="180" DisplayAspectRatioNumerator="16" DisplayAspectRatioDenominator="9" Framerate="23.974" TargetFramerate="23.974" Bitrate="503" TargetBitrate="400" MaxGOPBitrate="661"/>  
        </VideoTracks>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_96kbps.mp4" Size="166780" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="93" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
      <AssetFile Name="BigBuckBunny_AAC_und_ch2_56kbps.mp4" Size="124576" Duration="PT8.4288444S">  
        <Sources>  
          <Source Name="BigBuckBunny.mp4"/>  
        </Sources>  
        <AudioTracks>  
          <AudioTrack Id="0" Codec="AacLc" Channels="2" SamplingRate="44100" Bitrate="53" BitsPerSample="16"/>  
        </AudioTracks>  
      </AssetFile>  
    </AssetFiles>  

## <a name="next-steps"></a>Volgende stappen
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geven
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
