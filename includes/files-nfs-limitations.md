---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 09/15/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 10177dd949ac531027e13cf633b11c16674fd4ab
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94386549"
---
In de preview-versie heeft NFS de volgende beperkingen:

- NFS 4,1 ondersteunt momenteel alleen de verplichte functies uit de [protocol specificatie](https://tools.ietf.org/html/rfc5661). Optionele functies zoals delegaties en retour aanroepen van allerlei soorten, het vergren delen van upgrades en downgrades en Kerberos-verificatie en-versleuteling worden niet ondersteund.
- Als het meren deel van uw aanvragen meta gegevens georiënteerd is, is de latentie verergert in vergelijking met lees-en schrijf-en bijwerk bewerkingen.
- U moet een nieuw opslag account maken om een NFS-share te kunnen maken.
- Alleen de REST Api's van het beheer vlak worden ondersteund. REST-Api's voor het gegevens vlak zijn niet beschikbaar. Dit betekent dat hulpprogram ma's zoals Storage Explorer niet werken met NFS-shares en dat u niet kunt bladeren door NFS-share gegevens in de Azure Portal.
- Alleen beschikbaar voor de Premium-laag.
- Momenteel alleen beschikbaar met lokaal redundante opslag (LRS).

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage functies die nog niet worden ondersteund

Daarnaast zijn de volgende Azure Files-functies niet beschikbaar met NFS-shares:

- Verificatie op basis van identiteit
- Ondersteuning voor Azure Backup
- Momentopnamen
- Voorlopig verwijderen
- Volledige versleuteling-in-transit ondersteuning (voor meer informatie raadpleegt u [NFS-beveiliging](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (alleen beschikbaar voor Windows-clients, die niet door NFS 4,1 worden ondersteund)
