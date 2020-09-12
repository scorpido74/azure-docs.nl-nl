---
title: Azure-Service Fabric-opslagplaats referenties voor container configureren
description: Opslagplaats referenties configureren voor het downloaden van installatie kopieën uit het container register
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 142ede6fcc59063d83854712a966a90c7472923b
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421421"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Opslagplaats referenties voor uw toepassing configureren om container installatie kopieën te downloaden

Configureer container register verificatie door toe `RepositoryCredentials` te voegen aan de `ContainerHostPolicies` sectie van het toepassings manifest. Voeg het account en het wacht woord voor uw container register toe (*myregistry.azurecr.io* in het onderstaande voor beeld), zodat de service de container installatie kopie kan downloaden uit de opslag plaats.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Het is raadzaam om het wacht woord voor de opslag plaats te versleutelen met behulp van een coderings certificaat dat is geïmplementeerd op alle knoop punten van het cluster. Wanneer Service Fabric het servicepakket naar het cluster implementeert, wordt het versleutelingscertificaat gebruikt om de versleutelde tekst te ontsleutelen. De cmdlet Invoke-ServiceFabricEncryptText wordt gebruikt om de gecodeerde tekst voor het wachtwoord te maken, die wordt toegevoegd aan het bestand ApplicationManifest.xml.
Zie [geheim beheer](service-fabric-application-secret-management.md) voor meer informatie over certificaten en de semantiek van versleuteling.

## <a name="configure-cluster-wide-credentials"></a>Referenties voor het hele cluster configureren

Met Service Fabric kunt u referenties voor het hele cluster configureren die als standaard referenties voor de opslag plaats kunnen worden gebruikt door toepassingen.

Deze functie kan worden in-of uitgeschakeld door het `UseDefaultRepositoryCredentials` kenmerk toe te voegen aan `ContainerHostPolicies` in ApplicationManifest.xml met een `true` of- `false` waarde.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Service Fabric worden vervolgens de standaard opslagplaats referenties gebruikt, die kunnen worden opgegeven in de ClusterManifest onder de `Hosting` sectie.  Als `UseDefaultRepositoryCredentials` dat `true` het geval is, service Fabric leest de volgende waarden uit de ClusterManifest:

* DefaultContainerRepositoryAccountName (teken reeks)
* DefaultContainerRepositoryPassword (teken reeks)
* IsDefaultContainerRepositoryPasswordEncrypted (BOOL)
* DefaultContainerRepositoryPasswordType (teken reeks)

Hier volgt een voor beeld van wat kan worden toegevoegd `Hosting` in de sectie in de ClusterManifestTemplate.jsin het bestand. De `Hosting` sectie kan worden toegevoegd tijdens het maken van een cluster of later in een configuratie-upgrade. Zie [azure service Fabric-cluster instellingen wijzigen](service-fabric-cluster-fabric-settings.md) en [Azure service Fabric-toepassings geheimen beheren](service-fabric-application-secret-management.md) voor meer informatie

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          },
          {
        "name": "DefaultMSIEndpointForTokenAuthentication",
        "value": "URI"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Tokens gebruiken als register referenties

Service Fabric ondersteunt het gebruik van tokens als referenties voor het downloaden van installatie kopieën voor uw containers.  Deze functie maakt gebruik van de *beheerde identiteit* van de onderliggende schaalset voor virtuele machines om te verifiëren bij het REGI ster, waardoor het niet nodig is om gebruikers referenties te beheren.  Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.  Als u deze functie wilt gebruiken, moeten de volgende stappen worden uitgevoerd:

1. Zorg ervoor dat door het *systeem toegewezen beheerde identiteit* is ingeschakeld voor de virtuele machine.

    ![Azure Portal: identiteits optie voor schaal sets voor virtuele machines maken](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Ken machtigingen toe aan de schaalset voor virtuele machines voor het ophalen/lezen van installatie kopieën uit het REGI ster. Voeg op de Blade Access Control (IAM) van uw Azure Container Registry in de Azure Portal een *roltoewijzing* voor uw virtuele machine toe:

    ![VM-Principal toevoegen aan ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Wijzig vervolgens het toepassings manifest. `ContainerHostPolicies`Voeg het kenmerk toe aan de sectie `‘UseTokenAuthenticationCredentials=”true”` .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > Als de vlag `UseDefaultRepositoryCredentials` is ingesteld op True `UseTokenAuthenticationCredentials` , treedt er een fout op tijdens de implementatie.

### <a name="using-token-credentials-outside-of-azure-global-cloud"></a>Token referenties gebruiken buiten Azure Global Cloud

Wanneer u register referenties op basis van tokens gebruikt, Service Fabric een token uit naam van de virtuele machine ophalen die aan ACR wordt gepresenteerd. Service Fabric vraagt standaard een token waarvan het publiek het globale Azure-Cloud eindpunt is. Als u implementeert naar een andere Cloud instantie, zoals Azure Duitsland, of Azure Government, moet u de standaard waarde van de para meter overschrijven `DefaultMSIEndpointForTokenAuthentication` . Als u niet in een speciale omgeving implementeert, moet u deze para meter niet overschrijven. Als dat het geval is, vervangt u de standaard waarde, die

```
http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.windows.net/
```

met het juiste resource-eind punt voor uw omgeving. Bijvoorbeeld: voor [Azure Duitsland](https://docs.microsoft.com/azure/germany/germany-developer-guide#endpoint-mapping)is de onderdrukking 

```json
{
    "name": "DefaultMSIEndpointForTokenAuthentication",
    "value": "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.core.cloudapi.de/"
}
```

[Meer informatie over het ophalen van tokens voor virtuele-machine schaal sets](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-use-vm-token).

## <a name="next-steps"></a>Volgende stappen

* Zie meer informatie over [container Registry-verificatie](../container-registry/container-registry-authentication.md).
