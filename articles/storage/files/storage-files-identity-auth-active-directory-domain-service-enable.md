---
title: Azure AD Domain Services gebruiken om de toegang tot bestandsgegevens via SMB te autoriseren
description: Meer informatie over het inschakelen van verificatie op basis van identiteit via Server Message Block (SMB) voor Azure Files via Azure Active Directory Domain Services. Uw virtuele Windows-machines (VM's) met domeinlid Windows hebben vervolgens toegang tot Azure-bestandsshares met behulp van Azure AD-referenties.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: cb173bcbf7cd163dca16c211d45018e0fe056edd
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666857"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Azure Active Directory Domain Services-verificatie inschakelen in Azure-bestanden

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Zie [Overzicht van Azure Active Directory-verificatie via SMB voor Azure-bestanden](storage-files-active-directory-overview.md)voor Een overzicht van Azure Active Directory-verificatie via SMB voor Azure-bestanden . Dit artikel is gericht op het inschakelen van verificatie met Azure Active Directory Domain Services (Azure AD DS) op Azure Files.

> [!NOTE]
> Azure Files ondersteunt Kerberos-verificatie met Azure AD DS met RC4-HMAC-versleuteling. AES Kerberos encryptie wordt nog niet ondersteund.

## <a name="prerequisites"></a>Vereisten

Voordat u Azure AD over SMB inschakelt voor Azure-bestandsshares, moet u ervoor zorgen dat u de volgende vereisten hebt voltooid:

1.  **Selecteer of maak een Azure AD-tenant.**

    U een nieuwe of bestaande tenant voor Azure AD-verificatie via SMB gebruiken. De tenant en het bestandsaandeel dat u wilt openen, moeten aan hetzelfde abonnement zijn gekoppeld.

    Als u een nieuwe Azure AD-tenant wilt maken, u [een Azure AD-tenant en een Azure AD-abonnement toevoegen.](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Zie [Een Azure Active Directory-tenant maken](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)als u een bestaande Azure AD-tenant hebt, maar een nieuwe tenant wilt maken voor gebruik met Azure-bestandsshares.

1.  **Azure AD Domain Services inschakelen op de Azure AD-tenant.**

    Als u verificatie wilt ondersteunen met Azure AD-referenties, moet u Azure AD Domain Services inschakelen voor uw Azure AD-tenant. Als u niet de beheerder van de Azure AD-tenant bent, neemt u contact op met de beheerder en volgt u de stapsgewijze richtlijnen om Azure Active Directory Domain Services in te [schakelen met behulp van de Azure-portal.](../../active-directory-domain-services/tutorial-create-instance.md)

    Het duurt meestal ongeveer 15 minuten voordat een Azure AD DS-implementatie is voltooid. Controleer of de status van Azure AD DS **Actief**weergeeft, waarbij wachtwoordhashsynchronisatie is ingeschakeld, voordat u doorgaat naar de volgende stap.

1.  **Deelnemen aan een Azure VM met Azure AD DS.**

    Als u een bestandsshare wilt openen met Azure AD-referenties van een vm, moet uw vm zijn verbonden met Azure AD DS. Zie [Een virtuele machine van Windows Server deelnemen aan een beheerd domein voor](../../active-directory-domain-services/join-windows-vm.md)meer informatie over het deelnemen aan een vm.

    > [!NOTE]
    > Azure AD DS-verificatie over SMB met Azure-bestandsshares wordt alleen ondersteund op Azure VM's die worden uitgevoerd op OS-versies boven Windows 7 of Windows Server 2008 R2.

1.  **Selecteer of maak een Azure-bestandsshare.**

    Selecteer een nieuw of bestaand bestandsaandeel dat is gekoppeld aan hetzelfde abonnement als uw Azure AD-tenant. Zie [Een bestandsshare maken in Azure-bestanden](storage-how-to-create-file-share.md)voor informatie over het maken van een nieuwe bestandsshare.
    Voor optimale prestaties raden we u aan uw bestandsshare in dezelfde regio te bevinden als de VM van waaruit u van plan bent toegang te krijgen tot het aandeel.

1.  **Controleer de connectiviteit van Azure Files door Azure-bestandsshares te monteren met uw opslagaccountsleutel.**

    Als u wilt controleren of uw vm en bestandsshare correct zijn geconfigureerd, probeert u het bestandsshare te monteren met uw opslagaccountsleutel. Zie [Een Azure-bestandsshare monteren en toegang krijgen tot het aandeel in Windows](storage-how-to-use-files-windows.md)voor meer informatie.

## <a name="overview-of-the-workflow"></a>Overzicht van de werkstroom

Voordat u Azure AD DS-verificatie over SMB voor Azure-bestandsshares inschakelt, controleert u of uw Azure AD- en Azure Storage-omgevingen correct zijn geconfigureerd. We raden u aan de [vereisten](#prerequisites) door te nemen om ervoor te zorgen dat u alle vereiste stappen hebt voltooid.

Ga vervolgens de volgende dingen uit om toegang te verlenen tot Azure Files-bronnen met Azure AD-referenties:

1. Azure AD DS-verificatie via SMB inschakelen voor uw opslagaccount om het opslagaccount te registreren bij de bijbehorende Azure AD DS-implementatie.
2. Toegangsmachtigingen voor een aandeel toewijzen aan een Azure AD-identiteit (een gebruiker, groep of serviceprincipal).
3. NTFS-machtigingen configureren via SMB voor mappen en bestanden.
4. Een Azure-bestandsshare monteren vanaf een vm die is verbonden met een domein.

In het volgende diagram wordt de end-to-end werkstroom weergegeven voor het inschakelen van Azure AD DS-verificatie via SMB voor Azure-bestanden.

![Diagram met Azure AD over smb voor Azure-bestanden](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="1-enable-azure-ad-ds-authentication-for-your-account"></a>1. Azure AD DS-verificatie inschakelen voor uw account

Als u Azure AD DS-verificatie via SMB voor Azure Files wilt inschakelen, u een eigenschap voor opslagaccounts instellen met behulp van de Azure-portal, Azure PowerShell of Azure CLI. Deze eigenschap is impliciet 'domeinjoins' van het opslagaccount met de bijbehorende Azure AD DS-implementatie. Azure AD DS-verificatie via SMB is vervolgens ingeschakeld voor alle nieuwe en bestaande bestandsshares in het opslagaccount.

Houd er rekening mee dat u Azure AD DS-verificatie via SMB alleen inschakelen nadat u Azure AD DS hebt geïmplementeerd in uw Azure AD-tenant. Zie voor meer informatie de [voorwaarden.](#prerequisites)

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om Azure AD DS-verificatie via SMB in te schakelen met de [Azure-portal:](https://portal.azure.com)

1. Ga in de Azure-portal naar uw bestaande opslagaccount of [maak een opslagaccount](../common/storage-account-create.md).
1. Selecteer **Configuratie** in **Configuration**de sectie Instellingen .
1. Schakel **onder Toegang tot bestanden voor bestandsshares** over naar Azure Active Directory Domain Service **(AAD DS)** naar **Ingeschakeld**.
1. Selecteer **Opslaan**.

In de volgende afbeelding ziet u hoe u Azure AD DS-verificatie via SMB inschakelt voor uw opslagaccount.

![Azure AD DS-verificatie via SMB inschakelen in de Azure-portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Als u Azure AD DS-verificatie via SMB wilt inschakelen met Azure PowerShell, installeert u de nieuwste Az-module (2.4 of nieuwer) of de Az.Storage-module (1,5 of nieuwer). Zie [Azure PowerShell installeren op Windows met PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps)voor meer informatie over het installeren van PowerShell.

Als u een nieuw opslagaccount wilt maken, roept u [Nieuw-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0)aan en stelt u de parameter **EnableAzureActiveDirectoryDomainServicesForFile** in op **true**. In het volgende voorbeeld moet u de tijdelijke aanduidingswaarden vervangen door uw eigen waarden. (Als u de vorige voorbeeldmodule gebruikt, is de parameter voor functieenablement **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Als u deze functie voor bestaande opslagaccounts wilt inschakelen, gebruikt u de volgende opdracht:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Azure CLI

Als u Azure AD-verificatie via SMB met Azure CLI wilt inschakelen, installeert u de nieuwste CLI-versie (versie 2.0.70 of nieuwer). Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)voor meer informatie over het installeren van Azure CLI.

Als u een nieuw opslagaccount wilt maken, `--enable-files-aadds` maakt u[az-opslagaccount aan](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)en stelt u de eigenschap in op **true**. In het volgende voorbeeld moet u de tijdelijke aanduidingswaarden vervangen door uw eigen waarden. (Als u de vorige voorbeeldmodule gebruikt, is de parameter voor functie-enablement **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Als u deze functie voor bestaande opslagaccounts wilt inschakelen, gebruikt u de volgende opdracht:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

U hebt azure AD DS-verificatie nu met smb-verificatie ingeschakeld en een aangepaste rol toegewezen die toegang biedt tot een Azure-bestandsshare met een Azure AD-identiteit. Als u extra gebruikers toegang wilt verlenen tot uw bestandsshare, volgt u de instructies in de [toegangsmachtigingen toewijzen](#2-assign-access-permissions-to-an-identity) om een identiteit te gebruiken en [NTFS-machtigingen configureren voor SMB-secties](#3-configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende bronnen voor meer informatie over Azure Files en het gebruik van Azure AD via SMB:

- [Overzicht van azure files-verificatieondersteuning voor SMB-toegang](storage-files-active-directory-overview.md)
- [Veelgestelde vragen](storage-files-faq.md)
