---
title: Geheimen van Azure Service Fabric-toepassingsgeheimen beheren
description: Meer informatie over het beveiligen van geheime waarden in een Service Fabric-toepassing (platform-agnostisch).
author: vturecek
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: vturecek
ms.openlocfilehash: 4a489993f982993d5703a9b46d42fffaa6134038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259056"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Versleutelde geheimen beheren in Service Fabric-toepassingen
Deze gids leidt u door de stappen van het beheren van geheimen in een Service Fabric-toepassing. Geheimen kunnen gevoelige informatie zijn, zoals tekenreeksen voor opslagverbindingen, wachtwoorden of andere waarden die niet in platte tekst mogen worden verwerkt.

Het gebruik van versleutelde geheimen in een Service Fabric-toepassing omvat drie stappen:
* Stel een versleutelingscertificaat in en versleutel geheimen.
* Versleutelde geheimen opgeven in een toepassing.
* Versleutelde geheimen ontsleutelen van servicecode.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Een versleutelingscertificaat instellen en geheimen versleutelen
Het opzetten van een versleutelingscertificaat en het gebruik ervan om geheimen te versleutelen varieert tussen Windows en Linux.
* [Stel een versleutelingscertificaat in en versleutel geheimen op Windows-clusters.][secret-management-windows-specific-link]
* [Stel een versleutelingscertificaat in en versleutel geheimen op Linux-clusters.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Versleutelde geheimen opgeven in een toepassing
In de vorige stap wordt beschreven hoe u een geheim versleutelen met een certificaat en een basis-64 gecodeerde tekenreeks produceren voor gebruik in een toepassing. Deze basis-64 gecodeerde tekenreeks kan worden opgegeven als een gecodeerde [parameter][parameters-link] in de Instellingen.xml van een service of als een gecodeerde [omgevingsvariabele][environment-variables-link] in serviceManifest.xml van een service.

Geef een versleutelde [parameter][parameters-link] op in het configuratiebestand `IsEncrypted` Settings.xml `true`van uw service met het kenmerk ingesteld op:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
Geef een versleutelde [omgevingsvariabele][environment-variables-link] op in het ServiceManifest.xml-bestand van uw servicemet kenmerk `Type` ingesteld op: `Encrypted`
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

De geheimen moeten ook worden opgenomen in uw Service Fabric-toepassing door een certificaat op te geven in het toepassingsmanifest. Voeg een **SecretsCertificate-element** toe aan **ApplicationManifest.xml** en voeg de duimafdruk van het gewenste certificaat toe.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```

### <a name="inject-application-secrets-into-application-instances"></a>Toepassingsgeheimen in toepassingsinstanties injecteren
Idealiter moet de implementatie naar verschillende omgevingen zo geautomatiseerd mogelijk zijn. Dit kan worden bereikt door het uitvoeren van geheime encryptie in een build-omgeving en het verstrekken van de versleutelde geheimen als parameters bij het maken van toepassingsinstanties.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Overridable parameters gebruiken in Settings.xml
Het configuratiebestand Settings.xml maakt overridable parameters mogelijk die kunnen worden opgegeven tijdens het maken van toepassingen. Gebruik `MustOverride` het kenmerk in plaats van een waarde voor een parameter op te geven:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

Als u waarden wilt overschrijven in Settings.xml, geeft u een overschrijfparameter voor de service in ApplicationManifest.xml aan:

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

Nu kan de waarde worden opgegeven als een *toepassingsparameter* bij het maken van een instantie van de toepassing. Het maken van een toepassingsinstantie kan worden gescript met PowerShell of geschreven in C#, voor eenvoudige integratie in een buildproces.

Met PowerShell wordt de parameter `New-ServiceFabricApplication` als [hashtabel](https://technet.microsoft.com/library/ee692803.aspx)aan de opdracht geleverd:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

Met Behulp van C#, `ApplicationDescription` worden `NameValueCollection`toepassingsparameters opgegeven in een als :

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

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Versleutelde geheimen uit servicecode decoderen
De API's voor toegang tot [parameters][parameters-link] en [omgevingsvariabelen][environment-variables-link] zorgen voor eenvoudige decryptie van versleutelde waarden. Aangezien de versleutelde tekenreeks informatie bevat over het certificaat dat wordt gebruikt voor versleuteling, hoeft u het certificaat niet handmatig op te geven. Het certificaat hoeft alleen maar te worden geïnstalleerd op het knooppunt waarop de service wordt uitgevoerd.

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
* Service Fabric [Secrets Store](service-fabric-application-secret-store.md) 
* Meer informatie over [applicatie- en servicebeveiliging](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md