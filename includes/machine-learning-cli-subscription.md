---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: df4735699f71256598bfd418a505ae8d3e5b816c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296887"
---
Nadat u zich hebt aanmeldt, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnementsgegevens `isDefault: true` waarmee het momenteel geactiveerde abonnement voor Azure CLI-opdrachten zijn. Dit abonnement moet hetzelfde abonnement zijn dat uw Azure Machine Learning-werkruimte bevat. U de abonnements-id vinden in de [Azure-portal](https://portal.azure.com) door naar de overzichtspagina voor uw werkruimte te gaan. U de SDK ook gebruiken om de abonnements-ID van het werkruimteobject op te halen. Bijvoorbeeld `Workspace.from_config().subscription_id`.
    
Als u een ander abonnement wilt selecteren, gebruikt u de opdracht [az-accountset](https://docs.microsoft.com/cli/azure/account#az-account-set) met de abonnements-ID om naar over te schakelen. Zie [Meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)voor meer informatie over abonnementsselectie.