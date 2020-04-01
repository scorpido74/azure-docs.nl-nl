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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67509081"
---
1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer **Alle services** in het linkermenu en selecteer vervolgens **Meldingshubs** in de sectie **Mobiel.** Selecteer het sterpictogram naast de servicenaam om de service toe te voegen aan de sectie **FAVORIETEN** in het linkermenu. Nadat u **Notificatiehubs** hebt toegevoegd aan **FAVORIETEN,** selecteert u deze in het linkermenu.

      ![Azure Portal - Notification Hubs selecteren](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. Op de **Notification Hubs**-pagina selecteert u **Toevoegen** op de werkbalk.

      ![Toevoegen van Notification Hubs - knop op de werkbalk](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Voer op de pagina **Notification Hub** de volgende stappen uit:

    1. Voer een naam in **de meldingshub in**.  

    1. Voer een naam in **Een nieuwe naamruimte maken**. Een naamruimte bevat een of meer hubs.

    1. Selecteer een waarde in de vervolgkeuzelijst **Locatie.** Deze waarde geeft de locatie aan waarin u de hub wilt maken.

    1. Selecteer een bestaande resourcegroep in **Resourcegroep**of maak een naam voor een nieuwe resourcegroep.

    1. Selecteer **Maken**.

        ![Azure Portal - eigenschappen van de Notification Hub instellen](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Selecteer **Meldingen** (het belpictogram) en selecteer **Ga naar resource**. U de lijst ook vernieuwen op de pagina **Meldingenhubs** en uw hub selecteren.

      ![Azure Portal - Meldingen -> Naar de resource gaan](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. Selecteer **Toegangsbeleid** in de lijst. Houd er rekening mee dat de twee verbindingstekenreeksen voor u beschikbaar zijn. Je hebt ze later nodig om pushmeldingen te verwerken.

      >[!IMPORTANT]
      >Gebruik het **defaultFullSharedAccessSignature-beleid** *niet* in uw toepassing. Dit is bedoeld om alleen in je back-end te worden gebruikt.
      >

      ![Azure Portal - verbindingsreeksen voor de Notification Hub](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
