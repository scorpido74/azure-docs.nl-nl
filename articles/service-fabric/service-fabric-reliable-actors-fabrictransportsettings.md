---
title: FabricTransport-instellingen wijzigen
description: Meer informatie over het configureren van azure service fabric-actorcommunicatie-instellingen voor verschillende actorconfiguraties.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: 182b35194a647f5de706f9ada07b8e9db51bb4c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645579"
---
# <a name="configure-fabrictransport-settings-for-reliable-actors"></a>FabricTransport-instellingen configureren voor betrouwbare actoren

Dit zijn de instellingen die u configureren:
- C#: [FabricTransportRemotingInstellingen](
https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)
- Java: [FabricTransportRemotingInstellingen](https://docs.microsoft.com/java/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportremotingsettings)

U de standaardconfiguratie van FabricTransport op de volgende manieren wijzigen.

## <a name="assembly-attribute"></a>Kenmerk Assemblage

Het kenmerk [FabricTransportActorRemotingProvider](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors.remoting.fabrictransport.fabrictransportactorremotingproviderattribute?redirectedfrom=MSDN) moet worden toegepast op de actorclient- en actorservice-samenstellingen.

In het volgende voorbeeld ziet u hoe u de standaardwaarde van de bewerkingstime-outinstellingen van FabricTransport wijzigen:

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600)]
   ```

   In het tweede voorbeeld worden de standaardwaarden van FabricTransport MaxMessageSize en OperationTimeoutInSeconds gewijzigd.

  ```csharp
    using Microsoft.ServiceFabric.Actors.Remoting.FabricTransport;
    [assembly:FabricTransportActorRemotingProvider(OperationTimeoutInSeconds = 600,MaxMessageSize = 134217728)]
   ```

## <a name="config-package"></a>Config-pakket

U een [config-pakket](service-fabric-application-and-service-manifests.md) gebruiken om de standaardconfiguratie te wijzigen.

> [!IMPORTANT]
> Op Linux-knooppunten moeten certificaten pem-geformatteerd zijn. Zie Certificaten configureren [op Linux](./service-fabric-configure-certificates-linux.md)voor meer informatie over het zoeken en configureren van certificaten voor Linux. 
> 

### <a name="configure-fabrictransport-settings-for-the-actor-service"></a>FabricTransport-instellingen configureren voor de actorservice

Voeg een sectie TransportInstellingen toe in het bestand settings.xml.

Actorcode zoekt standaard naar SectionName&lt;als&gt;"ActorName TransportSettings". Als dat niet is gevonden, wordt gecontroleerd op SectionName als 'TransportSettings'.

  ```xml
  <Section Name="MyActorServiceTransportSettings">
       <Parameter Name="MaxMessageSize" Value="10000000" />
       <Parameter Name="OperationTimeoutInSeconds" Value="300" />
       <Parameter Name="SecurityCredentialsType" Value="X509" />
       <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
       <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
       <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
       <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
       <Parameter Name="CertificateStoreName" Value="My" />
       <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
       <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
   </Section>
  ```

### <a name="configure-fabrictransport-settings-for-the-actor-client-assembly"></a>FabricTransport-instellingen configureren voor de actorclientassemblage

Als de client niet wordt uitgevoerd als onderdeel van&lt;een&gt;service, u een bestand Client Exe Name .settings.xml maken op dezelfde locatie als het client .exe-bestand. Voeg vervolgens een sectie TransportInstellingen toe in dat bestand. SectionName moet worden "TransportSettings".

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <Section Name="TransportSettings">
      <Parameter Name="SecurityCredentialsType" Value="X509" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
      <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
      <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
      <Parameter Name="OperationTimeoutInSeconds" Value="300" />
      <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
      <Parameter Name="CertificateStoreName" Value="My" />
      <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
      <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
    </Section>
  </Settings>
   ```

* FabricTransport-instellingen configureren voor Secure Actor Service/Client met secundair certificaat.
  Secundaire certificaatgegevens kunnen worden toegevoegd door parameter CertificateFindValuebySecondary toe te voegen.
  Hieronder vindt u het voorbeeld voor de Listener TransportSettings.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
  <Parameter Name="CertificateFindValuebySecondary" Value="h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateRemoteThumbprints" Value="4FEF3950642138446CC364A396E1E881DB76B48C,a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
   Hieronder vindt u het voorbeeld voor de Client TransportSettings.

  ```xml
  <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
  <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
  <Parameter Name="CertificateFindValuebySecondary" Value="a9449b018d0f6839a2c5d62b5b6c6ac822b6f667" />
  <Parameter Name="CertificateRemoteThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662,h9449b018d0f6839a2c5d62b5b6c6ac822b6f690" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
  * FabricTransport-instellingen configureren voor het beveiligen van Actor-service/-client met behulp van onderwerpnaam.
    Gebruiker moet findType opgeven als FindBySubjectName, CertificateIssuerThumbprints en CertificateRemoteCommonNames-waarden toevoegen.
    Hieronder vindt u het voorbeeld voor de Listener TransportSettings.

    ```xml
    <Section Name="TransportSettings">
    <Parameter Name="SecurityCredentialsType" Value="X509" />
    <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
    <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Alice" />
    <Parameter Name="CertificateIssuerThumbprints" Value="b3449b018d0f6839a2c5d62b5b6c6ac822b6f662" />
    <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Bob" />
    <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
    <Parameter Name="CertificateStoreName" Value="My" />
    <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
    </Section>
    ```
    Hieronder vindt u het voorbeeld voor de Client TransportSettings.

  ```xml
   <Section Name="TransportSettings">
  <Parameter Name="SecurityCredentialsType" Value="X509" />
  <Parameter Name="CertificateFindType" Value="FindBySubjectName" />
  <Parameter Name="CertificateFindValue" Value="CN = WinFabric-Test-SAN1-Bob" />
  <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
  <Parameter Name="CertificateStoreName" Value="My" />
  <Parameter Name="CertificateRemoteCommonNames" Value="WinFabric-Test-SAN1-Alice" />
  <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
  </Section>
   ```
