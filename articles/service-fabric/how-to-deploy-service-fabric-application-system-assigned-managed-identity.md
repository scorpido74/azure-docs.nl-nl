---
title: Azure Service Fabric-een Azure Service Fabric-toepassing implementeren met een door het systeem toegewezen beheerde identiteit | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een door het systeem toegewezen beheerde identiteit toewijst aan een Azure Service Fabric-toepassing
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: article
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: cf971d71c2566d91bc5a2490d47521725c62b17d
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973411"
---
# <a name="deploy-service-fabric-application-with-system-assigned-managed-identity-preview"></a>Service Fabric-toepassing implementeren met door het systeem toegewezen beheerde identiteit (preview)

Als u toegang wilt krijgen tot de functie Managed Identity voor Azure Service Fabric-toepassingen, moet u eerst de service beheerde identiteits token inschakelen op het cluster. Deze service is verantwoordelijk voor de verificatie van Service Fabric toepassingen met behulp van hun beheerde identiteiten en voor het verkrijgen van toegangs tokens in hun naam. Zodra de service is ingeschakeld, kunt u deze weer geven in Service Fabric Explorer onder het gedeelte **systeem** in het linkerdeel venster, dat wordt uitgevoerd onder de naam **Fabric:/System/ManagedIdentityTokenService** naast andere systeem services.

> [!NOTE] 
> De implementatie van Service Fabric toepassingen met beheerde identiteiten wordt ondersteund vanaf de API-versie `"2019-06-01-preview"`. U kunt ook dezelfde API-versie gebruiken voor het toepassings type, de versie van het toepassings type en service bronnen. De mini maal ondersteunde Service Fabric runtime is 6,5 CU2. In additoin moet de ontwikkel-en pakket omgeving ook de SF .NET SDK op CU2 of hoger hebben

## <a name="system-assigned-managed-identity"></a>Door het systeem toegewezen beheerde identiteit

### <a name="application-template"></a>Toepassingsjabloon

Als u een toepassing met een door het systeem toegewezen beheerde identiteit wilt inschakelen, voegt u de eigenschap **Identity** toe aan de toepassings resource, met het type **systemAssigned** , zoals wordt weer gegeven in het volgende voor beeld:

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
Met deze eigenschap declareert u (in Azure Resource Manager, respectievelijk de beheerde identiteits-en Service Fabric resource providers, die deze resource een impliciete (@no__t 0) beheerde identiteit heeft.

### <a name="application-and-service-package"></a>Toepassings-en service pakket

1. Werk het toepassings manifest bij om een **ManagedIdentity** -element toe te voegen aan de sectie **principals** met een enkel item, zoals hieronder wordt weer gegeven:

    **ApplicationManifest. XML**

    ```xml
    <Principals>
      <ManagedIdentities>
        <ManagedIdentity Name="SystemAssigned" />
      </ManagedIdentities>
    </Principals>
    ```
    Hiermee wijst u de identiteit die aan de toepassing is toegewezen als een resource toe aan een beschrijvende naam, voor verdere toewijzing aan de services die deel uitmaakt van de toepassing. 

2. Voeg in de sectie **ServiceManifestImport** die overeenkomt met de service waaraan de beheerde identiteit wordt toegewezen, een **IdentityBindingPolicy** -element toe, zoals hieronder wordt aangegeven:

    **ApplicationManifest. XML**

      ```xml
        <ServiceManifestImport>
          <Policies>
            <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="SystemAssigned" />
          </Policies>
        </ServiceManifestImport>
      ```

    Dit element wijst de identiteit van de toepassing toe aan de service. zonder deze toewijzing heeft de service geen toegang meer tot de identiteit van de toepassing. In het bovenstaande code fragment is de identiteit van de `SystemAssigned` (een gereserveerd tref woord) toegewezen aan de definitie van de service onder de beschrijvende naam `WebAdmin`.

3. Werk het service manifest bij om een **ManagedIdentity** -element toe te voegen aan de sectie **resources** met de naam die overeenkomt met de waarde van de instelling `ServiceIdentityRef` van de definitie van de `IdentityBindingPolicy` in het manifest van de toepassing:

    **ServiceManifest. XML**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```
    Dit is de equivalente toewijzing van een identiteit aan een service zoals hierboven wordt beschreven, maar vanuit het perspectief van de service definitie. Er wordt naar de identiteit verwezen met de beschrijvende naam (`WebAdmin`), zoals aangegeven in het manifest van de toepassing.

## <a name="next-steps"></a>Volgende stappen
* [Ondersteuning voor beheerde identiteiten](./concepts-managed-identity.md) bekijken in azure service Fabric
* [Een nieuwe implementeren](./configure-new-azure-service-fabric-enable-managed-identity.md) Azure Service Fabric-cluster met ondersteuning voor beheerde identiteiten 
* [Beheerde identiteit inschakelen](./configure-existing-cluster-enable-managed-identity-token-service.md) in een bestaand Azure service Fabric-cluster
* Gebruikmaken van de [beheerde identiteit van](./how-to-managed-identity-service-fabric-app-code.md) een service Fabric toepassing uit de bron code
* [Een Azure Service Fabric-toepassing implementeren met een door de gebruiker toegewezen beheerde identiteit](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Een Azure Service Fabric-toepassing toegang verlenen tot andere Azure-resources](./how-to-grant-access-other-resources.md)
