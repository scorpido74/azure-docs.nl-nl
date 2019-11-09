---
title: Hybride Azure Active Directory-deelname configureren voor federatieve domeinen | Microsoft Docs
description: Hybride Azure Active Directory-koppeling configureren voor federatieve domeinen.
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
ms.openlocfilehash: 14565c7e499b04b9c41184111d6ddcc88fffac80
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883012"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Zelfstudie: Hybride Azure Active Directory-koppeling configureren voor federatieve domeinen

Net als bij een gebruiker in uw organisatie is een apparaat een kern identiteit die u wilt beveiligen. U kunt de identiteit van een apparaat gebruiken om uw resources te beschermen op elk gewenst moment en vanaf elke locatie. U kunt dit doel doen door de apparaat-id's te halen en te beheren in Azure Active Directory (Azure AD) met een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Als u uw apparaten naar Azure AD brengt, wordt de gebruikers productiviteit via eenmalige aanmelding (SSO) in de Cloud en on-premises resources gemaximaliseerd. U kunt de toegang tot uw Cloud en on-premises resources met [voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) op hetzelfde moment beveiligen.

Een gefedereerde omgeving moet een id-provider hebben die de volgende vereisten ondersteunt. Als u een gefedereerde omgeving hebt met Active Directory Federation Services (AD FS), worden de onderstaande vereisten al ondersteund.

- **WIAORMULTIAUTHN claim:** Deze claim is vereist voor het uitvoeren van hybride Azure AD-deelname voor Windows-apparaten op lagere niveaus.
- **WS-Trust-Protocol:** Dit protocol is vereist voor de verificatie van Windows Current Hybrid Azure AD gekoppelde apparaten met Azure AD.
  Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eind punten inschakelen: `/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> **ADFS/Services/Trust/2005/windowstransport** of **ADFS/Services/Trust/13/windowstransport** moeten alleen worden ingeschakeld als intranet gerichte eind punten en mogen niet worden weer gegeven als een extranet gerichte eind punten via de Web Application proxy. Zie [Windows-eind punten van WS-Trust uitschakelen op de proxy](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)voor meer informatie over het uitschakelen van WS-Trust Windows-eind punten. Onder **Service** > **Eindpunten** in de AD FS-beheerconsole kunt u zien welke eindpunten zijn ingeschakeld.

In deze zelf studie leert u hoe u hybride Azure AD join kunt configureren voor Active Directory computers die lid zijn van een domein in een federatieve omgeving door gebruik te maken van AD FS.

In deze zelfstudie leert u procedures om het volgende te doen:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Windows-down level apparaten inschakelen
> * De registratie verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

In deze zelf studie wordt ervan uitgegaan dat you'e die bekend zijn met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Uw hybride implementatie van Azure AD-deelname plannen](hybrid-azuread-join-plan.md)
- [Hoe wordt de validatie van hybride Azure AD-deelname gecontroleerd](hybrid-azuread-join-control.md)

Voor het configureren van het scenario in deze zelfstudie hebt u het volgende nodig:

- Windows Server 2012 R2 met AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) versie 1.1.819.0 of hoger

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard die u kunt gebruiken om hybride Azure AD-deelname te configureren. De wizard vereenvoudigt het configuratie proces aanzienlijk. De gerelateerde wizard:

- Hiermee configureert u de service verbindings punten (Scp's) voor apparaatregistratie
- Maakt een back-up van uw bestaande vertrouwensrelatie van de Relying Party van Azure AD
- Werkt de claimregels in uw Azure AD-vertrouwensrelatie bij

De configuratie stappen in dit artikel zijn gebaseerd op het gebruik van de Azure AD Connect wizard. Als u een eerdere versie van Azure AD Connect hebt geïnstalleerd, moet u deze upgraden naar 1.1.819 of hoger om de wizard te kunnen gebruiken. Zie [How to configure Hybrid Azure AD join's](hybrid-azuread-join-manual.md)als u de nieuwste versie van Azure AD Connect niet kunt installeren.

Hybride Azure AD-deelname vereist dat apparaten toegang hebben tot de volgende micro soft-resources vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- De beveiligings token service (STS) van uw organisatie (voor federatieve domeinen)
- `https://autologon.microsoftazuread-sso.com` (als u naadloze SSO gebruikt of wilt gebruiken)

Met ingang van Windows 10 1803, als de direct hybride Azure AD-deelname voor een gefedereerde omgeving door het gebruik van AD FS mislukt, vertrouwen we op Azure AD Connect om het computer object in azure AD te synchroniseren dat vervolgens wordt gebruikt om de apparaatregistratie voor hybride Azure te volt ooien AD-deelname. Controleer of Azure AD Connect de computer objecten hebt gesynchroniseerd van de apparaten met wie u een hybride Azure AD wilt maken en lid wilt worden van Azure AD. Als de computer objecten deel uitmaken van specifieke organisatie-eenheden (Ou's), moet u ook de Ou's configureren om te synchroniseren in Azure AD Connect. Zie voor meer informatie over het synchroniseren van computer objecten met behulp van Azure AD Connect [filters configureren met behulp van Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Als uw organisatie toegang tot internet via een uitgaande proxy vereist, raadt micro soft aan om [Web Proxy Auto-Discovery (WPAD) te implementeren](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) om Windows 10-computers in te scha kelen voor apparaatregistratie met Azure AD. Als u problemen ondervindt met het configureren en beheren van WPAD, raadpleegt u [problemen met automatische detectie oplossen](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Als u geen gebruik maakt van WPAD en proxy-instellingen op uw computer wilt configureren, kunt u dit doen vanaf Windows 10 1709. Zie [WinHTTP-instellingen configureren met behulp van een groeps beleidsobject (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)voor meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met behulp van WinHTTP-instellingen, kunnen computers die geen verbinding kunnen maken met de geconfigureerde proxy, geen verbinding maken met internet.

Als uw organisatie toegang tot internet via een geverifieerde uitgaande proxy vereist, moet u ervoor zorgen dat uw Windows 10-computers kunnen worden geverifieerd bij de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie worden uitgevoerd met behulp van machine context, moet u uitgaande proxy verificatie configureren met behulp van machine context. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Als u wilt controleren of het apparaat toegang heeft tot de bovenstaande micro soft-resources onder het systeem account, kunt u het verbindings script voor het [registreren van apparaten](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) gebruiken.

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Als u een hybride Azure AD-deelname wilt configureren met behulp van Azure AD Connect, hebt u het volgende nodig:

- De referenties van een globale beheerder voor uw Azure AD-Tenant  
- De referenties van de ondernemings beheerder voor elk forest
- De referenties van uw AD FS-beheerder

**Een hybride Azure AD-deelname configureren met behulp van Azure AD Connect**:

1. Start Azure AD Connect en selecteer vervolgens **configureren**.

   ![Welkom](./media/hybrid-azuread-join-federated-domains/11.png)

1. Selecteer op de pagina **extra taken** de optie **Apparaatinstellingen configureren**en selecteer vervolgens **volgende**.

   ![Extra taken](./media/hybrid-azuread-join-federated-domains/12.png)

1. Selecteer op de pagina **overzicht** de optie **volgende**.

   ![Overzicht](./media/hybrid-azuread-join-federated-domains/13.png)

1. Op de pagina **verbinding maken met Azure AD** voert u de referenties in van een globale beheerder voor uw Azure AD-Tenant en selecteert u vervolgens **volgende**.

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Selecteer **hybride Azure AD-deelname configureren**op de pagina **apparaat opties** en selecteer **volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-federated-domains/15.png)

1. Voer op de **SCP** -pagina de volgende stappen uit en selecteer **volgende**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecteer de forest.
   1. Selecteer de verificatieservice. U moet **AD FS server** selecteren, tenzij uw organisatie uitsluitend Windows 10-clients heeft en u de synchronisatie van computer/apparaat hebt geconfigureerd, of als uw organisatie gebruikmaakt van naadloze SSO.
   1. Selecteer **toevoegen** om de referenties voor de ondernemings beheerder in te voeren.

1. Selecteer op de pagina **besturings systemen voor apparaten** de besturings systemen die door de apparaten in uw Active Directory omgeving worden gebruikt en selecteer **volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-federated-domains/17.png)

1. Voer op de pagina **Federatie configuratie** de referenties van uw AD FS-beheerder in en selecteer vervolgens **volgende**.

   ![Federatieconfiguratie](./media/hybrid-azuread-join-federated-domains/18.png)

1. Selecteer **configureren**op de pagina **gereed voor configuratie** .

   ![Klaar om te configureren](./media/hybrid-azuread-join-federated-domains/19.png)

1. Selecteer op de pagina **configuratie voltooid** de optie **Afsluiten**.

   ![Configuratie voltooid](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows-down level apparaten inschakelen

Als sommige apparaten die lid zijn van het domein, Windows down level-apparaten zijn, moet u:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Micro soft Workplace Join installeren voor Windows-down level computers

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Als u de hybride Azure AD join van uw Windows down level-apparaten wilt volt ooien en certificaat prompts wilt voor komen wanneer apparaten worden geverifieerd bij Azure AD, kunt u een beleid naar uw aan het domein gekoppelde apparaten pushen om de volgende Url's toe te voegen aan de zone Lokaal intranet in Internet Nerve

- `https://device.login.microsoftonline.com`
- De STS van uw organisatie (voor federatieve domeinen)
- `https://autologon.microsoftazuread-sso.com` (voor naadloze SSO)

U moet ook **toestaan dat updates voor de status balk via script worden** ingeschakeld in de lokale intranet zone van de gebruiker.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Micro soft Workplace Join installeren voor Windows-down level computers

Voor het registreren van Windows down level-apparaten moeten organisaties [micro soft Workplace join installeren voor niet-Windows 10-computers](https://www.microsoft.com/download/details.aspx?id=53554). Micro soft Workplace Join voor niet-Windows 10-computers is beschikbaar in het micro soft Download centrum.

U kunt het pakket implementeren met behulp van een software distributiesysteem als [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Het pakket ondersteunt de standaard opties voor installatie op de achtergrond met de para meter `quiet`. De huidige vertakking van Configuration Manager biedt voor delen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatie programma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikers context. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak wordt op de achtergrond met Azure AD toegevoegd aan het apparaat met behulp van de referenties van de gebruiker na verificatie met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Als u de registratie status van het apparaat in uw Azure-Tenant wilt controleren, kunt u de cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** in de [Power shell-module Azure Active Directory](/powershell/azure/install-msonlinev1?view=azureadps-2.0)gebruiken.

Wanneer u de cmdlet **Get-MSolDevice** gebruikt om de service details te controleren:

- Er moet een object zijn met de **apparaat-id** die overeenkomt met de id op de Windows-client.
- Moet de waarde voor **DeviceTrustType** op **Toegevoegd aan domein** zijn ingesteld. Deze instelling komt overeen met de **Hybrid Azure AD join** -status onder **apparaten** in de Azure AD-Portal.
- Voor apparaten die in voorwaardelijke toegang worden gebruikt, moet de waarde voor **ingeschakeld zijn ingesteld** op **True** en moet **DeviceTrustLevel** worden **beheerd**.

**Details van de service controleren**:

1. Open Windows Power shell als Administrator.
1. Voer `Connect-MsolService` in om verbinding te maken met uw Azure-Tenant.  
1. Voer `get-msoldevice -deviceId <deviceId>` in.
1. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Als u problemen ondervindt met het volt ooien van hybride Azure AD join voor Windows-apparaten die lid zijn van een domein, raadpleegt u:

- [Problemen met hybride Azure AD-deelname voor Windows-huidige apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure AD-deelname voor Windows down level-apparaten oplossen](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het beheren van apparaat-id's met behulp van de Azure Portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
