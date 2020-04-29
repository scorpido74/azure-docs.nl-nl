---
title: IP-groepen maken in Azure Firewall
description: Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444535"
---
# <a name="create-ip-groups-preview"></a>IP-groepen maken (preview)

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Met IP-groepen kunt u IP-adressen voor Azure Firewall regels groeperen en beheren. Ze kunnen één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

## <a name="create-an-ip-group"></a>Een IP-groep maken

1. Op de start pagina van Azure Portal selecteert u **een resource maken**.
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