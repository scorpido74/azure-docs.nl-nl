---
title: Metrics Advisor-service versleuteling
titleSuffix: Azure Cognitive Services
description: Metrics Advisor-service versleuteling van gegevens in rust.
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: mbullwin
ms.openlocfilehash: 9a7a914acd1358243c1e8a29f59dadf4fac46957
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046924"
---
# <a name="metrics-advisor-service-encryption-of-data-at-rest"></a>Metrics Advisor-service versleuteling van gegevens in rust

Met de data Advisor-service worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de metrische gegevens van de Advisor-service wordt de informatie beschermd en kunnen we u helpen om te voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [aanvraag formulier voor metrische gegevens Advisor-Service Customer-Managed](https://aka.ms/cogsvc-cmk). Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Na goed keuring voor het gebruik van CMK met de metrics Advisor-service, moet u een nieuwe resource Advisor maken en selecteert u E0 als prijs categorie. Zodra uw metrische Advisor-resource met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Volgende stappen

* [Aanvraag formulier voor metrische gegevens Advisor-service Customer-Managed](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
