---
title: AzCopy configureren, optimaliseren en oplossen met Azure Storage | Microsoft Documenten
description: AzCopy configureren, optimaliseren en oplossen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 87a335f44a31436de735395adbee9035493cbbd2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263417"
---
# <a name="configure-optimize-and-troubleshoot-azcopy"></a>AzCopy configureren, optimaliseren en oplossen

AzCopy is een command-line hulpprogramma dat u gebruiken om blobs of bestanden te kopiëren van of naar een opslagaccount. In dit artikel u geavanceerde configuratietaken uitvoeren en u problemen oplossen die zich kunnen voordoen wanneer u AzCopy gebruikt.

> [!NOTE]
> Als u op zoek bent naar inhoud om u op weg te helpen met AzCopy, raadpleegt u een van de volgende artikelen:
> - [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
> - [Gegevens overbrengen met AzCopy- en blob-opslag](storage-use-azcopy-blobs.md)
> - [Gegevens overbrengen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
> - [Gegevens overbrengen met AzCopy- en Amazon S3-buckets](storage-use-azcopy-s3.md)

## <a name="configure-proxy-settings"></a>Proxyinstellingen configureren

Als u de proxy-instellingen voor `https_proxy` AzCopy wilt configureren, stelt u de omgevingsvariabele in. Als u AzCopy op Windows uitvoert, detecteert AzCopy automatisch proxy-instellingen, zodat u deze instelling niet hoeft te gebruiken in Windows. Als u ervoor kiest deze instelling in Windows te gebruiken, wordt de automatische detectie overschreven.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | In een opdrachtpromptgebruik:`set https_proxy=<proxy IP>:<proxy port>`<br> In PowerShell gebruik:`$env:https_proxy="<proxy IP>:<proxy port>"`|
| **Linux** | `export https_proxy=<proxy IP>:<proxy port>` |
| **Macos** | `export https_proxy=<proxy IP>:<proxy port>` |

AzCopy biedt momenteel geen ondersteuning voor proxy's waarvoor verificatie vereist is met NTLM of Kerberos.

## <a name="optimize-performance"></a>Prestaties optimaliseren

U prestaties benchmarken en vervolgens opdrachten en omgevingsvariabelen gebruiken om een optimale afweging te vinden tussen prestaties en resourceverbruik.

Met deze sectie u deze optimalisatietaken uitvoeren:

> [!div class="checklist"]
> * Benchmarktests uitvoeren
> * Doorvoer optimaliseren
> * Geheugengebruik optimaliseren 
> * Bestandssynchronisatie optimaliseren

### <a name="run-benchmark-tests"></a>Benchmarktests uitvoeren

U een prestatiebenchmarktest uitvoeren op specifieke blobcontainers om algemene prestatiestatistieken te bekijken en om knelpunten in identiteitsprestaties te bekijken. 

Gebruik de volgende opdracht om een prestatiebenchmarktest uit te voeren.

|    |     |
|--------|-----------|
| **Syntaxis** | `azcopy bench 'https://<storage-account-name>.blob.core.windows.net/<container-name>'` |
| **Voorbeeld** | `azcopy bench 'https://mystorageaccount.blob.core.windows.net/mycontainer/myBlobDirectory?sv=2018-03-28&ss=bjqt&srs=sco&sp=rjklhjup&se=2019-05-10T04:37:48Z&st=2019-05-09T20:37:48Z&spr=https&sig=%2FSOVEFfsKDqRry4bk3qz1vAQFwY5DDzp2%2B%2F3Eykf%2FJLs%3D'` |

> [!TIP]
> In dit voorbeeld worden padargumenten omsluit met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

Met deze opdracht wordt een prestatiebenchmark uitgevoerd door testgegevens naar een bepaalde bestemming te uploaden. De testgegevens worden gegenereerd in het geheugen, geüpload naar de bestemming en vervolgens van de bestemming verwijderd nadat de test is voltooid. U opgeven hoeveel bestanden u wilt genereren en hoe groot u wilt dat ze zijn met behulp van optionele opdrachtparameters.

Zie [azcopy bench](storage-ref-azcopy-bench.md)voor gedetailleerde referentiedocumenten.

Als u gedetailleerde helprichtlijnen voor `azcopy bench -h` deze opdracht wilt weergeven, typt u en drukt u op enter.

### <a name="optimize-throughput"></a>Doorvoer optimaliseren

U `cap-mbps` de vlag in uw opdrachten gebruiken om een plafond op de doorvoergegevenssnelheid te plaatsen. Met de volgende opdracht wordt bijvoorbeeld een `10` taak hervat en wordt de doorvoer naar megabits (MB) per seconde gecapd. 

```azcopy
azcopy jobs resume <job-id> --cap-mbps 10
```

Doorvoer kan afnemen bij het overbrengen van kleine bestanden. U de doorvoer verhogen `AZCOPY_CONCURRENCY_VALUE` door de omgevingsvariabele in te stellen. Deze variabele geeft het aantal gelijktijdige aanvragen op dat kan optreden.  

Als uw computer minder dan 5 CPU's heeft, `32`wordt de waarde van deze variabele ingesteld op . Anders is de standaardwaarde gelijk aan 16 vermenigvuldigd met het aantal CPU's. De maximale standaardwaarde `3000`van deze variabele is, maar u deze waarde handmatig hoger of lager instellen. 

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Linux** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |
| **Macos** | `export AZCOPY_CONCURRENCY_VALUE=<value>` |

Gebruik `azcopy env` de om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, u lezen welke waarde wordt gebruikt door te kijken naar het begin van een AzCopy-logboekbestand. De geselecteerde waarde en de reden waarom deze is geselecteerd, worden daar gerapporteerd.

Voordat u deze variabele instelt, raden we u aan een benchmarktest uit te voeren. Het benchmarktestproces rapporteert de aanbevolen gelijktijdigheidswaarde. Als uw netwerkomstandigheden en payloads variëren, stelt u `AUTO` deze variabele ook in op het woord in plaats van op een bepaald nummer. Dat zal ertoe leiden dat AzCopy altijd hetzelfde automatische tuningproces uitvoert dat het gebruikt in benchmarktests.

### <a name="optimize-memory-use"></a>Geheugengebruik optimaliseren

Stel `AZCOPY_BUFFER_GB` de omgevingsvariabele in om de maximale hoeveelheid van uw systeemgeheugen op te geven die u wilt dat AzCopy gebruikt bij het downloaden en uploaden van bestanden.
Deze waarde uitdrukken in gigabytes (GB).

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_BUFFER_GB=<value>` |
| **Linux** | `export AZCOPY_BUFFER_GB=<value>` |
| **Macos** | `export AZCOPY_BUFFER_GB=<value>` |

### <a name="optimize-file-synchronization"></a>Bestandssynchronisatie optimaliseren

De [opdracht synchroniseren](storage-ref-azcopy-sync.md) identificeert alle bestanden op de bestemming en vergelijkt vervolgens bestandsnamen en laatst gewijzigde tijdstempels voordat de synchronisatiebewerking wordt gestart. Als u een groot aantal bestanden hebt, u de prestaties verbeteren door deze verwerking vooraf te elimineren. 

Gebruik hiervoor in plaats daarvan de opdracht [azcopy-kopie](storage-ref-azcopy-copy.md) en stelt de `--overwrite` vlag in op `ifSourceNewer`. AzCopy zal bestanden vergelijken als ze worden gekopieerd zonder het uitvoeren van up-front scans en vergelijkingen. Dit biedt een prestatievoordeel in gevallen waarin er een groot aantal bestanden te vergelijken zijn.

De [azcopy-kopieopdracht](storage-ref-azcopy-copy.md) verwijdert geen bestanden van de bestemming, dus als u bestanden op de bestemming wilt verwijderen wanneer ze `--delete-destination` niet meer bij `true` `prompt`de bron bestaan, gebruikt u de opdracht [azcopy-synchronisatie](storage-ref-azcopy-sync.md) met de vlag ingesteld op een waarde van of . 

## <a name="troubleshoot-issues"></a>Problemen oplossen

AzCopy maakt log- en planbestanden voor elke taak. U de logboeken gebruiken om mogelijke problemen te onderzoeken en op te lossen. 

De logboeken bevatten de`UPLOADFAILED`status `COPYFAILED`van `DOWNLOADFAILED`het mislukken ( , en ), het volledige pad en de reden van de fout.

Standaard bevinden de logboek- en planbestanden zich in de `%USERPROFILE%\.azcopy` directory op Windows of `$HOME$\.azcopy` directory op Mac en Linux, maar u die locatie wijzigen als u dat wilt.

De relevante fout is niet noodzakelijkerwijs de eerste fout die in het bestand wordt weergegeven. Voor fouten zoals netwerkfouten, time-outs en serverdrukke fouten probeert AzCopy tot 20 keer opnieuw en meestal slaagt het proces voor opnieuw proberen.  De eerste fout die u ziet, is mogelijk iets onschadelijks dat opnieuw is geprobeerd.  Dus in plaats van te kijken naar de eerste fout `UPLOADFAILED` `COPYFAILED`in `DOWNLOADFAILED`het bestand, kijk voor de fouten die in de buurt, , of . 

> [!IMPORTANT]
> Wanneer u een verzoek indient bij Microsoft Support (of het probleem oplost waarbij een derde partij betrokken is), deelt u de geredigeerde versie van de opdracht die u wilt uitvoeren. Dit zorgt ervoor dat de SAS niet per ongeluk met iemand wordt gedeeld. U vindt de geredigeerde versie aan het begin van het logboekbestand.

### <a name="review-the-logs-for-errors"></a>De logboeken controleren op fouten

Met de volgende opdracht `UPLOADFAILED` worden alle `04dc9ca9-158f-7945-5933-564021086c79` fouten met de status uit het logboek verwijderd:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

### <a name="view-and-resume-jobs"></a>Taken weergeven en hervatten

Elke overdrachtsbewerking maakt een AzCopy-taak. Gebruik de volgende opdracht om de geschiedenis van taken weer te geven:

```
azcopy jobs list
```

Als u de taakstatistieken wilt weergeven, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id>
```

Als u de overdrachten wilt filteren op status, gebruikt u de volgende opdracht:

```
azcopy jobs show <job-id> --with-status=Failed
```

Gebruik de volgende opdracht om een mislukte/geannuleerde taak te hervatten. Deze opdracht gebruikt de id samen met het SAS-token omdat deze om veiligheidsredenen niet persistent is:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>"
azcopy jobs resume <job-id> --destination-sas="<sas-token>"
```

> [!TIP]
> Sluit padargumenten zoals het SAS-token om met enkele aanhalingstekens (''). Gebruik enkele aanhalingstekens in alle opdrachtshells, behalve de Windows Command Shell (cmd.exe). Als u een Windows Command Shell (cmd.exe) gebruikt, sluit u padargumenten om met dubbele aanhalingstekens ("") in plaats van enkele aanhalingstekens ('').

Wanneer u een taak hervat, bekijkt AzCopy het taakplanbestand. In het planbestand worden alle bestanden weergegeven die zijn geïdentificeerd voor verwerking toen de taak voor het eerst werd gemaakt. Wanneer u een taak hervat, probeert AzCopy alle bestanden over te zetten die zijn opgenomen in het planbestand dat nog niet is overgedragen.

## <a name="change-the-location-of-the-plan-and-log-files"></a>De locatie van het plan en logboekbestanden wijzigen

Standaard bevinden u plan- en `%USERPROFILE%\.azcopy` logbestanden zich in `$HOME$\.azcopy` de directory op Windows of in de map op Mac en Linux. U deze locatie wijzigen.

### <a name="change-the-location-of-plan-files"></a>De locatie van planbestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Macos** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Gebruik `azcopy env` de om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden bestanden plannen naar de standaardlocatie geschreven.

### <a name="change-the-location-of-log-files"></a>De locatie van logboekbestanden wijzigen

Gebruik een van deze opdrachten.

| Besturingssysteem | Opdracht  |
|--------|-----------|
| **Windows** | `set AZCOPY_LOG_LOCATION=<value>` |
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **Macos** | `export AZCOPY_LOG_LOCATION=<value>` |

Gebruik `azcopy env` de om de huidige waarde van deze variabele te controleren. Als de waarde leeg is, worden logboeken naar de standaardlocatie geschreven.

## <a name="change-the-default-log-level"></a>Het standaardlogboekniveau wijzigen

Standaard is het azcopy-logboekniveau ingesteld op `INFO`. Als u de logverbositeit wilt verminderen om schijfruimte te besparen, overschrijft u deze instelling met behulp van de ``--log-level`` optie. 

Beschikbare logniveaus `NONE`zijn: `INFO` `WARNING`, `ERROR` `DEBUG` `PANIC`, `FATAL`, , en .

## <a name="remove-plan-and-log-files"></a>Plan- en logboekbestanden verwijderen

Als u alle plan- en logboekbestanden van uw lokale machine `azcopy jobs clean` wilt verwijderen om schijfruimte op te slaan, gebruikt u de opdracht.

Als u het plan wilt verwijderen en `azcopy jobs rm <job-id>`bestanden wilt registreren die aan slechts één taak zijn gekoppeld, gebruikt u . Vervang `<job-id>` de tijdelijke aanduiding in dit voorbeeld door de taak-id van de taak.


