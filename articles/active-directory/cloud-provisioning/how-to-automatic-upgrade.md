---
title: 'Azure AD Connect Cloud-inrichtings agent: automatische upgrade | Microsoft Docs'
description: In dit onderwerp wordt de ingebouwde functie voor automatische upgrades in de Azure AD Connect Cloud inrichtings agent beschreven.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 193804064fbf6d1abb2ce06df1e923ec709ef6de
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794456"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect Cloud-inrichtings agent: automatische upgrade

Ervoor zorgen dat de installatie van de Azure AD Connect Cloud inrichtings agent altijd up-to-date is, is nog nooit zo eenvoudig geweest met de functie voor **automatische upgrades** . Deze functie is standaard ingeschakeld en kan niet worden uitgeschakeld.

De agent wordt hier geïnstalleerd: **' Program FILES\AZURE AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe '**

U kunt uw versie controleren door met de rechter muisknop op het uitvoer bare bestand te klikken en eigenschappen en vervolgens details te selecteren.

![Versie van agent bestand](media/how-to-automatic-upgrade/agent1.png)

De agent Updater wordt hier geïnstalleerd: **' Program FILES\AZURE AD Connect inrichtings agent Updater\AzureADConnectAgentUpdater.exe '**

U kunt uw versie controleren door met de rechter muisknop op het uitvoer bare bestand te klikken en eigenschappen en vervolgens details te selecteren.

![Versie van agent updater](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstalling-the-agent"></a>De agent verwijderen
Als u de agent wilt verwijderen, gaat u naar **een programma verwijderen of wijzigen** en verwijdert u het volgende:

- Updater Microsoft Azure AD-agent
- Inrichtings agent Microsoft Azure AD verbinding maken
- Inrichtings agent pakket Microsoft Azure AD koppelen

![Agent verwijderen](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)

