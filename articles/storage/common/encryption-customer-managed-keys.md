---
title: Door de klant beheerde sleutels gebruiken met Azure Key Vault om accountversleuteling te beheren
titleSuffix: Azure Storage
description: U uw eigen versleutelingssleutel gebruiken om de gegevens in uw opslagaccount te beschermen. Wanneer u een door de klant beheerde sleutel opgeeft, wordt die sleutel gebruikt om de toegang tot de sleutel die uw gegevens versleutelt, te beschermen en te beheren. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangscontroles te beheren.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456818"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Door de klant beheerde sleutels gebruiken met Azure Key Vault om Azure Storage-versleuteling te beheren

U uw eigen versleutelingssleutel gebruiken om de gegevens in uw opslagaccount te beschermen. Wanneer u een door de klant beheerde sleutel opgeeft, wordt die sleutel gebruikt om de toegang tot de sleutel die uw gegevens versleutelt, te beschermen en te beheren. Door de klant beheerde sleutels bieden meer flexibiliteit om toegangscontroles te beheren.

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U uw eigen sleutels maken en opslaan in een sleutelkluis, of u de Azure Key Vault API's gebruiken om sleutels te genereren. Het opslagaccount en de sleutelkluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie Wat is Azure Key Vault voor meer informatie over Azure Key [Vault?](../../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Over door de klant beheerde sleutels

In het volgende diagram ziet u hoe Azure Storage Azure Active Directory en Azure Key Vault gebruikt om aanvragen in te dienen met behulp van de door de klant beheerde sleutel:

![Diagram met de manier waarop door klanten beheerde sleutels werken in Azure Storage](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault-beheerder verleent machtigingen voor versleutelingssleutels voor de beheerde identiteit die is gekoppeld aan het opslagaccount.
2. Een Azure Storage-beheerder configureert versleuteling met een door de klant beheerde sleutel voor het opslagaccount.
3. Azure Storage gebruikt de beheerde identiteit die is gekoppeld aan het opslagaccount om toegang tot Azure Key Vault te verifiëren via Azure Active Directory.
4. Azure Storage omsluit de accountversleutelingssleutel met de klantsleutel in Azure Key Vault.
5. Voor lees-/schrijfbewerkingen verzendt Azure Storage aanvragen naar Azure Key Vault om de accountversleutelingssleutel uit te pakken om versleutelings- en decryptiebewerkingen uit te voeren.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Een account maken dat door klanten beheerde sleutels voor wachtrijen en tabellen ondersteunt

Gegevens die zijn opgeslagen in de wachtrij- en tabelservices, worden niet automatisch beschermd door een door de klant beheerde sleutel wanneer door de klant beheerde sleutels zijn ingeschakeld voor het opslagaccount. U deze services optioneel configureren op het moment dat u het opslagaccount maakt dat in deze beveiliging moet worden opgenomen.

Zie [Een account maken dat door klanten beheerde sleutels voor tabellen en wachtrijen ondersteunt](account-encryption-key-create.md)voor meer informatie over het maken van een opslagaccount dat door klanten beheerde sleutels ondersteunt.

Gegevens in de blob- en bestandsservices worden altijd beschermd door door de klant beheerde sleutels wanneer door de klant beheerde sleutels zijn geconfigureerd voor het opslagaccount.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Door de klant beheerde sleutels inschakelen voor een opslagaccount

Door de klant beheerde sleutels kunnen alleen worden ingeschakeld op bestaande opslagaccounts. De sleutelkluis moet worden ingericht met toegangsbeleid dat belangrijke machtigingen verleent aan de beheerde identiteit die is gekoppeld aan het opslagaccount. De beheerde identiteit is alleen beschikbaar nadat het opslagaccount is gemaakt.

Wanneer u een door de klant beheerde sleutel configureert, wordt in Azure Storage de hoofdgegevensversleutelingssleutel voor het account verpakt met de door de klant beheerde sleutel in de bijbehorende sleutelkluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en wordt onmiddellijk van kracht.

Wanneer u de sleutel wijzigt die wordt gebruikt voor Azure Storage-versleuteling door door klanten beheerde sleutels in te schakelen of uit te schakelen, de sleutelversie bij te werken of een andere sleutel op te geven, verandert de versleuteling van de hoofdsleutel, maar hoeven de gegevens in uw Azure Storage-account niet opnieuw te worden versleuteld.

Wanneer u beheerde sleutels van klanten in- of uitschakelt of uitschakelt, of wanneer u de sleutel of de sleutelversie wijzigt, verandert de beveiliging van de hoofdversleutelingssleutel, maar hoeven de gegevens in uw Azure Storage-account niet opnieuw te worden versleuteld.

Zie een van de volgende artikelen voor meer informatie over het gebruik van door klanten beheerde sleutels met Azure Key Vault voor Azure Storage-versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling vanuit de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling vanuit Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn afhankelijk van beheerde identiteiten voor Azure-resources, een functie van Azure AD. Beheerde identiteiten ondersteunen momenteel geen cross-directory scenario's. Wanneer u door de klant beheerde sleutels configureert in de Azure-portal, wordt een beheerde identiteit automatisch toegewezen aan uw opslagaccount onder de dekking. Als u vervolgens het abonnement, de brongroep of het opslagaccount van de ene Azure AD-map naar de andere verplaatst, wordt de beheerde identiteit die is gekoppeld aan het opslagaccount niet overgedragen aan de nieuwe tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **Een abonnement overbrengen tussen Azure AD-mappen** in [veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources voor](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)meer informatie.  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door klanten beheerde sleutels voor een opslagaccount wilt inschakelen, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet zowel de eigenschappen **Soft Delete** als Do **Not Purge** inschakelen op de sleutelkluis.

Alleen 2048-bits RSA- en RSA-HSM-sleutels worden ondersteund met Azure Storage-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels roteren

U een door de klant beheerde sleutel roteren in Azure Key Vault volgens uw nalevingsbeleid. Wanneer de sleutel is gedraaid, moet u het opslagaccount bijwerken om de nieuwe sleutelversie URI te gebruiken. Zie de sectie getiteld **De sleutelversie** bijwerken in Beheerde sleutels voor Azure Storage configureren met behulp van [de Azure-portal](storage-encryption-keys-portal.md)voor meer informatie over het bijwerken van het opslagaccount om een nieuwe versie van de sleutel in de Azure-portal te gebruiken.

Het roteren van de sleutel leidt niet tot herversleuteling van gegevens in het opslagaccount. Er is geen verdere actie vereist van de gebruiker.

## <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

U de toegang van het opslagaccount tot de door de klant beheerde sleutel op elk gewenst moment intrekken. Nadat de toegang tot door de klant beheerde sleutels is ingetrokken of nadat de sleutel is uitgeschakeld of verwijderd, kunnen clients geen bewerkingen oproepen die lezen uit of schrijven naar een blob of de metagegevens ervan. Pogingen om een van de volgende bewerkingen aan te roepen mislukken met foutcode 403 (Verboden) voor alle gebruikers:

- [Blobs weergeven](/rest/api/storageservices/list-blobs), `include=metadata` wanneer deze wordt aangeroepen met de parameter op de aanvraag URI
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties) (Blob-eigenschappen ophalen)
- [Blob-metagegevens oppakken](/rest/api/storageservices/get-blob-metadata)
- [Blob-metagegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [Momentopnameblob](/rest/api/storageservices/snapshot-blob), wanneer `x-ms-meta-name` deze wordt aangeroepen met de hoofdtekst van het verzoek
- [Blob kopiëren](/rest/api/storageservices/copy-blob)
- [Blob kopiëren van URL](/rest/api/storageservices/copy-blob-from-url)
- [Blob-laag instellen](/rest/api/storageservices/set-blob-tier)
- [Zet blok](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [Blokkeren toevoegen aan URL](/rest/api/storageservices/append-block-from-url)
- [Blob plaatsen](/rest/api/storageservices/put-blob)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [Blob incrementele kopie](/rest/api/storageservices/incremental-copy-blob)

Als u deze bewerkingen opnieuw wilt aanroepen, herstelt u de toegang tot de door de klant beheerde sleutel.

Alle gegevensbewerkingen die niet in deze sectie worden vermeld, kunnen doorgaan nadat door de klant beheerde sleutels zijn ingetrokken of een sleutel is uitgeschakeld of verwijderd.

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) of [Azure CLI](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Door de klant beheerde sleutels voor door Azure beheerde schijven

Door de klant beheerde sleutels zijn ook beschikbaar voor het beheren van versleuteling van door Azure beheerde schijven. Door de klant beheerde sleutels gedragen zich anders voor beheerde schijven dan voor Azure Storage-bronnen. Zie [Server-side encryptie van Azure managed disks](../../virtual-machines/windows/disk-encryption.md) voor Windows of Server side encryption van Azure managed [disks](../../virtual-machines/linux/disk-encryption.md) voor Linux voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling vanuit de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage-versleuteling vanuit Azure CLI](storage-encryption-keys-cli.md)
- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)
