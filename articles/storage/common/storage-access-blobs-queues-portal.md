---
title: De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens
titleSuffix: Azure Storage
description: Wanneer u blob- of wachtrijgegevens opent via de Azure-portal, doet de portal aanvragen voor Azure Storage onder de covers. Deze aanvragen voor Azure Storage kunnen worden geverifieerd en geautoriseerd met uw Azure AD-account of de toegangssleutel voor het opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 01/10/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 602be49ef0c60274f1cd016c4f8e870cf033ec7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75866900"
---
# <a name="use-the-azure-portal-to-access-blob-or-queue-data"></a>De Azure-portal gebruiken om toegang te krijgen tot blob- of wachtrijgegevens

Wanneer u blob- of wachtrijgegevens opent via de [Azure-portal,](https://portal.azure.com)doet de portal aanvragen voor Azure Storage onder de covers. Een aanvraag voor Azure Storage kan worden geautoriseerd met uw Azure AD-account of de toegangssleutel voor het opslagaccount. De portal geeft aan welke methode u gebruikt en stelt u in staat om tussen de twee te schakelen als u over de juiste machtigingen beschikt.  

## <a name="permissions-needed-to-access-blob-or-queue-data"></a>Machtigingen die nodig zijn voor toegang tot blob- of wachtrijgegevens

Afhankelijk van hoe u toegang tot blob- of wachtrijgegevens in de Azure-portal wilt autoriseren, hebt u specifieke machtigingen nodig. In de meeste gevallen worden deze machtigingen verstrekt via role-based access control (RBAC). Zie [Wat is role-based access control (RBAC)?](../../role-based-access-control/overview.md)voor meer informatie over RBAC?

### <a name="account-access-key"></a>Accounttoegangssleutel

Als u blob- en wachtrijgegevens wilt openen met de accounttoegangssleutel, moet u een RBAC-rol aan u hebben toegewezen die de RBAC-actie **Microsoft.Storage/storageAccounts/listkeys/action**bevat. Deze RBAC-rol kan een ingebouwde of een aangepaste rol zijn. Ingebouwde rollen die ondersteuning bieden voor **Microsoft.Storage/storageAccounts/listkeys/action** omvatten:

- De functie Azure Resource [Manager-eigenaar](../../role-based-access-control/built-in-roles.md#owner)
- De functie Azure Resource [Manager-bijdrager](../../role-based-access-control/built-in-roles.md#contributor)
- De rol [Opslagaccountbijdrage](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Wanneer u blob- of wachtrijgegevens in de Azure-portal probeert te openen, controleert de portal eerst of u een rol hebt toegewezen met **Microsoft.Storage/storageAccounts/listkeys/action**. Als u een rol bij deze actie hebt toegewezen, gebruikt de portal de accountsleutel voor toegang tot blob- en wachtrijgegevens. Als u geen rol bij deze actie hebt toegewezen, probeert de portal toegang te krijgen tot gegevens met uw Azure AD-account.

> [!NOTE]
> De klassieke functies servicebeheerder en co-beheerder van abonnementen bevatten het equivalent van de rol Azure Resource [Manager-eigenaar.](../../role-based-access-control/built-in-roles.md#owner) De rol **Eigenaar** omvat alle acties, inclusief de **Microsoft.Storage/storageAccounts/listkeys/action,** zodat een gebruiker met een van deze beheerdersrollen ook toegang heeft tot blob- en wachtrijgegevens met de accountsleutel. Zie [De beheerdersrollen van klassieke abonnementen voor](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)meer informatie .

### <a name="azure-ad-account"></a>Azure AD-account

Als u blob- of wachtrijgegevens wilt openen vanuit de Azure-portal met uw Azure AD-account, moeten beide volgende instructies voor u gelden:

- U hebt de rol Azure Resource Manager [Reader](../../role-based-access-control/built-in-roles.md#reader) toegewezen gekregen, minimaal op het niveau van het opslagaccount of hoger. De rol **Reader** verleent de meest beperkte machtigingen, maar een andere Azure Resource Manager-rol die toegang verleent tot opslagaccountbeheerbronnen, is ook aanvaardbaar.
- U hebt een ingebouwde of aangepaste rol toegewezen gekregen die toegang biedt tot blob- of wachtrijgegevens.

De **roltoewijzing reader** of een andere Azure Resource Manager-roltoewijzing is noodzakelijk, zodat de gebruiker opslagaccountbeheerbronnen in de Azure-portal kan bekijken en navigeren. De RBAC-rollen die toegang verlenen tot blob- of wachtrijgegevens verlenen geen toegang tot bronnen voor opslagaccountbeheer. Om blob- of wachtrijgegevens in de portal te openen, heeft de gebruiker machtigingen nodig om door opslagaccountbronnen te navigeren. Zie De rol Reader [toewijzen voor portaltoegang voor](../common/storage-auth-aad-rbac-portal.md#assign-the-reader-role-for-portal-access)meer informatie over deze vereiste .

De ingebouwde rollen die de toegang tot uw blob- of wachtrijgegevens ondersteunen, zijn onder andere:

- [Storage Blob Data Owner:](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)Voor POSIX access control voor Azure Data Lake Storage Gen2.
- [Opslagblobgegevensinzender](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor): machtigingen voor lezen/schrijven/verwijderen voor blobs.
- [OpslagBlob-gegevenslezer:](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)alleen-lezen machtigingen voor blobs.
- [Opslagwachtrijgegevens inzender](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor): machtigingen voor lezen/schrijven/verwijderen voor wachtrijen.
- [Gegevenslezer opslagwachtrij](../../role-based-access-control/built-in-roles.md#storage-queue-data-reader): alleen-lezen machtigingen voor wachtrijen.
    
Aangepaste rollen kunnen verschillende combinaties van dezelfde machtigingen ondersteunen die worden verstrekt door de ingebouwde rollen. Zie [Aangepaste rollen voor Azure-resources](../../role-based-access-control/custom-roles.md) en [Functiedefinities voor Azure-resources voor](../../role-based-access-control/role-definitions.md)meer informatie over het maken van aangepaste RBAC-rollen.

Wachtrijen met een klassieke abonnementsbeheerderworden niet ondersteund. Als u wachtrijen wilt weergeven, moet een gebruiker aan hem de rol Azure Resource Manager **Reader,** de rol **Van de gegevenslezer van de opslagwachtrij** of de rol van de **opslagwachtrijgegevensbijdrage** hebben toegewezen.

> [!IMPORTANT]
> De preview-versie van Storage Explorer in de Azure-portal biedt geen ondersteuning voor het gebruik van Azure AD-referenties om blob- of wachtrijgegevens weer te geven en te wijzigen. Storage Explorer in de Azure-portal gebruikt altijd de accountsleutels om toegang te krijgen tot gegevens. Als u Storage Explorer wilt gebruiken in de Azure-portal, moet u een rol toegewezen krijgen die **Microsoft.Storage/storageAccounts/listkeys/action**bevat.

## <a name="navigate-to-blobs-or-queues-in-the-portal"></a>Navigeren naar blobs of wachtrijen in de portal

Als u blob- of wachtrijgegevens in de portal wilt weergeven, navigeert u naar het **overzicht** voor uw opslagaccount en klikt u op de koppelingen voor blobs of **wachtrijen.** **Queues** U ook naar de gedeelten **blobservice** en **wachtrijservice** in het menu navigeren. 

![Navigeren naar blob- of wachtrijgegevens in de Azure-portal](media/storage-access-blobs-queues-portal/blob-queue-access.png)

## <a name="determine-the-current-authentication-method"></a>De huidige verificatiemethode bepalen

Wanneer u naar een container of een wachtrij navigeert, geeft de Azure-portal aan of u momenteel de accounttoegangssleutel of uw Azure AD-account gebruikt om te verifiëren.

De voorbeelden in deze sectie tonen toegang tot een container en de blobs, maar de portal geeft hetzelfde bericht weer wanneer u een wachtrij en de bijbehorende berichten of wachtrijen voor aanbiedingen opent.

### <a name="account-access-key"></a>Accounttoegangssleutel

Als u de accounttoegangssleutel authenticert, ziet u **Access-sleutel** als verificatiemethode in de portal:

![Momenteel toegang tot containergegevens met de accountsleutel](media/storage-access-blobs-queues-portal/auth-method-access-key.png)

Als u wilt overschakelen naar het gebruik van het Azure AD-account, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als u de juiste machtigingen hebt via de RBAC-rollen die aan u zijn toegewezen, u verdergaan. Als u echter niet over de juiste machtigingen beschikt, ziet u een foutbericht zoals de volgende:

![Fout weergegeven als Azure AD-account geen toegang ondersteunt](media/storage-access-blobs-queues-portal/auth-error-azure-ad.png)

Merk op dat er geen blobs in de lijst worden weergegeven als uw Azure AD-account geen machtigingen heeft om ze te bekijken. Klik op **de switch om toegang te krijgen tot de sleutelkoppeling** om de toegangssleutel opnieuw te gebruiken voor verificatie.

### <a name="azure-ad-account"></a>Azure AD-account

Als u authenticeren met uw Azure AD-account, ziet u **Azure AD-gebruikersaccount** opgegeven als de verificatiemethode in de portal:

![Momenteel toegang tot containergegevens met Azure AD-account](media/storage-access-blobs-queues-portal/auth-method-azure-ad.png)

Als u wilt overschakelen naar het gebruik van de accounttoegangssleutel, klikt u op de koppeling die in de afbeelding is gemarkeerd. Als je toegang hebt tot de accountsleutel, kun je verder gaan. Als u echter geen toegang hebt tot de accountsleutel, ziet u een foutbericht zoals de volgende:

![Fout weergegeven als u geen toegang hebt tot accountsleutel](media/storage-access-blobs-queues-portal/auth-error-access-key.png)

Houd er rekening mee dat er geen blobs in de lijst worden weergegeven als u geen toegang hebt tot de accountsleutels. Klik op de koppeling **Overschakelen naar Azure AD-gebruikersaccount** om uw Azure AD-account opnieuw te gebruiken voor verificatie.

## <a name="next-steps"></a>Volgende stappen

- [Toegang tot Azure-blobs en wachtrijen verifiëren met Azure Active Directory](storage-auth-aad.md)
- [Toegang verlenen tot Azure-containers en wachtrijen met RBAC in de Azure-portal](storage-auth-aad-rbac-portal.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van Azure CLI](storage-auth-aad-rbac-cli.md)
- [Toegang verlenen tot Azure blob en wachtrijgegevens met RBAC met behulp van PowerShell](storage-auth-aad-rbac-powershell.md)
