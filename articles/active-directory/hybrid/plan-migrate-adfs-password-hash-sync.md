---
title: 'Azure AD Connect: migreren van federatie naar PHS voor Azure AD | Microsoft Documenten'
description: In dit artikel vindt u informatie over het verplaatsen van uw hybride identiteitsomgeving van federatie naar wachtwoordhashsynchronisatie.
services: active-directory
author: billmath
manager: daveba
ms.reviewer: martincoetzer
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b621c9cbc35d0e9956f6648d870102affd84c24f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76028388"
---
# <a name="migrate-from-federation-to-password-hash-synchronization-for-azure-active-directory"></a>Migreren van federatie naar wachtwoordhashsynchronisatie voor Azure Active Directory

In dit artikel wordt beschreven hoe u uw organisatiedomeinen verplaatst van Ad FS (Active Directory Federation Services) naar wachtwoordhashsynchronisatie.

> [!NOTE]
> Het wijzigen van uw verificatiemethode vereist planning, testen en mogelijk downtime. [Gefaseerde implementatie](how-to-connect-staged-rollout.md) biedt een alternatieve manier om te testen en geleidelijk te migreren van federatie naar cloudverificatie met behulp van wachtwoordhashsynchronisatie.

## <a name="prerequisites-for-migrating-to-password-hash-synchronization"></a>Vereisten voor het migreren naar wachtwoordhashsynchronisatie

De volgende vereisten zijn vereist om te migreren van het gebruik van AD FS naar het gebruik van wachtwoordhashsynchronisatie.

### <a name="update-azure-ad-connect"></a>Azure AD Connect bijwerken

Als u minimaal de stappen uitvoert om te migreren naar wachtwoordhashsynchronisatie, moet u [Azure AD connect](https://www.microsoft.com/download/details.aspx?id=47594) 1.1.819.0 hebben. Deze versie bevat belangrijke wijzigingen in de manier waarop aanmeldingsconversie wordt uitgevoerd en vermindert de totale tijd om van Federation naar Cloud Authentication te migreren van mogelijk uren naar minuten.


> [!IMPORTANT]
> U in verouderde documentatie, hulpprogramma's en blogs lezen dat gebruikersconversie vereist is wanneer u domeinen converteert van federatieve identiteit naar beheerde identiteit. *Het converteren van gebruikers* is niet langer nodig. Microsoft werkt aan het bijwerken van documentatie en hulpprogramma's om deze wijziging weer te geven.

Als u Azure AD Connect wilt bijwerken, voert u de stappen uit in [Azure AD Connect: Upgrade naar de nieuwste versie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version).

### <a name="password-hash-synchronization-required-permissions"></a>Voor wachtwoordhashsynchronisatie vereiste machtigingen

U Azure AD Connect configureren met behulp van expresinstellingen of een aangepaste installatie. Als u de aangepaste installatieoptie hebt gebruikt, zijn de [vereiste machtigingen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-accounts-permissions) voor wachtwoordhashsynchronisatie mogelijk niet aanwezig.

Het AD DS-serviceaccount (Azure AD Connect Active Directory Domain Services) vereist de volgende machtigingen om wachtwoordhashes te synchroniseren:

* Mapwijzigingen repliceren
* Mapwijzigingen repliceren Alles

Nu is een goed moment om te controleren of deze machtigingen zijn op zijn plaats voor alle domeinen in het forest.

### <a name="plan-the-migration-method"></a>De migratiemethode plannen

U kiezen uit twee methoden om te migreren van federatief identiteitsbeheer naar wachtwoordhashsynchronisatie en naadloze single sign-on (SSO). De methode die u gebruikt, is afhankelijk van hoe uw AD FS-exemplaar oorspronkelijk is geconfigureerd.

* **Azure AD Connect**. Als u AD FS oorspronkelijk hebt geconfigureerd met Azure AD Connect, *moet* u de synchronisatie van wachtwoordhash wijzigen met de wizard Azure AD Connect.

   Azure AD Connect voert de cmdlet **Set-MsolDomainAuthentication** automatisch uit wanneer u de aanmeldingsmethode van de gebruiker wijzigt. Azure AD Connect maakt automatisch alle geverifieerde federatieve domeinen in uw Azure AD-tenant ongedaan.

   > [!NOTE]
   > Als u Azure AD Connect oorspronkelijk hebt gebruikt om AD FS te configureren, u niet voorkomen dat u alle domeinen in uw tenant niet meer hoeft te ontlasten wanneer u de aanmelding van de gebruiker wijzigt in synchronisatie van wachtwoordhash. ‎
* **Azure AD Verbinding maken met PowerShell**. U deze methode alleen gebruiken als u AD FS oorspronkelijk niet hebt geconfigureerd met Azure AD Connect. Voor deze optie moet u de aanmeldingsmethode van de gebruiker nog steeds wijzigen via de wizard Azure AD Connect. Het kernverschil met deze optie is dat de wizard de cmdlet **Set-MsolDomainAuthentication** niet automatisch uitvoert. Met deze optie hebt u volledige controle over welke domeinen worden geconverteerd en in welke volgorde.

Voer de stappen in de volgende secties uit om te begrijpen welke methode u moet gebruiken.

#### <a name="verify-current-user-sign-in-settings"></a>Huidige aanmeldingsinstellingen voor gebruikers verifiëren

Ga als volgt te werk om de aanmeldingsinstellingen van uw huidige gebruiker te verifiëren:

1. Meld u aan bij de [Azure AD-portal](https://aad.portal.azure.com/) met behulp van een Global Administrator-account.
2. Controleer in de **aanmeldingssectie Gebruiker** de volgende instellingen:
   * **Federatie** is ingesteld op **Ingeschakeld**.
   * **Naadloze enkele aanmelding** is ingesteld op **Uitgeschakeld**.
   * **Pass-through-verificatie** is ingesteld op **Uitgeschakeld**.

   ![Schermafbeelding van de instellingen in de aanmeldingssectie Azure AD Connect-gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image1.png)

#### <a name="verify-the-azure-ad-connect-configuration"></a>De Configuratie van Azure AD Connect verifiëren

1. Open Azure AD Connect op uw Azure AD Connect-server. Selecteer **Configureren**.
2. Selecteer op de pagina **Extra taken** de optie Huidige **configuratie weergeven**en selecteer **Volgende**.<br />

   ![Schermafbeelding van de optie Huidige configuratie weergeven geselecteerd op de pagina Extra taken](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image2.png)<br />
3. Noteer op de pagina **Uw oplossing controleren** de status **wachtwoordhashsynchronisatie.**<br /> 

   * Als **hashsynchronisatie** voor wachtwoorden is ingesteld op **Uitgeschakeld,** voert u de stappen in dit artikel uit om deze in te schakelen.
   * Als **wachtwoordhashsynchronisatie** is ingesteld op **Ingeschakeld,** u de sectie **Stap 1: Wachtwoordhashsynchronisatie** in dit artikel inschakelen.
4. Schuif **op** de pagina Uw oplossing controleren naar **Active Directory Federation Services (AD FS).**<br />

   * Als de AD FS-configuratie in deze sectie wordt weergegeven, u er veilig van uitgaan dat AD FS oorspronkelijk is geconfigureerd met Azure AD Connect. U uw domeinen converteren van federatieve identiteit naar beheerde identiteit met behulp van de **aanmeldingsoptie** Azure AD Connect Change user.You can convert your domains from federated identity to managed identity using the Azure AD Connect Change user sign-in option. Het proces wordt beschreven in de sectie **Optie A: Overschakelen van federatie naar wachtwoordhashsynchronisatie met Azure AD Connect**.
   * Als AD FS niet wordt vermeld in de huidige instellingen, moet u uw domeinen handmatig converteren van federatieve identiteit naar beheerde identiteit met PowerShell. Zie de sectie **Optie B: Overschakelen van synchronisatie van federatie naar wachtwoordhash met Azure AD Connect en PowerShell**voor meer informatie over dit proces.

### <a name="document-current-federation-settings"></a>Huidige federatie-instellingen documenteren

Voer de cmdlet **Get-MsolDomainFederationSettings** uit om uw huidige federatie-instellingen te vinden:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName YourDomain.extention | fl *
```

Voorbeeld:

``` PowerShell
Get-MsolDomainFederationSettings -DomainName Contoso.com | fl *
```

Controleer alle instellingen die mogelijk zijn aangepast voor de ontwerp- en implementatiedocumentatie van uw federatie. Zoek specifiek naar aanpassingen in **PreferredAuthenticationProtocol,** **SupportsMfa**en **PromptLoginBehavior**.

Raadpleeg voor meer informatie de volgende artikelen:

* [Ondersteuning voor AD FS-prompt=login-parameter](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-prompt-login)
* [Set-MsolDomainAuthentication](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainauthentication?view=azureadps-1.0)

> [!NOTE]
> Als **SupportsMfa** is ingesteld op **True,** gebruikt u een on-premises multi-factor authenticatieoplossing om een tweede-factor-uitdaging in de gebruikersverificatiestroom te injecteren. Deze instelling werkt niet meer voor Azure AD-verificatiescenario's nadat dit domein is geconverteerd van federatief naar beheerde verificatie. Nadat u de federatie hebt uitgeschakeld, vermindert u de relatie met uw on-premises federatie en dit omvat on-premises MFA-adapters. 
>
> Gebruik in plaats daarvan de azure multi-factor authentication cloud-gebaseerde service om dezelfde functie uit te voeren. Evalueer uw vereisten voor multifactorauthenticatie zorgvuldig voordat u verdergaat. Voordat u uw domeinen converteert, moet u weten hoe u Azure Multi-Factor Authentication, de licentieimplicaties en het gebruikersregistratieproces gebruiken.

#### <a name="back-up-federation-settings"></a>Back-ups van federaties

Hoewel er tijdens de processen die in dit artikel in dit artikel worden beschreven, geen wijzigingen worden aangebracht in andere relying partijen in uw AD FS-farm, raden we u aan een geldige back-up van uw AD FS-farm te hebben waarvan u herstellen. U een geldige back-up maken met behulp van de gratis Microsoft [AD FS Rapid Restore Tool.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-rapid-restore-tool) U het hulpprogramma gebruiken om een back-up te maken van AD FS en om een bestaande farm te herstellen of een nieuwe farm te maken.

Als u ervoor kiest om het AD FS Rapid Restore Tool niet te gebruiken, moet u minimaal het Microsoft Office 365 Identity Platform vertrouwend partijvertrouwen en de bijbehorende aangepaste claimregels exporteren die u hebt toegevoegd. U de vertrouwensrelatie van de relying party en de bijbehorende claimregels exporteren met behulp van het volgende PowerShell-voorbeeld:

``` PowerShell
(Get-AdfsRelyingPartyTrust -Name "Microsoft Office 365 Identity Platform") | Export-CliXML "C:\temp\O365-RelyingPartyTrust.xml"
```

## <a name="deployment-considerations-and-using-ad-fs"></a>Implementatieoverwegingen en het gebruik van AD FS

In deze sectie worden overwegingen en details over het gebruik van AD FS beschreven.

### <a name="current-ad-fs-use"></a>Huidig AD FS-gebruik

Voordat u converteert van federatieve identiteit naar beheerde identiteit, moet u goed bekijken hoe u AD FS momenteel gebruikt voor Azure AD, Office 365 en andere toepassingen (vertrouwensrelaties van de relying party). Houd in het bijzonder rekening met de scenario's die in de volgende tabel worden beschreven:

| Als | Dan |
|-|-|
| U bent van plan AD FS te blijven gebruiken met andere toepassingen (andere dan Azure AD en Office 365). | Nadat u uw domeinen hebt geconverteerd, gebruikt u zowel AD FS als Azure AD. Denk aan de gebruikerservaring. In sommige scenario's moeten gebruikers mogelijk twee keer verifiëren: één keer naar Azure AD (waarbij een gebruiker SSO-toegang krijgt tot andere toepassingen, zoals Office 365), en opnieuw voor toepassingen die nog steeds zijn gekoppeld aan AD FS als vertrouwensrelatie tussen een relying party. |
| Uw AD FS-exemplaar is sterk aangepast en is afhankelijk van specifieke aanpassingsinstellingen in het bestand onload.js (bijvoorbeeld als u de aanmeldingservaring hebt gewijzigd zodat gebruikers alleen een **SamAccountName-indeling** gebruiken voor hun gebruikersnaam in plaats van een User Principal Name (UPN), of als uw organisatie de aanmeldingservaring sterk heeft gebrandmerkt). Het bestand onload.js kan niet worden gedupliceerd in Azure AD. | Voordat u verdergaat, moet u controleren of Azure AD kan voldoen aan uw huidige aanpassingsvereisten. Zie de secties over AD FS-branding en AD FS-aanpassing voor meer informatie en voor begeleiding.|
| U gebruikt AD FS om eerdere versies van verificatieclients te blokkeren.| Overweeg AD FS-besturingselementen te vervangen die eerdere versies van verificatieclients blokkeren met behulp van een combinatie van [voorwaardelijke toegangsbesturingselementen](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions) en [Exchange Online-clienttoegangsregels](https://aka.ms/EXOCAR). |
| U vereist dat gebruikers multi-factor authenticatie uitvoeren tegen een on-premises multi-factor authenticatieserveroplossing wanneer gebruikers zich verifiëren bij AD FS.| In een beheerd identiteitsdomein u een multi-factor authenticatieuitdaging niet injecteren via de on-premises multi-factor authenticatieoplossing in de verificatiestroom. U de Azure Multi-Factor Authentication-service echter gebruiken voor multi-factor authenticatie nadat het domein is geconverteerd.<br /><br /> Als uw gebruikers momenteel geen Azure Multi-Factor Authentication gebruiken, is een eenmalige stap voor gebruikersregistratie vereist. U moet de geplande registratie voorbereiden en aan uw gebruikers meedelen. |
| U gebruikt momenteel toegangscontrolebeleid (AuthZ-regels) in AD FS om de toegang tot Office 365 te beheren.| Overweeg het beleid te vervangen door het gelijkwaardige Azure AD [Conditional Access-beleid](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) en [Exchange Online Client Access-regels](https://aka.ms/EXOCAR).|

### <a name="common-ad-fs-customizations"></a>Algemene AD FS-aanpassingen

In deze sectie worden veelvoorkomende AD FS-aanpassingen beschreven.

#### <a name="insidecorporatenetwork-claim"></a>InsideCorporateNetwork claim

AD FS geeft de **Claim InsideCorporateNetwork** uit als de gebruiker die authenticeert zich binnen het bedrijfsnetwerk bevindt. Deze claim kan vervolgens worden doorgegeven aan Azure AD. De claim wordt gebruikt om multi-factor authenticatie te omzeilen op basis van de netwerklocatie van de gebruiker. Zie [Vertrouwde IP's voor federatieve gebruikers voor](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud)meer informatie over hoe u bepalen of deze functionaliteit momenteel is ingeschakeld in AD FS.

De **Claim InsideCorporateNetwork** is niet beschikbaar nadat uw domeinen zijn geconverteerd naar wachtwoordhashsynchronisatie. U [benoemde locaties in Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) gebruiken om deze functionaliteit te vervangen.

Nadat u benoemde locaties hebt geconfigureerd, moet u alle beleid voor voorwaardelijke toegang bijwerken die zijn geconfigureerd om het netwerk op te nemen of uit te sluiten **Alle vertrouwde locaties** of MFA Trusted **IP-waarden** worden bijgewerkt om de nieuwe benoemde locaties weer te geven.

Zie [Active Directory Voorwaardelijke toegangslocaties](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)voor meer informatie over de voorwaarde **Locatie** in Voorwaardelijke toegang.

#### <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure-apparaten die zijn aangesloten bij AD

Wanneer u lid wordt van een apparaat met Azure AD, u regels voor voorwaardelijke toegang maken die afdwingen dat apparaten voldoen aan uw toegangsnormen voor beveiliging en naleving. Gebruikers kunnen zich ook aanmelden bij een apparaat met behulp van een organisatiewerk- of schoolaccount in plaats van een persoonlijk account. Wanneer u hybride Azure-apparaten met AD gebruikt, u lid worden van uw active directory-apparaten die zijn verbonden met Azure AD. Uw federatieve omgeving is mogelijk ingesteld om deze functie te gebruiken.

Om ervoor te zorgen dat hybride joinblijft werken voor alle apparaten die zijn verbonden met het domein nadat uw domeinen zijn geconverteerd naar wachtwoordhashsynchronisatie, moet u azure AD Connect-apparaatopties gebruiken om Active Directory-computer te synchroniseren accounts naar Azure AD. 

Voor Windows 8- en Windows 7-computeraccounts maakt hybride join gebruik van naadloze SSO om de computer in Azure AD te registreren. U hoeft windows 8- en Windows 7-computeraccounts niet te synchroniseren zoals u dat doet voor Windows 10-apparaten. U moet echter een bijgewerkt workplacejoin.exe-bestand (via een .msi-bestand) implementeren naar Windows 8- en Windows 7-clients, zodat ze zichzelf kunnen registreren met naadloze SSO. [Download het .msi-bestand](https://www.microsoft.com/download/details.aspx?id=53554).

Zie [Hybride Azure-apparaten configureren](https://docs.microsoft.com/azure/active-directory/device-management-hybrid-azuread-joined-devices-setup)voor meer informatie .

#### <a name="branding"></a>Huisstijl

Als uw organisatie [uw AD FS-aanmeldingspagina's heeft aangepast](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-user-sign-in-customization) om informatie weer te geven die relevanter is voor de organisatie, u overwegen vergelijkbare aanpassingen te maken als de [aanmeldingspagina van Azure AD.](https://docs.microsoft.com/azure/active-directory/customize-branding)

Hoewel vergelijkbare aanpassingen beschikbaar zijn, moeten enkele visuele wijzigingen op aanmeldingspagina's worden verwacht na de conversie. U gebruikers informatie verstrekken over verwachte wijzigingen in uw communicatie.

> [!NOTE]
> Organisatiebranding is alleen beschikbaar als u de Premium- of Basic-licentie voor Azure Active Directory aanschaft of als u een Office 365-licentie hebt.

## <a name="plan-deployment-and-support"></a>Implementatie en ondersteuning plannen

Voltooi de taken die in deze sectie worden beschreven om u te helpen bij het plannen van implementatie en ondersteuning.

### <a name="plan-the-maintenance-window"></a>Het onderhoudsvenster plannen

Hoewel het domeinconversieproces relatief snel is, kan Azure AD sommige verificatieaanvragen tot vier uur na het beëindigen van de domeinconversie naar uw AD FS-servers blijven verzenden. Tijdens dit venster van vier uur en afhankelijk van verschillende caches aan de servicezijde, accepteert Azure AD deze verificaties mogelijk niet. Gebruikers kunnen een foutmelding ontvangen. De gebruiker kan zich nog steeds verifiëren ten opzichte van AD FS, maar Azure AD accepteert het uitgegeven token van de gebruiker niet meer omdat die federatievertrouwensrelatie nu is verwijderd.

Alleen gebruikers die toegang hebben tot de services via een webbrowser tijdens dit venster na de conversie voordat de cache aan de servicezijde wordt gewist, worden getroffen. Oudere clients (Exchange ActiveSync, Outlook 2010/2013) worden naar verwachting niet beïnvloed omdat Exchange Online gedurende een bepaalde periode een cache van hun referenties bewaart. De cache wordt gebruikt om de gebruiker in stilte opnieuw te verifiëren. De gebruiker hoeft niet terug te keren naar AD FS. Referenties die op het apparaat voor deze clients zijn opgeslagen, worden gebruikt om zichzelf in stilte opnieuw te verifiëren nadat deze cache is gewist. Van gebruikers wordt niet verwacht dat ze wachtwoordprompts ontvangen als gevolg van het domeinconversieproces. 

Moderne verificatieclients (Office 2016 en Office 2013-, iOS- en Android-apps) gebruiken een geldig vernieuwingstoken om nieuwe toegangstokens te verkrijgen voor voortdurende toegang tot bronnen in plaats van terug te keren naar AD FS. Deze clients zijn immuun voor wachtwoordprompts die voortvloeien uit het domeinconversieproces. De clients blijven functioneren zonder extra configuratie.

> [!IMPORTANT]
> Sluit uw AD FS-omgeving niet af of verwijder de vertrouwensrelatie van de vertrouwende Office 365-basistotdat u hebt geverifieerd dat alle gebruikers met cloudverificatie kunnen verifiëren.

### <a name="plan-for-rollback"></a>Plannen voor terugdraaien

Als u een belangrijk probleem tegenkomt dat u niet snel oplossen, u besluiten de oplossing terug te draaien naar federatie. Het is belangrijk om te plannen wat u moet doen als uw implementatie niet wordt uitgerold zoals bedoeld. Als de conversie van het domein of de gebruikers mislukt tijdens de implementatie of als u moet terugdraaien naar de federatie, moet u begrijpen hoe u eventuele uitval beperken en het effect op uw gebruikers verminderen.

#### <a name="to-roll-back"></a>Terugdraaien

Als u het terugdraaien wilt plannen, raadpleegt u de documentatie voor het ontwerp en de implementatie van de federatie voor uw specifieke implementatiegegevens. Het proces moet de volgende taken omvatten:

* Beheerde domeinen converteren naar federatieve domeinen met behulp van de **cmdlet Convert-MSOLDomainToFederated.**
* Indien nodig, het configureren van aanvullende claimregels.

### <a name="plan-communications"></a>De communicatie plannen

Een belangrijk onderdeel van de implementatie en ondersteuning van de planning is ervoor te zorgen dat uw gebruikers proactief worden geïnformeerd over aankomende wijzigingen. Gebruikers moeten van tevoren weten wat ze kunnen ervaren en wat er van hen wordt verlangd. 

Nadat zowel wachtwoordhashsynchronisatie als naadloze SSO zijn geïmplementeerd, is de gebruikersaanmeldingservaring voor toegang tot Office 365 en andere bronnen die zijn geverifieerd via Azure AD-wijzigingen. Gebruikers die zich buiten het netwerk bevinden, zien alleen de aanmeldingspagina van Azure AD. Deze gebruikers worden niet doorgestuurd naar de pagina op basis van formulieren die wordt gepresenteerd door proxyservers voor extern gerichte webtoepassingen.

Neem de volgende elementen op in uw communicatiestrategie:

* Informeer gebruikers over aankomende en vrijgegeven functionaliteit met behulp van:
   * E-mail en andere interne communicatiekanalen.
   * Visuals, zoals posters.
   * Uitvoerende, live of andere communicatie.
* Bepaal wie de communicatie zal aanpassen en wie de communicatie zal verzenden, en wanneer.

## <a name="implement-your-solution"></a>Implementeer uw oplossing

Je hebt je oplossing gepland. Nu u het implementeren. De implementatie omvat de volgende onderdelen:

* Synchronisatie van wachtwoordhash inschakelen.
* Voorbereiden op naadloze SSO.
* De aanmeldingsmethode wijzigen in wachtwoordhashsynchronisatie en naadloze SSO inschakelen.

### <a name="step-1-enable-password-hash-synchronization"></a>Stap 1: Synchronisatie van wachtwoordhash inschakelen

De eerste stap om deze oplossing te implementeren is het inschakelen van wachtwoordhashsynchronisatie met behulp van de wizard Azure AD Connect. Wachtwoordhashsynchronisatie is een optionele functie die u inschakelen in omgevingen die federatie gebruiken. Er is geen effect op de verificatiestroom. In dit geval begint Azure AD Connect met het synchroniseren van wachtwoordhashes zonder dat dit gevolgen heeft voor gebruikers die zich aanmelden met behulp van federatie.

Daarom raden we u aan deze stap als voorbereidingstaak goed te voltooien voordat u de aanmeldingsmethode van uw domein wijzigt. Vervolgens hebt u voldoende tijd om te controleren of wachtwoordhashsynchronisatie correct werkt.

Ga als instellen voor wachtwoordhashsynchronisatie:

1. Open op de Azure AD Connect-server de wizard Azure AD Connect en selecteer **Configureren.**
2. Selecteer **Synchronisatieopties aanpassen**en selecteer **Volgende**.
3. Voer op de pagina **Verbinding maken met Azure AD** de gebruikersnaam en het wachtwoord van een Algemeen Administrator-account in.
4. Selecteer **Volgende**op de pagina **Uw mappen verbinden** .
5. Selecteer **Volgende**op de filterpagina **Domain en OU** .
6. Selecteer op de pagina **Optionele functies** de optie **Wachtwoordsynchronisatie**en selecteer **Volgende**.
 
   ![Schermafbeelding van de optie Wachtwoordsynchronisatie geselecteerd op de pagina Optionele functies](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image6.png)<br />
7. Selecteer **Volgende** op de overige pagina's. Selecteer op de laatste pagina **Configureren**.
8. Azure AD Connect begint wachtwoordhashes te synchroniseren bij de volgende synchronisatie.

Nadat wachtwoordhashsynchronisatie is ingeschakeld, worden de wachtwoordhashes voor alle gebruikers in de Azure AD Connect-synchronisatiescope opnieuw gehasht en naar Azure AD geschreven. Afhankelijk van het aantal gebruikers kan deze bewerking minuten of enkele uren duren.

Voor planningsdoeleinden moet u schatten dat ongeveer 20.000 gebruikers in 1 uur worden verwerkt.

Als u wilt controleren of wachtwoordhashsynchronisatie correct werkt, voltooit u de taak **Probleemoplossing** in de wizard Azure AD Connect:

1. Open een nieuwe Windows PowerShell-sessie op uw Azure AD Connect-server met de optie Uitvoeren als administrator.
2. Uitvoeren `Set-ExecutionPolicy RemoteSigned` `Set-ExecutionPolicy Unrestricted`of .
3. Start de wizard Azure AD Connect.
4. Ga naar de pagina **Extra taken,** selecteer **Problemen oplossen**en selecteer **Volgende**.
5. Selecteer op de pagina **Probleemoplossing** de optie **Starten** om het menu probleemoplossing in PowerShell te starten.
6. Selecteer **probleemoplossing voor wachtwoordhashsynchronisatie**in het hoofdmenu.
7. Selecteer In het submenu de optie **Wachtwoordhashsynchronisatie werkt helemaal niet.**

Zie [Problemen met wachtwoordhashsynchronisatie oplossen met Azure AD Connect-synchronisatie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)voor problemen met problemen met problemen.

### <a name="step-2-prepare-for-seamless-sso"></a>Stap 2: Bereid je voor op naadloze SSO

Als u naadloze SSO wilt gebruiken, moet u een Azure AD-URL toevoegen aan de instellingen van de intranetzone van gebruikers met behulp van een groepsbeleid in Active Directory.

Standaard berekenen webbrowsers automatisch de juiste zone, internet of intranet, vanuit een URL. Bijvoorbeeld **http:\/\/contoso/** kaarten naar de intranetzone en **http:\/\/intranet.contoso.com** kaarten naar de internetzone (omdat de URL een punt bevat). Browsers sturen Kerberos-tickets naar een cloudeindpunt, zoals de Azure AD-URL, alleen als u de URL expliciet toevoegt aan de intranetzone van de browser.

Voer de stappen uit om de vereiste wijzigingen in uw apparaten uit te [rollen.](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-quick-start)

> [!IMPORTANT]
> Als u deze wijziging doorvoeren, wordt de manier waarop uw gebruikers zich aanmelden bij Azure AD niet gewijzigd. Het is echter belangrijk dat u deze configuratie toepast op al uw apparaten voordat u verdergaat. Gebruikers die zich aanmelden op apparaten die deze configuratie niet hebben ontvangen, moeten eenvoudig een gebruikersnaam en wachtwoord invoeren om zich aan te melden bij Azure AD.

### <a name="step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso"></a>Stap 3: De aanmeldingsmethode wijzigen in wachtwoordhashsynchronisatie en naadloze SSO inschakelen

U hebt twee opties om de aanmeldingsmethode te wijzigen in wachtwoordhashsynchronisatie en naadloze SSO in te schakelen.

#### <a name="option-a-switch-from-federation-to-password-hash-synchronization-by-using-azure-ad-connect"></a>Optie A: Overschakelen van federatie naar wachtwoordhashsynchronisatie met Azure AD Connect

Gebruik deze methode als u uw AD FS-omgeving in eerste instantie hebt geconfigureerd met Azure AD Connect. U deze methode niet gebruiken als u uw AD *FS-omgeving* oorspronkelijk niet hebt geconfigureerd met Azure AD Connect.

Wijzig eerst de aanmeldingsmethode:

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **Aanmelding voor gebruikers wijzigen**en selecteer **Volgende**. 

   ![Schermafbeelding van de aanmeldingsoptie Gebruiker wijzigen op de pagina Extra taken](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image7.png)<br />
3. Voer op de pagina **Verbinding maken met Azure AD** de gebruikersnaam en het wachtwoord van een Algemeen Administrator-account in.
4. Selecteer op de **aanmeldingspagina van gebruiker** de **knop Synchronisatie van de hash-synchronisatie van wachtwoorden**. Schakel het selectievakje **Gebruikersaccounts niet converteren** in. De optie is afgeschaft. Selecteer **Eén aanmelding inschakelen**en selecteer **Volgende**.

   ![Schermafbeelding van de enkele aanmeldingspagina inschakelen](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image8.png)<br />

   > [!NOTE]
   > Vanaf Azure AD Connect-versie 1.1.880.0 is het selectievakje **Naadloos eenmalig aanmelden** standaard ingeschakeld.

   > [!IMPORTANT]
   > U de waarschuwingen die aangeven dat gebruikersconversie en volledige wachtwoordhashsynchronisatie vereist zijn, stappen zijn vereist om van federatie naar cloudverificatie te converteren. Houd er rekening mee dat deze stappen niet meer nodig zijn. Als u deze waarschuwingen nog steeds ziet, controleert u of u de nieuwste versie van Azure AD Connect uitvoert en dat u de nieuwste versie van deze handleiding gebruikt. Zie de sectie [Azure AD Connect bijwerken](#update-azure-ad-connect)voor meer informatie.

5. Voer op de pagina **Eén aanmelding inschakelen** de referenties van het domeinbeheerdersaccount in en selecteer **Volgende**.

   ![Schermafbeelding van de enkele aanmeldingspagina inschakelen](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image9.png)<br />

   > [!NOTE]
   > Domeinbeheerdersaccountreferenties zijn vereist om naadloze SSO in te schakelen. Het proces voltooit de volgende acties, waarvoor deze verhoogde machtigingen nodig zijn. De accountreferenties van de domeinbeheerder worden niet opgeslagen in Azure AD Connect of in Azure AD. De accountreferenties van de domeinbeheerder worden alleen gebruikt om de functie in te schakelen. De referenties worden verwijderd wanneer het proces is voltooid.
   >
   > 1. Er wordt een computeraccount met de naam AZUREADSSOACC (dat Azure AD vertegenwoordigt) gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-decryptiesleutel van het computeraccount wordt veilig gedeeld met Azure AD.
   > 3. Er worden twee Kerberos-serviceprincipalnamen (SN's) gemaakt om twee URL's weer te geven die worden gebruikt tijdens het aanmelden van Azure AD.

6. Controleer op de pagina **Gereed om te configureren** of het selectievakje **Synchronisatieproces starten wanneer de configuratie is voltooid,** is ingeschakeld. Selecteer vervolgens **Configureren**.

      ![Schermafbeelding van de pagina Klaar om te configureren](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image10.png)<br />

   > [!IMPORTANT]
   > Op dit moment worden al uw federatieve domeinen gewijzigd in beheerde verificatie. Wachtwoordhashsynchronisatie is de nieuwe verificatiemethode.

7. Selecteer Azure **Active Directory** > Azure AD Connect in de Azure**AD-portal**.
8. Controleer de volgende instellingen:
   * **Federatie** is ingesteld op **Uitgeschakeld**.
   * **Naadloze enkele aanmelding** is ingesteld op **Ingeschakeld**.
   * **Wachtwoordsynchronisatie** is ingesteld op **Ingeschakeld**.<br /> 

   ![Schermafbeelding van de instellingen in de aanmeldingssectie Gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image11.png)<br />

Ga naar [Testen en volgende stappen](#testing-and-next-steps).

   > [!IMPORTANT]
   > De sectie **optie B overslaan: Overschakelen van synchronisatie van federatie naar wachtwoordhash met Azure AD Connect en PowerShell**. De stappen in die sectie zijn niet van toepassing als u optie A hebt gekozen om de aanmeldingsmethode te wijzigen in wachtwoordhashsynchronisatie en naadloze SSO in te schakelen.

#### <a name="option-b-switch-from-federation-to-password-hash-synchronization-using-azure-ad-connect-and-powershell"></a>Optie B: Overschakelen van federatie naar wachtwoordhashsynchronisatie met Azure AD Connect en PowerShell

Gebruik deze optie als u uw federatieve domeinen niet in eerste instantie hebt geconfigureerd met Azure AD Connect. Tijdens dit proces schakel je naadloze SSO in en schakel je je domeinen over van federated naar managed.

1. Open de wizard Azure AD Connect op de Azure AD Connect-server.
2. Selecteer **Aanmelding voor gebruikers wijzigen**en selecteer **Volgende**.
3. Voer op de pagina **Verbinding maken met Azure AD** de gebruikersnaam en het wachtwoord voor een Algemeen Administrator-account in.
4. Selecteer op de **aanmeldingspagina van gebruiker** de knop **Synchronisatie van de hash-synchronisatie van wachtwoorden.** Selecteer **Eén aanmelding inschakelen**en selecteer **Volgende**.

   Voordat u wachtwoordhashsynchronisatie inschakelt: ![schermafbeelding met de optie Niet configureren op de aanmeldingspagina van de gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image12.png)<br />

   Nadat u wachtwoordhashsynchronisatie hebt ![ingeschakeld: schermafbeelding met nieuwe opties op de aanmeldingspagina van de gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image13.png)<br />
   
   > [!NOTE]
   > Vanaf Azure AD Connect-versie 1.1.880.0 is het selectievakje **Naadloos eenmalig aanmelden** standaard ingeschakeld.

5. Voer op de pagina **Eén aanmelding inschakelen** de referenties in voor een domeinbeheerdersaccount en selecteer **Volgende**.

   > [!NOTE]
   > Domeinbeheerdersaccountreferenties zijn vereist om naadloze SSO in te schakelen. Het proces voltooit de volgende acties, waarvoor deze verhoogde machtigingen nodig zijn. De accountreferenties van de domeinbeheerder worden niet opgeslagen in Azure AD Connect of in Azure AD. De accountreferenties van de domeinbeheerder worden alleen gebruikt om de functie in te schakelen. De referenties worden verwijderd wanneer het proces is voltooid.
   >
   > 1. Er wordt een computeraccount met de naam AZUREADSSOACC (dat Azure AD vertegenwoordigt) gemaakt in uw on-premises Active Directory-exemplaar.
   > 2. De Kerberos-decryptiesleutel van het computeraccount wordt veilig gedeeld met Azure AD.
   > 3. Er worden twee Kerberos-serviceprincipalnamen (SN's) gemaakt om twee URL's weer te geven die worden gebruikt tijdens het aanmelden van Azure AD.

6. Controleer op de pagina **Gereed om te configureren** of het selectievakje **Synchronisatieproces starten wanneer de configuratie is voltooid,** is ingeschakeld. Selecteer vervolgens **Configureren**.

   ![Schermafbeelding van de knop Configureren op de pagina Klaar om te configureren](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image15.png)<br />
   Wanneer u de knop **Configureren** selecteert, wordt naadloze SSO geconfigureerd zoals aangegeven in de vorige stap. Wachtwoordhashsynchronisatieconfiguratie wordt niet gewijzigd omdat deze eerder is ingeschakeld.

   > [!IMPORTANT]
   > Er worden op dit moment geen wijzigingen aangebracht in de manier waarop gebruikers zich aanmelden.

7. Controleer in de Azure AD-portal de volgende instellingen:
   * **Federatie** is ingesteld op **Ingeschakeld**.
   * **Naadloze enkele aanmelding** is ingesteld op **Ingeschakeld**.
   * **Wachtwoordsynchronisatie** is ingesteld op **Ingeschakeld**.

   ![Schermafbeelding van de instellingen in de aanmeldingssectie Gebruiker](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image16.png)

#### <a name="convert-domains-from-federated-to-managed"></a>Domeinen converteren van federatief naar beheerd

Op dit moment is federatie nog steeds ingeschakeld en operationeel voor uw domeinen. Om door te gaan met de implementatie, moet elk domein worden geconverteerd van federatief naar beheerd om gebruikersverificatie via wachtwoordhashsynchronisatie te forceren.

> [!IMPORTANT]
> U hoeft niet alle domeinen tegelijk te converteren. U ervoor kiezen om te beginnen met een testdomein op uw productietenant of te beginnen met uw domein met het laagste aantal gebruikers.

Voltooi de conversie met de Azure AD PowerShell-module:

1. Meld u in PowerShell aan bij Azure AD met een Global Administrator-account.
2. Voer de volgende opdracht uit om het eerste domein te converteren:

   ``` PowerShell
   Set-MsolDomainAuthentication -Authentication Managed -DomainName <domain name>
   ```

3. Selecteer Azure **Active Directory** > Azure AD Connect in de Azure**AD-portal**.
4. Controleer of het domein is geconverteerd naar beheer door de volgende opdracht uit te voeren:

   ``` PowerShell
   Get-MsolDomain -DomainName <domain name>
   ```

## <a name="testing-and-next-steps"></a>Testen en volgende stappen

Voltooi de volgende taken om de synchronisatie van wachtwoordhash te verifiëren en het conversieproces te voltooien.

### <a name="test-authentication-by-using-password-hash-synchronization"></a>Verificatie testen met wachtwoordhashsynchronisatie 

Toen uw tenant federatieve identiteit gebruikte, werden gebruikers doorgestuurd van de azure AD-aanmeldingspagina naar uw AD FS-omgeving. Nu de tenant is geconfigureerd om wachtwoordhashsynchronisatie te gebruiken in plaats van gefedereerde verificatie, worden gebruikers niet doorgestuurd naar AD FS. In plaats daarvan melden gebruikers zich rechtstreeks aan op de aanmeldingspagina van Azure AD.

Ga als een proef met wachtwoordhashsynchronisatie:

1. Open Internet Explorer in de InPrivate-modus, zodat naadloze SSO u niet automatisch aanmeldt.
2. Ga naar de aanmeldingspagina van[https://portal.office.com](https://portal.office.com/)Office 365 ( ).
3. Voer een gebruiker UPN in en selecteer **Volgende**. Zorg ervoor dat u de UPN invoert van een hybride gebruiker die is gesynchroniseerd vanuit uw on-premises Active Directory-exemplaar en die eerder federatieve verificatie heeft gebruikt. Er wordt een pagina weergegeven waarop u de gebruikersnaam en het wachtwoord invoert:

   ![Schermafbeelding van de aanmeldingspagina waarin u een gebruikersnaam invoert](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image18.png)

   ![Schermafbeelding van de aanmeldingspagina waarin u een wachtwoord invoert](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image19.png)

4. Nadat u het wachtwoord hebt ingevoerd en **Aanmelden**hebt geselecteerd, wordt u doorgestuurd naar de Office 365-portal.

   ![Schermafbeelding van de Office 365-portal](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image20.png)


### <a name="test-seamless-sso"></a>Test naadloze SSO

1. Meld u aan bij een met een domein verbonden machine die is verbonden met het bedrijfsnetwerk.
2. Ga in Internet Explorer of Chrome naar een van de volgende URL's (vervang 'contoso' door uw domein):

   * https:\/\/myapps.microsoft.com/contoso.com
   * https:\/\/myapps.microsoft.com/contoso.onmicrosoft.com

   De gebruiker wordt kort doorgestuurd naar de aanmeldingspagina van Azure AD, waarop het bericht 'U probeert aan te melden' wordt weergegeven. De gebruiker wordt niet gevraagd om een gebruikersnaam of wachtwoord.<br />

   ![Schermafbeelding van de aanmeldingspagina en het bericht van Azure AD](media/plan-migrate-adfs-password-hash-sync/migrating-adfs-to-phs_image21.png)<br />
3. De gebruiker wordt omgeleid en is aangemeld bij het toegangspaneel:

   > [!NOTE]
   > Naadloze SSO werkt op Office 365-services die domeinhint ondersteunen (bijvoorbeeld myapps.microsoft.com/contoso.com). Momenteel biedt de Office 365-portal (portal.office.com) geen ondersteuning voor domeinhints. Gebruikers zijn verplicht om een UPN in te voeren. Nadat een UPN is ingevoerd, haalt naadloze SSO het Kerberos-ticket op namens de gebruiker. De gebruiker is aangemeld zonder een wachtwoord in te voeren.

   > [!TIP]
   > Overweeg om Azure AD hybrid join te implementeren [op Windows 10](https://docs.microsoft.com/azure/active-directory/device-management-introduction) voor een verbeterde SSO-ervaring.

### <a name="remove-the-relying-party-trust"></a>De vertrouwensrelatie van de relying party verwijderen

Nadat u hebt gevalideerd dat alle gebruikers en clients met succes zijn geauthenticeren via Azure AD, is het veilig om de vertrouwensrelatie van de Vertrouwend Partij voor Office 365 te verwijderen.

Als u AD FS niet voor andere doeleinden gebruikt (dat wil zeggen voor andere vertrouwensrelaties van relying party), is het op dit moment veilig om AD FS buiten bedrijf te stellen.

### <a name="rollback"></a>Terugdraaien

Als u een groot probleem ontdekt en het niet snel oplossen, u ervoor kiezen om de oplossing terug te draaien naar federatie.

Raadpleeg de documentatie over het ontwerp en de implementatie van de federatie voor uw specifieke implementatiegegevens. Het proces moet betrekking hebben op deze taken:

* Gemanaged domeinen converteren naar federated authenticatie met behulp van de **cmdlet Convert-MSOLDomainToFederated.**
* Configureer indien nodig aanvullende claimregels.

### <a name="sync-userprincipalname-updates"></a>UserPrincipalName-updates synchroniseren

In het verleden worden updates van het kenmerk **UserPrincipalName,** dat de synchronisatieservice van de on-premises omgeving gebruikt, geblokkeerd, tenzij beide voorwaarden waar zijn:

* De gebruiker bevindt zich in een beheerd (niet-gefedereerd) identiteitsdomein.
* De gebruiker heeft geen licentie toegewezen gekregen.

Zie [UserPrincipalName-updates synchroniseren](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsyncservice-features)voor meer informatie over het verifiëren of inschakelen van deze functie.

### <a name="troubleshooting"></a>Problemen oplossen

Uw ondersteuningsteam moet begrijpen hoe u eventuele verificatieproblemen oplost die zich voordoen tijdens of na de wijziging van federatie naar beheerd. Gebruik de volgende documentatie voor probleemoplossing om uw ondersteuningsteam vertrouwd te maken met de algemene stappen voor het oplossen van problemen en de juiste acties die kunnen helpen om het probleem te isoleren en op te lossen.

[Problemen oplossen azure Active Directory password hash synchronisatie](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnectsync-troubleshoot-password-hash-synchronization)

[Problemen oplossen azure Active Directory Seamless Single Sign-On](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sso)

## <a name="roll-over-the-seamless-sso-kerberos-decryption-key"></a>Rol over de naadloze SSO Kerberos decryptiesleutel

Het is belangrijk om regelmatig de Kerberos-decryptiesleutel van het AZUREADSSOACC-computeraccount (dat Azure AD vertegenwoordigt) over te rollen. Het AZUREADSSOACC-computeraccount wordt gemaakt in uw on-premises Active Directory-forest. We raden u ten zeerste aan om de kerberos-decryptiesleutel ten minste elke 30 dagen om af te stemmen op de manier waarop Active Directory-domeinleden wachtwoordwijzigingen indienen. Er is geen gekoppeld apparaat gekoppeld aan het azureadssoacc-computeraccountobject, dus u moet de rollover handmatig uitvoeren.

Start de rollover van de naadloze SSO Kerberos-decryptiesleutel op de on-premises server waarop Azure AD Connect wordt uitgevoerd.

Zie Voor meer informatie [hoe kan ik de kerberos-decryptiesleutel van het AZUREADSSOACC-computeraccount omrollen?](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-faq).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure AD Connect-ontwerpconcepten](plan-connect-design-concepts.md).
* Kies de [juiste verificatie](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).
* Meer informatie over [ondersteunde topologieën](plan-connect-design-concepts.md).
