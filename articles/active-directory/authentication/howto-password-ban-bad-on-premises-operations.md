---
title: Bewerkingen en rapporten voor wachtwoord beveiliging-Azure Active Directory
description: Azure AD-bewerkingen voor wachtwoord beveiliging na de implementatie en rapportage
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98668fc836aa21bdd14831c4a801557cdab5a202
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381668"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Operationele procedures voor Azure AD-wachtwoord beveiliging

Nadat u de [installatie van Azure AD-wachtwoord beveiliging](howto-password-ban-bad-on-premises-deploy.md) on-premises hebt voltooid, zijn er een paar items die moeten worden geconfigureerd in de Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>De aangepaste lijst met uitgesloten wacht woorden configureren

Volg de instructies in het artikel [de aangepaste lijst met verboden wacht woorden configureren](howto-password-ban-bad-configure.md) voor de stappen voor het aanpassen van de lijst met geblokkeerde wacht woorden voor uw organisatie.

## <a name="enable-password-protection"></a>Wachtwoord beveiliging inschakelen

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en blader naar **Azure Active Directory**, **verificatie methoden**en vervolgens **wachtwoord beveiliging**.
1. Stel **wachtwoord beveiliging op Windows Server Active Directory** in op **Ja**
1. Zoals vermeld in de [implementatie handleiding](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), wordt aangeraden de **modus** in te stellen op **controleren**
   * Nadat u vertrouwd bent met de functie, kunt u de **modus** **afdwingen naar afgedwongen**
1. Klik op **Opslaan**.

![Azure AD-onderdelen voor wachtwoord beveiliging inschakelen in de Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Controle modus

De controle modus is bedoeld als een manier om de software uit te voeren in een ' What '-modus. Elke DC-Agent service evalueert een inkomend wacht woord volgens het huidige actieve beleid. Als het huidige beleid is geconfigureerd om te worden uitgevoerd in de controle modus, resulteren de wacht woorden in gebeurtenis logboek berichten, maar worden ze geaccepteerd. Dit is het enige verschil tussen de controle-en afdwingings modus. alle andere bewerkingen worden hetzelfde uitgevoerd.

> [!NOTE]
> Micro soft raadt u aan om de initiële implementatie en het testen altijd uit te voeren in de controle modus. Gebeurtenissen in het gebeurtenis logboek moeten vervolgens worden gecontroleerd om te anticiperen of bestaande operationele processen worden verstoord zodra de modus afdwingen is ingeschakeld.

## <a name="enforce-mode"></a>Modus afdwingen

De afdwingings modus is bedoeld als de definitieve configuratie. Net als in de controle modus evalueert elke DC-Agent service binnenkomende wacht woorden volgens het huidige actieve beleid. Als de afdwingings modus is ingeschakeld, wordt een wacht woord dat als onveilig wordt beschouwd volgens het beleid, geweigerd.

Wanneer een wacht woord in de afdwingings modus wordt afgewezen door de Azure AD-agent voor wachtwoord beveiliging, is de zicht bare impact van een eind gebruiker identiek aan wat ze zouden zien als hun wacht woord is geweigerd door traditionele on-premises wachtwoord complexiteit afdwingen. Een gebruiker ziet bijvoorbeeld het volgende traditionele fout bericht op het scherm Windows logon\change-wacht woord:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Dit bericht bevat slechts één voor beeld van een aantal mogelijke resultaten. Het specifieke fout bericht kan variëren, afhankelijk van de werkelijke software of het scenario waarmee wordt geprobeerd een onbeveiligd wacht woord in te stellen.

Betrokken eind gebruikers moeten mogelijk samen werken met hun IT-mede werkers om inzicht te krijgen in de nieuwe vereisten, en kunnen beter beveiligde wacht woorden kiezen.

> [!NOTE]
> Azure AD-wachtwoord beveiliging heeft geen controle over het specifieke fout bericht dat wordt weer gegeven door de client computer wanneer een zwak wacht woord wordt afgewezen.

## <a name="enable-mode"></a>Modus inschakelen

Deze instelling moet in de standaard status ingeschakeld (Ja) blijven staan. Als u deze instelling instelt op uitgeschakeld (Nee), worden alle geïmplementeerde domein controller-agents van Azure AD-wachtwoord beveiliging in een quiescent-modus geplaatst, waarbij alle wacht woorden worden geaccepteerd als-is en er geen validatie activiteiten worden uitgevoerd (bijvoorbeeld niet-even audit gebeurtenissen wordt verzonden).

## <a name="next-steps"></a>Volgende stappen

[Bewaking voor Azure AD-wachtwoord beveiliging](howto-password-ban-bad-on-premises-monitor.md)
