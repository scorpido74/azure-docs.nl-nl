---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 2cfd376f595ae70daf9ab468d464dd9c8ff13d74
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335021"
---
| Resource | Limiet |
| --- | --- |
| [Virtuele machines](../articles/virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) per Cloud service<sup>1</sup> |50 |
| Invoer eindpunten per Cloud service<sup>2</sup> |150 |

<sup>1</sup> Virtuele machines die zijn gemaakt met behulp van het klassieke implementatie model in plaats van Azure Resource Manager, worden automatisch opgeslagen in een Cloud service. U kunt meer virtuele machines toevoegen aan die Cloud service voor taak verdeling en beschik baarheid. 

<sup>2</sup> Met invoer eindpunten kunnen berichten van buiten de Cloud service van de virtuele machine worden gecommuniceerd naar een virtuele machine. Virtuele machines in dezelfde Cloud service of een virtueel netwerk kunnen automatisch met elkaar communiceren. Zie [eind punten instellen voor een virtuele machine](../articles/virtual-machines/windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)voor meer informatie. 
