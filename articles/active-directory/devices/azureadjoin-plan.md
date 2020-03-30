---
title: Uw Azure Active Directory-join-implementatie plannen
description: Hiermee worden de stappen uitgelegd die nodig zijn om aazure AD-apparaten in uw omgeving te implementeren.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672699"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>How to: Uw Azure AD-implementatie plannen

Met Azure AD-join u apparaten rechtstreeks aansluiten bij Azure AD zonder dat u hoeft deel te nemen aan on-premises Active Directory terwijl u uw gebruikers productief en veilig houdt. Azure AD join is geschikt voor zowel op schaal als scoped implementaties.   

In dit artikel vindt u de informatie die u nodig hebt om uw Azure AD-implementatie te plannen.
 
## <a name="prerequisites"></a>Vereisten

In dit artikel wordt ervan uitgegaan dat u bekend bent met de [inleiding tot apparaatbeheer in Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Uw implementatie plannen

Als u uw Azure AD-join-implementatie wilt plannen, moet u vertrouwd raken met:

|   |   |
|---|---|
|![Selecteren][1]|Bekijk uw scenario's|
|![Selecteren][1]|Uw identiteitsinfrastructuur bekijken|
|![Selecteren][1]|Uw apparaatbeheer beoordelen|
|![Selecteren][1]|Inzicht in overwegingen voor toepassingen en resources|
|![Selecteren][1]|Inzicht in uw inrichtingsopties|
|![Selecteren][1]|Roaming voor ondernemingsstatus configureren|
|![Selecteren][1]|Voorwaardelijke toegang configureren|

## <a name="review-your-scenarios"></a>Bekijk uw scenario's 

Hoewel Hybride Azure AD-lid kan worden gekoppeld voor bepaalde scenario's, stelt Azure AD Join u in staat om over te schakelen naar een cloud-first-model met Windows. Als u van plan bent uw apparaatbeheer te moderniseren en de it-kosten voor apparaten te verlagen, biedt Azure AD join een geweldige basis om deze doelstellingen te bereiken.  
 
U moet azure AD overwegen om lid te worden als uw doelen overeenkomen met de volgende criteria:

- U gebruikt Microsoft 365 als productiviteitssuite voor uw gebruikers.
- U wilt apparaten beheren met een cloudapparaatbeheeroplossing.
- U wilt de inrichting van apparaten voor geografisch gedistribueerde gebruikers vereenvoudigen.
- U bent van plan uw applicatie-infrastructuur te moderniseren.

## <a name="review-your-identity-infrastructure"></a>Uw identiteitsinfrastructuur bekijken  

Azure AD join werkt met zowel beheerde als federatieve omgevingen.  

### <a name="managed-environment"></a>Beheerde omgeving

Een beheerde omgeving kan worden geïmplementeerd via [Password Hash Sync](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) of Pass Through [Authentication](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) met Seamless Single Sign On.

Voor deze scenario's hoeft u geen federatieserver te configureren voor verificatie.

### <a name="federated-environment"></a>Federatieve omgeving

Een federatieve omgeving moet een identiteitsprovider hebben die zowel WS-Trust- als WS-Fed-protocollen ondersteunt:

- **WS-Fed:** Dit protocol is vereist om een apparaat te koppelen aan Azure AD.
- **WS-Trust:** Dit protocol is vereist om u aan te melden bij een Azure AD-apparaat.

Wanneer u AD FS gebruikt, moet u de volgende WS-Trust-eindpunten inschakelen:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Als uw identiteitsprovider deze protocollen niet ondersteunt, werkt Azure AD-lid niet native. 

>[!NOTE]
> Momenteel werkt Azure AD join niet met [AD FS 2019 geconfigureerd met externe verificatieproviders als de primaire verificatiemethode.](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary) Azure AD sluit standaardinstellingen samen met wachtwoordverificatie als de primaire methode, wat resulteert in verificatiefouten in dit scenario


### <a name="smartcards-and-certificate-based-authentication"></a>Smartcards en verificatie op basis van certificaten

U geen smartcards of verificatie op basis van certificaten gebruiken om apparaten aan te sluiten bij Azure AD. Smartcards kunnen echter worden gebruikt om u aan te melden bij azure AD-apparaten als ad FS is geconfigureerd.

**Aanbeveling:** Implementeer Windows Hello for Business voor sterke, wachtwoordloze verificatie op Windows 10-apparaten.

### <a name="user-configuration"></a>Gebruikersconfiguratie

Als u gebruikers maakt in uw:

- **On-premises Active Directory**, u moet ze synchroniseren met Azure AD met [Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, er is geen extra installatie vereist.

On-premises UPN's die afwijken van Azure AD UPN's worden niet ondersteund op azure AD-apparaten. Als uw gebruikers een on-premises UPN gebruiken, moet u van plan zijn over te schakelen naar het gebruik van hun primaire UPN in Azure AD.

## <a name="assess-your-device-management"></a>Uw apparaatbeheer beoordelen

### <a name="supported-devices"></a>Ondersteunde apparaten

Azure AD wordt lid van:

- Is alleen van toepassing op Windows 10-apparaten. 
- Is niet van toepassing op eerdere versies van Windows of andere besturingssystemen. Als u Windows 7/8.1-apparaten hebt, moet u upgraden naar Windows 10 om Azure AD-lid te implementeren.
- Wordt niet ondersteund op apparaten met TPM in FIPS-modus.
 
**Aanbeveling:** Gebruik altijd de nieuwste Windows 10-release om te profiteren van bijgewerkte functies.

### <a name="management-platform"></a>Beheerplatform

Apparaatbeheer voor Azure AD-samengevoegde apparaten is gebaseerd op een MDM-platform zoals Intune en MDM-CSP's. Windows 10 heeft een ingebouwde MDM-agent die werkt met alle compatibele MDM-oplossingen.

> [!NOTE]
> Groepsbeleid wordt niet ondersteund in Azure AD-apparaten die zijn samengevoegd omdat ze niet zijn verbonden met on-premises Active Directory. Beheer van Azure AD-samengevoegde apparaten is alleen mogelijk via MDM

Er zijn twee benaderingen voor het beheren van azure AD-apparaten:

- **MDM-only** - Een apparaat wordt exclusief beheerd door een MDM-provider zoals Intune. Alle beleidsregels worden geleverd als onderdeel van het MDM-inschrijvingsproces. Voor Azure AD Premium- of EMS-klanten is MDM-inschrijving een geautomatiseerde stap die deel uitmaakt van een Azure AD-deelname.
- **Co-management** - Een apparaat wordt beheerd door een MDM-provider en SCCM. In deze benadering wordt de SCCM-agent geïnstalleerd op een MDM-beheerd apparaat om bepaalde aspecten te beheren.

Als u groepsbeleid gebruikt, evalueert u de pariteit van uw MDM-beleid met behulp van het [MDM Migration Analysis Tool (MMAT).](https://github.com/WindowsDeviceManagement/MMAT) 

Bekijk ondersteund en niet-ondersteund beleid om te bepalen of u een MDM-oplossing gebruiken in plaats van groepsbeleid. Voor niet-ondersteunde beleidsregels moet u het volgende overwegen:

- Zijn de niet-ondersteunde beleidsregels die nodig zijn voor Azure AD-apparaten of -gebruikers?
- Is het niet-ondersteunde beleid van toepassing in een cloudgestuurde implementatie?

Als uw MDM-oplossing niet beschikbaar is via de azure AD-app-galerie, u deze toevoegen volgens het proces dat is beschreven in [Azure Active Directory-integratie met MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Door middel van co-management u SCCM gebruiken om bepaalde aspecten van uw apparaten te beheren terwijl het beleid wordt geleverd via uw MDM-platform. Microsoft Intune maakt co-management met SCCM mogelijk. Zie [Wat is co-management voor co-management](/configmgr/core/clients/manage/co-management-overview)voor Windows 10-apparaten? Als u een ander MDM-product dan Intune gebruikt, neem dan contact op met uw MDM-provider over toepasselijke co-managementscenario's.

**Aanbeveling:** Overweeg MDM alleen beheer voor Azure AD samengevoegde apparaten.

## <a name="understand-considerations-for-applications-and-resources"></a>Inzicht in overwegingen voor toepassingen en resources

We raden u aan toepassingen te migreren van on-premises naar de cloud voor een betere gebruikerservaring en toegangscontrole. Azure AD-apparaten kunnen echter naadloos toegang bieden tot zowel on-premises als cloudtoepassingen. Zie [Hoe SSO-resources werken op Azure AD-apparaten voor](azuread-join-sso.md)meer informatie.

In de volgende secties worden overwegingen voor verschillende typen toepassingen en resources vermeld.

### <a name="cloud-based-applications"></a>Cloudgebaseerde toepassingen

Als een toepassing wordt toegevoegd aan de azure AD-appgalerij, krijgen gebruikers SSO via Azure AD-apparaten. Er is geen extra configuratie vereist. Gebruikers krijgen SSO op beide Microsoft Edge- en Chrome-browsers. Voor Chrome moet u de [windows 10-accountsextensie](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)implementeren. 

Alle Win32-toepassingen die:

- Vertrouw op Web Account Manager (WAM) voor tokenaanvragen en ontvang ook SSO op Azure AD-apparaten. 
- Vertrouw niet op WAM kan gebruikers om verificatie vragen. 

### <a name="on-premises-web-applications"></a>On-premises webapplicaties

Als uw apps op maat zijn gemaakt en/of on-premises worden gehost, moet u ze toevoegen aan de vertrouwde sites van uw browser om:

- Geïntegreerde windows-verificatie inschakelen om te werken 
- Geef gebruikers een no-prompt SSO-ervaring. 

Zie AD FS [verifiëren en beheren als](/previous-versions/azure/azure-services/jj151809(v%3dazure.100))u AD FS gebruikt. 

**Aanbeveling:** Overweeg hosting in de cloud (bijvoorbeeld Azure) en integratie met Azure AD voor een betere ervaring.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>On-premises toepassingen die vertrouwen op verouderde protocollen

Gebruikers krijgen SSO van Azure AD-apparaten als het apparaat toegang heeft tot een domeincontroller. 

**Aanbeveling:** Implementeer [de proxy van Azure AD App](/azure/active-directory/manage-apps/application-proxy) om beveiligde toegang voor deze toepassingen mogelijk te maken.

### <a name="on-premises-network-shares"></a>On-premises netwerkaandelen

Uw gebruikers hebben SSO van Azure AD-apparaten verbonden wanneer een apparaat toegang heeft tot een on-premises domeincontroller.

### <a name="printers"></a>Printers

Voor printers moet u [hybride cloudafdrukken](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) implementeren voor het ontdekken van printers op azure AD-apparaten. 

Hoewel printers niet automatisch kunnen worden gedetecteerd in een cloudomgeving, kunnen uw gebruikers ook het UNC-pad van de printers gebruiken om ze rechtstreeks toe te voegen. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>On-premises toepassingen die afhankelijk zijn van machineauthenticatie

Azure AD-apparaten bieden geen ondersteuning voor on-premises toepassingen die afhankelijk zijn van machineverificatie. 

**Aanbeveling:** Overweeg het terugtrekken van deze toepassingen en verhuizen naar hun moderne alternatieven.

### <a name="remote-desktop-services"></a>Externe bureaubladservices

Voor verbinding met extern bureaublad met een Azure AD-verbindingsapparaat moet de hostmachine zijn aangesloten bij Azure AD of hybride Azure AD. Extern bureaublad vanaf een niet-verbonden of niet-Windows-apparaat wordt niet ondersteund. Zie [Verbinding maken met externe Azure AD-pc](/windows/client-management/connect-to-remote-aadj-pc) voor meer informatie

## <a name="understand-your-provisioning-options"></a>Inzicht in uw inrichtingsopties

U Azure AD-lid maken via de volgende benaderingen:

- **Selfservice in OOBE/Settings** - In de selfservicemodus doorlopen gebruikers het Azure AD-joinproces tijdens Windows Out of Box Experience (OOBE) of vanuit Windows-instellingen. Zie [Uw werkapparaat aansluiten bij het netwerk van uw organisatie](/azure/active-directory/user-help/user-help-join-device-on-network)voor meer informatie. 
- **Windows Autopilot** - Windows Autopilot maakt het mogelijk om apparaten vooraf te configureren voor een soepelere ervaring in OOBE om een Azure AD-join uit te voeren. Zie het overzicht [van Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)voor meer informatie. 
- **Bulkinschrijving** - Met bulkinschrijvingen kan een beheerder van Azure AD-lid worden door een hulpmiddel voor het inrichten van bulkte gebruiken om apparaten te configureren. Zie [Bulkinschrijving voor Windows-apparaten voor](/intune/windows-bulk-enroll)meer informatie .
 
Hier is een vergelijking van deze drie benaderingen 
 
|   | Selfservice-installatie | Windows Autopilot | Bulkinschrijving |
| --- | --- | --- | --- |
| Gebruikersinteractie vereisen om in te stellen | Ja | Ja | Nee |
| It-inspanning vereisen | Nee | Ja | Ja |
| Toepasbare stromen | OOBE-&-instellingen | Alleen OOBE | Alleen OOBE |
| Lokale beheerdersrechten voor primaire gebruikers | Ja, standaard | Configureerbaar | Nee |
| Ondersteuning voor apparaat-OEM vereisen | Nee | Ja | Nee |
| Ondersteunde versies | 1511+ | 1709+ | 1703+ |
 
Kies uw implementatiebenadering of -benaderingen door de bovenstaande tabel te bekijken en de volgende overwegingen voor het volgen van een van beide benaderingen te bekijken:  

- Zijn uw gebruikers tech savvy om te gaan door de setup zelf? 
   - Selfservice kan het beste werken voor deze gebruikers. Overweeg Windows Autopilot om de gebruikerservaring te verbeteren.  
- Zijn uw gebruikers afgelegen of binnen bedrijfsruimten? 
   - Selfservice of Autopilot werken het beste voor externe gebruikers voor een probleemloze installatie. 
- Heeft u liever een door de gebruiker gestuurde of een door de beheerder beheerde configuratie? 
   - Bulkinschrijving werkt beter voor beheergestuurde implementatie om apparaten in te stellen voordat ze worden overgedragen aan gebruikers.     
- Koopt u apparaten van 1-2 OEM's, of heeft u een brede distributie van OEM-apparaten?  
   - Als u koopt bij beperkte OEM's die ook Autopilot ondersteunen, u profiteren van een nauwere integratie met Autopilot. 

## <a name="configure-your-device-settings"></a>Uw apparaatinstellingen configureren

Met de Azure-portal u de implementatie van Azure AD-samengevoegde apparaten in uw organisatie beheren. Als u de gerelateerde **Azure Active Directory page**instellingen wilt configureren, `Devices > Device settings`selecteert u op de pagina Azure Active Directory .

### <a name="users-may-join-devices-to-azure-ad"></a>Gebruikers kunnen apparaten deelnemen aan Azure AD

Stel deze optie in op **Alles** of **Geselecteerd** op basis van het bereik van uw implementatie en wie u wilt toestaan om een Azure AD-apparaat in te stellen. 

![Gebruikers kunnen apparaten deelnemen aan Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Extra lokale beheerders op Azure AD-apparaten

Kies **Geselecteerd** en selecteer de gebruikers die u wilt toevoegen aan de groep lokale beheerders op alle azure AD-apparaten. 

![Extra lokale beheerders op Azure AD-apparaten](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Multi-factor Auth vereisen om apparaten aan te sluiten

Selecteer **'Ja** als u gebruikers nodig hebt om MFA uit te voeren terwijl u apparaten samenvoegt met Azure AD. Voor de gebruikers die apparaten met Azure AD verbinden met MFA, wordt het apparaat zelf een 2e factor.

![Multi-factor Auth vereisen om apparaten aan te sluiten](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Uw mobiliteitsinstellingen configureren

Voordat u uw mobiliteitsinstellingen configureren, moet u mogelijk eerst een MDM-provider toevoegen.

**Een MDM-provider toevoegen:**

1. Klik op de **pagina Azure Active Directory**in de sectie **Beheren** op `Mobility (MDM and MAM)`. 
1. Klik **op Toepassing toevoegen**.
1. Selecteer uw MDM-provider in de lijst.

   ![Een toepassing toevoegen](./media/azureadjoin-plan/04.png)

Selecteer uw MDM-provider om de bijbehorende instellingen te configureren. 

### <a name="mdm-user-scope"></a>MDM-gebruikersbereik

Selecteer **Sommige** of **Alles** op basis van het bereik van uw implementatie. 

![MDM-gebruikersbereik](./media/azureadjoin-plan/05.png)

Op basis van uw bereik gebeurt een van de volgende dingen: 

- **Gebruiker bevindt zich in MDM-bereik:** Als u een Azure AD Premium-abonnement hebt, wordt MDM-inschrijving samen met Azure AD-deelname geautomatiseerd. Alle gebruikers met scopen moeten over een geschikte licentie voor uw MDM beschikken. Als MDM-inschrijving mislukt in dit scenario, wordt Azure AD-deelname ook teruggedraaid.
- **Gebruiker valt niet binnen MDM-bereik:** Als gebruikers zich niet in mdm-bereik bevinden, wordt Azure AD samengevoegd zonder MDM-inschrijving. Dit resulteert in een onbeheerd apparaat.

### <a name="mdm-urls"></a>MDM-URL's

Er zijn drie URL's die gerelateerd zijn aan uw MDM-configuratie:

- URL voor MDM-gebruiksvoorwaarden
- URL voor MDM-detectie 
- URL voor MDM-naleving

![Een toepassing toevoegen](./media/azureadjoin-plan/06.png)

Elke URL heeft een vooraf gedefinieerde standaardwaarde. Als deze velden leeg zijn, neem dan contact op met uw MDM-provider voor meer informatie.

### <a name="mam-settings"></a>MAM-instellingen

MAM is niet van toepassing op Azure AD join. 

## <a name="configure-enterprise-state-roaming"></a>Roaming voor ondernemingsstatus configureren

Zie Enterprise State Roaming inschakelen [in Azure Active Directory](enterprise-state-roaming-enable.md)als u statusroaming naar Azure AD wilt inschakelen, zodat gebruikers hun instellingen op verschillende apparaten kunnen synchroniseren. 

**Aanbeveling:** Schakel deze instelling zelfs in voor hybride Azure AD-apparaten.

## <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

Als u een MDM-provider hebt geconfigureerd voor uw Azure AD-apparaten, markeert de provider het apparaat als compatibel zodra het apparaat onder beheer is. 

![Compatibel apparaat](./media/azureadjoin-plan/46.png)

U deze implementatie gebruiken om [beheerde apparaten voor toegang tot de cloud-app](../conditional-access/require-managed-devices.md)met voorwaardelijke toegang te vereisen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lid worden van een nieuw Windows 10-apparaat met Azure AD tijdens een eerste run](azuread-joined-devices-frx.md)
> [Word lid van uw werkapparaat in het netwerk van uw organisatie](/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
