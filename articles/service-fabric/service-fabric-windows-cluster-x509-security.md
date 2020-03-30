---
title: Een cluster beveiligen op Windows met behulp van certificaten
description: Beveiligde communicatie binnen een Azure Service Fabric-standalone of on-premises cluster, evenals tussen clients en het cluster.
author: dkkapur
ms.topic: conceptual
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 5a18f957dfb7143f403d5ac30ea184023021f12c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75613921"
---
# <a name="secure-a-standalone-cluster-on-windows-by-using-x509-certificates"></a>Een zelfstandig cluster op Windows beveiligen met X.509-certificaten
In dit artikel wordt beschreven hoe u de communicatie tussen de verschillende knooppunten van uw zelfstandige Windows-cluster beveiligen. Het beschrijft ook hoe u clients verifiëren die verbinding maken met dit cluster met behulp van X.509-certificaten. Verificatie zorgt ervoor dat alleen geautoriseerde gebruikers toegang hebben tot het cluster en de geïmplementeerde toepassingen en beheertaken kunnen uitvoeren. Certificaatbeveiliging moet zijn ingeschakeld op het cluster wanneer het cluster wordt gemaakt.  

Zie [Clusterbeveiligingsscenario's](service-fabric-cluster-security.md)voor meer informatie over clusterbeveiliging, zoals beveiliging van knooppunt tot knooppunt, beveiliging van client-naar-node en op rollen gebaseerde toegangscontrole.

## <a name="which-certificates-do-you-need"></a>Welke certificaten heb je nodig?
Download om te beginnen [het Service Fabric for Windows Server-pakket](service-fabric-cluster-creation-for-windows-server.md#download-the-service-fabric-for-windows-server-package) naar een van de knooppunten in uw cluster. In het gedownloade pakket vindt u een ClusterConfig.X509.MultiMachine.json-bestand. Open het bestand en controleer de sectie voor beveiliging onder de sectie Eigenschappen:

```JSON
"security": {
    "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    "CertificateInformation": {
        "ClusterCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },        
        "ClusterCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ClusterCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ServerCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ServerCertificateCommonNames": {
            "CommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]"
            }
            ],
            "X509StoreName": "My"
        },
        "ServerCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames" : "Root"
            }
        ],
        "ClientCertificateThumbprints": [
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": false
            },
            {
                "CertificateThumbprint": "[Thumbprint]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateCommonNames": [
            {
                "CertificateCommonName": "[CertificateCommonName]",
                "CertificateIssuerThumbprint": "[Thumbprint1,Thumbprint2,Thumbprint3,...]",
                "IsAdmin": true
            }
        ],
        "ClientCertificateIssuerStores": [
            {
                "IssuerCommonName": "[IssuerCommonName]",
                "X509StoreNames": "Root"
            }
        ]
        "ReverseProxyCertificate": {
            "Thumbprint": "[Thumbprint]",
            "ThumbprintSecondary": "[Thumbprint]",
            "X509StoreName": "My"
        },
        "ReverseProxyCertificateCommonNames": {
            "CommonNames": [
                {
                "CertificateCommonName": "[CertificateCommonName]"
                }
            ],
            "X509StoreName": "My"
        }
    }
},
```

In deze sectie worden de certificaten beschreven die u nodig hebt om uw zelfstandige Windows-cluster te beveiligen. Als u een clustercertificaat opgeeft, stelt u de waarde van ClusterCredentialType in op _X509_. Als u een servercertificaat opgeeft voor externe verbindingen, stelt u het ServerCredentialType in op _X509_. Hoewel niet verplicht, raden we u aan beide certificaten te hebben voor een goed beveiligd cluster. Als u deze waarden instelt op *X509,* moet u ook de bijbehorende certificaten opgeven of met Service Fabric wordt een uitzondering gemaakt. In sommige scenario's u alleen de _ClientCertificateThumbprints_ of het _ReverseProxyCertificate_opgeven. In deze scenario's hoeft u _clustercredentialtype_ of _ServerCredentialType_ niet in te stellen op _X509._


> [!NOTE]
> Een [duimafdruk](https://en.wikipedia.org/wiki/Public_key_fingerprint) is de primaire identiteit van een certificaat. Zie [Een duimafdruk van een certificaat ophalen](https://msdn.microsoft.com/library/ms734695.aspx)voor de duimafdruk van de certificaten die u maakt.
> 
> 

In de volgende tabel worden de certificaten weergegeven die u nodig hebt voor de installatie van uw cluster:

| **CertificateInformation-instelling** | **Beschrijving** |
| --- | --- |
| Clustercertificaat |Aanbevolen voor een testomgeving. Dit certificaat is vereist om de communicatie tussen de knooppunten op een cluster te beveiligen. U twee verschillende certificaten, een primaire en een secundaire, gebruiken voor een upgrade. Stel de duimafdruk van het primaire certificaat in de sectie Duimafdruk en die van de secundaire in de ThumbprintSecondary variabelen. |
| ClusterCertificateCommonNames |Aanbevolen voor een productieomgeving. Dit certificaat is vereist om de communicatie tussen de knooppunten op een cluster te beveiligen. U een of twee algemene namen van het clustercertificaat gebruiken. De CertificateIssuerThumbprint komt overeen met de duimafdruk van de uitgever van dit certificaat. Als er meer dan één certificaat met dezelfde algemene naam wordt gebruikt, u meerdere duimafdrukken van uitgevende instellingen opgeven.|
| ClusterCertificateIssuerStores |Aanbevolen voor een productieomgeving. Dit certificaat komt overeen met de uitgever van het clustercertificaat. U de algemene naam van de uitgever en de bijbehorende winkelnaam onder deze sectie opgeven in plaats van de duimafdruk van de uitgever op te geven onder ClusterCertificateCommonNames.  Dit maakt het eenvoudig om clusteruitgevende certificaten te rolloveren. Meerdere emittenten kunnen worden opgegeven als er meer dan één clustercertificaat wordt gebruikt. Met een lege IssuerCommonName worden alle certificaten in de bijbehorende winkels vermeld onder X509StoreNames.|
| ServerCertificate |Aanbevolen voor een testomgeving. Dit certificaat wordt aan de client gepresenteerd wanneer het probeert verbinding te maken met dit cluster. Voor het gemak u ervoor kiezen om hetzelfde certificaat te gebruiken voor ClusterCertificate en ServerCertificate. U twee verschillende servercertificaten gebruiken, een primaire en een secundaire, voor een upgrade. Stel de duimafdruk van het primaire certificaat in de sectie Duimafdruk en die van de secundaire in de ThumbprintSecondary variabelen. |
| Namen van servercertificatecommonnames |Aanbevolen voor een productieomgeving. Dit certificaat wordt aan de client gepresenteerd wanneer het probeert verbinding te maken met dit cluster. De CertificateIssuerThumbprint komt overeen met de duimafdruk van de uitgever van dit certificaat. Als er meer dan één certificaat met dezelfde algemene naam wordt gebruikt, u meerdere duimafdrukken van uitgevende instellingen opgeven. Voor het gemak u ervoor kiezen om hetzelfde certificaat te gebruiken voor ClusterCertificateCommonNames en ServerCertificateCommonNames. U een of twee algemene namen van servercertificaten gebruiken. |
| ServerCertificateIssuerStores |Aanbevolen voor een productieomgeving. Dit certificaat komt overeen met de uitgever van het servercertificaat. U de algemene naam van de uitgever en de bijbehorende winkelnaam onder deze sectie opgeven in plaats van de duimafdruk van de uitgever op te geven onder ServerCertificateCommonNames.  Dit maakt het eenvoudig om serveruitgevende certificaten te rolloveren. Meerdere emittenten kunnen worden opgegeven als er meer dan één servercertificaat wordt gebruikt. Met een lege IssuerCommonName worden alle certificaten in de bijbehorende winkels vermeld onder X509StoreNames.|
| ClientCertificateThumbprints |Installeer deze set certificaten op de geverifieerde clients. U een aantal verschillende clientcertificaten laten installeren op de machines die u toegang tot het cluster wilt toestaan. Stel de duimafdruk van elk certificaat in de variabele CertificateThumbprint. Als u IsAdmin *op true instelt,* kan de client met dit certificaat erop administratorbeheeractiviteiten op het cluster uitvoeren. Als IsAdmin *onwaar*is, kan de client met dit certificaat de acties uitvoeren die alleen zijn toegestaan voor gebruikerstoegangsrechten, meestal alleen-lezen. Zie [RBAC (Role-Based Access Control) voor](service-fabric-cluster-security.md#role-based-access-control-rbac)meer informatie over rollen. |
| ClientCertificateCommonNames |Stel de algemene naam in van het eerste clientcertificaat voor de CertificateCommonName. De CertificateIssuerThumbprint is de duimafdruk voor de uitgever van dit certificaat. Zie [Werken met certificaten](https://msdn.microsoft.com/library/ms731899.aspx)voor meer informatie over algemene namen en de uitgever. |
| ClientCertificateIssuerStores |Aanbevolen voor een productieomgeving. Dit certificaat komt overeen met de uitgever van het clientcertificaat (zowel admin- als niet-beheerdersrollen). U de algemene naam van de uitgever en de bijbehorende winkelnaam onder deze sectie opgeven in plaats van de duimafdruk van de uitgever op te geven onder ClientCertificateCommonNames.  Dit maakt het eenvoudig om clientissuer certificaten te rolloveren. Meerdere emittenten kunnen worden opgegeven als er meer dan één clientcertificaat wordt gebruikt. Met een lege IssuerCommonName worden alle certificaten in de bijbehorende winkels vermeld onder X509StoreNames.|
| ReverseProxyCertificaat |Aanbevolen voor een testomgeving. Dit optionele certificaat kan worden opgegeven als u uw [omgekeerde proxy](service-fabric-reverseproxy.md)wilt beveiligen. Zorg ervoor dat reverseProxyEndpointPort is ingesteld in nodeTypes als u dit certificaat gebruikt. |
| ReverseProxyCertificateCommonNames |Aanbevolen voor een productieomgeving. Dit optionele certificaat kan worden opgegeven als u uw [omgekeerde proxy](service-fabric-reverseproxy.md)wilt beveiligen. Zorg ervoor dat reverseProxyEndpointPort is ingesteld in nodeTypes als u dit certificaat gebruikt. |

Hier is een voorbeeldclusterconfiguratie waarbij het cluster, de server en de clientcertificaten zijn verstrekt. Voor cluster/server/reverseProxy-certificaten kunnen de duimafdruk en de algemene naam niet samen worden geconfigureerd voor hetzelfde certificaattype.

 ```JSON
 {
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "10-2017",
    "nodes": [{
        "nodeName": "vm0",
        "metadata": "Replace the localhost below with valid IP address or FQDN",
        "iPAddress": "10.7.0.5",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r0",
        "upgradeDomain": "UD0"
    }, {
        "nodeName": "vm1",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "iPAddress": "10.7.0.4",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r1",
        "upgradeDomain": "UD1"
    }, {
        "nodeName": "vm2",
        "iPAddress": "10.7.0.6",
        "metadata": "Replace the localhost with valid IP address or FQDN",
        "nodeTypeRef": "NodeType0",
        "faultDomain": "fd:/dc1/r2",
        "upgradeDomain": "UD2"
    }],
    "properties": {
        "diagnosticsStore": {
        "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
        "dataDeletionAgeInDays": "7",
        "storeType": "FileShare",
        "IsEncrypted": "false",
        "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
        },
        "security": {
            "metadata": "The Credential type X509 indicates this cluster is secured by using X509 certificates. The thumbprint format is d5 ec 42 3b 79 cb e5 07 fd 83 59 3c 56 b9 d5 31 24 25 42 64.",
            "ClusterCredentialType": "X509",
            "ServerCredentialType": "X509",
            "CertificateInformation": {
                "ClusterCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myClusterCertCommonName"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClusterCertificateIssuerStores": [
                    {
                        "IssuerCommonName": "ClusterIssuer1",
                        "X509StoreNames" : "Root"
                    },
                    {
                        "IssuerCommonName": "ClusterIssuer2",
                        "X509StoreNames" : "Root"
                    }
                ],
                "ServerCertificateCommonNames": {
                  "CommonNames": [
                    {
                      "CertificateCommonName": "myServerCertCommonName",
                      "CertificateIssuerThumbprint": "7c fc 91 97 13 16 8d ff a8 ee 71 2b a2 f4 62 62 00 03 49 0d"
                    }
                  ],
                  "X509StoreName": "My"
                },
                "ClientCertificateThumbprints": [{
                    "CertificateThumbprint": "c4 c18 8e aa a8 58 77 98 65 f8 61 4a 0d da 4c 13 c5 a1 37 6e",
                    "IsAdmin": false
                }, {
                    "CertificateThumbprint": "71 de 04 46 7c 9e d0 54 4d 02 10 98 bc d4 4c 71 e1 83 41 4e",
                    "IsAdmin": true
                }]
            }
        },
        "reliabilityLevel": "Bronze",
        "nodeTypes": [{
            "name": "NodeType0",
            "clientConnectionEndpointPort": "19000",
            "clusterConnectionEndpointPort": "19001",
            "leaseDriverEndpointPort": "19002",
            "serviceConnectionEndpointPort": "19003",
            "httpGatewayEndpointPort": "19080",
            "applicationPorts": {
                "startPort": "20001",
                "endPort": "20031"
            },
            "ephemeralPorts": {
                "startPort": "20032",
                "endPort": "20062"
            },
            "isPrimary": true
        }
         ],
        "fabricSettings": [{
            "name": "Setup",
            "parameters": [{
                "name": "FabricDataRoot",
                "value": "C:\\ProgramData\\SF"
            }, {
                "name": "FabricLogRoot",
                "value": "C:\\ProgramData\\SF\\Log"
            }]
        }]
    }
}
 ```

## <a name="certificate-rollover"></a>Certificaatrollover
Wanneer u een gemeenschappelijke certificaatnaam gebruikt in plaats van een duimafdruk, vereist certificaatrollover geen upgrade van de clusterconfiguratie. Voor upgrades van duimafdrukken van uitgevers moet u ervoor zorgen dat de nieuwe lijst met duimafdruk en de oude lijst wordt doorsneden. U moet eerst een config-upgrade uitvoeren met de nieuwe duimafdrukken van de uitgever en vervolgens de nieuwe certificaten (zowel cluster-/servercertificaten als emittentcertificaten) in de winkel installeren. Bewaar het oude certificaat van de uitgever ten minste twee uur nadat u het nieuwe emittentcertificaat hebt geïnstalleerd, in het certificaatarchief.
Als u emittentwinkels gebruikt, hoeft er geen config-upgrade te worden uitgevoerd voor het overrollen van emittentcertificaten. Installeer het nieuwe emittentcertificaat met een laatste vervaldatum in het bijbehorende certificaatarchief en verwijder het oude certificaat van de uitgever na een paar uur.

## <a name="acquire-the-x509-certificates"></a>De X.509-certificaten verkrijgen
Als u de communicatie binnen het cluster wilt beveiligen, moet u eerst X.509-certificaten voor uw clusterknooppunten verkrijgen. Om de verbinding met dit cluster te beperken tot geautoriseerde machines/gebruikers, moet u certificaten voor de clientmachines verkrijgen en installeren.

Voor clusters waarop productieworkloads worden uitgevoerd, gebruikt u een [certificaatinstantie (CA)](https://en.wikipedia.org/wiki/Certificate_authority)-ondertekend X.509-certificaat om het cluster te beveiligen. Zie [Een certificaat verkrijgen](https://msdn.microsoft.com/library/aa702761.aspx)voor meer informatie over het verkrijgen van deze certificaten.

Voor clusters die u voor testdoeleinden gebruikt, u ervoor kiezen om een zelfondertekend certificaat te gebruiken.

## <a name="optional-create-a-self-signed-certificate"></a>Optioneel: een zelfondertekend certificaat maken
Een manier om een zelfondertekend certificaat te maken dat correct kan worden beveiligd, is door het Script CertSetup.ps1 te gebruiken in de map Service Fabric SDK in de map C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\Secure. Bewerk dit bestand om de standaardnaam van het certificaat te wijzigen. (Zoek naar de waarde CN=ServiceFabricDevClusterCert.) Voer dit `.\CertSetup.ps1 -Install`script uit als .

Exporteer het certificaat nu naar een .pfx-bestand met een beveiligd wachtwoord. Haal eerst de duimafdruk van het certificaat. 
1. Voer in het menu **Start** **computercertificaten beheren**uit . 

2. Ga naar de map **Lokale computer\Personal** en zoek het certificaat dat u hebt gemaakt. 

3. Dubbelklik op het certificaat om het te openen, selecteer het tabblad **Details** en blader omlaag naar het veld **Duimafdruk.** 

4. Verwijder de spaties en kopieer de duimafdrukwaarde naar de volgende PowerShell-opdracht. 

5. Wijzig `String` de waarde in een geschikt beveiligd wachtwoord om het te beveiligen en voer het volgende uit in PowerShell:

   ```powershell   
   $pswd = ConvertTo-SecureString -String "1234" -Force –AsPlainText
   Get-ChildItem -Path cert:\localMachine\my\<Thumbprint> | Export-PfxCertificate -FilePath C:\mypfx.pfx -Password $pswd
   ```

6. Voer de volgende PowerShell-opdracht uit om de details van een certificaat dat op de machine is geïnstalleerd, te bekijken:

   ```powershell
   $cert = Get-Item Cert:\LocalMachine\My\<Thumbprint>
   Write-Host $cert.ToString($true)
   ```

Als u een Azure-abonnement hebt, volgt u ook de stappen in [Een cluster Servicefabric maken met Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

## <a name="install-the-certificates"></a>De certificaten installeren
Nadat u certificaten hebt, u deze installeren op de clusterknooppunten. Uw knooppunten moeten de nieuwste Windows PowerShell 3.x hebben geïnstalleerd. Herhaal deze stappen op elk knooppunt voor zowel cluster- als servercertificaten en secundaire certificaten.

1. Kopieer het .pfx-bestand of de pfx-bestanden naar het knooppunt.

2. Open een PowerShell-venster als beheerder en voer de volgende opdrachten in. Vervang *$pswd* door het wachtwoord dat u hebt gebruikt om dit certificaat te maken. Vervang *$PfxFilePath* door het volledige pad van de .pfx gekopieerd naar dit knooppunt.
   
    ```powershell
    $pswd = "1234"
    $PfxFilePath ="C:\mypfx.pfx"
    Import-PfxCertificate -Exportable -CertStoreLocation Cert:\LocalMachine\My -FilePath $PfxFilePath -Password (ConvertTo-SecureString -String $pswd -AsPlainText -Force)
    ```
3. Stel nu het toegangscontrolebesturingselement in op dit certificaat, zodat het Service Fabric-proces, dat wordt uitgevoerd onder het Network Service-account, het kan gebruiken door het volgende script uit te voeren. Geef de duimafdruk van het certificaat en **de netwerkservice** voor het serviceaccount. U controleren of de ACL's op het certificaat correct zijn **All Tasks** > door het certificaat te openen in **Computercertificaten starten** > **beheren** en alle taken**privésleutels beheren**te bekijken.
   
    ```powershell
    param
    (
    [Parameter(Position=1, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$pfxThumbPrint,
   
    [Parameter(Position=2, Mandatory=$true)]
    [ValidateNotNullOrEmpty()]
    [string]$serviceAccount
    )
   
    $cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object -FilterScript { $PSItem.ThumbPrint -eq $pfxThumbPrint; }
   
    # Specify the user, the permissions, and the permission type
    $permission = "$($serviceAccount)","FullControl","Allow"
    $accessRule = New-Object -TypeName System.Security.AccessControl.FileSystemAccessRule -ArgumentList $permission
   
    # Location of the machine-related keys
    $keyPath = Join-Path -Path $env:ProgramData -ChildPath "\Microsoft\Crypto\RSA\MachineKeys"
    $keyName = $cert.PrivateKey.CspKeyContainerInfo.UniqueKeyContainerName
    $keyFullPath = Join-Path -Path $keyPath -ChildPath $keyName
   
    # Get the current ACL of the private key
    $acl = (Get-Item $keyFullPath).GetAccessControl('Access')
   
    # Add the new ACE to the ACL of the private key
    $acl.SetAccessRule($accessRule)
   
    # Write back the new ACL
    Set-Acl -Path $keyFullPath -AclObject $acl -ErrorAction Stop
   
    # Observe the access rights currently assigned to this certificate
    get-acl $keyFullPath| fl
    ```
4. Herhaal de vorige stappen voor elk servercertificaat. U deze stappen ook gebruiken om de clientcertificaten te installeren op de machines die u toegang tot het cluster wilt toestaan.

## <a name="create-the-secure-cluster"></a>Het beveiligde cluster maken
Nadat u het beveiligingsgedeelte van het clusterbestand ClusterConfig.X509.MultiMachine.json hebt geconfigureerd, u overgaan tot de sectie [Het cluster maken](service-fabric-cluster-creation-for-windows-server.md#create-the-cluster) om de knooppunten te configureren en het zelfstandige cluster te maken. Vergeet niet om het bestand ClusterConfig.X509.MultiMachine.json te gebruiken terwijl u het cluster maakt. Uw opdracht kan er bijvoorbeeld als volgt uitzien:

```powershell
.\CreateServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

Nadat u het beveiligde standalone Windows-cluster hebt uitgevoerd en de geverifieerde clients hebt ingesteld om verbinding te maken, volgt u de stappen in de sectie [Verbinding maken met een cluster met PowerShell](service-fabric-connect-to-secure-cluster.md#connect-to-a-cluster-using-powershell) om er verbinding mee te maken. Bijvoorbeeld:

```powershell
$ConnectArgs = @{  ConnectionEndpoint = '10.7.0.5:19000';  X509Credential = $True;  StoreLocation = 'LocalMachine';  StoreName = "MY";  ServerCertThumbprint = "057b9544a6f2733e0c8d3a60013a58948213f551";  FindType = 'FindByThumbprint';  FindValue = "057b9544a6f2733e0c8d3a60013a58948213f551"   }
Connect-ServiceFabricCluster $ConnectArgs
```

U vervolgens andere PowerShell-opdrachten uitvoeren om met dit cluster te werken. U bijvoorbeeld [Get-ServiceFabricNode](https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode?view=azureservicefabricps) uitvoeren om een lijst met knooppunten op dit beveiligde cluster weer te geven.


Als u het cluster wilt verwijderen, maakt u verbinding met het knooppunt op het cluster waar u het Service Fabric-pakket hebt gedownload, opent u een opdrachtregel en gaat u naar de pakketmap. Voer nu de volgende opdracht uit:

```powershell
.\RemoveServiceFabricCluster.ps1 -ClusterConfigFilePath .\ClusterConfig.X509.MultiMachine.json
```

> [!NOTE]
> Onjuiste certificaatconfiguratie voorkomt mogelijk dat het cluster tijdens de implementatie wordt ingevoerd. Als u beveiligingsproblemen zelf wilt diagnosticeren, kijkt u in de **Groep Toepassingen en Services van logboeken** > van logboeken**van logboeken microsoft-service**.
> 
> 

