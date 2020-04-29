---
title: Door de klant beheerde sleutels gebruiken met Azure Key Vault voor het beheren van account versleuteling
titleSuffix: Azure Storage
description: U kunt uw eigen versleutelings sleutel gebruiken om de gegevens in uw opslag account te beveiligen. Wanneer u een door de klant beheerde sleutel opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de sleutel die uw gegevens versleutelt. Door de klant beheerde sleutels bieden meer flexibiliteit voor het beheren van toegangs beheer.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81456818"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Door de klant beheerde sleutels gebruiken met Azure Key Vault voor het beheren van Azure Storage versleuteling

U kunt uw eigen versleutelings sleutel gebruiken om de gegevens in uw opslag account te beveiligen. Wanneer u een door de klant beheerde sleutel opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de sleutel die uw gegevens versleutelt. Door de klant beheerde sleutels bieden meer flexibiliteit voor het beheren van toegangs beheer.

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-Tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](../../key-vault/general/overview.md)voor meer informatie over Azure Key Vault.

## <a name="about-customer-managed-keys"></a>Over door de klant beheerde sleutels

In het volgende diagram ziet u hoe Azure Storage Azure Active Directory gebruikt en Azure Key Vault om aanvragen te maken met de door de klant beheerde sleutel:

![Diagram waarin wordt getoond hoe door de klant beheerde sleutels in Azure Storage werken](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault beheerder verleent machtigingen voor de versleutelings sleutels aan de beheerde identiteit die is gekoppeld aan het opslag account.
2. Een Azure Storage-beheerder configureert versleuteling met een door de klant beheerde sleutel voor het opslag account.
3. Azure Storage gebruikt de beheerde identiteit die aan het opslag account is gekoppeld om de toegang tot Azure Key Vault via Azure Active Directory te verifiëren.
4. Azure Storage verloopt de versleutelings sleutel van het account met de klant sleutel in Azure Key Vault.
5. Voor lees-en schrijf bewerkingen verzendt Azure Storage aanvragen naar Azure Key Vault om de versleutelings sleutel van het account te decoderen om bewerkingen voor versleuteling en ontsleuteling uit te voeren.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Een account maken dat door de klant beheerde sleutels voor wacht rijen en tabellen ondersteunt

Gegevens die zijn opgeslagen in de wachtrij en tabel services worden niet automatisch beveiligd door een door de klant beheerde sleutel wanneer door de klant beheerde sleutels zijn ingeschakeld voor het opslag account. U kunt deze services eventueel configureren op het moment dat u het opslag account maakt dat moet worden opgenomen in deze beveiliging.

Zie voor meer informatie over het maken van een opslag account dat door de klant beheerde sleutels voor wacht rijen en tabellen ondersteunt [een account maken dat door de klant beheerde sleutels ondersteunt voor tabellen en wacht rijen](account-encryption-key-create.md).

Gegevens in de BLOB en bestands services worden altijd beschermd door door de klant beheerde sleutels wanneer door de klant beheerde sleutels zijn geconfigureerd voor het opslag account.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Door de klant beheerde sleutels voor een opslag account inschakelen

Door de klant beheerde sleutels kunnen alleen worden ingeschakeld op bestaande opslag accounts. De sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan het opslag account is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat het opslag account is gemaakt.

Wanneer u een door de klant beheerde sleutel configureert, plaatst Azure Storage de versleutelings sleutel van de hoofd gegevens voor het account met de door de klant beheerde sleutel in de bijbehorende sleutel kluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en is direct van kracht.

Wanneer u de sleutel die wordt gebruikt voor Azure Storage versleuteling wijzigt door door de klant beheerde sleutels in of uit te scha kelen, de sleutel versie bij te werken of een andere sleutel op te geven, wordt de versleuteling van de hoofd sleutel gewijzigd, maar de gegevens in uw Azure Storage account hoeven niet opnieuw te worden versleuteld.

Wanneer u door de klant beheerde sleutels in-of uitschakelt of wanneer u de sleutel of de sleutel versie wijzigt, wordt de beveiliging van de hoofd versleutelings sleutel gewijzigd, maar de gegevens in uw Azure Storage-account hoeven niet opnieuw te worden versleuteld.

Zie een van de volgende artikelen voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor Azure Storage versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van de Azure Portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Power shell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure AD. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw opslag account. Als u het abonnement, de resource groep of het opslag account vervolgens verplaatst van de ene Azure AD-Directory naar een andere, wordt de beheerde identiteit die aan het opslag account is gekoppeld, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen voor een opslag account, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen.

Alleen 2048-bits RSA-en RSA-HSM-sleutels worden ondersteund met Azure Storage versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u het opslag account bijwerken om de nieuwe sleutel versie-URI te gebruiken. Voor informatie over het bijwerken van het opslag account voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal, zie de sectie de **sleutel versie bijwerken** in [door de klant beheerde sleutels configureren voor Azure Storage met behulp van de Azure Portal](storage-encryption-keys-portal.md).

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in het opslag account niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

U kunt de toegang tot het opslag account op elk gewenst moment intrekken voor de door de klant beheerde sleutel. Nadat de toegang tot door de klant beheerde sleutels is ingetrokken of nadat de sleutel is uitgeschakeld of verwijderd, kunnen clients geen bewerkingen aanroepen die lezen van of schrijven naar een BLOB of de meta gegevens ervan. Pogingen om een van de volgende bewerkingen aan te roepen, mislukken met fout code 403 (verboden) voor alle gebruikers:

- [Blobs vermelden](/rest/api/storageservices/list-blobs), indien aangeroepen met de `include=metadata` para meter voor de aanvraag-URI
- [BLOB ophalen](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blob-eigenschappen ophalen)
- [BLOB-meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Moment opname-BLOB](/rest/api/storageservices/snapshot-blob), wanneer aangeroepen `x-ms-meta-name` met de aanvraag header
- [BLOB kopiëren](/rest/api/storageservices/copy-blob)
- [BLOB kopiëren van URL](/rest/api/storageservices/copy-blob-from-url)
- [Blob-laag instellen](/rest/api/storageservices/set-blob-tier)
- [Blok keren](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [Blok van URL toevoegen](/rest/api/storageservices/append-block-from-url)
- [BLOB plaatsen](/rest/api/storageservices/put-blob)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [BLOB voor incrementele kopie](/rest/api/storageservices/incremental-copy-blob)

Als u deze bewerkingen opnieuw wilt aanroepen, moet u de toegang tot de door de klant beheerde sleutel herstellen.

Alle gegevens bewerkingen die niet in deze sectie worden vermeld, kunnen door gaan nadat door de klant beheerde sleutels zijn ingetrokken of een sleutel wordt uitgeschakeld of verwijderd.

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u [Power shell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) of [Azure cli](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Door de klant beheerde sleutels voor Azure Managed disks

Door de klant beheerde sleutels zijn ook beschikbaar voor het beheren van versleuteling van Azure Managed disks. Door de klant beheerde sleutels gedragen zich anders voor Managed disks dan voor Azure Storage resources. Zie versleuteling aan de [server zijde van Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) voor Windows of [server side Encryption of Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) for Linux voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van de Azure Portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Power shell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Azure CLI](storage-encryption-keys-cli.md)
- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)
