---
title: Azure Service Fabric Central Secrets Store
description: In dit artikel wordt beschreven hoe u de Central Secrets Store gebruiken in Azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: 4087e7ccdcb2281c4a08af155d35a10c66147a85
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770419"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Centrale geheimen winkel in Azure Service Fabric 
In dit artikel wordt beschreven hoe u DE Central Secrets Store (CSS) in Azure Service Fabric gebruiken om geheimen te maken in Service Fabric-toepassingen. CSS is een lokale geheime winkelcache die gevoelige gegevens, zoals een wachtwoord, tokens en sleutels, versleuteld in het geheugen bewaart.

## <a name="enable-central-secrets-store"></a>Centrale geheimen winkel inschakelen
Voeg het volgende script toe `fabricSettings` aan uw clusterconfiguratie onder om CSS in te schakelen. We raden u aan een ander certificaat te gebruiken dan een clustercertificaat voor CSS. Zorg ervoor dat het versleutelingscertificaat `NetworkService` op alle knooppunten is geïnstalleerd en dat de leestoestemming voor de privésleutel van het certificaat heeft.
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
## <a name="declare-a-secret-resource"></a>Een geheime bron declareren
U een geheime bron maken met behulp van de REST API.
  > [!NOTE] 
  > Als het cluster windows-verificatie gebruikt, wordt de REST-aanvraag verzonden via een onbeveiligd HTTP-kanaal. De aanbeveling is om een X509 gebaseerd cluster te gebruiken met beveiligde eindpunten.

Als u `supersecret` een geheime bron wilt maken met `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`behulp van de API REST, dient u een PUT-verzoek in om . U hebt het clustercertificaat of het beheerdersclientcertificaat nodig om een geheime bron te maken.

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
### <a name="examine-the-secret-value"></a>De geheime waarde onderzoeken
```powershell
Invoke-WebRequest -CertificateThumbprint <ClusterCertThumbprint> -Method POST -Uri "https:<clusterfqdn>/Resources/Secrets/supersecret/values/ver1/list_value?api-version=6.4-preview"
```
## <a name="use-the-secret-in-your-application"></a>Gebruik het geheim in uw toepassing

Volg deze stappen om het geheim in uw Service Fabric-toepassing te gebruiken.

1. Voeg een sectie toe in het bestand **settings.xml** met het volgende fragment. Houd hier rekening mee dat`secretname:version`de waarde in de indeling { }.

   ```xml
     <Section Name="testsecrets">
      <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
     </Section>
   ```

1. Importeer de sectie in **ApplicationManifest.xml**.
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

   De omgevingsvariabele `SecretPath` wijst naar de map waar alle geheimen worden opgeslagen. Elke parameter die `testsecrets` onder de sectie wordt vermeld, wordt opgeslagen in een apart bestand. De toepassing kan nu het geheim als volgt gebruiken:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. Monteer de geheimen op een container. De enige verandering die nodig is om `specify` de geheimen `<ConfigPackage>`beschikbaar te maken in de container is om een mount punt in .
Het volgende fragment is het gewijzigde **ApplicationManifest.xml**.  

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
   Geheimen zijn beschikbaar onder de mount punt in uw container.

1. U een geheim binden aan een `Type='SecretsStoreRef`variabele procesomgeving door op te geven. Het volgende fragment is een voorbeeld `supersecret` `ver1` van hoe `MySuperSecret` u de versie aan de omgevingsvariabele in **ServiceManifest.xml kunt**binden.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [applicatie- en servicebeveiliging](service-fabric-application-and-service-security.md).
