---
title: QnA Maker encryptie van gegevens in rust
titleSuffix: Azure Cognitive Services
description: QnA Maker encryptie van gegevens in rust .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: bd64321a6a7afbac61a63365c77a75120f837e83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79372093"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker encryptie van gegevens in rust

QnA Maker versleutelt uw gegevens automatisch wanneer deze in de cloud worden uitgevoerd, zodat u de beveiligings- en nalevingsdoelstellingen van uw organisatie bereiken.

## <a name="about-encryption-key-management"></a>Over beheer van versleutelingssleutels

Standaard maakt uw abonnement gebruik van door Microsoft beheerde versleutelingssleutels. Er is ook een optie om uw abonnement met uw eigen sleutels te beheren. Door de klant beheerde sleutels (CMK) bieden meer flexibiliteit om toegangsbesturingselementen te maken, te roteren, uit te schakelen en in te trekken. U ook de versleutelingssleutels controleren die worden gebruikt om uw gegevens te beschermen.

QnA Maker maakt gebruik van CMK-ondersteuning vanuit Azure search. U moet [CMK](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)maken in Azure Search met Azure Key Vault. Deze Azure-instantie moet worden gekoppeld aan de QnA Maker-service om deze CMK ingeschakeld te maken.

> [!IMPORTANT]
> Uw Azure Search-servicebron moet na januari 2019 zijn gemaakt en mag niet in de gratis (gedeelde) laag staan. Er is geen ondersteuning voor het configureren van door de klant beheerde sleutels in de Azure-portal.

## <a name="enable-customer-managed-keys"></a>Door de klant beheerde sleutels inschakelen

De QnA Maker-service maakt gebruik van CMK vanuit de Azure Search-service. Volg de volgende stappen om CMK's in te schakelen:

1. Maak een nieuwe Azure Search-instantie en schakel de vereisten in die worden vermeld in de door de [klant beheerde sleutelvereisten voor Azure Cognitive Search.](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites)

   ![Versleutelingsinstellingen weergeven](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Wanneer u een QnA Maker-bron maakt, wordt deze automatisch gekoppeld aan een Azure Search-instantie. Dit kan niet worden gebruikt met CMK. Als u CMK wilt gebruiken, moet u uw nieuw gemaakte exemplaar van Azure Search koppelen dat in stap 1 is gemaakt. In het bijzonder moet u `AzureSearchAdminKey` `AzureSearchName` de en in uw QnA Maker-bron bijwerken.

   ![Versleutelingsinstellingen weergeven](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Maak vervolgens een nieuwe toepassingsinstelling:
   * **Naam:** Stel dit in op`CustomerManagedEncryptionKeyUrl`
   * **Waarde:** dit is de waarde die u in stap 1 hebt gekregen bij het maken van uw Azure Search-exemplaar.

   ![Versleutelingsinstellingen weergeven](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Start de runtime opnieuw. Nu is uw QnA Maker-service CMK-enabled.

## <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn beschikbaar in alle Azure Search-regio's.

## <a name="next-steps"></a>Volgende stappen

* [Versleuteling in Azure Search met CMK's in Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Gegevensversleuteling in rust](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
