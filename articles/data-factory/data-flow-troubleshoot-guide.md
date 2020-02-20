---
title: Problemen met gegevens stromen oplossen
description: Meer informatie over het oplossen van problemen met gegevens stromen in Azure Data Factory.
services: data-factory
ms.author: makromer
author: kromerm
manager: anandsub
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.openlocfilehash: e2e1ddd031041f49107545cd0b3d3de4eaebcd6d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472125"
---
# <a name="troubleshoot-data-flows-in-azure-data-factory"></a>Problemen met gegevens stromen in Azure Data Factory oplossen

In dit artikel worden algemene probleemoplossings methoden voor gegevens stromen in Azure Data Factory besproken.

## <a name="common-errors-and-messages"></a>Veelvoorkomende fouten en berichten

### <a name="error-code-df-executor-sourceinvalidpayload"></a>Fout code: DF-uitvoerder-SourceInvalidPayload
- **Bericht**: uitvoering van voor beeld, fout opsporing en pipeline-gegevens stroom is mislukt omdat de container niet bestaat
- **Oorzaken**: wanneer dataset een container bevat die niet voor komt in de opslag
- **Aanbeveling**: Controleer of de container waarnaar wordt verwezen in uw gegevensset bestaat of toegankelijk is.

### <a name="error-code-df-executor-systemimplicitcartesian"></a>Fout code: DF-uitvoerder-SystemImplicitCartesian

- **Bericht**: impliciet Cartesisch product voor Inner join wordt niet ondersteund. gebruik in plaats daarvan cross join. Kolommen die in de samen voeging worden gebruikt, moeten een unieke sleutel voor rijen maken.
- **Oorzaken**: impliciet Cartesisch product voor Inner join tussen logische abonnementen wordt niet ondersteund. Als de kolommen die worden gebruikt in de samen voeging de unieke sleutel maakt, is ten minste één kolom van beide zijden van de relatie vereist.
- **Aanbeveling**: voor niet-gelijkheid gebaseerde samen voegingen moet u kiezen voor aangepaste cross-koppeling.

### <a name="error-code-df-executor-systeminvalidjson"></a>Fout code: DF-uitvoerder-SystemInvalidJson

- **Bericht**: JSON-Parseerfout, niet-ondersteunde code ring of meerdere regels
- **Oorzaken**: mogelijke problemen met het JSON-bestand: niet-ondersteunde code ring, beschadigde bytes of het gebruik van JSON-bron als één document op veel geneste lijnen
- **Aanbeveling**: Controleer of de code ring van het JSON-bestand wordt ondersteund. Op de bron transformatie die gebruikmaakt van een JSON-gegevensset, vouwt u JSON-instellingen uit en schakelt u ' single document ' in.
 
### <a name="error-code-df-executor-broadcasttimeout"></a>Fout code: DF-uitvoerder-BroadcastTimeout

- **Bericht**: er is een time-out opgetreden voor de broadcast. Zorg ervoor dat de stroom van de uitzending gegevens binnen 60 seconden produceert in debug-uitvoeringen en 300 seconden bij uitvoering
- **Oorzaken**: Broadcast heeft een standaard time-out van 60 seconden in debug-uitvoeringen en 300 seconden in de uitvoering van taken. De stroom die is gekozen voor de broadcast lijkt groot te zijn voor het produceren van gegevens binnen deze limiet.
- **Aanbeveling**: Vermijd het uitzenden van grote gegevens stromen waarbij de verwerking meer dan 60 seconden kan duren. Kies in plaats daarvan een kleinere stroom om te broadcasten. Grote SQL/DW-tabellen en-bron bestanden zijn meestal onjuiste kandidaten.

### <a name="error-code-df-executor-conversion"></a>Fout code: DF-uitvoeringen-conversie

- **Bericht**: converteren naar een datum of tijd is mislukt vanwege een ongeldig teken
- **Oorzaken**: gegevens hebben niet de verwachte indeling
- **Aanbeveling**: gebruik het juiste gegevens type

### <a name="error-code-df-executor-invalidcolumn"></a>Fout code: DF-uitvoerder-InvalidColumn

- **Bericht**: de kolom naam moet worden opgegeven in de query, een alias instellen als u een SQL-functie gebruikt
- **Oorzaken**: er is geen kolom naam opgegeven
- **Aanbeveling**: Stel een alias in als u een SQL-functie gebruikt, zoals min ()/Max (), enzovoort.

## <a name="general-troubleshooting-guidance"></a>Algemene richt lijnen voor probleem oplossing

1. Controleer de status van uw gegevensset-verbindingen. Ga in elke bron-en Sink-trans formatie naar de gekoppelde service voor elke gegevensset die u gebruikt en test verbindingen.
1. Controleer de status van uw bestands-en tabel verbindingen van de ontwerp functie voor gegevens stromen. Schakel over op fout opsporing en klik op voor beeld van gegevens op de bron transformaties om ervoor te zorgen dat u toegang hebt tot uw gegevens.
1. Als alles er goed uitziet in de preview van gegevens, gaat u naar de ontwerp functie voor pijp lijnen en plaatst u uw gegevens stroom in een pijplijn activiteit. Fout opsporing voor de pijp lijn voor een end-to-end-test.

## <a name="next-steps"></a>Volgende stappen

Probeer deze bronnen voor meer informatie over probleem oplossing:
*  [Data Factory Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory functie aanvragen](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-Video's](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [MSDN-forum](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum voor Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-informatie over Data Factory](https://twitter.com/hashtag/DataFactory)
*  [Prestatie gids voor gegevens stromen van ADF-toewijzing](concepts-data-flow-performance.md)
