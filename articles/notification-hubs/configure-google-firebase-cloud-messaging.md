---
title: Google Firebase Cloud Messaging configureren in Azure Notification Hubs | Microsoft Documenten
description: Meer informatie over het configureren van een Azure-meldingshub met Google Firebase Cloud Messaging-instellingen.
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
ms.openlocfilehash: 1adbce654bc5c057270df9a874911731a0135034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127469"
---
# <a name="configure-google-firebase-settings-for-a-notification-hub-in-the-azure-portal"></a>Google Firebase-instellingen configureren voor een meldingshub in de Azure-portal

In dit artikel ziet u hoe u de FCM-instellingen (Google Firebase Cloud Messaging) configureert voor een Azure-meldingshub met behulp van de Azure-portal.  

## <a name="prerequisites"></a>Vereisten
Als u nog geen meldingshub hebt gemaakt, maakt u er nu een. Zie [Een Azure-meldingshub maken in de Azure-portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging configureren (FCM)

De volgende procedure geeft u stappen om de INSTELLINGEN van Google Firebase Cloud Messaging (FCM) te configureren voor een meldingshub: 

1. Selecteer **google (GCM/FCM)** in de Azure-portal op de pagina **Meldingshub** in het linkermenu. 
2. Plak de **API-sleutel** voor het FCM-project dat u eerder hebt opgeslagen. 
3. Selecteer **Opslaan**. 

   ![Schermafbeelding van het configureren van meldingshubs voor Google FCM](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

## <a name="next-steps"></a>Volgende stappen
Zie [Pushmeldingen naar Android-apparaten met behulp van Notification Hubs en Google FCM](notification-hubs-android-push-notification-google-fcm-get-started.md)voor een zelfstudie met stapsgewijze instructies voor het pushen naar Android-apparaten.

