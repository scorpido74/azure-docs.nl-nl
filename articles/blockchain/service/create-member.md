---
title: Een lid van de Azure Block Chain-service maken met behulp van de Azure Portal
description: Gebruik de Azure Block Chain-service om een Block Chain-lid te maken.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: c87f9a044d6655b2062f2c2657d3b465699e0dd2
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329264"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snelstartgids: een Block Chain-lid van de Azure Block Chain-service maken met behulp van de Azure Portal

De Azure Block Chain-service is een Block Chain-platform waarmee u uw bedrijfs logica binnen een slim contract kunt uitvoeren. In deze Snelstartgids ziet u hoe u aan de slag kunt met het maken van een Block Chain-lid met behulp van de Azure Portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Maak een Block Chain-lid dat het quorum grootboek protocol in een nieuw of bestaand consortium uitvoert.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **block chain** > **Azure Block Chain-Service**.
1. Voltooi de sjabloon.

    ![Service maken](./media/create-member/create-member.png)

    Instelling | Beschrijving
    --------|------------
    Block Chain-lid | Kies een unieke naam die uw Azure Block Chain Service Block Chain-lid aanduidt. De naam van het block Chain-lid mag alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen 2 en 20 tekens lang zijn.
    Abonnement | Selecteer het Azure-abonnement dat u wilt gebruiken voor uw service. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Een nieuwe resourcegroepnaam of een bestaande naam uit uw abonnement.
    Regio | De locatie moet hetzelfde zijn voor alle leden van het consortium.
    Wacht woord voor leden account | Het wacht woord van het lid-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor uw lid wordt gemaakt. U gebruikt het wacht woord voor het account en het lid van het account voor het beheer van consortiums.
    Consortium naam | Voer een unieke naam in voor een nieuw consortium. Als u een consortium wilt toevoegen via een uitnodiging, is de waarde het consortium dat u wilt toevoegen.
    Beschrijving | Beschrijving van het consortium.
    Protocol |  Preview ondersteunt het quorum protocol.
    Prijzen | De knooppunt configuratie voor uw nieuwe service. Selecteer **standaard**. 2 validatie knooppunten en 1 transactie knooppunt zijn de standaard instellingen.
    Wacht woord van transactie knooppunt | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid.

1. Selecteer **maken** om de service in te richten. Het inrichten duurt ongeveer 10 minuten.
1. Selecteer **meldingen** op de werk balk om het implementatie proces te bewaken.
1. Ga na de implementatie naar het block Chain-lid.

Selecteer **overzicht**, u kunt de basis informatie over uw service bekijken, met inbegrip van het RootContract-adres en het account van de gebruiker.

![Overzicht van Block Chain-leden](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het lid gebruiken dat u hebt gemaakt voor de volgende Snelstartgids of zelf studie. Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup`-Resource groep te verwijderen die u hebt gemaakt door de Azure Block Chain-service.

De resource groep verwijderen:

1. Ga in het Azure Portal naar de **resource groep** in het navigatie deel venster links en selecteer de resource groep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Controleer het verwijderen door de naam van de resource groep in te voeren en **verwijderen**te selecteren.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een slim contract gebruiken om verbinding te maken en te implementeren](connect-metamask.md)