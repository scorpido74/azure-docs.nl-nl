---
title: Een lid van de Azure Block Chain-service maken-Azure Portal
description: Maak een lid van de Azure Block Chain-Service voor een Block Chain consortium met behulp van de Azure Portal.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 3c468633a193d78fb1c017a756ee372c6feefb12
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203658"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snelstartgids: een Block Chain-lid van de Azure Block Chain-service maken met behulp van de Azure Portal

In deze Quick Start implementeert u een nieuw Block Chain-lid en consortium in azure Block Chain service met behulp van de Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Een Block Chain-lid maken

Een lid van de Azure Block Chain-service is een Block Chain-knoop punt in een particulier consortium Block chain-netwerk. Bij het inrichten van een lid kunt u een consortium netwerk maken of eraan toevoegen. U hebt ten minste één lid nodig voor een consortium netwerk. Het aantal Block Chain-leden dat nodig is voor deel nemers is afhankelijk van uw scenario. Consortium deelnemers kunnen een of meer Block Chain leden hebben of ze kunnen leden delen met andere deel nemers. Zie [Azure Block Chain Service consortium](consortium.md)voor meer informatie over consortiums.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **block chain** > **Azure Block Chain Service (preview)** .

    ![Service maken](./media/create-member/create-member.png)

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Maak een nieuwe resource groep of kies een bestaande naam uit uw abonnement.
    Regio | Kies een regio om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bestaan.
    Protocol | Azure Block Chain Service preview ondersteunt momenteel het quorum protocol.
    Consortium | Voer een unieke naam in voor een nieuw consortium. Als u een consortium wilt toevoegen via een uitnodiging, kiest u het consortium dat u wilt toevoegen. Zie [Azure Block Chain Service consortium](consortium.md)voor meer informatie over consortiums.
    Naam | Kies een unieke naam voor het lid van de Azure Block Chain-service. De naam van het block Chain-lid mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen 2 en 20 tekens lang zijn.
    Wacht woord voor leden account | Het wacht woord van het lid-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor uw lid wordt gemaakt. U gebruikt het wacht woord voor het account en het lid van het account voor het beheer van consortiums.
    Prijzen | De knooppunt configuratie en de kosten voor uw nieuwe service. Selecteer de koppeling **wijzigen** om te kiezen tussen de **Standard** -en **Basic** -laag. De *basis* -laag gebruiken is voor het ontwikkelen, testen en controleren van concepten. De *Standard* -laag gebruiken voor implementaties van productie cijfers.
    Knooppunt wachtwoord | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid.

1. Selecteer **controleren + maken** om uw instellingen te valideren. Selecteer **maken** om de service in te richten. Het inrichten duurt ongeveer 10 minuten.
1. Selecteer **meldingen** op de werk balk om het implementatie proces te bewaken.
1. Ga na de implementatie naar het block Chain-lid.

Selecteer **overzicht**, u kunt de basis informatie over uw service bekijken, met inbegrip van het RootContract-adres en het account van de gebruiker.

![Overzicht van Block Chain-leden](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het lid gebruiken dat u hebt gemaakt voor de volgende Snelstartgids of zelf studie. Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup` resource groep te verwijderen die u hebt gemaakt voor de Snelstartgids.

De resource groep verwijderen:

1. Ga in het Azure Portal naar de **resource groep** in het navigatie deel venster links en selecteer de resource groep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Controleer het verwijderen door de naam van de resource groep in te voeren en **verwijderen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lid van de Azure Block Chain-service en een nieuw consortium geïmplementeerd. Probeer de volgende Snelstartgids om Azure Block Chain Development Kit te gebruiken voor Ethereum om te koppelen aan een lid van de Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om verbinding te maken met de Azure Block Chain-Service](connect-vscode.md)
