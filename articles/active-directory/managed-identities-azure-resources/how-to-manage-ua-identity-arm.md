---
title: Een door de gebruiker toegewezen beheerde identiteit maken & verwijderen met Azure Resource Manager
description: Stap voor stap instructies voor het maken en verwijderen van door de gebruiker toegewezen beheerde identiteiten met Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253375"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weergeven en verwijderen met Azure Resource Manager


Beheerde identiteiten voor Azure-resources bieden Azure-services een beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor services die Azure AD-verificatie ondersteunen, zonder dat u referenties in uw code nodig hebt. 

In dit artikel maakt u een door de gebruiker toegewezen beheerde identiteit met behulp van een Azure Resource Manager.

Het is niet mogelijk om een door de gebruiker toegewezen beheerde identiteit aan te bieden en te verwijderen met behulp van een Azure Resource Manager-sjabloon.  Zie de volgende artikelen om een door de gebruiker toegewezen beheerde identiteit te maken en weer te geven:

- [Door de gebruiker toegewezen beheerde identiteit weergeven](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Beheerde identiteit die door de gebruiker is toegewezen](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="template-creation-and-editing"></a>Sjabloonmaken en bewerken

Net als bij de Azure-portal en scripting bieden Azure Resource Manager-sjablonen de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-brongroep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op portalen gebaseerd, waaronder:

- Met behulp van een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), waarmee u een sjabloon vanaf nul maken of deze baseren op een bestaande algemene of [QuickStart-sjabloon.](https://azure.microsoft.com/documentation/templates/)
- Afgeleid van een bestaande resourcegroep, door een sjabloon te exporteren vanuit [de oorspronkelijke implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)of uit de huidige status van de [implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Een lokale [JSON-editor gebruiken (zoals VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met PowerShell of CLI.
- Het project Visual Studio [Azure Resource Group gebruiken](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) om een sjabloon te maken en te implementeren. 

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, heeft uw account de roltoewijzing [Voor beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) nodig.

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, gebruikt u de volgende sjabloon. Vervang `<USER ASSIGNED IDENTITY NAME>` de waarde door uw eigen waarden:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Volgende stappen

Zie [Beheerde identiteiten configureren voor Azure-resources op een Azure VM met behulp van een](qs-configure-template-windows-vm.md)Azure Resource Manager-sjabloon voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-vm.


 
