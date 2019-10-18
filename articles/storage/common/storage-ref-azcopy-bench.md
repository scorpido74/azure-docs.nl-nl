---
title: azcopy Bank | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy bank opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518302"
---
# <a name="azcopy-bench"></a>azcopy Bank

Voert een prestatie-Bench Mark uit door test gegevens naar een opgegeven bestemming te uploaden. De test gegevens worden automatisch gegenereerd.

De Bench Mark-opdracht voert hetzelfde upload proces uit als ' kopie ', met uitzonde ring van het volgende:

  - Er is geen bron parameter.  Voor de opdracht is alleen een doel-URL vereist. In de huidige release moet deze doel-URL verwijzen naar een BLOB-container.
  
  - De payload wordt beschreven door opdracht regel parameters, waarmee wordt bepaald hoeveel bestanden automatisch worden gegenereerd en hoe groot ze zijn. Het generatie proces vindt volledig in het geheugen plaats. De schijf wordt niet gebruikt.
  
  - Slechts enkele van de optionele para meters die beschikbaar zijn voor de Kopieer opdracht worden ondersteund.
  
  - Aanvullende diagnostische gegevens worden gemeten en gerapporteerd.
  
  - Standaard worden de overgebrachte gegevens verwijderd aan het einde van de test uitvoering.

De Bench Mark-modus wordt automatisch afgestemd op het aantal parallelle TCP-verbindingen dat de maximale door Voer geeft. Het nummer wordt aan het einde weer gegeven. Stel de omgevings variabele AZCOPY_CONCURRENCY_VALUE in op een specifiek aantal verbindingen om automatisch afstemmen te voor komen.

Alle gebruikelijke verificatie typen worden ondersteund. De meest handige benadering voor benchmarking is meestal om een lege container met een SAS-token te maken en SAS-verificatie te gebruiken.

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy bench [destination] [flags]
```

Voer een bench Mark-test uit met de standaard parameters (geschikt voor benchmarking-netwerken tot 1 Gbps): '

- azcopy Bank "https://[account]. blob. core. Windows. net/[container]? <SAS> "

Voer een bench Mark-test uit die 100 bestanden uploadt, elk 2 GiB in grootte: (geschikt voor benchmarking op een snel netwerk, bijvoorbeeld 10 Gbps): '

- azcopy Bank "https://[account]. blob. core. Windows. net/[container]? <SAS> " --bestands aantal 100--grootte-per bestand, 2G

Hetzelfde als hierboven, maar gebruik 50.000-bestanden, elke 8 MiB in grootte en bereken de MD5-hashes (op dezelfde manier als met de vlag--put-MD5 wordt gebruikgemaakt van de Kopieer opdracht). Het doel van--put-MD5 wanneer u vergelijkt, is om te testen of de MD5-berekening van invloed is op de door Voer voor het geselecteerde aantal bestanden en de grootte:

- azcopy Bank "https://[account]. blob. core. Windows. net/[container]? <SAS> " --bestands aantal 50000--grootte-per-bestand 8 min.--put-MD5

## <a name="options"></a>Opties

**--BLOB-type** teken reeks definieert het type BLOB bij het doel. Wordt gebruikt om benchmarking van verschillende BLOB-typen toe te staan. Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht (standaard ' detect ').

**--block-size-MB** float gebruik deze blok grootte (opgegeven in MIB). De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan, bijvoorbeeld 0,25. Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht.

**--Delete-test-data**  Indien true, worden de benchmark gegevens verwijderd aan het einde van de Bench Mark-uitvoeringsrun.  Stel deze waarde in op ONWAAR als u de gegevens op het doel wilt blijven gebruiken, bijvoorbeeld voor hand matige tests buiten de Bench Mark-modus (standaard ingesteld op True).

**--Bestands aantal** uint het aantal automatisch gegenereerde gegevens bestanden dat moet worden gebruikt (standaard 100).

**-h,--Help**  Hulp voor Bank

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: info (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard INFO)

**--put-MD5**  Maak een MD5-hash van elk bestand en sla de hash op als de content-MD5-eigenschap van de BLOB/het doel bestand. (Standaard wordt de hash niet gemaakt.) Gelijk aan de para meter met dezelfde naam in de Kopieer opdracht.

**--grootte:** de teken reeks grootte per bestand van elk automatisch gegenereerd gegevens bestand. Dit moet een getal zijn dat onmiddellijk wordt gevolgd door K, M of G. bijv. 12k of 200G (standaard ' 250M ').

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps uint32**  De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaard waarde is ' text '. (standaard tekst).

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
