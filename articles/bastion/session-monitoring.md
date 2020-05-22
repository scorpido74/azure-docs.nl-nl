---
title: Azure Bastion-sessie bewaking en-beheer | Microsoft Docs
description: In dit artikel vindt u informatie over het selecteren van een actieve sessie en het afdwingen van verbinding maken of verwijderen.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: charwen
ms.openlocfilehash: 617ec30fc9b97e89b6ccd0de6255d65da94d7b63
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780449"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Sessie bewaking en-beheer voor Azure Bastion

Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, kunt u deze gebruiken om naadloos verbinding te maken met elke virtuele machine in dit virtuele netwerk. Wanneer gebruikers verbinding maken met werk belastingen, kan Azure Bastion worden gebruikt om de externe sessies te controleren en snelle beheer acties uit te voeren. Met de Azure Bastion-sessie bewaking kunt u zien welke gebruikers zijn verbonden met welke Vm's. Hier wordt het IP-adres weer gegeven waarmee de gebruiker verbinding maakt, hoe lang ze zijn verbonden en wanneer ze zijn verbonden. Met de sessie beheer ervaring kunt u een actieve sessie selecteren en geforceerd afbreken of een sessie verwijderen om de verbinding van de gebruiker met de actieve sessie te verbreken.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Externe sessies bewaken

1. Ga in het [Azure Portal](https://portal.azure.com)naar uw Azure Bastion-resource en selecteer **sessies** op de pagina Azure Bastion.

   ![sessies](./media/session-monitoring/sessions.png)
2. Op de pagina **sessies** kunt u de actieve externe sessies aan de rechter kant bekijken.

   ![sessie weer geven](./media/session-monitoring/view-session.png)
3. Selecteer **vernieuwen** om de bijgewerkte lijst met externe sessies weer te geven. Wanneer u vernieuwen selecteert, haalt Azure Bastion de meest recente bewakings gegevens op en vernieuwt deze in de portal.

   ![refresh](./media/session-monitoring/refresh.png)


## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Een actieve externe sessie verwijderen of forceren

U kunt een set sessie (s) selecteren en de verbinding forceren. De volgende stappen laten zien hoe u externe sessies verwijdert:

1. Navigeer naar uw Azure Bastion-resource en selecteer **sessies** op de Azure Bastion-pagina.

   ![navigate](./media/session-monitoring/navigate.png)
2. Nadat u sessies hebt geselecteerd, ziet u een lijst met externe sessies.

   ![sessies weer geven](./media/session-monitoring/list.png)
3. Selecteer een specifieke externe sessie en selecteer vervolgens de drie weglatings tekens aan de rechter kant van de rij met de sessie en selecteer vervolgens **verwijderen**.

   ![delete](./media/session-monitoring/delete.png)
4. Wanneer u verwijderen selecteert, wordt de externe sessie verbroken en wordt de gebruiker in de externe sessie een bericht weer gegeven dat de verbinding is verbroken.

   ![verbinding verbreken](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Volgende stappen

Lees de [Veelgestelde vragen over Bastion](bastion-faq.md).
