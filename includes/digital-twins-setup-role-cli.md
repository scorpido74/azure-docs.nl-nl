---
author: baanders
description: bestand insluiten voor vereiste rol in azure Digital Apparaatdubbels-installatie
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 1714fac465531a2d3a5829e7080132510ccd1ec4
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407446"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

## <a name="prerequisites-permission-requirements"></a>Vereisten: machtigings vereisten

Als u alle stappen in dit artikel wilt uitvoeren, moet u worden geclassificeerd als een eigenaar van uw Azure-abonnement. 

U kunt uw machtigings niveau controleren door deze opdracht uit te voeren in Cloud Shell:

```azurecli-interactive
az role assignment list --assignee <your-Azure-email>
```

Als u een eigenaar bent, `roleDefinitionName` is de waarde in de uitvoer *eigenaar*:

:::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/cloud-shell/owner-role.png" alt-text="Cloud Shell venster met de uitvoer van de opdracht AZ Role Assignment List":::

Als u merkt dat de waarde *Inzender* of iets anders is dan de *eigenaar*, kunt u door gaan op een van de volgende manieren:
* Neem contact op met de eigenaar van uw abonnement en vraag de eigenaar om de stappen in dit artikel namens u uit te voeren
* Neem contact op met de eigenaar van uw abonnement of iemand met de rol van beheerder voor gebruikers toegang voor het abonnement en vraag om u te helpen bij de eigenaar van het abonnement, zodat u de juiste machtigingen hebt om door te gaan. Of dit geschikt is, is afhankelijk van uw organisatie en uw rol.