---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335021"
---
| Resource | Limiet |
| --- | --- |
| [Virtuele machines](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per cloudservice<sup>1</sup> |50 |
| Eindpunten invoeren per cloudservice<sup>2</sup> |150 |

<sup>1.</sup> Virtuele machines die zijn gemaakt met behulp van het klassieke implementatiemodel in plaats van Azure Resource Manager, worden automatisch opgeslagen in een cloudservice. U meer virtuele machines toevoegen aan die cloudservice voor taakverdeling en beschikbaarheid. 

<sup>2.</sup> Met ingangseindpunten u communicatie naar een virtuele machine van buiten de cloudservice van de virtuele machine maken. Virtuele machines in dezelfde cloudservice of virtueel netwerk kunnen automatisch met elkaar communiceren. Zie [Eindpunten instellen op een virtuele machine voor](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)meer informatie. 
