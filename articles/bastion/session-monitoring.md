---
title: Azure Bastion-sessie bewaking en-beheer | Microsoft Docs
description: In dit artikel vindt u informatie over het selecteren van een actieve sessie en het afdwingen van verbinding maken of verwijderen.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a4a97ebd0e44bfd3b0ee167a2f3a7da435ac5087
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513001"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Sessie bewaking en-beheer voor Azure Bastion

Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om naadloos verbinding te maken met elke virtuele machine in dit virtuele netwerk. Wanneer gebruikers verbinding maken met werk belastingen, kan Azure Bastion worden gebruikt om de externe sessies te controleren en snelle beheer acties uit te voeren. Met de Azure Bastion-sessie bewaking kunt u zien welke gebruikers zijn verbonden met welke Vm's. Hier wordt het IP-adres weer gegeven waarmee de gebruiker verbinding maakt, hoe lang ze zijn verbonden en wanneer ze zijn verbonden. Met de sessie beheer ervaring kunt u een actieve sessie selecteren en geforceerd afbreken of een sessie verwijderen om de verbinding van de gebruiker met de actieve sessie te verbreken.

## <a name="monitor"></a>Externe sessies bewaken

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure Bastion-resource en selecteer **sessies** op de pagina Azure Bastion.

   ![sessies](./media/session-monitoring/sessions.png)
2. Op de pagina **sessies** kunt u de actieve externe sessies aan de rechter kant bekijken.

   ![sessie weer geven](./media/session-monitoring/view-session.png)
3. Selecteer **vernieuwen** om de bijgewerkte lijst met externe sessies weer te geven. Wanneer u vernieuwen selecteert, haalt Azure Bastion de meest recente bewakings gegevens op en vernieuwt deze in de portal.

   ![klikken](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Schakel poort 4443 voor binnenkomend verkeer van Gateway beheer in om sessie bewaking te laten werken.
>

## <a name="view"></a>Een actieve externe sessie verwijderen of forceren

U kunt een set sessie (s) selecteren en de verbinding forceren. De volgende stappen laten zien hoe u externe sessies verwijdert:

1. Navigeer naar uw Azure Bastion-resource en selecteer **sessies** op de Azure Bastion-pagina.

   ![Scha](./media/session-monitoring/navigate.png)
2. Nadat u sessies hebt geselecteerd, ziet u een lijst met externe sessies.

   ![sessies weer geven](./media/session-monitoring/list.png)
3. Selecteer een specifieke externe sessie en selecteer vervolgens de drie weglatings tekens aan de rechter kant van de rij met de sessie en selecteer vervolgens **verwijderen**.

   ![delete](./media/session-monitoring/delete.png)
4. Wanneer u verwijderen selecteert, wordt de externe sessie verbroken en wordt de gebruiker in de externe sessie een bericht weer gegeven dat de verbinding is verbroken.

   ![Los](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).