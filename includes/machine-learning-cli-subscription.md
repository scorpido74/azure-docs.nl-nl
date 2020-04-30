---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81616818"
---
> [!TIP]
> Nadat u zich hebt aangemeld, ziet u een lijst met abonnementen die zijn gekoppeld aan uw Azure-account. De abonnements gegevens met `isDefault: true` is het momenteel geactiveerde abonnement voor Azure cli-opdrachten. Dit abonnement moet gelijk zijn aan de naam die uw Azure Machine Learning-werk ruimte bevat. U vindt de abonnements-ID van de [Azure Portal](https://portal.azure.com) door de pagina overzicht voor uw werk ruimte te bezoeken. U kunt ook de SDK gebruiken om de abonnements-ID van het werkruimte object op te halen. Bijvoorbeeld `Workspace.from_config().subscription_id`.
> 
> Als u een ander abonnement wilt selecteren `az account set -s <subscription name or ID>` , gebruikt u de opdracht en geeft u de naam van het abonnement of de id op om over te scha kelen. Zie [meerdere Azure-abonnementen gebruiken](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest)voor meer informatie over het selecteren van abonnementen.