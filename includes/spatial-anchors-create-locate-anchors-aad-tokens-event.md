---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "67175737"
---
Net als bij toegangstokens, moet u, als er geen Azure AD-token is ingesteld, de gebeurtenis TokenRequired afhandelen of de methode tokenRequired implementeren op het gemachtigde protocol.

U kunt de gebeurtenis synchroon afhandelen door de eigenschap voor de gebeurtenisargumenten in te stellen.
