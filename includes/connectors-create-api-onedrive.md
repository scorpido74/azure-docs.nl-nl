---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 951ab2300aa4ffed2c5f1039ff993cd7f6af543f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789665"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -account 

Voordat u uw OneDrive-account kunt gebruiken in een logische app, moet u de logische app machtigen om verbinding te maken met uw OneDrive-account.  U kunt dit eenvoudig doen in uw logische app op het Azure Portal. 

Machtig uw logische app om verbinding te maken met uw OneDrive-account door de volgende stappen uit te voeren:

1. Maak een logische app. Selecteer in de Logic Apps Designer **micro soft Managed Api's weer geven** in de vervolg keuzelijst en voer ' onedrive ' in het zoekvak in. Selecteer een van de Triggers of acties:  
   ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Als u nog geen verbindingen met OneDrive hebt gemaakt, wordt u gevraagd u aan te melden met uw OneDrive-referenties:  
   ![](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecteer **Aanmelden**en voer uw gebruikers naam en wacht woord in. Selecteer **Aanmelden**:  
   ![](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Deze referenties worden gebruikt om uw logische app te autoriseren om verbinding te maken met en om toegang te krijgen tot de gegevens in uw OneDrive-account. 
4. Selecteer **Ja** om de logische app te autoriseren voor het gebruik van uw OneDrive-account:  
   ![](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. U ziet dat de verbinding is gemaakt. Ga nu verder met de andere stappen in uw logische app:  
   ![](./media/connectors-create-api-onedrive/onedrive-5.png)

