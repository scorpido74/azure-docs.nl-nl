---
title: Hybride Azure Active Directory-deelname configureren voor beheerde domeinen | Microsoft Docs
description: Informatie over het configureren van hybride Azure Active Directory-deelname voor beheerde domeinen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2768dedf80ab567582322bba4b4190b31400284f
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76167479"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Zelfstudie: Hybride Azure Active Directory-koppeling configureren voor beheerde domeinen

Net als bij een gebruiker in uw organisatie is een apparaat een kern identiteit die u wilt beveiligen. U kunt de identiteit van een apparaat gebruiken om uw resources te beschermen op elk gewenst moment en vanaf elke locatie. U kunt dit doel doen door de apparaat-id's te halen en te beheren in Azure Active Directory (Azure AD) met een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Als u uw apparaten naar Azure AD brengt, wordt de gebruikers productiviteit via eenmalige aanmelding (SSO) in de Cloud en on-premises resources gemaximaliseerd. U kunt de toegang tot uw Cloud en on-premises resources met [voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) op hetzelfde moment beveiligen.

In deze zelf studie leert u hoe u hybride Azure AD join kunt configureren voor Active Directory computer apparaten die lid zijn van een domein in een beheerde omgeving. 

Een beheerde omgeving kan worden geïmplementeerd via een [PHS (Password Hash Sync)](../hybrid/whatis-phs.md) of [Pass Through-verificatie (PTA)](../hybrid/how-to-connect-pta.md) met [naadloze eenmalige aanmelding](../hybrid/how-to-connect-sso.md). Voor deze scenario's is het niet nodig om een Federatie server te configureren voor authenticatie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Downlevel Windows-apparaten inschakelen
> * Gekoppelde apparaten verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat u bekend bent met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Uw hybride implementatie van Azure AD-deelname plannen](hybrid-azuread-join-plan.md)
- [Hoe wordt de validatie van hybride Azure AD-deelname gecontroleerd](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD biedt geen ondersteuning voor Smart Cards of certificaten in beheerde domeinen.

Als u het scenario in dit artikel wilt configureren, hebt u de [nieuwste versie van Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 of hoger) geïnstalleerd.

Controleer of Azure AD Connect de computer objecten hebt gesynchroniseerd van de apparaten met wie u een hybride Azure AD wilt maken en lid wilt worden van Azure AD. Als de computer objecten deel uitmaken van specifieke organisatie-eenheden (Ou's), moet u ook de Ou's configureren om te synchroniseren in Azure AD Connect. Zie voor meer informatie over het synchroniseren van computer objecten met behulp van Azure AD Connect [filters configureren met behulp van Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard die u kunt gebruiken om hybride Azure AD-deelname te configureren. De wizard vereenvoudigt het configuratie proces aanzienlijk. Met de wizard wordt de service verbindings punten (Scp's) voor apparaatregistratie geconfigureerd.

De configuratie stappen in dit artikel zijn gebaseerd op het gebruik van de wizard in Azure AD Connect.

Hybride Azure AD-deelname vereist dat apparaten toegang hebben tot de volgende micro soft-resources vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (als u naadloze SSO gebruikt of wilt gebruiken)

Als uw organisatie toegang tot internet via een uitgaande proxy vereist, raadt micro soft aan om [Web Proxy Auto-Discovery (WPAD) te implementeren](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) om Windows 10-computers in te scha kelen voor apparaatregistratie met Azure AD. Als u problemen ondervindt met het configureren en beheren van WPAD, raadpleegt u [problemen met automatische detectie oplossen](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Als u geen gebruik maakt van WPAD en proxy-instellingen op uw computer moet configureren, kunt u dit doen vanaf Windows 10 1709. Zie [WinHTTP-instellingen configureren met behulp van een groeps beleidsobject (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)voor meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met behulp van WinHTTP-instellingen, kunnen computers die geen verbinding kunnen maken met de geconfigureerde proxy, geen verbinding maken met internet.

Als uw organisatie toegang tot internet via een geverifieerde uitgaande proxy vereist, moet u ervoor zorgen dat uw Windows 10-computers kunnen worden geverifieerd bij de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie worden uitgevoerd met behulp van machine context, moet u uitgaande proxy verificatie configureren met behulp van machine context. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Als u wilt controleren of het apparaat toegang heeft tot de bovenstaande micro soft-resources onder het systeem account, kunt u het verbindings script voor het [registreren van apparaten](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) gebruiken.

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Voor het configureren van een hybride Azure AD-koppeling via Azure AD Connect hebt u het volgende nodig:

- De referenties van een globale beheerder voor uw Azure AD-Tenant
- De referenties van de ondernemings beheerder voor elk forest

**Een hybride Azure AD-deelname configureren met behulp van Azure AD Connect:**

1. Start Azure AD Connect en selecteer vervolgens **configureren**.

   ![Welkom](./media/hybrid-azuread-join-managed-domains/11.png)

1. Selecteer op de pagina **extra taken** de optie **Apparaatinstellingen configureren**en selecteer vervolgens **volgende**.

   ![Extra taken](./media/hybrid-azuread-join-managed-domains/12.png)

1. Selecteer op de pagina **overzicht** de optie **volgende**.

   ![Overzicht](./media/hybrid-azuread-join-managed-domains/13.png)

1. Voer op de pagina **Verbinding maken met Azure AD** de referenties in van een hoofdbeheerder voor uw Azure AD-tenant.  

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. Selecteer **hybride Azure AD-deelname configureren**op de pagina **apparaat opties** en selecteer **volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-managed-domains/15.png)

1. Voer de volgende stappen uit op de **SCP** -pagina voor elk forest waar u Azure AD Connect wilt configureren van het SCP, en selecteer vervolgens **volgende**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Selecteer de forest.
   1. Selecteer de verificatieservice.
   1. Selecteer **toevoegen** om de referenties voor de ondernemings beheerder in te voeren.

1. Selecteer op de pagina **besturings systemen voor apparaten** de besturings systemen die worden gebruikt in uw Active Directory omgeving en selecteer vervolgens **volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-managed-domains/17.png)

1. Selecteer **configureren**op de pagina **gereed voor configuratie** .

   ![Gereed voor configuratie](./media/hybrid-azuread-join-managed-domains/19.png)

1. Selecteer op de pagina **configuratie voltooid** de optie **Afsluiten**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows-down level apparaten inschakelen

Als sommige apparaten die lid zijn van het domein, Windows down level-apparaten zijn, moet u:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Naadloze SSO configureren
- Micro soft Workplace Join installeren voor Windows-down level computers

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. [Ondersteuning voor Windows 7 is beëindigd](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)voor meer informatie.

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Als u de hybride Azure AD join van uw Windows down level-apparaten wilt volt ooien en certificaat prompts wilt voor komen wanneer apparaten worden geverifieerd bij Azure AD, kunt u een beleid naar uw aan het domein gekoppelde apparaten pushen om de volgende Url's toe te voegen aan de zone Lokaal intranet in Internet Nerve

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

U moet ook **toestaan dat updates voor de status balk via script worden** ingeschakeld in de lokale intranet zone van de gebruiker.

### <a name="configure-seamless-sso"></a>Naadloze SSO configureren

U moet ook [naadloze SSO configureren](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature)om de hybride Azure AD-deelname van uw Windows down level-apparaten in een beheerd domein dat gebruikmaakt van [PHS](../hybrid/whatis-phs.md) of [PTA](../hybrid/how-to-connect-pta.md) als uw Azure AD-Cloud authenticatie methode, te kunnen volt ooien.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Micro soft Workplace Join installeren voor Windows-down level computers

Voor het registreren van Windows down level-apparaten moeten organisaties [micro soft Workplace join installeren voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554). Micro soft Workplace Join voor niet-Windows 10-computers is beschikbaar in het micro soft Download centrum.

U kunt het pakket implementeren met behulp van een software distributiesysteem als [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Het pakket ondersteunt de standaard opties voor installatie op de achtergrond met de para meter `quiet`. De huidige vertakking van Configuration Manager biedt voor delen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatie programma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikers context. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt op de achtergrond met Azure AD toegevoegd aan het apparaat met behulp van de referenties van de gebruiker na verificatie met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Als u de registratie status van het apparaat in uw Azure-Tenant wilt controleren, kunt u de cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** in de [Power shell-module Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)gebruiken.

Wanneer u de cmdlet **Get-MSolDevice** gebruikt om de service details te controleren:

- Er moet een object zijn met de **apparaat-id** die overeenkomt met de id op de Windows-client.
- Moet de waarde voor **DeviceTrustType** op **Toegevoegd aan domein** zijn ingesteld. Deze instelling komt overeen met de **hybride Azure AD join** -status op de pagina **apparaten** in de Azure AD-Portal.
- Voor apparaten die in voorwaardelijke toegang worden gebruikt, moet de waarde voor **ingeschakeld zijn ingesteld** op **True** en moet **DeviceTrustLevel** worden **beheerd**.

**Details van de service controleren**:

1. Open Windows PowerShell als administrator.
1. Voer `Connect-MsolService` in om verbinding te maken met uw Azure-Tenant.  
1. Voer `get-msoldevice -deviceId <deviceId>` in.
1. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Als u problemen ondervindt met het volt ooien van hybride Azure AD join voor Windows-apparaten die lid zijn van een domein, raadpleegt u:

- [Problemen met hybride Azure AD-deelname voor Windows-huidige apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure AD-deelname voor Windows down level-apparaten oplossen](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van apparaat-id's met behulp van de Azure Portal](device-management-azure-portal.md).
