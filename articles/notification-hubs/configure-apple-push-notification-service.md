---
title: Apple Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van een Azure notification hub met Apple Push Notification Service (APNS)-instellingen.
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
ms.openlocfilehash: 116af5192236045ec50409f65a9687ffec5223b5
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406102"
---
# <a name="configure-apple-push-notification-service-apns-settings-for-a-notification-hub-in-the-azure-portal"></a>Configureer Apple Push Notification Service (APNS)-instellingen voor een notification hub in de Azure Portal
In dit artikel wordt beschreven hoe u Apple Push Notification Service (APNS)-instellingen configureert voor een Azure notification hub met behulp van de Azure Portal. 

## <a name="prerequisites"></a>Vereisten
Als u nog geen notification hub hebt gemaakt, maakt u er nu een. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-apple-push-notification-service"></a>Apple Push Notification Service configureren

De volgende procedure bevat stappen voor het configureren van Apple Push Notification Service (APNS)-instellingen voor een notification hub:

1. Selecteer in het Azure Portal op de pagina **Notification hub** **Apple (APNS)** in het menu links.

1. Voor de **verificatie modus**selecteert u **certificaat** of **token**.

   a. Als u **certificaat**selecteert:
   * Selecteer het pictogram bestand en selecteer vervolgens het *. p12* -bestand dat u wilt uploaden.
   * Voer een wacht woord in.
   * Selecteer de modus **Sandbox**. Als u push meldingen wilt verzenden naar gebruikers die uw app uit de Store hebben gekocht, selecteert u **productie** modus.

     ![Scherm opname van een configuratie van een APNS-certificaat in de Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-cert.png)

   b. Als u **token**selecteert:

   * Voer de waarden in voor de **sleutel-id**, de **bundel-id**, de **Team-ID**en het **token**.
   * Selecteer de modus **Sandbox**. Als u push meldingen wilt verzenden naar gebruikers die uw app uit de Store hebben gekocht, selecteert u **productie** modus.

     ![Scherm afbeelding van een configuratie van een APNS-token in de Azure Portal](./media/configure-apple-push-notification-service/notification-hubs-apple-config-token.png)

## <a name="next-steps"></a>Volgende stappen
Voor een zelf studie met stapsgewijze instructies voor het pushen van meldingen naar iOS-apparaten raadpleegt u het volgende artikel: [Push meldingen naar IOS-apparaten met behulp van Notification hubs en APNS](notification-hubs-ios-apple-push-notification-apns-get-started.md)
