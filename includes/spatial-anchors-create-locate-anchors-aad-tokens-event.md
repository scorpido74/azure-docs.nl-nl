---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67175737"
---
Net als bij toegangs tokens, moet u, als er geen Azure AD-token is ingesteld, de gebeurtenis TokenRequired afhandelen of de methode tokenRequired implementeren op het gemachtigde protocol.

U kunt de gebeurtenis synchroon afhandelen door de eigenschap voor de gebeurtenis argumenten in te stellen.
