---
title: Toepassingen in azure Block Chain Workbench preview gebruiken
description: Zelf studie over het gebruik van toepassings contracten in azure Block Chain Workbench preview.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 09/05/2019
ms.topic: tutorial
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: e1ba09c4fbba6492e2d5f870a6c31a3d5a6d8956
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70845137"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>Zelfstudie: Toepassingen gebruiken in Azure Blockchain Workbench

U kunt Blockchain Workbench gebruiken om contracten te maken en acties op contracten uit te voeren. Daarnaast kunt gegevens van contracten bekijken, zoals de status en transactiegeschiedenis.

U leert het volgende:

> [!div class="checklist"]
> * Een nieuw contract maken
> * Actie ondernemen op een contract

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

* Een Blockchain Workbench-implementatie. Zie [Azure Blockchain Workbench implementeren](deploy.md) voor meer informatie.
* Een geïmplementeerde blockchain-toepassing in Blockchain Workbench. Zie [Een blockchain-toepassing maken in Azure Blockchain Workbench](create-app.md) voor meer informatie.

[Open Blockchain Workbench](deploy.md#blockchain-workbench-web-url) in uw browser.

![Blockchain Workbench](./media/use/workbench.png)

U moet zich aanmelden als lid van Blockchain Workbench. Als er geen toepassingen worden vermeld, bent u wel lid van Blockchain Workbench, maar niet van een toepassing. De beheerder van Blockchain Workbench kan leden toewijzen aan toepassingen.

## <a name="create-new-contract"></a>Nieuw contract maken

Als u een nieuw contract wilt maken, moet u een lid zijn dat is opgegeven als een contract **aanvrager**. Zie [Werkstromen in het configuratieoverzicht](configuration.md#workflows) voor informatie over het definiëren van toepassingsrollen en initiators voor het contract. Zie [Een lid toevoegen aan een toepassing](manage-users.md#add-member-to-application) voor informatie over het toewijzen van leden aan toepassingsrollen.

1. Selecteer in de sectie Blockchain Workbench-toepassing de toepassingstegel met het contract dat u wilt maken. U ziet een lijst met actieve contracten.

2. Selecteer **Nieuw contract** om een nieuw contract te maken.

    ![De knop Nieuw contract](./media/use/contract-list.png)

3. Het deelvenster **Nieuw contract** wordt weergegeven. Geef waarden op voor de parameters. Selecteer **Maken**.

    ![Deelvenster voor nieuw contract](./media/use/new-contract.png)

    Het zojuist gemaakte contract wordt weergegeven in de lijst met de andere actieve contracten.

    ![Lijst met actieve contracten](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>Actie ondernemen op een contract

Afhankelijk van de status van het contract, kunnen leden actie ondernemen om naar de volgende status van het contract te gaan. Acties zijn gedefinieerd als [overgangen](configuration.md#transitions) binnen een [status](configuration.md#states). Leden die horen bij een toegestane toepassing of exemplaarrol voor de overgang kunnen de actie uitvoeren. 

1. In de sectie van de Blockchain Workbench-toepassing, selecteer de toepassingstegel met de opdracht om de actie te ondernemen.
2. Selecteer het contract in de lijst. U ziet in verschillende secties gegevens van het contract. 

    ![Contractgegevens](./media/use/contract-details.png)

    | Section  | Description  |
    |---------|---------|
    | Status | Een overzicht van de huidige voortgang binnen de fasen van het contract |
    | Details | De huidige waarden van het contract |
    | Action | Gegevens van de laatste actie |
    | Activiteit | Transactiegeschiedenis van het contract |
    
3. Selecteer **Actie ondernemen** in de sectie **Actie**.

4. De gegevens van de huidige status van het contract worden in een deelvenster weergegeven. Kies de gewenste actie in de vervolgkeuzelijst. 

    ![Actie kiezen](./media/use/choose-action.png)

5. Selecteer **Actie ondernemen** om de actie uit te voeren.
6. Als er parameters vereist zijn voor de actie, geeft u de waarden voor de actie op.

    ![Actie ondernemen](./media/use/take-action.png)

7. Selecteer **Actie ondernemen** om de actie uit te voeren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Versiebeheer van Azure Blockchain Workbench-toepassing](version-app.md)
