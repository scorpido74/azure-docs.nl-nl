---
title: IP-groepen maken in Azure Firewall
description: Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/23/2020
ms.author: victorh
ms.openlocfilehash: c3ae62bf5b4f0b4796cac2e7079c8a09116d4895
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85602530"
---
# <a name="create-ip-groups"></a>IP-groepen maken

Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren. Ze kunnen één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

## <a name="create-an-ip-group"></a>Een IP-groep maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ **IP-groepen** in het tekstvak zoeken en selecteer vervolgens **IP-groepen**.
3. Selecteer **Maken**.
4. Selecteer uw abonnement.
5. Selecteer een resource groep of maak een nieuwe.
6. Typ een unieke naam voor de IP-groep en selecteer vervolgens een regio.

6. Selecteer **volgende: IP-adressen**.
7. Typ een IP-adres, meerdere IP-adressen of IP-adresbereiken.

   Er zijn twee manieren om IP-adressen in te voeren:
   - U kunt deze hand matig invoeren
   - U kunt ze uit een bestand importeren

   Als u wilt importeren uit een bestand, selecteert u **importeren uit een bestand**. U kunt uw bestand naar het vak slepen of bladeren naar **bestanden**selecteren. Indien nodig kunt u uw geüploade IP-adressen bekijken en bewerken.

   Wanneer u een IP-adres typt, wordt het door de portal gecontroleerd om te controleren of er overlappende, dubbele en opmaak problemen zijn.

5. Wanneer u klaar bent, selecteert u **controleren + maken**.
6. Selecteer **Maken**.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over IP-groepen](ip-groups.md)