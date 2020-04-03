---
title: Azure Bastion-sessiebewaking en -beheer | Microsoft Documenten
description: In dit artikel leert u hoe u een lopende sessie selecteert en de verbinding met de verbinding force-verbreken of verwijderen.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 6bf80be4868295145fa79ae29d5322181b6131d1
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619182"
---
# <a name="session-monitoring-and-management-for-azure-bastion"></a>Sessiebewaking en -beheer voor Azure Bastion

Zodra de Bastion-service is ingericht en geïmplementeerd in uw virtuele netwerk, u deze gebruiken om naadloos verbinding te maken met elke vm in dit virtuele netwerk. Als gebruikers verbinding maken met workloads, kan Azure Bastion worden gebruikt om de externe sessies te controleren en snel beheeracties uit te voeren. Met Azure Bastion-sessiebewaking u zien met welke gebruikers u bent verbonden met welke VM's. Het toont het IP dat de gebruiker verbonden van, hoe lang ze zijn aangesloten, en wanneer ze aangesloten. Met de sessiebeheerervaring u een lopende sessie selecteren en een sessie forceren of verwijderen om de gebruiker los te koppelen van de lopende sessie.

## <a name="monitor-remote-sessions"></a><a name="monitor"></a>Externe sessies controleren

1. Navigeer in de [Azure-portal](https://portal.azure.com)naar uw Azure Bastion-bron en selecteer **Sessies** op de Azure Bastion-pagina.

   ![Sessies](./media/session-monitoring/sessions.png)
2. Op de pagina **Sessies** ziet u de lopende externe sessies aan de rechterkant.

   ![sessie bekijken](./media/session-monitoring/view-session.png)
3. Selecteer **Vernieuwen** om de bijgewerkte lijst met externe sessies te bekijken. Wanneer u Vernieuwen selecteert, haalt Azure Bastion de nieuwste bewakingsinformatie op en vernieuwt deze in de portal.

   ![refresh](./media/session-monitoring/refresh.png)

>[!IMPORTANT]
> Schakel poort 4443 in voor binnenkomend verkeer van Gateway Manager voor sessiebewaking naar werk.
>

## <a name="delete-or-force-disconnect-an-ongoing-remote-session"></a><a name="view"></a>Een doorlopende externe sessie verwijderen of verbreken

U een set sessie(s) selecteren en deze met kracht loskoppelen. In de volgende stappen ziet u hoe u externe sessies verwijdert:

1. Navigeer naar uw Azure Bastion-bron en selecteer **Sessies** op de Azure Bastion-pagina.

   ![navigate](./media/session-monitoring/navigate.png)
2. Nadat u Sessies hebt geselecteerd, ziet u een lijst met externe sessies.

   ![lijstsessies](./media/session-monitoring/list.png)
3. Selecteer een specifieke externe sessie, selecteer vervolgens de drie ellipsen aan de rechterkant van de sessierij en selecteer **Verwijderen**.

   ![delete](./media/session-monitoring/delete.png)
4. Wanneer u Verwijderen selecteert, wordt de verbinding met de externe sessie verbroken en krijgt de gebruiker een bericht 'U bent losgekoppeld' te zien in de externe sessie.

   ![verbinding verbreken](./media/session-monitoring/disconnect.png)

## <a name="next-steps"></a>Volgende stappen

Lees de [Bastion FAQ](bastion-faq.md).