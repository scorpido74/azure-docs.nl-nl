---
title: Gegevensstromen oplossen
description: Meer informatie over het oplossen van problemen met de gegevensstroom in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472125"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Problemen met gegevensstromen in Azure Data Factory oplossen

In dit artikel worden veelvoorkomende probleemoplossingsmethoden voor gegevensstromen in Azure Data Factory onderzocht.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Foutcode: DF-Executor-SourceInvalidPayload
- **Bericht:** Uitvoering gegevensvoorbeeld, foutopsporing en pijplijngegevensstroom mislukt omdat container niet bestaat
- **Oorzaken**: Wanneer de gegevensset een container bevat die niet bestaat in de opslag
- **Aanbeveling:** Zorg ervoor dat de container waarnaar in uw gegevensset wordt verwezen, bestaat of toegankelijk is.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Foutcode: DF-Executor-SystemImplicitCartesian

- **Bericht:** Impliciet cartesiaans product voor INNER join wordt niet ondersteund, gebruik IN plaats daarvan CROSS JOIN. Kolommen die worden gebruikt bij join moeten een unieke sleutel voor rijen maken.
- **Oorzaken**: Impliciet cartesiaans product voor INNER join tussen logische plannen wordt niet ondersteund. Als de kolommen die in de join worden gebruikt, de unieke sleutel maken, is ten minste één kolom van beide zijden van de relatie vereist.
- **Aanbeveling:** Voor joins op basis van niet-gelijkheid moet je kiezen voor CUSTOM CROSS JOIN.

### <a name="error-code-df-executor-systeminvalidjson"></a>Foutcode: DF-Executor-SystemInvalidJson

- **Bericht:** JSON parsing error, unsupported encoding or multiline
- **Oorzaken:** mogelijke problemen met het JSON-bestand: niet-ondersteunde codering, corrupte bytes of het gebruik van JSON-bron als enkel document op veel geneste regels
- **Aanbeveling:** Controleer of de codering van het JSON-bestand wordt ondersteund. Vouw 'JSON-instellingen' uit en schakel 'Single Document' in op de brontransformatie die een JSON-gegevensset gebruikt.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Foutcode: DF-Executor-BroadcastTimeout

- **Bericht:** Broadcast join time-out error, zorg ervoor dat broadcast stream produceert gegevens binnen 60 seconden in debug runs en 300 seconden in taak loopt
- **Oorzaken**: Broadcast heeft een standaard time-out van 60 seconden in foutopsporingsuitvoeringen en 300 seconden in taakuitvoeringen. Stream gekozen voor uitzending lijkt te groot om gegevens te produceren binnen deze limiet.
- **Aanbeveling**: Vermijd het uitzenden van grote gegevensstromen waar de verwerking meer dan 60 seconden kan duren. Kies een kleinere stream om uit te zenden. Grote SQL/DW-tabellen en bronbestanden zijn doorgaans slechte kandidaten.

### <a name="error-code-df-executor-conversion"></a>Foutcode: DF-Executor-Conversion

- **Bericht:** Converteren naar een datum of tijd is mislukt vanwege een ongeldig teken
- **Oorzaken**: Gegevens zijn niet in de verwachte indeling
- **Aanbeveling**: Het juiste gegevenstype gebruiken

### <a name="error-code-df-executor-invalidcolumn"></a>Foutcode: DF-Executor-InvalidEKolom

- **Bericht:** Kolomnaam moet worden opgegeven in de query, een alias instellen als u een SQL-functie gebruikt
- **Oorzaken**: Er is geen kolomnaam opgegeven
- **Aanbeveling**: Stel een alias in als u een SQL-functie gebruikt, zoals min()/max(), enz.

## <a name="general-troubleshooting-guidance"></a>Algemene richtlijnen voor probleemoplossing

1. Controleer de status van uw gegevenssetverbindingen. Ga in elke bron- en sinktransformatie naar de Gekoppelde service voor elke gegevensset die u gebruikt en test verbindingen.
1. Controleer de status van uw bestand en tabelverbindingen van de gegevensstroomontwerper. Schakel Foutopsporing in en klik op Gegevensvoorbeeld voor uw brontransformaties om ervoor te zorgen dat u toegang hebt tot uw gegevens.
1. Als alles er goed uitziet vanuit het voorbeeld van gegevens, gaat u naar de pijplijnontwerper en plaatst u uw gegevensstroom in een pijplijnactiviteit. Debuging van de pijplijn voor een end-to-end test.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer hulp bij het oplossen van problemen:
*  [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Functieaanvragen gegevensfabriek](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatiehandleiding voor ADF-toewijzingsgegevens](concepts-data-flow-performance.md)
