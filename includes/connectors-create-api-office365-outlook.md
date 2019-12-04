---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 635a69fc8e75e3a6249728dc1df31cf839b345c6
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789596"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [Office 365](https://office365.com) -account  

Voordat u uw Office 365-account in een logische app kunt gebruiken, moet u de logische app machtigen om verbinding te maken met uw Office 365-account. U kunt dit eenvoudig doen in uw logische app op het Azure Portal.  

Machtig uw logische app om verbinding te maken met uw Office 365-account door de volgende stappen uit te voeren:

1. Maak een logische app. Selecteer in de Logic Apps Designer **micro soft Managed Api's weer geven** in de vervolg keuzelijst en voer vervolgens ' Office 365 ' in het zoekvak in. Selecteer een van de Triggers of acties:  
    ![Office 365-verbinding maken stap](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Als u nog geen verbindingen met Office 365 hebt gemaakt, wordt u gevraagd u aan te melden met uw Office 365-referenties:  
    ![Stap voor het maken van een verbinding met Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Selecteer **Aanmelden**en voer uw gebruikers naam en wacht woord in. Selecteer **Aanmelden**:  
    ![Office 365-verbinding maken stap](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Deze referenties worden gebruikt om uw logische app te autoriseren om verbinding te maken met en om toegang te krijgen tot uw Office 365-account. 
4. U ziet dat de verbinding is gemaakt. Ga nu verder met de andere stappen in uw logische app:   
    ![Stap voor het maken van een verbinding met Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

