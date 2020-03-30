---
title: Handleiding voor het oplossen van problemen
titleSuffix: Microsoft Genomics
description: Meer informatie over probleemoplossingsstrategieën voor het gebruik van Microsoft Genomics, inclusief foutmeldingen en het oplossen ervan.
keywords: probleemoplossing, fout, foutopsporing
services: genomics
author: ruchir
editor: jasonwhowell
ms.author: ruchir
ms.service: genomics
ms.workload: genomics
ms.topic: troubleshooting
ms.date: 10/29/2018
ms.openlocfilehash: f6ef56e4188a7541036db096e4ab35a1b95fc141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73485998"
---
# <a name="troubleshooting-guide"></a>Handleiding voor het oplossen van problemen

Hier volgen een paar tips voor het oplossen van problemen voor enkele van de veelvoorkomende problemen waarmee u te maken krijgen bij het gebruik van de Microsoft Genomics-service, MSGEN.

 Zie [Veelgestelde vragen](frequently-asked-questions-genomics.md)voor veelgestelde vragen die niet gerelateerd zijn aan probleemoplossing.
## <a name="step-1-locate-error-codes-associated-with-the-workflow"></a>Stap 1: Foutcodes zoeken die zijn gekoppeld aan de werkstroom

U de foutberichten die aan de werkstroom zijn gekoppeld, vinden door:

1. De opdrachtregel gebruiken en typen`msgen status`
2. Onderzoek van de inhoud van standardoutput.txt.

### <a name="1-using-the-command-line-msgen-status"></a>1. De opdrachtregel gebruiken`msgen status`

```bash
msgen status -u URL -k KEY -w ID 
```




Er zijn drie vereiste argumenten:

* URL - de basis URI voor de API
* KEY - de toegangssleutel voor uw Genomics-account
    * Als u uw URL en sleutel wilt vinden, gaat u naar de Azure-portal en opent u uw Microsoft Genomics-accountpagina. Kies **Access-toetsen**onder de kop **Beheer** . Daar vindt u zowel de API-URL als uw toegangssleutels.

  
* ID - de werkstroom-id
    * Als u de `msgen list` opdracht voor het type werkstroom-id wilt zoeken. Ervan uitgaande dat uw config-bestand de URL en uw toegangssleutels bevat en zich op dezelfde locatie bevindt als uw msgen exe, ziet de opdracht er als volgt uit: 
        
        ```bash
        msgen list -f "config.txt"
        ```
        Uitvoer van deze opdracht ziet er als volgt uit:
        
        ```bash
            Microsoft Genomics command-line client v0.7.4
                Copyright (c) 2018 Microsoft. All rights reserved.
                
                Workflow List
                -------------
                Total Count  : 1
                
                Workflow ID     : 10001
                Status          : Completed successfully
                Message         :
                Process         : snapgatk-20180730_1
                Description     :
                Created Date    : Mon, 27 Aug 2018 20:27:24 GMT
                End Date        : Mon, 27 Aug 2018 20:55:12 GMT
                Wall Clock Time : 0h 27m 48s
                Bases Processed : 1,348,613,600 (1 GBase)
        ```

  > [!NOTE]
  >  U ook het pad naar het config-bestand opnemen in plaats van rechtstreeks de URL en KEY in te voeren. Als u deze argumenten zowel in de opdrachtregel als in het config-bestand opneemt, hebben de opdrachtregelargumenten voorrang.  

Voor workflow-ID 1001 en config.txt-bestand dat op hetzelfde pad is geplaatst als het uitvoerbare msgen, ziet de opdracht er als volgt uit:

```bash
msgen status -w 1001 -f "config.txt"
```

### <a name="2--examine-the-contents-of-standardoutputtxt"></a>2. 
Zoek de uitvoercontainer voor de werkstroom in kwestie. MSGEN maakt `[workflowfilename].logs.zip` een map na elke werkstroomuitvoering. Rits de map uit om de inhoud te bekijken:

* outputFileList.txt - een lijst met de uitvoerbestanden die tijdens de werkstroom zijn geproduceerd
* standarderror.txt - dit bestand is leeg.
* standardoutput.txt - registreert alle statusberichten op het hoogste niveau, inclusief fouten, die zijn opgetreden tijdens het uitvoeren van de werkstroom.
* GATK-logboekbestanden - alle `logs` andere bestanden in de map

Voor het oplossen van problemen, onderzoekt u de inhoud van standardoutput.txt en noteer eventuele foutberichten die worden weergegeven.


## <a name="step-2-try-recommended-steps-for-common-errors"></a>Stap 2: Aanbevolen stappen voor veelvoorkomende fouten proberen

In dit gedeelte worden de algemene foutenuitvoer van Microsoft Genomics-service (msgen) en de strategieën die u gebruiken om deze op te lossen kort belicht. 

De Microsoft Genomics-service (msgen) kan de volgende twee soorten fouten bevatten:

1. Interne servicefouten: fouten die intern zijn voor de service, die mogelijk niet worden opgelost door parameters of invoerbestanden vast te stellen. Soms kan het opnieuw indienen van de werkstroom deze fouten oplossen.
2. Invoerfouten: fouten die kunnen worden opgelost met behulp van de juiste argumenten of het oplossen van bestandsindelingen.

### <a name="1-internal-service-errors"></a>1. Interne servicefouten

Een interne servicefout is niet bruikbaar voor de gebruiker. U de werkstroom opnieuw indienen, maar als dat niet werkt, neemt u contact op met de ondersteuning van Microsoft Genomics

| Foutbericht                                                                                                                            | Aanbevolen stappen voor probleemoplossing                                                                                                                                   |
|------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Er is een interne fout opgetreden. Probeer de werkstroom opnieuw in te dienen. Als u deze fout opnieuw ziet, neemt u contact op met de ondersteuning van Microsoft Genomics voor hulp | Verzend de werkstroom opnieuw. Neem contact op met de ondersteuning van Microsoft Genomics voor hulp als het probleem blijft bestaan door een [ondersteuningsticket](file-support-ticket-genomics.md )te maken. |

### <a name="2-input-errors"></a>2. Invoerfouten

Deze fouten zijn bruikbaar voor de gebruiker. Op basis van het type bestand en de foutcode worden afzonderlijke foutcodes uitgevoerd door de Microsoft Genomics-service. Volg de aanbevolen stappen voor het oplossen van problemen die hieronder worden vermeld.

| Type bestand | Foutcode | Foutbericht                                                                           | Aanbevolen stappen voor probleemoplossing                                                                                         |
|--------------|------------|-----------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------|
| Alle          | 701        | Lees [readId] heeft [numberOfBases] bases, maar de limiet is [maxReadLength]           | De meest voorkomende reden voor deze fout is bestandsbeschadiging die leidt tot samenvoeging van twee leest. Controleer uw invoerbestanden. |
| Bam          | 200        |   Kan bestand '[yourFileName]' niet lezen.                                                                                       | Controleer het formaat van het BAM-bestand. Verzend de werkstroom opnieuw met een correct opgemaakt bestand.                                                                           |
| Bam          | 201        |  Kan BAM-bestand [File_name] niet lezen.                                                                                      |Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                            |
| Bam          | 202        | Kan BAM-bestand [File_name] niet lezen. Bestand te klein en ontbrekende header.                                                                                        | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                            |
| Bam          | 203        |   Kan BAM-bestand [File_name] niet lezen. Header van het bestand was beschadigd.                                                                                      |Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                           |
| Bam          | 204        |    Kan BAM-bestand [File_name] niet lezen. Header van het bestand was beschadigd.                                                                                     | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                           |
| Bam          | 205        |    Kan BAM-bestand [File_name] niet lezen. Header van het bestand was beschadigd.                                                                                     | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                            |
| Bam          | 206        |   Kan BAM-bestand [File_name] niet lezen. Header van het bestand was beschadigd.                                                                                      | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                            |
| Bam          | 207        |  Kan BAM-bestand [File_name] niet lezen. Bestand afgekapt in de buurt van verschuiving [offset].                                                                                       | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                            |
| Bam          | 208        |   Ongeldig BAM-bestand. De ReadID [Read_Id] heeft geen sequentie in bestand [File_name].                                                                                      | Controleer het formaat van het BAM-bestand.  Verzend de werkstroom met een correct opgemaakt bestand.                                                                             |
| FASTQ FASTQ        | 300        |  Kan FASTQ-bestand niet lezen. [File_name] eindigt niet met een nieuwe regel.                                                                                     | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                           |
| FASTQ FASTQ        | 301        |   Kan FASTQ-bestand niet lezen [File_name]. FASTQ-record is groter dan buffergrootte bij offset: [_offset]                                                                                      | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                         |
| FASTQ FASTQ        | 302        |     FASTQ-syntaxisfout. Bestand [File_name] heeft een lege regel.                                                                                    | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                         |
| FASTQ FASTQ        | 303        |       FASTQ-syntaxisfout. Bestand[File_name] heeft een ongeldig beginteken bij verschuiving: [_offset], regeltype: [line_type], teken: [_char]                                                                                  | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                         |
| FASTQ FASTQ        | 304      |  FASTQ-syntaxisfout bij readID [_ReadID].  Eerste lezing van batch heeft geen readID eindigend in / 1 in bestand [File_name]                                                                                       | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                         |
| FASTQ FASTQ        | 305        |  FASTQ-syntaxisfout bij readID [_readID]. Tweede lezing van batch heeft geen readID eindigend in / 2 in bestand [File_name]                                                                                      | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                          |
| FASTQ FASTQ        | 306        |  FASTQ-syntaxisfout bij readID [_ReadID]. Eerste lezing van paar heeft geen ID die eindigt in / 1 in bestand [File_name]                                                                                       | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                          |
| FASTQ FASTQ        | 307        |   FASTQ-syntaxisfout bij readID [_ReadID]. ReadID eindigt niet met /1 of/2. Bestand [File_name] kan niet worden gebruikt als een gekoppeld FASTQ-bestand.                                                                                      |Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                          |
| FASTQ FASTQ        | 308        |  FASTQ leesfout. Reads van beide uiteinden reageerden verschillend. Heb je de juiste FASTQ bestanden gekozen?                                                                                       | Corrigeer de indeling van het FASTQ-bestand en dien de werkstroom opnieuw in.                                                                         |
|        |       |                                                                                        |                                                                           |

## <a name="step-3-contact-microsoft-genomics-support"></a>Stap 3: Neem contact op met de ondersteuning van Microsoft Genomics

Als u nog steeds taakfouten hebt of als u andere vragen hebt, neemt u contact op met microsoft Genomics-ondersteuning via de Azure-portal. Aanvullende informatie over het indienen van een ondersteuningsverzoek vindt u [hier.](file-support-ticket-genomics.md)

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u veelvoorkomende problemen met de Microsoft Genomics-service oplossen en oplossen. Zie [Algemene vragen voor](frequently-asked-questions-genomics.md)meer informatie en algemene veelgestelde vragen. 
