---
title: H264 Single Bitrate 4K Audio 5.1 | Microsoft Documenten
description: Het onderwerp geeft een overzicht van de **H264 Single Bitrate 4K Audio 5.1** taak preset.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 72cb95ac-2cd6-4ef4-9938-8f22cea04920
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 9c4b133ae74438def30f4fcd9e49a5ca5e6f9cd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61463562"
---
# <a name="h264-single-bitrate-4k-audio-51"></a>H264 Single Bitrate 4K Audio 5.1
`Media Encoder Standard`definieert een set coderingsvoorinstellingen die u gebruiken bij het maken van coderingstaken. U een `preset name` a gebruiken om aan te geven in welke indeling u uw mediabestand wilt coderen. U ook uw eigen JSON- of XML-gebaseerde voorinstellingen maken (met UTF-8- of UTF-16-codering. U zou dan de aangepaste voorinstelling doorgeven aan de encoder. Zie `Media Encoder Standard` [Taakvoorinstellingen voor Media Encoder Standard voor](media-services-mes-presets-overview.md)de lijst met alle vooraf ingestelde namen die door deze encoder worden ondersteund.  
  
 In dit `H264 Single Bitrate 4K Audio 5.1` onderwerp wordt de voorinstelling weergegeven (in XML- en JSON-indeling).  
  
 Deze voorinstelling produceert een enkel MP4-bestand met een bitrate van 18000 kbps en AAC 5.1-audio. Voor gedetailleerde informatie over profiel, bitrate, sampling rate, etc. van deze preset, onderzoekt u de XML of JSON die hieronder is gedefinieerd. Zie het [schema Media Encoder Standard](media-services-mes-schema.md)voor uitleg over wat elk element betekent en de geldige waarden voor elk element.  
  
> [!NOTE]
>  Je moet de Premium gereserveerde eenheid type met 4K codeert. Zie [Codering schalen voor](https://azure.microsoft.com/documentation/articles/media-services-portal-encoding-units)meer informatie .  
  
 XML  
  
```  
<?xml version="1.0" encoding="utf-16"?>  
<Preset xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">  
  <Encoding>  
    <H264Video>  
      <KeyFrameInterval>00:00:02</KeyFrameInterval>  
      <SceneChangeDetection>true</SceneChangeDetection>  
      <H264Layers>  
        <H264Layer>  
          <Bitrate>18000</Bitrate>  
          <Width>3840</Width>  
          <Height>2160</Height>  
          <FrameRate>0/1</FrameRate>  
          <Profile>Auto</Profile>  
          <Level>auto</Level>  
          <BFrames>3</BFrames>  
          <ReferenceFrames>3</ReferenceFrames>  
          <Slices>0</Slices>  
          <AdaptiveBFrame>true</AdaptiveBFrame>  
          <EntropyMode>Cabac</EntropyMode>  
          <BufferWindow>00:00:05</BufferWindow>  
          <MaxBitrate>18000</MaxBitrate>  
        </H264Layer>  
      </H264Layers>  
      <Chapters />  
    </H264Video>  
    <AACAudio>  
      <Profile>AACLC</Profile>  
      <Channels>6</Channels>  
      <SamplingRate>48000</SamplingRate>  
      <Bitrate>384</Bitrate>  
    </AACAudio>  
  </Encoding>  
  <Outputs>  
    <Output FileName="{Basename}_{Width}x{Height}_{VideoBitrate}.mp4">  
      <MP4Format />  
    </Output>  
  </Outputs>  
</Preset>  
```  
  
 JSON  
  
```  
{  
  "Version": 1.0,  
  "Codecs": [  
    {  
      "KeyFrameInterval": "00:00:02",  
      "SceneChangeDetection": true,  
      "H264Layers": [  
        {  
          "Profile": "Auto",  
          "Level": "auto",  
          "Bitrate": 18000,  
          "MaxBitrate": 18000,  
          "BufferWindow": "00:00:05",  
          "Width": 3840,  
          "Height": 2160,  
          "BFrames": 3,  
          "ReferenceFrames": 3,  
          "AdaptiveBFrame": true,  
          "Type": "H264Layer",  
          "FrameRate": "0/1"  
        }  
      ],  
      "Type": "H264Video"  
    },  
    {  
      "Profile": "AACLC",  
      "Channels": 6,  
      "SamplingRate": 48000,  
      "Bitrate": 384,  
      "Type": "AACAudio"  
    }  
  ],  
  "Outputs": [  
    {  
      "FileName": "{Basename}_{Width}x{Height}_{VideoBitrate}.mp4",  
      "Format": {  
        "Type": "MP4Format"  
      }  
    }  
  ]  
}  
```
