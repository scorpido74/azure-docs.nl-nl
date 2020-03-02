---
title: Overwegingen voor het inrichten van Azure-bestands shares.
description: Een Azure-bestands share inrichten voor gebruik met Azure File Sync. Een gemeen schappelijk tekst blok, gedeeld tussen migratie documenten.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 8cb398d1b1ec14f52d9c5fa5c122dc2e4ba4376d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209426"
---
Een Azure-bestands share wordt opgeslagen in de cloud in een Azure-opslag account.
Er is een andere mate van prestatie overwegingen.

Als u beschikt over zeer actieve shares-shares die worden gebruikt door veel gebruikers en/of toepassingen, kunnen de prestatie limiet van een opslag account worden bereikt door twee Azure-bestands shares.

De aanbevolen procedure is om opslag accounts te implementeren met één bestands share.
U kunt meerdere Azure-bestands shares in hetzelfde opslag account poolen, voor het geval u archiverings shares hebt of u verwacht een goedkope activiteit.

Deze overwegingen zijn van toepassing op directe Cloud toegang (via een Azure VM) dan die van toepassing is op Azure File Sync. Als u van plan bent om alleen Azure File Sync op deze shares te gebruiken, kunt u in één Azure Storage-account verschillende groepen groeperen.

Als u een lijst met shares hebt gemaakt, wijst u elke share toe aan het opslag account waarin ze zich bevinden.

In de vorige fase hebt u het juiste aantal shares bepaald. In deze stap hebt u een toewijzing van opslag accounts gemaakt aan bestands shares. Implementeer het nu juiste aantal Azure Storage-accounts met het juiste aantal Azure-bestands shares.

Zorg ervoor dat de regio van elk van uw opslag accounts hetzelfde is en overeenkomt met de regio van de opslag synchronisatie service resource die u al hebt geïmplementeerd.

> [!CAUTION]
> Als u een 100 TiB limiet van Azure file share maakt, kan die share alleen lokale redundante opslag of zone redundantie opties voor opslag gebruiken. Houd rekening met de vereisten voor opslag redundantie voordat u 100 TiB-bestands shares gebruikt.

Azure-bestands shares worden nog steeds gemaakt met een limiet van 5 TiB. Omdat u nieuwe opslag accounts maakt, moet u de [richt lijnen volgen voor het maken van opslag accounts waarmee Azure-bestands shares met 100 TIB-limieten zijn toegestaan](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Een andere overweging bij het implementeren van een opslag account is de redundantie van uw Azure-opslag. Zie: [Azure Storage redundantie opties](../articles/storage/common/storage-redundancy.md).

De namen van uw resources zijn ook belang rijk. Als u bijvoorbeeld meerdere shares voor de HR-afdeling in een Azure-opslag account groepeert, moet u het opslag account op de juiste wijze benoemen. Wanneer u uw Azure-bestands shares een naam geeft, moet u ook namen gebruiken die vergelijkbaar zijn met die van hun on-premises equivalenten.