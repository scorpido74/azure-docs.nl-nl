---
title: Versleuteling aan de server zijde van Azure Managed Disks-Power shell
description: Azure Storage beveiligt uw gegevens door deze te versleutelen voordat deze in de opslag clusters worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw beheerde schijven, of u kunt door de klant beheerde sleutels gebruiken om versleuteling te beheren met uw eigen sleutels.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: d0625c817703fe5d5645bcfdab962591cb184ae2
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232726"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Versleuteling aan de server zijde van Azure Disk Storage

Met SSE (server side Encryption) beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Met SSE worden uw gegevens die zijn opgeslagen op Azure Managed disks (besturings systeem en gegevens schijven), op rest automatisch gecodeerd wanneer deze in de cloud worden bewaard. 

Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2. Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

Versleuteling aan de server zijde heeft geen invloed op de prestaties van beheerde schijven en er zijn geen extra kosten. 

> [!NOTE]
> Tijdelijke schijven zijn geen beheerde schijven en worden niet versleuteld door SSE, tenzij u versleuteling op de host inschakelt.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in Managed disks. 

In de volgende secties worden de opties voor sleutel beheer in meer detail beschreven.

### <a name="platform-managed-keys"></a>Door het platform beheerde sleutels

Beheerde schijven gebruiken standaard versleutelings sleutels die door het platform worden beheerd. Vanaf 10 juni 2017 worden alle nieuwe beheerde schijven, moment opnamen, installatie kopieën en nieuwe gegevens die worden geschreven naar bestaande beheerde schijven automatisch versleuteld met door het platform beheerde sleutels.

### <a name="customer-managed-keys"></a>Door klant beheerde sleutels

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Beperkingen

De door de klant beheerde sleutels hebben nu de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omzeilen, moet u [alle gegevens](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Versleuteling van host-end-to-end-versleuteling voor uw VM-gegevens

End-to-end-versleuteling wordt gestart vanaf de VM-host, de Azure-server waaraan uw VM is toegewezen. Gegevens op uw tijdelijke schijven en de caches van het besturings systeem of de gegevens schijf worden opgeslagen op die VM-host. Wanneer u end-to-end-versleuteling inschakelt, worden alle gegevens versleuteld op rest en stromen die zijn versleuteld naar de opslag service, waar deze worden bewaard. End-to-end-versleuteling maakt geen gebruik van de CPU van uw virtuele machine en heeft geen invloed op de prestaties van uw virtuele machine. 

Tijdelijke schijven worden op rest versleuteld met door het platform beheerde sleutels wanneer u end-to-end-versleuteling inschakelt. De caches van het besturings systeem en de gegevens schijf worden op rest versleuteld met door de klant beheerde of door het platform beheerde sleutels, afhankelijk van het versleutelings type. Als een schijf bijvoorbeeld is versleuteld met door de klant beheerde sleutels, wordt de cache voor de schijf versleuteld met door de klant beheerde sleutels en als een schijf wordt versleuteld met door het platform beheerde sleutels, wordt de cache voor de schijf versleuteld met door het platform beheerde sleutels.

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dubbele versleuteling bij rest

Hoge beveiligings gevoelige klanten die zich zorgen maken over het risico dat is gekoppeld aan een bepaalde versleutelings algoritme, implementatie of sleutel die wordt aangetast, kunnen nu kiezen voor extra versleutelen met een ander versleutelings algoritme/dezelfde modus op de laag van de infra structuur met behulp van door het platform beheerde versleutelings sleutels. Deze nieuwe laag kan worden toegepast op schijven, moment opnamen en installatie kopieën, die allemaal worden versleuteld met dubbele versleuteling.

### <a name="supported-regions"></a>Ondersteunde regio's

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de server zijde versus Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) -functie van Windows voor het versleutelen van beheerde schijven met door de klant beheerde sleutels in de gast-VM.  Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

- [Versleuteling op basis van een host inschakelen](disks-enable-host-based-encryption-powershell.md)
- [Azure PowerShell-dubbele versleuteling op schijven met rest-beheer inschakelen](disks-enable-double-encryption-at-rest-powershell.md)
- [Door de klant beheerde sleutels voor uw beheerde schijf inschakelen-Power shell](disks-enable-customer-managed-keys-powershell.md)
- [Door de klant beheerde sleutels inschakelen-beheerde schijven](disks-enable-customer-managed-keys-portal.md)
- [Bekijk de Azure Resource Manager sjablonen voor het maken van versleutelde schijven met door de klant beheerde sleutels](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)