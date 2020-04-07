---
title: Modelconversie
description: Beschrijft het proces van het converteren van een model voor rendering
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 135c58a665779ffaad8750ffe618bdbe38639b66
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681491"
---
# <a name="convert-models"></a>Modellen converteren

Azure Remote Rendering stelt u in staat om zeer complexe modellen weer te geven. Om maximale prestaties te bereiken, moeten de gegevens worden voorverwerkt om in een optimaal formaat te zijn. Afhankelijk van de hoeveelheid gegevens kan deze stap even duren. Het zou onpraktisch zijn, als deze tijd werd besteed tijdens het laden van het model. Ook zou het verspillend zijn om dit proces te herhalen voor meerdere sessies. Om deze redenen biedt ARR-service een speciale *conversieservice,* die u van tevoren uitvoeren.
Eenmaal geconverteerd kan een model worden geladen vanuit een Azure Storage-account.

## <a name="supported-source-formats"></a>Ondersteunde bronindelingen

De conversieservice ondersteunt deze indelingen:

- **FBX** (versie 2011 en hoger)
- **GLTF** (versie 2.x)
- **GLB** (versie 2.x)

Er zijn kleine verschillen tussen de indelingen met betrekking tot materiaaleigenschapconversie, zoals vermeld in [hoofdstukmateriaaltoewijzing voor modelnotaties](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Het conversieproces

1. [Twee Azure Blob Storage-containers voorbereiden:](blob-storage.md)één voor invoer, één voor uitvoer
1. Uw model uploaden naar de invoercontainer (optioneel onder een subpad)
1. Het conversieproces activeren via [de API voor de rest van de modelconversie](conversion-rest-api.md)
1. Peil de service voor conversievoortgang
1. Zodra u klaar bent, laadt u een model
    - van een gekoppeld opslagaccount (zie de stappen 'Opslagaccounts koppelen' op [Een account maken](../create-an-account.md#link-storage-accounts) om uw opslagaccount te koppelen)
    - of door een *Shared Access Signature (SAS) te*verstrekken.

Alle modelgegevens (invoer en uitvoer) worden opgeslagen in de door de gebruiker verstrekte Azure blob-opslag. Azure Remote Rendering geeft u volledige controle over uw asset management.

## <a name="conversion-parameters"></a>Conversieparameters

Zie [dit hoofdstuk](configure-model-conversion.md)voor de verschillende conversieopties.

## <a name="examples"></a>Voorbeelden

- [Snelstart: Een model converteren voor rendering](../../quickstarts/convert-model.md) is een stapsgewijze introductie om een model om te zetten.
- [Voorbeeld PowerShell-scripts](../../samples/powershell-example-scripts.md), die het gebruik van de conversieservice aantonen, zijn te vinden in de [ARR-samplesrepository](https://github.com/Azure/azure-remote-rendering) in de map *Scripts.*

## <a name="next-steps"></a>Volgende stappen

- [Azure Blob Storage gebruiken voor modelconversie](blob-storage.md)
- [De API voor de modelconversie REST](conversion-rest-api.md)
- [De modelconversie configureren](configure-model-conversion.md)
- [Materiaaltoewijzing voor modelindelingen](../../reference/material-mapping.md)
