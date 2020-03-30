---
title: Een cluster maken met gemeenschappelijke certificaatnaam
description: Meer informatie over het maken van een cluster servicestructuur met behulp van de algemene naam van het certificaat van een sjabloon.
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 4a4448c88fa9493979f075f6b9c669927dd1d39e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75614550"
---
# <a name="deploy-a-service-fabric-cluster-that-uses-certificate-common-name-instead-of-thumbprint"></a>Een cluster servicestructuur implementeren dat de algemene naam van het certificaat gebruikt in plaats van duimafdruk
Geen twee certificaten kunnen dezelfde duimafdruk hebben, wat clustercertificaatrollover of beheer moeilijk maakt. Meerdere certificaten kunnen echter dezelfde algemene naam of onderwerp hebben.  Een cluster met gebrese certificaatnamen maakt certificaatbeheer veel eenvoudiger. In dit artikel wordt beschreven hoe u een cluster servicestructuur implementeert om de algemene naam van het certificaat te gebruiken in plaats van de duimafdruk van het certificaat.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>Een certificaat aanvragen
Ontvang eerst een certificaat van een [certificeringsinstantie (CA).](https://wikipedia.org/wiki/Certificate_authority)  De algemene naam van het certificaat moet zijn voor het aangepaste domein dat u bezit en gekocht bij een domeinregistrar. Bijvoorbeeld "azureservicefabricbestpractices.com"; degenen die geen Microsoft-werknemers zijn, kunnen geen certs voor MS-domeinen inrichten, dus u de DNS-namen van uw LB of Traffic Manager niet gebruiken als algemene namen voor uw certificaat, en u moet een [Azure DNS-zone inrichten](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns) als uw aangepaste domein in Azure oplosbaar moet zijn. U wilt ook uw aangepaste domein dat u bezit als het 'managementEndpoint' van uw cluster declareren als u wilt dat portal de aangepaste domeinalias voor uw cluster weergeeft.

Voor testdoeleinden u een CA-ondertekend certificaat krijgen van een gratis of open certificaatautoriteit.

> [!NOTE]
> Zelfondertekende certificaten, inclusief certificaten die worden gegenereerd bij het implementeren van een Service Fabric-cluster in de Azure-portal, worden niet ondersteund. 

## <a name="upload-the-certificate-to-a-key-vault"></a>Het certificaat uploaden naar een sleutelkluis
In Azure wordt een cluster van Servicefabric geïmplementeerd op een virtuele machineschaalset.  Upload het certificaat naar een sleutelkluis.  Wanneer het cluster wordt geïmplementeerd, wordt het certificaat geïnstalleerd op de virtuele machineschaalset waarop het cluster wordt uitgevoerd.

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    
```

## <a name="download-and-update-a-sample-template"></a>Een voorbeeldsjabloon downloaden en bijwerken
In dit artikel wordt gebruik gemaakt van de sjabloon [voor beveiligde clustervoorbeelden](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure) en sjabloonparameters met vijf node-groepen. Download de *azuredeploy.json-* en *azuredeploy.parameters.json-bestanden* naar uw computer.

### <a name="update-parameters-file"></a>Bestand parameters bijwerken
Open eerst het bestand *azuredeploy.parameters.json* in een teksteditor en voeg de volgende parameterwaarde toe:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
```

Stel vervolgens de *parameterwaarden certificateCommonName*, *sourceVaultValue*en *certificateUrlValue* in op de parameterwaarden die door het vorige script worden geretourneerd:
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
"certificateIssuerThumbprint": {
    "value": "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
},
"sourceVaultValue": {
  "value": "/subscriptions/<subscription>/resourceGroups/testvaultgroup/providers/Microsoft.KeyVault/vaults/testvault"
},
"certificateUrlValue": {
  "value": "https://testvault.vault.azure.net:443/secrets/testcert/5c882b7192224447bbaecd5a46962655"
},
```

### <a name="update-the-template-file"></a>Het sjabloonbestand bijwerken
Open vervolgens het *azuredeploy.json-bestand* in een teksteditor en breng drie updates uit om de algemene naam van het certificaat te ondersteunen.

1. Voeg in de sectie **parameters** een parameter *certificateCommonName* toe:
    ```json
    "certificateCommonName": {
      "type": "string",
      "metadata": {
        "description": "Certificate Commonname"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "metadata": {
        "description": "Certificate Authority Issuer Thumpbrint for Commonname cert"
      }
    },
    ```

    Ook overwegen het verwijderen van de *certificaatThumbprint*, kan het niet langer nodig zijn.

2. Stel de waarde van de *variabele sfrpApiVersion* in op "2018-02-01":
    ```json
    "sfrpApiVersion": "2018-02-01",
    ```

3. Werk in de bron **Microsoft.Compute/virtualMachineScaleSets** de extensie voor virtuele machines bij om de algemene naam in certificaatinstellingen te gebruiken in plaats van de duimafdruk.  In **virtualMachineProfile**->**extensieProfiel**->**extensies**->**eigenschappen**->**instellingen**->**certificaat**, toevoegen 
    ```json
       "commonNames": [
        "[parameters('certificateCommonName')]"
       ],
    ```

    en `"thumbprint": "[parameters('certificateThumbprint')]",`verwijderen .

    ```json
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
          {
            "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
            "properties": {
              "type": "ServiceFabricNode",
              "autoUpgradeMinorVersion": true,
              "protectedSettings": {
                "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
              },
              "publisher": "Microsoft.Azure.ServiceFabric",
              "settings": {
                "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                "nodeTypeRef": "[variables('vmNodeType0Name')]",
                "dataPath": "D:\\SvcFab",
                "durabilityLevel": "Bronze",
                "enableParallelJobs": true,
                "nicPrefixOverride": "[variables('subnet0Prefix')]",
                "certificate": {
                  "commonNames": [
                     "[parameters('certificateCommonName')]"
                  ],
                  "x509StoreName": "[parameters('certificateStoreValue')]"
                }
              },
              "typeHandlerVersion": "1.0"
            }
          },
    ```

4. Werk in de **bron Microsoft.ServiceFabric/clusters** de API-versie bij naar '2018-02-01'.  Voeg ook een **instelling voor certificateCommonNames** toe met een eigenschap **commonNames** en verwijder de **certificaatinstelling** (met de eigenschap thumbprint) zoals in het volgende voorbeeld:
   ```json
   {
       "apiVersion": "2018-02-01",
       "type": "Microsoft.ServiceFabric/clusters",
       "name": "[parameters('clusterName')]",
       "location": "[parameters('clusterLocation')]",
       "dependsOn": [
       "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
       ],
       "properties": {
       "addonFeatures": [
           "DnsService",
           "RepairManager"
       ],        
       "certificateCommonNames": {
           "commonNames": [
           {
               "certificateCommonName": "[parameters('certificateCommonName')]",
               "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
           }
           ],
           "x509StoreName": "[parameters('certificateStoreValue')]"
       },
       ...
   ```
   > [!NOTE]
   > Met het veld 'certificateIssuerThumbprint' u de verwachte emittenten van certificaten met een bepaalde gemeenschappelijke naam opgeven. Dit veld accepteert een komma-gescheiden opsomming van SHA1 duimafdrukken. Let op: dit is een versterking van de certificaatvalidatie - in het geval dat de uitgever niet is opgegeven of leeg is, wordt het certificaat geaccepteerd voor verificatie als de keten kan worden gebouwd en belandt het in een root die wordt vertrouwd door de validator. Als de uitgever is opgegeven, wordt het certificaat geaccepteerd als de duimafdruk van de directe uitgevende instelling overeenkomt met een van de waarden die in dit veld zijn opgegeven - ongeacht of de wortel wordt vertrouwd of niet. Houd er rekening mee dat een PKI verschillende certificeringsinstanties kan gebruiken om certificaten voor hetzelfde onderwerp uit te geven, en daarom is het belangrijk om alle verwachte duimafdrukken van uitgevende instellingen voor een bepaald onderwerp op te geven.
   >
   > Het opgeven van de emittent wordt beschouwd als een best practice; terwijl het weglaten van het zal blijven werken - voor certificaten chaining tot een vertrouwde root - dit gedrag heeft beperkingen en kan worden uitgefaseerd in de nabije toekomst. Houd er ook rekening mee dat clusters die zijn geïmplementeerd in Azure en zijn beveiligd met X509-certificaten die zijn uitgegeven door een privé-PKI en per persoon zijn gedeclareerd, mogelijk niet kunnen worden gevalideerd door de Azure Service Fabric-service (voor cluster-to-service-communicatie), als het certificaatbeleid van de PKI is niet vindbaar, beschikbaar en toegankelijk. 

## <a name="deploy-the-updated-template"></a>De bijgewerkte sjabloon implementeren
Implementeer de bijgewerkte sjabloon opnieuw nadat u de wijzigingen hebt aangebracht.

```powershell
# Variables.
$groupname = "testclustergroup"
$clusterloc="southcentralus"  
$id="<subscription ID"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $id 

# Create a new resource group and deploy the cluster.
New-AzResourceGroup -Name $groupname -Location $clusterloc

New-AzResourceGroupDeployment -ResourceGroupName $groupname -TemplateParameterFile "C:\temp\cluster\AzureDeploy.Parameters.json" -TemplateFile "C:\temp\cluster\AzureDeploy.json" -Verbose
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [clusterbeveiliging](service-fabric-cluster-security.md).
* Meer informatie over het [overrollen van een clustercertificaat](service-fabric-cluster-rollover-cert-cn.md)
* [Clustercertificaten bijwerken en beheren](service-fabric-cluster-security-update-certs-azure.md)
* Certificaatbeheer vereenvoudigen door [cluster te wijzigen van duimafdruk van certificaat naar algemene naam](service-fabric-cluster-change-cert-thumbprint-to-cn.md)

[image1]: .\media\service-fabric-cluster-change-cert-thumbprint-to-cn\PortalViewTemplates.png
