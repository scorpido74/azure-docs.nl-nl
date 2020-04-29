---
title: bestand opnemen
description: bestand opnemen
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 5afcc8e4524a0e8353766ba239d5ab9161b29d86
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67509081"
---
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer **alle services** in het linkermenu en selecteer vervolgens **Notification hubs** in het **mobiele** gedeelte. Selecteer het ster pictogram naast de service naam om de service toe te voegen aan de sectie **Favorieten** in het menu links. Nadat u **Notification hubs** aan **Favorieten**hebt toegevoegd, selecteert u deze in het menu links.

      ![Azure Portal - Notification Hubs selecteren](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Op de **Notification Hubs**-pagina selecteert u **Toevoegen** op de werkbalk.

      ![Toevoegen van Notification Hubs - knop op de werkbalk](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Voer op de pagina **Notification Hub** de volgende stappen uit:

    1. Voer een naam in de **Notification hub**in.  

    1. Geef een naam op in **een nieuwe naam ruimte maken**. Een naamruimte bevat een of meer hubs.

    1. Selecteer een waarde in de vervolg keuzelijst **locatie** . Met deze waarde geeft u de locatie op waarin u de hub wilt maken.

    1. Selecteer een bestaande resource groep in de **resource groep**of maak een naam voor een nieuwe resource groep.

    1. Selecteer **Maken**.

        ![Azure Portal - eigenschappen van de Notification Hub instellen](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecteer **meldingen** (het klok pictogram) en selecteer vervolgens **Ga naar resource**. U kunt de lijst ook op de **Notification hubs** pagina vernieuwen en uw hub selecteren.

      ![Azure Portal - Meldingen -> Naar de resource gaan](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecteer **Toegangsbeleid** in de lijst. Houd er rekening mee dat de twee verbindings reeksen voor u beschikbaar zijn. U hebt deze later nodig voor het afhandelen van push meldingen.

      >[!IMPORTANT]
      >Gebruik het **DefaultFullSharedAccessSignature** -beleid *niet* in uw toepassing. Dit is uitsluitend bedoeld om te worden gebruikt in uw back-end.
      >

      ![Azure Portal - verbindingsreeksen voor de Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
