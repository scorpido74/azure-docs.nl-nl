---
title: Azure-Service Fabric-opslagplaats referenties voor container configureren | Microsoft Docs
description: Opslagplaats referenties configureren voor het downloaden van installatie kopieën uit het container register
services: service-fabric
documentationcenter: .net
author: arya
manager: gkhanna
ms.assetid: b93d31e5-9e4c-4405-b266-c0efa4643d97
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 8/1/2019
ms.author: arya
ms.openlocfilehash: c415739934e2318ea5287d5eed9f8235029b666f
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74405624"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Opslagplaats referenties voor uw toepassing configureren om container installatie kopieën te downloaden

Configureer de verificatie van het containerregister `RepositoryCredentials` door toe te voegen aan `ContainerHostPolicies` van het bestand ApplicationManifest.xml. Voeg het account en wachtwoord toe aan het containerregister myregistry.azurecr.io, waardoor de service voor de containerinstallatiekopie uit de opslagplaats kan downloaden.

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

Deze functie kan worden ingeschakeld of uitgeschakeld door het `UseDefaultRepositoryCredentials` kenmerk toe te voegen aan `ContainerHostPolicies` in ApplicationManifest. XML met een `true` of `false` waarde.

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

Service Fabric maakt vervolgens gebruik van de standaard referenties voor de opslag plaats die kunnen worden opgegeven in de ClusterManifest onder de sectie `Hosting`.  Als `UseDefaultRepositoryCredentials` `true`is, leest Service Fabric de volgende waarden uit de ClusterManifest:

* DefaultContainerRepositoryAccountName (teken reeks)
* DefaultContainerRepositoryPassword (teken reeks)
* IsDefaultContainerRepositoryPasswordEncrypted (BOOL)
* DefaultContainerRepositoryPasswordType (String)---ondersteund vanaf de 6,4-runtime

Hier volgt een voor beeld van wat kan worden toegevoegd in de sectie `Hosting` in het bestand ClusterManifestTemplate. json. U kunt de sectie `Hosting` toevoegen tijdens het maken van een cluster of later in een configuratie-upgrade. Zie [azure service Fabric-cluster instellingen wijzigen](service-fabric-cluster-fabric-settings.md) en [Azure service Fabric-toepassings geheimen beheren](service-fabric-application-secret-management.md) voor meer informatie

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

## <a name="leveraging-the-managed-identity-of-the-virtual-machine-scale-set-by-using-managed-identity-service-msi"></a>De beheerde identiteit van de schaalset voor virtuele machines gebruiken met behulp van beheerde identiteits service (MSI)

Service Fabric ondersteunt het gebruik van tokens als referenties voor het downloaden van installatie kopieën voor uw containers.  Deze functie maakt gebruik van de beheerde identiteit van de onderliggende schaalset voor virtuele machines om te verifiëren bij het REGI ster, waardoor het niet nodig is om gebruikers referenties te beheren.  Zie [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) voor meer informatie over MSI.  Als u deze functie wilt gebruiken, moeten de volgende stappen worden uitgevoerd:

1.  Zorg ervoor dat door het systeem toegewezen beheerde identiteit is ingeschakeld voor de virtuele machine (Zie de onderstaande scherm afbeelding)

    ![De identiteit van de schaalset voor virtuele machines maken](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

2.  Daarna moet u machtigingen verlenen aan de VM (SS) om installatie kopieën uit het REGI ster te halen of te lezen.  Ga naar Access Control (IAM) van uw ACR via Azure-Blade en geef uw VM (SS) de juiste machtigingen, zoals hieronder wordt weer gegeven:

    ![VM-Principal toevoegen aan ACR](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3.  Nadat de bovenstaande stappen zijn voltooid, wijzigt u het bestand applicationManifest. XML.  Zoek de tag met de naam ' ContainerHostPolicies ' en voeg het kenmerk `‘UseTokenAuthenticationCredentials=”true”`toe.

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
    > De vlag `UseDefaultRepositoryCredentials` ingesteld op true wanneer `UseTokenAuthenticationCredentials` True is, veroorzaakt een fout tijdens de implementatie.

## <a name="next-steps"></a>Volgende stappen

* Zie meer informatie over [container Registry-verificatie](/azure/container-registry/container-registry-authentication).
