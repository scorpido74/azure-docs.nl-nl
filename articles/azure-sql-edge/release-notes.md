---
title: Release opmerkingen voor Azure SQL Edge
description: Release opmerkingen bevatten informatie over wat er nieuw is of wat er is gewijzigd in de installatie kopieën van Azure SQL Edge
keywords: release opmerkingen SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 06/05/2020
ms.openlocfilehash: 7238e2d7cbcb26a7b45ae9d7db52a0826abe0754
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84897980"
---
# <a name="azure-sql-edge-release-notes"></a>Release opmerkingen bij Azure SQL Edge 

In dit artikel wordt beschreven wat er nieuw is en wat er is gewijzigd bij elke nieuwe build van Azure SQL Edge.

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>Buildnummer van SQL-engine-15.0.2000.1401
### <a name="whats-new"></a>Wat is nieuw?
1.  De product naam is bijgewerkt naar ' Azure SQL Edge '
2.  Functie Date_bucket

    i.  Ondersteuning voor datum, tijd, type DateTime
3.  Voors PELLEn met ONNX
    
    i.  RUNTIME parameter vereist voor ONNX 
    
4.  Ondersteuning voor TSQL-streaming (beperkte preview) 
 
### <a name="known-issues"></a>Bekende problemen

1. <b>Probleem:</b> Mogelijke fouten met het Toep assen van dacpac bij het opstarten vanwege een timing probleem.

    <b>Tijdelijke oplossing:</b> Als SQL Server of container opnieuw wordt gestart, wordt de dacpac opnieuw geprobeerd en wordt het probleem opgelost
### <a name="request-support"></a>Ondersteuning aanvragen
1. U kunt ondersteuning aanvragen op de [pagina ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Zorg ervoor dat de volgende velden zijn geselecteerd: 
    * Probleem type-technisch 
    * Service-IoT Edge
    * Probleem type: mijn probleem is gekoppeld aan een IoT Edge module
    * Subtype van probleem-Azure SQL Edge

   ![Voor beeld van een ondersteunings ticket](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Buildnummer van SQL-engine-15.0.2000.1331
### <a name="whats-new"></a>Wat is nieuw?
1. Functie Date_bucket
    
    i. Ondersteuning voor date time offset type
2. Voors PELLEn met ONNX-modellen

    i. ondersteuning voor nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Buildnummer van SQL-engine-15.0.2000.1247
### <a name="whats-new"></a>Wat is nieuw?
1.  Voors PELLEn met ONNX-modellen
 
    i.  Varchar-ondersteuning
    
    ii. Migratie naar ONNX runtime versie 1,0 
2.  Ondersteuning voor functies: de volgende functies zijn ingeschakeld:

    i.   CDC-ondersteuning

    ii.  Geschiedenis tabel met compressie

    iii. Hogere schaal factor voor het vooraf lezen van Logboeken

    4.  Batch modus ES filter pushdown

    v.   Vooruit-optimaliseringen lezen
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Buildnummer van SQL-engine-15.0.2000.1147
### <a name="whats-new"></a>Wat is nieuw?
1. Azure IOT-Portal implementeren 

    i.   Ondersteuning voor het implementeren van AMD64-en ARM-installatie kopieën

    ii.  Ondersteuning voor het maken van streaming-taken

    iii. Dacpac-implementatie
2. Voors PELLEn met ONNX-modellen

    i. Ondersteuning voor numeriek type
3. Ondersteuning voor functies: de volgende functies zijn ingeschakeld:

    i.  Pushdown aggregatie naar column Store-scan

    ii. Gelukkig-ronde-scans
4. Vermindering van footprint en geheugen gebruik
