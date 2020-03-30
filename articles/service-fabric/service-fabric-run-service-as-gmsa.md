---
title: Een Azure Service Fabric-service uitvoeren onder een gMSA-account
description: Meer informatie over het uitvoeren van een service als groepsbeheerserviceaccount (gMSA) op een zelfstandig cluster van Service Fabric Windows.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988393"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Een service uitvoeren als door een groep beheerd serviceaccount

Op een zelfstandig Windows Server-cluster u een service uitvoeren als *groepsbeheerserviceaccount* (gMSA) met behulp van een *RunAs-beleid.*  Standaard worden Service Fabric-toepassingen uitgevoerd `Fabric.exe` onder het account waarop het proces wordt uitgevoerd. Het uitvoeren van toepassingen onder verschillende accounts, zelfs in een gedeelde gehoste omgeving, maakt ze veiliger van elkaar. Door het gebruik van een gMSA, is er geen wachtwoord of versleuteld wachtwoord opgeslagen in de applicatie manifest.  U een service ook uitvoeren als [Active Directory-gebruiker of -groep.](service-fabric-run-service-as-ad-user-or-group.md)

In het volgende voorbeeld ziet u hoe u een gMSA-account maakt met de naam *svc-Test$*, hoe u dat beheerde serviceaccount implementeert naar de clusterknooppunten en hoe u de hoofdsom van de gebruiker configureert.

> [!NOTE]
> Voor het gebruik van een gMSA met een zelfstandig Service Fabric-cluster is Active Directory on-premises binnen uw domein vereist (in plaats van Azure AD (Azure AD)).

Vereisten:

- Het domein heeft een KDS-rootsleutel nodig.
- Er moet ten minste één Windows Server 2012 (of R2) DC in het domein zijn.

1. Laat een Active Directory-domeinbeheerder een groepsbeheerserviceaccount maken met `New-ADServiceAccount` `PrincipalsAllowedToRetrieveManagedPassword` de cmdlet en zorg ervoor dat alle clusterknooppunten van de ServiceFabric zijn opgenomen. `AccountName`, `DnsHostName`en `ServicePrincipalName` moet uniek zijn.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Op elk van de clusterknooppunten servicestructuur `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`(bijvoorbeeld), installeer en test u de gMSA.
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configureer de hoofdtekst `RunAsPolicy` van gebruiker en configureer de verwijzing naar de [gebruiker](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyApplicationType" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
        <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="MyServiceTypePkg" ServiceManifestVersion="1.0.0" />
          <ConfigOverrides />
          <Policies>
              <RunAsPolicy CodePackageRef="Code" UserRef="DomaingMSA"/>
          </Policies>
        </ServiceManifestImport>
      <Principals>
        <Users>
          <User Name="DomaingMSA" AccountType="ManagedServiceAccount" AccountName="domain\svc-Test$"/>
        </Users>
      </Principals>
    </ApplicationManifest>
    ```

> [!NOTE]
> Als u een RunAs-beleid toepast op een service en het servicemanifest eindpuntbronnen declareert met het HTTP-protocol, moet u een **SecurityAccessPolicy**opgeven.  Zie [Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten voor](service-fabric-assign-policy-to-endpoint.md)meer informatie.
>

In de volgende artikelen worden de volgende stappen doorlopen:

- [Het toepassingsmodel begrijpen](service-fabric-application-model.md)
- [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
- [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
