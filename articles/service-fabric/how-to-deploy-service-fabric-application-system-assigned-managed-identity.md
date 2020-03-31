---
title: Een Service Fabric-app implementeren met mi met systeemtoegewezen MI
description: In dit artikel ziet u hoe u een door het systeem toegewezen beheerde identiteit toewijst aan een Azure Service Fabric-toepassing
ms.topic: article
ms.date: 07/25/2019
ms.openlocfilehash: d5a14722363d642957904f9c7c699d3cf1d66c0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614822"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Servicefabric-toepassing implementeren met door het systeem toegewezen beheerde identiteit (voorbeeld)

Als u toegang wilt krijgen tot de beheerde identiteitsfunctie voor Azure Service Fabric-toepassingen, moet u eerst de Managed Identity Token Service in het cluster inschakelen. Deze service is verantwoordelijk voor de verificatie van Service Fabric-toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangstokens namens hen. Zodra de service is ingeschakeld, u deze zien in Service Fabric Explorer onder de sectie **Systeem** in het linkerdeelvenster, uitgevoerd onder de naam **fabric:/System/ManagedIdentityTokenService** naast andere systeemservices.

> [!NOTE] 
> Implementatie van Service Fabric-toepassingen met beheerde identiteiten wordt ondersteund vanaf de API-versie. `"2019-06-01-preview"` U dezelfde API-versie ook gebruiken voor toepassingstype, toepassingstypeversie en servicebronnen. De minimaal ondersteunde Service Fabric-runtime is 6,5 CU2. In additoin moet de build/package-omgeving ook de SF .Net SDK op CU2 of hoger hebben

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

### <a name="application-template"></a>Toepassingsjabloon

Als u toepassing wilt inschakelen met een door het systeem toegewezen beheerde identiteit, voegt u de **eigenschap identiteit** toe aan de toepassingsbron, waarbij het **typesysteem Toegewezen** wordt zoals in het onderstaande voorbeeld wordt weergegeven:

```json
    {
      "apiVersion": "2019-06-01-preview",
      "type": "Microsoft.ServiceFabric/clusters/applications",
      "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]"
      ],
      "identity": {
        "type" : "systemAssigned"
      },
      "properties": {
        "typeName": "[parameters('applicationTypeName')]",
        "typeVersion": "[parameters('applicationTypeVersion')]",
        "parameters": {
        }
      }
    }
```
Deze eigenschap verklaart (aan Azure Resource Manager en de Managed Identity and Service Fabric Resource`system assigned`Providers, respectievelijk, dat deze bron een impliciete ( ) beheerde identiteit heeft.

### <a name="application-and-service-package"></a>Toepassings- en servicepakket

1. Werk het toepassingsmanifest bij om een **ManagedIdentity-element** toe te voegen aan de sectie **Principals,** met één vermelding zoals hieronder wordt weergegeven:

    **ApplicationManifest.xml**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Hiermee wordt de identiteit die aan de toepassing is toegewezen als bron in kaart gebracht voor een vriendschappelijke naam, voor verdere toewijzing aan de services die de toepassing omvatten. 

2. Voeg in de sectie **ServiceManifestImport** die overeenkomt met de service waaraan de beheerde identiteit wordt toegewezen, een element **IdentityBindingPolicy** toe, zoals hieronder wordt aangegeven:

    **ApplicationManifest.xml**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Dit element wijst de identiteit van de toepassing toe aan de service; zonder deze toewijzing heeft de service geen toegang tot de identiteit van de toepassing. In het bovenstaande `SystemAssigned` fragment wordt de identiteit (die een gereserveerd zoekwoord is) `WebAdmin`toegewezen aan de definitie van de service onder de vriendelijke naam .

3. Werk het servicemanifest bij om een **ManagedIdentity-element** toe te `ServiceIdentityRef` voegen aan `IdentityBindingPolicy` de sectie **Resources** met de naam die overeenkomt met de waarde van de instelling van de definitie in het toepassingsmanifest:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Dit is de gelijkwaardige toewijzing van een identiteit aan een service zoals hierboven beschreven, maar vanuit het perspectief van de servicedefinitie. De identiteit wordt hier aangeduid met`WebAdmin`de vriendelijke naam ( ), zoals aangegeven in het aanvraagmanifest.

## <a name="next-steps"></a>Volgende stappen
* [Beheerde identiteitsondersteuning controleren](./concepts-managed-identity.md) in Azure Service Fabric
* [Een nieuwe implementeren](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-cluster met beheerde identiteitsondersteuning 
* [Beheerde identiteit inschakelen](./configure-existing-cluster-enable-managed-identity-token-service.md) in een bestaand Azure Service Fabric-cluster
* De beheerde identiteit van een Service Fabric-toepassing [gebruiken vanuit de broncode](./how-to-managed-identity-service-fabric-app-code.md)
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-bronnen](./how-to-grant-access-other-resources.md)
