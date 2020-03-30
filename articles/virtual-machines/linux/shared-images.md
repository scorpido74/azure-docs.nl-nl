---
title: Gedeelde afbeeldingsgalerieën maken met de Azure CLI
description: In dit artikel leert u hoe u de Azure CLI gebruiken om een gedeelde afbeelding van een VM in Azure te maken.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: axayjo
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/06/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: ''
ms.openlocfilehash: de1afa2367afcb78e8ca68e518acc93e33f61c43
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74034968"
---
# <a name="create-a-shared-image-gallery-with-the-azure-cli"></a>Een gedeelde afbeeldingsgalerie maken met de Azure CLI

Een [gedeelde afbeeldingsgalerie](shared-image-galleries.md) vereenvoudigt het delen van aangepaste afbeeldingen in uw hele organisatie. Aangepaste installatiekopieën zijn soortgelijk aan Marketplace-installatiekopieën, maar u kunt deze zelf maken. Aangepaste installatiekopieën kunnen worden gebruikt voor het opstarten van configuraties, zoals het vooraf laden van toepassingen, toepassingsconfiguraties en andere besturingssysteemconfiguraties. 

Met de gedeelde afbeeldingsgalerie u uw aangepaste VM-afbeeldingen delen met anderen in uw organisatie, binnen of tussen regio's, binnen een AAD-tenant. Kies in welke afbeeldingen u wilt delen, in welke regio's u ze beschikbaar wilt maken en met wie u ze wilt delen. U meerdere galerieën maken, zodat u op logischmogelijke wijze gedeelde afbeeldingen groeperen. 

De galerie is een bron op het hoogste niveau die volledige role-based access control (RBAC) biedt. Afbeeldingen kunnen worden geversioneerd en u ervoor kiezen om elke afbeeldingsversie te repliceren naar een andere set Azure-regio's. De galerie werkt alleen met Beheerde afbeeldingen.

De functie Gedeelde afbeeldingsgalerie heeft meerdere resourcetypen. We zullen deze gebruiken of bouwen in dit artikel:

| Resource | Beschrijving|
|----------|------------|
| **Beheerde afbeelding** | Dit is een basisafbeelding die alleen kan worden gebruikt of kan worden gebruikt om een **afbeeldingsversie** in een afbeeldingsgalerie te maken. Beheerde afbeeldingen worden gemaakt op gealgemene VM's. Een beheerde afbeelding is een speciaal type VHD dat kan worden gebruikt om meerdere VM's te maken en kan nu worden gebruikt om versies van gedeelde afbeeldingen te maken. |
| **Afbeeldingsgalerie** | Net als de Azure Marketplace is een **afbeeldingsgalerie** een opslagplaats voor het beheren en delen van afbeeldingen, maar u bepaalt wie toegang heeft. |
| **Afbeeldingsdefinitie** | Afbeeldingen worden gedefinieerd in een galerie en bevatten informatie over de afbeelding en vereisten voor het intern gebruiken ervan. Dit omvat of de afbeelding Windows of Linux is, release notes en minimale en maximale geheugenvereisten. Het is een definitie van een type beeld. |
| **Versie van installatiekopie** | Een **afbeeldingsversie** is wat u gebruikt om een vm te maken wanneer u een galerie gebruikt. U meerdere versies van een afbeelding hebben als dat nodig is voor uw omgeving. Net als een beheerde afbeelding wordt de afbeeldingsversie gebruikt om nieuwe schijven voor de virtuele machine te maken wanneer u een **afbeeldingsversie** gebruikt om een afbeeldingsversie te maken. Afbeeldingsversies kunnen meerdere keren worden gebruikt. |



[!INCLUDE [virtual-machines-common-shared-images-cli](../../../includes/virtual-machines-common-shared-images-cli.md)]

## <a name="create-a-vm"></a>Een virtuele machine maken

Maak een VM uit de nieuwste afbeeldingsversie met behulp van [az vm create](/cli/azure/vm#az-vm-create).

```azurecli-interactive 
az vm create\
   --resource-group myGalleryRG \
   --name myVM \
   --image "/subscriptions/subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --generate-ssh-keys
```

U ook een specifieke versie gebruiken met `--image` de afbeeldingsversie-ID voor de parameter. Als u bijvoorbeeld afbeeldingsversie *1.0.0-type* wilt gebruiken: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"`.

[!INCLUDE [virtual-machines-common-gallery-list-cli](../../../includes/virtual-machines-common-gallery-list-cli.md)]

[!INCLUDE [virtual-machines-common-shared-images-update-delete-cli](../../../includes/virtual-machines-common-shared-images-update-delete-cli.md)]

## <a name="next-steps"></a>Volgende stappen
[Azure Image Builder (preview)](image-builder-overview.md) kan helpen bij het automatiseren van het maken van afbeeldingsversies, u het zelfs gebruiken om een nieuwe afbeeldingsversie bij te werken en [te maken vanuit een bestaande afbeeldingsversie.](image-builder-gallery-update-image-version.md) 

U ook bronnen voor shared image gallery maken met sjablonen. Er zijn verschillende Azure Quickstart-sjablonen beschikbaar: 

- [Een gedeelde afbeeldingsgalerie maken](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Een afbeeldingsdefinitie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Een afbeeldingsversie maken in een gedeelde afbeeldingsgalerie](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Een VM maken op basis van afbeeldingsversie](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

Zie het [overzicht](shared-image-galleries.md)voor meer informatie over gedeelde galerieën. Zie [Problemen oplossen met gedeelde afbeeldingsgalerieën](troubleshooting-shared-images.md)als u problemen ondervindt.
