---
title: Vereisten - Azure Disk Encryption voor IaaS-VM's | Microsoft Docs
description: Dit artikel bevat vereisten voor het gebruik van Microsoft Azure-schijfversleuteling voor IaaS-VM's.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: bd51e70122a5457c8dcf1bd8f2061fe25943e87f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224510"
---
# <a name="azure-disk-encryption-prerequisites"></a>Vereisten voor Azure Disk Encryption

In dit artikel, de vereisten voor Azure Disk Encryption, wordt beschreven welke items die worden voldaan moet voordat u Azure Disk Encryption kunt gebruiken. Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) voor het beheer van versleutelingssleutels. U kunt [Azure PowerShell](/powershell/azure/overview), [Azure CLI](/cli/azure/), of de [Azure-portal](https://portal.azure.com) Azure Disk Encryption te configureren.

Voordat u Azure Disk Encryption op Azure IaaS Virtual Machines voor de ondersteunde scenario's die zijn beschreven inschakelen in de [overzicht van Azure Disk Encryption](azure-security-disk-encryption-overview.md) artikel, zorg ervoor dat u de vereisten hebt voldaan. 

> [!WARNING]
> - Als u eerder Azure Disk Encryption hebt gebruikt [met Azure AD-App](azure-security-disk-encryption-prerequisites-aad.md) voor het versleutelen van deze VM, moet u deze optie blijven gebruiken om uw virtuele machine te versleutelen. U kunt [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) op deze versleutelde VM niet gebruiken omdat dit geen ondersteund scenario is, wat betekent dat het uitschakelen van de Aad-toepassing voor deze versleutelde virtuele machine niet wordt ondersteund.
> - Bepaalde aanbevelingen verhogen gegevens-, netwerk- of computerresources, wat resulteert in extra kosten in licentie of abonnement. U moet een geldige actief Azure-abonnement om resources te maken in Azure in de ondersteunde regio's hebben.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

Azure Disk Encryption is niet beschikbaar op [virtuele machines van de basis, A-serie](https://azure.microsoft.com/pricing/details/virtual-machines/series/). Azure Disk Encryption is beschikbaar op andere virtuele machines die voldoen aan de minimale geheugen vereisten:

| Virtuele Machine | Minimale geheugen vereiste |
|--|--|
| Virtuele Windows-machines | 2 GB |
| Virtuele Linux-machines wanneer gegevens volumes alleen worden versleuteld| 2 GB |
| Linux-Vm's bij het versleutelen van gegevens en OS-volumes, waarbij het gebruik van het basis-(/) bestands systeem 4 GB of minder is | 8 GB |
| Linux-Vm's bij het versleutelen van gegevens en besturingssysteem volumes, waarbij het gebruik van het root-(/) bestands systeem groter is dan 4 GB | Het root-bestandssysteem gebruik * 2. Een voor beeld: voor een 16 GB aan root File System-gebruik is ten minste GB RAM vereist |

Zodra het versleutelings proces van de besturingssysteem schijf is voltooid op virtuele Linux-machines, kan de virtuele machine worden geconfigureerd om te worden uitgevoerd met minder geheugen. 

> [!NOTE]
> Versleuteling van Linux-besturingssysteem schijf is niet beschikbaar voor [Virtual Machine Scale sets](../virtual-machine-scale-sets/index.yml).

Azure Disk Encryption is ook beschikbaar voor virtuele machines met Premium Storage. 

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

### <a name="windows"></a>Windows

- Windows-client: Windows 8 en hoger.
- Windows Server: Windows Server 2008 R2 of hoger.  
 
> [!NOTE]
> Voor Windows Server 2008 R2 moet .NET Framework 4,5 zijn geïnstalleerd voor versleuteling. Installeer het van Windows Update met de optionele update Microsoft .NET Framework 4.5.2 voor Windows Server 2008 R2 x64-systemen ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 core en Windows Server 2016 core vereist dat het onderdeel bdehdcfg op de virtuele machine wordt geïnstalleerd voor versleuteling.


### <a name="linux"></a>Linux 

Azure Disk Encryption wordt ondersteund op een subset van de door [Azure goedgekeurde Linux-distributies](../virtual-machines/linux/endorsed-distros.md). Dit is een subset van alle mogelijke distributies van Linux server.

![Venn-diagram van Linux-server distributies die ondersteuning bieden voor Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Linux-server distributies die niet zijn goedgekeurd door Azure, bieden geen ondersteuning voor Azure Disk Encryption en, van degenen die worden goedgekeurd, alleen de volgende distributies en versies ondersteunen Azure Disk Encryption:

| Linux-distributie | Versie | Volumetype wordt ondersteund voor versleuteling|
| --- | --- |--- |
| Ubuntu | 18,04| Besturingssysteem- en schijf |
| Ubuntu | 16.04| Besturingssysteem- en schijf |
| Ubuntu | 14.04.5</br>[met een afgestemde kernel van Azure bijgewerkt tot 4,15 of hoger](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Besturingssysteem- en schijf |
| RHEL | 7,6 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.5 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.4 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.3 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 7.2 | Besturings systeem en gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.8 | Gegevens schijf (zie opmerking hieronder) |
| RHEL | 6.7 | Gegevens schijf (zie opmerking hieronder) |
| CentOS | 7,6 | Besturingssysteem- en schijf |
| CentOS | 7.5 | Besturingssysteem- en schijf |
| CentOS | 7.4 | Besturingssysteem- en schijf |
| CentOS | 7.3 | Besturingssysteem- en schijf |
| CentOS | 7.2n | Besturingssysteem- en schijf |
| CentOS | 6.8 | Gegevensschijf |
| openSUSE | 42,3 | Gegevensschijf |
| SLES | 12-SP4 | Gegevensschijf |
| SLES | 12-SP3 | Gegevensschijf |

> [!NOTE]
> De nieuwe ADE-implementatie wordt ondersteund voor RHEL-besturings systeem en gegevens schijf voor RHEL7 betalen per gebruik-installatie kopieën. ADE wordt momenteel niet ondersteund voor RHEL (BYOS)-installatie kopieën van uw eigen abonnement. Zie [Azure Disk Encryption voor Linux](azure-security-disk-encryption-linux.md) voor meer informatie.

- Azure Disk Encryption is vereist dat uw sleutelkluis en de virtuele machines zich in de dezelfde Azure-regio en het abonnement bevinden. Configureren van de resources in verschillende regio's, zorgt ervoor dat een fout opgetreden bij het inschakelen van de Azure Disk Encryption-functie.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Aanvullende vereisten voor virtuele Linux IaaS-machines 

- Azure Disk Encryption vereist dat de DM-cryptografie-en vfat-modules aanwezig zijn op het systeem. Als u vfat uit de standaard installatie kopie verwijdert of uitschakelt, wordt het sleutel volume niet door het systeem gelezen en wordt de benodigde sleutel voor het ontgrendelen van de schijven bij de volgende keer opnieuw opstarten voor komen. De stappen voor het beveiligen van het systeem die de vfat-module van het systeem verwijderen, zijn niet compatibel met Azure Disk Encryption. 
- Voordat u versleuteling inschakelt, moeten de gegevensschijven moeten worden versleuteld goed worden weergegeven in/etc/fstab. Gebruik een permanente blok apparaatnaam op voor deze vermelding als apparaat namen in de indeling '/ dev/sdX' kunnen niet worden gebruikt om te worden gekoppeld aan dezelfde schijf tijdens opnieuw opstarten, met name als versleuteling wordt toegepast. Zie voor meer informatie over dit gedrag: [Naam wijzigingen van Linux VM-apparaat oplossen](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Zorg ervoor dat de/etc/fstab-instellingen juist zijn geconfigureerd voor koppelen. Deze instellingen configureren, uitvoeren van de opdracht mount- of opnieuw opstarten van de virtuele machine en activeren van de beschadigingsgebeurtenissen op die manier. Zodra dat is voltooid, controleert u de uitvoer van de opdracht lsblk om te controleren dat de schijf nog steeds is gekoppeld. 
  - Als het bestand/etc/fstab niet naar behoren het station koppelen voordat versleuteling is ingeschakeld, kunnen Azure Disk Encryption correct koppelen niet mogelijk.
  - De Azure Disk Encryption-proces worden de mount-gegevens uit/etc/fstab en in een eigen configuratiebestand als onderdeel van het versleutelingsproces verplaatst. Geen worden zorgen om te zien van de vermelding ontbreekt in/etc/fstab nadat gegevens stationsversleuteling is voltooid.
  - Voordat u begint met het versleutelen, moet u alle services en processen die kunnen schrijven naar gekoppelde gegevens schijven stoppen en uitschakelen, zodat ze niet automatisch opnieuw worden opgestart na het opnieuw opstarten. Hierdoor blijven bestanden geopend op deze partities, waardoor de versleutelings procedure niet opnieuw kan worden gekoppeld, waardoor de versleuteling mislukt. 
  - Het duurt na opnieuw opstarten, tijd voor de Azure Disk Encryption-proces om de zojuist versleutelde schijven te koppelen. Ze zijn niet meteen beschikbaar na het opnieuw opstarten. Het proces tijd om te beginnen, ontgrendelen en koppel vervolgens de versleutelde schijven voordat deze beschikbaar is voor andere processen voor toegang tot nodig heeft. Dit proces duurt langer dan een minuut na opnieuw opstarten, afhankelijk van de kenmerken van het systeem.

Een voorbeeld van de opdrachten die kan worden gebruikt om de gegevensschijven koppelen en de benodigde/etc/fstab-vermeldingen te maken kunt u vinden [regels 244-248 van dit scriptbestand](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Netwerk- en Groepsbeleid

**Functie, de IaaS-VM's moet voldoen aan de volgende configuratievereisten voor netwerk-eindpunt voor de Azure Disk Encryption inschakelen:**
  - Als u een token voor het verbinding maken met uw key vault, moet de IaaS-VM geen verbinding maken met een Azure Active Directory-eindpunt, zijn \[login.microsoftonline.com\].
  - Als de versleutelingssleutels wilt opslaan op uw key vault, moet de IaaS-VM geen verbinding maken met het eindpunt van de sleutelkluis.
  - De IaaS-VM moet geen verbinding maken met een Azure storage-eindpunt dat als host fungeert voor de Azure-extensie-opslagplaats en een Azure storage-account dat als host fungeert voor de VHD-bestanden.
  -  Als uw beveiligingsbeleid beperkt de toegang van Azure VM's tot het Internet, kunt u deze kunt oplossen door de voorgaande URI en configureren van een specifieke regel voor het toestaan van uitgaande verbinding met de IP-adressen. Zie voor meer informatie, [Azure Key Vault achter een firewall](../key-vault/key-vault-access-behind-firewall.md).    


**Groepsbeleid:**
 - De Azure Disk Encryption-oplossing maakt gebruik van de externe BitLocker-sleutelbeveiliging voor Windows IaaS-VM's. Voor virtuele machines van een domein, niet een Groepsbeleid TPM beveiligingstoepassingen afdwingen pushen. Zie voor meer informatie over het groepsbeleid voor "Toestaan BitLocker zonder een compatibele TPM" [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Het BitLocker-beleid voor virtuele machines die lid zijn van een domein met aangepast groeps beleid moet de volgende instelling bevatten: [Gebruikers opslag configureren van BitLocker-herstel gegevens-> 256-bits herstel sleutel toestaan](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption mislukt wanneer aangepaste groeps beleids instellingen voor BitLocker incompatibel zijn. Het nieuwe beleid toepassen op virtuele machines die niet de juiste instelling hebben, en vervolgens opnieuw te starten is mogelijk vereist afdwingen dat het nieuwe beleid om bij te werken (gpupdate.exe/Force).

- Azure Disk Encryption mislukt als groeps beleid op domein niveau het AES-CBC-algoritme blokkeert dat door BitLocker wordt gebruikt.


## <a name="bkmk_PSH"></a> Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) biedt een set cmdlets die gebruikmaakt van de [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) model voor het beheren van uw Azure-resources. U kunt deze gebruiken in uw browser met [Azure Cloud Shell](../cloud-shell/overview.md), of kunt u deze installeren op uw lokale computer met behulp van de onderstaande instructies om te worden gebruikt in een PowerShell-sessie. Als u al deze lokaal geïnstalleerd hebt, zorg er dan voor dat u de nieuwste versie van Azure PowerShell SDK-versie om te configureren van Azure Disk Encryption. Download de nieuwste versie van [Azure PowerShell versie](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Azure PowerShell installeren voor gebruik op uw lokale computer (optioneel): 
1. Volg de instructies in de koppelingen voor uw besturingssysteem wordt voortgezet maar de rest van de onderstaande stappen.      
   - [Installeer en configureer Azure PowerShell](/powershell/azure/install-az-ps). 
     - Installeer PowerShellGet, Azure PowerShell en laad de AZ-module. 

2. Controleer de geïnstalleerde versies van de AZ-module. Indien nodig, [bijwerken van de Azure PowerShell-module](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    U wordt aangeraden de nieuwste AZ-module versie te gebruiken.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Meld u aan bij Azure met de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  Indien nodig, Bekijk [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Installeer de Azure CLI voor gebruik op uw lokale computer (optioneel)

De [Azure CLI 2.0](/cli/azure) is een opdrachtregelprogramma voor het beheren van Azure-resources. De CLI is ontworpen voor flexibel gegevens op te vragen, langdurige bewerkingen als niet-blokkerende processen ondersteunen en scripts eenvoudig uitvoeren. U kunt PowerShell in uw browser gebruiken met [Azure Cloud Shell](../cloud-shell/overview.md), of installeren op uw lokale computer en dan gebruiken in een PowerShell-sessie.

1. [Azure CLI installeren](/cli/azure/install-azure-cli) voor gebruik op uw lokale computer (optioneel):

2. Controleer of de geïnstalleerde versie.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Aanmelden bij Azure met [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Beoordeling [aan de slag met Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) indien nodig. 


## <a name="prerequisite-workflow-for-key-vault"></a>Vereiste werkstroom voor Key Vault
Als u al bekend met de Key Vault en Azure AD-vereisten voor Azure Disk Encryption bent, kunt u de [PowerShell script met vereisten voor Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Zie voor meer informatie over het gebruik van het script met vereisten voor de [een snelstartgids van Virtual machines versleutelen](azure-disk-encryption-linux-powershell-quickstart.md) en de [Azure Disk Encryption bijlage](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Indien nodig, kunt u een resourcegroep maken.
2. Een sleutelkluis maken. 
3. Set-sleutelkluis geavanceerde toegangsbeleid.

>[!WARNING]
>Voordat u een key vault verwijdert, zorg ervoor dat u kunt bestaande virtuele machines met het heeft niet versleutelen. Een kluis beveiligen tegen onopzettelijk verwijderen [voorlopig verwijderen inschakelen](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) en een [resourcevergrendeling](../azure-resource-manager/resource-group-lock-resources.md) op de kluis. 
 
## <a name="bkmk_KeyVault"></a> Een sleutelkluis maken 
Azure Disk Encryption is geïntegreerd met [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) om te controleren en beheren van de schijf-versleutelingssleutels en geheimen in uw key vault-abonnement. U kunt een key vault maken of gebruik een bestaande resourcegroep voor Azure Disk Encryption. Zie voor meer informatie over sleutelkluizen [aan de slag met Azure Key Vault](../key-vault/key-vault-get-started.md) en [uw key vault beveiligen](../key-vault/key-vault-secure-your-key-vault.md). U kunt een Resource Manager-sjabloon, Azure PowerShell of Azure CLI gebruiken om een sleutelkluis te maken. 


>[!WARNING]
>Om te verzekeren dat de geheimen van de versleuteling niet overschreden door de regionale grenzen, moet Azure Disk Encryption de Key Vault en de virtuele machines in dezelfde regio worden geplaatst. Maak en gebruik van een Key Vault die zich in dezelfde regio als de virtuele machine moeten worden versleuteld. 


### <a name="bkmk_KVPSH"></a> Een sleutelkluis maken met PowerShell

U kunt een sleutel kluis maken met Azure PowerShell met behulp van de cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Zie [AZ. sleutel kluis](/powershell/module/az.keyvault/)voor extra cmdlets voor Key Vault. 

1. Indien nodig, [verbinding maken met uw Azure-abonnement](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Maak indien nodig een nieuwe resource groep met [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Gebruik [Get-AzLocation](/powershell/module/az.resources/get-azlocation)om de locaties van data centers weer te geven. 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Een nieuwe sleutel kluis maken met [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Houd er rekening mee de **Kluisnaam**, **Resourcegroepnaam**, **Resource-ID**, **Vault URI**, en de **Object-ID** die worden geretourneerd voor later gebruik wanneer u de schijven versleutelen. 


### <a name="bkmk_KVCLI"></a> Een sleutelkluis maken met Azure CLI
U kunt uw key vault met behulp van Azure CLI beheren de [az keyvault](/cli/azure/keyvault#commands) opdrachten. Gebruik voor het maken van een key vault [az keyvault maken](/cli/azure/keyvault#az-keyvault-create).

1. Indien nodig, [verbinding maken met uw Azure-abonnement](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Maak een nieuwe resourcegroep, indien nodig, met [az-groep maken](/cli/azure/group#az-group-create). Aan de lijst met locaties, gebruikt u [az account list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Maak een nieuwe sleutelkluis met [az keyvault maken](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Houd er rekening mee de **Kluisnaam** (naam), **groepsnaam voor Accountresources**, **Resource-ID** (ID), **Vault URI**, en de **Object-ID** die later worden geretourneerd voor gebruik. 

### <a name="bkmk_KVRM"></a> Een sleutelkluis maken met Resource Manager-sjabloon

U kunt een sleutelkluis maken met behulp van de [Resource Manager-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Klik op de Azure-quickstart-sjabloon, **implementeren in Azure**.
2. Selecteer het abonnement, resourcegroeplocatie voor resourcegroep, Key Vault-naam, Object-ID, juridische voorwaarden en overeenkomst en klik vervolgens op **aankoop**. 


## <a name="bkmk_KVper"></a> Geavanceerde toegangsbeleid voor sleutelkluis instellen
Het Azure-platform moet toegang hebben tot de sleutels of geheimen in uw key vault zodat ze beschikbaar voor de virtuele machine voor opstarten en ontsleutelen van de volumes. Schijf-versleuteling inschakelen voor de sleutelkluis of implementaties mislukken.  

### <a name="bkmk_KVperPSH"></a> Sleutelkluis set geavanceerde toegangsbeleid met Azure PowerShell
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

### <a name="bkmk_KVperCLI"></a> Sleutelkluis set geavanceerde toegangsbeleid met de Azure CLI
Gebruik [az keyvault update](/cli/azure/keyvault#az-keyvault-update) om in te schakelen schijfversleuteling voor de key vault. 

 - **Key Vault voor schijf versleuteling inschakelen:** Ingeschakeld-voor-schijf versleuteling is vereist. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Schakel Key Vault in voor implementatie, indien nodig:** Hiermee kan de micro soft. Compute-resource provider geheimen ophalen van deze sleutel kluis wanneer er naar deze sleutel kluis wordt verwezen bij het maken van een virtuele machine.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Schakel Key Vault voor sjabloon implementatie in, indien nodig:** Resource Manager mag geheimen uit de kluis ophalen.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Sleutelkluis set geavanceerde toegangsbeleid via Azure portal

1. Selecteer uw Key Vault, gaat u naar **toegangsbeleid**, en **Klik hierop om geavanceerde Toegangsbeleidsregels**.
2. Selecteer het selectievakje **toegang tot Azure Disk Encryption voor volumeversleuteling**.
3. Selecteer **toegang tot Azure Virtual Machines voor implementatie inschakelen** en/of **inschakelen toegang tot Azure Resource Manager voor sjabloonimplementatie**, indien nodig. 
4. Klik op **Opslaan**.

    ![Azure-sleutelkluis geavanceerde toegangsbeleid](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Instellen van een sleutel van versleutelingssleutel (optioneel)
Als u wilt een sleutel key-versleuteling (KEK) te gebruiken voor een extra beveiligingslaag voor versleutelingssleutels, moet u een KEK-sleutel toevoegen aan uw key vault. Gebruik de cmdlet [add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) om een sleutel versleutelings sleutel te maken in de sleutel kluis. U kunt ook een KEK-sleutel van uw on-premises Sleutelbeheer HSM importeren. Zie voor meer informatie, [Key Vault-documentatie](../key-vault/key-vault-hsm-protected-keys.md). Wanneer een sleutel van versleutelingssleutel is opgegeven, gebruikt Azure Disk Encryption die sleutel het verpakken van de geheimen van de versleuteling voor het schrijven naar de Key Vault.

* Gebruik een RSA-sleutel type bij het genereren van sleutels. Azure Disk Encryption biedt nog geen ondersteuning voor het gebruik van elliptische curve toetsen.

* Uw key vault-geheim en de KEK-sleutel-URL's moeten worden bijgehouden. Azure wordt deze beperking van versiebeheer afgedwongen. Zie de volgende voorbeelden voor geldige geheim en KEK-sleutel-URL's:

  * Voorbeeld van een geldige geheime URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Voorbeeld van een geldige KEK-sleutel-URL:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption biedt geen ondersteuning voor het opgeven van de poortnummers als onderdeel van de key vault-geheimen en KEK-sleutel-URL's. Zie voor voorbeelden van niet-ondersteunde en ondersteunde key vault-URL's in de volgende voorbeelden:

  * URL voor key vault niet toegestaan.  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL voor acceptabel key vault:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Instellen van een sleutel van versleutelingssleutel met Azure PowerShell 
Voordat u het PowerShell-script gebruikt, moet u bekend bent met de Azure Disk Encryption-vereisten voor het begrijpen van de stappen in het script. Het voorbeeldscript mogelijk wijzigingen voor uw omgeving. Dit script maakt alle vereisten voor Azure Disk Encryption en een bestaande IaaS-VM, de versleutelingssleutel van de schijf met behulp van een sleutel van versleutelingssleutel wrapping versleutelt. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Schakel Azure Disk Encryption voor Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Schakel Azure Disk Encryption voor Linux](azure-security-disk-encryption-linux.md)

