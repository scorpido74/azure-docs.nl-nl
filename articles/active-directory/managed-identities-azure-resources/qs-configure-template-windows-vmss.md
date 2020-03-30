---
title: Sjabloon configureren om beheerde identiteiten te gebruiken op virtuele machineschaalsets - Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een virtuele machineschaalset met behulp van een Azure Resource Manager-sjabloon.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425558"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure-virtuele machineschaal met behulp van een sjabloon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. U deze identiteit gebruiken om te verifiëren voor elke service die Azure AD-verificatie ondersteunt, zonder referenties in uw code te hebben.

In dit artikel vindt u informatie over het uitvoeren van de volgende beheerde identiteiten voor Azure-resourcesbewerkingen op een Azure-set voor virtuele machines, met behulp van de implementatiesjabloon Azure Resource Manager:
- De door het systeem toegewezen beheerde identiteit in- en uitschakelen op een azure-schaalset voor virtuele machines
- Een door de gebruiker toegewezen beheerde identiteit toevoegen en verwijderen op een azure-schaalset voor virtuele machines

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-bronnen, raadpleegt u het [overzichtsgedeelte](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#how-does-the-managed-identities-for-azure-resources-work)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheerbewerkingen in dit artikel wilt uitvoeren, heeft uw account de volgende op Azure-rollen gebaseerde toegangsbeheertoewijzingen nodig:

    > [!NOTE]
    > Er zijn geen extra Azure AD-maproltoewijzingen vereist.

    - [Virtuele machinebijdrager](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) om een virtuele machineschaalset te maken en systeem- en/of door de gebruiker toegewezen beheerde identiteit in te schakelen en te verwijderen uit een virtuele machineschaalset.
    - [Functie Beheerde identiteitsbijdrager](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - [Functie Managed Identity Operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) om een door de gebruiker toegewezen beheerde identiteit toe te wijzen en te verwijderen van en naar een virtuele machineschaalset.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als bij de Azure-portal en scripting bieden [Azure Resource Manager-sjablonen](../../azure-resource-manager/management/overview.md) de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-brongroep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op portalen gebaseerd, waaronder:

   - Met behulp van een [aangepaste sjabloon van de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template), waarmee u een sjabloon vanaf nul maken of deze baseren op een bestaande algemene of [quickstartsjabloon.](https://azure.microsoft.com/documentation/templates/)
   - Afgeleid van een bestaande resourcegroep, door een sjabloon te exporteren vanuit [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md)of uit de huidige status van de [implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Een lokale [JSON-editor gebruiken (zoals VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met PowerShell of CLI.
   - Het project Visual Studio [Azure Resource Group gebruiken](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) om een sjabloon te maken en te implementeren.  

Ongeacht de optie die u kiest, is de syntaxis van de sjabloon hetzelfde tijdens de eerste implementatie en herschikking. Beheerde identiteiten voor Azure-resources inschakelen op een nieuwe of bestaande VM gebeurt op dezelfde manier. Azure Resource Manager werkt standaard ook een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) voor implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie schakelt u de door het systeem toegewezen beheerde identiteit in en uit met behulp van een Azure Resource Manager-sjabloon.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Systeemtoegewezen beheerde identiteit inschakelen tijdens het maken van een virtuele machineschaalset of een bestaande virtuele machineschaalset

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat.
2. Als u de door het systeem toegewezen beheerde identiteit `Microsoft.Compute/virtualMachinesScaleSets` wilt inschakelen, laadt u `identity` de sjabloon in een `"type": "Microsoft.Compute/virtualMachinesScaleSets"` editor, zoekt u de resource van belang in de sectie resources en voegt u de eigenschap toe op hetzelfde niveau als de eigenschap. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> U optioneel de beheerde identiteiten voor azure-resources inrichten `extensionProfile` met de extensie voor virtuele machineschaaldoor deze op te geven in het element van de sjabloon. Deze stap is optioneel omdat u het IMDS-identiteitseindpunt (Azure Instance Metadata Service) gebruiken om ook tokens op te halen.  Zie [Migreren van VM-extensie naar Azure IMDS voor verificatie voor](howto-migrate-vm-extension.md)meer informatie.


4. Wanneer u klaar bent, moeten de volgende secties worden toegevoegd aan het resourcegedeelte van uw sjabloon en moeten deze op het volgende lijken:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een Azure-schaalset voor virtuele machines

Als u een virtuele machineschaalset hebt die geen beheerde identiteit met systeemtoegewezen identiteit meer nodig heeft:

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat.

2. Laad de sjabloon [editor](#azure-resource-manager-templates) in een `Microsoft.Compute/virtualMachineScaleSets` editor en `resources` zoek de bron van belang in de sectie. Als u een VM hebt die alleen een door het systeem toegewezen `None`beheerde identiteit heeft, u deze uitschakelen door het identiteitstype te wijzigen in.

   **Microsoft.Compute/virtualMachineScaleSets API versie 2018-06-01**

   Als uw apiVersion dit is `2018-06-01` en uw VM zowel door `SystemAssigned` het systeem als `UserAssigned` door de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u het identiteitstype en houdt u samen met de woordenboekwaarden van userAssignedIdentities.

   **Microsoft.Compute/virtualMachineScaleSets API versie 2018-06-01**

   Als uw `2017-12-01` apiVersion is en uw virtuele machineschaalset zowel door `SystemAssigned` het systeem als `UserAssigned` door de `identityIds` gebruiker toegewezen beheerde identiteiten heeft, verwijdert u het identiteitstype en houdt u de array van de door de gebruiker toegewezen beheerde identiteiten bij.



   In het volgende voorbeeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een schaalset voor virtuele machines zonder door de gebruiker toegewezen beheerde identiteiten:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Door een gebruiker toegewezen beheerde identiteit

In deze sectie wijst u een door de gebruiker toegewezen beheerde identiteit toe aan een virtuele machineschaalset met azure resource manager-sjabloon.

> [!Note]
> Zie Een door de [gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)met behulp van een Azure Resource Manager-sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een schaalset voor virtuele machines

1. Voeg `resources` onder het element de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit toe te wijzen aan uw virtuele machineschaalset.  Zorg ervoor `<USERASSIGNEDIDENTITY>` dat u de naam vervangt van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Microsoft.Compute/virtualMachineScaleSets API versie 2018-06-01**

   Als uw apiVersion `2018-06-01`dit is, worden uw door `userAssignedIdentities` de gebruiker `<USERASSIGNEDIDENTITYNAME>` toegewezen beheerde identiteiten opgeslagen `variables` in de woordenboekindeling en moet de waarde worden opgeslagen in een variabele die is gedefinieerd in de sectie van uw sjabloon.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API versie 2017-12-01**

   Als `apiVersion` uw `2017-12-01` of eerder is, worden uw door de `identityIds` gebruiker `<USERASSIGNEDIDENTITYNAME>` toegewezen beheerde identiteiten opgeslagen in de array en moet de waarde worden opgeslagen in een variabele die is gedefinieerd in het gedeelte variabelen van uw sjabloon.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> U optioneel de beheerde identiteiten voor azure-resources inrichten `extensionProfile` met de extensie voor virtuele machineschaaldoor deze op te geven in het element van de sjabloon. Deze stap is optioneel omdat u het IMDS-identiteitseindpunt (Azure Instance Metadata Service) gebruiken om ook tokens op te halen.  Zie [Migreren van VM-extensie naar Azure IMDS voor verificatie voor](howto-migrate-vm-extension.md)meer informatie.

3. Wanneer u klaar bent, moet uw sjabloon er als volgt uitzien:

   **Microsoft.Compute/virtualMachineScaleSets API versie 2018-06-01**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API versie 2017-12-01**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Beheerde identiteit van de gebruiker verwijderen uit een Azure-schaalset voor virtuele machines

Als u een virtuele machineschaalset hebt die geen door de gebruiker toegewezen beheerde identiteit meer nodig heeft:

1. Of u zich nu lokaal bij Azure aanmeldt of via de Azure-portal, gebruik een account dat is gekoppeld aan het Azure-abonnement dat de grootteset voor virtuele machines bevat.

2. Laad de sjabloon [editor](#azure-resource-manager-templates) in een `Microsoft.Compute/virtualMachineScaleSets` editor en `resources` zoek de bron van belang in de sectie. Als u een virtuele machineschaalset hebt die alleen een door de gebruiker toegewezen `None`beheerde identiteit heeft, u deze uitschakelen door het identiteitstype te wijzigen in.

   In het volgende voorbeeld ziet u hoe u alle door de gebruiker toegewezen beheerde identiteiten verwijdert uit een vm zonder door het systeem toegewezen beheerde identiteiten:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API versie 2018-06-01**

   Als u een beheerde identiteit met één gebruiker wilt verwijderen `userAssignedIdentities` uit een virtuele machineschaalset, verwijdert u deze uit het woordenboek.

   Als u een door het systeem toegewezen identiteit `type` hebt, `identity` houdt u deze in de waarde onder de waarde.

   **Microsoft.Compute/virtualMachineScaleSets API versie 2017-12-01**

   Als u een door de gebruiker toegewezen beheerde identiteit wilt `identityIds` verwijderen uit een virtuele machineschaalset, verwijdert u deze uit de array.

   Als u een door het systeem toegewezen beheerde `type` identiteit `identity` hebt, houdt u deze in de waarde onder de waarde.

## <a name="next-steps"></a>Volgende stappen

- [Beheerde identiteiten voor het overzicht van Azure-bronnen](overview.md).
