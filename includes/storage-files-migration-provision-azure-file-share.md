---
title: Overwegingen voor het inrichten van Azure-bestandsshares.
description: Azure-bestandsshares inrichten voor gebruik met Azure File Sync. Een gemeenschappelijk tekstblok, gedeeld tussen migratiedocumenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78209426"
---
Een Azure-bestandsshare wordt opgeslagen in de cloud in een Azure-opslagaccount.
Er is een ander niveau van prestatieoverwegingen hier.

Als u zeer actieve aandelen hebt - aandelen die door veel gebruikers en/of toepassingen worden gebruikt, kunnen twee Azure-bestandsshares de prestatielimiet van een opslagaccount bereiken.

Het beste is om opslagaccounts te implementeren met elk één bestandsshare.
U meerdere Azure-bestandsshares bundelen in hetzelfde opslagaccount, voor het geval u archiveringsshares hebt of u verwacht dat er weinig dagelijkse activiteiten in zitten.

Deze overwegingen zijn meer van toepassing op directe cloudtoegang (via een Azure VM) dan op Azure File Sync. Als u alleen Azure File Sync voor deze shares wilt gebruiken, is het prima om meerdere te groeperen in één Azure-opslagaccount.

Als u een lijst van uw aandelen hebt gemaakt, moet u elk aandeel toewijzen aan het opslagaccount waarin ze zich bevinden.

In de vorige fase hebt u het juiste aantal aandelen bepaald. In deze stap hebt u een toewijzing van opslagaccounts gemaakt om aandelen in te dienen. Implementeer het nu juiste aantal Azure-opslagaccounts met het juiste aantal Azure-bestandsshares erin.

Zorg ervoor dat de regio van elk van uw opslagaccounts hetzelfde is en overeenkomt met het gebied van de storage sync service-bron die u al hebt geïmplementeerd.

> [!CAUTION]
> Als u een Azure-bestandsshare van 100 TiB-limiet maakt, kan dat aandeel alleen lokaal redundante opslag- of zoneredundantieredundantieopties gebruiken. Houd rekening met uw redundantiebehoeften voor opslag voordat u 100 TiB-bestandsshares gebruikt.

Azure-bestandsshares worden standaard nog steeds gemaakt met een 5 TiB-limiet. Aangezien u nieuwe opslagaccounts maakt, moet u de richtlijnen volgen [om opslagaccounts te maken waarmee Azure-bestandsshares met 100 TiB-limieten kunnen worden toegestaan.](../articles/storage/files/storage-files-how-to-create-large-file-share.md)

Een andere overweging bij het implementeren van een opslagaccount is de redundantie van uw Azure-opslag. Zie: [Redundantieopties voor Azure Storage](../articles/storage/common/storage-redundancy.md).

De namen van uw bronnen zijn ook belangrijk. Als u bijvoorbeeld meerdere aandelen voor de HR-afdeling groepeert in een Azure-opslagaccount, moet u het opslagaccount op de juiste naam geven. Als u uw Azure-bestandsshares een naam geeft, moet u ook namen gebruiken die vergelijkbaar zijn met die welke worden gebruikt voor hun on-premises tegenhangers.