---
title: bestand opnemen
description: bestand opnemen
services: functions
author: jeffhollan
ms.service: azure-functions
ms.topic: include
ms.date: 10/25/2019
ms.author: jehollan, glenga
ms.custom: include file
ms.openlocfilehash: fca8c3b0b1bd4d22720a6d15313e297d05b7fac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76020826"
---
1. De Azure-portal openen vanuit[https://portal.azure.com](https://portal.azure.com)

1. De knop **Een resource maken** selecteren

    ![Bron maken](./media/functions-create-function-app-portal/function-app-create-resource.png)

1. Selecteer De > **rekenfunctie-app**. **Compute**

    ![Een functie-app maken in Azure Portal](./media/functions-premium-create/function-app-create-start.png)

1. Gebruik voor de functie-app de instellingen die in de tabel onder de afbeelding zijn opgegeven.

    ![Basisbeginselen](./media/functions-premium-create/function-app-create-basics.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **Abonnement** | Uw abonnement | Het abonnement waarmee deze nieuwe functie-app is gemaakt. |
    | **[Resourcegroep](../articles/azure-resource-manager/management/overview.md)** |  *myResourceGroup* | Naam voor de nieuwe resourcegroep waarin uw functie-app moet worden gemaakt. |
    | **Functie-app-naam** | Wereldwijd unieke naam | Naam waarmee uw nieuwe functie-app wordt aangeduid. Geldige tekens `a-z` zijn (geval `0-9`ongevoelig), en `-`.  |
    |**Publiceren**| Code | Optie voor het publiceren van codebestanden of een Docker-container. |
    | **Runtimestack** | Voorkeurstaal | Kies een runtime die uw favoriete functieprogrammeertaal ondersteunt. Kies **.NET** voor C#- en F#-functies. |
    |**Regio**| Voorkeursregio | Kies een [regio](https://azure.microsoft.com/regions/) in de buurt of in de buurt van andere services die door uw functie worden gebruikt. |

    Selecteer de knop **Volgende: Hosting >.**

1. Voer de volgende hostinginstellingen in.

    ![Hosting](./media/functions-premium-create/function-app-premium-create-hosting.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Opslagaccount](../articles/storage/common/storage-account-create.md)** |  Wereldwijd unieke naam |  Maak een opslagaccount die wordt gebruikt door uw functie-app. Namen van opslagaccounts moeten tussen 3 en 24 tekens lang zijn en mogen alleen cijfers en kleine letters bevatten. U ook een bestaand account gebruiken, dat moet voldoen aan de [vereisten voor opslagaccount.](../articles/azure-functions/functions-scale.md#storage-account-requirements) |
    |**Besturingssysteem**| Voorkeursbesturingssysteem | Een besturingssysteem is vooraf voor u geselecteerd op basis van uw runtime stackselectie, maar u de instelling indien nodig wijzigen. |
    | **[Plan](../articles/azure-functions/functions-scale.md)** | Premium | Selecteer voor Type Abonnement **Premium (Voorbeeld)** en selecteer standaardinstellingen voor de selecties *voor Windows-abonnement* en *Sku en grootte.* |

    Selecteer de knop **Volgende : >bewaken.**

1. Voer de volgende bewakingsinstellingen in.

    ![Bewaking](./media/functions-create-function-app-portal/function-app-create-monitoring.png)

    | Instelling      | Voorgestelde waarde  | Beschrijving |
    | ------------ | ---------------- | ----------- |
    | **[Application Insights](../articles/azure-functions/functions-monitoring.md)** | Standaard | Hiermee maakt u een application insights-bron met dezelfde *app-naam* in het dichtstbijzijnde ondersteunde gebied. Door deze instelling uit te breiden, u de **naam van** de nieuwe bron wijzigen of een andere **locatie** kiezen in een [Azure-geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u uw gegevens wilt opslaan. |

    Selecteer **Controleren + Maken** om de selecties voor app-configuraties te controleren.

1. Selecteer **Maken** om de functie-app in te richten en te implementeren.