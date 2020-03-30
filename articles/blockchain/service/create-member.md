---
title: Een Azure Blockchain Service-lid maken - Azure-portal
description: Maak een Azure Blockchain Service-lid voor een blockchainconsortium met behulp van de Azure-portal.
ms.date: 03/12/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 3c468633a193d78fb1c017a756ee372c6feefb12
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79203658"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-the-azure-portal"></a>Snelstart: een Azure Blockchain Service-blockchain-lid maken met behulp van de Azure-portal

In deze quickstart implementeert u een nieuw blockchain-lid en consortium in Azure Blockchain Service via de Azure-portal.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Een Azure Blockchain Service-lid is een blockchain-knooppunt in een blockchain-netwerk voor een privéconsortium. Wanneer u een lid indient, u een consortiumnetwerk maken of lid worden. U hebt ten minste één lid nodig voor een consortiumnetwerk. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortiumdeelnemers kunnen een of meer blockchain-leden hebben of leden delen met andere deelnemers. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer in de linkerbovenhoek van Azure Portal **Een resource maken**.
1. Selecteer **Blockchain** > **Azure Blockchain Service (voorbeeld)**.

    ![Service maken](./media/create-member/create-member.png)

    Instelling | Beschrijving
    --------|------------
    Abonnement | Selecteer het Azure-abonnement dat u voor uw service wilt gebruiken. Als u meerdere abonnementen hebt, kiest u het abonnement waarin u wordt gefactureerd voor de resource.
    Resourcegroep | Maak een nieuwe naam van de brongroep of kies een bestaande naam uit uw abonnement.
    Regio | Kies een regio om het lid te maken. Alle leden van het consortium moeten zich op dezelfde locatie bevinden.
    Protocol | Momenteel ondersteunt Azure Blockchain Service Preview het Quorum-protocol.
    Consortium | Voer voor een nieuw consortium een unieke naam in. Als u zich via een uitnodiging aansluit bij een consortium, kiest u het consortium waartoe u lid wordt. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.
    Name | Kies een unieke naam voor het Azure Blockchain Service-lid. De naam van het blockchain-lid kan alleen kleine letters en cijfers bevatten. Het eerste teken moet een letter zijn. De waarde moet tussen de 2 en 20 tekens lang zijn.
    Wachtwoord van lidaccount | Het wachtwoord van het ledenaccount wordt gebruikt om de privésleutel voor het Ethereum-account dat voor uw lid is gemaakt, te versleutelen. U gebruikt het accountaccount en het wachtwoord van het ledenaccount voor consortiumbeheer.
    Prijzen | De knooppuntconfiguratie en kosten voor uw nieuwe service. Selecteer de koppeling **Wijzigen** om te kiezen tussen **standaard-** en **basislagen.** Gebruik de *basislaag* is voor ontwikkeling, testen en proof of concepts. Gebruik de *standaardlaag* voor implementaties van productiekwaliteit.
    Knooppuntwachtwoord | Het wachtwoord voor het standaard transactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie wanneer u verbinding maakt met het standaardtransactieknooppunt openbare eindpunt van het blockchain-lid.

1. Selecteer **Controleren + maken** om uw instellingen te valideren. Selecteer **Maken** om de service in te richten. Inrichten duurt ongeveer 10 minuten.
1. Selecteer **Meldingen** op de werkbalk om het implementatieproces te controleren.
1. Na implementatie navigeer je naar je blockchain-lid.

Selecteer **Overzicht**, u de basisgegevens over uw service bekijken, waaronder het RootContract-adres en het ledenaccount.

![Overzicht van Blockchain-leden](./media/create-member/overview.png)

## <a name="clean-up-resources"></a>Resources opschonen

U het lid dat u hebt gemaakt gebruiken voor de volgende quickstart of zelfstudie. Wanneer dit niet meer nodig is, `myResourceGroup` u de resources verwijderen door de resourcegroep die u voor de quickstart hebt gemaakt, te verwijderen.

De resourcegroep verwijderen:

1. Navigeer in de Azure-portal naar **de groep Resource** in het linkernavigatiedeelvenster en selecteer de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**. Verwijder het verwijderen door de naam van de brongroep in te voeren en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Blockchain Service-lid en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om Azure Blockchain Development Kit voor Ethereum te gebruiken om te koppelen aan een Azure Blockchain Service-lid.

> [!div class="nextstepaction"]
> [Visual Studio-code gebruiken om verbinding te maken met Azure Blockchain-service](connect-vscode.md)
