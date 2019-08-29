---
title: Een functie-app maken vanuit Azure Portal | Microsoft Azure Docs
description: Maak een nieuwe functie-app in Azure App Service vanuit de portal.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.topic: quickstart
ms.date: 04/11/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: eb362ec652b306a12a41e7e96dcbc86638369c17
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085897"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Een functie-app maken vanuit Azure Portal

Azure functie-apps maakt gebruik van de Azure App Service-infrastructuur. Dit onderwerp leert u hoe u een functie-app vanuit Azure Portal kunt maken. Een functie-app is de container die als host fungeert voor het uitvoeren van afzonderlijke functies. Wanneer u in het App Service-hostingabonnement een functie-app maakt, kan uw app van alle functies van App Service gebruikmaken.

## <a name="create-a-function-app"></a>Een functie-app maken

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Wanneer u een functie-app maakt, moet u een geldige **app-naam** opgeven, die alleen letters, cijfers en afbreekstreepjes mag bevatten. Het onderstrepingsteken ( **_** ) is niet toegestaan.

Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. De naam van uw opslagaccount moet uniek zijn binnen Azure. 

Nadat de functie-app is gemaakt, kunt u afzonderlijke functies in een of meer verschillende talen maken. U kunt functies maken [met behulp van de portal](functions-create-first-azure-function.md#create-function), via [continue implementatie](functions-continuous-deployment.md) of door te [uploaden via FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Service-abonnementen

Azure Functions biedt twee verschillende serviceabonnementen: Het verbruiksabonnement en het App Service-abonnement. In het abonnement Consumption wordt automatisch rekencapaciteit toegewezen op het moment dat uw code wordt uitgevoerd, wordt de schaal naar boven aangepast als dat nodig is om de werkbelasting aan te kunnen, en naar beneden als de code niet wordt uitgevoerd. Met het App Service-abonnement heeft uw functie-app toegang tot de faciliteiten van App Service. Als uw functie-app wordt gemaakt, moet u een serviceabonnement kiezen. Dit kan momenteel niet worden gewijzigd. Zie [Een Azure Functions-hostingabonnement kiezen](functions-scale.md) voor meer informatie.

Als u van plan bent JavaScript-functies op een App Service-abonnement uit te voeren, moet u een abonnement met minder cores kiezen. Zie de [JavaScript-naslaginformatie voor Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans) voor meer informatie.

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app in App Service maakt, moet u een Azure Storage-account voor algemeen gebruik of een koppeling ernaar maken. Dit account moet ondersteuning bieden voor Blob Storage, Queue Storage en Table Storage. Intern maakt Functions gebruik van Storage voor bewerkingen zoals het beheren van triggers en het vastleggen van functie-uitvoeringen in logboeken. Sommige opslagaccounts bieden geen ondersteuning voor wachtrijen en tabellen, zoals accounts alleen voor blobs, Azure Premium Storage en opslagaccounts voor algemeen gebruik met ZRS-replicatie (zone-redundante opslag). Deze accounts worden niet op de blade Opslagaccount vermeld als er een functie-app wordt gemaakt.

>[!NOTE]
>Als u gebruikmaakt van het hostingabonnement Consumption worden uw functiecode en uw bindingsconfiguratiebestanden opgeslagen in het belangrijkste opslagaccount in Azure File Storage. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#azure-storage-services) voor meer informatie over opslagaccounttypen. 

## <a name="next-steps"></a>Volgende stappen

De Azure Portal maakt het eenvoudig om functies te maken en uit te proberen, maar we raden u aan de [lokale ontwikkeling te ontwikkelen](functions-develop-local.md). Nadat u een functie-app in de portal hebt gemaakt, moet u nog steeds een functie toevoegen. 

> [!div class="nextstepaction"]
> [Een door HTTP geactiveerde functie toevoegen](functions-create-first-azure-function.md#create-function)
