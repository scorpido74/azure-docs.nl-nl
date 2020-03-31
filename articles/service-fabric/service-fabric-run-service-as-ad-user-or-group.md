---
title: Een Azure Service Fabric-service uitvoeren als EEN AD-gebruiker of -groep
description: Meer informatie over het uitvoeren van een service als Active Directory-gebruiker of -groep op een zelfstandig cluster van Service Fabric Windows.
author: dkkapur
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: dekapur
ms.openlocfilehash: d440aadb66562e32331c9725a9367c12440a315d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464243"
---
# <a name="run-a-service-as-an-active-directory-user-or-group"></a>Een service uitvoeren als Active Directory-gebruiker of -groep
In een zelfstandige windows server-cluster u een service uitvoeren als Active Directory-gebruiker of -groep met behulp van een RunAs-beleid.  Standaard worden Service Fabric-toepassingen uitgevoerd onder het account waarop het Fabric.exe-proces wordt uitgevoerd. Het uitvoeren van toepassingen onder verschillende accounts, zelfs in een gedeelde gehoste omgeving, maakt ze veiliger van elkaar. Houd er rekening mee dat hiermee Active Directory on-premises binnen uw domein wordt gebruikt en niet azure active directory (Azure AD).  U een service ook uitvoeren als een [groepsmanaged serviceaccount (gMSA).](service-fabric-run-service-as-gmsa.md)

Door een domeingebruiker of -groep te gebruiken, u vervolgens toegang krijgen tot andere bronnen in het domein (bijvoorbeeld bestandsshares) die machtigingen hebben gekregen.

In het volgende voorbeeld wordt een Active Directory-gebruiker met de naam *TestUser* weergegeven met het domeinwachtwoord dat is versleuteld met behulp van een certificaat genaamd *MyCert*. U `Invoke-ServiceFabricEncryptText` de opdracht PowerShell gebruiken om de geheime versleutelingstekst te maken. Zie [Geheimen beheren in Service Fabric-toepassingen](service-fabric-application-secret-management.md) voor meer informatie.

U moet de privésleutel van het certificaat implementeren om het wachtwoord naar de lokale machine te decoderen met behulp van een out-of-band-methode (in Azure is dit via Azure Resource Manager). Wanneer Service Fabric vervolgens het servicepakket naar de machine implementeert, kan het het geheim decoderen en (samen met de gebruikersnaam) verifiëren met Active Directory om onder deze referenties uit te voeren.

```xml
<Principals>
  <Users>
    <User Name="TestUser" AccountType="DomainUser" AccountName="Domain\User" Password="[Put encrypted password here using MyCert certificate]" PasswordEncrypted="true" />
  </Users>
</Principals>
<Policies>
  <DefaultRunAsPolicy UserRef="TestUser" />
  <SecurityAccessPolicies>
    <SecurityAccessPolicy ResourceRef="MyCert" PrincipalRef="TestUser" GrantRights="Full" ResourceType="Certificate" />
  </SecurityAccessPolicies>
</Policies>
<Certificates>
```

> [!NOTE] 
> Als u een RunAs-beleid toepast op een service en het servicemanifest eindpuntbronnen declareert met het HTTP-protocol, moet u ook een **SecurityAccessPolicy**opgeven.  Zie [Een beveiligingstoegangsbeleid toewijzen voor HTTP- en HTTPS-eindpunten voor](service-fabric-assign-policy-to-endpoint.md)meer informatie. 
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
Lees als volgende stap de volgende artikelen:
* [Het toepassingsmodel begrijpen](service-fabric-application-model.md)
* [Resources opgeven in een servicemanifest](service-fabric-service-manifest-resources.md)
* [Een app implementeren](service-fabric-deploy-remove-applications.md)

[image1]: ./media/service-fabric-application-runas-security/copy-to-output.png
