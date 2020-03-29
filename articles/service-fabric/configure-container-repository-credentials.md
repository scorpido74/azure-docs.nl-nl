---
title: Azure Service Fabric - Referenties voor containeropslagplaatsen configureren
description: Repository-referenties configureren om afbeeldingen uit containerregister te downloaden
ms.topic: conceptual
ms.date: 12/09/2019
ms.custom: sfrev
ms.openlocfilehash: 9bd6e6a0a22f7568760f014897fd28ff47e9450b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934979"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Repository-referenties configureren voor uw toepassing om containerafbeeldingen te downloaden

Configureer verificatie van `RepositoryCredentials` het `ContainerHostPolicies` containerregister door toe te voegen aan het gedeelte van het toepassingsmanifest. Voeg het account en wachtwoord voor uw containerregister toe *(myregistry.azurecr.io* in het onderstaande voorbeeld), waarmee de service de containerafbeelding uit de opslagplaats kan downloaden.

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

Het wordt aanbevolen om het archiefwachtwoord te versleutelen met behulp van een certificaat voor vercijfering dat is geïmplementeerd op alle knooppunten van het cluster. Wanneer Service Fabric het servicepakket naar het cluster implementeert, wordt het versleutelingscertificaat gebruikt om de versleutelde tekst te ontsleutelen. De cmdlet Invoke-ServiceFabricEncryptText wordt gebruikt om de gecodeerde tekst voor het wachtwoord te maken, die wordt toegevoegd aan het bestand ApplicationManifest.xml.
Zie [Geheim beheer](service-fabric-application-secret-management.md) voor meer informatie over certificaten en versleutelingssemantiek.

## <a name="configure-cluster-wide-credentials"></a>Clusterbrede referenties configureren

Met Service Fabric u clusterbrede referenties configureren die kunnen worden gebruikt als standaardreferenties voor opslagplaatsen door toepassingen.

Deze functie kan worden ingeschakeld of `UseDefaultRepositoryCredentials` uitgeschakeld `ContainerHostPolicies` door het kenmerk toe `true` `false` te voegen aan in ApplicationManifest.xml met een of waarde.

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

Servicefabric gebruikt vervolgens de standaardrepository-referenties die kunnen worden `Hosting` opgegeven in het clustermanifest onder de sectie.  Als `UseDefaultRepositoryCredentials` `true`dat zo is, leest Service Fabric de volgende waarden uit het Clustermanifest:

* StandaardContainerRepositoryAccountName (tekenreeks)
* DefaultContainerRepositoryPassword (tekenreeks)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (tekenreeks)

Hier vindt u een voorbeeld van `Hosting` wat er in de sectie in het bestand ClusterManifestTemplate.json kan worden toegevoegd. De `Hosting` sectie kan worden toegevoegd bij het maken van het cluster of later in een configuratie-upgrade. Zie [Clusterinstellingen voor Azure Service Fabric wijzigen](service-fabric-cluster-fabric-settings.md) en azure [servicefabric-toepassingsgeheimen beheren](service-fabric-application-secret-management.md)

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
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Tokens gebruiken als registerreferenties

Service Fabric ondersteunt het gebruik van tokens als referenties om afbeeldingen voor uw containers te downloaden.  Deze functie maakt gebruik van de *beheerde identiteit* van de onderliggende virtuele machineschaal die is ingesteld om te verifiëren in het register, waardoor gebruikersreferenties niet meer hoeven te worden beheerd.  Zie [Beheerde identiteiten voor Azure-bronnen](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie.  Het gebruik van deze functie vereist de volgende stappen:

1. Controleer of *System Assigned Managed Identity* is ingeschakeld voor de VM.

    ![Azure-portal: de optie voor het schalen van virtuele machines maken](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2. Geef machtigingen toe aan de virtuele machineschaal die is ingesteld om afbeeldingen uit het register te trekken/lezen. Voeg vanuit het IAM-blad (Access Control) van uw Azure Container Registry toe in de Azure-portal een *roltoewijzing* voor uw virtuele machine:

    ![VM-principal toevoegen aan ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Wijzig vervolgens het toepassingsmanifest. Voeg `ContainerHostPolicies` in de sectie `‘UseTokenAuthenticationCredentials=”true”`het kenmerk toe .

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
    > De `UseDefaultRepositoryCredentials` vlag ingesteld `UseTokenAuthenticationCredentials` op true terwijl waar is, veroorzaakt een fout tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [verificatie van het containerregister](../container-registry/container-registry-authentication.md).
