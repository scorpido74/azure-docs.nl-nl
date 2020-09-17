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
ms.openlocfilehash: 8901f54c4802766eb3f12830d98d29fb47337ae5
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705211"
---
In de preview-versie heeft NFS de volgende beperkingen:

- NFS 4,1 ondersteunt momenteel alleen de verplichte functies uit de [protocol specificatie](https://tools.ietf.org/html/rfc5661). Optionele functies zoals delegaties en retour aanroepen van allerlei soorten, het vergren delen van upgrades en downgrades en Kerberos-verificatie en-versleuteling worden niet ondersteund.
- Als het meren deel van uw aanvragen meta gegevens georiënteerd is, is de latentie verergert in vergelijking met lees-en schrijf-en bijwerk bewerkingen.
- U moet een nieuw opslag account maken om een NFS-share te kunnen maken.
- Alleen de REST Api's van het beheer vlak worden ondersteund. REST-Api's voor het gegevens vlak zijn niet beschikbaar. Dit betekent dat hulpprogram ma's zoals Storage Explorer niet werken met NFS-shares en dat u niet kunt bladeren door de NFS-share gegevens in de Azure Portal.
- Alleen beschikbaar voor de Premium-laag.
- Momenteel alleen beschikbaar met lokaal redundante opslag.

### <a name="azure-storage-features-not-yet-supported"></a>Azure Storage functies die nog niet worden ondersteund

Daarnaast zijn de volgende Azure Files-functies niet beschikbaar met NFS-shares:

- Verificatie op basis van identiteit
- Ondersteuning voor Azure Backup
- Momentopnamen
- Voorlopig verwijderen
- Volledige versleuteling-in-transit ondersteuning (voor meer informatie raadpleegt u [NFS-beveiliging](../articles/storage/files/storage-files-compare-protocols.md#security))
- Azure File Sync (alleen beschikbaar voor Windows-clients, die niet door NFS 4,1 worden ondersteund)