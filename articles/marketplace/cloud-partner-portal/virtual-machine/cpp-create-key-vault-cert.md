---
title: Een Azure Key Vault certificaat maken | Azure Marketplace
description: Hierin wordt uitgelegd hoe u een virtuele machine registreert vanuit een door Azure geïmplementeerde VHD.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 11/29/2018
ms.author: pabutler
ms.openlocfilehash: 4adc6f716050e2d792e0a5c022972e4340d2846a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823115"
---
# <a name="create-certificates-for-azure-key-vault"></a>Certificaten maken voor Azure Key Vault

In dit artikel wordt uitgelegd hoe u de zelfondertekende certificaten kunt inrichten die vereist zijn om een WinRM-verbinding (Windows Remote Management) tot stand te brengen met een door Azure gehoste virtuele machine (VM). Dit proces bestaat uit drie stappen:

1.  Maak het beveiligings certificaat. 
2.  Maak de Azure Key Vault om dit certificaat op te slaan. 
3.  Sla de certificaten op in deze sleutel kluis. 

U kunt een nieuwe of een bestaande Azure-resource groep gebruiken voor dit werk.  De eerste benadering wordt in de volgende uitleg gebruikt.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Het certificaat maken

Bewerk en voer het volgende Azure Power shell-script uit om het certificaat bestand (. pfx) in een lokale map te maken.  U moet de waarden vervangen voor de volgende para meters:

|  **Bepaalde**        |   **Beschrijving**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Lokale map waarin het. pfx-bestand moet worden opgeslagen  |
| `$location`    | Een van de standaard geografische locaties van Azure  |
| `$vmName`      | Naam van de geplande virtuele machine van Azure   |
| `$certname`    | De naam van het certificaat; moet overeenkomen met de Fully Qualified Domain Name van de geplande virtuele machine  |
| `$certpassword` | Het wacht woord voor de certificaten moet overeenkomen met het wacht woord dat wordt gebruikt voor de geplande VM  |
|  |  |

```powershell
    # Certification creation script 
    
    # pfx certification stored path
    $certroopath = "C:\certLocation"
    
    #location of the resource group
    $location = "westus"
    
    # Azure virtual machine name that we are going to create
    $vmName = "testvm000000906"
    
    # Certification name - should match with FQDN of Windows Azure creating VM 
    $certname = "$vmName.$location.cloudapp.azure.com"
    
    # Certification password - should be match with password of Windows Azure creating VM 
    $certpassword = "SecretPassword@123"
    
    $cert=New-SelfSignedCertificate -DnsName "$certname" -CertStoreLocation cert:\LocalMachine\My
    $pwd = ConvertTo-SecureString -String $certpassword -Force -AsPlainText
    $certwithThumb="cert:\localMachine\my\"+$cert.Thumbprint
    $filepath="$certroopath\$certname.pfx"
    Export-PfxCertificate -cert $certwithThumb -FilePath $filepath -Password $pwd
    Remove-Item -Path $certwithThumb 

```
> [!TIP]
> Bewaar dezelfde Power shell-console sessie actief tijdens deze stappen zodat de waarden van de verschillende para meters behouden blijven.

> [!WARNING]
> Als u dit script opslaat, slaat u het alleen op een veilige locatie op omdat het beveiligings gegevens bevat (een wacht woord).


## <a name="create-the-key-vault"></a>De sleutel kluis maken

Kopieer de inhoud van de [sjabloon voor de sleutel kluis implementatie](./cpp-key-vault-deploy-template.md) naar een bestand op uw lokale computer. (in het onderstaande voorbeeld script is deze resource `C:\certLocation\keyvault.json`.)  Bewerk en voer het volgende Azure Power shell-script uit om een Azure Key Vault-exemplaar en de gekoppelde resource groep te maken.  U moet de waarden vervangen voor de volgende para meters:

|  **Bepaalde**        |   **Beschrijving**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Wille keurige numerieke teken reeks toegevoegd aan implementatie-id's                     |
| `$rgName`             | Te maken naam van de Azure-resource groep (RG)                                        |
|  `$location`          | Een van de standaard geografische locaties van Azure                                  |
| `$kvTemplateJson`     | Het pad naar het bestand (de code van de hoofd kluis. json) dat een resource manager-sjabloon bevat voor sleutel kluis |
| `$kvname`             | De naam van de nieuwe sleutel kluis                                                       |
|  |  |

```powershell
    # Creating Key vault in resource group
    
    # "Random" number for deployment identifiers
    $postfix = "0101048"
    
    # Resource group name
    $rgName = "TestRG$postfix"
    
    # Location of Resource Group
    $location = "westus"
    
    # Key vault template location
    $kvTemplateJson = "C:\certLocation\keyvault.json"
    
    # Key vault name
    $kvname = "isvkv$postfix"
    
    # code snippet to get the Azure user object ID
    try
       {
        $accounts = Get-AzureAccount
        $accountNum = 0
        $accounts.Id | %{ ++$accountNum; Write-Host $accountNum $_}
        Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
        [Int] $accountChoice = Read-Host 
        
        While($accountChoice -lt 1 -or $accountChoice -gt $accounts.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            Write-Host "`nPlease select User, e.g. 1:" -ForegroundColor DarkYellow
            [Int] $accountChoice = Read-Host
        }
        
        $accountSelected = $accounts[$accountChoice-1]
        echo $accountSelected
        $id = $accountSelected.Id
                              
        Write-Host "User $id Selected"
        $myobjectId=(Get-AzADUser -Mail $id)[0].Id
      }
      catch
      {
      Write-Host $_.Exception.Message    
      Break
      } 

    # code snippet to get Azure User Tenant Id
      # SELECT Subscriptions
        #**************************************
        try
        {
        $subslist=Get-AzureSubscription
        for($i=1; $i -le $subslist.Length;$i++)
        {
           Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
        }
        Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
        [int] $selectedsub=Read-Host
    
        While($selectedsub -lt 1 -or $selectedsub -gt $subslist.Length)
        {
            Write-Host "incorrect input" -ForegroundColor Red 
            for($i=1; $i -le $subslist.Length;$i++)
             {
              Write-Host ($i.ToString() +":"+ $subslist[$i-1].SubscriptionName)
             }
            Write-Host "`nPlease pick subscription from above, e.g. 1:" -ForegroundColor DarkYellow
           [int] $selectedsub=Read-Host
        }
        if($selectedsub -ge 1 -and $selectedsub -le $subslist.Length)
        {
        $mysubid=$subslist[$selectedsub-1].SubscriptionId
        $mysubName=$subslist[$selectedsub-1].SubscriptionName
        $mytenantId=$subslist[$selectedsub-1].TenantId
        Write-Host "$mysubName selected"
        }
        }
        catch
        {
        Write-Host $_.Exception.Message    
        Break
        }
    
    # Create a resource group
     Write-Host "Creating Resource Group $rgName"
     Create-ResourceGroup -rgName $rgName -location $location
     Write-Host "-----------------------------------" 
    
    # Create key vault and configure access
    New-AzResourceGroupDeployment -Name "kvdeploy$postfix" -ResourceGroupName $rgName -TemplateFile $kvTemplateJson -keyVaultName $kvname -tenantId $mytenantId -objectId $myobjectId
    
    Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $myobjectId -PermissionsToKeys all -PermissionsToSecrets all 
        
```

## <a name="store-the-certificate"></a>Het certificaat opslaan

U kunt de certificaten in het pfx-bestand nu opslaan in de nieuwe sleutel kluis door het volgende script uit te voeren. 

```powershell
    #push certificate to key vault secret

     $fileName =$certroopath+"\$certname"+".pfx" 
      
     $fileContentBytes = get-content $fileName -Encoding Byte
     $fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
    
            $jsonObject = @"
    {
    "data": "$filecontentencoded",
    "dataType" :"pfx",
    "password": "$certpassword"
    }
"@
            echo $certpassword
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $objAzureKeyVaultSecret=Set-AzureKeyVaultSecret -VaultName $kvname -Name "ISVSecret$postfix" -SecretValue $secret
            echo $objAzureKeyVaultSecret.Id 
    
```


## <a name="next-steps"></a>Volgende stappen

Vervolgens [implementeert u een VM op basis van uw VM-installatie kopie van uw gebruiker](./cpp-deploy-vm-user-image.md).
