---
title: Een Azure Key Vault-certificaat maken | Azure Marketplace
description: Hier wordt uitgelegd hoe u een VM registreert vanaf een VHD die door Azure is geïmplementeerd.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: dc106ecf40367172f28c0f339bbcce8bddb50a34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278072"
---
# <a name="create-certificates-for-azure-key-vault"></a>Certificaten maken voor Azure Key Vault

In dit artikel wordt uitgelegd hoe u de zelfondertekende certificaten indient die nodig zijn om een WinRM-verbinding (Windows Remote Management) tot stand te brengen met een door Azure gehoste virtuele machine (VM). Dit proces bestaat uit drie stappen:

1.  Maak het beveiligingscertificaat. 
2.  Maak de Azure Key Vault om dit certificaat op te slaan. 
3.  Bewaar de certificaten in deze sleutelkluis. 

U hiervoor een nieuwe of een bestaande Azure-brongroep gebruiken.  De eerste benadering wordt gebruikt in de volgende verklaring.



[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="create-the-certificate"></a>Het certificaat maken

Bewerk en voer het volgende Azure Powershell-script uit om het certificaatbestand (.pfx) in een lokale map te maken.  U moet de waarden voor de volgende parameters vervangen:

|  **Parameter**        |   **Beschrijving**                                                               |
|  -------------        |   ---------------                                                               |
| `$certroopath` | Lokale map om het PFX-bestand op te slaan  |
| `$location`    | Een van de Azure-standaardgeografische locaties  |
| `$vmName`      | Naam van de geplande virtuele Azure-machine   |
| `$certname`    | Naam van het certificaat; moet overeenkomen met de volledig gekwalificeerde domeinnaam van de geplande VM  |
| `$certpassword` | Wachtwoord voor de certificaten moet overeenkomen met het wachtwoord dat wordt gebruikt voor de geplande VM  |
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
> Houd dezelfde PowerShell-consolesessie actief tijdens deze stappen, zodat de waarden van de verschillende parameters behouden blijven.

> [!WARNING]
> Als u dit script opslaat, slaat u het alleen op een veilige locatie op omdat het beveiligingsgegevens (een wachtwoord) bevat.


## <a name="create-the-key-vault"></a>De sleutelkluis maken

Kopieer de inhoud van de sjabloon voor het implementeren van [de sleutelkluis](./cpp-key-vault-deploy-template.md) naar een bestand op uw lokale machine. (in het onderstaande voorbeeldscript `C:\certLocation\keyvault.json`is deze bron .)  Bewerk en voer het volgende Azure Powershell-script uit om een Azure Key Vault-exemplaar en de bijbehorende brongroep te maken.  U moet de waarden voor de volgende parameters vervangen:

|  **Parameter**        |   **Beschrijving**                                                               |
|  -------------        |   ---------------                                                               |
| `$postfix`            | Willekeurige numerieke tekenreeks die is toegevoegd aan implementatie-id's                     |
| `$rgName`             | RG-naam (Azure Resource Group) om te maken                                        |
|  `$location`          | Een van de Azure-standaardgeografische locaties                                  |
| `$kvTemplateJson`     | Pad van bestand (keyvault.json) met resourcebeheersjabloon voor sleutelkluis |
| `$kvname`             | Naam van de nieuwe sleutelkluis                                                       |
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

U de certificaten, die in het PFX-bestand staan, nu opslaan in de nieuwe sleutelkluis door het volgende script uit te voeren. 

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

Vervolgens implementeert u [een VM van uw VM-afbeelding voor gebruikers.](./cpp-deploy-vm-user-image.md)
