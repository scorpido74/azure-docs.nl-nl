---
title: SaaS-fulfillment-Api's | Azure Marketplace
description: Introduceert de versies van de fulfillment-Api's waarmee u uw SaaS-aanbiedingen kunt integreren met de Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: 92b1c52457fa92709381124480c05a5f636167f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80275727"
---
# <a name="saas-fulfillment-apis"></a>SaaS-uitvoerings-API's

Met de SaaS-fulfillment-Api's kunnen onafhankelijke software leveranciers (Isv's) hun SaaS-toepassingen integreren met de Azure Marketplace. Met deze Api's kunnen ISV-toepassingen deel nemen aan alle commerce ingeschakelde kanalen: direct, partner-LED (wederverkoper) en veld-LED.  Ze zijn vereist voor het weer geven van transactable SaaS-aanbiedingen op de Azure Marketplace.

> [!WARNING]
> De huidige versie van deze API is versie 2, die moet worden gebruikt voor alle nieuwe SaaS-aanbiedingen.  Versie 1 van de API is afgeschaft en wordt onderhouden ter ondersteuning van bestaande aanbiedingen.


## <a name="business-model-support"></a>Business model-ondersteuning

Deze API biedt ondersteuning voor de volgende mogelijkheden voor bedrijfs modellen. U kunt:

* Geef meerdere plannen op voor een aanbieding. Deze abonnementen hebben verschillende functionaliteit en kunnen op verschillende manieren worden geprijsd.
* Geef een aanbieding op per site of op basis van een facturerings model per gebruiker.
* Betaal opties voor maandelijks en jaarlijks (betaalde voor zijde).
* Bied persoonlijke prijzen aan een klant op basis van een onderhandelde zakelijke overeenkomst.


## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, registreert u uw SaaS-toepassing in de [Azure Portal](https://ms.portal.azure.com) zoals uitgelegd in [een Azure AD-toepassing registreren](./pc-saas-registration.md).  Daarna gebruikt u de meest recente versie van deze interface voor ontwikkeling: [SaaS fulfillment API-versie 2](./pc-saas-fulfillment-api-v2.md).
