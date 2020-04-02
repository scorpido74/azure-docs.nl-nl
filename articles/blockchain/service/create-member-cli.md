---
title: Een Azure Blockchain Service-lid maken - Azure CLI
description: Maak een Azure Blockchain Service-lid voor een blockchainconsortium met de Azure CLI.
ms.date: 03/30/2020
ms.topic: quickstart
ms.reviewer: ravastra
ms.openlocfilehash: 322b1884726b6dfe322560032ed1b8a98c600154
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529621"
---
# <a name="quickstart-create-an-azure-blockchain-service-blockchain-member-using-azure-cli"></a>Snelstart: een Azure Blockchain Service-blockchain-lid maken met Azure CLI

In deze quickstart implementeert u een nieuw blockchain-lid en consortium in Azure Blockchain Service met Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell starten

Azure Cloud Shell is een gratis interactieve shell waarmee u de stappen in dit artikel kunt uitvoeren. In deze shell zijn algemene Azure-hulpprogramma's vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account.

Als u Cloud Shell wilt openen, selecteert u **Proberen** in de rechterbovenhoek van een codeblok. U Cloud Shell ook starten op [https://shell.azure.com/bash](https://shell.azure.com/bash)een apart browsertabblad door naar. Klik op **Kopiëren** om de codeblokken te kopiëren, plak deze in Cloud Shell en druk vervolgens op Enter om de code uit te voeren.

Als u de CLI liever lokaal installeert en gebruikt, vereist deze quickstart Azure CLI-versie 2.0.51 of hoger. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli)als u moet installeren of upgraden.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep maken met de opdracht [az group create](https://docs.microsoft.com/cli/azure/group). Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create \
                 --name myResourceGroup \
                 --location westus2
```

## <a name="create-a-blockchain-member"></a>Een blockchain-lid maken

Een Azure Blockchain Service-lid is een blockchain-knooppunt in een blockchain-netwerk voor een privéconsortium. Wanneer u een lid indient, u een consortiumnetwerk maken of lid worden. U hebt ten minste één lid nodig voor een consortiumnetwerk. Het aantal blockchain-leden dat deelnemers nodig hebben, is afhankelijk van uw scenario. Consortiumdeelnemers kunnen een of meer blockchain-leden hebben of leden delen met andere deelnemers. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.

Er zijn verschillende parameters en eigenschappen die u moet passeren. Vervang de voorbeeldparameters door uw waarden.

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
| **resourcegroep** | Naam van resourcegroep waar Azure Blockchain Service-resources worden gemaakt. Gebruik de resourcegroep die u in de vorige sectie hebt gemaakt.
| **Naam** | Een unieke naam die uw Azure Blockchain Service blockchain-lid identificeert. De naam wordt gebruikt voor het adres van het openbare eindpunt. Bijvoorbeeld `myblockchainmember.blockchain.azure.com`.
| **Locatie** | Azure-regio waar het blockchain-lid wordt gemaakt. Bijvoorbeeld `westus2`. Kies de locatie die zich het dichtst bij uw gebruikers of uw andere Azure-toepassingen bevindt.
| **wachtwoord** | Het wachtwoord voor het standaard transactieknooppunt van het lid. Gebruik het wachtwoord voor basisverificatie wanneer u verbinding maakt met het standaardtransactieknooppunt openbare eindpunt van het blockchain-lid.
| **Consortium** | Naam van het consortium om toe te treden of te maken. Zie [Azure Blockchain Service consortium](consortium.md)voor meer informatie over consortia.
| **consortiumAccountPassword** | Het wachtwoord van het consortiumaccount wordt ook wel het wachtwoord van het ledenaccount genoemd. Het wachtwoord van het ledenaccount wordt gebruikt om de privésleutel voor het Ethereum-account dat voor uw lid is gemaakt, te versleutelen. U gebruikt het accountaccount en het wachtwoord van het ledenaccount voor consortiumbeheer.
| **skuName** | Niveautype. Gebruik S0 voor Standard en B0 voor Basic. Gebruik de *basislaag* voor ontwikkeling, testen en proof of concepts. Gebruik de *standaardlaag* voor implementaties van productiekwaliteit. U moet ook de *standaardlaag* gebruiken als u Blockchain Data Manager gebruikt of een groot aantal privétransacties verzendt. Het wijzigen van de prijslaag tussen basis en standaard na het maken van leden wordt niet ondersteund.

Het duurt ongeveer 10 minuten om het blockchain-lid te maken en resources te ondersteunen.

## <a name="clean-up-resources"></a>Resources opschonen

U het blockchain-lid gebruiken dat u hebt gemaakt voor de volgende quickstart of zelfstudie. Wanneer dit niet meer nodig is, `myResourceGroup` u de resources verwijderen door de resourcegroep die u voor de quickstart hebt gemaakt, te verwijderen.

Voer de volgende opdracht uit om de resourcegroep en alle gerelateerde resources te verwijderen.

```azurecli-interactive
az group delete \
                 --name myResourceGroup \
                 --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Blockchain Service-lid en een nieuw consortium geïmplementeerd. Probeer de volgende quickstart om Azure Blockchain Development Kit voor Ethereum te gebruiken om te koppelen aan een Azure Blockchain Service-lid.

> [!div class="nextstepaction"]
> [Visual Studio-code gebruiken om verbinding te maken met Azure Blockchain-service](connect-vscode.md)
