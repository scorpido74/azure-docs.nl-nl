---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39a013f5f4b587137366147ade77f0be1b353c4c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204485"
---
- Incrementele moment opnamen kunnen momenteel niet worden verplaatst tussen abonnementen.
- U kunt momenteel alleen SAS-Uri's van Maxi maal vijf moment opnamen van een bepaalde momentopname familie genereren op een bepaald moment.
- U kunt geen incrementele moment opname maken voor een bepaalde schijf buiten het abonnement van de schijf.
- Er kunnen Maxi maal zeven incrementele moment opnamen per schijf worden gemaakt om de vijf minuten.
- Er kunnen voor één schijf een totaal van 200 incrementele moment opnamen worden gemaakt.
- U kunt de wijzigingen niet ophalen tussen moment opnamen die zijn gemaakt vóór en na de wijziging in de grootte van de bovenliggende schijf over 4 TB grens. U moet de volledige kopie van de moment opname die is gemaakt na het formaat ervan, downloaden. Daarna kunt u de wijzigingen ophalen tussen de moment opnamen die zijn gemaakt na het formaat van 4 TB. 
