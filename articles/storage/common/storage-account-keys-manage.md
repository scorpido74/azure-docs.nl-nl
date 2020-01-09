---
title: Toegangssleutels voor accounts beheren
titleSuffix: Azure Storage
description: Meer informatie over het weer geven, beheren en draaien van de toegangs sleutels van uw opslag account.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/18/2019
ms.author: tamram
ms.openlocfilehash: 68d0ef14a7b5967d6a4535da85d32128c3a12a19
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75486727"
---
# <a name="manage-storage-account-access-keys"></a>Toegangs sleutels voor opslag accounts beheren

Wanneer u een opslag account maakt, genereert Azure 2 512-bits toegangs sleutels voor opslag accounts. Deze sleutels kunnen worden gebruikt om toegang te verlenen tot gegevens in uw opslag account via de verificatie van de gedeelde sleutel.

Micro soft raadt u aan Azure Key Vault te gebruiken voor het beheren van uw toegangs sleutels en om uw sleutels regel matig te draaien en opnieuw te genereren. Met Azure Key Vault kunt u eenvoudig uw sleutels zonder onderbreking naar uw toepassingen draaien. U kunt uw sleutels ook hand matig draaien.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-access-keys-and-connection-string"></a>Toegangs sleutels en connection string weer geven

[!INCLUDE [storage-view-keys-include](../../../includes/storage-view-keys-include.md)]

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Azure Key Vault gebruiken om uw toegangs sleutels te beheren

Micro soft raadt u aan Azure Key Vault te gebruiken om uw toegangs sleutels te beheren en te draaien. Uw toepassing kan veilig toegang krijgen tot uw sleutels in Key Vault, zodat u ze niet kunt opslaan met de code van uw toepassing. Raadpleeg de volgende artikelen voor meer informatie over het gebruik van Key Vault voor sleutel beheer:

- [Sleutels voor opslag accounts beheren met Azure Key Vault en Power shell](../../key-vault/key-vault-overview-storage-keys-powershell.md)
- [Sleutels voor opslag accounts beheren met Azure Key Vault en de Azure CLI](../../key-vault/key-vault-ovw-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Toegangs sleutels hand matig draaien

Micro soft raadt u aan uw toegangs sleutels regel matig te draaien om uw opslag account veilig te houden. Gebruik, indien mogelijk, Azure Key Vault om uw toegangs sleutels te beheren. Als u Key Vault niet gebruikt, moet u de sleutels hand matig draaien.

Er zijn twee toegangs sleutels toegewezen zodat u uw sleutels kunt draaien. Als u twee sleutels hebt, zorgt u ervoor dat uw toepassing gedurende het proces toegang tot Azure Storage houdt.

> [!WARNING]
> Het opnieuw genereren van de toegangs sleutels kan invloed hebben op alle toepassingen of Azure-Services die afhankelijk zijn van de sleutel van het opslag account. Clients die gebruikmaken van de account sleutel voor toegang tot het opslag account moeten worden bijgewerkt om de nieuwe sleutel te gebruiken, inclusief Media Services, Cloud, desktop-en mobiele toepassingen en Graphical User Interface toepassingen voor Azure Storage, zoals [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

Volg deze procedure om de sleutels van uw opslag account te roteren:

1. Werk de verbindings reeksen in de toepassings code bij om de secundaire sleutel te gebruiken.
2. Genereer de primaire toegangssleutel voor uw opslagaccount opnieuw. Klik op de Blade **toegangs sleutels** in de Azure Portal op **key1 opnieuw genereren**en klik vervolgens op **Ja** om te bevestigen dat u een nieuwe sleutel wilt genereren.
3. Werk de verbindingsreeksen in uw code bij, zodat deze verwijzen naar de nieuwe primaire toegangssleutel.
4. Genereer de secundaire toegangssleutel op dezelfde manier opnieuw.

> [!NOTE]
> Micro soft raadt u aan om op hetzelfde moment slechts één van de sleutels in al uw toepassingen te gebruiken. Als u Key 1 op sommige locaties en sleutel 2 in andere gebruikt, kunt u de sleutels niet draaien zonder dat de toepassing de toegang verliest.

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Storage-account](storage-account-overview.md)
- [Een opslagaccount maken](storage-quickstart-create-account.md)
