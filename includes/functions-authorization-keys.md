---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/06/2020
ms.author: glenga
ms.openlocfilehash: 926434d7110877e234888682cb6c946afe3ae685
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83648930"
---
Met functies kunt u sleutels gebruiken om het moeilijker te maken om toegang te krijgen tot de eind punten van uw HTTP-functies tijdens het ontwikkelen. Tenzij het HTTP-toegangs niveau voor een HTTP-geactiveerde functie is ingesteld op `anonymous` , moeten aanvragen een API-toegangs sleutel bevatten in de aanvraag. 

#### <a name="authorization-scopes-function-level"></a>Autorisatie bereiken (functie niveau)

Er zijn twee toegangs scopes voor sleutels op functie niveau:

* **Functie**: deze sleutels zijn alleen van toepassing op de specifieke functies waaronder ze zijn gedefinieerd. Wanneer u als een API-sleutel wordt gebruikt, is deze alleen toegankelijk voor deze functie.

* **Host**: sleutels met een bereik van een host kunnen worden gebruikt voor toegang tot alle functies in de functie-app. Wanneer deze als een API-sleutel wordt gebruikt, is toegang tot alle functies in de functie-app toegestaan. 

Elke sleutel krijgt de naam van de verwijzing en er is een standaard sleutel (met de naam ' default ') op het niveau van de functie en de host. Functie sleutels hebben voor rang op de sleutels van de host. Wanneer twee sleutels met dezelfde naam zijn gedefinieerd, wordt de functie toets altijd gebruikt.

#### <a name="master-key-admin-level"></a>Hoofd sleutel (beheer niveau) 

Elke functie-app heeft ook een host-sleutel op beheer niveau met de naam `_master` . Naast het verschaffen van toegang op hostniveau voor alle functies in de app, biedt de hoofd sleutel ook beheerders toegang tot de runtime REST Api's. Deze sleutel kan niet worden ingetrokken. Wanneer u een toegangs niveau instelt `admin` , moeten aanvragen gebruikmaken van de hoofd sleutel. een andere sleutel resulteert in een toegangs fout.

> [!CAUTION]  
> Als gevolg van de verhoogde machtigingen in uw functie-app die is verleend door de hoofd sleutel, moet u deze sleutel niet delen met derden of deze distribueren in native client toepassingen. Wees voorzichtig bij het kiezen van het toegangs niveau voor de beheerder.
