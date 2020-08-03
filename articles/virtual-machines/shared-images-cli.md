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
ms.custom: devx-track-azurecli
ms.openlocfilehash: ff50f61fe159c518d488da9f9911e40f1ddb8ca3
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87501454"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Een galerie met gedeelde installatie kopieën maken met Azure CLI

Met een [Shared Image Gallery](./linux/shared-image-galleries.md) kunt u het delen van aangepaste installatiekopieën in uw organisatie vereenvoudigen. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de Shared Image Gallery kunt u aangepaste VM-installatiekopieën met anderen delen. U kunt kiezen welke installatiekopieën u wilt delen, in welke regio’s u ze beschikbaar wilt maken en met wie u ze wilt delen. 

[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Volgende stappen

Een installatie kopie versie maken op basis van een [virtuele machine](image-version-vm-cli.md)of een [beheerde installatie kopie](image-version-managed-image-cli.md) met behulp van de Azure cli.

Zie het [overzicht](./linux/shared-image-galleries.md)voor meer informatie over gedeelde afbeeldings galerieën. Als u problemen ondervindt, raadpleegt u [problemen met de galerie met gedeelde afbeeldingen oplossen](troubleshooting-shared-images.md).
