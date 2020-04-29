---
title: Model conversie
description: Beschrijft het proces van het converteren van een model voor Rendering
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681491"
---
# <a name="convert-models"></a>Modellen converteren

Met Azure remote rendering kunt u zeer complexe modellen weer geven. Om maximale prestaties te kunnen garanderen, moeten de gegevens worden voorverwerkt om een optimale indeling te hebben. Afhankelijk van de hoeveelheid gegevens kan deze stap enige tijd duren. Het is niet praktisch als deze tijd is besteed tijdens het laden van het model. Het is ook verspilling om dit proces voor meerdere sessies te herhalen. Om die reden biedt ARR service een speciale *conversie service*, die u vooraf kunt uitvoeren.
Na de conversie kan een model worden geladen vanuit een Azure Storage-account.

## <a name="supported-source-formats"></a>Ondersteunde bron indelingen

De conversie service ondersteunt de volgende indelingen:

- **FBX** (versie 2011 en hoger)
- **GLTF** (versie 2. x)
- **GLB** (versie 2. x)

Er zijn kleine verschillen tussen de indelingen met betrekking tot de conversie van materiaal eigenschappen, zoals vermeld in hoofdstuk [toewijzing van modellen voor model indelingen](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Het conversie proces

1. [Bereid twee Azure Blob Storage-containers](blob-storage.md)voor: één voor invoer, één voor uitvoer
1. Uw model uploaden naar de invoer container (optioneel onder een subpad)
1. Activeer het conversie proces via [de model conversie rest API](conversion-rest-api.md)
1. De service voor de voortgang van de conversie controleren
1. Als u klaar bent, laadt u een model
    - van een gekoppeld opslag account (Zie de stappen voor het koppelen van opslag accounts in [een account maken](../create-an-account.md#link-storage-accounts) om uw opslag account te koppelen)
    - of door een *Shared Access Signature (SAS)* op te geven.

Alle model gegevens (invoer en uitvoer) worden opgeslagen in Azure Blob-opslag die door de gebruiker is opgegeven. Met Azure remote rendering hebt u volledige controle over het beheer van uw bedrijfs middelen.

## <a name="conversion-parameters"></a>Conversie parameters

Zie [dit hoofd stuk](configure-model-conversion.md)voor de verschillende conversie opties.

## <a name="examples"></a>Voorbeelden

- [Quick Start: een model converteren voor rendering](../../quickstarts/convert-model.md) is een stapsgewijze inleiding hoe een model kan worden geconverteerd.
- [Voor beelden van Power shell-scripts](../../samples/powershell-example-scripts.md), waarmee het gebruik van de conversie service wordt gedemonstreerd, vindt u in de [opslag plaats met ARR](https://github.com/Azure/azure-remote-rendering) -voor beelden in de map *scripts* .

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob Storage gebruiken voor modelconversie](blob-storage.md)
- [De model conversie REST API](conversion-rest-api.md)
- [De model conversie configureren](configure-model-conversion.md)
- [Materiaaltoewijzing voor modelindelingen](../../reference/material-mapping.md)
