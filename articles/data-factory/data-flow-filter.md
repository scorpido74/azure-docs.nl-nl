---
title: Filter transformatie van gegevens stroom Azure Data Factory koppelen
description: Filter transformatie van gegevens stroom Azure Data Factory koppelen
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd29753ff14d16081a46eebbc2ea02d94e5985f1
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029357"
---
# <a name="azure-data-factory-filter-transformation"></a>Filter transformatie van Azure data factory



De filter transformaties bieden het filteren van rijen. Bouw een expressie die de filter voorwaarde definieert. Klik in het tekstvak om de opbouw functie voor expressies te starten. In de opbouw functie voor expressies bouwt u een filter-expressie op om te bepalen welke rijen van de huidige gegevens stroom moeten worden door gegeven (filter) naar de volgende trans formatie. Denk na over de filter transformatie als de component WHERE van een SQL-instructie.

## <a name="filter-on-loan_status-column"></a>Filteren op kolom loan_status:

```
in([‘Default’, ‘Charged Off’, ‘Fully Paid’], loan_status).
```

Filter op de kolom Year in de demo movies:

```
year > 1980
```

## <a name="next-steps"></a>Volgende stappen

Probeer een filter transformatie voor kolommen, de [optie trans formatie selecteren](data-flow-select.md)
