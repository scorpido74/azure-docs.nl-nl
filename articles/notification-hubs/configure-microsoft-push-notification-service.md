---
title: Micro soft Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van instellingen voor micro soft Push Notification Service voor een Azure notification hub.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127330"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Instellingen voor micro soft Push Notification Service (MPNS) configureren in de Azure Portal

In dit artikel wordt beschreven hoe u instellingen voor micro soft Push Notification Service (MPNS) voor een Azure notification hub configureert met behulp van de Azure Portal. 

## <a name="prerequisites"></a>Vereisten
Als u nog geen notification hub hebt gemaakt, maakt u er nu een. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Micro soft Push Notification Service (MPNS) configureren

De volgende procedure bevat stappen voor het configureren van instellingen voor micro soft Push Notification Service (MPNS) voor een notification hub: 

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Windows Phone (MPNS)** in het menu links.
1. Niet-geverifieerde of geverifieerde push meldingen inschakelen:

   a. Als u niet-geverifieerde push meldingen wilt inschakelen, selecteert u niet- **geverifieerde push** > **Opslaan**inschakelen.

      ![Scherm afbeelding die laat zien hoe u niet-geverifieerde push meldingen inschakelt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Geverifieerde push meldingen inschakelen:
      * Selecteer **certificaat uploaden**op de werk balk.
      * Selecteer het pictogram bestand en selecteer vervolgens het certificaat bestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer op de pagina **Windows Phone (MPNS)** de optie **Opslaan**.

## <a name="next-steps"></a>Volgende stappen
Voor een zelf studie met stapsgewijze instructies voor het pushen van meldingen naar Windows Phone apparaten met behulp van Azure Notification Hubs en micro soft Push Notification Service (MPNS), raadpleegt [u push meldingen voor het Windows Phone van apps met behulp van Notification hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

