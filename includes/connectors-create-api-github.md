---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 03/02/2018
ms.openlocfilehash: 1aabc1b33722c571c6519b6620fb62211a44c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789758"
---
1. Maak in de [Azure-portal](https://portal.azure.com)een lege logische app. 

2. Typ in de Logic Apps Designer 'github' als filter. 

3. Selecteer de GitHub-connector en de trigger die u wilt gebruiken.

   ![Selecteer de GitHub-connector en een trigger](./media/connectors-create-api-github/github-connector.png)

   > [!NOTE]
   > Alle logische app-werkstromen moeten beginnen met een trigger. U acties alleen selecteren wanneer uw logische werkstroom al met een trigger begint. 

4. Als u niet eerder een verbinding hebt gemaakt, kiest u **Aanmelden,** zodat u uw GitHub-referenties verstrekken wanneer u daarom wordt gevraagd.  

   ![Inloggen met uw GitHub-referenties](./media/connectors-create-api-github/github-connector-sign-in-credentials.png)

   Uw logica-app gebruikt deze referenties om het verbinden en openen van gegevens voor uw GitHub-account te autoriseren. 

5. Geef uw GitHub-gebruikersnaam en -wachtwoord op en bevestig vervolgens uw autorisatie.

   ![Referenties verstrekken en autorisatie bevestigen](./media/connectors-create-api-github/github-connector-authorize.png)   

   Uw verbinding is nu gemaakt in de Azure-portal en is klaar voor gebruik.

6. Blijf uw logische app-werkstroom definiëren.

   ![Meer acties toevoegen aan de werkstroom van uw logische app](./media/connectors-create-api-github/github-connector-logic-app.png)

