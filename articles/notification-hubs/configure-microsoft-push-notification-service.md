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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80127330"
---
# <a name="configure-microsoft-push-notification-service-mpns-settings-in-the-azure-portal"></a>Instellingen voor micro soft Push Notification Service (MPNS) configureren in de Azure Portal

In dit artikel wordt beschreven hoe u instellingen voor micro soft Push Notification Service (MPNS) voor een Azure notification hub configureert met behulp van de Azure Portal. 

## <a name="prerequisites"></a>Vereisten
Maak nu een notification hub als u dat nog niet hebt gedaan. Zie [een Azure notification hub maken in de Azure Portal](create-notification-hub-portal.md)voor meer informatie. 

## <a name="configure-microsoft-push-notification-service-mpns"></a>Micro soft Push Notification Service (MPNS) configureren

De volgende procedure bevat stappen voor het configureren van instellingen voor micro soft Push Notification Service (MPNS) voor een notification hub: 

1. Selecteer in de Azure Portal op de pagina **Notification Hub** **Windows Phone (MPNS)** in het menu links.
1. Niet-geverifieerde of geverifieerde pushmeldingen inschakelen:

   a. Selecteer **Niet-geverifieerde push inschakelen** > **Opslaan** om niet-geverifieerde pushmeldingen in te schakelen.

      ![Schermafbeelding die laat zien hoe u niet-geverifieerde pushmeldingen inschakelt](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. Geverifieerde pushmeldingen inschakelen:
      * Selecteer **Certificaat uploaden** op de werkbalk.
      * Selecteer het pictogram van het bestand en selecteer vervolgens het certificaatbestand.
      * Voer het wachtwoord voor het certificaat in.
      * Selecteer **OK**.
      * Selecteer **Opslaan** op de pagina **Windows Phone (MPNS)** .

## <a name="next-steps"></a>Volgende stappen
Voor een zelf studie met stapsgewijze instructies voor het pushen van meldingen naar Windows Phone apparaten met behulp van Azure Notification Hubs en micro soft Push Notification Service (MPNS), raadpleegt [u push meldingen voor het Windows Phone van apps met behulp van Notification hubs](notification-hubs-windows-mobile-push-notifications-mpns.md).

