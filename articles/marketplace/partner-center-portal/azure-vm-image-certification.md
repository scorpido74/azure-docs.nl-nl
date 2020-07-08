---
title: Certificering van Azure virtual machine-Azure Marketplace
description: Meer informatie over het testen en verzenden van een aanbieding voor de virtuele machine in de commerciële Marketplace.
author: emuench
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 63f18556847a717322b00092b973f59877102a1d
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963900"
---
# <a name="azure-virtual-machine-vm-image-certification"></a>Certificering van installatie kopie van virtuele Azure-machine (VM)

In dit artikel wordt beschreven hoe u een installatie kopie van een virtuele machine (VM) in de commerciële Marketplace kunt testen en verzenden om te controleren of deze voldoet aan de meest recente publicatie vereisten voor Azure Marketplace.

Voer de volgende stappen uit voordat u uw VM-aanbieding verzendt:

1. Certificaten maken en implementeren.
2. Implementeer een Azure VM met behulp van uw gegeneraliseerde installatie kopie.
3. Voer validaties uit.

## <a name="create-and-deploy-certificates-for-azure-key-vault"></a>Certificaten voor Azure Key Vault maken en implementeren

In deze sectie wordt beschreven hoe u zelfondertekende certificaten maakt en implementeert die vereist zijn voor het instellen van WinRM-connectiviteit (Windows Remote Management) voor een virtuele machine die door Azure wordt gehost.

### <a name="create-certificates-for-azure-key-vault"></a>Certificaten maken voor Azure Key Vault

Dit proces bestaat uit drie stappen:

1. Maak het beveiligings certificaat.
2. Maak de Azure Key Vault om het certificaat op te slaan.
3. Sla de certificaten op in de sleutel kluis.

U kunt een nieuwe of een bestaande Azure-resource groep gebruiken voor dit werk.

#### <a name="create-the-security-certificate"></a>Het beveiligings certificaat maken

Bewerk en voer het volgende Azure PowerShell script uit om het certificaat bestand (. pfx) in een lokale map te maken. Vervang de waarden voor de para meters die in de volgende tabel worden weer gegeven.

| **Parameter** | **Beschrijving** |
| --- | --- |
| $certroopath | Lokale map waarin het. pfx-bestand moet worden opgeslagen. |
| $location | Een van de standaard geografische locaties van Azure. |
| $vmName | De naam van de geplande virtuele machine van Azure. |
| $certname | De naam van het certificaat; moet overeenkomen met de Fully Qualified Domain Name van de geplande virtuele machine. |
| $certpassword | Het wacht woord voor de certificaten moet overeenkomen met het wacht woord dat voor de geplande virtuele machine wordt gebruikt. |
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
> Houd dezelfde Azure PowerShell-console sessie open en voer tijdens deze stappen uit om de waarden van de verschillende para meters te bewaren.

> [!WARNING]
> Als u dit script opslaat, slaat u het op een veilige locatie op omdat het beveiligings gegevens bevat (een wacht woord).

#### <a name="create-the-azure-key-vault-to-store-the-certificate"></a>De Azure-sleutel kluis maken om het certificaat op te slaan

Kopieer de inhoud van de onderstaande sjabloon naar een bestand op uw lokale computer. In het onderstaande voorbeeld script is deze bron `C:\certLocation\keyvault.json` .

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

Bewerk en voer het volgende Azure PowerShell script uit om een Azure Key Vault en de gekoppelde resource groep te maken. Vervang de waarden voor de para meters die in de volgende tabel worden weer gegeven

| **Parameter** | **Beschrijving** |
| --- | --- |
| $postfix | Wille keurige numerieke teken reeks gekoppeld aan implementatie-id's. |
| $rgName | De naam van de Azure-resource groep (RG) die u wilt maken. |
| $location | Een van de standaard geografische locaties van Azure. |
| $kvTemplateJson | Het pad van het bestand (keyvault.jsop) met de Resource Manager-sjabloon voor de sleutel kluis. |
| $kvname | De naam van de nieuwe sleutel kluis.|
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

#### <a name="store-the-certificates-to-the-key-vault"></a>De certificaten opslaan in de sleutel kluis

Sla de certificaten in het pfx-bestand op in de nieuwe sleutel kluis met behulp van dit script:

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

## <a name="deploy-an-azure-vm-using-your-generalized-image"></a>Een Azure-VM implementeren met behulp van uw gegeneraliseerde installatie kopie

In deze sectie wordt beschreven hoe u een gegeneraliseerde VHD-installatie kopie implementeert voor het maken van een nieuwe Azure VM-resource. Voor dit proces gebruiken we de meegeleverde Azure Resource Manager sjabloon en Azure PowerShell script.

### <a name="prepare-an-azure-resource-manager-template"></a>Een Azure Resource Manager sjabloon voorbereiden

Kopieer de volgende Azure Resource Manager sjabloon voor VHD-implementatie naar een lokaal bestand met de naam VHDtoImage.jsop. Het volgende script vraagt de locatie op de lokale computer aan om deze JSON te gebruiken.

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

Bewerk dit bestand om waarden op te geven voor deze para meters:

| **Parameter** | **Beschrijving** |
| --- | --- |
| ResourceGroupName | De naam van de bestaande Azure-resource groep. Gebruik normaal gesp roken dezelfde RG als uw sleutel kluis. |
| TemplateFile | Volledige padnaam naar het bestand VHDtoImage.jsop. |
| userStorageAccountName | Naam van het opslagaccount. |
| sNameForPublicIP | DNS-naam voor het open bare IP-adres. moet een kleine letter zijn. |
| subscriptionId | Azure-abonnements-id. |
| Locatie | Standaard geografische locatie van Azure van de resource groep. |
| vmName | De naam van de virtuele machine. |
| vaultName | De naam van de sleutel kluis. |
| vaultResourceGroup | De resource groep van de sleutel kluis. |
| certificateUrl | Webadres (URL) van het certificaat, inclusief de versie die is opgeslagen in de sleutel kluis, bijvoorbeeld: `https://testault.vault.azure.net/secrets/testcert/b621es1db241e56a72d037479xab1r7` . |
| vhdUrl | Het webadres van de virtuele harde schijf. |
| vmSize | Grootte van het exemplaar van de virtuele machine. |
| publicIPAddressName | De naam van het open bare IP-adres. |
| virtualNetworkName | De naam van het virtuele netwerk. |
| nicName | De naam van de netwerk interface kaart voor het virtuele netwerk. |
| adminUserName | De gebruikers naam van het Administrator-account. |
| adminPassword | Beheerders wachtwoord. |
|   |   |

### <a name="deploy-an-azure-vm"></a>Een Azure-VM implementeren

Kopieer en bewerk het volgende script om waarden voor de variabelen en op te geven `$storageaccount` `$vhdUrl` . Voer deze uit om een Azure VM-resource te maken op basis van uw bestaande gegeneraliseerde VHD.

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

Er zijn twee manieren om validaties uit te voeren op de geïmplementeerde installatie kopie:

- Certificerings test hulpprogramma gebruiken voor Azure Certified
- De zelf test-API gebruiken

### <a name="download-and-run-the-certification-test-tool"></a>Het hulp programma voor certificerings test downloaden en uitvoeren

Het certificerings test hulpprogramma voor Azure Certified wordt uitgevoerd op een lokale Windows-computer, maar test een op Azure gebaseerde Windows-of Linux-VM. Het verklaart dat uw VM-installatie kopie van uw gebruiker kan worden gebruikt met Microsoft Azure en dat aan de richt lijnen en vereisten rond het voorbereiden van uw VHD is voldaan. De uitvoer van het hulp programma is een compatibiliteits rapport dat u uploadt naar de partner centrum-Portal om een VM-certificering aan te vragen.

1. Down load en installeer het meest recente [certificerings test programma voor Azure Certified](https://www.microsoft.com/download/details.aspx?id=44299).
2. Open het certificerings programma en selecteer vervolgens **nieuwe test starten**.
3. Voer in het scherm **test informatie** een **test naam** in voor de test uitvoering.
4. Selecteer het **platform** voor uw virtuele machine, ofwel Windows Server of Linux. De platform keuze is van invloed op de resterende opties.
5. Als uw virtuele machine gebruikmaakt van deze database service, schakelt u het selectie vakje **testen voor Azure SQL database** in.

### <a name="connect-the-certification-tool-to-a-vm-image"></a>Het certificerings hulpprogramma verbinden met een VM-installatie kopie

Het hulp programma maakt verbinding met Vm's op basis van Windows met [Azure PowerShell](https://docs.microsoft.com/powershell/) en maakt verbinding met virtuele Linux-machines via [SSH.net](https://www.ssh.com/ssh/protocol/).

### <a name="connect-the-certification-tool-to-a-linux-vm-image"></a>Verbind het certificerings programma met een Linux VM-installatie kopie

1. Selecteer de **SSH-verificatie** modus: wachtwoord verificatie of sleutel bestands verificatie.
2. Als u verificatie op basis van wacht woorden gebruikt, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam**en het **wacht woord**. U kunt ook het standaard **SSH-poort** nummer wijzigen.

    ![Azure Certified test tool, wachtwoord verificatie van Linux VM-installatie kopie](media/avm-cert2.png)

3. Als u gebruikmaakt van verificatie op basis van een sleutel bestand, voert u waarden in voor de **DNS-naam van de virtuele machine**, de **gebruikers naam**en de locatie van de **persoonlijke sleutel** . U kunt ook een **wachtwoordzin** toevoegen of het standaard- **SSH-poort** nummer wijzigen.

### <a name="connect-the-certification-tool-to-a-windows-based-vm-image"></a>**Het certificerings hulpprogramma verbinden met een VM-installatie kopie op basis van Windows**

1. Voer de volledig gekwalificeerde **VM-DNS-naam** in (bijvoorbeeld MyVMName.Cloudapp.net).
2. Voer waarden in voor de **gebruikers naam** en het **wacht woord**.

    ![Azure Certified test tool, wachtwoord verificatie van VM-installatie kopie op basis van Windows](media/avm-cert4.png)

### <a name="run-a-certification-test"></a>Een certificerings test uitvoeren

Nadat u de parameter waarden voor uw VM-installatie kopie in het certificerings programma hebt opgegeven, selecteert u **verbinding testen** om een geldige verbinding met uw virtuele machine te maken. Nadat een verbinding is geverifieerd, selecteert u **volgende** om de test te starten. Wanneer de test is voltooid, worden de test resultaten weer gegeven in een tabel. In de kolom Status wordt voor elke test weer gegeven (pass/fail/Warning). Als een van de tests mislukt, is uw installatie kopie _niet_ gecertificeerd. In dit geval raadpleegt u de vereisten en fout berichten, brengt u de voorgestelde wijzigingen aan en voert u de test opnieuw uit.

Nadat de geautomatiseerde test is voltooid, geeft u aanvullende informatie over de VM-installatie kopie op de twee tabbladen van het **vragenlijst** scherm, **algemene evaluatie** en aanpassing van de **kernel**en selecteert u **volgende**.

In het laatste scherm kunt u meer informatie opgeven, zoals SSH-toegangs gegevens voor een Linux VM-installatie kopie, en een uitleg voor eventuele mislukte beoordelingen als u op zoek bent naar uitzonde ringen.

Selecteer ten slotte **rapport genereren** om de test resultaten en logboek bestanden voor de uitgevoerde test cases samen met uw antwoorden op de vragen lijst te downloaden. Sla de resultaten op in dezelfde container als uw Vhd's.

## <a name="next-step"></a>Volgende stap

- [Veelvoorkomende problemen met SAS-URI'S en oplossingen](common-sas-uri-issues.md)
