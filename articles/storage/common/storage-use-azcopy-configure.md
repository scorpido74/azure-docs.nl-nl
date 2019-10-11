---
title: AzCopy configureren, optimaliseren en problemen oplossen met Azure Storage | Microsoft Docs
description: AzCopy configureren, optimaliseren en problemen oplossen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 42d2dae148b83687ff06d4ed321a881bcb9e7ae0
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273937"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy configureren, optimaliseren en problemen oplossen

AzCopy is een opdracht regel programma dat u kunt gebruiken voor het kopiëren van blobs of bestanden naar of van een opslag account. Dit artikel helpt u bij het uitvoeren van geavanceerde configuratie taken en helpt u bij het oplossen van problemen die kunnen optreden tijdens het gebruik van AzCopy.

> [!NOTE]
> Als u op zoek bent naar inhoud die u helpt aan de slag te gaan met AzCopy, raadpleegt u een van de volgende artikelen:
> - [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
> - [Gegevens overdragen met AzCopy en Blob Storage](storage-use-azcopy-blobs.md)
> - [Gegevens overdragen met AzCopy en File Storage](storage-use-azcopy-files.md)
> - [Gegevens overdragen met AzCopy en Amazon S3-buckets](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxy-instellingen configureren

Als u de proxy-instellingen voor AzCopy wilt configureren, stelt u de omgevings variabele `https_proxy` in. Als u AzCopy uitvoert in Windows, detecteert AzCopy automatisch proxy-instellingen. u hoeft deze instelling niet te gebruiken in Windows. Als u kiest voor het gebruik van deze instelling in Windows, wordt automatische detectie vervangen.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | In een opdracht prompt gebruikt u: `set https_proxy=<proxy IP>:<proxy port>`<br> In Power shell gebruiken: `$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **MacOS** | `export https_proxy=<proxy IP>:<proxy port>` |

Momenteel biedt AzCopy geen ondersteuning voor proxy's waarvoor authenticatie met NTLM of Kerberos is vereist.

## <a name="optimize-performance"></a>Prestaties optimaliseren

U kunt benchmark prestaties en vervolgens opdrachten en omgevings variabelen gebruiken om een optimale balans te vinden tussen prestaties en Resource verbruik.

### <a name="run-benchmark-tests"></a>Bench Mark-tests uitvoeren

U kunt een benchmark test voor prestaties uitvoeren op specifieke BLOB-containers om algemene prestatie statistieken en knel punten voor identiteits prestaties te bekijken. 

> [!NOTE]
> In de huidige versie is deze functie alleen beschikbaar voor Blob Storage-containers.

Gebruik de volgende opdracht om een bench Mark-test voor prestaties uit te voeren.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory/'` |

Met deze opdracht wordt een prestatie Bench Mark uitgevoerd door test gegevens naar een opgegeven bestemming te uploaden. De test gegevens worden in het geheugen gegenereerd, geüpload naar het doel en vervolgens verwijderd uit de bestemming nadat de test is voltooid. U kunt opgeven hoeveel bestanden er moeten worden gegenereerd en welke grootte u wilt gebruiken met optionele opdracht parameters.

Als u gedetailleerde Help-informatie voor deze opdracht wilt weer geven, typt u `azcopy bench -h` en drukt u vervolgens op ENTER.

### <a name="optimize-throughput"></a>Door Voer optimaliseren

U kunt de vlag `cap-mbps` gebruiken om een plafond te plaatsen op basis van het gegevens aantal door voer. Met de volgende opdracht wordt bijvoorbeeld een hoofd letter doorvoer tot `10` megabits (MB) per seconde.

```azcopy
azcopy cap-mbps 10
```

De door Voer kan afnemen bij het overbrengen van kleine bestanden. U kunt de door Voer verhogen door de omgevings variabele `AZCOPY_CONCURRENCY_VALUE` in te stellen. Met deze variabele geeft u het aantal gelijktijdige aanvragen op dat kan worden uitgevoerd.  

Als uw computer minder dan 5 Cpu's heeft, wordt de waarde van deze variabele ingesteld op `32`. Anders is de standaard waarde gelijk aan 16 vermenigvuldigd met het aantal Cpu's. De maximale standaard waarde van deze variabele is `3000`, maar u kunt deze waarde ook op een hoger of lager niveau instellen. 

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **MacOS** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, kunt u lezen welke waarde wordt gebruikt door te kijken naar het begin van een AzCopy-logboek bestand. De geselecteerde waarde, en de reden dat deze is geselecteerd, worden daar gerapporteerd.

Voordat u deze variabele instelt, wordt u aangeraden een bench Mark-test uit te voeren. In het Bench Mark-test proces wordt de aanbevolen gelijktijdigheids waarde gerapporteerd. Als uw netwerk voorwaarden en nettoladingen verschillen, stelt u deze variabele in op het woord `AUTO` in plaats van een bepaald aantal. Hierdoor wordt AzCopy altijd hetzelfde automatische afstemmings proces uitgevoerd dat wordt gebruikt in Bench Mark-tests.

### <a name="optimize-memory-use"></a>Geheugen gebruik optimaliseren

Stel de omgevings variabele `AZCOPY_BUFFER_GB` in om de maximale hoeveelheid van het systeem geheugen op te geven dat door AzCopy moet worden gebruikt bij het downloaden en uploaden van bestanden.
Deze waarde in gigabytes (GB) uitdrukken.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **MacOS** | `export AZCOPY_BUFFER_GB=<value>` |

## <a name="troubleshoot-issues"></a>Problemen oplossen

AzCopy maakt logboek-en plan bestanden voor elke taak. U kunt de Logboeken gebruiken om potentiële problemen te onderzoeken en op te lossen. 

De logboeken bevatten de status mislukt (`UPLOADFAILED`, `COPYFAILED` en `DOWNLOADFAILED`), het volledige pad en de reden van de fout.

Standaard bevinden de logboek-en plan bestanden zich in de map @no__t 0 in Windows of `$HOME$\.azcopy` Directory op Mac en Linux, maar u kunt deze locatie desgewenst wijzigen.

> [!IMPORTANT]
> Wanneer u een aanvraag indient om Microsoft Ondersteuning (of het probleem met een derde partij op te lossen), deelt u de geredigeerde versie van de opdracht die u wilt uitvoeren. Dit zorgt ervoor dat de SAS niet per ongeluk met iedereen wordt gedeeld. U kunt de geredigeerde versie vinden aan het begin van het logboek bestand.

### <a name="review-the-logs-for-errors"></a>De logboeken controleren op fouten

Met de volgende opdracht worden alle fouten met de status `UPLOADFAILED` uit het logboek van `04dc9ca9-158f-7945-5933-564021086c79` opgehaald:

**Windows (Power shell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Taken weer geven en hervatten

Bij elke overdrachts bewerking wordt een AzCopy-taak gemaakt. Gebruik de volgende opdracht om de geschiedenis van taken weer te geven:

```
azcopy jobs list
```

Als u de taak statistieken wilt weer geven, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id>
```

Als u de overdrachten wilt filteren op status, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id> --with-status=Failed
```

Gebruik de volgende opdracht om een mislukte/geannuleerde taak te hervatten. Met deze opdracht wordt de id samen met het SAS-token gebruikt, omdat dit om veiligheids redenen niet permanent is:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

Wanneer u een taak hervat, zoekt AzCopy naar het job plan-bestand. In het plan bestand worden alle bestanden vermeld die zijn geïdentificeerd voor verwerking toen de taak voor het eerst werd gemaakt. Wanneer u een taak hervat, probeert AzCopy alle bestanden over te dragen die worden vermeld in het plan bestand dat niet al is overgedragen.

## <a name="change-the-location-of-the-plan-and-log-files"></a>De locatie van het plan en de logboek bestanden wijzigen

Plan-en logboek bestanden bevinden zich standaard in de map @no__t 0 in Windows of in de map `$HOME$\.azcopy` op Mac en Linux. U kunt deze locatie wijzigen.

### <a name="change-the-location-of-plan-files"></a>De locatie van plan bestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden de bestanden plannen naar de standaard locatie geschreven.

### <a name="change-the-location-of-log-files"></a>De locatie van de logboek bestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **MacOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Gebruik de `azcopy env` om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden logboeken naar de standaard locatie geschreven.

## <a name="change-the-default-log-level"></a>Het standaard logboek niveau wijzigen

AzCopy-logboek niveau is standaard ingesteld op `INFO`. Als u de uitgebreidheid van het logboek wilt beperken om schijf ruimte te besparen, kunt u deze instelling overschrijven met de optie ``--log-level``. 

Beschik bare logboek niveaus zijn: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` en `FATAL`.

## <a name="remove-plan-and-log-files"></a>Plan-en logboek bestanden verwijderen

Als u alle plannings-en logboek bestanden van uw lokale computer wilt verwijderen om schijf ruimte te besparen, gebruikt u de `azcopy jobs clean` opdracht.

Als u de plannings-en logboek bestanden die zijn gekoppeld aan één taak wilt verwijderen, gebruikt u `azcopy jobs rm <job-id>`. Vervang de tijdelijke aanduiding `<job-id>` in dit voor beeld door de taak-id van de taak.


