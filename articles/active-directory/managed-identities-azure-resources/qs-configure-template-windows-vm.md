---
title: Beheerde identiteiten configureren op een virtuele Azure-machine met behulp van sjabloon-Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure-VM met behulp van een Azure Resource Manager sjabloon.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e3b4373a121395a7be1fbf7a8db1394a01e563d
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84693564"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-templates"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure-VM met behulp van sjablonen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij elke service die ondersteuning biedt voor Azure AD-verificatie, zonder dat u referenties hebt in uw code.

In dit artikel wordt gebruikgemaakt van de Azure Resource Manager-implementatie sjabloon voor het uitvoeren van de volgende beheerde identiteiten voor Azure-bronnen bewerkingen op een Azure VM:

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met het gebruik van Azure Resource Manager-implementatie sjabloon, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als bij de Azure Portal en scripting bieden [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sjablonen de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-resource groep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

   - Met een [aangepaste sjabloon van Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)kunt u een volledig nieuwe sjabloon maken of deze baseren op een bestaande sjabloon common of [Quick](https://azure.microsoft.com/documentation/templates/)start.
   - Deze worden afgeleid van een bestaande resource groep door een sjabloon te exporteren uit [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md)of vanuit de [huidige status van de implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Een lokale [JSON-editor gebruiken (zoals VS code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met behulp van Power shell of cli.
   - Het Visual Studio [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) gebruiken om een sjabloon te maken en te implementeren.  

Ongeacht de optie die u kiest, is de sjabloon syntaxis hetzelfde tijdens de eerste implementatie en opnieuw implementeren. Het inschakelen van een door een systeem of gebruiker toegewezen beheerde identiteit op een nieuwe of bestaande virtuele machine gebeurt op dezelfde manier. Azure Resource Manager voert standaard een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) uit voor implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie schakelt u een door het systeem toegewezen beheerde identiteit in en uit met behulp van een Azure Resource Manager sjabloon.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele Azure-machine of een bestaande virtuele machine

Als u door het systeem toegewezen beheerde identiteit op een virtuele machine wilt inschakelen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Als u door het systeem toegewezen beheerde identiteit wilt inschakelen, laadt u de sjabloon in een editor, zoekt u de `Microsoft.Compute/virtualMachines` bron van belang binnen de `resources` sectie en voegt u de `"identity"` eigenschap toe op hetzelfde niveau als de `"type": "Microsoft.Compute/virtualMachines"` eigenschap. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Wanneer u klaar bent, moeten de volgende secties worden toegevoegd aan de `resource` sectie van uw sjabloon en deze moet er als volgt uitzien:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Een rol toewijzen aan de door het systeem toegewezen beheerde identiteit van de virtuele machine

Nadat u door het systeem toegewezen beheerde identiteit op uw virtuele machine hebt ingeschakeld, wilt u deze mogelijk een rol geven zoals **lezers** toegang tot de resource groep waarin deze is gemaakt.

Als u een rol wilt toewijzen aan de door het systeem toegewezen identiteit van uw VM, moet uw account beschikken over de toewijzing van de rol beheerder van de [gebruiker](/azure/role-based-access-control/built-in-roles#user-access-administrator) .

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Laad de sjabloon in een [Editor](#azure-resource-manager-templates) en voeg de volgende informatie toe om uw VM- **lezer** toegang te geven tot de resource groep waarin deze is gemaakt.  De structuur van uw sjabloon kan variëren, afhankelijk van de editor en het implementatie model dat u kiest.

   Onder de `parameters` sectie voegt u het volgende toe:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Onder de `variables` sectie voegt u het volgende toe:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Onder de `resources` sectie voegt u het volgende toe:

    ```JSON
    {
        "apiVersion": "2017-09-01",
        "type": "Microsoft.Authorization/roleAssignments",
        "name": "[parameters('rbacGuid')]",
        "properties": {
            "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
            "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
            "scope": "[resourceGroup().id]"
        },
         "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
        ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een Azure-VM

Als u een door het systeem toegewezen beheerde identiteit van een virtuele machine wilt verwijderen, moet uw account de roltoewijzing van de [virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben.  Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Laad de sjabloon in een [Editor](#azure-resource-manager-templates) en zoek de `Microsoft.Compute/virtualMachines` bron van belang in de `resources` sectie. Als u een VM hebt die alleen door het systeem toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteits type te wijzigen in `None` .  

   **Micro soft. Compute/informatie API-versie 2018-06-01**

   Als uw virtuele machine zowel door het systeem en de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u `SystemAssigned` het identiteits type en blijft u `UserAssigned` samen met de `userAssignedIdentities` woordenlijst waarden.

   **Micro soft. Compute/informatie API-versie 2018-06-01**

   Als uw `apiVersion` `2017-12-01` virtuele machine en de door de gebruiker toegewezen beheerde identiteiten zijn, verwijdert u het `SystemAssigned` identiteits type en blijft u `UserAssigned` samen met de `identityIds` matrix van de door de gebruiker toegewezen beheerde identiteiten.  

In het volgende voor beeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een VM zonder door de gebruiker toegewezen beheerde identiteiten:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie wijst u een door de gebruiker toegewezen beheerde identiteit toe aan een Azure-VM met behulp van Azure Resource Manager sjabloon.

> [!Note]
> Zie [een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)om een door de gebruiker toegewezen beheerde identiteit te maken met behulp van een Azure Resource Manager sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een Azure VM

Als u een door de gebruiker toegewezen identiteit aan een VM wilt toewijzen, moet uw account de roltoewijzingen van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en de rollen voor de [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Voeg onder het `resources` -element de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit aan uw virtuele machine toe te wijzen.  Zorg ervoor dat u vervangt door `<USERASSIGNEDIDENTITY>` de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Micro soft. Compute/informatie API-versie 2018-06-01**

   Als dat het geval `apiVersion` is `2018-06-01` , worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de `userAssignedIdentities` woordenlijst indeling en `<USERASSIGNEDIDENTITYNAME>` moet de waarde worden opgeslagen in een variabele die is gedefinieerd in de `variables` sectie van uw sjabloon.

   ```JSON
    {
        "apiVersion": "2018-06-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": {
                "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
            }
        }
    }
   ```

   **Micro soft. Compute/informatie API-versie 2017-12-01**

   Als dat het geval `apiVersion` is `2017-12-01` , worden uw door de gebruiker toegewezen beheerde identiteiten in de `identityIds` matrix opgeslagen en `<USERASSIGNEDIDENTITYNAME>` moet de waarde worden opgeslagen in een variabele die is gedefinieerd in de `variables` sectie van uw sjabloon.

   ```JSON
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```

3. Wanneer u klaar bent, moeten de volgende secties worden toegevoegd aan de `resource` sectie van uw sjabloon en deze moet er als volgt uitzien:

   **Micro soft. Compute/informatie API-versie 2018-06-01**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]   
   ```
   **Micro soft. Compute/informatie API-versie 2017-12-01**

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-VM

Als u een door de gebruiker toegewezen identiteit van een virtuele machine wilt verwijderen, moet uw account de roltoewijzing van de [virtuele machine](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) hebben. Er zijn geen extra Azure AD-Directory roltoewijzingen vereist.

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de virtuele machine bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Laad de sjabloon in een [Editor](#azure-resource-manager-templates) en zoek de `Microsoft.Compute/virtualMachines` bron van belang in de `resources` sectie. Als u een VM hebt die alleen door de gebruiker toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteits type te wijzigen in `None` .

   In het volgende voor beeld ziet u hoe u alle door de gebruiker toegewezen beheerde identiteiten verwijdert uit een VM zonder door het systeem toegewezen beheerde identiteiten:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Micro soft. Compute/informatie API-versie 2018-06-01**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele machine wilt verwijderen, verwijdert u deze uit de `useraAssignedIdentities` woorden lijst.

   Als u een door het systeem toegewezen beheerde identiteit hebt, moet u deze in de `type` waarde onder de waarde laten staan `identity` .

   **Micro soft. Compute/informatie API-versie 2017-12-01**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een virtuele machine wilt verwijderen, verwijdert u deze uit de `identityIds` matrix.

   Als u een door het systeem toegewezen beheerde identiteit hebt, moet u deze in de `type` waarde onder de waarde laten staan `identity` .

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md).
