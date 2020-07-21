---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 11/03/2016
ms.openlocfilehash: 8b5f72f18ee5bf9c1e4dbaaa87d6705f55b09bc5
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86524201"
---
## <a name="prerequisites"></a>Vereisten

* Een Azure-account; u kunt een [gratis account](https://azure.microsoft.com/free) maken
* Een [OneDrive](https://www.microsoft.com/store/apps/onedrive/9wzdncrfj1p3) -account 

Voordat u uw OneDrive-account kunt gebruiken in een logische app, moet u de logische app machtigen om verbinding te maken met uw OneDrive-account.  U kunt dit eenvoudig doen in uw logische app op het Azure Portal. 

Machtig uw logische app om verbinding te maken met uw OneDrive-account door de volgende stappen uit te voeren:

1. Maak een logische app. Selecteer in de Logic Apps Designer **micro soft Managed Api's weer geven** in de vervolg keuzelijst en voer ' onedrive ' in het zoekvak in. Selecteer een van de Triggers of acties:  
   ![Een dialoog venster met de titel ' door micro soft beheerde Api's weer geven ' bevat een zoekvak dat ' onedrive ' bevat. Hieronder ziet u een lijst met vier triggers. De eerste in de lijst is ' OneDrive-wanneer een bestand wordt gemaakt ', dat is geselecteerd.](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Als u nog geen verbindingen met OneDrive hebt gemaakt, wordt u gevraagd u aan te melden met uw OneDrive-referenties:  
   ![Er wordt een dialoog venster met de titel ' OneDrive-wanneer een bestand wordt gemaakt ' met een knop met de naam ' meldt '.](./media/connectors-create-api-onedrive/onedrive-2.png)
3. Selecteer **Aanmelden**en voer uw gebruikers naam en wacht woord in. Selecteer **Aanmelden**:  
   ![In een dialoog venster met de titel ' Aanmelden ' krijgt u de Microsoft-account. Het heeft twee tekst vakken met het label "E-mail of telefoon" en "wacht woord" heeft ook een selectie vakje met het label "aangemeld blijven" en een knop met het label "aanmelden".](./media/connectors-create-api-onedrive/onedrive-3.png)   
   
    Deze referenties worden gebruikt om uw logische app te autoriseren om verbinding te maken met en om toegang te krijgen tot de gegevens in uw OneDrive-account. 
4. Selecteer **Ja** om de logische app te autoriseren voor het gebruik van uw OneDrive-account:  
   ![Een dialoog venster met de titel ' laat deze app toegang krijgen tot uw gegevens ' vraagt om toestemming om de volgende vier dingen uit te voeren: 1) ' Meld u automatisch aan ', 2) ' toegang tot uw e-mail adressen ', 3) ' toegang tot uw gegevens altijd ' en 4) ' toegang tot OneDrive-bestanden '. Er is een knop ' ja ' om machtigingen toe te kennen en een knop ' nee ' om deze te weigeren. Er is een koppeling om deze toepassings machtigingen te wijzigen.](./media/connectors-create-api-onedrive/onedrive-4.png)   
5. U ziet dat de verbinding is gemaakt. Ga nu verder met de andere stappen in uw logische app:  
   ![Een dialoog venster met de titel ' wanneer een bestand wordt gemaakt ' heeft een tekstvak met de titel ' map ' met een bijbehorende knop Bladeren.](./media/connectors-create-api-onedrive/onedrive-5.png)

