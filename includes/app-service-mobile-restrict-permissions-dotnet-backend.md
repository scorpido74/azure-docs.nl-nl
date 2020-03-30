---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67176528"
---
Standaard kunnen API's in een back-end van mobiele apps anoniem worden aangeroepen. Vervolgens moet u de toegang beperken tot alleen geverifieerde clients.  

* **Node.js back-end (via de Azure-portal):**  

    Klik in de instellingen van mobiele apps op **Eenvoudige tabellen** en selecteer uw tabel. Klik **op Machtigingen wijzigen,** selecteer **Alleen geverifieerde toegang** voor alle machtigingen en klik op **Opslaan**.
* **.NET back-end (C#)**:  

    Navigeer in het serverproject naar **Controllers** > **TodoItemController.cs**. Voeg `[Authorize]` het kenmerk als volgt toe aan de klasse **TodoItemController.** Als u de toegang alleen tot specifieke methoden wilt beperken, u dit kenmerk ook alleen toepassen op die methoden in plaats van op de klasse. Publiceer het serverproject opnieuw.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Node.js backend (via Node.js code)** :  

    Als u verificatie voor tabeltoegang wilt vereisen, voegt u de volgende regel toe aan het node.js-serverscript:

        table.access = 'authenticated';

    Zie [Verificatie vereisen voor toegang tot tabellen voor](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth)meer informatie. Zie [Het backend-project voor backend van Node.js downloaden met Git voor](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart)meer informatie over het downloaden van het project quickstartcode van uw site.
