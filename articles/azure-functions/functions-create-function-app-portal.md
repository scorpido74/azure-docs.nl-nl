---
title: Een functie-app maken vanuit de Azure Portal
description: Maak een nieuwe functie-app in Azure vanuit de portal.
ms.topic: how-to
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 6c17dc28d7eea4897759c1c832fdb1aba7f1e445
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756542"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Een functie-app maken vanuit Azure Portal

In dit onderwerp ziet u hoe u Azure-functies gebruiken om een functie-app in de Azure-portal te maken. Een functie-app is de container die als host fungeert voor het uitvoeren van afzonderlijke functies. 

## <a name="create-a-function-app"></a>Een functie-app maken

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Nadat de functie-app is gemaakt, kunt u afzonderlijke functies in een of meer verschillende talen maken. U kunt functies maken [met behulp van de portal](functions-create-first-azure-function.md#create-function), via [continue implementatie](functions-continuous-deployment.md) of door te [uploaden via FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Service-abonnementen

Azure Functions heeft drie verschillende serviceplannen: verbruiksplan, Premium-abonnement en Een speciaal abonnement (App Service). U moet uw serviceplan kiezen wanneer uw functie-app wordt gemaakt en deze kan vervolgens niet worden gewijzigd. Zie [Een Azure Functions-hostingabonnement kiezen](functions-scale.md) voor meer informatie.

Als u van plan bent JavaScript-functies uit te voeren op een Dedicated (App Service)-abonnement, moet u een abonnement met minder cores kiezen. Zie de [JavaScript-naslaginformatie voor Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans) voor meer informatie.

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemene doeleinden maken of koppelen dat blob-, wachtrij- en tabelopslag ondersteunt. Intern maakt Functions gebruik van Storage voor bewerkingen zoals het beheren van triggers en het vastleggen van functie-uitvoeringen in logboeken. Sommige opslagaccounts bieden geen ondersteuning voor wachtrijen en tabellen, zoals accounts alleen voor blobs, Azure Premium Storage en opslagaccounts voor algemeen gebruik met ZRS-replicatie (zone-redundante opslag). Deze accounts worden niet op de blade Opslagaccount vermeld als er een functie-app wordt gemaakt.

>[!NOTE]
>Als u gebruikmaakt van het hostingabonnement Consumption worden uw functiecode en uw bindingsconfiguratiebestanden opgeslagen in het belangrijkste opslagaccount in Azure File Storage. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#azure-storage-services) voor meer informatie over opslagaccounttypen. 

## <a name="next-steps"></a>Volgende stappen

Hoewel de Azure-portal het eenvoudig maakt om functies te maken en uit te proberen, raden we [lokale ontwikkeling](functions-develop-local.md)aan. Nadat u een functie-app in de portal hebt hebt gemaakt, moet u nog steeds een functie toevoegen. 

> [!div class="nextstepaction"]
> [Een HTTP-geactiveerde functie toevoegen](functions-create-first-azure-function.md#create-function)
