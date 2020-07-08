---
title: Een door de gebruiker toegewezen beheerde identiteit maken & verwijderen met behulp van Azure Resource Manager
description: Stapsgewijze instructies voor het maken en verwijderen van door de gebruiker toegewezen beheerde identiteiten met behulp van Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 466b0853648fab078af89f01a9aea157205e81d1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85608480"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven en verwijderen met behulp van Azure Resource Manager


Beheerde identiteiten voor Azure-resources bieden Azure-Services met een beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel maakt u een door de gebruiker toegewezen beheerde identiteit met behulp van een Azure Resource Manager.

Het is niet mogelijk om een door de gebruiker toegewezen beheerde identiteit weer te geven en te verwijderen met behulp van een Azure Resource Manager sjabloon.  Raadpleeg de volgende artikelen voor het maken en vermelden van een door de gebruiker toegewezen beheerde identiteit:

- [Door de gebruiker toegewezen beheerde identiteit weer geven](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Door de gebruiker toegewezen beheerde identiteit verwijderen](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="template-creation-and-editing"></a>Sjabloon maken en bewerken

Net als bij de Azure Portal en scripting bieden Azure Resource Manager sjablonen de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-resource groep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

- Met een [aangepaste sjabloon van Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)kunt u een volledig nieuwe sjabloon maken of deze baseren op een bestaande sjabloon common of [Quick](https://azure.microsoft.com/documentation/templates/)start.
- Deze worden afgeleid van een bestaande resource groep door een sjabloon te exporteren uit [de oorspronkelijke implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)of vanuit de [huidige status van de implementatie](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Een lokale [JSON-editor gebruiken (zoals VS code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met behulp van Power shell of cli.
- Het Visual Studio [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) gebruiken om een sjabloon te maken en te implementeren. 

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) .

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, gebruikt u de volgende sjabloon. Vervang de `<USER ASSIGNED IDENTITY NAME>` waarde door uw eigen waarden:

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

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure-VM met behulp van een Azure Resource Manager-sjabloon raadpleegt u [beheerde identiteiten voor Azure-resources configureren op een Azure-VM met behulp van een](qs-configure-template-windows-vm.md)sjabloon.


 
