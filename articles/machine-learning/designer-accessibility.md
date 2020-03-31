---
title: Toegankelijkheidsfuncties gebruiken in de ontwerper (voorbeeld)
titleSuffix: Azure Machine Learning
description: Meer informatie over de sneltoetsen en toegankelijkheidsfuncties voor schermlezers die beschikbaar zijn in de ontwerper.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: peterlu
author: peterclu
ms.date: 01/09/2020
ms.openlocfilehash: 59199291589a81d0a0d96b7867078b8196be086f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77366193"
---
# <a name="use-a-keyboard-to-use-azure-machine-learning-designer-preview"></a>Een toetsenbord gebruiken om Azure Machine Learning-ontwerper te gebruiken (voorbeeld)

Meer informatie over het gebruik van een toetsenbord en schermlezer om Azure Machine Learning designer te gebruiken. Zie [Sneltoetsen in de Azure-portal](../azure-portal/azure-portal-keyboard-shortcuts.md) voor een lijst met sneltoetsen die overal in de Azure-portal werken.

Deze werkstroom is getest met [Narrator](https://support.microsoft.com/help/22798/windows-10-complete-guide-to-narrator) en [JAWS,](https://www.freedomscientific.com/products/software/jaws/)maar moet werken met andere standaard schermlezers.

## <a name="navigate-the-pipeline-graph"></a>Navigeren in de pijplijngrafiek

De pijplijngrafiek is georganiseerd als een geneste lijst. De buitenste lijst is een modulelijst, die alle modules in de pijplijngrafiek beschrijft. De binnenlijst is een verbindingslijst, die alle verbindingen van een specifieke module beschrijft.  

1. Gebruik in de modulelijst de pijltoets om van module te wisselen.
1. Het tabblad gebruiken om de verbindingslijst voor de doelmodule te openen.
1. Gebruik de pijltoets om te schakelen tussen de verbindingspoorten voor de module.
1. Gebruik "G" om naar de doelmodule te gaan.

## <a name="edit-the-pipeline-graph"></a>De pijplijngrafiek bewerken

### <a name="add-a-module-to-the-graph"></a>Een module toevoegen aan de grafiek

1. Gebruik Ctrl+F6 om de focus over te schakelen van het canvas naar de modulestructuur.
1. Zoek de gewenste module in de modulestructuur met behulp van standaard treeview-bediening.

### <a name="edit-a-module"></a>Een module bewerken

Ga als lid van de module over naar een andere module:

1. Gebruik Ctrl + Shift + H wanneer u een module in de modulelijst target om de verbindingshelper te openen.
1. Bewerk de verbindingspoorten voor de module.

Ga als u de module-eigenschappen aanpassen:

1. Gebruik Ctrl + Shift + E wanneer u een module target om de module-eigenschappen te openen.
1. De module-eigenschappen bewerken.

## <a name="navigation-shortcuts"></a>Navigatiesneltoetsen

| Toetsaanslag | Beschrijving |
|-|-|
| Ctrl + F6 | Focus schakelen tussen canvas en modulestructuur |
| Ctrl+F1   | Open de informatiekaart wanneer u zich richt op een knooppunt in modulestructuur |
| Ctrl+Shift+H | Open de verbindingshelper wanneer de focus op een knooppunt ligt |
| Ctrl+Shift+E | Module-eigenschappen openen wanneer de focus op een knooppunt ligt |
| Ctrl+G | Focus verplaatsen naar het eerste mislukte knooppunt als de pijplijnrun is mislukt |

## <a name="action-shortcuts"></a>Snelkoppelingen voor actie

Gebruik de volgende sneltoetsen met de toegangssleutel. Zie https://en.wikipedia.org/wiki/Access_keyvoor meer informatie over toegangssleutels .

| Toetsaanslag | Actie |
|-|-|
| Toegangssleutel + R | Voer |
| Toegangssleutel + P | Publiceren |
| Toegangstoets + C | Klonen |
| Toegangssleutel + D | Implementeren |
| Toegangssleutel + I | Inferencepijplijn maken/bijwerken |
| Toegangssleutel + B | Batch-gevolgtrekkingspijplijn maken/bijwerken |
| Toegangstoets + K | Vervolgkeuzelijst 'Inference pipeline maken' openen |
| Toegangssleutel + U | Vervolgkeuzelijst 'Inference pipeline bijwerken' openen |
| Toegangssleutel + M | Meer(...) dropdown openen |

## <a name="next-steps"></a>Volgende stappen

- [Hoog contrast inschakelen of het thema wijzigen](../azure-portal/azure-portal-change-theme-high-contrast.md)
- [Toegankelijkheidsgerelateerde tools bij Microsoft](https://www.microsoft.com/accessibility)
