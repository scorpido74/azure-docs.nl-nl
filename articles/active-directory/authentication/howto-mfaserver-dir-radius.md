---
title: RADIUS en Azure MFA Server - Azure Active Directory
description: Leer hoe u RADIUS-verificatie en Azure Multi-Factor Authentication-server implementeert.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 552226c35d4d129f73b96b689871708950b7ffb1
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80652951"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS-verificatie integreren met Azure Multi-Factor Authentication-server

RADIUS is een standaardprotocol om verificatieaanvragen te accepteren en te verwerken. De Azure Multi-Factor Authentication-server kan optreden als een RADIUS-server. Plaats de server hiervoor tussen uw RADIUS-client (VPN-apparaat) en het verificatiedoel om verificatie in twee stappen toe te voegen. Het verificatiedoel kan Active Directory, een LDAP-adreslijst of een andere RADIUS-server zijn. Azure Multi-Factor Authentication (MFA) werkt alleen als u de Azure MFA-server zo configureert dat deze kan communiceren met zowel de clientservers als het verificatiedoel. De Azure MFA-server accepteert aanvragen van een RADIUS-client, controleert referenties met behulp van het verificatiedoel, voegt Azure Multi-Factor Authentication toe en stuurt een antwoord terug naar de RADIUS-client. De verificatieaanvraag slaagt alleen als zowel de primaire verificatie als de Azure Multi-Factor Authentication slaagt.

> [!IMPORTANT]
> Dit artikel is alleen voor gebruikers van Azure MFA Server. Als u Azure MFA in de cloud gebruikt, ziet u in plaats daarvan hoe u [kunt integreren met RADIUS-verificatie voor Azure MFA.](howto-mfa-nps-extension.md)
>
> Vanaf 1 juli 2019 biedt Microsoft geen MFA Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die MFA Server vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

> [!NOTE]
> De MFA-server ondersteunt alleen PAP (Password Authentication Protocol) en MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protocollen wanneer deze als RADIUS-server fungeert.  Andere protocollen zoals EAP (Extensible Authentication Protocol) kunnen worden gebruikt wanneer de MFA-server fungeert als een RADIUS-proxy naar een andere RADIUS-server die ondersteuning biedt voor dat protocol.
>
> In deze configuratie werken eenzijdige SMS- en OATH-tokens niet omdat de MFA-server geen geslaagd RADIUS Challenge-antwoord met alternatieve protocollen kan starten.

![Radiusverificatie in MFA-server](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>Een RADIUS-client toevoegen

Installeer de Azure Multi-Factor Authentication-server op een Windows-server als u RADIUS-verificatie wilt configureren. Als u een Active Directory-omgeving hebt, moet de server zijn toegevoegd aan het domein in het netwerk. Gebruik de volgende procedure om de Azure Multi-Factor Authentication-server te configureren:

1. Klik in de Azure Multi-Factor Authentication-server in het menu links op het pictogram RADIUS-authenticatie.
2. Schakel het selectievakje **RADIUS-verificatie inschakelen** in.
3. Wijzig de verificatie- en accountingpoorten op het tabblad Clients als de Azure MFA RADIUS-service naar RADIUS-aanvragen op niet-standaardpoorten moet luisteren.
4. Klik op**toevoegen**.
5. Voer het IP-adres in van het apparaat dat of de server die moet worden geverifieerd bij de Azure Multi-Factor Authentication-server, en voeg (optioneel) een toepassingsnaam en een gedeeld geheim toe.

   De naam van de toepassing wordt opgenomen in rapporten en kan worden weergegeven in verificatieberichten via sms of in mobiele apps.

   Het gedeelde geheim moet op de Azure Multi-Factor Authentication-server en het apparaat/de server hetzelfde zijn.

6. Schakel het selectievakje **Overeenkomende Multi-Factor Authentication-gebruiker vereisen** in als alle gebruikers zijn geïmporteerd op de server en aan meervoudige verificatie moeten worden onderworpen. Als een groot aantal gebruikers nog niet is geïmporteerd op de server of zijn vrijgesteld van verificatie in twee stappen, laat u het selectievakje uitgeschakeld.
7. Schakel het selectievakje **Alternatief OATH-token inschakelen** in als u OATH-wachtwoordcodes van mobiele verificatie-apps wilt gebruiken als terugvaloptie.
8. Klik op **OK**.

Herhaal stap 4 tot en met 8 om het gewenste aantal extra RADIUS-clients toe te voegen.

## <a name="configure-your-radius-client"></a>De RADIUS-client configureren

1. Klik op het tabblad **Doel**.
   * Als de Azure MFA Server is geïnstalleerd op een server die is verbonden met een domein in een Active Directory-omgeving, selecteert u **Windows-domein**.
   * Selecteer **LDAP-binding** als gebruikers moeten worden geverifieerd aan de hand van een LDAP-adreslijst.
      Selecteer het pictogram Adreslijstintegratie en bewerk de LDAP-configuratie op het tabblad Instellingen om ervoor te zorgen dat de server verbinding kan maken met uw adreslijst. In de [handleiding voor LDAP-proxyconfiguratie](howto-mfaserver-dir-ldap.md) vindt u instructies voor de configuratie van LDAP.
   * Als gebruikers moeten worden geverifieerd tegen een andere RADIUS-server, selecteert u **RADIUS-server(s)**.
1. Klik op **toevoegen** als u de server waarop de Azure MFA-server de RADIUS-aanvragen een volmacht geeft, wilt configureren.
1. Voer in het dialoogvenster RADIUS-server toevoegen het IP-adres van de RADIUS-server en een gedeeld geheim in.

   Het gedeelde geheim moet op de Azure Multi-Factor Authentication-server en de RADIUS-server hetzelfde zijn. Wijzig de verificatiepoort en accountingpoort als door de RADIUS-server andere poorten worden gebruikt.

1. Klik op **OK**.
1. Voeg de Azure MFA-server toe als een RADIUS-client op de andere RADIUS-server, opdat toegangsverzoeken afkomstig van de Azure MFA-server kunnen worden verwerkt. Gebruik hetzelfde gedeelde geheim dat ook op de Azure Multi-Factor Authentication-server is geconfigureerd.

Herhaal deze stappen om meer RADIUS-servers toe te voegen. Met de knoppen **Omhoog** en **Omlaag** kunt u de volgorde configureren waarin de Azure MFA-server de servers aanroept.

De configuratie van de Azure Multi-Factor Authentication-server is hiermee voltooid. De server luistert nu naar de geconfigureerde poorten voor RADIUS-toegangsverzoeken van de geconfigureerde clients.

## <a name="radius-client-configuration"></a>RADIUS-clientconfiguratie

Als u de RADIUS-client wilt configureren, gebruikt u de volgende richtlijnen:

* Configureer uw toestel/server om via RADIUS te verifiëren naar het IP-adres van de Azure Multi-Factor Authentication Server, dat fungeert als de RADIUS-server.
* Gebruik hetzelfde gedeelde geheim dat eerder is geconfigureerd.
* Configureer de RADIUS-time-out op 30-60 seconden, zodat er tijd is om de referenties van de gebruiker te valideren, verificatie in twee stappen uit te voeren, hun antwoord te ontvangen en vervolgens te reageren op het RADIUS-toegangsverzoek.

## <a name="next-steps"></a>Volgende stappen

Lees hier meer over [de integratie van RADIUS-verificatie](howto-mfa-nps-extension.md) als u Azure Multi-Factor Authentication in de cloud gebruikt. 
