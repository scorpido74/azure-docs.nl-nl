---
title: Azure Storage versleuteling voor Data-at-rest
description: Azure Storage beveiligt uw gegevens door deze automatisch te versleutelen voordat deze in de cloud worden bewaard. U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels.
services: storage
author: tamram
ms.service: storage
ms.date: 02/05/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 86d6a63601036abdde4ee7ae73114566d749feca
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77082831"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Azure Storage versleuteling voor Data-at-rest

Azure Storage worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Azure Storage versleuteling beveiligt uw gegevens en helpt u om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

## <a name="about-azure-storage-encryption"></a>Over Azure Storage versleuteling

Gegevens in Azure Storage worden transparant versleuteld en ontsleuteld met 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Azure Storage versleuteling lijkt op BitLocker-versleuteling in Windows.

Azure Storage versleuteling is ingeschakeld voor alle nieuwe opslag accounts, met inbegrip van Resource Manager en klassieke opslag accounts. Azure Storage versleuteling kan niet worden uitgeschakeld. Omdat uw gegevens standaard worden beveiligd, hoeft u uw code of toepassingen niet te wijzigen om Azure Storage versleuteling te benutten.

Opslag accounts worden versleuteld, ongeacht hun prestatie niveau (Standard of Premium) of implementatie model (Azure Resource Manager of klassiek). Alle Azure Storage redundantie opties ondersteunen versleuteling en alle kopieën van een opslag account worden versleuteld. Alle Azure Storage bronnen worden versleuteld, waaronder blobs, schijven, bestanden, wacht rijen en tabellen. Alle meta gegevens van objecten worden ook versleuteld.

Versleuteling heeft geen invloed op de prestaties van Azure Storage. Er zijn geen extra kosten verbonden aan Azure Storage versleuteling.

Elke blok-blob, een toevoeg-BLOB of een pagina-blob die na 20 oktober 2017 is geschreven naar Azure Storage, is versleuteld. De blobs die vóór deze datum zijn gemaakt, worden versleuteld met een achtergrond proces. Als u de versleuteling wilt afdwingen van een blob die is gemaakt vóór 20 oktober 2017, kunt u de BLOB herschrijven. Zie [de versleutelings status van een BLOB controleren](../blobs/storage-blob-encryption-status.md)voor meer informatie over het controleren van de versleutelings status van een blob.

Zie [crypto GRAFIE API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)(Engelstalig) voor meer informatie over de onderliggende cryptografische modules Azure Storage versleuteling.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

U kunt gebruikmaken van door micro soft beheerde sleutels voor de versleuteling van uw opslag account, of u kunt versleuteling beheren met uw eigen sleutels. Als u ervoor kiest om versleuteling te beheren met uw eigen sleutels, hebt u twee opties:

- U kunt een door de *klant beheerde sleutel* opgeven met Azure Key Vault die moet worden gebruikt voor het versleutelen en ontsleutelen van gegevens in Blob Storage en in azure files. <sup>1, 2</sup>
- U kunt een door de *klant opgegeven sleutel* voor Blob-opslag bewerkingen opgeven. Een client die een lees-of schrijf aanvraag uitvoert voor Blob Storage, kan een versleutelings sleutel bevatten op de aanvraag voor gedetailleerde controle over hoe BLOB-gegevens worden versleuteld en ontsleuteld.

De volgende tabel vergelijkt de opties voor sleutel beheer voor Azure Storage versleuteling.

|                                        |    Door micro soft beheerde sleutels                             |    Door klant beheerde sleutels                                                                                                                        |    Door de klant verschafte sleutels                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Bewerkingen voor versleuteling/ontsleuteling    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Azure Storage services ondersteund    |    Alle                                                |    Blob-opslag, Azure Files<sup>1, 2</sup>                                                                                                               |    Blob Storage                                                                  |
|    Sleutel opslag                         |    Micro soft-sleutel archief    |    Azure Key Vault                                                                                                                              |    Azure Key Vault of een ander sleutel archief                                                                 |
|    Verantwoordelijkheid voor sleutel rotatie         |    Microsoft                                          |    Klant                                                                                                                                     |    Klant                                                                      |
|    Sleutelgebruik                           |    Microsoft                                          |    Azure Portal, opslag Resource provider REST API, Azure Storage beheer Bibliotheken, Power shell, CLI        |    Azure Storage REST API (Blob Storage), Azure Storage client bibliotheken    |
|    Sleutel toegang                          |    Alleen micro soft                                     |    Micro soft, klant                                                                                                                    |    Alleen klant                                                                 |

<sup>1</sup> Zie [een account maken dat door de klant beheerde sleutels voor wacht rijen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met behulp van een wachtrij opslag.<br />
<sup>2</sup> Zie voor meer informatie over het maken van een account dat gebruikmaakt van door de klant beheerde sleutels met table-opslag [een account maken dat door de klant beheerde sleutels voor tabellen ondersteunt](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json).

In de volgende secties worden de opties voor sleutel beheer in meer detail beschreven.

## <a name="microsoft-managed-keys"></a>Door micro soft beheerde sleutels

Uw opslag account maakt standaard gebruik van door micro soft beheerde versleutelings sleutels. U kunt de versleutelings instellingen voor uw opslag account bekijken in de sectie **versleuteling** van het [Azure Portal](https://portal.azure.com), zoals wordt weer gegeven in de volgende afbeelding.

![Account weer geven dat is versleuteld met door micro soft beheerde sleutels](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Door de klant beheerde sleutels met Azure Key Vault

U kunt Azure Storage versleuteling op het niveau van het opslag account beheren met uw eigen sleutels. Wanneer u een door de klant beheerde sleutel op het niveau van het opslag account opgeeft, wordt die sleutel gebruikt voor het beveiligen en beheren van de toegang tot de hoofd versleutelings sleutel voor het opslag account die op zijn beurt wordt gebruikt voor het versleutelen en ontsleutelen van alle BLOB-en bestands gegevens. Door de klant beheerde sleutels bieden meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelings sleutels controleren die worden gebruikt voor het beveiligen van uw gegevens.

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. Het opslag account en de sleutel kluis moeten zich in dezelfde regio en in dezelfde Azure Active Directory-Tenant (Azure AD) bevinden, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)voor meer informatie over Azure Key Vault.

In dit diagram ziet u hoe Azure Storage Azure Active Directory en Azure Key Vault gebruikt om aanvragen te maken met de door de klant beheerde sleutel:

![Diagram waarin wordt getoond hoe door de klant beheerde sleutels in Azure Storage werken](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

In de volgende lijst worden de genummerde stappen in het diagram uitgelegd:

1. Een Azure Key Vault beheerder verleent machtigingen voor de versleutelings sleutels aan de beheerde identiteit die is gekoppeld aan het opslag account.
2. Een Azure Storage-beheerder configureert versleuteling met een door de klant beheerde sleutel voor het opslag account.
3. Azure Storage gebruikt de beheerde identiteit die aan het opslag account is gekoppeld om de toegang tot Azure Key Vault via Azure Active Directory te verifiëren.
4. Azure Storage verloopt de versleutelings sleutel van het account met de klant sleutel in Azure Key Vault.
5. Voor lees-en schrijf bewerkingen stuurt Azure Storage aanvragen naar Azure Key Vault om de versleutelings sleutel van het account te verpakken en uit te pakken om bewerkingen voor versleuteling en ontsleuteling uit te voeren.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Door de klant beheerde sleutels voor een opslag account inschakelen

Wanneer u versleuteling inschakelt met door de klant beheerde sleutels voor een opslag account, Azure Storage verpakt de account versleutelings sleutel met de door de klant beheerde sleutel in de bijbehorende sleutel kluis. Het inschakelen van door de klant beheerde sleutels heeft geen invloed op de prestaties en het account wordt direct met de nieuwe sleutel versleuteld, zonder enige tijd vertraging.

Een nieuw opslag account wordt altijd versleuteld met door micro soft beheerde sleutels. Het is niet mogelijk om door de klant beheerde sleutels in te scha kelen op het moment dat het account wordt gemaakt. Door de klant beheerde sleutels worden opgeslagen in Azure Key Vault en de sleutel kluis moet worden ingericht met toegangs beleid waarmee sleutel machtigingen worden verleend aan de beheerde identiteit die aan het opslag account is gekoppeld. De beheerde identiteit is alleen beschikbaar nadat het opslag account is gemaakt.

Wanneer u de sleutel die wordt gebruikt voor Azure Storage versleuteling wijzigt door door de klant beheerde sleutels in of uit te scha kelen, de sleutel versie bij te werken of een andere sleutel op te geven, wordt de versleuteling van de hoofd sleutel gewijzigd, maar de gegevens in uw Azure Storage-account niet moet opnieuw worden versleuteld.

Zie een van de volgende artikelen voor meer informatie over het gebruik van door de klant beheerde sleutels met Azure Key Vault voor Azure Storage versleuteling:

- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van de Azure Portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Power shell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren met Key Vault voor Azure Storage versleuteling van Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn gebaseerd op beheerde identiteiten voor Azure-resources, een functie van Azure AD. Beheerde identiteiten bieden momenteel geen ondersteuning voor scenario's met meerdere mappen. Wanneer u door de klant beheerde sleutels in de Azure Portal configureert, wordt er automatisch een beheerde identiteit toegewezen aan uw opslag account. Als u het abonnement, de resource groep of het opslag account vervolgens verplaatst van de ene Azure AD-Directory naar een andere, wordt de beheerde identiteit die aan het opslag account is gekoppeld, niet overgebracht naar de nieuwe Tenant, zodat door de klant beheerde sleutels mogelijk niet meer werken. Zie **een abonnement overdragen tussen Azure AD-mappen** in [Veelgestelde vragen en bekende problemen met beheerde identiteiten voor Azure-resources](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)voor meer informatie.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Door de klant beheerde sleutels opslaan in Azure Key Vault

Als u door de klant beheerde sleutels wilt inschakelen voor een opslag account, moet u een Azure Key Vault gebruiken om uw sleutels op te slaan. U moet de instellingen **voorlopig verwijderen** en **niet wissen** in de sleutel kluis inschakelen.

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Azure Storage versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

### <a name="rotate-customer-managed-keys"></a>Door de klant beheerde sleutels draaien

U kunt een door de klant beheerde sleutel in Azure Key Vault draaien volgens uw nalevings beleid. Wanneer de sleutel wordt geroteerd, moet u het opslag account bijwerken om de nieuwe sleutel-URI te gebruiken. Voor informatie over het bijwerken van het opslag account voor het gebruik van een nieuwe versie van de sleutel in de Azure Portal, zie de sectie de **sleutel versie bijwerken** in [door de klant beheerde sleutels configureren voor Azure Storage met behulp van de Azure Portal](storage-encryption-keys-portal.md).

Als u de sleutel roteert, wordt het opnieuw versleutelen van gegevens in het opslag account niet geactiveerd. Er is geen verdere actie vereist van de gebruiker.

### <a name="revoke-access-to-customer-managed-keys"></a>Toegang tot door de klant beheerde sleutels intrekken

Als u de toegang tot door de klant beheerde sleutels wilt intrekken, gebruikt u Power shell of Azure CLI. Zie [Azure Key Vault Power shell](/powershell/module/az.keyvault//) of [Azure Key Vault cli](/cli/azure/keyvault)(Engelstalig) voor meer informatie. Als u toegang intrekt, wordt de toegang tot alle gegevens in het opslag account effectief geblokkeerd, omdat de versleutelings sleutel niet toegankelijk is voor Azure Storage.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Door de klant beheerde sleutels voor Azure Managed disks (preview)

Door de klant beheerde sleutels zijn ook beschikbaar voor het beheren van versleuteling van Azure Managed disks (preview). Door de klant beheerde sleutels gedragen zich anders voor Managed disks dan voor Azure Storage resources. Zie [server side Encryption of Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) for Windows of [server side Encryption of Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) for Linux voor meer informatie.

## <a name="customer-provided-keys-preview"></a>Door de klant verschafte sleutels (preview-versie)

Clients die aanvragen indienen voor Azure Blob-opslag hebben de optie om een versleutelings sleutel op te geven voor een afzonderlijke aanvraag. Met inbegrip van de versleutelings sleutel op de aanvraag biedt gedetailleerde controle over de versleutelings instellingen voor Blob Storage-bewerkingen. Door de klant verschafte sleutels (preview) kunnen worden opgeslagen in Azure Key Vault of in een ander sleutel archief.

Voor een voor beeld van hoe u een door de klant opgegeven sleutel opgeeft voor een aanvraag voor Blob-opslag, raadpleegt [u een door de klant opgegeven sleutel opgeven voor een aanvraag voor Blob-opslag met .net](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Lees-en schrijf bewerkingen versleutelen

Wanneer een client toepassing een versleutelings sleutel voor de aanvraag levert, Azure Storage de versleuteling en ontsleuteling transparant uitvoeren tijdens het lezen en schrijven van BLOB-gegevens. Azure Storage schrijft een SHA-256-hash van de versleutelings sleutel naast de inhoud van de blob. De hash wordt gebruikt om te controleren of alle volgende bewerkingen voor de BLOB dezelfde versleutelings sleutel gebruiken. 

Azure Storage de versleutelings sleutel niet opslaat of beheert die de client met de aanvraag verzendt. De sleutel wordt veilig verwijderd zodra het proces voor versleuteling of ontsleuteling is voltooid.

Wanneer een-client een BLOB maakt of bijwerkt met behulp van een door de klant geleverde sleutel, moeten volgende Lees-en schrijf aanvragen voor die BLOB ook de sleutel bevatten. Als de sleutel niet is opgenomen in een aanvraag voor een blob die al is versleuteld met een door de klant verschafte sleutel, mislukt de aanvraag met fout code 409 (conflict).

Als de client toepassing een versleutelings sleutel op de aanvraag verstuurt en het opslag account ook is versleuteld met een door micro soft beheerde sleutel of een door de klant beheerde sleutel, gebruikt Azure Storage de sleutel die op de aanvraag voor versleuteling en ontsleuteling wordt vermeld.

Als u de versleutelings sleutel wilt verzenden als onderdeel van de aanvraag, moet een client een beveiligde verbinding tot stand brengen met Azure Storage met behulp van HTTPS.

Elke BLOB-moment opname kan een eigen versleutelings sleutel hebben.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Aanvraag headers voor het opgeven van door de klant opgegeven sleutels

Voor REST-aanroepen kunnen clients de volgende headers gebruiken om informatie over de versleutelings sleutel op een aanvraag voor Blob-opslag veilig door te geven:

|Aanvraagheader | Beschrijving |
|---------------|-------------|
|`x-ms-encryption-key` |Vereist voor zowel schrijf-als Lees aanvragen. Een met base64 gecodeerde AES-256-versleutelings sleutel waarde. |
|`x-ms-encryption-key-sha256`| Vereist voor zowel schrijf-als Lees aanvragen. De met base64 gecodeerde SHA256 van de versleutelings sleutel. |
|`x-ms-encryption-algorithm` | Vereist voor schrijf aanvragen, optioneel voor lees aanvragen. Hiermee geeft u de algoritme op die moet worden gebruikt voor het versleutelen van gegevens met de opgegeven sleutel. Moet AES256 zijn. |

Het opgeven van de versleutelings sleutels voor de aanvraag is optioneel. Als u echter een van de hierboven vermelde kopteksten voor een schrijf bewerking opgeeft, moet u deze allemaal opgeven.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Blob Storage-bewerkingen die door de klant verschafte sleutels worden ondersteund

De volgende bewerkingen voor Blob Storage ondersteunen het verzenden van door de klant verschafte versleutelings sleutels voor een aanvraag:

- [BLOB plaatsen](/rest/api/storageservices/put-blob)
- [Blokkerings lijst plaatsen](/rest/api/storageservices/put-block-list)
- [Blok keren](/rest/api/storageservices/put-block)
- [Blok van URL plaatsen](/rest/api/storageservices/put-block-from-url)
- [Pagina plaatsen](/rest/api/storageservices/put-page)
- [Pagina van URL plaatsen](/rest/api/storageservices/put-page-from-url)
- [Blok toevoegen](/rest/api/storageservices/append-block)
- [BLOB-eigenschappen instellen](/rest/api/storageservices/set-blob-properties)
- [BLOB-meta gegevens instellen](/rest/api/storageservices/set-blob-metadata)
- [BLOB ophalen](/rest/api/storageservices/get-blob)
- [BLOB-eigenschappen ophalen](/rest/api/storageservices/get-blob-properties)
- [BLOB-meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata)
- [Moment opname-BLOB](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Door de klant verschafte sleutels draaien

Als u een versleutelings sleutel wilt draaien die is door gegeven voor de aanvraag, downloadt u de BLOB en uploadt u deze opnieuw met de nieuwe versleutelings sleutel.

> [!IMPORTANT]
> De Azure Portal kan niet worden gebruikt om te lezen van of te schrijven naar een container of BLOB die is versleuteld met een sleutel die in de aanvraag is gegeven.
>
> Zorg ervoor dat u de versleutelings sleutel beveiligt die u opgeeft voor een aanvraag voor Blob-opslag in een beveiligd sleutel archief zoals Azure Key Vault. Als u een schrijf bewerking probeert uit te voeren op een container of BLOB zonder de versleutelings sleutel, mislukt de bewerking en krijgt u geen toegang meer tot het object.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Azure Storage versleuteling versus schijf versleuteling

Azure Storage versleuteling versleutelt de pagina-blobs die back-ups maken van virtuele machines van Azure. Daarnaast kunnen alle schijven van virtuele machines van Azure, met inbegrip van lokale tijdelijke schijven, eventueel worden versleuteld met [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md). Azure Disk Encryption maakt gebruik van een industrie standaard [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows en [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) op Linux om versleutelings oplossingen op basis van besturings systemen te bieden die zijn geïntegreerd met Azure Key Vault.

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure Key Vault?](../../key-vault/key-vault-overview.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling via de Azure-portal](storage-encryption-keys-portal.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van PowerShell](storage-encryption-keys-powershell.md)
- [Door de klant beheerde sleutels configureren voor Azure Storage-versleuteling met behulp van Azure CLI](storage-encryption-keys-cli.md)
