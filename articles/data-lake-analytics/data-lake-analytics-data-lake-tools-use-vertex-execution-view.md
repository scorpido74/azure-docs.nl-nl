---
title: Vertex Execution View in Data Lake Tools voor Visual Studio
description: In dit artikel wordt beschreven hoe u de Vertex-uitvoeringsweergave gebruiken om Gegevens Lake Analytics-taken te bekijken.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.topic: conceptual
ms.date: 10/13/2016
ms.openlocfilehash: f5adbb75e6852551976aa040a1a1c723d2e3f59b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309714"
---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>Gebruik de Vertex Execution View in Data Lake Tools voor Visual Studio
Meer informatie over het gebruik van de Vertex-uitvoeringsweergave om Data Lake Analytics-taken te bekijken.


## <a name="open-the-vertex-execution-view"></a>De uitvoeringsweergave van Vertex openen
Open een U-SQL-taak in Data Lake Tools voor Visual Studio. Klik op **De uitvoeringsweergave van Vertex** in de linkerbenedenhoek. Mogelijk wordt u gevraagd om eerst profielen te laden en het kan enige tijd duren, afhankelijk van uw netwerkverbinding.

![Weergave van de uitvoering van Data Lake Analytics-tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>Vertex-uitvoeringsweergave begrijpen
De Vertex Execution View bestaat uit drie delen:

![Weergave van de uitvoering van Data Lake Analytics-tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

Met de **Vertex-kiezer** aan de linkerkant u vertices selecteren op basis van functies (zoals top 10 gelezen gegevens of per fase kiezen). Een van de meest gebruikte filters is om de **hoekjes op kritieke paden**te zien. Het **kritieke pad** is de langste keten van vertices van een U-SQL-taak. Inzicht in het kritieke pad is handig voor het optimaliseren van uw taken door te controleren welke hoekpunt het langst duurt.
  
![Weergave van de uitvoering van Data Lake Analytics-tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)

Het bovenste middelste deelvenster toont de **lopende status van alle hoekpunten.**
  
![Weergave van de uitvoering van Data Lake Analytics-tools](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)

In het onderste middelste deelvenster vindt u informatie over elk hoekpunt:
* Procesnaam: de naam van de hoekpuntinstantie. Het bestaat uit verschillende delen in StageName| VertexName| VertexRunInstance. Zo staat de SV7_Split[62].v1 vertex voor de tweede lopende instantie (.v1, index vanaf 0) van Vertex nummer 62 in Stage SV7_Split.
* Total Data Read/Written: De gegevens zijn gelezen/geschreven door dit vertex.
* Status van status/uitgang: de uiteindelijke status wanneer het hoekpunt is beëindigd.
* Code/fouttype afsluiten: de fout wanneer het hoekpunt is mislukt.
* Scheppingreden: waarom het hoekpunt is gemaakt.
* Resourcelatentie/Proceslatentie/PN-wachtrijlatentie: de tijd die nodig is om te wachten op resources, om gegevens te verwerken en in de wachtrij te blijven.
* Guid proces/maker: GUID voor het huidige draaiende hoekpunt of de maker ervan.
* Versie: het N-th-exemplaar van het draaiende hoekpunt (het systeem kan nieuwe exemplaren van een hoekpunt plannen om vele redenen, bijvoorbeeld failover, compute redundantie, enz.)
* Versie Gemaakt Tijd.
* Proces Begintijd/proceswachtrijtijd/procesbegintijd/procesvolledige tijd: wanneer het vertexproces wordt gestart met het maken; wanneer het hoekpuntproces in de wachtrij begint te staan; wanneer het bepaalde hoekpuntproces begint; wanneer het bepaalde hoekpunt is voltooid.

## <a name="next-steps"></a>Volgende stappen
* Zie [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md) (Diagnostische logboeken openen voor Azure Data Lake Analytics) voor logboekregistratie van diagnostische informatie.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md) voor een complexere query.
* Zie [Taakbrowser en taakweergave gebruiken voor Azure Data lake Analytics-taken](data-lake-analytics-data-lake-tools-view-jobs.md) als u taakgegevens wilt weergeven.
