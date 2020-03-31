---
title: Releasegeschiedenis van versie
titleSuffix: Microsoft Genomics
description: De releasegeschiedenis van updates voor de Microsoft Genomics Python-client voor fixes en nieuwe functionaliteit.
services: genomics
author: grhuynh
manager: cgronlun
ms.author: grhuynh
ms.service: genomics
ms.topic: conceptual
ms.date: 01/11/2019
ms.openlocfilehash: 20475e2cde1b42790740889cf341b3a0a0afccc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76991079"
---
# <a name="version-release-history"></a>Releasegeschiedenis van versie
Het Microsoft Genomics-team werkt de Microsoft Genomics Python-client regelmatig bij voor oplossingen en nieuwe functionaliteit. 

## <a name="latest-release"></a>Laatste release
De huidige Python-client is versie 0.9.0. Het werd uitgebracht op 6 februari 2019 en ondersteunt lopende workflows met GATK 3.5 en GATK4. Het ondersteunt gVCF-uitvoer en kan een optioneel argument voor uitvoercompressie accepteren.


## <a name="release-history"></a>Releasegeschiedenis 
Nieuwe versies van de Microsoft Genomics Python-client worden ongeveer één keer per jaar uitgebracht. Aangezien nieuwe versies van de Microsoft Genomics Python-client worden uitgebracht, wordt hier een lijst met oplossingen en functies bijgewerkt. Wanneer nieuwe versies worden uitgebracht, moeten eerdere versies ten minste 90 dagen worden ondersteund. Wanneer eerdere versies niet meer worden ondersteund, wordt deze op deze pagina aangegeven. 

### <a name="version-090"></a>Versie 0.9.0
Versie 0.9.0 bevat ondersteuning voor uitvoercompressie. Dit is gelijk `-bgzip` aan `-tabix` het uitvoeren gevolgd door op de vcf of gvcf output. Zie [Veelgestelde vragen](frequently-asked-questions-genomics.md)voor meer informatie. 

### <a name="version-081"></a>Versie 0.8.1
Versie 0.8.1 bevat kleine bugfixes.  

### <a name="version-080"></a>Versie 0.8.0
Versie 0.8.0 bevat ondersteuning voor GATK4 en outputting gVCFs.  

### <a name="version-074"></a>Versie 0.7.4
Versie 0.7.4 bevat ondersteuning voor het accepteren van `config.txt` SAS-tokens in plaats van accountsleutels in de invoer. Zie [Snel starten van SAS-tokens invoeren](quickstart-input-sas.md)voor meer informatie. 

### <a name="version-073"></a>Versie 0.7.3
Versie 0.7.3 bevat kleine bugfixes.

### <a name="version-072"></a>Versie 0.7.2
Versie 0.7.2 is de eerste versie. Het werd uitgebracht op 1 november 2017.
