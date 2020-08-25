---
title: Versleuteling aan de server zijde van Azure Managed Disks-Azure CLI
description: Azure Storage beveiligt uw gegevens door deze te versleutelen voordat deze in de opslag clusters worden bewaard. U kunt door de klant beheerde sleutels gebruiken om versleuteling te beheren met uw eigen sleutels, of u kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw beheerde schijven.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e541fd90f0cb9c8f0e2763f16541824c5a7b3000
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816895"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Versleuteling aan de server zijde van Azure Disk Storage

Met SSE (server side Encryption) beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie. Met SSE worden uw gegevens die zijn opgeslagen op Azure Managed disks (besturings systeem en gegevens schijven), op rest automatisch gecodeerd wanneer deze in de cloud worden bewaard. 

Gegevens in azure Managed disks worden transparant versleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok versleuteling die beschikbaar is en is compatibel met FIPS 140-2. Zie [crypto GRAFIE API: Next Generation](/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

Versleuteling aan de server zijde heeft geen invloed op de prestaties van beheerde schijven en er zijn geen extra kosten. 

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw beheerde schijf of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle gegevens in Managed disks. 

In de volgende secties worden de opties voor sleutel beheer in meer detail beschreven.

### <a name="platform-managed-keys"></a>Door het platform beheerde sleutels

Beheerde schijven gebruiken standaard versleutelings sleutels die door het platform worden beheerd. Alle beheerde schijven, moment opnamen, installatie kopieën en gegevens die naar bestaande beheerde schijven worden geschreven, worden automatisch versleuteld met door het platform beheerde sleutels.

### <a name="customer-managed-keys"></a>Door klant beheerde sleutels

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Beperkingen

De door de klant beheerde sleutels hebben nu de volgende beperkingen:

- Als deze functie is ingeschakeld voor uw schijf, kunt u deze niet uitschakelen.
    Als u dit wilt omzeilen, moet u [alle gegevens](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) naar een volledig andere beheerde schijf kopiëren die geen door de klant beheerde sleutels gebruikt.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure Active Directory (Azure AD). Wanneer u door de klant beheerde sleutels configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw resources onder de kaften. Als u het abonnement, de resource groep of de beheerde schijf vervolgens verplaatst van een Azure AD-Directory naar een andere, wordt de beheerde identiteit die is gekoppeld aan Managed disks niet overgedragen naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie [een abonnement overdragen tussen Azure AD-mappen](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Versleuteling van host-end-to-end-versleuteling voor uw VM-gegevens

Wanneer u versleuteling op de host inschakelt, wordt die versleuteling gestart op de VM-host zelf, de Azure-server waaraan uw VM is toegewezen. De gegevens voor de caches van de tijdelijke schijf en het besturings systeem of de gegevens schijf worden opgeslagen op die VM-host. Nadat de versleuteling op de host is ingeschakeld, worden alle gegevens versleuteld in rust en stromen die zijn versleuteld naar de opslag service, waar ze worden bewaard. In wezen versleutelt versleuteling op host uw gegevens van end-to-end. Versleuteling op host maakt geen gebruik van de CPU van uw virtuele machine en heeft geen invloed op de prestaties van uw virtuele machine. 

Tijdelijke schijven worden op rest versleuteld met door het platform beheerde sleutels wanneer u end-to-end-versleuteling inschakelt. De caches van het besturings systeem en de gegevens schijf worden op rest versleuteld met door de klant beheerde of door het platform beheerde sleutels, afhankelijk van het type schijf versleuteling dat is geselecteerd. Als een schijf bijvoorbeeld is versleuteld met door de klant beheerde sleutels, wordt de cache voor de schijf versleuteld met door de klant beheerde sleutels en als een schijf wordt versleuteld met door het platform beheerde sleutels, wordt de cache voor de schijf versleuteld met door het platform beheerde sleutels.

### <a name="restrictions"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Ondersteunde VM-grootten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dubbele versleuteling bij rest

Hoge beveiligings gevoelige klanten die zich zorgen maken over het risico dat is gekoppeld aan een bepaalde versleutelings algoritme, implementatie of sleutel die wordt aangetast, kunnen nu kiezen voor extra versleutelen met een ander versleutelings algoritme/dezelfde modus op de laag van de infra structuur met behulp van door het platform beheerde versleutelings sleutels. Deze nieuwe laag kan worden toegepast op schijven, moment opnamen en installatie kopieën, die allemaal worden versleuteld met dubbele versleuteling.

### <a name="supported-regions"></a>Ondersteunde regio’s

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Versleuteling aan de server zijde versus Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) maakt gebruik van de [DM-cryptografie](https://en.wikipedia.org/wiki/Dm-crypt) functie van Linux om beheerde schijven te versleutelen met door de klant beheerde sleutels in de gast-VM.  Versleuteling aan de server zijde met door de klant beheerde sleutels wordt verbeterd op ADE door u in staat te stellen alle typen besturings systemen en installatie kopieën voor uw virtuele machines te gebruiken door gegevens in de opslag service te versleutelen.

## <a name="next-steps"></a>Volgende stappen

- End-to-end-versleuteling inschakelen met versleuteling op de host met [cli](disks-enable-host-based-encryption-cli.md) of de [Azure Portal](../disks-enable-host-based-encryption-portal.md).
- Schakel dubbele versleuteling in op de rest voor beheerde schijven met [cli](disks-enable-double-encryption-at-rest-cli.md) of de [Azure Portal](../disks-enable-double-encryption-at-rest-portal.md).
- Door de klant beheerde sleutels voor beheerde schijven inschakelen met [cli](disks-enable-customer-managed-keys-cli.md) of de [Azure Portal](../disks-enable-customer-managed-keys-portal.md).
- [Wat is Azure Key Vault?](../../key-vault/general/overview.md)
