---
title: Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)
description: In dit artikel vindt u voorwaarden voor het gebruik van Microsoft Azure Disk Encryption voor IaaS VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: b80d2e5e7c9157da2a3b05a7b422f0c0a950f90c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452040"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Een sleutelkluis voor Azure-schijfversleuteling maken en configureren met Azure AD (vorige release)

**De nieuwe release van Azure Disk Encryption elimineert de vereiste voor het verstrekken van een Azure AD-toepassingsparameter om VM-schijfversleuteling in te schakelen. Met de nieuwe release hoeft u geen Azure AD-referenties meer op te geven tijdens de stap versleuteling inschakelen. Alle nieuwe VM's moeten worden versleuteld zonder de Azure AD-toepassingsparameters met behulp van de nieuwe versie. Zie [Azure Disk Encryption](disk-encryption-overview.md)als u instructies wilt weergeven om VM-schijfversleuteling in te schakelen met de nieuwe versie. VM's die al zijn versleuteld met Azure AD-toepassingsparameters worden nog steeds ondersteund en moeten worden gehandhaafd met de syntaxis van AAD.**

Azure Disk Encryption gebruikt Azure Key Vault om schijfversleutelingssleutels en -geheimen te beheren en te beheren.  Zie [Aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en Uw [sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md)voor meer informatie over sleutelkluizen. 

Het maken en configureren van een sleutelkluis voor gebruik met Azure Disk Encryption met Azure AD (vorige release) omvat drie stappen:

1. Een sleutelkluis maken. 
2. Stel een Azure AD-toepassing en serviceprincipal in.
3. Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen.
4. Geavanceerd toegangsbeleid voor de sleutelkluis instellen.
 
U ook, indien u dat wenst, een sleutelversleutelingssleutel (KEK) genereren of importeren.

Zie het belangrijkste artikel [Voor het maken en configureren van een sleutelkluis voor Azure Disk Encryption](disk-encryption-key-vault.md) voor stappen over het installeren van [hulpprogramma's en het maken van verbinding met Azure.](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in de [vereisten voor Azure Disk Encryption CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en Azure Disk [Encryption-vereisten PowerShell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Een sleutelkluis maken 
Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om u te helpen de schijfversleutelingssleutels en -geheimen in uw key vault-abonnement te beheren en te beheren. U een sleutelkluis maken of een bestaande kluis gebruiken voor Azure Disk Encryption. Zie [Aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en Uw [sleutelkluis beveiligen](../../key-vault/general/secure-your-key-vault.md)voor meer informatie over sleutelkluizen. U een Resource Manager-sjabloon, Azure PowerShell of de Azure CLI gebruiken om een sleutelkluis te maken. 


>[!WARNING]
>Om ervoor te zorgen dat de versleutelingsgeheimen de regionale grenzen niet overschrijden, heeft Azure Disk Encryption de Key Vault en de VM's nodig om zich in dezelfde regio te bevinden. Maak en gebruik een Key Vault die zich in dezelfde regio bevindt als de VM die moet worden versleuteld. 


### <a name="create-a-key-vault-with-powershell"></a>Een sleutelkluis maken met PowerShell

U een sleutelkluis maken met Azure PowerShell met de cmdlet [Nieuw-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Zie [Az.KeyVault](/powershell/module/az.keyvault/)voor extra cmdlets voor Key Vault. 

1. Maak indien nodig een nieuwe resourcegroep met [Nieuwe AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Als u datacenterlocaties wilt weergeven, gebruikt u [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Maak een nieuwe sleutelkluis met [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Let op de **vault name**, Resource **Group Name**, **Resource ID,** **Vault URI**en de **Object ID** die worden geretourneerd voor later gebruik wanneer u de schijven versleutelt. 


### <a name="create-a-key-vault-with-azure-cli"></a>Een sleutelkluis maken met Azure CLI
U uw sleutelkluis beheren met Azure CLI met behulp van de opdrachten [az keyvault.](/cli/azure/keyvault#commands) Gebruik [az keyvault create](/cli/azure/keyvault#az-keyvault-create)om een sleutelkluis te maken.

1. Maak indien nodig een nieuwe resourcegroep met [de AZ-groep.](/cli/azure/group#az-group-create) Als u locaties wilt vermelden, gebruikt u [de lijstlocaties van az-account](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Maak een nieuwe sleutelkluis met [az keyvault create.](/cli/azure/keyvault#az-keyvault-create)
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Let op de **vault name** (naam), Resource **Group Name,** **Resource ID** (ID), **Vault URI**en de Object **ID** die later worden geretourneerd voor gebruik. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Een sleutelkluis maken met een resourcemanagersjabloon

U een sleutelkluis maken met de [sjabloon Resourcebeheer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik op de Azure-snelstartsjabloon op **Implementeren naar Azure**.
2. Selecteer het abonnement, de resourcegroep, de locatie van de resourcegroep, de naam van de sleutelkluis, object-id, juridische voorwaarden en overeenkomst en klik op **Kopen**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Een Azure AD-app en serviceprincipal instellen 
Wanneer u versleuteling nodig hebt om te worden ingeschakeld op een draaiende VM in Azure, genereert en schrijft Azure Disk Encryption de coderingssleutels naar uw sleutelkluis. Voor het beheren van versleutelingssleutels in uw sleutelkluis is Azure AD-verificatie vereist. Maak hiervoor een Azure AD-toepassing. Voor verificatiedoeleinden u verificatie op basis van clientgeheim of [azure AD-verificatie op basis van clientcertificaat](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)gebruiken.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Een Azure AD-app en serviceprincipal instellen met Azure PowerShell 
Als u de volgende opdrachten wilt uitvoeren, krijgt u de [Azure AD PowerShell-module](/powershell/azure/active-directory/install-adv2)en gebruikt u deze. 

1. Gebruik de [PowerShell-cmdlet Nieuw-AzADApplication](/powershell/module/az.resources/new-azadapplication) om een Azure AD-toepassing te maken. MyApplicationHomePage en de MyApplicationUri kunnen alle waarden zijn die u wenst.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. De $azureAdApplication.ApplicationId is de Azure AD ClientID en de $aadClientSecret is het clientgeheim dat u later gebruikt om Azure Disk Encryption in te schakelen. Bescherm het azure AD-clientgeheim op de juiste manier. Running `$azureAdApplication.ApplicationId` toont je de ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Een Azure AD-app en serviceprincipal instellen met Azure CLI

U uw serviceprincipals beheren met Azure CLI met behulp van de [az-ad sp-opdrachten.](/cli/azure/ad/sp) Zie [Een Azure-serviceprincipal maken](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

1. Maak een nieuwe serviceprincipal.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  De teruggegeven appId is de Azure AD ClientID die in andere opdrachten wordt gebruikt. Het is ook de SPN die je gebruikt voor het AZ Keyvault set-policy. Het wachtwoord is het clientgeheim dat u later moet gebruiken om Azure Disk Encryption in te schakelen. Bescherm het azure AD-clientgeheim op de juiste manier.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Een Azure AD-app en serviceprincipal instellen via de Azure-portal
Gebruik de stappen van de [portal Gebruiken om een Azure Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot het](../../active-directory/develop/howto-create-service-principal-portal.md) artikel over resources om een Azure AD-toepassing te maken. Elke stap hieronder zal u rechtstreeks naar het artikel sectie te voltooien. 

1. [Vereiste machtigingen verifiëren](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Een Azure Active Directory-toepassing maken](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - U elke naam en aanmeldings-URL gebruiken die u wilt bij het maken van de toepassing.
3. [Haal de toepassings-id en de verificatiesleutel](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)op. 
     - De verificatiesleutel is het clientgeheim en wordt gebruikt als AadClientSecret voor Set-AzVMDiskEncryptionExtension. 
        - De verificatiesleutel wordt door de toepassing gebruikt als referentie om u aan te melden bij Azure AD. In de Azure-portal wordt dit geheim sleutels genoemd, maar heeft het geen relatie met sleutelkluizen. Beveilig dit geheim op de juiste manier. 
     - De applicatie-id zal later worden gebruikt als de AadClientId voor Set-AzVMDiskEncryptionExtension en als de ServicePrincipalName voor Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Het toegangsbeleid voor de sleutelkluis voor de Azure AD-app instellen
Azure Disk Encryption heeft de client-id en het clientgeheim van de Azure Active Directory-toepassing met machtigingen nodig om geheimen te schrijven voor de Key Vault om versleuteling te schrijven naar een bepaalde Key Vault. 

> [!NOTE]
> Azure Disk Encryption vereist dat u het volgende toegangsbeleid configureert voor uw Azure AD-clienttoepassing: _WrapKey_ en _Set-machtigingen._

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Het toegangsbeleid voor belangrijke vaults voor de Azure AD-app instellen met Azure PowerShell
Uw Azure AD-toepassing heeft rechten nodig om toegang te krijgen tot de sleutels of geheimen in de kluis. Gebruik de cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om machtigingen toe te kennen aan de toepassing met behulp van de client-id (die is gegenereerd toen de toepassing werd geregistreerd) als de parameter _-ServicePrincipalName._ Zie het blogbericht [Azure Key Vault - Stap voor stap](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)voor meer informatie. 

1. Stel het toegangsbeleid voor de sleutelkluis voor de AD-toepassing in met PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Het toegangsbeleid voor belangrijke vaults voor de Azure AD-app instellen met Azure CLI
Gebruik [het az keyvault-setbeleid](/cli/azure/keyvault#az-keyvault-set-policy) om het toegangsbeleid in te stellen. Zie [Sleutelkluis beheren met CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)voor meer informatie.

Geef de serviceprincipal die u via de Azure CLI-toegang hebt gemaakt om geheimen te krijgen en sleutels te verpakken met de volgende opdracht:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Het toegangsbeleid voor belangrijke vaults voor de Azure AD-app instellen met de portal

1. Open de resourcegroep met uw sleutelkluis.
2. Selecteer uw sleutelkluis, ga naar **Toegangsbeleid**en klik op **Nieuw toevoegen**.
3. Zoek **onder Hoofd selecteren**naar de Azure AD-toepassing die u hebt gemaakt en selecteer deze. 
4. Voor **Sleutelmachtigingen**controleert u **Wrap Key** onder **Cryptografische bewerkingen**.
5. Voor **geheime machtigingen**schakelt u **Instellen** in onder Secret **Management Operations**.
6. Klik op **OK** om het toegangsbeleid op te slaan. 

![Azure Key Vault-cryptografische bewerkingen - Wrap-toets](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault Secret-machtigingen - Instellen](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangsbeleid voor de sleutelkluis instellen
Het Azure-platform heeft toegang nodig tot de versleutelingssleutels of -geheimen in uw sleutelkluis om ze beschikbaar te maken voor de VM voor het opstarten en decoderen van de volumes. Schijfversleuteling inschakelen op de sleutelkluis of implementaties mislukken.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Geavanceerde toegangsbeleidsregels voor belangrijke vault instellen met Azure PowerShell
 Gebruik de key vault PowerShell cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om schijfversleuteling voor de sleutelkluis in te schakelen.

  - **Key Vault inschakelen voor schijfversleuteling:** EnabledForDiskEncryption is vereist voor Azure Disk-versleuteling.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de Microsoft.Compute-resourceprovider geheimen ophalen uit deze sleutelkluis wanneer naar deze sleutelkluis wordt verwezen in het maken van resources, bijvoorbeeld bij het maken van een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Schakel Key Vault in voor sjabloonimplementatie, indien nodig:** Hiermee kan Azure Resource Manager geheimen uit deze sleutelkluis krijgen wanneer naar deze sleutelkluis wordt verwezen in een sjabloonimplementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Geavanceerde toegangsbeleidsregels voor sleutelkluizen instellen met behulp van de Azure CLI
Gebruik [de AZ KeyVault-update](/cli/azure/keyvault#az-keyvault-update) om schijfversleuteling voor de sleutelkluis in te schakelen. 

 - **Key Vault inschakelen voor schijfversleuteling:** Ingeschakeld-voor-schijf-encryptie is vereist. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Sta virtuele machines toe om certificaten op te halen die zijn opgeslagen als geheimen uit de kluis.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Schakel Key Vault in voor sjabloonimplementatie, indien nodig:** Laat Resource Manager geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Geavanceerde toegangsbeleidsregels voor sleutelkluizen instellen via de Azure-portal

1. Selecteer uw keyvault, ga naar **Toegangsbeleid**en **klik om geavanceerd toegangsbeleid weer te geven**.
2. Schakel het vak met het label **Toegang tot Azure-schijfversleuteling inschakelen in voor volumeversleuteling**.
3. Selecteer **Toegang tot Azure Virtual Machines inschakelen voor implementatie** en/of Toegang tot Azure Resource Manager inschakelen voor **sjabloonimplementatie**, indien nodig. 
4. Klik op **Opslaan**.

![Azure key vault advanced access policies](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Een sleutelversleutelingssleutel instellen (optioneel)
Als u een sleutelversleutelingssleutel (KEK) wilt gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, voegt u een KEK toe aan uw sleutelkluis. Gebruik de [cmdlet Add-AzKeyVaultKey Om](/powershell/module/az.keyvault/add-azkeyvaultkey) een sleutelversleutelingssleutel in de sleutelkluis te maken. U ook een KEK importeren van uw on-premises key management HSM. Zie [Key Vault Documentation voor](../../key-vault/keys/hsm-protected-keys.md)meer informatie. Wanneer een sleutelversleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel om de versleutelingsgeheimen te verpakken voordat u naar Key Vault gaat. 

* Gebruik bij het genereren van sleutels een RSA-sleuteltype. Azure Disk Encryption biedt nog geen ondersteuning voor elliptische curvetoetsen.

* Uw sleutelkluisgeheim en KEK-URL's moeten worden geversioneerd. Azure handhaaft deze beperking van versiebeheer. Zie de volgende voorbeelden voor geldige geheime en KEK-URL's:

  * Voorbeeld van een geldige geheime URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Voorbeeld van een geldige KEK-URL:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption ondersteunt geen ondersteuning voor het opgeven van poortnummers als onderdeel van sleutelkluisgeheimen en KEK-URL's. Zie de volgende voorbeelden voor niet-ondersteunde en ondersteunde URL's voor sleutelkluizen:

  * URL van onaanvaardbare sleutelkluis*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL van acceptabele sleutelkluis:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Een sleutelversleutelingssleutel instellen met Azure PowerShell 
Voordat u het PowerShell-script gebruikt, moet u bekend zijn met de vereisten voor Azure-schijfversleuteling om de stappen in het script te begrijpen. Het voorbeeldscript heeft mogelijk wijzigingen nodig voor uw omgeving. Dit script maakt alle vereisten voor Azure Disk Encryption en versleutelt een bestaande IaaS VM, waarmee de schijfversleutelingssleutel wordt verpakt met behulp van een sleutelversleutelingssleutel. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a>Verificatie op basis van certificaten (optioneel)
Als u certificaatverificatie wilt gebruiken, u deze uploaden naar uw sleutelkluis en deze implementeren naar de client. Voordat u het PowerShell-script gebruikt, moet u bekend zijn met de vereisten voor Azure-schijfversleuteling om de stappen in het script te begrijpen. Het voorbeeldscript heeft mogelijk wijzigingen nodig voor uw omgeving.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Verificatie op basis van certificaten en een KEK (optioneel)

Als u certificaatverificatie wilt gebruiken en de versleutelingssleutel wilt inpakken met een KEK, u het onderstaande script als voorbeeld gebruiken. Voordat u het PowerShell-script gebruikt, moet u bekend zijn met alle eerdere vereisten voor Azure-schijfversleuteling om de stappen in het script te begrijpen. Het voorbeeldscript heeft mogelijk wijzigingen nodig voor uw omgeving.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Volgende stappen

[Azure-schijfversleuteling inschakelen met Azure AD op Windows VM's (vorige release)](disk-encryption-windows-aad.md)
