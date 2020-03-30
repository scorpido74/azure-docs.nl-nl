---
title: azcopy bank | Microsoft Documenten
description: Dit artikel bevat referentie-informatie voor de azcopy bank commando.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518302"
---
# <a name="azcopy-bench"></a>azcopy bench

Voert een prestatiebenchmark uit door testgegevens te uploaden naar een bepaalde bestemming. De testgegevens worden automatisch gegenereerd.

De benchmarkopdracht voert hetzelfde uploadproces uit als 'kopiëren', behalve dat:

  - Er is geen bronparameter.  De opdracht vereist alleen een bestemmings-URL. In de huidige versie moet deze bestemmings-URL verwijzen naar een blobcontainer.
  
  - De payload wordt beschreven door command line parameters, die bepalen hoeveel bestanden automatisch worden gegenereerd en hoe groot ze zijn. Het generatieproces vindt volledig plaats in het geheugen. Schijf wordt niet gebruikt.
  
  - Slechts enkele van de optionele parameters die beschikbaar zijn voor de kopieeropdracht worden ondersteund.
  
  - Aanvullende diagnostiek wordt gemeten en gerapporteerd.
  
  - Standaard worden de overgedragen gegevens aan het einde van de testrun verwijderd.

De benchmarkmodus stemt zichzelf automatisch af op het aantal parallelle TCP-verbindingen dat de maximale doorvoer geeft. Het zal dat nummer aan het eind tonen. Als u automatisch afstemmen wilt voorkomen, stelt u de AZCOPY_CONCURRENCY_VALUE omgevingsvariabele in op een bepaald aantal verbindingen.

Alle gebruikelijke verificatietypen worden ondersteund. De meest handige benadering voor benchmarking is echter meestal het maken van een lege container met een SAS-token en het gebruik van SAS-verificatie.

## <a name="examples"></a>Voorbeelden

```azcopy
azcopy bench [destination] [flags]
```

Voer een benchmarktest uit met standaardparameters (geschikt voor het benchmarken van netwerken tot 1 Gbps):'

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>"

Voer een benchmarktest uit die 100 bestanden uploadt, elk 2 GiB in grootte: (geschikt voor benchmarking op een snel netwerk, bijvoorbeeld 10 Gbps):'

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 100 --size-per-file 2G

Hetzelfde als hierboven, maar gebruik 50.000 bestanden, elk 8 MiB in grootte en bereken hun MD5 hashes (op dezelfde manier dat de --put-md5 vlag doet dit in de kopie commando). Het doel van --put-md5 bij benchmarking is om te testen of MD5-berekening van invloed is op de doorvoer voor het geselecteerde aantal bestanden en de grootte:

- azcopy bench "https://[account].blob.core.windows.net/[container]? <SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Opties

**--blob-type** tekenreeks Definieert het type blob op de bestemming. Wordt gebruikt om benchmarking van verschillende blobtypen mogelijk te maken. Identiek aan de parameter met dezelfde naam in de opdracht Kopiëren (standaard 'Detecteren').

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB). Standaard wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan - bijvoorbeeld 0,25. Identiek aan de parameter met dezelfde naam in de opdracht Kopiëren.

**--delete-test-data**  Als dit waar is, worden de benchmarkgegevens aan het einde van de benchmarkrun verwijderd.  Stel deze in op false als u de gegevens op de plaats van bestemming wilt bewaren - bijvoorbeeld om deze te gebruiken voor handmatige tests buiten de benchmarkmodus (standaard true).

**--file-count** uint Het aantal automatisch gegenereerde gegevensbestanden dat moet worden gebruikt (standaard 100).

**-h, --help**  Hulp voor bank

**--tekenreeks op logboekniveau** Definieer de logboekverbositeit voor het logboekbestand, beschikbare niveaus: INFO (alle aanvragen/antwoorden), WAARSCHUWING(langzame reacties), FOUT (alleen mislukte aanvragen) en GEEN (geen uitvoerlogboeken). (standaard "INFO")

**--put-md5**  Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doelblob/-bestand. (Standaard wordt de hash NIET gemaakt.) Identiek aan de parameter met dezelfde naam in de opdracht Kopiëren.

**--grootte-per-bestand** tekenreeks Grootte van elk automatisch gegenereerd gegevensbestand. Moet een nummer onmiddellijk gevolgd door K, M of G. Bijv. 12k of 200G (standaard "250M").

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps uint32**  Caps de overdrachtssnelheid, in megabits per seconde. De doorvoer van moment tot moment kan enigszins afwijken van de dop. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgetopt.

**--uitvoer-type** tekenreeks Notatie van de uitvoer van de opdracht. De keuzes zijn: tekst, json. De standaardwaarde is 'tekst'. (standaard 'tekst').

## <a name="see-also"></a>Zie ook

- [azcopie](storage-ref-azcopy.md)
