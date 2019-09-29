---
title: Azure Storage versleuteling voor gegevens in rust | Microsoft Docs
description: Azure Storage beveiligt uw gegevens door deze automatisch te versleutelen voordat deze in de cloud worden bewaard. Alle gegevens in een Azure Storage worden op transparante wijze versleuteld en ontsleuteld met 256-bits AES-versleuteling en voldoen aan de vereisten FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673046"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage versleuteling voor Data-at-rest

Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Versleuteling beschermt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Azure Storage versleuteling is ingeschakeld voor alle nieuwe en bestaande opslag accounts en kan niet worden uitgeschakeld. Omdat uw gegevens standaard worden beveiligd, hoeft u uw code of toepassingen niet te wijzigen om Azure Storage versleuteling te benutten. 

Opslag accounts worden versleuteld, ongeacht hun prestatie niveau (Standard of Premium) of implementatie model (Azure Resource Manager of klassiek). Alle Azure Storage redundantie opties ondersteunen versleuteling en alle kopieën van een opslag account worden versleuteld. Alle Azure Storage bronnen worden versleuteld, waaronder blobs, schijven, bestanden, wacht rijen en tabellen. Alle meta gegevens van objecten worden ook versleuteld.

Versleuteling heeft geen invloed op de prestaties van Azure Storage. Er zijn geen extra kosten verbonden aan Azure Storage versleuteling.

Zie [Cryptography-API voor meer informatie over de onderliggende cryptografische modules Azure Storage versleuteling: Volgende generatie @ no__t-0.

## <a name="key-management"></a>Sleutelbeheer

U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling met uw eigen sleutels beheren, samen met Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Door micro soft beheerde sleutels

Uw opslag account maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. U kunt de versleutelings instellingen voor uw opslag account bekijken in de sectie **versleuteling** van het [Azure Portal](https://portal.azure.com), zoals wordt weer gegeven in de volgende afbeelding.

![Account weer geven dat is versleuteld met door micro soft beheerde sleutels](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Door de klant beheerde sleutels

U kunt Azure Storage versleuteling beheren met door de klant beheerde sleutels. Door de klant beheerde sleutels bieden u meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens. 

Gebruik Azure Key Vault voor het beheren van uw sleutels en het controleren van uw sleutel gebruik. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis moeten zich in dezelfde regio bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)voor meer informatie over Azure Key Vault.

Zie [Azure Key Vault Power shell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) en [Azure Key Vault cli](https://docs.microsoft.com/cli/azure/keyvault)voor meer informatie over het intrekken van de toegang tot door de klant beheerde sleutels. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

Zie een van de volgende artikelen voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Storage:

- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling via de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels gebruiken met Azure Storage versleuteling van Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u een abonnement van een Azure AD-Directory overbrengt naar een andere, worden beheerde identiteiten niet bijgewerkt en werken de door de klant beheerde sleutels mogelijk niet meer. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

> [!NOTE]  
> Door de klant beheerde sleutels worden niet ondersteund voor [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage versleuteling versus schijf versleuteling

Met Azure Storage versleuteling worden alle Azure Storage-accounts en de resources die ze bevatten versleuteld, met inbegrip van de pagina-blobs die back-ups van virtuele machines van Azure maken. Bovendien kunnen virtuele machine schijven van Azure worden versleuteld met [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption maakt gebruik van een industrie standaard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows en [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) op Linux om versleutelings oplossingen op basis van besturings systemen te bieden die zijn geïntegreerd met Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)
