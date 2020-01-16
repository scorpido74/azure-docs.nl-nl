---
title: Archief met Azure Service Fabric Central-geheimen
description: In dit artikel wordt beschreven hoe u het archief centrale geheimen kunt gebruiken in azure Service Fabric.
ms.topic: conceptual
ms.date: 07/25/2019
ms.openlocfilehash: bc6ea6260bf50d5b4f8e294e0a3827426f90bee3
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75980941"
---
# <a name="central-secrets-store-in-azure-service-fabric"></a>Archief met centrale geheimen in azure Service Fabric 
In dit artikel wordt beschreven hoe u centraal geheimen (CSS) in azure Service Fabric kunt gebruiken om geheimen te maken in Service Fabric toepassingen. CSS is een lokale archief cache voor het opslaan van gevoelige gegevens, zoals een wacht woord, tokens en sleutels, versleuteld in het geheugen.

## <a name="enable-central-secrets-store"></a>Archief met centrale geheimen inschakelen
Voeg het volgende script toe aan de cluster configuratie onder `fabricSettings` om CSS in te scha kelen. Het is raadzaam om een ander certificaat dan een cluster certificaat voor CSS te gebruiken. Zorg ervoor dat het versleutelings certificaat is geïnstalleerd op alle knoop punten en dat `NetworkService` Lees machtigingen heeft voor de persoonlijke sleutel van het certificaat.
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
U kunt een geheime bron maken met behulp van de Azure Resource Manager sjabloon of de REST API.

### <a name="use-resource-manager"></a>Resource Manager gebruiken

Gebruik de volgende sjabloon om Resource Manager te gebruiken om de geheime resource te maken. De sjabloon maakt een `supersecret` geheime resource, maar er is nog geen waarde ingesteld voor de geheime resource.


```json
   "resources": [
      {
        "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
          }
        }
      ]
```

### <a name="use-the-rest-api"></a>REST-API gebruiken

Als u een `supersecret` geheime resource wilt maken met behulp van de REST API, maakt u een PUT-aanvraag `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. U hebt het cluster certificaat of het client certificaat van de beheerder nodig voor het maken van een geheime resource.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-the-secret-value"></a>De geheime waarde instellen

### <a name="use-the-resource-manager-template"></a>De Resource Manager-sjabloon gebruiken

Gebruik de volgende Resource Manager-sjabloon om de geheime waarde te maken en in te stellen. Met deze sjabloon wordt de geheime waarde voor de `supersecret` geheime resource ingesteld als versie `ver1`.
```json
  {
  "parameters": {
  "supersecret": {
      "type": "string",
      "metadata": {
        "description": "supersecret value"
      }
   }
  },
  "resources": [
    {
      "apiVersion": "2018-07-01-preview",
        "name": "supersecret",
        "type": "Microsoft.ServiceFabricMesh/secrets",
        "location": "[parameters('location')]", 
        "dependsOn": [],
        "properties": {
          "kind": "inlinedValue",
            "description": "Application Secret",
            "contentType": "text/plain",
        }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "supersecret/ver1",
      "type": "Microsoft.ServiceFabricMesh/secrets/values",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/secrets/supersecret"
      ],
      "properties": {
        "value": "[parameters('supersecret')]"
      }
    }
  ],
  ```
### <a name="use-the-rest-api"></a>REST-API gebruiken

Gebruik het volgende script om de REST API te gebruiken om de geheime waarde in te stellen.
```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="use-the-secret-in-your-application"></a>Het geheim gebruiken in uw toepassing

Volg deze stappen om het geheim in uw Service Fabric-toepassing te gebruiken.

1. Voeg een sectie toe aan het bestand **Settings. XML** met het volgende code fragment. Hier ziet u dat de waarde de notatie {`secretname:version`} heeft.

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

   De omgevings variabele `SecretPath` verwijst naar de map waarin alle geheimen zijn opgeslagen. Elke para meter die wordt vermeld in de sectie `testsecrets`, wordt opgeslagen in een afzonderlijk bestand. De toepassing kan het geheim nu als volgt gebruiken:
   ```C#
   secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
   ```
1. De geheimen koppelen aan een container. De enige wijziging die nodig is om de geheimen beschikbaar te maken binnen de container is het `specify` van een koppel punt in `<ConfigPackage>`.
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

1. U kunt een geheim binden aan een proces omgevings variabele door `Type='SecretsStoreRef`op te geven. Het volgende code fragment is een voor beeld van het koppelen van de `supersecret` versie `ver1` aan de omgevings variabele `MySuperSecret` in **ServiceManifest. XML**.

   ```xml
   <EnvironmentVariables>
     <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
   </EnvironmentVariables>
   ```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md).
