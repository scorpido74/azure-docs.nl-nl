---
title: Problemen met latentie oplossen met behulp van logboeken voor Opslaganalyse
description: Problemen met latentie identificeren en oplossen met Azure Storage Analytic-logboeken en de clienttoepassing optimaliseren.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196510"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Problemen met latentie oplossen met behulp van logboeken voor Opslaganalyse

Diagnose en probleemoplossing is een belangrijke vaardigheid voor het bouwen en ondersteunen van clienttoepassingen met Azure Storage.

Vanwege het gedistribueerde karakter van een Azure-toepassing kunnen het diagnosticeren en oplossen van zowel fouten als prestatieproblemen complexer zijn dan in traditionele omgevingen.

De volgende stappen laten zien hoe u latentieproblemen identificeren en oplossen met Azure Storage Analytic-logboeken en de clienttoepassing optimaliseren.

## <a name="recommended-steps"></a>Aanbevolen stappen

1. Download de [logboeken storage analytics](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data).

2. Gebruik het volgende PowerShell-script om de logboeken met de raw-indeling om te zetten in tabelindeling:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. Het script start een GUI-venster waar u de informatie filteren op kolommen, zoals hieronder wordt weergegeven.

   ![Venster voor analytische logboeken](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Verklein de logboekvermeldingen op basis van 'operationeel type' en zoek naar de logboekvermelding die tijdens het tijdsbestek van het probleem is gemaakt.

   ![Logboekvermeldingen van het type bewerking](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Tijdens het moment dat het probleem zich heeft voorgedaan, zijn de volgende waarden belangrijk:

   * Operation-type = GetBlob
   * aanvraagstatus = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Server-Latency-In-Ms = 391

   End-to-End Latency wordt berekend met behulp van de volgende vergelijking:

   * End-to-End latentie = Server-Latency + ClientLatency

   Bereken de clientlatentie met de logboekvermelding:

   * Clientlatentie = End-to-End latentie – Serverlatentie

          * Example: 8453 – 391 = 8062ms

   In de volgende tabel vindt u informatie over de resultaten van OperationType en RequestStatus met hoge latentie:

   |   |Aanvraagstatus=<br>Geslaagd|Aanvraagstatus=<br>(SAS) Netwerkfout|Aanbeveling|
   |---|---|---|---|
   |GetBlob (GetBlob)|Ja|Nee|[**GetBlob-bewerking:** RequestStatus = Succes](#getblob-operation-requeststatus--success)|
   |GetBlob (GetBlob)|Nee|Ja|[**GetBlob-bewerking:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob PutBlob|Ja|Nee|[**Zet Operatie:** RequestStatus = Succes](#put-operation-requeststatus--success)|
   |PutBlob PutBlob|Nee|Ja|[**Zet Operatie:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Statusresultaten

### <a name="getblob-operation-requeststatus--success"></a>GetBlob-bewerking: requeststatus = succes

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie 'Aanbevolen stappen':

* End-to-End latentie
* Serverlatentie
* Clientlatentie

In een **GetBlob-bewerking** met **RequestStatus = Succes**, als **Max. Tijd** wordt besteed aan **client-latentie,** geeft dit aan dat Azure Storage een groot deel van de tijd besteedt aan het schrijven van gegevens aan de client. Deze vertraging duidt op een probleem aan de clientzijde.

**Aanbeveling:**

* Onderzoek de code in uw cliënt.
* Gebruik Wireshark, Microsoft Message Analyzer of Tcping om problemen met de netwerkconnectiviteit van de client te onderzoeken. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob-bewerking: requeststatus = (SAS)NetworkError

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie 'Aanbevolen stappen':

* End-to-End latentie
* Serverlatentie
* Clientlatentie

In een **GetBlob-bewerking** met **RequestStatus = (SAS)NetworkError**, als **Max Time** wordt besteed aan **client-latentie,** is het meest voorkomende probleem dat de client de verbinding verbreekt voordat een time-out verloopt in de opslagservice.

**Aanbeveling:**

* Onderzoek de code in uw client om te begrijpen waarom en wanneer de client de verbinding met de opslagservice verbreekt.
* Gebruik Wireshark, Microsoft Message Analyzer of Tcping om problemen met de netwerkconnectiviteit van de client te onderzoeken. 

### <a name="put-operation-requeststatus--success"></a>Put Operation: RequestStatus = Succes

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie 'Aanbevolen stappen':

* End-to-End latentie
* Serverlatentie
* Clientlatentie

In een **putbewerking** met **RequestStatus = Succes** **Client-Latency**, als **Max.** Deze vertraging duidt op een probleem aan de clientzijde.

**Aanbeveling:**

* Onderzoek de code in uw cliënt.
* Gebruik Wireshark, Microsoft Message Analyzer of Tcping om problemen met de netwerkconnectiviteit van de client te onderzoeken. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put Operation: RequestStatus = (SAS)NetworkError

Controleer de volgende waarden zoals vermeld in stap 5 van de sectie 'Aanbevolen stappen':

* End-to-End latentie
* Serverlatentie
* Clientlatentie

In een **PutBlob-bewerking** met **RequestStatus = (SAS)NetworkError**, als **Max.** **Client-Latency**

**Aanbeveling:**

* Onderzoek de code in uw client om te begrijpen waarom en wanneer de client de verbinding met de opslagservice verbreekt.
* Gebruik Wireshark, Microsoft Message Analyzer of Tcping om problemen met de netwerkconnectiviteit van de client te onderzoeken.

