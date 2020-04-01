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
ms.openlocfilehash: 1a61c89199c89f09b5cc0e553dbbf48655ad1b6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239096"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Zelfstudie: Hybride Azure Active Directory-koppeling configureren voor federatieve domeinen

Net als een gebruiker in uw organisatie is een apparaat een kernidentiteit die u wilt beschermen. U de identiteit van een apparaat gebruiken om uw resources op elk gewenst moment en vanaf elke locatie te beschermen. U dit doel bereiken door apparaatidentiteiten aan te brengen en deze te beheren in Azure Active Directory (Azure AD) met behulp van een van de volgende methoden:

- Azure AD-koppeling
- Hybride Azure AD-koppeling
- Azure AD-registratie

Als u uw apparaten naar Azure AD brengt, wordt de productiviteit van gebruikers verhoogd via single sign-on (SSO) in uw cloud en on-premises resources. U tegelijkertijd toegang tot uw cloud- en on-premises resources beveiligen met [Voorwaardelijke toegang.](../active-directory-conditional-access-azure-portal.md)

Een federatieve omgeving moet een identiteitsprovider hebben die de volgende vereisten ondersteunt. Als u een federatieve omgeving hebt met Active Directory Federation Services (AD FS), worden de onderstaande vereisten al ondersteund.

- **WIAORMULTIAUTHN claim:** Deze claim is vereist voor hybride Azure AD join voor Windows down-level apparaten.
- **WS-Trust-protocol:** Dit protocol is vereist om windows huidige hybride Azure AD-apparaten te verifiëren met Azure AD.
  Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eindpunten inschakelen:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Zowel **adfs/services/trust/2005/windowstransport** als **adfs/services/trust/13/windowstransport** moeten alleen worden ingeschakeld als intranet gerichte eindpunten en mogen NIET worden blootgesteld als extranetgerichte eindpunten via de Web Application Proxy. Zie [Windows-eindpunten voor WS-Trust uitschakelen op de proxy](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet)voor meer informatie over het uitschakelen van Windows-eindpunten voor WS-Trust. U zien welke eindpunten zijn ingeschakeld via de AD FS-beheerconsole onder > **Serviceeindpunten**. **Service**

In deze zelfstudie leert u hoe u hybride Azure AD-join configureren voor Active Directory-computers die zijn verbonden met active directory-bestanden in een federatieve omgeving met behulp van AD FS.

Procedures voor:

> [!div class="checklist"]
> * Hybride Azure AD-koppeling configureren
> * Windows downlevel-apparaten inschakelen
> * De registratie verifiëren
> * Problemen oplossen

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u bekend bent met deze artikelen:

- [Wat is een apparaat-id?](overview.md)
- [Uw hybride Azure AD-implementatie plannen](hybrid-azuread-join-plan.md)
- [Gecontroleerde validatie van hybride Azure AD-join uitvoeren](hybrid-azuread-join-control.md)

Voor het configureren van het scenario in deze zelfstudie hebt u het volgende nodig:

- Windows Server 2012 R2 met AD FS
- [Azure AD Connect-versie](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 of hoger

Vanaf versie 1.1.819.0 bevat Azure AD Connect een wizard die u gebruiken om hybride Azure AD-verbinding te configureren. De wizard vereenvoudigt het configuratieproces aanzienlijk. De gerelateerde wizard:

- Configureert de serviceverbindingspunten (SCP's) voor apparaatregistratie
- Maakt een back-up van uw bestaande vertrouwensrelatie van de Relying Party van Azure AD
- Werkt de claimregels in uw Azure AD-vertrouwensrelatie bij

De configuratiestappen in dit artikel zijn gebaseerd op het gebruik van de wizard Azure AD Connect. Als u een eerdere versie van Azure AD Connect hebt geïnstalleerd, moet u deze upgraden naar 1.1.819 of hoger om de wizard te gebruiken. Als het installeren van de nieuwste versie van Azure AD Connect geen optie voor u is, raadpleegt u hoe u [hybride Azure AD-join handmatig configureert.](hybrid-azuread-join-manual.md)

Voor hybride Azure AD-join moeten apparaten toegang hebben tot de volgende Microsoft-bronnen vanuit het netwerk van uw organisatie:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- StS (Security Token Service) van uw organisatie (Voor federatieve domeinen)
- `https://autologon.microsoftazuread-sso.com`(Als u naadloze SSO gebruikt of van plan bent te gebruiken)

Als de onmiddellijk hybride Azure AD lid wordt voor een federatieve omgeving met AD FS mislukt, vertrouwen we op Azure AD Connect om het computerobject in Azure AD te synchroniseren dat vervolgens wordt gebruikt om de apparaatregistratie voor hybride Azure AD-join te voltooien. Controleer of Azure AD Connect de computerobjecten heeft gesynchroniseerd van de apparaten waarvan u hybride Azure AD wilt zijn, is verbonden met Azure AD. Als de computerobjecten tot specifieke organisatie-eenheden (OE's) behoren, moet u de Ok-instellingen ook configureren om te synchroniseren in Azure AD Connect. Zie [Filteren configureren met Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)voor meer informatie over het synchroniseren van computerobjecten met Azure AD Connect.

Als uw organisatie toegang tot internet nodig heeft via een uitgaande proxy, raadt Microsoft aan [om Web Proxy Auto-Discovery (WPAD)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) te implementeren om Windows 10-computers in te schakelen voor apparaatregistratie bij Azure AD. Zie Problemen met [automatische detectie oplossen](/previous-versions/tn-archive/cc302643(v=technet.10))als u problemen ondervindt bij het configureren en beheren van WPAD. 

Als u WPAD niet gebruikt en proxy-instellingen op uw computer wilt configureren, u dit doen vanaf Windows 10 1709. Zie [WinHTTP-instellingen configureren met een groepsbeleidsobject (GPO) voor](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)meer informatie.

> [!NOTE]
> Als u proxy-instellingen op uw computer configureert met winhttp-instellingen, kunnen computers die geen verbinding kunnen maken met de geconfigureerde proxy geen verbinding maken met internet.

Als uw organisatie toegang tot internet nodig heeft via een geverifieerde uitgaande proxy, moet u ervoor zorgen dat uw Windows 10-computers met succes kunnen verifiëren naar de uitgaande proxy. Omdat Windows 10-computers apparaatregistratie uitvoeren met behulp van machinecontext, moet u uitgaande proxyverificatie configureren met behulp van machinecontext. Vraag uw provider van de uitgaande proxy naar de configuratievereisten.

Als u wilt controleren of het apparaat toegang heeft tot de bovenstaande Microsoft-bronnen onder het systeemaccount, u het script [Voor apparaatregistratieregistratie](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) testen gebruiken.

## <a name="configure-hybrid-azure-ad-join"></a>Hybride Azure AD-koppeling configureren

Als u een hybride Azure AD-verbinding wilt configureren met Azure AD Connect, hebt u het:

- De referenties van een globale beheerder voor uw Azure AD-tenant  
- De referenties van de bedrijfsbeheerder voor elk van de forests
- De referenties van uw AD FS-beheerder

**Een hybride Azure AD-lid configureren met Azure AD Connect:**

1. Start Azure AD Connect en selecteer **Configureren**.

   ![Welkom](./media/hybrid-azuread-join-federated-domains/11.png)

1. Selecteer op de pagina **Extra taken** de optie **Apparaatopties configureren**en selecteer **Volgende**.

   ![Extra taken](./media/hybrid-azuread-join-federated-domains/12.png)

1. Selecteer **Volgende**op de pagina **Overzicht** .

   ![Overzicht](./media/hybrid-azuread-join-federated-domains/13.png)

1. Voer op de pagina **Verbinding maken met Azure AD** de referenties in van een globale beheerder voor uw Azure AD-tenant en selecteer **Volgende**.

   ![Verbinding maken met Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. Selecteer op de pagina **Apparaatopties** de optie **Hybride Azure AD-verbinding configureren**en selecteer **Volgende**.

   ![Apparaatopties](./media/hybrid-azuread-join-federated-domains/15.png)

1. Voer op de **SCP-pagina** de volgende stappen uit en selecteer **Volgende:**

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Selecteer de forest.
   1. Selecteer de verificatieservice. U moet **AD FS-server** selecteren, tenzij uw organisatie uitsluitend Windows 10-clients heeft en u de synchronisatie van computers/apparaten hebt geconfigureerd of uw organisatie naadloze SSO gebruikt.
   1. Selecteer **Toevoegen** om de referenties van de bedrijfsbeheerder in te voeren.

1. Selecteer op de pagina **Apparaatbesturingssystemen** de besturingssystemen die de apparaten in uw Active Directory-omgeving gebruiken en selecteer **Volgende**.

   ![Apparaatbesturingssysteem](./media/hybrid-azuread-join-federated-domains/17.png)

1. Voer op de pagina **Federatieconfiguratie** de referenties van uw AD FS-beheerder in en selecteer **Volgende**.

   ![Federatieconfiguratie](./media/hybrid-azuread-join-federated-domains/18.png)

1. Selecteer op de pagina **Gereed om te configureren** de optie **Configureren**.

   ![Gereed voor configuratie](./media/hybrid-azuread-join-federated-domains/19.png)

1. Selecteer **Afsluiten**op de pagina **Configuratie voltooid** .

   ![Configuratie voltooid](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Windows downlevel-apparaten inschakelen

Als sommige van uw apparaten met domeinlid windows-downlevel-apparaten zijn, moet u het:

- De lokale intranetinstellingen voor apparaatregistratie configureren
- Microsoft Workplace Join voor Windows-downlevelcomputers installeren

> [!NOTE]
> Windows 7-ondersteuning is beëindigd op 14 januari 2020. Voor meer informatie [is de ondersteuning voor Windows 7 beëindigd.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>De lokale intranetinstellingen voor apparaatregistratie configureren

Als u de hybride Azure AD-verbinding met uw Windows-downlevel-apparaten wilt voltooien en certificaatprompts wilt voorkomen wanneer apparaten zich verifiëren bij Azure AD, u een beleid naar uw met een domein verbonden apparaten pushen om de volgende URL's toe te voegen aan de lokale intranetzone in Internet Explorer:

- `https://device.login.microsoftonline.com`
- STS van uw organisatie (Voor federatieve domeinen)
- `https://autologon.microsoftazuread-sso.com`(Voor naadloze SSO)

U moet ook **Updates toestaan voor de statusbalk inschakelen via het script** in de lokale intranetzone van de gebruiker.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Microsoft Workplace Join voor Windows-downlevelcomputers installeren

Als u apparaten met downlevel van Windows wilt registreren, moeten organisaties Microsoft Workplace Join installeren [voor niet-Windows 10-computers.](https://www.microsoft.com/download/details.aspx?id=53554) Microsoft Workplace Join voor niet-Windows 10-computers is beschikbaar in het Microsoft Download centrum.

U het pakket implementeren met behulp van een softwaredistributiesysteem zoals [Microsoft Endpoint Configuration Manager.](/configmgr/) Het pakket ondersteunt de standaard `quiet` stille installatieopties met de parameter. De huidige tak van Configuration Manager biedt voordelen ten opzichte van eerdere versies, zoals de mogelijkheid om voltooide registraties bij te houden.

Het installatieprogramma maakt een geplande taak op het systeem dat wordt uitgevoerd in de gebruikerscontext. De taak wordt geactiveerd wanneer de gebruiker zich aanmeldt bij Windows. De taak voegt het apparaat in stilte samen met Azure AD door de gebruikersreferenties te gebruiken nadat deze is geverifieerd met Azure AD.

## <a name="verify-the-registration"></a>De registratie verifiëren

Om de status van de apparaatregistratie in uw Azure-tenant te verifiëren, kunt u de cmdlet **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** in de [Azure Active Directory PowerShell-module](/powershell/azure/install-msonlinev1?view=azureadps-2.0) gebruiken.

Wanneer u de **cmdlet Get-MSolDevice** gebruikt om de servicegegevens te controleren:

- Er moet een object met de **apparaat-id** bestaan dat overeenkomt met de id op de Windows-client.
- Moet de waarde voor **DeviceTrustType** op **Toegevoegd aan domein** zijn ingesteld. Deze instelling is gelijk aan de **hybride Azure AD-status** onder **Apparaten** in de Azure AD-portal.
- Voor apparaten die worden gebruikt in Voorwaardelijke toegang, moet de waarde voor **Ingeschakeld** **True** zijn en **moet DeviceTrustLevel** worden **beheerd.**

**Om de servicegegevens te controleren:**

1. Open Windows PowerShell als administrator.
1. Voer `Connect-MsolService` deze in om verbinding te maken met uw Azure-tenant.  
1. Voer `get-msoldevice -deviceId <deviceId>` in.
1. Verifieer dat **Ingeschakeld** is ingesteld op **Waar**.

## <a name="troubleshoot-your-implementation"></a>Problemen met uw implementatie oplossen

Zie als u problemen ondervindt bij het voltooien van hybride Azure AD voor Windows-apparaten met een domein:

- [Problemen met hybride Azure AD-join voor windows-huidige apparaten oplossen](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure AD-join voor Apparaten met downlevel van Windows oplossen](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beheren van apparaatidentiteiten met behulp van de Azure-portal.](device-management-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
