---
title: Een functie-app maken vanuit Azure Portal
description: Maak vanuit de portal een nieuwe functie-app in Azure.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230775"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Een functie-app maken vanuit Azure Portal

In dit onderwerp wordt beschreven hoe u Azure Functions kunt gebruiken om een functie-app te maken in de Azure Portal. Een functie-app is de container die als host fungeert voor het uitvoeren van afzonderlijke functies. 

## <a name="create-a-function-app"></a>Een functie-app maken

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Wanneer u een functie-app maakt, moet u een geldige **app-naam** opgeven, die alleen letters, cijfers en afbreekstreepjes mag bevatten. Het onderstrepingsteken ( **_** ) is niet toegestaan.

Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. De naam van uw opslagaccount moet uniek zijn binnen Azure. 

Nadat de functie-app is gemaakt, kunt u afzonderlijke functies in een of meer verschillende talen maken. U kunt functies maken [met behulp van de portal](functions-create-first-azure-function.md#create-function), via [continue implementatie](functions-continuous-deployment.md) of door te [uploaden via FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Service-abonnementen

Azure Functions heeft drie verschillende service plannen: verbruiks plan, Premium plan en toegewijd (App Service) plan. U moet uw service plan kiezen wanneer uw functie-app wordt gemaakt en deze vervolgens niet meer kan worden gewijzigd. Zie [Een Azure Functions-hostingabonnement kiezen](functions-scale.md) voor meer informatie.

Als u van plan bent java script-functies uit te voeren op een speciaal (App Service)-abonnement, kiest u een abonnement met minder kernen. Zie de [JavaScript-naslaginformatie voor Functions](functions-reference-node.md#choose-single-vcpu-app-service-plans) voor meer informatie.

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Vereisten voor een opslagaccount

Wanneer u een functie-app maakt, moet u een Azure Storage-account voor algemeen gebruik maken of koppelen dat ondersteuning biedt voor blob-, wachtrij-en tabel opslag. Intern maakt Functions gebruik van Storage voor bewerkingen zoals het beheren van triggers en het vastleggen van functie-uitvoeringen in logboeken. Sommige opslagaccounts bieden geen ondersteuning voor wachtrijen en tabellen, zoals accounts alleen voor blobs, Azure Premium Storage en opslagaccounts voor algemeen gebruik met ZRS-replicatie (zone-redundante opslag). Deze accounts worden niet op de blade Opslagaccount vermeld als er een functie-app wordt gemaakt.

>[!NOTE]
>Als u gebruikmaakt van het hostingabonnement Consumption worden uw functiecode en uw bindingsconfiguratiebestanden opgeslagen in het belangrijkste opslagaccount in Azure File Storage. Wanneer u het belangrijkste opslagaccount verwijdert, wordt de inhoud verwijderd en kan deze niet worden hersteld.

Zie [Introductie van de Azure Storage-services](../storage/common/storage-introduction.md#azure-storage-services) voor meer informatie over opslagaccounttypen. 

## <a name="next-steps"></a>Volgende stappen

De Azure Portal maakt het eenvoudig om functies te maken en uit te proberen, maar we raden u aan de [lokale ontwikkeling te ontwikkelen](functions-develop-local.md). Nadat u een functie-app in de portal hebt gemaakt, moet u nog steeds een functie toevoegen. 

> [!div class="nextstepaction"]
> [Een door HTTP geactiveerde functie toevoegen](functions-create-first-azure-function.md#create-function)
