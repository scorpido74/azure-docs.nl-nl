---
title: Azure Event Grid-abonnementen via portal
description: In dit artikel wordt beschreven hoe u Event Grid-abonnementen maakt voor de ondersteunde bronnen, zoals Azure Blob Storage, met behulp van de Azure-portal.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 599f48ed241010d8551bd110c7f778c9ef508eac
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393179"
---
# <a name="subscribe-to-events-through-portal"></a>Abonneer u op evenementen via portal

In dit artikel wordt beschreven hoe u eventgrid-abonnementen maakt via de portal.

## <a name="create-event-subscriptions"></a>Gebeurtenisabonnementen maken

Als u een abonnement op eventgrid wilt maken voor een van de ondersteunde [gebeurtenisbronnen,](overview.md#event-sources)gebruikt u de volgende stappen. In dit artikel ziet u hoe u een Event Grid-abonnement maakt voor een Azure-abonnement.

1. Selecteer **Alle services**.

   ![Alle services selecteren](./media/subscribe-through-portal/select-all-services.png)

1. Zoek naar **abonnementen op het gebeurtenisraster** en selecteer deze uit de beschikbare opties.

   ![Search](./media/subscribe-through-portal/search.png)

1. Selecteer **+ Gebeurtenisabonnement**.

   ![Abonnement toevoegen](./media/subscribe-through-portal/add-subscription.png)

1. Selecteer het type abonnement dat u wilt maken. Als u zich bijvoorbeeld wilt abonneren op gebeurtenissen voor uw Azure-abonnement, selecteert u **Azure-abonnementen** en het doelabonnement.

   ![Azure-abonnement selecteren](./media/subscribe-through-portal/azure-subscription.png)

1. Als u zich wilt abonneren op alle gebeurtenistypen voor deze gebeurtenisbron, houdt u de optie **Abonneren op alle gebeurtenistypen** ingeschakeld. Selecteer anders de gebeurtenistypen voor dit abonnement.

   ![Gebeurtenistypen selecteren](./media/subscribe-through-portal/select-event-types.png)

1. Geef meer informatie over het gebeurtenisabonnement, zoals het eindpunt voor het afhandelen van gebeurtenissen en een abonnementsnaam.

   ![Abonnementsgegevens opgeven](./media/subscribe-through-portal/provide-subscription-details.png)

1. Als u beleid voor dode letters wilt inschakelen en beleid voor nieuwe apparaten wilt aanpassen, selecteert u **Extra functies**.

   ![Extra functies selecteren](./media/subscribe-through-portal/select-additional-features.png)

1. Selecteer een container die u wilt gebruiken voor het opslaan van gebeurtenissen die niet worden geleverd en stel in hoe nieuwe pogingen worden verzonden.

   ![Dode letters inschakelen en opnieuw proberen](./media/subscribe-through-portal/set-deadletter-retry.png)

1. Wanneer u klaar bent, selecteert u **Maken**.

## <a name="create-subscription-on-resource"></a>Abonnement maken op resource

Sommige gebeurtenisbronnen ondersteunen het maken van een gebeurtenisabonnement via de portalinterface voor die bron. Selecteer de gebeurtenisbron en zoek naar **gebeurtenissen** in het linkerdeelvenster.

![Abonnementsgegevens opgeven](./media/subscribe-through-portal/resource-events.png)

De portal biedt u opties voor het maken van een evenementabonnement dat relevant is voor die bron.

## <a name="next-steps"></a>Volgende stappen

* Voor informatie over het bezorgen en opnieuw proberen van [gebeurtenissen, het verzenden van het gebeurtenisrasterbericht en het opnieuw proberen](delivery-and-retry.md).
* Zie [Een inleiding tot Event Grid](overview.md) voor een inleiding tot Event Grid.
* Zie [Aangepaste gebeurtenissen maken en routeren met Azure Event Grid](custom-event-quickstart.md)om snel aan de slag te gaan met gebeurtenisraster.
