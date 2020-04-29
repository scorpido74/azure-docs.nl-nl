---
title: Archief met Azure Service Fabric Central-geheimen
description: In dit artikel wordt beschreven hoe u het archief centrale geheimen kunt gebruiken in azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770419"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Archief met centrale geheimen in azure Service Fabric 
In dit artikel wordt beschreven hoe u centraal geheimen (CSS) in azure Service Fabric kunt gebruiken om geheimen te maken in Service Fabric toepassingen. CSS is een lokale archief cache voor het opslaan van gevoelige gegevens, zoals een wacht woord, tokens en sleutels, versleuteld in het geheugen.

## <a name="enable-central-secrets-store"></a>Archief met centrale geheimen inschakelen
Voeg het volgende script toe aan de cluster configuratie `fabricSettings` onder om CSS in te scha kelen. Het is raadzaam om een ander certificaat dan een cluster certificaat voor CSS te gebruiken. Zorg ervoor dat het versleutelings certificaat is geïnstalleerd op alle `NetworkService` knoop punten en dat lees machtigingen heeft voor de persoonlijke sleutel van het certificaat.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "name":  "CentralSecretService",
        "parameters":  [
                {
                    "name":  "IsEnabled",
                    "value":  "true"
                },
                {
                    "name":  "MinReplicaSetSize",
                    "value":  "3"
                },
                {
                    "name":  "TargetReplicaSetSize",
                    "value":  "3"
                },
                 {
                    "name" : "EncryptionCertificateThumbprint",
                    "value": "<thumbprint>"
                 }
                ,
                ],
            },
            ]
     }
        ...
     ]
```
## <a name="declare-a-secret-resource"></a>Een geheime resource declareren
U kunt een geheime bron maken met behulp van de REST API.
  > [!NOTE] 
  > Als het cluster gebruikmaakt van Windows-verificatie, wordt de REST-aanvraag verzonden via een onbeveiligd HTTP-kanaal. De aanbeveling is een x509-cluster te gebruiken met beveiligde eind punten.

Als u een `supersecret` geheime resource wilt maken met behulp van de rest API, maakt `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`u een put-aanvraag naar. U hebt het cluster certificaat of het client certificaat van de beheerder nodig voor het maken van een geheime resource.

```powershell
$json = '{"properties": {"kind": "inlinedValue", "contentType": "text/plain", "description": "supersecret"}}'
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint> -Body $json
```

## <a name="set-the-secret-value"></a>De geheime waarde instellen

Gebruik het volgende script om de REST API te gebruiken om de geheime waarde in te stellen.
```powershell
$Params = '{"properties": {"value": "mysecretpassword"}}'
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
### <a name="examine-the-secret-value"></a>Controleer de geheime waarde
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Het geheim gebruiken in uw toepassing

Volg deze stappen om het geheim in uw Service Fabric-toepassing te gebruiken.

1. Voeg een sectie toe aan het bestand **Settings. XML** met het volgende code fragment. Hier ziet u dat de waarde de indeling {`secretname:version`} heeft.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importeer de sectie in **ApplicationManifest. XML**.
   ```xml
     <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" EnvironmentVariableName="SecretPath" />
           </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```

   De omgevings `SecretPath` variabele verwijst naar de map waarin alle geheimen zijn opgeslagen. Elke para meter die in `testsecrets` de sectie wordt vermeld, wordt opgeslagen in een afzonderlijk bestand. De toepassing kan het geheim nu als volgt gebruiken:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. De geheimen koppelen aan een container. De enige wijziging die is vereist om de geheimen beschikbaar te maken in de `specify` container, is op `<ConfigPackage>`een koppel punt in.
Het volgende code fragment is de gewijzigde **ApplicationManifest. XML**.  

   ```xml
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="testservicePkg" ServiceManifestVersion="1.0.0" />
       <ConfigOverrides />
       <Policies>
         <ConfigPackagePolicies CodePackageRef="Code">
           <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="C:\secrets" EnvironmentVariableName="SecretPath" />
           <!-- Linux Container
            <ConfigPackage Name="Config" SectionName="testsecrets" MountPoint="/mnt/secrets" EnvironmentVariableName="SecretPath" />
           -->
         </ConfigPackagePolicies>
       </Policies>
     </ServiceManifestImport>
   ```
   Geheimen zijn beschikbaar onder het koppel punt in uw container.

1. U kunt een geheim binden aan een proces omgevings variabele door `Type='SecretsStoreRef`op te geven. Het volgende code fragment is een voor beeld van het koppelen `supersecret` van `ver1` de versie aan de `MySuperSecret` omgevings variabele in **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md).
