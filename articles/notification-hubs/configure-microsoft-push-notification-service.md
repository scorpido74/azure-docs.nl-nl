---
title: Microsoft Push Notification Service configureren in Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het configureren van Microsoft Push Notification Service-instellingen voor een Azure-meldingshub.
services: notification-hubs
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.topic: article
ms.date: 03/25/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 99f29e7910fe6070c6202f6a936173455f979732
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127330"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>MpNS-instellingen (Microsoft Push Notification Service) configureren in de Azure-portal

In dit artikel ziet u hoe u MPNS-instellingen (Microsoft Push Notification Service) configureert voor een Azure-meldingshub met behulp van de Azure-portal. 

## <a name="prerequisites"></a>Vereisten
Als u nog geen meldingshub hebt gemaakt, maakt u er nu een. Zie [Een Azure-meldingshub maken in de Azure-portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Microsoft Push Notification Service (MPNS) configureren

De volgende procedure geeft u stappen om MPNS-instellingen (Microsoft Push Notification Service) te configureren voor een meldingshub: 

1. Selecteer **Windows Phone (MPNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.
1. Niet-geverifieerde of geverifieerde pushmeldingen inschakelen:

   a. Als u niet-geverifieerde pushmeldingen wilt inschakelen, selecteert u **Niet-geverifieerde push** > **opslaan inschakelen**.

      ![Schermafbeelding van het inschakelen van niet-geverifieerde pushmeldingen](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Ga als u geverifieerde pushmeldingen in:
      * Selecteer op de werkbalk **Certificaat uploaden**.
      * Selecteer het bestandspictogram en selecteer vervolgens het certificaatbestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer op de pagina **Windows Phone (MPNS)** de optie **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
Zie Pushmeldingen naar Windows Phone-apps pushmeldingen voor het verzenden van meldingen naar Windows Phone-apparaten met behulp van Azure Notification Hubs en Microsoft Push Notification Service (MPNS) voor een zelfstudie met stapsgewijze instructies voor het pushen van meldingen naar Windows Phone-apparaten met behulp van Azure Notification Hubs en Microsoft Push Notification Service (MPNS), zie [Pushmeldingen naar Windows Phone-apps met behulp van Meldingshubs.](notification-hubs-windows-mobile-push-notifications-mpns.md)

