---
title: De Azure-portal gebruiken om door de klant beheerde sleutels te configureren
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van de Azure-portal om door klanten beheerde sleutels te configureren met Azure Key Vault voor Azure Storage-versleuteling.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/19/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: e7878cae7bc6aabf1db58bfd63338955b9e830d3
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478235"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Door de klant beheerde sleutels configureren met Azure Key Vault met behulp van de Azure-portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel ziet u hoe u een Azure Key Vault configureert met door de klant beheerde sleutels met behulp van de [Azure-portal.](https://portal.azure.com/) Zie [Snelstart: Een geheim instellen en ophalen uit Azure Key Vault met behulp van de Azure-portal](../../key-vault/quick-create-portal.md)voor meer informatie over het maken van een sleutelkluis met de Azure-portal.

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Als u door de klant beheerde sleutels met Azure Storage-versleuteling gebruikt, moeten twee eigenschappen worden ingesteld op de sleutelkluis, **Soft Delete** en **Do Not Purge.** Deze eigenschappen zijn standaard niet ingeschakeld, maar kunnen worden ingeschakeld met PowerShell of Azure CLI op een nieuwe of bestaande sleutelkluis.

Zie de secties getiteld **Soft-delete** en **Inschakelen van zuiveringsbeveiliging** inschakelen in een van de volgende artikelen voor meer informatie over het inschakelen van deze eigenschappen op een bestaand sleutelkluis:

- [Soft-delete gebruiken met PowerShell](../../key-vault/key-vault-soft-delete-powershell.md).
- [Soft-delete gebruiken met CLI](../../key-vault/key-vault-soft-delete-cli.md).

Alleen 2048-bits RSA- en RSA-HSM-sleutels worden ondersteund met Azure Storage-versleuteling. Zie **Key Vault-sleutels** in [Over Azure Key Vault-sleutels, -geheimen en -certificaten](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Voer de volgende stappen uit om door klanten beheerde sleutels in de Azure-portal in te schakelen:

1. Ga naar uw opslagaccount.
1. Klik in het **pagina-blad Instellingen** voor het opslagaccount op **Versleuteling**. Selecteer de optie **Door de klant beheerde sleutels,** zoals in de volgende afbeelding wordt weergegeven.

    ![Portal-schermafbeelding met versleutelingsoptie](./media/storage-encryption-keys-portal/portal-configure-encryption-keys.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, u een sleutel opgeven die u aan het opslagaccount koppelen.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutelURI in de Azure-portal wilt vinden, navigeert u naar uw sleutelkluis en selecteert u de instelling **Sleutels.** Selecteer de gewenste sleutel en klik op de toets om de versies te bekijken. Selecteer een belangrijke versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **Sleutel-id,** dat de URI biedt.

    ![Schermafbeelding van de sleutelkluissleutel URI](media/storage-encryption-keys-portal/portal-copy-key-identifier.png)

1. Kies in de **coderingsinstellingen** voor uw opslagaccount de optie Uri van de **toets invoeren.**
1. Plak de URI die u hebt gekopieerd in het veld **Sleutel URI.**

   ![Schermafbeelding van het invoeren van sleutelURI](./media/storage-encryption-keys-portal/portal-specify-key-uri.png)

1. Geef het abonnement op dat de sleutelkluis bevat.
1. Sla uw wijzigingen op.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutelkluis opgeven

Als u een sleutel uit een sleutelkluis wilt opgeven, moet u eerst ervoor zorgen dat u een sleutelkluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel uit een sleutelkluis op te geven:

1. Kies de optie **Selecteren in Key Vault.**
1. Selecteer de sleutelkluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutelkluis.

   ![Schermafbeelding van de optie sleuteloptie met door de klant beheerde knop](./media/storage-encryption-keys-portal/portal-select-key-from-key-vault.png)

1. Sla uw wijzigingen op.

## <a name="update-the-key-version"></a>De belangrijkste versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u het opslagaccount bij om de nieuwe versie te gebruiken. Volg deze stappen:

1. Navigeer naar uw opslagaccount en geef de **versleutelingsinstellingen** weer.
1. Voer de URI in voor de nieuwe sleutelversie. U afwisselend de sleutelkluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor Azure Storage-versleuteling:

1. Navigeer naar uw opslagaccount en geef de **versleutelingsinstellingen** weer.
1. Voer de URI in voor de nieuwe sleutel. U afwisselend de sleutelkluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door klanten beheerde sleutels uitschakelt, wordt uw opslagaccount opnieuw versleuteld met door Microsoft beheerde sleutels. Voer de volgende stappen uit om door klanten beheerde sleutels uit te schakelen:

1. Navigeer naar uw opslagaccount en geef de **versleutelingsinstellingen** weer.
1. Schakel het selectievakje naast de instelling **Uw eigen sleutel gebruiken** uit.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage-versleuteling voor gegevens in rust](storage-service-encryption.md)
- [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
