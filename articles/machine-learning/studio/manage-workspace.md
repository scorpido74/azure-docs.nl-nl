---
title: Werkruimten beheren
titleSuffix: ML Studio (classic) - Azure
description: Toegang tot Azure Machine Learning Studio (klassieke) werk ruimten beheren en Machine Learning API-webservices implementeren en beheren
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: f4a153a69f3475418873fbac04dea7d932c14373
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73839463"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio-werk ruimte (klassieke) beheren

> [!NOTE]
> Voor informatie over het beheren van webservices in de Machine Learning Web Services-portal, Zie [een webservice beheren met de Azure machine learning Web Services-portal](manage-new-webservice.md).
> 
> 

U kunt Machine Learning Studio (klassieke) werk ruimten in de Azure Portal beheren.



## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Een studio-werk ruimte (klassieke) beheren in de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) met behulp van een Azure-abonnement beheerders account.
2. In het zoekvak boven aan de pagina voert u ' machine learning Studio (klassieke) werk ruimten ' in en selecteert u vervolgens **machine learning Studio (klassieke) werk ruimten**.
3. Klik op de werk ruimte die u wilt beheren.

Naast de standaard informatie en beschik bare opties voor resource management kunt u het volgende doen:

- **Eigenschappen** weer geven: deze pagina bevat informatie over de werk ruimte en de resource, en u kunt het abonnement en de resource groep wijzigen waarmee deze werk ruimte is verbonden.
- **Opslag sleutels** voor opnieuw synchroniseren: de werk ruimte behoudt de sleutels voor het opslag account. Als het opslag account sleutels wijzigt, kunt u op **Resync-sleutels** klikken om de sleutels te synchroniseren met de werk ruimte.

Gebruik de Machine Learning Web Services-portal om de webservices te beheren die zijn gekoppeld aan deze studio-werk ruimte (klassieke). Zie [Manage a web service using the Azure machine learning Web Services portal](manage-new-webservice.md) (Engelstalig) voor uitgebreide informatie.

> [!NOTE]
> Als u nieuwe webservices wilt implementeren of beheren, moet u een rol Inzender of beheerder toewijzen aan het abonnement waarop de webservice is geïmplementeerd. Als u een andere gebruiker uitnodigt voor een machine learning studio-werk ruimte, moet u deze toewijzen aan de rol Inzender of beheerder voor het abonnement voordat ze webservices kunnen implementeren of beheren. 
> 
>Zie [toegang beheren met RBAC en de Azure Portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van toegangs machtigingen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [implementeren van machine learning met Azure Resource Manager-sjablonen](deploy-with-resource-manager-template.md). 
