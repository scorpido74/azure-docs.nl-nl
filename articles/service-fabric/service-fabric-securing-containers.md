---
title: Invoercertificaten in een container
description: Lees nu of u certificaatbestanden wilt importeren in een containerservice servicefabric.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: da4babd8f9d1a25a8514d0c6f1526b43a9723854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614108"
---
# <a name="import-a-certificate-file-into-a-container-running-on-service-fabric"></a>Een certificaatbestand importeren in een container die op ServiceFabric wordt uitgevoerd

U uw containerservices beveiligen door een certificaat op te geven. Service Fabric biedt een mechanisme voor services in een container om toegang te krijgen tot een certificaat dat is geïnstalleerd op de knooppunten in een Windows- of Linux-cluster (versie 5.7 of hoger). Het certificaat moet worden geïnstalleerd in een certificaatarchief onder LocalMachine op alle knooppunten van het cluster. De privésleutel die overeenkomt met het certificaat moet beschikbaar, toegankelijk en - op Windows - exporteerbaar zijn. De certificaatgegevens worden weergegeven in `ContainerHostPolicies` het toepassingsmanifest onder de tag, zoals in het volgende fragment wordt weergegeven:

```xml
  <ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
    <CertificateRef Name="MyCert1" X509StoreName="My" X509FindValue="[Thumbprint1]"/>
    <CertificateRef Name="MyCert2" X509FindValue="[Thumbprint2]"/>
 ```

Voor Windows-clusters exporteert de runtime bij het starten van de toepassing elk certificaat waarnaar wordt verwezen en de bijbehorende privésleutel naar een PFX-bestand, beveiligd met een willekeurig gegenereerd wachtwoord. De PFX- en wachtwoordbestanden zijn respectievelijk toegankelijk in de container met behulp van de volgende omgevingsvariabelen: 

* Certificates_ServicePackageName_CodePackageName_CertName_PFX
* Certificates_ServicePackageName_CodePackageName_CertName_Password

Voor Linux-clusters worden de certificaten (PEM) overgekopieerd uit de winkel die is opgegeven door X509StoreName op de container. De bijbehorende omgevingsvariabelen op Linux zijn:

* Certificates_ServicePackageName_CodePackageName_CertName_PEM
* Certificates_ServicePackageName_CodePackageName_CertName_PrivateKey

Als u de certificaten al in de vereiste vorm hebt en deze in de container wilt openen, u ook een gegevenspakket in uw app-pakket maken en het volgende opgeven in uw toepassingsmanifest:

```xml
<ContainerHostPolicies CodePackageRef="NodeContainerService.Code">
  <CertificateRef Name="MyCert1" DataPackageRef="[DataPackageName]" DataPackageVersion="[Version]" RelativePath="[Relative Path to certificate inside DataPackage]" Password="[password]" IsPasswordEncrypted="[true/false]"/>
 ```

De containerservice of -verwerking is verantwoordelijk voor het importeren van de certificaatbestanden in de container. Als u het certificaat `setupentrypoint.sh` wilt importeren, u scripts gebruiken of aangepaste code uitvoeren binnen het containerproces. Hier vindt u voorbeeldcode in C# voor het importeren van het PFX-bestand:

```csharp
string certificateFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_PFX");
string passwordFilePath = Environment.GetEnvironmentVariable("Certificates_MyServicePackage_NodeContainerService.Code_MyCert1_Password");
X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
string password = File.ReadAllLines(passwordFilePath, Encoding.Default)[0];
password = password.Replace("\0", string.Empty);
X509Certificate2 cert = new X509Certificate2(certificateFilePath, password, X509KeyStorageFlags.MachineKeySet | X509KeyStorageFlags.PersistKeySet);
store.Open(OpenFlags.ReadWrite);
store.Add(cert);
store.Close();
```
Dit PFX-certificaat kan worden gebruikt voor het verifiëren van de toepassing of service of veilige communicatie met andere diensten. Standaard zijn de bestanden alleen naar SYSTEM. U ACL naar andere accounts, zoals vereist door de service.

Lees als volgende stap de volgende artikelen:

* [Een Windows-container implementeren voor servicefabric op Windows Server 2016](service-fabric-get-started-containers.md)
* [Een Docker-container implementeren voor servicefabric op Linux](service-fabric-get-started-containers-linux.md)
