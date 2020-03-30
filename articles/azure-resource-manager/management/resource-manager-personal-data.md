---
title: Persoonsgegevens
description: Meer informatie over het beheren van persoonlijke gegevens die zijn gekoppeld aan Azure Resource Manager-bewerkingen.
ms.topic: conceptual
ms.date: 05/14/2018
ms.openlocfilehash: 22cfc1b6096980f3d10db404a1c4e02f2de355d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485258"
---
# <a name="manage-personal-data-associated-with-azure-resource-manager"></a>Persoonlijke gegevens beheren die zijn gekoppeld aan Azure Resource Manager

Verwijder alle persoonlijke gegevens die u hebt opgegeven in implementaties, brongroepen of tags om gevoelige informatie niet bloot te leggen. Azure Resource Manager biedt bewerkingen waarmee u persoonlijke gegevens beheren die u mogelijk hebt opgegeven in implementaties, brongroepen of tags.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="delete-personal-data-in-deployment-history"></a>Persoonlijke gegevens verwijderen in implementatiegeschiedenis

Voor implementaties behoudt Resource Manager parameterwaarden en statusberichten in de implementatiegeschiedenis. Deze waarden blijven bestaan totdat u de implementatie uit de geschiedenis verwijdert. Als u wilt zien of u persoonsgegevens in deze waarden hebt verstrekt, vermeldt u de implementaties. Als u persoonlijke gegevens vindt, verwijdert u de implementaties uit de geschiedenis.

Als u implementaties in de geschiedenis wilt **weergeven,** gebruikt u het als:

* [Lijst per resourcegroep](/rest/api/resources/deployments/listbyresourcegroup)
* [Get-azResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment)
* [implementatielijst van az-groepen](/cli/azure/group/deployment#az-group-deployment-list)

Als u **implementaties** uit de geschiedenis wilt verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/deployments/delete)
* [Toewijzing azresourcegroep verwijderen](/powershell/module/az.resources/Remove-AzResourceGroupDeployment)
* [implementatie van AZ-groep verwijderen](/cli/azure/group/deployment#az-group-deployment-delete)

## <a name="delete-personal-data-in-resource-group-names"></a>Persoonlijke gegevens verwijderen in namen van resourcegroepen

De naam van de resourcegroep blijft bestaan totdat u de brongroep verwijdert. Als u wilt zien of u persoonlijke gegevens in de namen hebt verstrekt, geeft u de brongroepen aan. Als u persoonlijke gegevens vindt, [verplaatst u de resources](move-resource-group-and-subscription.md) naar een nieuwe brongroep en verwijdert u de brongroep met persoonlijke gegevens in de naam.

Als u **resourcegroepen wilt weergeven,** gebruikt u:

* [Lijst](/rest/api/resources/resourcegroups/list)
* [Get-AzResourceGroup](/powershell/module/az.resources/Get-AzResourceGroup)
* [az-groepslijst](/cli/azure/group#az-group-list)

Als u **resourcegroepen wilt**verwijderen, gebruikt u:

* [Verwijderen](/rest/api/resources/resourcegroups/delete)
* [Remove-AzResourceGroup](/powershell/module/az.resources/Remove-AzResourceGroup)
* [az group delete](/cli/azure/group#az-group-delete)

## <a name="delete-personal-data-in-tags"></a>Persoonlijke gegevens in tags verwijderen

Namen en waarden van tags blijven bestaan totdat u de tag verwijdert of wijzigt. Als u wilt zien of u persoonlijke gegevens in de tags hebt verstrekt, vermeldt u de tags. Als u persoonlijke gegevens vindt, verwijdert u de tags.

Gebruik het als u **tags wilt weergeven:**

* [Lijst](/rest/api/resources/tags/list)
* [Get-Aztag](/powershell/module/az.resources/Get-AzTag)
* [az-taglijst](/cli/azure/tag#az-tag-list)

Gebruik **het**als u tags wilt verwijderen:

* [Verwijderen](/rest/api/resources/tags/delete)
* [Verwijder-Aztag](/powershell/module/az.resources/Remove-AzTag)
* [az-tag verwijderen](/cli/azure/tag#az-tag-delete)

## <a name="next-steps"></a>Volgende stappen
* Zie Wat is Resource beheer voor een overzicht van Azure Resource [Manager?](overview.md)