---
title: Azure AD Connect Cloud-inrichtings agent installeren
description: Dit onderwerp beschrijft stapsgewijze instructies voor het installeren van inrichtings agent.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e3b2a113d46ff3d8799927f56fa66601c94ed5
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74846229"
---
# <a name="install-azure-ad-connect-cloud-provisioning-agent"></a>Azure AD Connect Cloud-inrichtings agent installeren
Dit document begeleidt u stapsgewijs door het installatie proces voor de Azure AD Connect inrichtings agent en hoe u deze in eerste instantie kunt configureren in de Azure Portal.

>[!IMPORTANT]
>In de volgende installatie-instructies wordt ervan uitgegaan dat aan alle [vereisten](how-to-prerequisites.md) is voldaan.

Het installeren en configureren van Azure AD Connect inrichting wordt uitgevoerd in de volgende stappen:
    
- [De agent installeren](#install-the-agent)
- [Agent installatie verifiëren](#verify-agent-installation)


## <a name="install-the-agent"></a>De agent installeren

1. Meld u aan bij de server die u wilt gebruiken met beheerders machtigingen voor de onderneming.
2. Ga naar het Azure Portal en selecteer Azure Active Directory aan de linkerkant.
3. Klik op **beheer inrichten (preview)** en selecteer **Alle agents controleren**.
3. Down load de Azure AD Connect-inrichtings agent vanuit de Azure Portal.
![Welkomstscherm](media/how-to-install/install9.png)</br>
3. Voer de Azure AD Connect-inrichting uit (AADConnectProvisioningAgent. installer)
3. **Accepteer** de licentie voorwaarden in het welkomst scherm en klik op **installeren**.</br>
![Welkomstscherm](media/how-to-install/install1.png)</br>

4. Zodra deze bewerking is voltooid, wordt de configuratie wizard gestart.  Meld u aan met uw Azure AD Global Administrator-account.
5. Klik in het scherm **verbinding maken Active Directory** op **map toevoegen** en meld u vervolgens aan met uw Active Directory beheerders account.  Met deze bewerking wordt uw on-premises Directory toegevoegd.  Klik op **Volgende**.</br>
![Welkomstscherm](media/how-to-install/install3.png)</br>

6. Klik in het scherm **configuratie voltooid** op **bevestigen**.  Met deze bewerking wordt de agent geregistreerd en opnieuw gestart.</br>
![Welkomstscherm](media/how-to-install/install4.png)</br>

7. Zodra deze bewerking is voltooid, ziet u een melding dat **uw verificatie is geslaagd.**  U kunt op **Afsluiten**klikken.</br>
![Welkomstscherm](media/how-to-install/install5.png)</br>
8. Als het eerste openings scherm nog steeds wordt weer gegeven, klikt u op **sluiten**.


## <a name="verify-agent-installation"></a>Agent installatie verifiëren
Verificatie van de agent vindt plaats in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent
Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden de optie **inrichting beheren (preview)** .</br>
![Azure-portal](media/how-to-install/install6.png)</br>

3.  Klik in het scherm **Azure AD inrichten (preview)** op **Alle agents controleren**.
![Azure AD-inrichtings](media/how-to-install/install7.png)</br>
 
4. Op het **scherm on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie is en is gemarkeerd als **actief**.
![inrichten agents](media/how-to-install/verify1.png)</br>

### <a name="verify-the-port"></a>De poort controleren
Als u wilt controleren of het Azure luistert op poort 443 en dat uw agent ermee kan communiceren, kunt u het volgende gebruiken:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Met deze test wordt gecontroleerd of uw agents kunnen communiceren met Azure via poort 443.  Open een browser en navigeer naar de bovenstaande URL van de server waarop de agent is geïnstalleerd.
![Services](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Op de lokale server
Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd:

1.  Meld u aan bij de server met een beheerders account
2.  Open **Services** door ernaar te navigeren of door naar start/uitvoeren/services. msc te gaan.
3.  Zorg ervoor dat bij **Services** **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt **uitgevoerd**.
![Services](media/how-to-troubleshoot/troubleshoot1.png)

>[!IMPORTANT]
>De agent is geïnstalleerd, maar moet worden geconfigureerd en ingeschakeld voordat de synchronisatie van gebruikers wordt gestart.  Zie [Azure AD Connect een nieuwe agent configuratie inrichten](how-to-configure.md)voor meer informatie over het configureren van een nieuwe agent.



## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)
 
