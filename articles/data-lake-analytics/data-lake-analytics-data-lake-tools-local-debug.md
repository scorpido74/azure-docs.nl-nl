---
title: Azure Data Lake Analytics-code lokaal debuggen
description: Meer informatie over het gebruik van Azure Data Lake Tools voor Visual Studio om U-SQL-taken op uw lokale werkstation te debuggen.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 0827311218202de447e5cf27356e00c4da020e94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61472988"
---
# <a name="debug-azure-data-lake-analytics-code-locally"></a>Azure Data Lake Analytics-code lokaal debuggen

U Azure Data Lake Tools voor Visual Studio gebruiken om Azure Data Lake Analytics-code uit te voeren en te debuggen op uw lokale werkstation, net zoals u dat in de Azure Data Lake Analytics-service.

Meer informatie over het [uitvoeren van U-SQL-script op uw lokale machine.](data-lake-analytics-data-lake-tools-local-run.md)

## <a name="debug-scripts-and-c-assemblies-locally"></a>Lokaal fouten opsporen in scripts en C#-assembly's

U C#-samenstellingen debuggen zonder ze in te dienen en te registreren bij de Azure Data Lake Analytics-service. U breekpunten instellen in zowel het code-behind-bestand als in een C#-project waarnaar wordt verwezen.

### <a name="debug-local-code-in-a-code-behind-file"></a>Lokale code debuggen in een code-achterbestand

1. Breekpunten instellen in het code-achterbestand.
2. Selecteer **F5** om het script lokaal te debuggen.

> [!NOTE]
   > De volgende procedure werkt alleen in Visual Studio 2015. In oudere Visual Studio-versies moet u mogelijk handmatig de **PDB-bestanden** toevoegen.  
   >
   >

### <a name="debug-local-code-in-a-referenced-c-project"></a>Lokale code debuggen in een C#-project waarnaar wordt verwezen

1. Maak een C#-assemblageproject en bouw het om het **uitvoer-DLL-bestand** te genereren.
2. Registreer het **DLL-bestand** met een U-SQL-instructie:

        CREATE ASSEMBLY assemblyname FROM @"..\..\path\to\output\.dll";
        
3. Stel onderbrekingspunten in in de C#-code.
4. Selecteer **F5** om het script te debuggen door het C# **DLL-bestand** lokaal te verwijzen.


## <a name="next-steps"></a>Volgende stappen

- Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)voor een voorbeeld van een complexere query.
- Zie [Taakbrowser en taakweergave gebruiken voor Azure Data Lake Analytics-taken](data-lake-analytics-data-lake-tools-view-jobs.md)als u taakgegevens wilt weergeven.
- Zie [De uitvoeringsweergave Vertex gebruiken in Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)als u de weergave vertex-uitvoering wilt gebruiken.
