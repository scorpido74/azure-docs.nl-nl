---
title: 'Azure AD Connect cloud provisioning agent: Automatische upgrade | Microsoft Documenten'
description: In dit artikel wordt de ingebouwde automatische upgradefunctie beschreven in de Azure AD Connect-cloudinrichtingsagent.
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
ms.openlocfilehash: f09b2fc685881aa8a7bd87b6a855c657af9ef43d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78190310"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Azure AD Connect cloud provisioning agent: automatische upgrade

Met de automatische upgradefunctie u ervoor zorgen dat de installatie van uw Azure Active Directory (Azure AD) Connect-cloudinrichtingsagent altijd up-to-date is.

De agent is hier geïnstalleerd: "Program files\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Als u uw versie wilt verifiëren, klikt u met de rechtermuisknop op de uitvoerbare eigenschappen en selecteert u eigenschappen en vervolgens details.

![Agent-bestandsversie](media/how-to-automatic-upgrade/agent1.png)

De agentupdater is hier geïnstalleerd: "Program files\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Als u uw versie wilt verifiëren, klikt u met de rechtermuisknop op de uitvoerbare eigenschappen en selecteert u eigenschappen en vervolgens details.

![Agent updater-versie](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Agent verwijderen
Als u de agent wilt verwijderen, gaat u naar **Verwijderen of een programma wijzigen** en verwijdert u het volgende:

- **Microsoft Azure AD Connect Agent Updater**
- **Microsoft Azure AD Connect Provisioning Agent**
- **Microsoft Azure AD Connect Provisioning Agent-pakket**

![Agent verwijdering](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)

