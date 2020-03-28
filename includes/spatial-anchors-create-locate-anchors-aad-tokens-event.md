---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67175737"
---
Als een Azure AD-token niet is ingesteld, moet u, net als bij toegangstokens, de gebeurtenis TokenRequired afhandelen of de tokenVereiste-methode implementeren op het gemachtigdeprotocol.

U de gebeurtenis synchroon afhandelen door de eigenschap in te stellen op de gebeurtenisargumenten.
