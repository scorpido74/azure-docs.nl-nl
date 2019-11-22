---
title: Een lid van de Azure Block Chain-service maken-Azure Portal
description: Maak een lid van de Azure Block Chain-Service voor een Block Chain consortium met behulp van de Azure Portal.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 11/18/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 9b08bf030d66c058ef1ec7c57f1fb2517046c522
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74285103"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snelstartgids: een Block Chain-lid van de Azure Block Chain-service maken met behulp van de Azure Portal

In deze Quick Start implementeert u een nieuw Block Chain-lid en consortium in azure Block Chain service met behulp van de Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Maak een Block Chain-lid dat het quorum grootboek protocol in een nieuw of bestaand consortium uitvoert.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **block chain** > **Azure Block Chain Service (preview)** .

    ![Service maken](./media/create-member/create-member.png)

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Maak een nieuwe resource groep of kies een bestaande naam uit uw abonnement.
    Regio | Kies een regio om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bestaan.
    Protocol | Azure Block Chain Service preview ondersteunt momenteel het quorum protocol.
    Consortium | Voer een unieke naam in voor een nieuw consortium. Als u een consortium wilt toevoegen via een uitnodiging, kiest u het consortium dat u wilt toevoegen.
    Naam | Kies een unieke naam voor het lid van de Azure Block Chain-service. De naam van het block Chain-lid mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen 2 en 20 tekens lang zijn.
    Wacht woord voor leden account | Het wacht woord van het lid-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor uw lid wordt gemaakt. U gebruikt het wacht woord voor het account en het lid van het account voor het beheer van consortiums.
    Beschrijving | Beschrijving van het consortium.
    Prijzen | De knooppunt configuratie en de kosten voor uw nieuwe service. Selecteer de koppeling **wijzigen** om te kiezen tussen de **Standard** -en **Basic** -laag.
    Knooppunt wachtwoord | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid.

1. Selecteer **controleren + maken** om uw instellingen te valideren. Selecteer **maken** om de service in te richten. Het inrichten duurt ongeveer 10 minuten.
1. Selecteer **meldingen** op de werk balk om het implementatie proces te bewaken.
1. Ga na de implementatie naar het block Chain-lid.

Selecteer **overzicht**, u kunt de basis informatie over uw service bekijken, met inbegrip van het RootContract-adres en het account van de gebruiker.

![Overzicht van Block Chain-leden](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het lid gebruiken dat u hebt gemaakt voor de volgende Snelstartgids of zelf studie. Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup` resource groep te verwijderen die u hebt gemaakt voor de Snelstartgids.

De resource groep verwijderen:

1. In de Azure-portal, gaat u naar **resourcegroep** in het navigatiedeelvenster links en selecteert u de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Bevestig de verwijdering door de naam van de resourcegroep in te voeren en selecteer **verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lid van de Azure Block Chain-service en een nieuw consortium geïmplementeerd. Probeer de volgende Snelstartgids om Azure Block Chain Development Kit te gebruiken voor Ethereum om te koppelen aan een consortium op Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om verbinding te maken met een Azure Block Chain Service consortium-netwerk](connect-vscode.md)
