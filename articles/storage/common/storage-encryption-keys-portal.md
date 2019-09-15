---
title: Door de klant beheerde sleutels configureren voor Azure Storage versleuteling van de Azure Portal
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels voor Azure Storage versleuteling. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 892cbe66222626a6847ad7a5b6c990d23991c182
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002256"
---
# <a name="configure-customer-managed-keys-for-azure-storage-encryption-from-the-azure-portal"></a>Door de klant beheerde sleutels configureren voor Azure Storage versleuteling van de Azure Portal

[!INCLUDE [storage-encryption-configure-keys-include](../../../includes/storage-encryption-configure-keys-include.md)]

In dit artikel wordt beschreven hoe u een sleutel kluis kunt configureren met door de klant beheerde sleutels met behulp van de [Azure Portal](https://portal.azure.com/). Ga [voor meer informatie over het maken van een sleutel kluis met behulp van de Azure Portal naar Quick Start: Stel een geheim in en haal het op uit Azure Key Vault](../../key-vault/quick-create-portal.md)met behulp van de Azure Portal. 


> [!IMPORTANT]
> Het gebruik van door de klant beheerde sleutels met Azure Storage versleuteling vereist dat er voor de sleutel kluis twee vereiste eigenschappen zijn geconfigureerd, worden **verwijderd** en kunnen **niet worden**verwijderd. Deze eigenschappen zijn standaard ingeschakeld wanneer u een nieuwe sleutel kluis maakt in de Azure Portal. Als u deze eigenschappen echter wilt inschakelen voor een bestaande sleutel kluis, moet u Power shell of Azure CLI gebruiken.
> Alleen RSA-sleutels en sleutel grootte 2048 worden ondersteund.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Ga naar uw opslagaccount.
1. Klik op de Blade **instellingen** voor het opslag account op **versleuteling**. Selecteer de optie **uw eigen sleutel gebruiken** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm opname van de portal met versleutelings optie](./media/storage-encryption-keys-portal/ssecmk1.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, hebt u de mogelijkheid om een sleutel op te geven die u wilt koppelen aan het opslag account.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als een URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de instellingen ervan weer te geven. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](media/storage-encryption-keys-portal/key-uri-portal.png)

1. Kies in de **versleutelings** instellingen voor uw opslag account de optie **sleutel-URI opgeven** .
1. Geef in het veld **sleutel-URI** de URI op.

   ![Scherm afbeelding die laat zien hoe de sleutel-URI moet worden ingevoerd](./media/storage-encryption-keys-portal/ssecmk2.png)

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutel kluis opgeven

Als u een sleutel van een sleutel kluis wilt opgeven, moet u eerst controleren of u een sleutel kluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel van een sleutel kluis op te geven:

1. Kies de optie **selecteren uit Key Vault** .
2. Kies de sleutel kluis met de sleutel die u wilt gebruiken.
3. Kies de sleutel in de sleutel kluis.

   ![Scherm afbeelding met door de klant beheerde sleutel optie](./media/storage-encryption-keys-portal/ssecmk3.png)

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, moet u het opslag account bijwerken voor gebruik van de nieuwe versie. Volg deze stappen:

1. Navigeer naar uw opslag account en geef de **versleutelings** instellingen weer.
1. Geef de URI op voor de nieuwe sleutel versie. U kunt ook de sleutel kluis en de sleutel opnieuw selecteren om de versie bij te werken.

## <a name="next-steps"></a>Volgende stappen

- [Azure Storage versleuteling voor Data-at-rest](storage-service-encryption.md)
- [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
