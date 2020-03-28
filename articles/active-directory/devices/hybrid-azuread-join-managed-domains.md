---
title: Hybride Azure Active Directory-deelname configureren voor beheerde domeinen | Microsoft Docs
description: Informatie over het configureren van hybride Azure Active Directory-deelname voor beheerde domeinen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239110"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Zelfstudie: Hybride Azure Active Directory-koppeling configureren voor beheerde domeinen

In deze zelfstudie leert u hoe u de verbinding met hybride Azure Active Directory (Azure AD) configureert voor apparaten die zijn verbonden met Active Directory-domein. Deze methode ondersteunt een beheerde omgeving die zowel on-premises Active Directory als Azure AD bevat.

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beschermen. U de identiteit van een apparaat gebruiken om uw resources op elk gewenst moment en vanaf elke locatie te beschermen. U dit doel bereiken door apparaatidentiteiten te beheren in Azure AD. Gebruik een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Dit artikel richt zich op hybride Azure AD join.

Als u uw apparaten naar Azure AD brengt, wordt de productiviteit van gebruikers verhoogd via single sign-on (SSO) in uw cloud en on-premises resources. U tegelijkertijd toegang tot uw cloud- en on-premises resources beveiligen met [Voorwaardelijke toegang.](../active-directory-conditional-access-azure-portal.md)

U een beheerde omgeving implementeren door gebruik te maken van [password hash sync (PHS)](../hybrid/whatis-phs.md) of [pass-through authentication (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze single sign-on.](../hybrid/how-to-connect-sso.md) Voor deze scenario's hoeft u geen federatieserver te configureren voor verificatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Downlevel Windows-apparaten inschakelen
> * Gekoppelde apparaten verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

- De [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 of hoger)
- De referenties van een globale beheerder voor uw Azure AD-tenant
- De referenties van de bedrijfsbeheerder voor elk van de forests

Maak jezelf vertrouwd met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [How To: Uw hybride Azure Active Directory join-implementatie plannen](hybrid-azuread-join-plan.md)
- [Gecontroleerde validatie van hybride Azure AD-deelname](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD biedt geen ondersteuning voor smartcards of certificaten in beheerde domeinen.

Controleer of Azure AD Connect de computerobjecten heeft gesynchroniseerd van de apparaten waarvan u hybride Azure AD wilt zijn, is verbonden met Azure AD. Als de computerobjecten tot specifieke organisatie-eenheden (OE's) behoren, configureert u de Gegevens om te synchroniseren in Azure AD Connect. Zie Filteren op [basis van configuratie-eenheid](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)voor meer informatie over het synchroniseren van computerobjecten met Azure AD Connect.

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard om hybride Azure AD-join te configureren. De wizard vereenvoudigt het configuratieproces aanzienlijk. De wizard configureert de SERVICE Connection Points (SCP's) voor apparaatregistratie.

De configuratiestappen in dit artikel zijn gebaseerd op het gebruik van de wizard in Azure AD Connect.

Voor hybride Azure AD-join moeten apparaten toegang hebben tot de volgende Microsoft-bronnen vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Als u naadloze SSO gebruikt of van plan bent te gebruiken)

Als uw organisatie toegang tot internet nodig heeft via een uitgaande proxy, raden we u aan [Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) te implementeren om Windows 10-computers in te schakelen voor apparaatregistratie bij Azure AD. Zie [Automatische detectie oplossen](/previous-versions/tn-archive/cc302643(v=technet.10))voor problemen voor het configureren en beheren van WPAD.

Als u WPAD niet gebruikt, u proxy-instellingen op uw computer configureren vanaf Windows 10 1709. Zie [WinHTTP-proxy-instellingen die door GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)zijn geïmplementeerd voor meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met winhttp-instellingen, kunnen computers die geen verbinding kunnen maken met de geconfigureerde proxy geen verbinding maken met internet.

Als uw organisatie toegang tot internet nodig heeft via een geverifieerde uitgaande proxy, moet u ervoor zorgen dat uw Windows 10-computers zich met succes kunnen verifiëren naar de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie uitvoeren met behulp van machinecontext, configureert u uitgaande proxyverificatie met behulp van machinecontext. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Controleer of het apparaat toegang heeft tot de bovenstaande Microsoft-bronnen onder het systeemaccount met behulp van het script Voor de [registratie van het testapparaat.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Ga als lid van een hybride Azure AD-lid met Azure AD Connect:

1. Start Azure AD Connect en selecteer **Configureren**.

   ![Welkom](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. Selecteer in **Extra taken**de optie **Apparaatopties configureren**en selecteer **Volgende**.

   ![Extra taken](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Selecteer **Volgende**in **Overzicht**.

   ![Overzicht](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. Voer in **Verbinding maken met Azure AD**de referenties in van een globale beheerder voor uw Azure AD-tenant.  

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Selecteer **in Apparaatopties**de optie **Hybride Azure AD-verbinding configureren**en selecteer **Volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Voer in **SCP-configuratie**voor elk forest waar u Azure AD Connect wilt configureren de volgende stappen uit en selecteer **vervolgens Volgende**.

   1. Selecteer het **forest**.
   1. Selecteer een **verificatieservice**.
   1. Selecteer **Toevoegen** om de referenties van de bedrijfsbeheerder in te voeren.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Selecteer **in apparaatbesturingssystemen**de besturingssystemen die apparaten in uw Active Directory-omgeving gebruiken en selecteer **Volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Selecteer **Configureren** **in Gereed om te configureren**.

   ![Gereed voor configuratie](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Selecteer **Afsluiten**in **Configuratie voltooid**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Downlevel Windows-apparaten inschakelen

Als sommige van uw apparaten met domeinverbonden apparaten apparaten op Windows-niveau zijn, moet u het als:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Naadloze SSO configureren
- Microsoft Workplace Join voor Windows-computers op downniveau installeren

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. Zie [Windows 7-ondersteuning is beëindigd](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)voor meer informatie.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Als u hybride Azure AD-verbinding met uw Windows-apparaten op downniveau wilt voltooien en certificaatprompts wilt voorkomen wanneer apparaten zich verifiëren bij Azure AD, u een beleid naar uw met een domein verbonden apparaten pushen om de volgende URL's toe te voegen aan de lokale intranetzone in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

U moet ook **Updates toestaan voor de statusbalk inschakelen via het script** in de lokale intranetzone van de gebruiker.

### <a name="configure-seamless-sso"></a>Naadloze SSO configureren

Als u hybride Azure AD-verbinding wilt maken met uw Windows-apparaten op downniveau in een beheerd domein dat [wachtwoordhashsynchronisatie](../hybrid/whatis-phs.md) of [pass-through-verificatie](../hybrid/how-to-connect-pta.md) gebruikt als uw Azure AD-cloudverificatiemethode, moet u ook [naadloze SSO configureren.](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Microsoft Workplace Join voor Windows-computers op downniveau installeren

Als u apparaten op downniveau van Windows wilt registreren, moeten organisaties Microsoft Workplace Join installeren [voor niet-Windows 10-computers.](https://www.microsoft.com/download/details.aspx?id=53554) Microsoft Workplace Join voor niet-Windows 10-computers is beschikbaar in het Microsoft Download centrum.

U het pakket implementeren met behulp van een softwaredistributiesysteem zoals [Microsoft Endpoint Configuration Manager.](/configmgr/) Het pakket ondersteunt de standaard `quiet` stille installatieopties met de parameter. De huidige versie van Configuration Manager biedt voordelen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatieprogramma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikerscontext. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak voegt het apparaat in stilte samen met Azure AD door de gebruikersreferenties te gebruiken nadat deze is geverifieerd met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Controleer de status van de apparaatregistratie in uw **[Azure-tenant met Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. Deze cmdlet bevindt zich in de [Azure Active Directory PowerShell-module](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Wanneer u de **cmdlet Get-MSolDevice** gebruikt om de servicegegevens te controleren:

- Er moet een object met de **apparaat-id** bestaan dat overeenkomt met de id op de Windows-client.
- De waarde voor **DeviceTrustType** is **Domain Joined**. Deze instelling is gelijk aan de **hybride Azure AD-status** op de pagina **Apparaten** in de Azure AD-portal.
- Voor apparaten die worden gebruikt in Voorwaardelijke toegang, is de waarde voor **Ingeschakeld** **True** en **wordt DeviceTrustLevel** **beheerd.**

Ga als volgt te werk om de servicedetails te controleren:

1. Open Windows PowerShell als administrator.
1. Voer `Connect-MsolService` deze in om verbinding te maken met uw Azure-tenant.  
1. Voer `get-msoldevice -deviceId <deviceId>` in.
1. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Zie als u problemen ondervindt bij het voltooien van hybride Azure AD voor Windows-apparaten met een domein:

- [Hybride Azure Active Directory-apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure Active Directory mogelijk maken van apparaten op downniveau](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het beheren van apparaatidentiteiten met behulp van de Azure-portal.
> [!div class="nextstepaction"]
> [Apparaatidentiteiten beheren](device-management-azure-portal.md)
