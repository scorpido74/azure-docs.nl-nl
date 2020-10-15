---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67175737"
---
Net als bij toegangstokens, moet u, als er geen Azure AD-token is ingesteld, de gebeurtenis TokenRequired afhandelen of de methode tokenRequired implementeren op het gemachtigde protocol.

U kunt de gebeurtenis synchroon afhandelen door de eigenschap voor de gebeurtenisargumenten in te stellen.
