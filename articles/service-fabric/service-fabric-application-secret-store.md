---
title: Archief met Service Fabric geheimen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Service Fabric geheimen-archief gebruikt.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: atsenthi
ms.openlocfilehash: 5315a8806f45e40204e8500e97c3440bfa9ab8b2
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077347"
---
#  <a name="service-fabric-secrets-store"></a>Archief met Service Fabric geheimen
In dit artikel wordt beschreven hoe u geheimen maakt en gebruikt in Service Fabric-toepassingen met behulp van Service Fabric geheimen-archief (CSS). CSS is een lokale archief cache, die wordt gebruikt om gevoelige gegevens te bewaren, zoals een wacht woord, tokens en sleutels die in het geheugen zijn versleuteld.

## <a name="enabling-secrets-store"></a>Archief met geheimen inschakelen
 Voeg het onderstaande toe aan uw cluster configuratie onder `fabricSettings` om CSS in te scha kelen. Het is raadzaam om een ander certificaat dan het cluster certificaat voor CSS te gebruiken. Zorg ervoor dat het versleutelings certificaat is geïnstalleerd op alle knoop punten en dat `NetworkService` Lees machtigingen hebt voor de persoonlijke sleutel van het certificaat.
  ```json
    "fabricSettings": 
    [
        ...
    {
        "parameters":  [
            "name":  "CentralSecretService"
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
```
## <a name="declare-secret-resource"></a>Geheime resource declareren
U kunt een geheime bron maken met behulp van de Resource Manager-sjabloon of met behulp van de REST API.

* Een Resource Manager-sjabloon gebruiken
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
De bovenstaande sjabloon maakt `supersecret` geheime resource, maar er is nog geen waarde ingesteld voor de geheime resource.

* Met behulp van de REST API

Als u een geheime resource wilt maken, `supersecret` maakt u een PUT-aanvraag naar `https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview`. U hebt het cluster certificaat of het client certificaat van de beheerder nodig voor het maken van een geheim.

```powershell
Invoke-WebRequest  -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret?api-version=6.4-preview -Method PUT -CertificateThumbprint <CertThumbprint>
```

## <a name="set-secret-value"></a>Geheime waarde instellen
* Een Resource Manager-sjabloon gebruiken

De onderstaande Resource Manager-sjabloon maakt en stelt een waarde in voor geheime `supersecret` met versie `ver1`.
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
* Met behulp van de REST API

```powershell
$Params = @{"properties": {"value": "mysecretpassword"}}
Invoke-WebRequest -Uri https://<clusterfqdn>:19080/Resources/Secrets/supersecret/values/ver1?api-version=6.4-preview -Method PUT -Body $Params -CertificateThumbprint <ClusterCertThumbprint>
```
## <a name="using-the-secret-in-your-application"></a>Het geheim in uw toepassing gebruiken

1.  Voeg een sectie toe aan het bestand settings. XML met de onderstaande inhoud. Opmerking Hier ziet u de indeling {`secretname:version`}

```xml
  <Section Name="testsecrets">
   <Parameter Name="TopSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/
  </Section>
```
2. Importeer nu de sectie in ApplicationManifest. XML
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

De omgevings variabele ' SecretPath ' verwijst naar de map waarin alle geheimen zijn opgeslagen. Elke para meter die wordt vermeld onder sectie `testsecrets` wordt opgeslagen in een afzonderlijk bestand. De toepassing kan nu het geheim gebruiken, zoals hieronder wordt weer gegeven
```C#
secretValue = IO.ReadFile(Path.Join(Environment.GetEnvironmentVariable("SecretPath"),  "TopSecret"))
```
3. Geheimen koppelen aan een container

Alleen wijzigingen die vereist zijn om de geheimen beschikbaar te maken binnen de container is het opgeven van een koppel punt in `<ConfigPackage>`.
Dit is de gewijzigde ApplicationManifest. XML  

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

4. Geheim binden aan een omgevings variabele 

U kunt geheim binden aan een proces omgevings variabele door type = ' SecretsStoreRef ' op te geven. Hier volgt een voor beeld van het binden van `supersecret` versie `ver1` aan omgevings variabele `MySuperSecret` in ServiceManifest. XML.

```xml
<EnvironmentVariables>
  <EnvironmentVariable Name="MySuperSecret" Type="SecretsStoreRef" Value="supersecret:ver1"/>
</EnvironmentVariables>
```
## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [beveiliging van toepassingen en services](service-fabric-application-and-service-security.md)