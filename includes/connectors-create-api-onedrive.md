---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789665"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-account; u een [gratis account aanmaken](https://azure.microsoft.com/free)
* Een [OneDrive-account](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) 

Voordat u uw OneDrive-account gebruiken in een logische app, moet u de logische app autoriseren om verbinding te maken met uw OneDrive-account.  U dit eenvoudig doen binnen uw logische app op de Azure-portal. 

Autoriseer uw logische app om verbinding te maken met uw OneDrive-account met de volgende stappen:

1. Maak een logische app. Selecteer in de ontwerper Logic Apps De optie **Door Microsoft beheerde API's weergeven** in de vervolgkeuzelijst en voer vervolgens 'onedrive' in het zoekvak in. Selecteer een van de triggers of acties:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Als u nog niet eerder verbindingen met OneDrive hebt gemaakt, wordt u gevraagd u aan te melden met uw OneDrive-referenties:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecteer **Aanmelden**en voer uw gebruikersnaam en wachtwoord in. Selecteer **Aanmelden:**  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Deze referenties worden gebruikt om uw logische app te autoriseren om verbinding mee te maken en toegang te krijgen tot de gegevens in uw OneDrive-account. 
4. Selecteer **Ja** om de logische app te autoriseren om uw OneDrive-account te gebruiken:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. Let op dat de verbinding is gemaakt. Ga nu verder met de andere stappen in uw logica-app:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

