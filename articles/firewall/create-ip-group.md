---
title: IP-groepen maken in Azure Firewall
description: Met IP-groepen u IP-adressen groeperen en beheren voor Azure Firewall-regels.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: 7e8b2350b9e85d07ce1c399ce4536703ec998cbc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444535"
---
# <a name="create-ip-groups-preview"></a>IP-groepen maken (voorbeeld)

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Met IP-groepen u IP-adressen groeperen en beheren voor Azure Firewall-regels. Ze kunnen één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

## <a name="create-an-ip-group"></a>Een IP-groep maken

1. Selecteer op de startpagina van de Azure-portal de optie **Een bron maken**.
2. Typ **IP-groepen** in het zoektekstvak en selecteer **IP-groepen**.
3. Selecteer **Maken**.
4. Selecteer uw abonnement.
5. Selecteer een resourcegroep of maak een nieuwe groep.
6. Typ een unieke naam voor uw IP-groep en selecteer vervolgens een regio.

6. Selecteer **Volgende: IP-adressen**.
7. Typ een IP-adres, meerdere IP-adressen of IP-adresbereiken.

   Er zijn twee manieren om IP-adressen in te voeren:
   - U ze handmatig invoeren
   - U ze importeren uit een bestand

   Als u uit een bestand wilt importeren, selecteert u **Importeren uit een bestand**. U uw bestand naar het vak slepen of **Bladeren naar bestanden**selecteren. Indien nodig u uw geüploade IP-adressen bekijken en bewerken.

   Wanneer u een IP-adres typt, valideert de portal het om te controleren op overlappings-, duplicaten- en opmaakproblemen.

5. Als u klaar bent, selecteert u **Controleren + Maken**.
6. Selecteer **Maken**.


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over IP-groepen](ip-groups.md)