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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75541801"
---
1. Meld u aan bij [Azure Machine Learning studio](https://ml.azure.com).

1. Upgrade uw werkruimte naar Enterprise-editie.

    Na het upgraden worden al uw experimenten met visuele interface omgezet in pijplijnconcepten in de ontwerper.
    
    > [!NOTE]
    > U hoeft niet te upgraden naar de Enterprise-editie om webservices voor visuele interface om te zetten in realtime eindpunten.
    
1. Ga naar het ontwerpgedeelte van de werkruimte om de lijst met pijplijnconcepten weer te geven. 
    
    Geconverteerde webservices kunnen worden gevonden door te navigeren naar **eindpunten** > **Real-time eindpunten**.

1. Selecteer een pijplijnconcept om het te openen.

    Als er een fout is opgetreden tijdens het conversieproces, verschijnt er een foutbericht met instructies om het probleem op te lossen. 

### <a name="known-issues"></a>Bekende problemen

 Hieronder staan bekende migratieproblemen die handmatig moeten worden aangepakt:

- **Modules gegevens importeren** **of gegevens exporteren**
        
    Als u een module **Gegevens importeren** of **Gegevens exporteren** in het experiment hebt, moet u de gegevensbron bijwerken om een gegevensopslag te gebruiken. Zie Hoe u toegang krijgt [tot gegevens in Azure-opslagservices](../articles/machine-learning/how-to-access-data.md)voor meer informatie over het maken van een gegevensarchief. Uw cloudopslagaccountgegevens zijn toegevoegd in de opmerkingen van de module **Gegevens importeren** of **Gegevens exporteren** voor uw gemak. 
      