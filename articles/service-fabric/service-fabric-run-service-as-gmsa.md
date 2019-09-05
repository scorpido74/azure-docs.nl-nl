---
title: Een Azure Service Fabric-service uitvoeren onder een gMSA-account | Microsoft Docs
description: Meer informatie over het uitvoeren van een service als een gMSA op een zelfstandige Windows-cluster Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 4242a1eb-a237-459b-afbf-1e06cfa72732
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d00eceffebb222196191a389058c0feb496e169a
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307647"
---
# <a name="run-a-service-as-a-group-managed-service-account"></a>Een service uitvoeren als door een groep beheerd serviceaccount
Op een zelfstandige Windows Server-cluster kunt u een service uitvoeren als een door een groep beheerd service account (gMSA) met behulp van een runas-beleid.  Service Fabric toepassingen worden standaard uitgevoerd onder het account waaronder het Fabric. exe-proces wordt uitgevoerd. Door toepassingen onder verschillende accounts uit te voeren, zelfs in een gedeelde gehoste omgeving, zijn ze beter te beveiligen tegen elkaar. Houd er rekening mee dat dit Active Directory on-premises binnen uw domein gebruikt en niet Azure Active Directory (Azure AD). Als u een gMSA gebruikt, is er geen wacht woord of versleuteld wacht woord opgeslagen in het toepassings manifest.  U kunt ook een service uitvoeren als een [Active Directory gebruiker of groep](service-fabric-run-service-as-ad-user-or-group.md).

In het volgende voor beeld ziet u hoe u een gMSA-account maakt met de naam *SVC-test $* ; hoe u dat beheerde service account implementeert op de cluster knooppunten; en het configureren van de gebruikers-principal.

Vereisten:
- Het domein heeft een KDS-basis sleutel nodig.
- Het domein moet ten minste één Windows Server 2012 (of R2)-domein controller bevatten.

1. Laat een Active Directory domein beheerder een beheerd service account voor een groep maken `New-ADServiceAccount` met behulp van de `PrincipalsAllowedToRetrieveManagedPassword` commandlet en zorg ervoor dat het alle service Fabric-cluster knooppunten bevat. `AccountName`, `DnsHostName` en`ServicePrincipalName` moeten uniek zijn.

    ```powershell
    New-ADServiceAccount -name svc-Test$ -DnsHostName svc-test.contoso.com  -ServicePrincipalNames http/svc-test.contoso.com -PrincipalsAllowedToRetrieveManagedPassword SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$
    ```

2. Installeer en test de gMSA op elk van de service Fabric cluster `SfNode0$,SfNode1$,SfNode2$,SfNode3$,SfNode4$`knooppunten (bijvoorbeeld).
    
    ```powershell
    Add-WindowsFeature RSAT-AD-PowerShell
    Install-AdServiceAccount svc-Test$
    Test-AdServiceAccount svc-Test$
    ```

3. Configureer de User Principal en configureer de RunAsPolicy om te verwijzen naar de gebruiker.
    
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

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lees de volgende artikelen als volgende stap:
* [Inzicht in het toepassings model](service-fabric-application-model.md)
* [Resources opgeven in een service manifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
