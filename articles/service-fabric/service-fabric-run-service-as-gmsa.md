---
title: Een Azure Service Fabric-service uitvoeren onder een gMSA-account
description: Meer informatie over het uitvoeren van een service als een door een groep beheerd service account (gMSA) op een zelfstandige Windows-cluster Service Fabric.
author: dkkapur
ms.topic: how-to
ms.date: 03/29/2018
ms.author: dekapur
ms.custom: sfrev
ms.openlocfilehash: 19343d370547cb5457f6bed70a8465187ff27102
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76988393"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Een service uitvoeren als door een groep beheerd serviceaccount

Op een zelfstandige Windows Server-cluster kunt u een service uitvoeren als een door een *groep beheerd service account* (gMSA) met behulp van een *runas* -beleid.  Service Fabric toepassingen worden standaard uitgevoerd onder het account dat door het `Fabric.exe` proces wordt uitgevoerd. Door toepassingen onder verschillende accounts uit te voeren, zelfs in een gedeelde gehoste omgeving, zijn ze beter te beveiligen tegen elkaar. Als u een gMSA gebruikt, is er geen wacht woord of versleuteld wacht woord opgeslagen in het toepassings manifest.  U kunt ook een service uitvoeren als een [Active Directory gebruiker of groep](service-fabric-run-service-as-ad-user-or-group.md).

In het volgende voor beeld ziet u hoe u een gMSA-account met de naam *SVC-test $* maakt, hoe u dat beheerde service account implementeert voor de cluster knooppunten en hoe u de User Principal configureert.

> [!NOTE]
> Het gebruik van een gMSA met een zelfstandig Service Fabric cluster vereist Active Directory on-premises binnen uw domein (in plaats van Azure Active Directory (Azure AD)).

Vereisten:

- Het domein heeft een KDS-basis sleutel nodig.
- Het domein moet ten minste één Windows Server 2012 (of R2)-domein controller bevatten.

1. Laat een Active Directory domein beheerder een door een groep beheerd service account maken met behulp van de `New-ADServiceAccount`-cmdlet en zorg ervoor dat de `PrincipalsAllowedToRetrieveManagedPassword` alle Service Fabric cluster knooppunten bevat. `AccountName`, `DnsHostName`en `ServicePrincipalName` moeten uniek zijn.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Installeer en test de gMSA op elk van de Service Fabric cluster knooppunten (bijvoorbeeld `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configureer de gebruikers-principal en configureer de `RunAsPolicy` om te verwijzen naar de [gebruiker](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-fabric-settings#runas).
    
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
> Als u een runas-beleid toepast op een service en het service manifest declareert eindpunt resources met het HTTP-protocol, moet u een **SecurityAccessPolicy**opgeven.  Zie [beleid voor beveiligings toegang toewijzen voor HTTP-en HTTPS-eind punten](service-fabric-assign-policy-to-endpoint.md)voor meer informatie.
>

In de volgende artikelen vindt u een overzicht van de volgende stappen:

- [Inzicht in het toepassings model](service-fabric-application-model.md)
- [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
- [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
