---
title: Face service-versleuteling van data-at-rest
titleSuffix: Azure Cognitive Services
description: Face service-versleuteling van data-at-rest.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 1e0275c91b2243132650be7af256071589091c4b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83201935"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Face service-versleuteling van data-at-rest

Met de service Face worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de face service-versleuteling beschermt u uw gegevens en kunt u voldoen aan de beveiligings-en nalevings verplichtingen van uw organisatie.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Door de klant beheerde sleutels zijn alleen beschikbaar in de prijs categorie E0. Als u de mogelijkheid wilt aanvragen om door de klant beheerde sleutels te gebruiken, vult u het [aanvraag formulier voor de klant beheerde sleutel](https://aka.ms/cogsvc-cmk)in. Het duurt ongeveer 3-5 werk dagen voordat de status van uw aanvraag wordt weer gegeven. Afhankelijk van de vraag, kunt u in een wachtrij plaatsen en worden goedgekeurd als er ruimte beschikbaar is. Nadat u hebt goedgekeurd voor het gebruik van CMK met de face-service, moet u een nieuwe gezichts bron maken en E0 selecteren als prijs categorie. Zodra uw gezichts bron met de prijs categorie E0 is gemaakt, kunt u Azure Key Vault gebruiken om uw beheerde identiteit in te stellen.

### <a name="regional-availability"></a>Regionale beschikbaarheid

Door de klant beheerde sleutels zijn momenteel beschikbaar in deze regio's:

* US - zuid-centraal
* VS - west 2
* VS - oost
* VS (overheid) - Virginia

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## <a name="next-steps"></a>Volgende stappen

* [Klant van Face service-aanvraag formulier voor beheerde sleutels](https://aka.ms/cogsvc-cmk)
* [Meer informatie over Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)


