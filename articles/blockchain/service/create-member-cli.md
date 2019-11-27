---
title: Een lid van de Azure Block Chain-service maken-Azure CLI
description: Maak een lid van de Azure Block Chain-Service voor een Block Chain consortium met behulp van de Azure CLI.
ms.date: 11/20/2019
ms.topic: quickstart
ms.reviewer: janders
ms.openlocfilehash: 6a9673431c3da21b3ce645b9aff30506be1012f3
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455945"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snelstartgids: een Block Chain-lid van de Azure Block Chain-service maken met behulp van Azure CLI

In deze Quick Start implementeert u een nieuw Block Chain-lid en consortium in azure Block Chain service met behulp van Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U kunt Cloud Shell ook openen in een afzonderlijk browsertabblad door naar [https://shell.azure.com/bash](https://shell.azure.com/bash) te gaan. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u liever de CLI lokaal wilt installeren en gebruiken, is voor deze Snelstartgids Azure CLI-versie 2.0.51 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Maak een Block Chain-lid in de Azure Block Chain-service die het quorum grootboek protocol in een nieuw consortium uitvoert. Er zijn verschillende para meters en eigenschappen die u moet door geven. Vervang de voorbeeld parameters door uw waarden.

```azurecli-interactive
az resource create \
                    --resource-group myResourceGroup \
                    --name myblockchainmember \
                    --resource-type Microsoft.Blockchain/blockchainMembers \
                    --is-full-object \
                    --properties '{"location":"westus2", "properties":{"password":"strongMemberAccountPassword@1", "protocol":"Quorum", "consortium":"myConsortiumName", "consortiumManagementAccountPassword":"strongConsortiumManagementPassword@1"}, "sku":{"name":"S0"}}'
```

| Parameter | Beschrijving |
|---------|-------------|
| **resource-groep** | De naam van de resource groep waar de Azure Block Chain-Service resources worden gemaakt. Gebruik de resource groep die u in de vorige sectie hebt gemaakt.
| **naam** | Een unieke naam die uw Azure Block Chain Service Block Chain-lid aanduidt. De naam wordt gebruikt voor het adres van het open bare eind punt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`.
| **location** | Azure-regio waar het block Chain-lid wordt gemaakt. Bijvoorbeeld `westus2`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
| **wacht woord** | Het wacht woord voor het standaard transactie knooppunt van het lid. Gebruik het wacht woord voor basis verificatie bij het maken van verbinding met het open bare eind punt van het standaard transactie knooppunt van het block Chain-lid.
| **verband** | Naam van het consortium dat u wilt toevoegen of maken.
| **consortiumAccountPassword** | Het wacht woord van het consortium account wordt ook wel het wacht woord van het lid-account genoemd. Het wacht woord van het lid-account wordt gebruikt voor het versleutelen van de persoonlijke sleutel voor het Ethereum-account dat voor uw lid wordt gemaakt. U gebruikt het wacht woord voor het account en het lid van het account voor het beheer van consortiums.
| **skuName** | Type laag. Gebruik S0 voor Standard en B0 voor Basic.

Het duurt ongeveer 10 minuten om het block Chain-lid en de ondersteunende bronnen te maken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt het block Chain-lid gebruiken dat u hebt gemaakt voor de volgende Snelstartgids of zelf studie. Wanneer u deze niet meer nodig hebt, kunt u de resources verwijderen door de `myResourceGroup` resource groep te verwijderen die u hebt gemaakt voor de Snelstartgids.

Voer de volgende opdracht uit om de resource groep en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een lid van de Azure Block Chain-service en een nieuw consortium geïmplementeerd. Probeer de volgende Snelstartgids om Azure Block Chain Development Kit te gebruiken voor Ethereum om te koppelen aan een consortium op Azure Block Chain-service.

> [!div class="nextstepaction"]
> [Visual Studio code gebruiken om verbinding te maken met de Azure Block Chain-Service](connect-vscode.md)
