---
title: Een hoek-app maken met de API van Azure Cosmos DB voor MongoDB (Part1)
description: Deel 4 van de serie zelfstudies voor het maken van een MongoDB-app met Angular en Node op Azure Cosmos DB, waarbij gebruik wordt gemaakt van dezelfde API's als voor MongoDB
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/06/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: d5ed706a0577423f0a95b8eb5f7a563da74a0e7f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75441174"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-cosmos-account"></a>Een Angular-app maken met de API voor MongoDB van Azure Cosmos DB - Een Cosmos-account maken

In deze meerdelige zelfstudie leert u hoe u een app maakt die is geschreven in Node.js met Express en Angular, en hoe u deze app verbindt met uw [Cosmos-account dat is geconfigureerd met de API van Cosmos DB voor MongoDB](mongodb-introduction.md).

Deel 4 van de zelfstudie bouwt voort op [deel 3](tutorial-develop-mongodb-nodejs-part3.md) en beschrijft de volgende taken:

> [!div class="checklist"]
> * Een Azure-resourcegroep maken met de Azure CLI
> * Een Cosmos-account maken met Azure CLI

## <a name="video-walkthrough"></a>Video-overzicht

> [!VIDEO https://www.youtube.com/embed/hfUM-AbOh94]

## <a name="prerequisites"></a>Vereisten

Voltooi de stappen in [deel 3](tutorial-develop-mongodb-nodejs-part3.md) van de zelfstudie voordat u aan dit deel begint. 

In dit gedeelte van de zelfstudie kunt u ofwel de Azure Cloud Shell gebruiken (in uw webbrowser) of de lokaal geïnstalleerde [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Log in to Azure](../../includes/login-to-azure.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]

> [!TIP]
> Aan de hand van deze zelfstudie wordt u stapsgewijs begeleid bij het maken van de toepassing. Als u het voltooide project wilt downloaden, kunt u de voltooide app vinden in de [angular-cosmosdb-opslagplaats](https://github.com/Azure-Samples/angular-cosmosdb) op GitHub.

## <a name="create-an-azure-cosmos-db-account"></a>Maak een Azure Cosmos DB-account

Maak een Azure Cosmos DB-account met [`az cosmosdb create`](/cli/azure/cosmosdb#az-cosmosdb-create) de opdracht.

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

* Gebruik voor `<cosmosdb-name>` uw eigen unieke Azure Cosmos DB-accountnaam. De naam moet uniek zijn binnen alle Azure Cosmos DB-accountnamen in Azure.
* De instelling `--kind MongoDB` maakt MongoDB-clientverbindingen mogelijk in Azure Cosmos DB.

Het duurt mogelijk enkele minuten voor de opdracht is voltooid. Wanneer de opdracht is voltooid geeft het terminalvenster informatie over de nieuwe database. 

Zodra het Azure Cosmos DB-account is gemaakt:
1. Open een nieuw browser venster en ga naar[https://portal.azure.com](https://portal.azure.com)
1. Klik op het logo van Azure Cosmos DB ![Azure Cosmos DB-pictogram in Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-icon.png) in de linkerbalk en toont alle de Azure Cosmos DB’s die u hebt.
1. Klik op het Azure Cosmos DB-account dat u zojuist hebt gemaakt, selecteer het tabblad **Overzicht** en scrol omlaag naar de kaart waarop de database zich bevindt. 

    ![Nieuw Azure Cosmos DB-account in de Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-angular-portal.png)

4. Scrol omlaag op de navigatiebalk aan de linkerkant en klik op het tabblad **Gegevens globaal repliceren**. Er wordt een kaart weergegeven waarop u de verschillende gebieden ziet waarin u kunt repliceren. U kunt bijvoorbeeld klikken op Australië - zuidoost of Australië - oost en uw gegevens repliceren naar Australië. Meer informatie over globale replicatie vindt u in [Gegevens globaal distribueren met Azure Cosmos DB](distribute-data-globally.md). Voorlopig behouden we dit exemplaar. Wanneer we willen repliceren, weten we hoe dit moet.

    ![Nieuw Azure Cosmos DB-account in de Azure Portal](./media/tutorial-develop-mongodb-nodejs-part4/azure-cosmos-db-replicate-portal.png)

## <a name="next-steps"></a>Volgende stappen

In dit deel van de zelfstudie hebt u het volgende gedaan:

> [!div class="checklist"]
> * U hebt een Azure-resourcegroep gemaakt met de Azure CLI
> * U hebt een Azure Cosmos DB-account gemaakt met de Azure CLI

U kunt doorgaan met het volgende deel van de zelfstudie om Azure Cosmos DB te koppelen aan uw app met Mongoose.

> [!div class="nextstepaction"]
> [Mongoose gebruiken om verbinding te maken met Azure Cosmos DB](tutorial-develop-mongodb-nodejs-part5.md)
