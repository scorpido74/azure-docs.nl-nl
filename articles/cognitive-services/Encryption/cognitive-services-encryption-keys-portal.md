---
title: Gebruik de Azure Portal voor het configureren van door de klant beheerde sleutels
titleSuffix: Cognitive Services
description: Meer informatie over het gebruik van de Azure Portal voor het configureren van door de klant beheerde sleutels met Azure Key Vault. Door de klant beheerde sleutels bieden u de mogelijkheid om toegangs beheer te maken, te draaien, uit te scha kelen en in te trekken.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 04a8a8d2520376931f2bb7727c1e751b83f6315f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81455254"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Door de klant beheerde sleutels met Azure Key Vault configureren met behulp van de Azure Portal

U moet Azure Key Vault gebruiken om uw door de klant beheerde sleutels op te slaan. U kunt zelf sleutels maken en deze opslaan in een sleutel kluis, of u kunt de Azure Key Vault-Api's gebruiken om sleutels te genereren. De Cognitive Services resource en de sleutel kluis moeten zich in dezelfde regio bevinden en in dezelfde Azure Active Directory (Azure AD)-Tenant, maar ze kunnen zich in verschillende abonnementen bevinden. Zie [Wat is Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)voor meer informatie over Azure Key Vault.

In dit artikel wordt beschreven hoe u een Azure Key Vault met door de klant beheerde sleutels configureert met behulp van de [Azure Portal](https://portal.azure.com/). Voor informatie over het maken van een sleutel kluis met behulp van de Azure Portal, raadpleegt u [Quick Start: een geheim instellen en ophalen van Azure Key Vault met behulp van de Azure Portal](../../key-vault/secrets/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Azure Key Vault configureren

Voor het gebruik van door de klant beheerde sleutels moet u twee eigenschappen instellen op de sleutel kluis, **verwijderen** en **niet wissen**. Deze eigenschappen zijn niet standaard ingeschakeld, maar kunnen worden ingeschakeld met Power shell of Azure CLI in een nieuwe of bestaande sleutel kluis.

> [!IMPORTANT]
> Als u niet de opties **voorlopig verwijderen** en **niet wissen** hebt ingeschakeld en u de sleutel verwijdert, kunt u de gegevens in uw cognitieve service resource niet herstellen.

Voor meer informatie over het inschakelen van deze eigenschappen voor een bestaande sleutel kluis raadpleegt u de secties met het **inschakelen van zacht verwijderen** en het **inschakelen van beveiliging opschonen** in een van de volgende artikelen:

- [Voorlopig verwijderen gebruiken met Power shell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- [Voorlopig verwijderen gebruiken met CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Alleen RSA-sleutels met een grootte van 2048 worden ondersteund met Azure Storage versleuteling. Zie **Key Vault sleutels** in [over Azure Key Vault sleutels, geheimen en certificaten](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)voor meer informatie over sleutels.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

Voer de volgende stappen uit om door de klant beheerde sleutels in te scha kelen in de Azure Portal:

1. Navigeer naar uw Cognitive Services-resource.
1. Klik op de Blade **instellingen** voor uw Cognitive Services bron op **versleuteling**. Selecteer de optie door de **klant beheerde sleutels** , zoals wordt weer gegeven in de volgende afbeelding.

    ![Scherm afbeelding die laat zien hoe u door de klant beheerde sleutels selecteert](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Een sleutel opgeven

Nadat u door de klant beheerde sleutels hebt ingeschakeld, hebt u de mogelijkheid om een sleutel op te geven die u wilt koppelen aan de Cognitive Services resource.

### <a name="specify-a-key-as-a-uri"></a>Een sleutel opgeven als een URI

Voer de volgende stappen uit om een sleutel als URI op te geven:

1. Als u de sleutel-URI in de Azure Portal wilt zoeken, navigeert u naar uw sleutel kluis en selecteert u de instelling **sleutels** . Selecteer de gewenste sleutel en klik vervolgens op de sleutel om de versies ervan weer te geven. Selecteer een sleutel versie om de instellingen voor die versie weer te geven.
1. Kopieer de waarde van het veld **sleutel-id** , dat de URI levert.

    ![Scherm opname van sleutel kluis sleutel-URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Kies in de **versleutelings** instellingen voor uw opslag account de optie **sleutel-URI opgeven** .
1. Plak de URI die u hebt gekopieerd in het veld **sleutel-URI** .

   ![Scherm afbeelding die laat zien hoe de sleutel-URI moet worden ingevoerd](../media/cognitive-services-encryption/ssecmk2.png)

1. Geef het abonnement op dat de sleutel kluis bevat.
1. Sla uw wijzigingen op.

### <a name="specify-a-key-from-a-key-vault"></a>Een sleutel uit een sleutel kluis opgeven

Als u een sleutel van een sleutel kluis wilt opgeven, moet u eerst controleren of u een sleutel kluis hebt die een sleutel bevat. Voer de volgende stappen uit om een sleutel van een sleutel kluis op te geven:

1. Kies de optie **selecteren uit Key Vault** .
1. Selecteer de sleutel kluis met de sleutel die u wilt gebruiken.
1. Selecteer de sleutel in de sleutel kluis.

   ![Scherm afbeelding met door de klant beheerde sleutel optie](../media/cognitive-services-encryption/ssecmk3.png)

1. Sla uw wijzigingen op.

## <a name="update-the-key-version"></a>De sleutel versie bijwerken

Wanneer u een nieuwe versie van een sleutel maakt, werkt u de Cognitive Services resource bij om de nieuwe versie te gebruiken. Volg deze stappen:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel versie. U kunt ook de sleutel kluis en de sleutel opnieuw selecteren om de versie bij te werken.
1. Sla uw wijzigingen op.

## <a name="use-a-different-key"></a>Een andere sleutel gebruiken

Voer de volgende stappen uit om de sleutel te wijzigen die wordt gebruikt voor versleuteling:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Voer de URI in voor de nieuwe sleutel. U kunt ook de sleutel kluis selecteren en een nieuwe sleutel kiezen.
1. Sla uw wijzigingen op.

## <a name="disable-customer-managed-keys"></a>Door de klant beheerde sleutels uitschakelen

Wanneer u door de klant beheerde sleutels uitschakelt, wordt uw Cognitive Services-resource vervolgens versleuteld met door micro soft beheerde sleutels. Voer de volgende stappen uit om door de klant beheerde sleutels uit te scha kelen:

1. Navigeer naar uw Cognitive Services-resource en geef de **versleutelings** instellingen weer.
1. Schakel het selectie vakje naast de instelling **uw eigen sleutel gebruiken** uit.

## <a name="next-steps"></a>Volgende stappen

* [Wat is Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)?
* [Cognitive Services aanvraag formulier voor door de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)
* [Gezichts Services versleuteling van gegevens in rust](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker versleuteling van gegevens in rust](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Language Understanding-service versleuteling van gegevens in rust](../LUIS/luis-encryption-of-data-at-rest.md)
* [Content Moderator versleuteling van gegevens in rust](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Conversie van gegevens in rust versleutelen](../translator/translator-encryption-of-data-at-rest.md)
* [Personalisatie versleuteling van gegevens in rust](../personalizer/personalizer-encryption-of-data-at-rest.md)
