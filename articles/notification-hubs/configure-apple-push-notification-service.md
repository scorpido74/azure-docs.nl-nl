---
title: Apple Push Notification Service configureren in Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het configureren van een Azure-meldingshub met APNS-instellingen (Apple Push Notification Service).
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
ms.openlocfilehash: eb1122ba3de0002507589d3e607d1e39d905c308
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127522"
---
# <a name="configure-apple-push-notification-service-settings-for-a-notification-hub-in-the-azure-portal"></a>Instellingen voor Apple Push Notification Service configureren voor een meldingshub in de Azure-portal

In dit artikel ziet u hoe u de instellingen van de Apple Push Notification Service (APNS) configureert voor een Azure-meldingshub met behulp van de Azure-portal. 

## <a name="prerequisites"></a>Vereisten
Als u nog geen meldingshub hebt gemaakt, maakt u er nu een. Zie [Een Azure-meldingshub maken in de Azure-portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service configureren

De volgende procedure geeft u stappen om apns-instellingen (Apple Push Notification Service) te configureren voor een meldingshub:

1. Selecteer **Apple (APNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.

1. Selecteer **Certificaat** of **Token**voor **verificatiemodus.**

   a. Als u **Certificaat**selecteert:
   * Selecteer het bestandspictogram en selecteer vervolgens het *.p12-bestand* dat u wilt uploaden.
   * Voer een wachtwoord in.
   * Selecteer de modus **Sandbox**. Of als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht, selecteert u **de productiemodus.**

     ![Schermafbeelding van een APNS-certificaatconfiguratie in de Azure-portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Als u **Token**selecteert:

   * Voer de waarden in voor **Sleutel-id,** **Bundel-id,** **Team-id**en **Token**.
   * Selecteer de modus **Sandbox**. Of als u pushmeldingen wilt verzenden naar gebruikers die uw app in de Store hebben gekocht, selecteert u **de productiemodus.**

     ![Schermafbeelding van een APNS-tokenconfiguratie in de Azure-portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor een zelfstudie met stapsgewijze instructies voor het pushen van meldingen naar iOS-apparaten: [Pushmeldingen naar iOS-apparaten met behulp van Meldinghubs en APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
