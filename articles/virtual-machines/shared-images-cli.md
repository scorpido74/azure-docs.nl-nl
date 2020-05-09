---
title: Galerieën met gedeelde installatie kopieën maken met Azure CLI
description: In dit artikel leert u hoe u de Azure CLI gebruikt om een gedeelde installatie kopie te maken van een virtuele machine in Azure.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: d64b5f94dae7ad0213fa231b5603064ad3647da1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82796692"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Een galerie met gedeelde installatie kopieën maken met Azure CLI

Een [Galerie met gedeelde afbeeldingen](./linux/shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de galerie gedeelde afbeeldingen kunt u uw aangepaste VM-installatie kopieën delen met anderen. Kies welke installatie kopieën u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u wilt delen. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Volgende stappen

Een installatie kopie versie maken op basis van een [virtuele machine](image-version-vm-cli.md)of een [beheerde installatie kopie](image-version-managed-image-cli.md) met behulp van de Azure cli.

Zie het [overzicht](./linux/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](troubleshooting-shared-images.md).
