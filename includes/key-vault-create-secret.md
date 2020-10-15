---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: efa380ac243338a91354f948fdc77b2da8dd5406
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87512795"
---
We gaan een geheim maken met de naam **mySecret** en de waarde **Success!** . Een geheim kan een wachtwoord zijn, een SQL-verbindingsreeks of andere gegevens die u zowel veilig als beschikbaar wilt houden voor de toepassing. 

Als u een geheim wilt toevoegen aan uw nieuwe sleutelkluis, gebruikt u de opdracht [az keyvault secret set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) van Azure CLI.

```azurecli
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "mySecret" --value "Success!"
```