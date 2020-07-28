---
title: Sjabloon configureren voor het gebruik van beheerde identiteiten in virtuele-machine schaal sets-Azure AD
description: Stapsgewijze instructies voor het configureren van beheerde identiteiten voor Azure-resources op een schaalset voor virtuele machines met behulp van een Azure Resource Manager sjabloon.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5afb11a275275ac49178b30929d7896c8a082591
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85609007"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Beheerde identiteiten configureren voor Azure-resources op een Azure virtual machine-schaal met behulp van een sjabloon

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U kunt deze identiteit gebruiken voor verificatie bij alle services die Microsoft Azure AD-verificatie ondersteunen, zonder dat u aanmeldingsgegevens in uw code hoeft te hebben.

In dit artikel leert u hoe u de volgende beheerde identiteiten voor Azure-bronnen bewerkingen kunt uitvoeren op een virtuele machine schaalset van Azure met Azure Resource Manager-implementatie sjabloon:
- De door het systeem toegewezen beheerde identiteit inschakelen en uitschakelen op een virtuele-machine schaalset van Azure
- Een door de gebruiker toegewezen beheerde identiteit toevoegen aan en verwijderen uit een Azure virtual machine-schaalset

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de [sectie Overzicht](overview.md). **Controleer het [verschil tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)**.
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- Als u de beheer bewerkingen in dit artikel wilt uitvoeren, moet uw account beschikken over de volgende toegangs beheer toewijzingen van Azure op basis van rollen:

    > [!NOTE]
    > Er zijn geen aanvullende Azure AD Directory-roltoewijzingen vereist.

    - [Inzender voor virtuele machines](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) voor het maken van een schaalset voor virtuele machines en het inschakelen en verwijderen van door het systeem en/of gebruiker toegewezen beheerde identiteit uit een schaalset voor virtuele machines.
    - Rol van [beheerde identiteit Inzender](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) om een door de gebruiker toegewezen beheerde identiteit te maken.
    - De rol [beheerde identiteits operator](/azure/role-based-access-control/built-in-roles#managed-identity-operator) voor het toewijzen en verwijderen van een door de gebruiker toegewezen beheerde identiteit van en aan een virtuele-machine schaalset.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Net als bij de Azure Portal en scripting bieden [Azure Resource Manager](../../azure-resource-manager/management/overview.md) sjablonen de mogelijkheid om nieuwe of gewijzigde resources te implementeren die zijn gedefinieerd door een Azure-resource groep. Er zijn verschillende opties beschikbaar voor het bewerken en implementeren van sjablonen, zowel lokaal als op basis van een portal, waaronder:

   - Met een [aangepaste sjabloon van Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)kunt u een volledig nieuwe sjabloon maken of deze baseren op een bestaande sjabloon common of [Quick](https://azure.microsoft.com/documentation/templates/)start.
   - Deze worden afgeleid van een bestaande resource groep door een sjabloon te exporteren uit [de oorspronkelijke implementatie](../../azure-resource-manager/templates/export-template-portal.md)of vanuit de [huidige status van de implementatie](../../azure-resource-manager/templates/export-template-portal.md).
   - Een lokale [JSON-editor gebruiken (zoals VS code)](../../azure-resource-manager/resource-manager-create-first-template.md)en vervolgens uploaden en implementeren met behulp van Power shell of cli.
   - Het Visual Studio [Azure Resource Group-project](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) gebruiken om een sjabloon te maken en te implementeren.  

Ongeacht de optie die u kiest, is de sjabloon syntaxis hetzelfde tijdens de eerste implementatie en opnieuw implementeren. Het inschakelen van beheerde identiteiten voor Azure-resources op een nieuwe of bestaande virtuele machine gebeurt op dezelfde manier. Azure Resource Manager voert standaard een [incrementele update](../../azure-resource-manager/templates/deployment-modes.md) uit voor implementaties.

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

In deze sectie schakelt u de door het systeem toegewezen beheerde identiteit in en uit met behulp van een Azure Resource Manager sjabloon.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Door het systeem toegewezen beheerde identiteit inschakelen tijdens het maken van een schaalset voor virtuele machines of een bestaande virtuele-machine schaalset

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.
2. Als u de door het systeem toegewezen beheerde identiteit wilt inschakelen, laadt u de sjabloon in een editor, zoekt u de `Microsoft.Compute/virtualMachinesScaleSets` bron van belang in de sectie resources en voegt u de `identity` eigenschap toe op hetzelfde niveau als de `"type": "Microsoft.Compute/virtualMachinesScaleSets"` eigenschap. Gebruik de volgende syntaxis:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> U kunt eventueel de beheerde identiteiten voor de virtuele-machine schaalset-extensie van Azure-resources inrichten door deze op te geven in het- `extensionProfile` element van de sjabloon. Deze stap is optioneel, omdat u het identiteits eindpunt van Azure Instance Metadata Service (IMDS) kunt gebruiken om tokens ook op te halen.  Zie [migreren van VM-extensie naar Azure IMDS voor authenticatie](howto-migrate-vm-extension.md)voor meer informatie.


4. Wanneer u klaar bent, moeten de volgende secties worden toegevoegd aan de resource sectie van uw sjabloon en moet er als volgt uitzien:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Een door het systeem toegewezen beheerde identiteit uitschakelen vanuit een schaalset voor virtuele Azure-machines

Als u een schaalset voor virtuele machines hebt waarvoor geen door het systeem toegewezen beheerde identiteit meer nodig is, doet u het volgende:

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Laad de sjabloon in een [Editor](#azure-resource-manager-templates) en zoek de `Microsoft.Compute/virtualMachineScaleSets` bron van belang in de `resources` sectie. Als u een VM hebt die alleen door het systeem toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteits type te wijzigen in `None` .

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2018-06-01**

   Als uw apiVersion `2018-06-01` en uw virtuele machine zowel door het systeem als de gebruiker toegewezen beheerde identiteiten hebben, verwijdert u `SystemAssigned` het identiteits type en blijft u `UserAssigned` samen met de userAssignedIdentities-woordenlijst waarden.

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2018-06-01**

   Als uw apiVersion `2017-12-01` en de virtuele-machine schaalset zowel door het systeem als de gebruiker toegewezen beheerde identiteiten heeft, verwijdert u `SystemAssigned` het identiteits type en blijft u `UserAssigned` samen met de `identityIds` matrix van de door de gebruiker toegewezen beheerde identiteiten.



   In het volgende voor beeld ziet u hoe u een door het systeem toegewezen beheerde identiteit verwijdert uit een schaalset voor virtuele machines zonder door de gebruiker toegewezen beheerde identiteiten:

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

In deze sectie wijst u een door de gebruiker toegewezen beheerde identiteit toe aan een schaalset voor virtuele machines met behulp van Azure Resource Manager sjabloon.

> [!Note]
> Zie [een door de gebruiker toegewezen beheerde identiteit maken](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)om een door de gebruiker toegewezen beheerde identiteit te maken met behulp van een Azure Resource Manager sjabloon.

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Een door de gebruiker toegewezen beheerde identiteit toewijzen aan een schaalset voor virtuele machines

1. Voeg onder het `resources` -element de volgende vermelding toe om een door de gebruiker toegewezen beheerde identiteit toe te wijzen aan de schaalset van de virtuele machine.  Zorg ervoor dat u vervangt door `<USERASSIGNEDIDENTITY>` de naam van de door de gebruiker toegewezen beheerde identiteit die u hebt gemaakt.

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2018-06-01**

   Als uw apiVersion is `2018-06-01` , worden uw door de gebruiker toegewezen beheerde identiteiten opgeslagen in de `userAssignedIdentities` woordenlijst indeling en `<USERASSIGNEDIDENTITYNAME>` moet de waarde worden opgeslagen in een variabele die is gedefinieerd in de `variables` sectie van uw sjabloon.

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

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2017-12-01**

   Als het `apiVersion` al `2017-12-01` of niet eerder is, worden uw door de gebruiker toegewezen beheerde identiteiten in de `identityIds` matrix opgeslagen en `<USERASSIGNEDIDENTITYNAME>` moet de waarde worden opgeslagen in een variabele die is gedefinieerd in de sectie Varia bles van uw sjabloon.

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
> U kunt eventueel de beheerde identiteiten voor de virtuele-machine schaalset-extensie van Azure-resources inrichten door deze op te geven in het- `extensionProfile` element van de sjabloon. Deze stap is optioneel, omdat u het identiteits eindpunt van Azure Instance Metadata Service (IMDS) kunt gebruiken om tokens ook op te halen.  Zie [migreren van VM-extensie naar Azure IMDS voor authenticatie](howto-migrate-vm-extension.md)voor meer informatie.

3. Wanneer u klaar bent, moet uw sjabloon er ongeveer als volgt uitzien:

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2018-06-01**   

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

   **Micro soft. Compute/informatie API-versie 2017-12-01**

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
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Door de gebruiker toegewezen beheerde identiteit verwijderen uit een schaalset voor virtuele Azure-machines

Als u een schaalset voor virtuele machines hebt waarvoor geen door de gebruiker toegewezen beheerde identiteit meer nodig is:

1. Gebruik een account dat is gekoppeld aan het Azure-abonnement dat de schaalset voor virtuele machines bevat, of u zich lokaal of via de Azure Portal aanmeldt bij Azure.

2. Laad de sjabloon in een [Editor](#azure-resource-manager-templates) en zoek de `Microsoft.Compute/virtualMachineScaleSets` bron van belang in de `resources` sectie. Als u een schaalset voor virtuele machines hebt die alleen door de gebruiker toegewezen beheerde identiteit heeft, kunt u deze uitschakelen door het identiteits type te wijzigen in `None` .

   In het volgende voor beeld ziet u hoe u alle door de gebruiker toegewezen beheerde identiteiten verwijdert uit een VM zonder door het systeem toegewezen beheerde identiteiten:

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

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2018-06-01**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een schaalset voor virtuele machines wilt verwijderen, verwijdert u deze uit de `userAssignedIdentities` woorden lijst.

   Als u een door het systeem toegewezen identiteit hebt, moet u deze in de `type` waarde onder de waarde laten staan `identity` .

   **Micro soft. Compute/virtualMachineScaleSets API-versie 2017-12-01**

   Als u één door de gebruiker toegewezen beheerde identiteit uit een schaalset voor virtuele machines wilt verwijderen, verwijdert u deze uit de `identityIds` matrix.

   Als u een door het systeem toegewezen beheerde identiteit hebt, moet u deze in de `type` waarde onder de waarde laten staan `identity` .

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van beheerde identiteiten voor Azure-resources](overview.md).
