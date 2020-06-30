---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 7c3887367b6365ea3577bbff6cc19bf34d178ee6
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095310"
---
### <a name="create-a-notification-hub"></a>Een Notification Hub maken 

In deze sectie maakt u een Notification Hub en configureert u verificatie met **APNS**. U kunt een P12-pushcertificaat of verificatie op basis van tokens gebruiken. Als u een Notification Hub wilt gebruiken die u al hebt gemaakt, kunt u doorgaan naar stap 5.

1. Meld u aan bij [Azure](https://portal.azure.com).

1. Klik op **Een resource maken**, zoek naar en kies **Notification Hub** en klik vervolgens op **Maken**.

1. Werk de volgende velden bij en klik vervolgens op **Maken**:

    **BASISGEGEVENS**  

    **Abonnement:** Kies het doel **abonnement** in de vervolgkeuzelijst  
    **Resourcegroep:** Maak een nieuwe **resourcegroep** (of kies een bestaande)  

    **NAAMRUIMTEDETAILS**  

    **Notification Hub-naamruimte:** Voer een wereldwijd unieke naam in voor de naamruimte **Notification Hub**  

    > [!NOTE]
    > Zorg ervoor dat de optie **Nieuw maken** is geselecteerd voor dit veld.

    **DETAILS VAN NOTIFICATION HUB**  

    **Notification Hub:** Voer een naam in voor de **Notification Hub**  
    **Locatie:** Kies een geschikte locatie in de vervolgkeuzelijst  
    **Prijscategorie:** De standaardoptie **Gratis** blijven gebruiken  

    > [!NOTE]
    > Tenzij u het maximum aantal hubs in de gratis laag hebt bereikt.

1. Wanneer de **Notification Hub** is ingericht, gaat u naar die resource.
1. Ga naar uw nieuwe **Notification Hub**.
1. Selecteer **Toegangsbeleid** uit de lijst (onder **BEHEREN**).
1. Noteer de naam van de waarden voor **Policy Name** samen met de bijbehorende waarden voor **Verbindingsreeks**.
