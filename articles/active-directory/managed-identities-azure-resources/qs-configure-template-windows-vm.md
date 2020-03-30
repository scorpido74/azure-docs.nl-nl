---
title: Beheerde identiteiten configureren op Azure VM met sjabloon - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een Azure VM met behulp van een Azure Resource Manager-sjabloon.
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
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972149"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure-vm met behulp van een sjablonen

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben.

In dit artikel vindt u met de azure resource manager-implementatiesjabloon informatie over het uitvoeren van de volgende beheerde identiteiten voor Azure-resources-bewerkingen op een Azure VM:

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met het gebruik van azure resource manager-implementatiesjabloon, raadpleegt u de [overzichtssectie](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als bij de Azure-portal en scripting bieden [Azure Resource Manager-sjablonen](../../azure-resource-manager/management/overview.md) de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-brongroep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op portalen gebaseerd, waaronder:

   - Met behulp van een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), waarmee u een sjabloon vanaf nul maken of deze baseren op een bestaande algemene of [quickstartsjabloon.](https://azure.microsoft.com/documentation/templates/)
   - Afgeleid van een bestaande resourcegroep, door een sjabloon te exporteren vanuit [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md)of uit de huidige status van de [implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Een lokale [JSON-editor gebruiken (zoals VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met PowerShell of CLI.
   - Het project Visual Studio [Azure Resource Group gebruiken](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) om een sjabloon te maken en te implementeren.  

Ongeacht de optie die u kiest, is de syntaxis van de sjabloon hetzelfde tijdens de eerste implementatie en herschikking. Het inschakelen van een door de gebruiker toegewezen beheerde identiteit op een nieuwe of bestaande virtuele machine gebeurt op dezelfde manier. Azure Resource Manager werkt standaard ook een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) voor implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie schakelt u een beheerde identiteit met een Azure Resource Manager-sjabloon in en uit.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Beheerde identiteit met systeemtoegewezen status inschakelen tijdens het maken van een Azure VM of op een bestaande VM

Als u beheerde identiteit met systeemtoewijzing op een virtuele apparaat wilt inschakelen, heeft uw account de roltoewijzing [voor virtuele machineinzendernodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

2. Als u beheerde identiteit met systeemtoegewezen wilt inschakelen, laadt u de sjabloon in een editor, zoekt u de `Microsoft.Compute/virtualMachines` bron van belang binnen de `resources` sectie en voegt u de `"identity"` eigenschap toe op hetzelfde niveau als de `"type": "Microsoft.Compute/virtualMachines"` eigenschap. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Wanneer u klaar bent, moeten de `resource` volgende secties worden toegevoegd aan het gedeelte van uw sjabloon en moet het volgende lijken:

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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Een rol toewijzen die de door de VM toegewezen beheerde identiteit heeft toegewezen

Nadat u beheerde identiteit met systeemtoegewezen op uw VM hebt ingeschakeld, u deze een rol toekennen, zoals **Reader-toegang** tot de resourcegroep waarin deze is gemaakt.

Als u een rol wilt toewijzen aan de door uw VM toegewezen identiteit, heeft uw account de roltoewijzing [voor gebruikerstoegangsbeheerders](/azure/role-based-access-control/built-in-roles#user-access-administrator) nodig.

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

2. Laad de sjabloon in een [editor](#azure-resource-manager-templates) en voeg de volgende informatie toe om uw VM **Reader** toegang te geven tot de brongroep waarin deze is gemaakt.  Uw sjabloonstructuur kan variëren afhankelijk van de editor en het implementatiemodel dat u kiest.

   Voeg `parameters` onder de sectie het volgende toe:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    Voeg `variables` onder de sectie het volgende toe:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Voeg `resources` onder de sectie het volgende toe:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen van een Azure-vm

Als u de door het systeem toegewezen beheerde identiteit uit een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender nodig.](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)  Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

2. Laad de sjabloon [editor](#azure-resource-manager-templates) in een `Microsoft.Compute/virtualMachines` editor en `resources` zoek de bron van belang in de sectie. Als u een VM hebt die alleen een door het systeem toegewezen `None`beheerde identiteit heeft, u deze uitschakelen door het identiteitstype te wijzigen in.  

   **Microsoft.Compute/virtualMachines API versie 2018-06-01**

   Als uw vm zowel door het systeem als `SystemAssigned` door de gebruiker `UserAssigned` toegewezen `userAssignedIdentities` beheerde identiteiten heeft, verwijdert u het identiteitstype en houdt u de woordenlijstwaarden bij.

   **Microsoft.Compute/virtualMachines API versie 2018-06-01**

   Als `apiVersion` uw `2017-12-01` systeem en uw VM zowel door het `SystemAssigned` systeem als door `UserAssigned` de gebruiker `identityIds` toegewezen beheerde identiteiten heeft, verwijdert u het identiteitstype en houdt u de array van de door de gebruiker toegewezen beheerde identiteiten bij.  

In het volgende voorbeeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een VM zonder door de gebruiker toegewezen beheerde identiteiten:

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

In deze sectie wijst u een door de gebruiker toegewezen beheerde identiteit toe aan een Azure VM met Azure Resource Manager-sjabloon.

> [!Note]
> Zie Een door de [gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)met behulp van een Azure Resource Manager-sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een Azure-vm

Als u een door de gebruiker toegewezen identiteit aan een vm wilt toewijzen, heeft uw account de roltoewijzingen [Virtual Machine Contributor](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) en Managed Identity [Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Voeg `resources` onder het element de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit toe te wijzen aan uw vm.  Zorg ervoor `<USERASSIGNEDIDENTITY>` dat u de naam vervangt van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Microsoft.Compute/virtualMachines API versie 2018-06-01**

   Als `apiVersion` dat `2018-06-01`zo is, worden uw door `userAssignedIdentities` de gebruiker `<USERASSIGNEDIDENTITYNAME>` toegewezen beheerde identiteiten opgeslagen in `variables` de woordenboekindeling en moet de waarde worden opgeslagen in een variabele die is gedefinieerd in het gedeelte van uw sjabloon.

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

   **Microsoft.Compute/virtualMachines API versie 2017-12-01**

   Als `apiVersion` dat `2017-12-01`zo is, worden uw door `identityIds` de gebruiker `<USERASSIGNEDIDENTITYNAME>` toegewezen beheerde identiteiten opgeslagen `variables` in de array en moet de waarde worden opgeslagen in een variabele die is gedefinieerd in het gedeelte van uw sjabloon.

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

3. Wanneer u klaar bent, moeten de `resource` volgende secties worden toegevoegd aan het gedeelte van uw sjabloon en moet het volgende lijken:

   **Microsoft.Compute/virtualMachines API versie 2018-06-01**    

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
   **Microsoft.Compute/virtualMachines API versie 2017-12-01**

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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een Azure-vm

Als u een door de gebruiker toegewezen identiteit uit een vm wilt verwijderen, heeft uw account de roltoewijzing [voor virtuele machineinzender](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) nodig. Er zijn geen extra Azure AD-maproltoewijzingen vereist.

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de VM bevat.

2. Laad de sjabloon [editor](#azure-resource-manager-templates) in een `Microsoft.Compute/virtualMachines` editor en `resources` zoek de bron van belang in de sectie. Als u een VM hebt die alleen door de gebruiker toegewezen beheerde `None`identiteit heeft, u deze uitschakelen door het identiteitstype te wijzigen in.

   In het volgende voorbeeld ziet u hoe u alle door de gebruiker toegewezen beheerde identiteiten verwijdert uit een vm zonder door het systeem toegewezen beheerde identiteiten:

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

   **Microsoft.Compute/virtualMachines API versie 2018-06-01**

   Als u één door de gebruiker toegewezen beheerde `useraAssignedIdentities` identiteit uit een vm wilt verwijderen, verwijdert u deze uit het woordenboek.

   Als u een door het systeem toegewezen beheerde `type` identiteit `identity` hebt, houdt u deze in de waarde onder de waarde.

   **Microsoft.Compute/virtualMachines API versie 2017-12-01**

   Als u een door de gebruiker toegewezen beheerde `identityIds` identiteit uit een vm wilt verwijderen, verwijdert u deze uit de array.

   Als u een door het systeem toegewezen beheerde `type` identiteit `identity` hebt, houdt u deze in de waarde onder de waarde.

## <a name="next-steps"></a>Volgende stappen

- [Beheerde identiteiten voor het overzicht van Azure-bronnen](overview.md).
