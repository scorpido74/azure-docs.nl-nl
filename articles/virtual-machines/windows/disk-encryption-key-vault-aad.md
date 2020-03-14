---
title: Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)
description: Dit artikel bevat vereisten voor het gebruik van Microsoft Azure-schijfversleuteling voor IaaS-VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243378"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Een sleutel kluis maken en configureren voor Azure Disk Encryption met Azure AD (vorige versie)

**De nieuwe versie van Azure Disk Encryption elimineert de vereiste voor het opgeven van een Azure AD-toepassings parameter om VM-schijf versleuteling in te scha kelen. Met de nieuwe versie hoeft u geen Azure AD-referenties meer op te geven tijdens het inschakelen van de versleutelings stap. Alle nieuwe Vm's moeten worden versleuteld zonder de para meters van de Azure AD-toepassing met de nieuwe versie. Zie [Azure Disk Encryption](disk-encryption-overview.md)als u instructies wilt weer geven voor het inschakelen van VM-schijf versleuteling met de nieuwe versie. Vm's die al zijn versleuteld met Azure AD-toepassings parameters, worden nog steeds ondersteund en blijven behouden met de AAD-syntaxis.**

Azure Disk Encryption gebruikt Azure Key Vault om sleutels en geheimen voor schijf versleuteling te beheren en te beheren.  Zie [aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en [uw sleutel kluis beveiligen](../../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over sleutel kluizen. 

Een sleutel kluis maken en configureren voor gebruik Azure Disk Encryption met Azure AD (eerdere versie) omvat drie stappen:

1. Een sleutelkluis maken. 
2. Instellen van een Azure AD-toepassing en service-principal.
3. Het toegangsbeleid voor key vault voor de Azure AD-app ingesteld.
4. Set-sleutelkluis geavanceerde toegangsbeleid.
 
U kunt eventueel ook een sleutel versleutelings sleutel genereren of importeren (KEK).

Zie de belangrijkste procedure [voor het maken en configureren van een sleutel kluis voor Azure Disk Encryption](disk-encryption-key-vault.md) artikel voor meer informatie over het [installeren van hulpprogram ma's en het maken van verbinding met Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> De stappen in dit artikel zijn geautomatiseerd in het [Azure Disk Encryption vereisten CLI-script](https://github.com/ejarvi/ade-cli-getting-started) en [Azure Disk Encryption vereisten Power shell-script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Een sleutelkluis maken 
Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , zodat u de schijf versleutelings sleutels en geheimen in uw sleutel kluis abonnement kunt controleren en beheren. U kunt een key vault maken of gebruik een bestaande resourcegroep voor Azure Disk Encryption. Zie [aan de slag met Azure Key Vault](../../key-vault/key-vault-get-started.md) en [uw sleutel kluis beveiligen](../../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over sleutel kluizen. U kunt een Resource Manager-sjabloon, Azure PowerShell of Azure CLI gebruiken om een sleutelkluis te maken. 


>[!WARNING]
>Om te verzekeren dat de geheimen van de versleuteling niet overschreden door de regionale grenzen, moet Azure Disk Encryption de Key Vault en de virtuele machines in dezelfde regio worden geplaatst. Maak en gebruik van een Key Vault die zich in dezelfde regio als de virtuele machine moeten worden versleuteld. 


### <a name="create-a-key-vault-with-powershell"></a>Een sleutel kluis maken met Power shell

U kunt een sleutel kluis maken met Azure PowerShell met behulp van de cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Zie [AZ. sleutel kluis](/powershell/module/az.keyvault/)voor extra cmdlets voor Key Vault. 

1. Maak indien nodig een nieuwe resource groep met [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Gebruik [Get-AzLocation](/powershell/module/az.resources/get-azlocation)om de locaties van data centers weer te geven. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Een nieuwe sleutel kluis maken met [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Noteer de **naam**van de kluis, de naam van de **resource groep**, de **resource-id**, de **kluis-URI**en de **object-id** die wordt geretourneerd voor later gebruik wanneer u de schijven versleutelt. 


### <a name="create-a-key-vault-with-azure-cli"></a>Een sleutel kluis maken met Azure CLI
U kunt uw sleutel kluis beheren met Azure CLI met behulp van de opdracht [AZ-kluis](/cli/azure/keyvault#commands) . Gebruik [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create)om een sleutel kluis te maken.

1. Maak indien nodig een nieuwe resource groep met [AZ Group Create](/cli/azure/group#az-group-create). Gebruik [AZ account list-locations](/cli/azure/account#az-account-list) om locaties weer te geven 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Maak een nieuwe sleutel kluis met [AZ Key kluis Create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Noteer de naam van de **kluis** (naam), de naam van de **resource groep**, de **resource-id** (id), de **kluis-URI**en de **object-id** die wordt geretourneerd voor later gebruik. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Een sleutel kluis maken met een resource manager-sjabloon

U kunt een sleutel kluis maken met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik in de Azure Quick Start-sjabloon op **implementeren naar Azure**.
2. Selecteer het abonnement, de resource groep, de locatie van de resource groep, de Key Vault naam, de object-ID, de juridische voor waarden en de overeenkomst en klik vervolgens op **kopen**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Een Azure AD-app en service-principal instellen 
Wanneer u versleuteling wordt ingeschakeld op een actieve virtuele machine in Azure nodig hebt, wordt Azure Disk Encryption genereert en schrijft de versleutelingssleutels in uw key vault. Beheer van versleutelingssleutels in uw key vault, vereist Azure AD-verificatie. Maak een Azure AD-toepassing voor dit doel. Voor verificatie doeleinden kunt u verificatie op basis van client geheim of [Azure AD-verificatie op basis van client certificaten](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)gebruiken.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Een Azure AD-app en-service-principal instellen met Azure PowerShell 
Als u de volgende opdrachten wilt uitvoeren, moet u de [Azure AD Power shell-module](/powershell/azure/active-directory/install-adv2)ophalen en gebruiken. 

1. Gebruik de Power shell [-cmdlet New-AzADApplication](/powershell/module/az.resources/new-azadapplication) om een Azure AD-toepassing te maken. MyApplicationHomePage en de MyApplicationUri mag geen waarden die u wilt.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. De $azureAdApplication.ApplicationId is de ClientID van Azure AD en de $aadClientSecret is het clientgeheim die u later gebruiken wilt voor Azure Disk Encryption inschakelen. Het clientgeheim van de Azure AD op de juiste manier beveiligen. Als u `$azureAdApplication.ApplicationId` uitvoert, wordt de ApplicationID weer gegeven.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Een Azure AD-app en-service-principal instellen met Azure CLI

U kunt de service-principals beheren met Azure CLI met behulp van de [AZ AD SP](/cli/azure/ad/sp) -opdrachten. Zie [een Azure-service-principal maken](/cli/azure/create-an-azure-service-principal-azure-cli)voor meer informatie.

1. Maak een nieuwe serviceprincipal.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  De toepassings-id geretourneerd aan de ClientID van Azure AD wordt gebruikt in andere opdrachten. Het is ook de SPN die u voor az keyvault-beleid instellen gebruikt. Het wachtwoord is het clientgeheim die u later gebruiken moet om Azure Disk Encryption inschakelen. Het clientgeheim van de Azure AD op de juiste manier beveiligen.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Een Azure AD-app en service-principal instellen, hoewel de Azure Portal
Gebruik de stappen in de [Portal gebruiken om een Azure Active Directory-toepassing en Service-Principal te maken die toegang hebben tot bronnen](../../active-directory/develop/howto-create-service-principal-portal.md) artikelen om een Azure AD-toepassing te maken. Elke stap hieronder gaat u rechtstreeks naar de sectie om te voltooien. 

1. [Vereiste machtigingen verifiëren](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Een Azure Active Directory-toepassing maken](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - U kunt een willekeurige naam gebruiken en aanmeldings-URL die u dat wilt bij het maken van de toepassing.
3. [Haal de toepassings-id en de verificatie sleutel](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in)op. 
     - De verificatie sleutel is het client geheim en wordt gebruikt als de AadClientSecret voor set-AzVMDiskEncryptionExtension. 
        - De verificatiesleutel wordt gebruikt door de toepassing als een referentie zich aanmeldt bij Azure AD. Dit geheim wordt aangeroepen sleutels in de Azure-portal, maar heeft geen relatie tot sleutelkluizen. Dit geheim op de juiste manier beveiligen. 
     - De toepassings-ID wordt later gebruikt als AadClientId voor set-AzVMDiskEncryptionExtension en als ServicePrincipalName voor set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Het toegangs beleid voor de sleutel kluis instellen voor de Azure AD-app
Azure Disk Encryption moet encryption geheimen worden geschreven naar een opgegeven Key Vault, de Client-ID en het Clientgeheim van de Azure Active Directory-toepassing met machtigingen voor geheimen schrijven naar de Key Vault. 

> [!NOTE]
> Azure Disk Encryption moet u het volgende toegangs beleid configureren voor uw Azure AD-client toepassing: _WrapKey_ en machtigingen _instellen_ .

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Stel het toegangs beleid voor de sleutel kluis in voor de Azure AD-app met Azure PowerShell
Uw Azure AD-toepassing moet de rechten voor toegang tot de sleutels of geheimen in de kluis. Gebruik de cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om machtigingen toe te kennen aan de toepassing, met behulp van de client-id (die is gegenereerd toen de toepassing is geregistreerd) als de parameter waarde _– ServicePrincipalName_ . Zie voor meer informatie het blog bericht [Azure Key Vault-stap voor stap](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Het toegangsbeleid voor key vault voor de AD-toepassing met PowerShell instellen.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Het toegangs beleid voor de sleutel kluis instellen voor de Azure AD-app met Azure CLI
Gebruik [AZ sleutel kluis set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) om het toegangs beleid in te stellen. Zie [Key Vault beheren met CLI 2,0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret)voor meer informatie.

Geef de service-principal die u hebt gemaakt via de Azure CLI-toegang tot het ophalen van geheimen en tekstterugloop sleutels met de volgende opdracht:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Het toegangs beleid voor de sleutel kluis instellen voor de Azure AD-app met de portal

1. Open de resourcegroep met uw key vault.
2. Selecteer uw sleutel kluis, ga naar **toegangs beleid**en klik vervolgens op **nieuwe toevoegen**.
3. Zoek onder **Principal selecteren**naar de Azure AD-toepassing die u hebt gemaakt en selecteer deze. 
4. Voor **sleutel machtigingen**controleert u de **toets SHIFT** onder **cryptografische bewerkingen**.
5. Controleer bij **geheime machtigingen** **instellen** onder **geheime beheer bewerkingen**.
6. Klik op **OK** om het toegangs beleid op te slaan. 

![Azure Key Vault cryptografische bewerkingen - sleutel Inpakken](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault-geheim machtigingen - instellen](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen
Het Azure-platform moet toegang hebben tot de sleutels of geheimen in uw key vault zodat ze beschikbaar voor de virtuele machine voor opstarten en ontsleutelen van de volumes. Schijf-versleuteling inschakelen voor de sleutelkluis of implementaties mislukken.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen met Azure PowerShell
 Gebruik de sleutel kluis Power shell [-cmdlet Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) om schijf versleuteling in te scha kelen voor de sleutel kluis.

  - **Key Vault voor schijf versleuteling inschakelen:** EnabledForDiskEncryption is vereist voor Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de micro soft. Compute-resource provider geheimen ophalen van deze sleutel kluis wanneer er naar deze sleutel kluis wordt verwezen bij het maken van een virtuele machine.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Schakel Key Vault voor sjabloon implementatie in, indien nodig:** Hiermee kunnen Azure Resource Manager geheimen van deze sleutel kluis ophalen wanneer naar deze sleutel kluis wordt verwezen in een sjabloon implementatie.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen met behulp van Azure CLI
Gebruik [AZ Key kluis update](/cli/azure/keyvault#az-keyvault-update) om schijf versleuteling in te scha kelen voor de sleutel kluis. 

 - **Key Vault voor schijf versleuteling inschakelen:** Ingeschakeld-voor-schijf versleuteling is vereist. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee staat u toe dat Virtual Machines certificaten kunt ophalen die zijn opgeslagen als geheimen van de kluis.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Schakel Key Vault voor sjabloon implementatie in, indien nodig:** Resource Manager mag geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Geavanceerd toegangs beleid voor sleutel kluis instellen via de Azure Portal

1. Selecteer uw sleutel kluis, ga naar **toegangs beleid**en **Klik om geavanceerd toegangs beleid weer te geven**.
2. Schakel het selectie vakje **toegang tot Azure Disk Encryption voor volume versleuteling inschakelen**in.
3. Selecteer **toegang tot Azure virtual machines inschakelen voor implementatie** en/of **toegang tot Azure Resource Manager voor sjabloon implementatie**inschakelen, indien nodig. 
4. Klik op **Opslaan**.

![Azure-sleutelkluis geavanceerde toegangsbeleid](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Een Key Encryption Key instellen (optioneel)
Als u wilt een sleutel key-versleuteling (KEK) te gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, moet u een KEK-sleutel toevoegen aan uw key vault. Gebruik de cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om een sleutel versleutelings sleutel te maken in de sleutel kluis. U kunt ook een KEK-sleutel van uw on-premises Sleutelbeheer HSM importeren. Zie [Key Vault-documentatie](../../key-vault/key-vault-hsm-protected-keys.md)voor meer informatie. Wanneer een sleutel van versleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel het verpakken van de geheimen van de versleuteling voor het schrijven naar de Key Vault. 

* Gebruik een RSA-sleutel type bij het genereren van sleutels. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van elliptische curve toetsen.

* Uw key vault-geheim en de KEK-sleutel-URL's moeten worden bijgehouden. Azure wordt deze beperking van versiebeheer afgedwongen. Zie de volgende voorbeelden voor geldige geheim en KEK-sleutel-URL's:

  * Voor beeld van een geldige geheime URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Voor beeld van een geldige KEK-URL: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption biedt geen ondersteuning voor het opgeven van de poortnummers als onderdeel van de key vault-geheimen en KEK-sleutel-URL's. Zie voor voorbeelden van niet-ondersteunde en ondersteunde key vault-URL's in de volgende voorbeelden:

  * Onacceptabele URL voor sleutel kluis *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Aanvaard bare sleutel kluis-URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Een coderings sleutel met Azure PowerShell instellen 
Voordat u het PowerShell-script gebruikt, moet u bekend bent met de Azure Disk Encryption-vereisten voor het begrijpen van de stappen in het script. Het voorbeeldscript mogelijk wijzigingen voor uw omgeving. Dit script maakt alle vereisten voor Azure Disk Encryption en een bestaande IaaS-VM, de versleutelingssleutel van de schijf met behulp van een sleutel van versleutelingssleutel wrapping versleutelt. 

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
Als u wilt om certificaatverificatie te gebruiken, kunt u een voor uw key vault uploaden en deze implementeren naar de client. Voordat u het PowerShell-script gebruikt, moet u bekend bent met de Azure Disk Encryption-vereisten voor het begrijpen van de stappen in het script. Het voorbeeldscript mogelijk wijzigingen voor uw omgeving.

     
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

Als u wilt het gebruik van certificaatverificatie en de versleutelingssleutel met een KEK-sleutel inpakken, kunt u het onderstaande script als voorbeeld. Voordat u het PowerShell-script gebruikt, moet u bekend bent met alle van de vorige vereisten voor Azure Disk Encryption wilt weten welke stappen in het script. Het voorbeeldscript mogelijk wijzigingen voor uw omgeving.

     
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

[Azure Disk Encryption met Azure AD inschakelen op Windows-Vm's (vorige versie)](disk-encryption-windows-aad.md)
