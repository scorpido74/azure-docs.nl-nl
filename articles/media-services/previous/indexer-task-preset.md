---
title: Taak voorinstelling voor Azure Media Indexer
description: In dit onderwerp vindt u een overzicht van de taak voorinstelling voor Azure Media Services Media Indexer.
services: media-services
documentationcenter: ''
author: Asolanki
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: 29753759af341f82429f12b6710ae9c32dcb4103
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "74896030"
---
# <a name="task-preset-for-azure-media-indexer"></a>Taak voorinstelling voor Azure Media Indexer 

Azure Media Indexer is een media processor die u gebruikt om de volgende taken uit te voeren: Media bestanden en inhoud doorzoekbaar maken, ondertitelings tracks en tref woorden genereren, Asset-index bestanden die deel uitmaken van uw asset.

In dit onderwerp wordt de taak voorinstelling beschreven die u moet door geven aan uw indexerings taak. Zie het [indexeren van media bestanden met Azure media indexer](media-services-index-content.md)voor een volledig voor beeld.

## <a name="azure-media-indexer-configuration-xml"></a>XML-configuratie Azure Media Indexer

In de volgende tabel worden elementen en kenmerken van de XML-configuratie beschreven.

|Naam|Require|Beschrijving|
|---|---|---|
|Invoer|waar|Activa bestand (en) die u wilt indexeren.<br/>Azure Media Indexer ondersteunt de volgende indelingen voor media bestanden: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>U kunt de bestands namen opgeven in het kenmerk **name** of **List** van het **input** -element (zoals hieronder weer gegeven). Als u niet opgeeft welk item bestand moet worden geïndexeerd, wordt het primaire bestand gekozen. Als er geen primair activa bestand is ingesteld, wordt het eerste bestand in de invoer Asset geïndexeerd.<br/><br/>Ga als volgt te werk om de naam van het activa bestand expliciet op te geven:<br/>```<input name="TestFile.wmv" />```<br/><br/>U kunt ook meerdere Asset-bestanden tegelijk indexeren (Maxi maal 10 bestanden). Om dit te doen:<br/>-Maak een tekst bestand (manifest bestand) en geef het de extensie. lst.<br/>-Voeg een lijst met alle bestands namen van assets in uw invoer Asset toe aan dit manifest bestand.<br/>-Voeg het manifest bestand toe (upload het) aan de Asset.<br/>-Geef de naam op van het manifest bestand in het lijst kenmerk van de invoer.<br/>```<input list="input.lst">```<br/><br/>**Opmerking:** Als u meer dan 10 bestanden toevoegt aan het manifest bestand, mislukt de indexerings taak met de fout code 2006.|
|metagegevens|false|Meta gegevens voor het opgegeven item bestand (en).<br/>```<metadata key="..." value="..." />```<br/><br/>U kunt waarden voor vooraf gedefinieerde sleutels opgeven. <br/><br/>Momenteel worden de volgende sleutels ondersteund:<br/><br/>**titel** en **Beschrijving** : wordt gebruikt om het taal model bij te werken om de nauw keurigheid van spraak herkenning te verbeteren.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**gebruikers naam** en **wacht woord** : wordt gebruikt voor verificatie bij het downloaden van Internet bestanden via http of https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>De waarden voor gebruikers naam en wacht woord zijn van toepassing op alle media-Url's in het invoer manifest.|
|functies<br/><br/>Toegevoegd in versie 1,2. Op dit moment is de enige ondersteunde functie spraak herkenning (ASR).|false|De functie voor spraak herkenning heeft de volgende instellingen sleutels:<br/><br/>Taal:<br/>-De natuurlijke taal die moet worden herkend in het multimedia bestand.<br/>-Engels, Spaans<br/><br/>CaptionFormats:<br/>-een door punt komma's gescheiden lijst met de gewenste indelingen van de uitvoer bijschriften (indien van toepassing)<br/>-ttml; webvtt<br/><br/><br/>GenerateKeywords:<br/>-Een Booleaanse vlag die aangeeft of een XML-bestand met een tref woord is vereist.<br/>Echte Terecht.|

## <a name="azure-media-indexer-configuration-xml-example"></a>Azure Media Indexer configuratie-XML-voor beeld

``` 
<?xml version="1.0" encoding="utf-8"?>  
<configuration version="2.0">  
  <input>  
    <metadata key="title" value="[Title of the media file]" />  
    <metadata key="description" value="[Description of the media file]" />  
  </input>  
  <settings>  
  </settings>  
  
  <features>  
    <feature name="ASR">    
      <settings>  
        <add key="Language" value="English"/>  
        <add key="GenerateKeywords" value ="true" />  
      </settings>  
    </feature>  
  </features>  
  
</configuration>  
```
  
## <a name="next-steps"></a>Volgende stappen

Zie [Media bestanden indexeren met Azure media indexer](media-services-index-content.md).

