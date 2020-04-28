---
title: Windows Push Notification Service configureren in azure Notification Hubs | Microsoft Docs
description: Meer informatie over het configureren van Windows Push Notification Service-instellingen voor een Azure notification hub.
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
ms.openlocfilehash: 73304e191242725c80204efb132c26aede9ce7e9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80127310"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Instellingen voor Windows Push Notification Service configureren in de Azure Portal

Dit artikel laat u zien hoe u WNS-instellingen (Windows Notification Service) kunt configureren voor een Azure notification hub met behulp van de Azure Portal.  

## <a name="prerequisites"></a>Vereisten
Als u nog geen notification hub hebt gemaakt, maakt u er nu een. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-windows-push-notification-service-wns"></a>Windows Push Notification Service (WNS) configureren

De volgende procedure bevat stappen voor het configureren van WNS-instellingen (Windows Push Notification Service) voor een notification hub: 

1. Selecteer in de Azure Portal op de pagina **Notification hub** de optie **Windows (WNS)** in het menu links.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Scherm opname van de vakken SID en beveiligings sleutel van het pakket](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Volgende stappen
Zie [meldingen verzenden naar UWP-apps met behulp van azure notification hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor een zelf studie met stapsgewijze instructies voor het pushen van meldingen naar universeel Windows-platform-toepassingen met behulp van Azure notification hubs en Windows Push Notification Service (WNS).


