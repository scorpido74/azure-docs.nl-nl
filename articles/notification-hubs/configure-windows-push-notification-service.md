---
title: Windows Push Notification Service configureren in Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het configureren van Windows Push Notification Service-instellingen voor een Azure-meldingshub.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127310"
---
# <a name="configure-windows-push-notification-service-settings-in-the-azure-portal"></a>Instellingen voor Windows Push Notification Service configureren in de Azure-portal

In dit artikel ziet u hoe u WNS-instellingen (Windows Notification Service) configureert voor een Azure-meldingshub met behulp van de Azure-portal.  

## <a name="prerequisites"></a>Vereisten
Als u nog geen meldingshub hebt gemaakt, maakt u er nu een. Zie [Een Azure-meldingshub maken in de Azure-portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-windows-push-notification-service-wns"></a>Windows Push Notification Service (WNS) configureren

De volgende procedure geeft u stappen om WNS-instellingen (Windows Push Notification Service) te configureren voor een meldingshub: 

1. Selecteer **Windows (WNS)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu.
2. Voer waarden in voor **Pakket-SID** en **Beveiligingssleutel**.
3. Selecteer **Opslaan**.

   ![Schermafbeelding van de vakken Package SID en Security Key](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Meldingen verzenden naar UWP-apps met Azure Notification Hubs](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)voor een zelfstudie met stapsgewijze instructies voor het pushen van meldingen naar Universele Windows Platform-toepassingen met behulp van Azure Notification Hubs en Windows Push Notification Service (WNS).


