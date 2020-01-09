---
title: bestand opnemen
description: bestand opnemen
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75541801"
---
1. Meld u aan bij [Azure machine learning Studio](https://ml.azure.com).

1. Voer een upgrade uit voor uw werk ruimte naar Enter prise Edition.

    Na de upgrade worden alle Visual-Interface experimenten geconverteerd naar de concepten van pijp lijnen in de ontwerp functie.
    
    > [!NOTE]
    > U hoeft niet bij te werken naar de Enter prise-editie om de webservices van Visual Interface te converteren naar realtime-eind punten.
    
1. Ga naar de sectie ontwerp van de werk ruimte om uw lijst met pijp lijnen concepten weer te geven. 
    
    Geconverteerde webservices kunt u vinden door te navigeren naar **eind punten** > **realtime-eind punten**.

1. Selecteer een pijp lijn concept om deze te openen.

    Als er tijdens het conversie proces een fout is opgetreden, wordt een fout bericht weer gegeven met instructies om het probleem op te lossen. 

### <a name="known-issues"></a>Bekende problemen

 Hieronder vindt u bekende migratie problemen die hand matig moeten worden opgelost:

- **Gegevens importeren** of **gegevens modules exporteren**
        
    Als u in het experiment een module **gegevens importeren** of **gegevens exporteren** hebt, moet u de gegevens bron bijwerken om een Data Store te gebruiken. Zie [toegang tot gegevens in azure Storage-services](../articles/machine-learning/how-to-access-data.md)voor meer informatie over het maken van een gegevens opslag. De gegevens van uw Cloud-opslag account zijn toegevoegd aan de opmerkingen van de module **gegevens importeren** of **gegevens exporteren** voor uw gemak. 
      