---
title: Toegangssleutels voor accounts beheren
titleSuffix: Azure Storage
description: Meer informatie over het weergeven, beheren en roteren van uw toegangssleutels voor opslagaccount.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/31/2020
ms.author: tamram
ms.openlocfilehash: 50c0980800bbc9b2951bf9107114c1a4d9265558
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454659"
---
# <a name="manage-storage-account-access-keys"></a>Toegangssleutels voor opslagaccount beheren

Wanneer u een opslagaccount maakt, genereert Azure twee toegangssleutels voor 512-bits opslagaccount. Deze sleutels kunnen worden gebruikt om de toegang tot gegevens in uw opslagaccount te autoriseren via de machtiging Met gedeelde sleutel.

Microsoft raadt u aan Azure Key Vault te gebruiken om uw toegangssleutels te beheren en uw sleutels regelmatig te roteren en te regenereren. Met Azure Key Vault u uw sleutels eenvoudig roteren zonder onderbreking van uw toepassingen. U uw toetsen ook handmatig draaien.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Toegangstoetsen en verbindingstekenreeks weergeven

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault gebruiken om uw toegangssleutels te beheren

Microsoft raadt aan azure key vault te gebruiken om uw toegangssleutels te beheren en te roteren. Uw toepassing heeft veilig toegang tot uw sleutels in Key Vault, zodat u voorkomen dat ze worden opgeslagen met uw toepassingscode. Zie de volgende artikelen voor meer informatie over het gebruik van Key Vault voor sleutelbeheer:

- [Opslagaccountsleutels beheren met Azure Key Vault en PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Opslagaccountsleutels beheren met Azure Key Vault en de Azure CLI](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Toegangstoetsen handmatig roteren

Microsoft raadt u aan uw toegangssleutels periodiek te roteren om uw opslagaccount veilig te houden. Gebruik indien mogelijk Azure Key Vault om uw toegangssleutels te beheren. Als u Key Vault niet gebruikt, moet u uw sleutels handmatig roteren.

Er zijn twee toegangssleutels toegewezen, zodat u uw sleutels roteren. Het hebben van twee sleutels zorgt ervoor dat uw toepassing gedurende het hele proces toegang heeft tot Azure Storage.

> [!WARNING]
> Het regenereren van uw toegangssleutels kan van invloed zijn op alle toepassingen of Azure-services die afhankelijk zijn van de opslagaccountsleutel. Alle clients die de accountsleutel gebruiken om toegang te krijgen tot het opslagaccount, moeten worden bijgewerkt om de nieuwe sleutel te gebruiken, inclusief mediaservices, cloud-, desktop- en mobiele toepassingen en grafische gebruikersinterfacetoepassingen voor Azure Storage, zoals [Azure Storage Explorer.](https://azure.microsoft.com/features/storage-explorer/)

Volg dit proces om uw opslagaccountsleutels te roteren:

1. Werk de verbindingstekenreeksen in uw toepassingscode bij om de secundaire sleutel te gebruiken.
2. Genereer de primaire toegangssleutel voor uw opslagaccount opnieuw. Klik op het blade **Access Keys** in de Azure-portal op **Toets regenereren 1**en klik vervolgens op **Ja** om te bevestigen dat u een nieuwe sleutel wilt genereren.
3. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
4. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

> [!NOTE]
> Microsoft raadt aan om slechts één van de sleutels in al uw toepassingen tegelijkertijd te gebruiken. Als u toets 1 op sommige plaatsen en Toets 2 in andere gebruikt, u uw sleutels niet roteren zonder dat een toepassing de toegang verliest.

Als u de toegangssleutels van een account wilt roteren, moet de gebruiker een servicebeheerder zijn of een RBAC-rol krijgen toegewezen die de **Microsoft.Storage/storageAccounts/regeneratekey/action**bevat. Sommige ingebouwde RBAC-rollen die deze actie bevatten, zijn de rollen van de functie **rolrollen Van**de eigenaar, **de inzender**en **de opslagaccountsleutel.** Zie Klassieke functies voor [abonnementsbeheerders, Azure RBAC-rollen en Azure AD-rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md)voor meer informatie over de functie Servicebeheerder. Zie de sectie **Opslag** in [azure-ingebouwde rollen voor Azure RBAC voor](../../role-based-access-control/built-in-roles.md#storage)gedetailleerde informatie over ingebouwde RBAC-rollen voor Azure.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure-opslagaccount](storage-account-overview.md)
- [Create a storage account](storage-account-create.md)
