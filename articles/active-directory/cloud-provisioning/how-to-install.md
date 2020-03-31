---
title: De agent voor Azure AD Connect-cloudinrichting installeren
description: In dit artikel wordt beschreven hoe u de Azure AD Connect-cloudinrichtingsagent installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4581ab89f74425682f569425f62714ead2c27c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263346"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>De agent voor Azure AD Connect-cloudinrichting installeren
Met dit document u het installatieproces voor de Azure Active Directory (Azure AD) Connect-inrichtingsagent doorlopen en hoe u deze in eerste instantie configureren in de Azure-portal.

>[!IMPORTANT]
>De volgende installatie-instructies gaan ervan uit dat aan alle [voorwaarden is](how-to-prerequisites.md) voldaan.

Het installeren en configureren van Azure AD Connect-inrichting wordt uitgevoerd in de volgende stappen:
    
- [De agent installeren](#install-the-agent)
- [Agentinstallatie verifiëren](#verify-agent-installation)


## <a name="install-the-agent"></a>De agent installeren
Voer de volgende stappen uit om de agent te installeren.

1. Meld u aan bij de server die u gebruikt met machtigingen voor bedrijfsbeheerders.
1. Ga naar Azure Portal. Selecteer Azure **Active Directory**aan de linkerkant .
1. Selecteer **Inrichting beheren (voorbeeld)** > **Alle agents controleren**.
1. Download de Azure AD Connect-inrichtingsagent van de Azure-portal.

   ![On-premises agent downloaden](media/how-to-install/install9.png)</br>
1. Voer het azure AD Connect-installatieprogramma (AADConnectProvisioningAgent.Installer) uit.
1. Accepteer de licentievoorwaarden en selecteer **Installeren**in het scherm **Microsoft Azure AD Connect Provisioning Agent Package.**

   ![Scherm Microsoft Azure AD Connect Provisioning Agent-pakket](media/how-to-install/install1.png)</br>

1. Nadat deze bewerking is voltooid, wordt de wizard configuratie gestart. Meld u aan met uw azure AD-globale beheerdersaccount.
1. Selecteer **Directory toevoegen**in het scherm Active **Directory verbinden** . Meld u vervolgens aan met uw Active Directory-beheerdersaccount. Deze bewerking voegt uw on-premises directory toe. Selecteer **Volgende**.

   ![Active Directory-scherm verbinden](media/how-to-install/install3.png)</br>

1. Selecteer **op het scherm Configuratie voltooid** de optie **Bevestigen**. Deze bewerking registreert en herstart de agent.

   ![Scherm volledig configureren](media/how-to-install/install4.png)</br>

1. Nadat deze bewerking is voltooid, ziet u de melding **uw agentconfiguratie is geverifieerd.** Selecteer **Afsluiten**.

   ![Knop Afsluiten](media/how-to-install/install5.png)</br>
1. Als u nog steeds het eerste scherm **van Microsoft Azure AD Connect Provisioning Agent Package** ziet, selecteert u **Sluiten**.

## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Agentverificatie vindt plaats in de Azure-portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Azure-portalagentverificatie
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory** > **Azure AD Connect**. Selecteer in het midden **De optie Inrichting beheren (voorbeeld)**.

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  Selecteer alle **agents**controleren in het scherm **Azure AD Provisioning (preview).**

    ![Alle agents-optie bekijken](media/how-to-install/install7.png)</br>
 
1. Op het scherm **On-premises inrichtingsagenten** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie aanwezig is en *actief*is gemarkeerd .

   ![Scherm van on-premises inrichtingsagenten](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>De poort verifiëren
Volg de volgende stappen om te controleren of Azure luistert op poort 443 en of uw agent ermee kan communiceren.

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Deze test controleert of uw agents kunnen communiceren met Azure via poort 443. Open een browser en ga naar de vorige URL vanaf de server waar de agent is geïnstalleerd.

![Verificatie van de bereikbaarheid van de poort](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Op de lokale server
Voer deze stappen uit om te controleren of de agent wordt uitgevoerd.

1.  Meld u aan bij de server met een beheerdersaccount.
1.  Open **Services** door ernaar te navigeren of door naar **Run** > **Services.msc****Run** > te gaan.
1.  Controleer **onder Services**of Microsoft Azure AD Connect Agent **Updater** en Microsoft Azure AD Connect **Provisioning Agent** aanwezig zijn en dat hun status wordt *uitgevoerd.*

    ![Servicesscherm](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>De agent is geïnstalleerd, maar moet worden geconfigureerd en ingeschakeld voordat het gebruikers gaat synchroniseren. Zie [Een nieuwe configuratie maken voor Azure AD Connect-cloudgebaseerde inrichting](how-to-configure.md)voor het configureren van een nieuwe agent.



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
 
