---
title: Taakvoorinstelling voor Azure Media Indexer
description: In dit onderwerp vindt u een overzicht van de voorinstelling voor taken voor Azure Media Services Media Indexer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896030"
---
# <a name="task-preset-for-azure-media-indexer"></a>Taakvoorinstelling voor Azure Media Indexer 

Azure Media Indexer is een mediaprocessor die u gebruikt om de volgende taken uit te voeren: mediabestanden en inhoud doorzoekbaar maken, ondertitelingstracks en trefwoorden genereren, assetbestanden indexeren die deel uitmaken van uw asset.

In dit onderwerp wordt de taakvoorinstelling beschreven die u moet doorgeven aan uw indexeringstaak. Zie Bijvoorbeeld [mediabestanden indexeren met Azure Media Indexer](media-services-index-content.md).

## <a name="azure-media-indexer-configuration-xml"></a>Xml configuratie-XML voor configuratie azure media-indexer

In de volgende tabel worden elementen en kenmerken van de configuratie-XML uitgelegd.

|Name|Require|Beschrijving|
|---|---|---|
|Invoer|waar|Assetbestand(en) dat u wilt indexeren.<br/>Azure Media Indexer ondersteunt de volgende mediabestandsindelingen: MP4, MOV, WMV, MP3, M4A, WMA, AAC, WAV. <br/><br/>U de bestandsnaam(s) opgeven in de **naam** of **het lijstkenmerk** van het **invoerelement** (zoals hieronder wordt weergegeven). Als u niet opgeeft welk actiefbestand moet worden geïndexeerd, wordt het primaire bestand gekozen. Als er geen primaire assetbestand is ingesteld, wordt het eerste bestand in het invoerelement geïndexeerd.<br/><br/>Ga als volgt te werk om de naam van het assetbestand expliciet op te geven:<br/>```<input name="TestFile.wmv" />```<br/><br/>U ook meerdere assetbestanden tegelijk indexeren (maximaal 10 bestanden). Om dit te doen:<br/>- Maak een tekstbestand (manifestbestand) en geef het een .lst extensie.<br/>- Voeg een lijst met alle namen van assetbestanden in uw invoerelement toe aan dit manifestbestand.<br/>- Voeg het manifestbestand toe aan het actief.- Add (upload) the manifest file to the asset.<br/>- Geef de naam van het manifestbestand op in het lijstkenmerk van de invoer.<br/>```<input list="input.lst">```<br/><br/>**Let op:** Als u meer dan 10 bestanden aan het manifestbestand toevoegt, mislukt de indexeringstaak met de foutcode van 2006.|
|metagegevens|false|Metagegevens voor het opgegeven assetbestand(en).<br/>```<metadata key="..." value="..." />```<br/><br/>U waarden leveren voor vooraf gedefinieerde sleutels. <br/><br/>Momenteel worden de volgende toetsen ondersteund:<br/><br/>**titel** en **beschrijving** - wordt gebruikt om het taalmodel bij te werken om de nauwkeurigheid van spraakherkenning te verbeteren.<br/>```<metadata key="title" value="[Title of the media file]" /><metadata key="description" value="[Description of the media file]" />```<br/><br/>**gebruikersnaam** en **wachtwoord** - gebruikt voor authenticatie bij het downloaden van internetbestanden via http of https.<br/>```<metadata key="username" value="[UserName]" /><metadata key="password" value="[Password]" />```<br/>De gebruikersnaam- en wachtwoordwaarden zijn van toepassing op alle media-URL's in het invoermanifest.|
|functies<br/><br/>Toegevoegd in versie 1.2. Momenteel is de enige ondersteunde functie spraakherkenning ('ASR').|false|De functie Spraakherkenning heeft de volgende instellingentoetsen:<br/><br/>Taal:<br/>- De natuurlijke taal die in het multimediabestand moet worden herkend.<br/>- Engels, Spaans<br/><br/>Bijschriftindelingen:<br/>- een puntkomma-gescheiden lijst van de gewenste uitvoerbijschriftindelingen (indien aanwezig)<br/>- ttml;webvtt<br/><br/><br/>Trefwoorden genereren:<br/>- Een booleaanse vlag die aangeeft of een XML-trefwoordbestand vereist is.<br/>- Waar; Valse.|

## <a name="azure-media-indexer-configuration-xml-example"></a>XML-configuratie-XML-configuratie van Azure Media Indexer

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

Zie [Mediabestanden indexeren met Azure Media Indexer](media-services-index-content.md).

