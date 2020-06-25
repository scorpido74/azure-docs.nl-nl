---
title: De agent voor Azure AD Connect-cloudinrichting installeren
description: In dit artikel wordt beschreven hoe u de Azure AD Connect Cloud-inrichtings Agent installeert.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6f06e9ad03face5082950ecedd3f73b97dc6ae4
ms.sourcegitcommit: f98ab5af0fa17a9bba575286c588af36ff075615
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/25/2020
ms.locfileid: "85360925"
---
# <a name="install-the-azure-ad-connect-cloud-provisioning-agent"></a>De agent voor Azure AD Connect-cloudinrichting installeren
Dit document helpt u bij het installatie proces voor de Azure Active Directory (Azure AD) Connect inrichtings agent en hoe u deze in eerste instantie kunt configureren in de Azure Portal.

>[!IMPORTANT]
>In de volgende installatie-instructies wordt ervan uitgegaan dat aan alle [vereisten](how-to-prerequisites.md) is voldaan.

Het installeren en configureren van Azure AD Connect inrichting wordt uitgevoerd in de volgende stappen:
    
- [De agent installeren](#install-the-agent)
- [Agentinstallatie verifiëren](#verify-agent-installation)


## <a name="install-the-agent"></a>De agent installeren
Voer de volgende stappen uit om de agent te installeren.

1. Meld u aan bij de server die u wilt gebruiken met beheerders machtigingen voor de onderneming.
1. Ga naar Azure Portal. Selecteer aan de linkerkant **Azure Active Directory**.
1. Selecteer **inrichting beheren (preview)**  >  **Alle agents controleren**.
1. Down load de Azure AD Connect-inrichtings agent vanuit de Azure Portal.

   ![On-premises agent downloaden](media/how-to-install/install9.png)</br>
1. Voer het Azure AD Connect Provisioning Installer (AADConnectProvisioningAgent. installer) uit.
1. Accepteer de licentie voorwaarden op het scherm Microsoft Azure AD het pakket voor het maken van een **inrichtings agent** en selecteer **installeren**.

   ![Microsoft Azure AD scherm voor het inrichtings agent-pakket koppelen](media/how-to-install/install1.png)</br>

1. Nadat deze bewerking is voltooid, wordt de configuratie wizard gestart. Meld u aan met uw globale beheerdersreferenties voor Azure AD.
1. Selecteer in het scherm **verbinding maken Active Directory** de optie **map toevoegen**. Meld u vervolgens aan met uw Active Directory-beheerders account. Met deze bewerking wordt uw on-premises Directory toegevoegd. Selecteer **Next**.

   ![Active Directory scherm verbinden](media/how-to-install/install3.png)</br>

1. Selecteer **bevestigen**in het scherm **configuratie voltooid** . Met deze bewerking wordt de agent geregistreerd en opnieuw gestart.

   ![Scherm configuratie voltooid](media/how-to-install/install4.png)</br>

1. Nadat deze bewerking is voltooid, ziet u de melding dat **de agent configuratie is geverifieerd.** Selecteer **Afsluiten**.

   ![Knop Afsluiten](media/how-to-install/install5.png)</br>
1. Als u nog steeds het scherm eerste **Microsoft Azure AD Connect inrichtings agent-pakket** ziet, selecteert u **sluiten**.

## <a name="verify-agent-installation"></a>Agentinstallatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory**  >  **Azure AD CONNECT**. Selecteer in het midden de optie **inrichting beheren (preview)**.

   ![Azure Portal](media/how-to-install/install6.png)</br>

1.  Selecteer **Alle agents controleren**op het scherm **Azure AD Provisioning (preview)** .

    ![Optie Alle agents controleren](media/how-to-install/install7.png)</br>
 
1. Op het scherm **on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie is en is gemarkeerd als *actief*.

   ![Scherm on-premises ingerichte agents](media/how-to-install/verify1.png)</br>



### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd.

1.  Meld u met een beheerders account aan bij de server.
1.  Open **Services** door te navigeren naar de service of **door te gaan**met het  >  **uitvoeren**van  >  **Services. msc**.
1.  Zorg er bij **Services**voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt *uitgevoerd*.

    ![Scherm Services](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>De agent is geïnstalleerd, maar moet worden geconfigureerd en ingeschakeld voordat de synchronisatie van gebruikers wordt gestart. Als u een nieuwe agent wilt configureren, raadpleegt u [een nieuwe configuratie maken voor Azure AD Connect inrichting op basis van de Cloud](how-to-configure.md).



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)
 
