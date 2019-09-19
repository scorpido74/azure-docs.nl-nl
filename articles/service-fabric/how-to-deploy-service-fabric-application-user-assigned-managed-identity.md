---
title: Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Service Fabric-toepassing implementeert met een door de gebruiker toegewezen beheerde identiteit
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 08/09/2019
ms.author: atsenthi
ms.openlocfilehash: 0cc1e51a4d5f9ad54866066a4247e1588da381a6
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037492"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity-preview"></a>Service Fabric toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit (preview)

Als u een Service Fabric toepassing wilt implementeren met beheerde identiteit, moet de toepassing worden geïmplementeerd via Azure Resource Manager, meestal met een Azure Resource Manager-sjabloon. Zie [toepassingen en services beheren als Azure Resource Manager bronnen](service-fabric-application-arm-resource.md)voor meer informatie over het implementeren van service Fabric-toepassing via Azure Resource Manager.

> [!NOTE] 
> 
> Toepassingen die niet zijn geïmplementeerd als een Azure-resource, **kunnen** geen beheerde identiteiten hebben. 
>
> Service Fabric toepassings implementatie met een beheerde identiteit wordt ondersteund met API `"2019-06-01-preview"`-versie. U kunt ook dezelfde API-versie gebruiken voor het toepassings type, de versie van het toepassings type en service bronnen.
>

## <a name="user-assigned-identity"></a>Door gebruiker toegewezen identiteit

Als u een toepassing met een door de gebruiker toegewezen identiteit wilt inschakelen, voegt u eerst de eigenschap **Identity** toe aan de toepassings resource met het type **userAssigned** en de door de gebruiker toegewezen identiteiten. Voeg vervolgens een sectie **managedIdentities** toe aan de sectie **Eigenschappen** van de **toepassings** resource die een lijst met beschrijvende namen bevat voor principalId-toewijzing voor elk van de door de gebruiker toegewezen identiteiten. Zie [een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)voor meer informatie over door de gebruiker toegewezen identiteiten.

### <a name="application-template"></a>Toepassingsjabloon

Als u de toepassing met de door de gebruiker toegewezen identiteit wilt inschakelen, moet u eerst de eigenschap **Identity** toevoegen aan de toepassings resource met het type **userAssigned** en de door de gebruiker toegewezen identiteiten, en vervolgens een **managedIdentities** -object toevoegen in dede sectie eigenschappen die een lijst met beschrijvende namen bevat voor principalId toewijzing voor elk van de door de gebruiker toegewezen identiteiten.

    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
      ],
      "identity": {
        "type" : "userAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
        }
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        },
        "managedIdentities": [
          {
            "name" : "[parameters('userAssignedIdentityName')]",
            "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
          }
        ]
      }
    }

In het bovenstaande voor beeld wordt de resource naam van de door de gebruiker toegewezen identiteit gebruikt als beschrijvende naam van de beheerde identiteit voor de toepassing. In de volgende voor beelden wordt ervan uitgegaan dat de daad werkelijke beschrijvende naam ' AdminUser ' is.

### <a name="application-package"></a>Toepassingspakket

1. Voor elke identiteit die is gedefinieerd `managedIdentities` in de sectie van de Azure Resource Manager sjabloon, `<ManagedIdentity>` voegt u een tag toe in het manifest van de toepassing, in de sectie **principals** . Het `Name` kenmerk moet overeenkomen met `name` de eigenschap die in `managedIdentities` de sectie is gedefinieerd.

    **ApplicationManifest. XML**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Voeg in de sectie **ServiceManifestImport** een **IdentityBindingPolicy** toe voor de service die gebruikmaakt van de beheerde identiteit. Dit beleid wijst de `AdminUser` identiteit toe aan een servicespecifieke identiteits naam die later aan het service manifest moet worden toegevoegd.

    **ApplicationManifest. XML**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Werk het service manifest bij om een **ManagedIdentity** toe te voegen aan de sectie **resources** met `ServiceIdentityRef` de naam `IdentityBindingPolicy` die overeenkomt met de in het manifest van de toepassing:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteit gebruiken in Service Fabric toepassings code](how-to-managed-identity-service-fabric-app-code.md)
* [Toegang tot Service Fabric toepassing verlenen aan andere Azure-resources](how-to-grant-access-other-resources.md)
