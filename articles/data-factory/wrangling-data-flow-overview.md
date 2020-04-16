---
title: Getouwtrek gegevensstromen in Azure Data Factory
description: Een overzicht van de gegevensstromen in Azure Data Factory
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 71df574602518fdc252d9d86859ed772a17be443
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408987"
---
# <a name="what-are-wrangling-data-flows"></a>Wat zijn getouwtrek datastromen?

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Organisaties moeten datavoorbereiding en getouwtrek doen voor een nauwkeurige analyse van complexe gegevens die elke dag blijven groeien. Gegevensvoorbereiding is vereist, zodat organisaties de gegevens in verschillende bedrijfsprocessen kunnen gebruiken en de time-to-value kunnen verkorten.

Met de gegevensstromen in Azure Data Factory u codevrije gegevensvoorbereiding op cloudschaal iteratief doen. Getouwtrek gegevensstromen integreren met [Power Query Online](https://docs.microsoft.com/power-query/) en maken Power Query M-functies beschikbaar voor gebruikers van gegevensfabrieken.

Wrangling data flow vertaalt M gegenereerd door de Power Query Online Mashup Editor in spark code voor cloud scale uitvoering.

Getouwtrek datastromen zijn vooral nuttig voor data engineers of 'citizen data integrators'.

## <a name="use-cases"></a>Gebruiksvoorbeelden

### <a name="fast-interactive-data-exploration-and-preparation"></a>Snelle interactieve data-exploratie en -voorbereiding

Meerdere data engineers en citizen data integrators kunnen datasets interactief verkennen en voorbereiden op cloudschaal. Met de opkomst van volume, verscheidenheid en snelheid van gegevens in gegevensmeren, hebben gebruikers een effectieve manier nodig om gegevenssets te verkennen en voor te bereiden. Het kan bijvoorbeeld nodig zijn om een gegevensset te maken die 'sinds 2017 alle demografische gegevens van klanten voor nieuwe klanten heeft'. Je bent niet in kaart brengen aan een bekend doelwit. Je onderzoekt, wrangling, en prepping datasets om te voldoen aan een vereiste voordat het publiceren in het meer. Getouwtrek gegevensstromen worden vaak gebruikt voor minder formele analysescenario's. De voorbereide datasets kunnen worden gebruikt voor het uitvoeren van transformaties en machine learning-bewerkingen stroomafwaarts.

### <a name="code-free-agile-data-preparation"></a>Codevrije agile datavoorbereiding

Citizen data integrators besteden meer dan 60% van hun tijd aan het zoeken naar en voorbereiden van gegevens. Ze willen het op een codevrije manier doen om de operationele productiviteit te verbeteren. Door gegevensintegratoren van burgers in staat te stellen gegevens te verrijken, vorm te geven en te publiceren met behulp van bekende tools zoals Power Query Online op een schaalbare manier, verbetert het hun productiviteit drastisch. Met de gegevensstroom in Azure Data Factory kan de vertrouwde Mashup-editor van Power Query Online gegevensintegratoren snel fouten oplossen, gegevens standaardiseren en gegevens van hoge kwaliteit produceren om zakelijke beslissingen te ondersteunen.

### <a name="data-validation"></a>Gegevensvalidatie

Scan uw gegevens visueel op een codevrije manier om eventuele uitschieters, afwijkingen te verwijderen en deze te conformeren aan een vorm voor snelle analyses.

## <a name="supported-sources"></a>Ondersteunde bronnen

| Connector | Gegevensindeling | Verificatietype |
| -- | -- | --|
| [Azure Blob Storage](connector-azure-blob-storage.md) | CSV, Parket | Accountsleutel |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | CSV | Service-principal |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | CSV, Parket | Accountsleutel, serviceprincipal |
| [Azure SQL Database](connector-azure-sql-database.md) | - | SQL-verificatie |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md) | - | SQL-verificatie |

## <a name="the-mashup-editor"></a>De mashup editor

Wanneer u een gegevensstroom maakt, worden alle brongegevenssets gegevenssetquery's en worden ze in de **ADFResource-map** geplaatst. Standaard wijst de UserQuery naar de eerste gegevenssetquery. Alle transformaties moeten worden uitgevoerd op de UserQuery als wijzigingen in gegevenssetquery's worden niet ondersteund, noch zullen ze worden gehandhaafd. Het hernoemen, toevoegen en verwijderen van query's wordt momenteel niet ondersteund.

![Gehakketak](media/wrangling-data-flow/editor.png)

Momenteel worden niet alle Power Query M-functies ondersteund voor gegevensgetouwtrek, ondanks dat ze beschikbaar zijn tijdens het ontwerpen. Tijdens het bouwen van uw getouwtrekgegevensstromen wordt u gevraagd met het volgende foutbericht als een functie niet wordt ondersteund:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Zie voor meer informatie over ondersteunde transformaties [de functies van de gegevensstroom](wrangling-data-flow-functions.md).

Momenteel wrangling data flow ondersteunt alleen het schrijven naar een gootsteen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een getouwtrek gegevensstroom.](wrangling-data-flow-tutorial.md)