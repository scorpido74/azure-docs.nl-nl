---
title: SaaS-fulfillment-Api's in micro soft Commercial Marketplace
description: Een inleiding tot de fulfillment-Api's waarmee u uw SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace kunt integreren.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: ba1b158bc529b148a8e3138d122c13ead19e073e
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82858084"
---
# <a name="saas-fulfillment-apis-in-microsoft-commercial-marketplace"></a>SaaS-fulfillment-Api's in micro soft Commercial Marketplace

Met de SaaS-fulfillment-Api's kunnen onafhankelijke software leveranciers (Isv's) hun SaaS-toepassingen integreren in Microsoft AppSource en Azure Marketplace. Met deze Api's kunnen ISV-toepassingen deel nemen aan alle commerce ingeschakelde kanalen: directe, partner-LED (wederverkoper) en veld-LED. Ze zijn verplicht om transactable SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace weer te geven.

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
