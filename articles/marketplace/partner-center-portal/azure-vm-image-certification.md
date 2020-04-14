---
title: Azure virtual machine certification - Azure Marketplace
description: Meer informatie over het testen en indienen van een virtuele machine aanbieding in de commerciële markt.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 009a8e3db097790788f71486431a3b5b05c488ea
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81265965"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Azure virtual machine (VM) afbeeldingscertificering

> [!NOTE]
> We verplaatsen het beheer van uw Azure VM-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, u de instructies in [Certificaten maken voor Azure Key Vault](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-key-vault-cert) in Cloud Partner Portal blijven volgen om uw aanbiedingen te beheren.

In dit artikel wordt beschreven hoe u een VM-afbeelding (Virtual Machine) testen en verzenden in de commerciële marktplaats om ervoor te zorgen dat deze voldoet aan de nieuwste publicatievereisten voor Azure Marketplace.This article describes how to test and submit a virtual machine (VM) image in the commercial marketplace to ensure it meet the latest Azure Marketplace publishing requirements.

Voer deze stappen uit voordat u uw VM-aanbieding indient:

1. Certificaten maken en implementeren.
2. Een Azure VM implementeren met uw algemene afbeelding.
3. Validaties uitvoeren.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Certificaten voor Azure Key Vault maken en implementeren

In dit gedeelte wordt beschreven hoe u de zelfondertekende certificaten maken en implementeren die nodig zijn voor het instellen van WinRM-connectiviteit (Windows Remote Management) op een door Azure gehoste virtuele machine.

### <a name="create-certificates-for-azure-key-vault"></a>Certificaten maken voor Azure Key Vault

Dit proces bestaat uit drie stappen:

1. Maak het beveiligingscertificaat.
2. Maak de Azure Key Vault om het certificaat op te slaan.
3. Bewaar de certificaten in de sleutelkluis.

U hiervoor een nieuwe of een bestaande Azure-brongroep gebruiken.

#### <a name="create-the-security-certificate"></a>Het beveiligingscertificaat maken

Bewerk en voer het volgende Azure PowerShell-script uit om het certificaatbestand (.pfx) in een lokale map te maken. Vervang de waarden voor de parameters in de volgende tabel.

| **Parameter** | **Beschrijving** |
| --- | --- |
| $certroopath | Lokale map om het PFX-bestand op te slaan. |
| $location | Een van de Azure-standaard geografische locaties. |
| $vmName | Naam van de geplande Azure virtuele machine. |
| $certname | Naam van het certificaat; moet overeenkomen met de volledig gekwalificeerde domeinnaam van de geplande VM. |
| $certpassword | Wachtwoord voor de certificaten moet overeenkomen met het wachtwoord dat wordt gebruikt voor de geplande VM. |
| | |

```PowerShell
   # Certification creation script

    # pfx certification stored path
    $certroopath = "C:\certLocation"

    # location of the resource group
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
> Houd dezelfde Azure PowerShell-consolesessie tijdens deze stappen geopend en uitgevoerd om de waarden van de verschillende parameters te behouden.

> [!WARNING]
> Als u dit script opslaat, slaat u het alleen op een veilige locatie op omdat het beveiligingsgegevens (een wachtwoord) bevat.

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>De Azure-sleutelkluis maken om het certificaat op te slaan

Kopieer de inhoud van de onderstaande sjabloon naar een bestand op uw lokale machine. In het onderstaande voorbeeldscript `C:\certLocation\keyvault.json`is deze bron ).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "keyVaultName": {
      "type": "string",
      "defaultValue":"isvkv0001",
      "metadata": {
        "description": "Name of the Vault"
      }
    },
    "tenantId": {
      "type": "string",
      "defaultValue":"72f988bf-86f1-41af-91ab-2d7cd011db47",
      "metadata": {
        "description": "Tenant Id of the subscription. Get using Get-AzureSubscription cmdlet or Get Subscription API"
      }
    },
    "objectId": {
      "type": "string",
      "defaultValue":"d55739bf-d5d6-4ce0-be1c-49ade53c4315",
      "metadata": {
        "description": "Object Id of the AD user. Get using Get-AzureADUser or Get-AzureADServicePrincipal cmdlets"
      }
    },
    "keysPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
      }
    },
    "secretsPermissions": {
      "type": "array",
      "defaultValue": ["all"],
      "metadata": {
        "description": "Permissions to secrets in the vault. Valid values are: all, get, set, list, and delete."
      }
    },
    "skuName": {
      "type": "string",
      "defaultValue": "Standard",
      "allowedValues": [
        "Standard",
        "Premium"
      ],
      "metadata": {
        "description": "SKU for the vault"
      }
    },
    "enableVaultForDeployment": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [
        true,
        false
      ],
      "metadata": {
        "description": "Specifies if the vault is enabled for a VM deployment"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.KeyVault/vaults",
      "name": "[parameters('keyVaultName')]",
      "apiVersion": "2015-06-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "enabledForDeployment": "[parameters('enableVaultForDeployment')]",
        "tenantId": "[parameters('tenantId')]",
        "accessPolicies": [
          {
            "tenantId": "[parameters('tenantId')]",
            "objectId": "[parameters('objectId')]",
            "permissions": {
              "keys": "[parameters('keysPermissions')]",
              "secrets": "[parameters('secretsPermissions')]"
            }
          }
        ],
        "sku": {
          "name": "[parameters('skuName')]",
          "family": "A"
        }
      }
    }
  ]
}

```

Bewerk en voer het volgende Azure PowerShell-script uit om een Azure Key Vault en de bijbehorende brongroep te maken. De waarden voor de parameters in de volgende tabel vervangen

| **Parameter** | **Beschrijving** |
| --- | --- |
| $postfix | Willekeurige numerieke tekenreeks die is gekoppeld aan implementatie-id's. |
| $rgName | De naam van de Azure-brongroep (RG) moet worden gemaakt. |
| $location | Een van de Azure-standaard geografische locaties. |
| $kvTemplateJson | Pad van bestand (keyvault.json) met resourcebeheersjabloon voor sleutelkluis. |
| $kvname | Naam van de nieuwe sleutelkluis.|
|   |   |

```PowerShell
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

#### <a name="store-the-certificates-to-the-key-vault"></a>De certificaten opslaan in de sleutelkluis

Sla de certificaten in het PFX-bestand op in het nieuwe sleutelkluismetje met dit script:

```PowerShell
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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Een Azure VM implementeren met uw algemene afbeelding

In deze sectie wordt beschreven hoe u een gegeneraliseerde VHD-afbeelding implementeert om een nieuwe Azure VM-bron te maken. Voor dit proces gebruiken we de meegeleverde Azure Resource Manager-sjabloon en Azure PowerShell-script.

### <a name="prepare-an-azure-resource-manager-template"></a>Een Azure Resource Manager-sjabloon voorbereiden

Kopieer de volgende Azure Resource Manager-sjabloon voor VHD-implementatie naar een lokaal bestand met de naam VHDtoImage.json. Het volgende script vraagt de locatie op de lokale machine om deze JSON te gebruiken.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "userStorageAccountName": {
            "type": "string"
        },
        "userStorageContainerName": {
            "type": "string",
            "defaultValue": "vhds"
        },
        "dnsNameForPublicIP": {
            "type": "string"
        },
        "adminUserName": {
            "defaultValue": "isv",
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring",
            "defaultValue": "Password@123"
        },
        "osType": {
            "type": "string",
            "defaultValue": "windows",
            "allowedValues": [
                "windows",
                "linux"
            ]
        },
        "subscriptionId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "vmSize": {
            "type": "string"
        },
        "publicIPAddressName": {
            "type": "string"
        },
        "vmName": {
            "type": "string"
        },
        "virtualNetworkName": {
            "type": "string"
        },
        "nicName": {
            "type": "string"
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "Name of the KeyVault"
            }
        },
        "vaultResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Resource Group of the KeyVault"
            }
        },
        "certificateUrl": {
            "type": "string",
            "metadata": {
                "description": "Url of the certificate with version in KeyVault e.g. https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7"
            }
        },
        "vhdUrl": {
            "type": "string",
            "metadata": {
                "description": "VHD Url..."
            }
        }
    },
        "variables": {
            "addressPrefix": "10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix": "10.0.0.0/24",
            "subnet2Prefix": "10.0.1.0/24",
            "publicIPAddressType": "Dynamic",
            "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "osDiskVhdName": "[concat('http://',parameters('userStorageAccountName'),'.blob.core.windows.net/',parameters('userStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('virtualNetworkName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "addressSpace": {
                        "addressPrefixes": [
                            "[variables('addressPrefix')]"
                        ]
                    },
                    "subnets": [
                        {
                            "name": "[variables('subnet1Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet1Prefix')]"
                            }
                        },
                        {
                            "name": "[variables('subnet2Name')]",
                            "properties": {
                                "addressPrefix": "[variables('subnet2Prefix')]"
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/networkInterfaces",
                "name": "[parameters('nicName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                    "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
                ],
                "properties": {
                    "ipConfigurations": [
                        {
                            "name": "ipconfig1",
                            "properties": {
                                "privateIPAllocationMethod": "Dynamic",
                                "publicIPAddress": {
                                    "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                                },
                                "subnet": {
                                    "id": "[variables('subnet1Ref')]"
                                }
                            }
                        }
                    ]
                }
            },
            {
                "apiVersion": "2015-06-15",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[parameters('vmName')]",
                "location": "[parameters('location')]",
                "dependsOn": [
                    "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
                ],
                "properties": {
                    "hardwareProfile": {
                        "vmSize": "[parameters('vmSize')]"
                    },
                    "osProfile": {
                        "computername": "[parameters('vmName')]",
                        "adminUsername": "[parameters('adminUsername')]",
                        "adminPassword": "[parameters('adminPassword')]",
                        "secrets": [
                            {
                                "sourceVault": {
                                    "id": "[resourceId(parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                                },
                                "vaultCertificates": [
                                    {
                                        "certificateUrl": "[parameters('certificateUrl')]",
                                        "certificateStore": "My"
                                    }
                                ]
                            }
                        ],
                        "windowsConfiguration": {
                            "provisionVMAgent": "true",
                            "winRM": {
                                "listeners": [
                                    {
                                        "protocol": "http"
                                    },
                                    {
                                        "protocol": "https",
                                        "certificateUrl": "[parameters('certificateUrl')]"
                                    }
                                ]
                            },
                            "enableAutomaticUpdates": "true"
                        }
                    },
                    "storageProfile": {
                        "osDisk": {
                            "name": "[concat(parameters('vmName'),'-osDisk')]",
                            "osType": "[parameters('osType')]",
                            "caching": "ReadWrite",
                            "image": {
                                "uri": "[parameters('vhdUrl')]"
                            },
                            "vhd": {
                                "uri": "[variables('osDiskVhdName')]"
                            },
                            "createOption": "FromImage"
                        }
                    },
                    "networkProfile": {
                        "networkInterfaces": [
                            {
                                "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                            }
                        ]
                    },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('http://', parameters('userStorageAccountName'), '.blob.core.windows.net')]"
                    }
                }
                }
            }
        ]
    }

```

Bewerk dit bestand om waarden voor deze parameters op te geven:

| **Parameter** | **Beschrijving** |
| --- | --- |
| ResourceGroupName | Bestaande naam van azure-bronnengroep. Gebruik doorgaans dezelfde RG als uw sleutelkluis. |
| Sjabloonbestand | Volledige pathname van het bestand VHDtoImage.json. |
| userStorageAccountName | Naam van het opslagaccount. |
| sNameForPublicIP | DNS-naam voor het openbare IP; kleine letters. |
| subscriptionId | Azure-abonnements-id. |
| Locatie | Standaard Azure geografische locatie van de resourcegroep. |
| vmName | Naam van de virtuele machine. |
| vaultName | Naam van de sleutelkluis. |
| vaultResourceGroup | Resourcegroep van de sleutelkluis. |
| certificateUrl certificateUrl certificateUrl certificateUrl | Webadres (URL) van het certificaat, inclusief versie die `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7`is opgeslagen in de sleutelkluis, bijvoorbeeld: . |
| vhdUrl | Webadres van de virtuele harde schijf. |
| vmSize | Grootte van de virtuele machine instantie. |
| publicIPAddressName | Naam van het openbare IP-adres. |
| virtualNetworkName | Naam van het virtuele netwerk. |
| nicNaam | Naam van de netwerkinterfacekaart voor het virtuele netwerk. |
| adminUserName | Gebruikersnaam van het beheerdersaccount. |
| adminPassword | Beheerderswachtwoord. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Een Azure VM implementeren

Kopieer en bewerk het volgende script `$storageaccount` `$vhdUrl` om waarden voor de en variabelen te geven. Voer deze uit om een Azure VM-bron te maken op basis van uw bestaande gegeneraliseerde VHD.

```PowerShell

# storage account of existing generalized VHD

$storageaccount = "testwinrm11815"

# generalized VHD URL

$vhdUrl = "https://testwinrm11815.blob.core.windows.net/vhds/testvm1234562016651857.vhd"

echo "New-AzResourceGroupDeployment -Name "dplisvvm$postfix" -ResourceGroupName "$rgName" -TemplateFile "C:\certLocation\VHDtoImage.json" -userStorageAccountName "$storageaccount" -dnsNameForPublicIP "$vmName" -subscriptionId "$mysubid" -location "$location" -vmName "$vmName" -vaultName "$kvname" -vaultResourceGroup "$rgName" -certificateUrl $objAzureKeyVaultSecret.Id  -vhdUrl "$vhdUrl" -vmSize "Standard\_A2" -publicIPAddressName "myPublicIP1" -virtualNetworkName "myVNET1" -nicName "myNIC1" -adminUserName "isv" -adminPassword $pwd"

# deploying VM with existing VHD

New-AzResourceGroupDeployment -Name"dplisvvm$postfix" -ResourceGroupName"$rgName" -TemplateFile"C:\certLocation\VHDtoImage.json" -userStorageAccountName"$storageaccount" -dnsNameForPublicIP"$vmName" -subscriptionId"$mysubid" -location"$location" -vmName"$vmName" -vaultName"$kvname" -vaultResourceGroup"$rgName" -certificateUrl$objAzureKeyVaultSecret.Id  -vhdUrl"$vhdUrl" -vmSize"Standard\_A2" -publicIPAddressName"myPublicIP1" -virtualNetworkName"myVNET1" -nicName"myNIC1" -adminUserName"isv" -adminPassword$pwd

```

## <a name="run-validations"></a>Validaties uitvoeren

Er zijn twee manieren om validaties uit te voeren op de geïmplementeerde afbeelding:

- Certificeringstesttool gebruiken voor Azure Certified
- De zelftest-API gebruiken

### <a name="download-and-run-the-certification-test-tool"></a>Het certificeringstestprogramma downloaden en uitvoeren

De Certification Test Tool voor Azure Certified wordt uitgevoerd op een lokale Windows-machine, maar test een Windows- of Linux-vm op Azure. Het verklaart dat uw VM-afbeelding van uw gebruiker kan worden gebruikt met Microsoft Azure en dat aan de richtlijnen en vereisten voor de voorbereiding van uw VHD is voldaan. De uitvoer van de tool is een compatibiliteitsrapport dat u uploadt naar de Portal van het Partnercenter om VM-certificering aan te vragen.

1. Download en installeer de meest recente [Certification Test Tool voor Azure Certified.](https://www.microsoft.com/download/details.aspx?id=44299)
2. Open het certificeringsgereedschap en selecteer **Nieuwe test starten**.
3. Voer in het scherm **Testinformatie** een **testnaam** in voor de testrun.
4. Selecteer het **platform** voor uw vm, Windows Server of Linux. Uw platformkeuze is van invloed op de overige opties.
5. Als uw VM deze databaseservice gebruikt, schakelt u het selectievakje **Testen voor Azure SQL Database** in.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificeringsgereedschap verbinden met een VM-afbeelding

De tool maakt verbinding met VMs op basis van Windows met [Azure PowerShell](https://docs.microsoft.com/powershell/) en maakt verbinding met Linux VM's via [SSH.Net.](https://www.ssh.com/ssh/protocol/)

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Het certificeringsprogramma verbinden met een Linux VM-afbeelding

1. Selecteer de **SSH-verificatiemodus:** wachtwoordverificatie of sleutelbestandsverificatie.
2. Als u verificatie op basis van een wachtwoord gebruikt, voert u waarden in voor de **VM DNS-naam**, **gebruikersnaam**en **wachtwoord**. U ook het standaard **SSH-poortnummer** wijzigen.

    ![Azure Certified Test Tool, wachtwoordverificatie van Linux VM Image](media/avm-cert2.png)

3. Als u verificatie op basis van sleutelbestanden gebruikt, voert u waarden in voor de **locatie VM DNS,** **Gebruikersnaam**en **Privésleutel.** U ook een **wachtwoordzin** opnemen of het standaard **SSH-poortnummer** wijzigen.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Het certificeringshulpprogramma verbinden met een VM-afbeelding op basis van Windows**

1. Voer de volledig gekwalificeerde **VM DNS-naam** in (bijvoorbeeld MyVMName.Cloudapp.net).
2. Voer waarden in voor de **gebruikersnaam** en **het wachtwoord**.

    ![Azure Certified Test Tool, wachtwoordverificatie van Windows-vm-afbeelding](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Een certificeringstest uitvoeren

Nadat u de parameterwaarden voor uw VM-afbeelding in het certificeringshulpprogramma hebt opgegeven, selecteert u **Verbinding testen** om een geldige verbinding met uw VM te maken. Nadat een verbinding is geverifieerd, selecteert u **Volgende** om de test te starten. Wanneer de test is voltooid, worden de testresultaten weergegeven in een tabel. De kolom Status toont (Pas/Mislukt/Waarschuwing) voor elke test. Als een van de tests mislukt, is uw afbeelding _niet_ gecertificeerd. Bekijk in dit geval de vereisten en foutberichten, breng de voorgestelde wijzigingen aan en voer de test opnieuw uit.

Nadat de geautomatiseerde test is voltooid, geeft u aanvullende informatie over uw VM-afbeelding op de twee tabbladen van het **scherm Vragenlijst,** **Algemene beoordeling** en **kernelaanpassing**en selecteert u **Volgende**.

Met het laatste scherm u meer informatie verstrekken, zoals SSH-toegangsinformatie voor een Linux VM-afbeelding en een verklaring voor mislukte beoordelingen als u op zoek bent naar uitzonderingen.

Selecteer ten slotte **Rapport genereren** om de testresultaten te downloaden en bestanden voor de uitgevoerde testcases te registreren, samen met uw antwoorden op de vragenlijst. Sla de resultaten op in dezelfde container als uw VHD's.

## <a name="next-step"></a>Volgende stap

- [Een uniforme resource-id 'URI' genereren voor elke VHD](https://aka.ms/AzureSASURI)
