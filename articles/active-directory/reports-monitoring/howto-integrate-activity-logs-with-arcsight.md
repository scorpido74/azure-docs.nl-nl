---
title: Logboeken integreren met ArcSight met Azure Monitor | Microsoft Documenten
description: Meer informatie over het integreren van Azure Active Directory-logboeken met ArcSight met Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: b37bef0d-982e-4e28-86b2-6c61ca524ae1
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/19/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05002c1b11ef31b61fb4036f09dc8edcdafca767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75608377"
---
# <a name="integrate-azure-active-directory-logs-with-arcsight-using-azure-monitor"></a>Azure Active Directory-logboeken integreren met ArcSight met Azure Monitor

[Micro Focus ArcSight](https://software.microfocus.com/products/siem-security-information-event-management/overview) is een SIEM-oplossing (Security Information and Event Management) waarmee u beveiligingsbedreigingen in uw platform detecteren en erop reageren. U Azure Active Directory (Azure AD)-logboeken nu met Azure Monitor naar ArcSight routeren met behulp van de ArcSight-connector voor Azure AD. Met deze functie u uw tenant controleren op beveiligingscompromissen met ArcSight.  

In dit artikel vindt u informatie over het doorsturen van Azure AD-logboeken naar ArcSight met Azure Monitor. 

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze functie te gebruiken:
* Een Azure-gebeurtenishub die Azure AD-activiteitslogboeken bevat. Meer informatie over het [streamen van uw activiteitslogboeken naar een gebeurtenishub](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Een geconfigureerdexemplaar van ArcSight Syslog NG Daemon SmartConnector (SmartConnector) of ArcSight Load Balancer. Als de gebeurtenissen naar ArcSight Load Balancer worden verzonden, worden ze vervolgens door de Load Balancer naar de SmartConnector verzonden.

Download en open de [configuratiehandleiding voor ArcSight SmartConnector voor Azure Monitor Event Hub.](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292) Deze handleiding bevat de stappen die u nodig hebt om de ArcSight SmartConnector voor Azure Monitor te installeren en te configureren. 

## <a name="integrate-azure-ad-logs-with-arcsight"></a>Azure AD-logboeken integreren met ArcSight

1. Voer eerst de stappen uit in het gedeelte **Voorwaarden** van de configuratiehandleiding. In deze sectie worden de volgende stappen bevat:
    * Stel gebruikersmachtigingen in Azure in om ervoor te zorgen dat er een gebruiker met de **eigenaarrol** is om de connector te implementeren en te configureren.
    * Open poorten op de server met Syslog NG Daemon SmartConnector, dus het is toegankelijk vanuit Azure. 
    * Met de implementatie wordt een Windows PowerShell-script uitgevoerd, dus u moet PowerShell inschakelen om scripts uit te voeren op de machine waar u de connector wilt implementeren.

2. Volg de stappen in het gedeelte **Connector implementeren** van de configuratiehandleiding om de connector te implementeren. In deze sectie u de connector downloaden en extraheren, toepassingseigenschappen configureren en het implementatiescript uitvoeren vanuit de uitgepakte map. 

3. Gebruik de stappen in de **implementatie verifiëren in Azure** om te controleren of de connector is ingesteld en correct functioneert. Controleer het volgende:
    * De vereiste Azure-functies worden gemaakt in uw Azure-abonnement.
    * De Azure AD-logboeken worden gestreamd naar de juiste bestemming. 
    * De toepassingsinstellingen van uw implementatie blijven bestaan in de toepassingsinstellingen in Azure Function Apps. 
    * Er wordt een nieuwe brongroep voor ArcSight gemaakt in Azure, met een Azure AD-toepassing voor de ArcSight-connector en opslagaccounts met de toegewezen bestanden in CEF-indeling.

4. Voer ten slotte de stappen na de implementatie uit in de **naimplementatieconfiguraties** van de configuratiehandleiding. In dit gedeelte wordt uitgelegd hoe u extra configuratie uitvoeren als u een App Service-abonnement gebruikt om te voorkomen dat de functie-apps na een time-outperiode niet actief worden, het streamen van diagnostische logboeken vanaf de gebeurtenishub configureert en de SysLog NG Daemon wordt bijgewerkt SmartConnector keystore-certificaat om het te koppelen aan het nieuw gemaakte opslagaccount.

5. In de configuratiehandleiding wordt ook uitgelegd hoe u de verbindingseigenschappen in Azure aanpassen en hoe u de connector upgraden en verwijderen. Er is ook een sectie over prestatieverbeteringen, waaronder het upgraden naar een [Azure-verbruiksplan](https://azure.microsoft.com/pricing/details/functions) en het configureren van een ArcSight Load Balancer als de gebeurtenisbelasting groter is dan wat een enkele Syslog NG Daemon SmartConnector aankan.

## <a name="next-steps"></a>Volgende stappen

[Configuratiehandleiding voor ArcSight SmartConnector voor Azure Monitor Event Hub](https://community.microfocus.com/t5/ArcSight-Connectors/SmartConnector-for-Microsoft-Azure-Monitor-Event-Hub/ta-p/1671292)
