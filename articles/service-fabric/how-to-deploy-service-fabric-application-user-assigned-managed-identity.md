---
title: App implementeren met een door de gebruiker toegewezen beheerde identiteit
description: In dit artikel ziet u hoe u servicefabric-toepassing implementeert met een door de gebruiker toegewezen beheerde identiteit
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 9aef81db7a455b72c83cf96898a0c228f1c382fd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415631"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Servicefabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit

Als u een Service Fabric-toepassing met beheerde identiteit wilt implementeren, moet de toepassing worden geïmplementeerd via Azure Resource Manager, meestal met een Azure Resource Manager-sjabloon. Zie Toepassingen en services beheren als Azure Resource [Manager-bronnen](service-fabric-application-arm-resource.md)voor meer informatie over het implementeren van Service Fabric-toepassingen via Azure Resource Manager.

> [!NOTE] 
> 
> Toepassingen die niet als Azure-bron zijn geïmplementeerd, **kunnen geen** beheerde identiteiten hebben. 
>
> Service Fabric-toepassingsimplementatie met Managed Identity `"2019-06-01-preview"`wordt ondersteund met API-versie. U dezelfde API-versie ook gebruiken voor toepassingstype, toepassingstypeversie en servicebronnen.
>

## <a name="user-assigned-identity"></a>Door de gebruiker toegewezen identiteit

Als u toepassing met door de gebruiker toegewezen identiteit wilt inschakelen, voegt u eerst de **identiteitseigenschap** toe aan de toepassingsbron met de **gebruiker toegewezen** tekst en de door de gebruiker toegewezen identiteiten. Voeg vervolgens een sectie **managedIdentities** toe in de sectie **eigenschappen** voor de **toepassingsbron** die een lijst met vriendelijke naam bevat aan principalId-toewijzing voor elk van de door de gebruiker toegewezen identiteiten. Zie [Een door de gebruiker toegewezen beheerde identiteit maken, aanbieden of verwijderen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell)voor meer informatie over door gebruikers toegewezen identiteiten .

### <a name="application-template"></a>Toepassingsjabloon

Als u toepassing met door gebruiker toegewezen identiteit wilt inschakelen, voegt u eerst **identiteitseigenschap** toe aan de toepassingsbron met door **de gebruiker Toegewezen** en de door de gebruiker toegewezen identiteiten, en voegt u vervolgens een object **managedIdentities** toe in de sectie **eigenschappen** dat een lijst met vriendelijke naam bevat aan de toewijzing van principalId voor elk van de door de gebruiker toegewezen identiteiten.

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

In het bovenstaande voorbeeld wordt de bronnaam van de door de gebruiker toegewezen identiteit gebruikt als de vriendelijke naam van de beheerde identiteit voor de toepassing. In de volgende voorbeelden wordt ervan uitgegaan dat de werkelijke vriendelijke naam "AdminUser" is.

### <a name="application-package"></a>Toepassingspakket

1. Voeg voor elke identiteit `managedIdentities` die is gedefinieerd in de `<ManagedIdentity>` sectie in de sjabloon Azure Resource Manager een tag toe in het toepassingsmanifest onder sectie **Principals.** Het `Name` kenmerk moet `name` overeenkomen met de `managedIdentities` eigenschap die in de sectie is gedefinieerd.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. Voeg in de sectie **ServiceManifestImport** een **identiteitsbindingsbeleid** toe voor de service die de beheerde identiteit gebruikt. Dit beleid `AdminUser` brengt de identiteit in kaart met een servicespecifieke identiteitsnaam die later in het servicemanifest moet worden toegevoegd.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Werk het servicemanifest bij om een **ManagedIdentity** toe `ServiceIdentityRef` te `IdentityBindingPolicy` voegen in de sectie **Resources** met de naam die overeenkomt met het in de toepassingsmanifest:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Volgende stappen

* [Beheerde identiteit gebruiken in de toepassingscode van Service Fabric](how-to-managed-identity-service-fabric-app-code.md)
* [Service Fabric-toepassingstoegang verlenen tot andere Azure-bronnen](how-to-grant-access-other-resources.md)
