---
title: Azure Service Fabric-toepassings geheimen beheren | Microsoft Docs
description: Meer informatie over het beveiligen van geheime waarden in een Service Fabric-toepassing (platform-neutraal).
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 94a67e45-7094-4fbd-9c88-51f4fc3c523a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 9854ad7118684e1a5e57b0809d733d812ad64176
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828836"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Versleutelde geheimen in Service Fabric-toepassingen beheren
In deze hand leiding worden de stappen beschreven voor het beheren van geheimen in een Service Fabric-toepassing. Geheimen kunnen gevoelige informatie zijn, zoals verbindings reeksen voor opslag, wacht woorden of andere waarden die niet in tekst zonder opmaak moeten worden verwerkt.

Het gebruik van versleutelde geheimen in een Service Fabric toepassing bestaat uit drie stappen:
* Stel een versleutelings certificaat in en versleutel geheimen.
* Versleutelde geheimen in een toepassing opgeven.
* Versleutelde geheimen van service code ontsleutelen.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Een versleutelings certificaat instellen en geheimen versleutelen
Het instellen van een versleutelings certificaat voor het versleutelen van geheimen is afhankelijk van Windows en Linux.
* [Stel een versleutelings certificaat in en versleutel geheimen op Windows-clusters.][secret-management-windows-specific-link]
* [Een versleutelings certificaat instellen en geheimen op Linux-clusters versleutelen.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Versleutelde geheimen in een toepassing opgeven
In de vorige stap wordt beschreven hoe u een geheim versleutelt met een certificaat en een met base 64 gecodeerde teken reeks produceert voor gebruik in een toepassing. Deze met base 64 gecodeerde teken reeks kan worden opgegeven als een versleutelde [para meter][parameters-link] in de instellingen. XML van een service of als een versleutelde [omgevings variabele][environment-variables-link] in de ServiceManifest. XML van een service.

Geef een versleutelde [para meter][parameters-link] op in het configuratie bestand instellingen. XML van uw service waarvoor het kenmerk `IsEncrypted` is ingesteld op `true`:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Geef een versleutelde [omgevings variabele][environment-variables-link] in het ServiceManifest. XML-bestand van uw service op waarvan het kenmerk `Type` is ingesteld op `Encrypted`:
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

De geheimen kunnen ook worden opgenomen in uw Service Fabric-toepassing door een certificaat op te geven in het manifest van de toepassing. Voeg een **SecretsCertificate** -element toe aan **ApplicationManifest. XML** en voeg de vinger afdruk van het gewenste certificaat toe.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Toepassings geheimen in toepassings exemplaren invoeren
In het ideale geval moet de implementatie naar verschillende omgevingen zo automatisch mogelijk worden uitgevoerd. Dit kan worden bereikt door geheime versleuteling uit te voeren in een bouw omgeving en de versleutelde geheimen op te geven als para meters bij het maken van toepassings exemplaren.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Overschrijf bare-para meters gebruiken in Settings. XML
Met het configuratie bestand settings. XML kunt u Overschrijf bare-para meters opgeven die tijdens het maken van de toepassing kunnen worden opgegeven. Gebruik het kenmerk `MustOverride` in plaats van een waarde voor een para meter op te geven:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Als u waarden in Settings. XML wilt overschrijven, declareert u een override-para meter voor de service in ApplicationManifest. XML:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

De waarde kan nu worden opgegeven als een *toepassings parameter* bij het maken van een exemplaar van de toepassing. Het maken van een toepassings exemplaar kan worden gescripteerd met Power shell C#of is geschreven in, voor een eenvoudige integratie in een bouw proces.

Met behulp van Power shell wordt de para meter door gegeven aan de `New-ServiceFabricApplication` opdracht als een [hash-tabel](https://technet.microsoft.com/library/ee692803.aspx):

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Met C#worden toepassings parameters in een `ApplicationDescription` opgegeven als een `NameValueCollection`:

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Versleutelde geheimen ontsleutelen van service code
Met de Api's voor toegang tot [para meters][parameters-link] en [omgevings variabelen][environment-variables-link] kunt u versleutelde waarden eenvoudig ontsleutelen. Omdat de versleutelde teken reeks informatie bevat over het certificaat dat voor versleuteling wordt gebruikt, hoeft u het certificaat niet hand matig op te geven. Het certificaat hoeft alleen te worden geïnstalleerd op het knoop punt waarop de service wordt uitgevoerd.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
