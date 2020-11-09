---
title: QnA Maker versleuteling van gegevens in rust
titleSuffix: Azure Cognitive Services
description: Micro soft biedt door micro soft beheerde coderings sleutels en kunt u ook uw Cognitive Services-abonnementen beheren met uw eigen sleutels, met de naam door de klant beheerde sleutels (CMK). Dit artikel bevat informatie over gegevens versleuteling in rust voor QnA Maker en hoe u CMK inschakelt en beheert.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 1ee3c3942ee7d01fa174947f5d9c278cddaf0424
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376911"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker versleuteling van gegevens in rust

QnA Maker versleutelt uw gegevens automatisch wanneer deze in de cloud worden bewaard, zodat u kunt voldoen aan de beveiligings-en nalevings doelen van uw organisatie.

## <a name="about-encryption-key-management"></a>Over het beheer van versleutelings sleutels

Uw abonnement maakt standaard gebruik van door Microsoft beheerde versleutelingssleutels. Er is ook de mogelijkheid om uw abonnement te beheren met uw eigen sleutels, genaamd door de klant beheerde sleutels (CMK). CMK biedt meer flexibiliteit voor het maken, draaien, uitschakelen en intrekken van toegangs beheer. U kunt ook de versleutelingssleutels controleren die worden gebruikt voor het beveiligen van uw gegevens. Als CMK is geconfigureerd voor uw abonnement, wordt er dubbele versleuteling gegeven, die een tweede beveiligingslaag biedt, terwijl u de versleutelings sleutel via uw Azure Key Vault kunt beheren.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele versie)](#tab/v1)

QnA Maker maakt gebruik van CMK-ondersteuning van Azure Search. [CMK configureren in azure Search met behulp van Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Dit Azure-exemplaar moet worden gekoppeld aan QnA Maker service om ervoor te zorgen dat het CMK is ingeschakeld.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-versie)](#tab/v2)

QnA Maker maakt gebruik [van CMK-ondersteuning van Azure Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)en koppelt automatisch de gegeven CMK om de gegevens te versleutelen die zijn opgeslagen in de Azure search-index.

---

> [!IMPORTANT]
> Uw Azure Search-service resource moet na januari 2019 zijn gemaakt en kan niet in de laag gratis (gedeeld) staan. Er is geen ondersteuning voor het configureren van door de klant beheerde sleutels in de Azure Portal.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

De QnA Maker-service gebruikt CMK uit de Azure Search-service. Volg deze stappen om CMKs in te scha kelen:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabiele versie)](#tab/v1)

1. Maak een nieuw exemplaar van Azure Search en schakel de vereisten in die worden vermeld in de door de [klant beheerde sleutel vereisten voor Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Versleutelings instellingen weer geven 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Wanneer u een QnA Maker resource maakt, wordt deze automatisch gekoppeld aan een Azure Search-exemplaar. Dit exemplaar kan niet worden gebruikt met CMK. Als u CMK wilt gebruiken, moet u het nieuwe exemplaar van Azure Search koppelen dat in stap 1 is gemaakt. U moet in het bijzonder de `AzureSearchAdminKey` en `AzureSearchName` in uw QnA Maker resource bijwerken.

   ![Versleutelings instellingen weer geven 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Maak vervolgens een nieuwe toepassings instelling:
   * **Naam** : ingesteld op `CustomerManagedEncryptionKeyUrl`
   * **Waarde** : gebruik de waarde die u in stap 1 hebt gekregen bij het maken van uw Azure Search-exemplaar.

   ![Versleutelings instellingen weer geven 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Wanneer u klaar bent, start u de runtime opnieuw. Uw QnA Maker-service is nu CMK ingeschakeld.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker beheerd (preview-versie)](#tab/v2)

1.  Ga naar het tabblad **versleuteling** van uw QnA Maker Managed (preview)-service.
2.  Selecteer de optie door de **klant beheerde sleutels** . Geef de details van de door de [klant beheerde sleutels](https://docs.microsoft.com/azure/storage/common/customer-managed-keys-configure-key-vault?tabs=portal) op en klik op **Opslaan**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="CMK-instelling voor QnA Maker Managed (preview)" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Bij een geslaagde opslag wordt de CMK gebruikt voor het versleutelen van de gegevens die zijn opgeslagen in de index van de Azure Search.

> [!IMPORTANT]
> U kunt het beste uw CMK instellen in een nieuwe Azure Cognitive Search-service voordat u de kennis bases maakt. Als u CMK in een QnA Maker-service met bestaande kennis bases hebt ingesteld, kunt u de toegang tot deze services verliezen. Lees meer over het [werken met versleutelde inhoud](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#work-with-encrypted-content) in azure cognitieve Search.

> [!NOTE]
> Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [Cognitive Services Customer-Managed aanvraag formulier](https://aka.ms/cogsvc-cmk)in.

---

## <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn beschikbaar in alle Azure Search regio's.

## <a name="encryption-of-data-in-transit"></a>Versleuteling van gegevens die onderweg zijn

QnA Maker portal wordt uitgevoerd in de browser van de gebruiker. Elke actie activeert een directe aanroep naar de respectieve cognitieve Service-API. QnA Maker is daarom compatibel met gegevens die onderweg zijn.
Omdat de QnA Maker Portal service echter wordt gehost in West-US, is het nog steeds niet ideaal voor klanten die geen gebruik maken van de VS. 

## <a name="next-steps"></a>Volgende stappen

* [Versleuteling in Azure Search met behulp van CMKs in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Versleuteling van inactieve gegevens](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)