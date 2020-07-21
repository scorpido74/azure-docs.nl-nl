---
title: Een Flatcar-container-VHD voor Linux maken en uploaden voor gebruik in azure
description: Meer informatie over het maken en uploaden van een VHD die een Flatcar container Linux-besturings systeem bevat.
author: marga-kinvolk
ms.author: danis
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/16/2020
ms.reviewer: cynthn
ms.openlocfilehash: 2bdbe93d5d593d429c4f7073227684c1e361864d
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86531515"
---
# <a name="using-a-prebuilt-flatcar-image-for-azure"></a>Een vooraf gemaakte Flatcar-installatie kopie gebruiken voor Azure

U kunt vooraf ontwikkelde installatie kopieën van virtuele harde schijven van Azure van Flatcar-container Linux downloaden voor elk van de Flatcar ondersteunde kanalen:

- [vast](https://stable.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [bèta](https://beta.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [letter](https://alpha.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)
- [Edge](https://edge.release.flatcar-linux.net/amd64-usr/current/flatcar_production_azure_image.vhd.bz2)

Deze installatie kopie is al volledig ingesteld en geoptimaliseerd voor uitvoering op Azure. U hoeft deze alleen te decomprimeren.

## <a name="building-your-own-flatcar-based-virtual-machine-for-azure"></a>Uw eigen virtuele machine op basis van Flatcar maken voor Azure

U kunt er ook voor kiezen om uw eigen Flatcar-container installatie kopie voor Linux te bouwen.

Volg op een op Linux gebaseerde computer de instructies in de [hand leiding voor de Flatcar container Linux Developer SDK](https://docs.flatcar-linux.org/os/sdk-modifying-flatcar/). Wanneer u het `image_to_vm.sh` script uitvoert, moet u `--format=azure` een virtuele harde schijf met Azure maken.

## <a name="next-steps"></a>Volgende stappen

Zodra u het VHD-bestand hebt, kunt u het resulterende bestand gebruiken om nieuwe virtuele machines te maken in Azure. Zie [een virtuele Linux-machine maken op basis van een aangepaste schijf](upload-vhd.md#option-1-upload-a-vhd)als dit de eerste keer is dat u een VHD-bestand naar Azure uploadt.
