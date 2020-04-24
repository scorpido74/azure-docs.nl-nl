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

In deze zelf studie leert u hoe u hybride Azure Active Directory (Azure AD) kunt configureren voor Active Directory apparaten die lid zijn van een domein. Deze methode ondersteunt een beheerde omgeving met zowel on-premises Active Directory als Azure AD.

Net als bij een gebruiker in uw organisatie is een apparaat een kern identiteit die u wilt beveiligen. U kunt de identiteit van een apparaat gebruiken om uw resources te beschermen op elk gewenst moment en vanaf elke locatie. U kunt dit doel bereiken door apparaat-id's te beheren in azure AD. Gebruik een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Dit artikel is gericht op hybride Azure AD-deelname.

Als u uw apparaten naar Azure AD brengt, wordt de gebruikers productiviteit via eenmalige aanmelding (SSO) in de Cloud en on-premises resources gemaximaliseerd. U kunt de toegang tot uw Cloud en on-premises resources met [voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) op hetzelfde moment beveiligen.

U kunt een beheerde omgeving implementeren met behulp van [PHS (Password Hash Sync)](../hybrid/whatis-phs.md) of [Pass Through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md). Voor deze scenario's is het niet nodig om een Federatie server te configureren voor authenticatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Downlevel Windows-apparaten inschakelen
> * Gekoppelde apparaten verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

- De [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 of hoger)
- De referenties van een globale beheerder voor uw Azure AD-Tenant
- De referenties van de ondernemings beheerder voor elk forest

Meer vertrouwd zijn met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Procedure: uw hybride Azure Active Directory deelname-implementatie plannen](hybrid-azuread-join-plan.md)
- [Gecontroleerde validatie van hybride Azure AD-deelname](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD biedt geen ondersteuning voor Smart Cards of certificaten in beheerde domeinen.

Controleer of Azure AD Connect de computer objecten hebt gesynchroniseerd van de apparaten met wie u een hybride Azure AD wilt maken en lid wilt worden van Azure AD. Als de computer objecten deel uitmaken van specifieke organisatie-eenheden (Ou's), configureert u de Ou's voor synchronisatie in Azure AD Connect. Zie [filteren op organisatie-eenheid](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)voor meer informatie over het synchroniseren van computer objecten met behulp van Azure AD Connect.

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard voor het configureren van hybride Azure AD-deelname. De wizard vereenvoudigt het configuratie proces aanzienlijk. Met de wizard wordt de service verbindings punten (Scp's) voor apparaatregistratie geconfigureerd.

De configuratie stappen in dit artikel zijn gebaseerd op het gebruik van de wizard in Azure AD Connect.

Hybride Azure AD-deelname vereist dat apparaten toegang hebben tot de volgende micro soft-resources vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Als u naadloze SSO gebruikt of wilt gebruiken)

Als uw organisatie toegang tot internet via een uitgaande proxy vereist, kunt u het beste [Web Proxy Auto-Discovery (WPAD) implementeren](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) om Windows 10-computers in te scha kelen voor apparaatregistratie met Azure AD. Zie [problemen met automatische detectie oplossen](/previous-versions/tn-archive/cc302643(v=technet.10))om problemen op te lossen bij het configureren en beheren van WPAD.

Als u WPAD niet gebruikt, kunt u proxy-instellingen op uw computer configureren vanaf Windows 10 1709. Zie voor meer informatie [WinHTTP-proxy-instellingen geïmplementeerd door GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met behulp van WinHTTP-instellingen, kunnen computers die geen verbinding kunnen maken met de geconfigureerde proxy, geen verbinding maken met internet.

Als uw organisatie toegang tot internet via een geverifieerde uitgaande proxy vereist, moet u ervoor zorgen dat uw Windows 10-computers kunnen worden geverifieerd bij de uitgaande proxy. Omdat Windows 10 computers apparaatregistratie uitvoeren met behulp van computer context, configureert u uitgaande proxy verificatie met behulp van machine context. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Controleer of het apparaat toegang heeft tot de bovenstaande micro soft-resources onder het systeem account met behulp van het verbindings script voor [apparaatregistratie testen](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Een hybride Azure AD-deelname configureren met behulp van Azure AD Connect:

1. Start Azure AD Connect en selecteer vervolgens **configureren**.

   ![Welkom](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. Selecteer in **extra taken** **opties configureren**en selecteer vervolgens **volgende**.

   ![Extra taken](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Selecteer in **overzicht** **volgende**.

   ![Overzicht](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. In **verbinding maken met Azure AD voert u**de referenties in van een globale beheerder voor uw Azure AD-Tenant.  

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. Selecteer in **Apparaatinstellingen**de optie **hybride Azure AD-deelname configureren**en selecteer vervolgens **volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. Voer in de **configuratie van SCP**de volgende stappen uit voor elk forest waar u Azure AD Connect wilt configureren van het SCP, en selecteer vervolgens **volgende**.

   1. Selecteer het **forest**.
   1. Selecteer een **verificatie service**.
   1. Selecteer **toevoegen** om de referenties voor de ondernemings beheerder in te voeren.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. Selecteer in **besturings systemen voor apparaten**de besturings systemen die worden gebruikt in uw Active Directory omgeving en selecteer vervolgens **volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Selecteer **configureren**in **gereed voor configuratie**.

   ![Gereed voor configuratie](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Selecteer **Afsluiten**in **configuratie voltooid**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Downlevel Windows-apparaten inschakelen

Als sommige apparaten die lid zijn van een domein Windows op een lager niveau zijn, moet u:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Naadloze SSO configureren
- Micro soft Workplace Join installeren voor Windows-computers op een lager niveau

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. Zie [ondersteuning voor Windows 7 is beëindigd](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020)voor meer informatie.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Als u de hybride Azure AD join van uw Windows-apparaten op lagere niveaus wilt volt ooien en certificaat prompts wilt voor komen wanneer apparaten worden geverifieerd bij Azure AD, kunt u een beleid pushen naar uw apparaten die lid zijn van het domein om de volgende Url's toe te voegen aan de zone Lokaal intranet in Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

U moet ook **toestaan dat updates voor de status balk via script worden** ingeschakeld in de lokale intranet zone van de gebruiker.

### <a name="configure-seamless-sso"></a>Naadloze SSO configureren

Als u de hybride Azure AD-verbinding van uw Windows-apparaten op het lagere niveau wilt volt ooien in een beheerd domein dat gebruikmaakt van een [wachtwoord hash-synchronisatie](../hybrid/whatis-phs.md) of [Pass Through-verificatie](../hybrid/how-to-connect-pta.md) als uw Azure AD-Cloud verificatie methode, moet u ook [naadloze SSO configureren](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Micro soft Workplace Join installeren voor Windows-computers op een lager niveau

Voor het registreren van Windows-apparaten op een lager niveau moeten organisaties [micro soft Workplace join installeren voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554). Micro soft Workplace Join voor niet-Windows 10-computers is beschikbaar in het micro soft Download centrum.

U kunt het pakket implementeren met behulp van een software distributiesysteem, zoals [micro soft Endpoint Configuration Manager](/configmgr/). Het pakket ondersteunt de standaard opties voor installatie op de `quiet` achtergrond met de para meter. De huidige versie van Configuration Manager biedt voor delen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatie programma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikers context. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt op de achtergrond met Azure AD toegevoegd aan het apparaat met behulp van de referenties van de gebruiker na verificatie met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Controleer de registratie status van apparaten in uw Azure-Tenant met behulp van **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. Deze cmdlet bevindt zich in de [Azure Active Directory Power shell-module](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

Wanneer u de cmdlet **Get-MSolDevice** gebruikt om de service details te controleren:

- Er moet een object zijn met de **apparaat-id** die overeenkomt met de id op de Windows-client.
- De waarde voor **DeviceTrustType** is **toegevoegd**aan een domein. Deze instelling komt overeen met de **hybride Azure AD join** -status op de pagina **apparaten** in de Azure AD-Portal.
- Voor apparaten die in voorwaardelijke toegang worden gebruikt, is de waarde voor **ingeschakeld ingesteld** op **True** en wordt **DeviceTrustLevel** **beheerd**.

Ga als volgt te werk om de servicedetails te controleren:

1. Open Windows PowerShell als administrator.
1. Voer `Connect-MsolService` in om verbinding te maken met uw Azure-Tenant.  
1. Voer `get-msoldevice -deviceId <deviceId>` in.
1. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Als u problemen ondervindt bij het volt ooien van hybride Azure AD join voor Windows-apparaten die lid zijn van een domein, zie:

- [Problemen oplossen met hybride Azure Active Directory gekoppelde apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen oplossen met hybride Azure Active Directory gekoppelde apparaten op hetzelfde niveau](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Ga naar het volgende artikel voor meer informatie over het beheren van apparaat-id's met behulp van de Azure Portal.
> [!div class="nextstepaction"]
> [Apparaatidentiteiten beheren](device-management-azure-portal.md)
