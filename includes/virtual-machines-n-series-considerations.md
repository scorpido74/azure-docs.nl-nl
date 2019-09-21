---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 06/19/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 14f5998ee1c562b649257f7dce9ffc2f52a66226
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174947"
---
## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

* Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor de beschik baarheid van virtuele machines uit de N-serie.

* Vm's uit de N-serie kunnen alleen worden geïmplementeerd in het Resource Manager-implementatie model.

* Vm's uit de N-serie verschillen in het type Azure Storage ze voor hun schijven ondersteunen. NC-en NV-Vm's bieden alleen ondersteuning voor VM-schijven die worden ondersteund door de standaard Disk Storage (HDD). NCv2-, NCv3-, ND-, NDv2-en NVv2-Vm's bieden alleen ondersteuning voor VM-schijven die worden ondersteund door Premium-Disk Storage (SSD).

* Als u meer dan een paar virtuele machines uit de N-serie wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

* Mogelijk moet u het quotum voor kernen (per regio) in uw Azure-abonnement verhogen en de afzonderlijke quota voor NC-, NCv2-, NCv3-, ND-, NDv2-, NV-of NVv2-kernen verhogen. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../articles/azure-supportability/how-to-create-azure-support-request.md) . De standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.




