---
title: Werkruimten beheren
titleSuffix: ML Studio (classic) - Azure
description: Beheer de toegang tot Azure Machine Learning Studio (klassieke) werkruimten en implementeer en beheer Machine Learning API-webservices
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: 68c9ca4c7b5f30d6f62d333014f4098fe7826c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217953"
---
# <a name="manage-an-azure-machine-learning-studio-classic-workspace"></a>Een Azure Machine Learning Studio -werkruimte (klassieke) werkruimte beheren

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

> [!NOTE]
> Zie [Een webservice beheren met de Azure Machine Learning Web Services-portal](manage-new-webservice.md)voor informatie over het beheren van webservices in de Portal Machine Learning Web Services.
> 
> 

U Machine Learning Studio (klassieke) werkruimten beheren in de Azure-portal.



## <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

Ga als lid van de studio (klassieke) werkruimte in de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/) met een Azure-abonnementsbeheerdersaccount.
2. Typ in het zoekvak boven aan de pagina 'machine learning Studio (klassieke) werkruimten' en selecteer **vervolgens Machine Learning Studio (klassieke) werkruimten.**
3. Klik op de werkruimte die u wilt beheren.

Naast de standaard informatie en opties voor resourcemanagement u:

- **Weergaveeigenschappen** : op deze pagina worden de werkruimte- en brongegevens weergegeven en u de abonnements- en brongroep wijzigen waarmee deze werkruimte is verbonden.
- **Opslagsleutels opnieuw synchroniseren** - De werkruimte onderhoudt sleutels van het opslagaccount. Als het opslagaccount de sleutels wijzigt, u op **Toetsen opnieuw synchroniseren** klikken om de toetsen met de werkruimte te synchroniseren.

Als u de webservices wilt beheren die zijn gekoppeld aan deze Studio-werkruimte (klassieke) werkruimte, gebruikt u de Portal Machine Learning Web Services. Zie [Een webservice beheren met de Azure Machine Learning Web Services-portal](manage-new-webservice.md) voor volledige informatie.

> [!NOTE]
> Als u Nieuwe webservices wilt implementeren of beheren, moet u een bijdrager of beheerder toegewezen krijgen aan het abonnement waarvoor de webservice is geïmplementeerd. Als u een andere gebruiker uitnodigt voor een werkruimte voor machine learning Studio (klassieke) werkruimte, moet u deze toewijzen aan een bijdrager of beheerder rol in het abonnement voordat deze webservices kunnen implementeren of beheren. 
> 
>Zie [Toegang beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md)voor meer informatie over het instellen van toegangsmachtigingen.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [het implementeren van Machine Learning met Azure Resource Manager-sjablonen](deploy-with-resource-manager-template.md). 
