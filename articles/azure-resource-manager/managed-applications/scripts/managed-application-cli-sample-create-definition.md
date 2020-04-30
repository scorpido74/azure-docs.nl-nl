---
title: Definitie van beheerde toepassing maken-Azure CLI
description: Voorziet in een Azure CLI-voorbeeld script voor het maken van een definitie van een beheerde toepassing in het abonnement.
author: tfitzmac
ms.devlang: azurecli
ms.topic: sample
ms.date: 10/25/2017
ms.author: tomfitz
ms.openlocfilehash: f4d5a0036ba44f7e0054db7ce820b91b0de629b8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75650189"
---
# <a name="create-a-managed-application-definition-with-azure-cli"></a>Een definitie van een beheerde toepassing maken met Azure CLI

Met dit script implementeert u de definitie van een beheerde toepassing naar een servicecatalogus. 


[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Voorbeeldscript

[!code-azurecli[main](../../../../cli_scripts/managed-applications/create-definition/create-definition.sh "Create definition")]


## <a name="script-explanation"></a>Uitleg van het script

In dit script wordt de volgende opdracht gebruikt om de definitie van de beheerde toepassing te maken. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [az managedapp definition create](https://docs.microsoft.com/cli/azure/managedapp/definition#az-managedapp-definition-create) | Hiermee maakt u de definitie van een beheerde toepassing. Geef het pakket op dat de vereiste bestanden bevat. |


## <a name="next-steps"></a>Volgende stappen

* Zie [Overzicht van door Azure beheerde toepassingen](../overview.md) voor algemene informatie over beheerde toepassingen.
* Zie de [documentatie van Azure CLI](https://docs.microsoft.com/cli/azure) voor meer informatie over de Azure CLI.
