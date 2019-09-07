---
title: Azure AD-wachtwoord beveiliging implementeren-Azure Active Directory
description: Azure AD-wachtwoord beveiliging implementeren om te voor komen dat ongeldige wacht woorden on-premises zijn
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5949f57a87f324dc2e6651611574f4b66215c8a8
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70389761"
---
# <a name="deploy-azure-ad-password-protection"></a>Wachtwoordbeveiliging in Azure AD implementeren

Nu u begrijpt [hoe u Azure AD-wachtwoord beveiliging voor Windows Server Active Directory afdwingt](concept-password-ban-bad-on-premises.md), is de volgende stap het plannen en uitvoeren van uw implementatie.

## <a name="deployment-strategy"></a>Implementatie strategie

U wordt aangeraden implementaties in de controle modus te starten. De controle modus is de standaard instelling, waar wacht woorden nog kunnen worden ingesteld. Wacht woorden die worden geblokkeerd, worden vastgelegd in het gebeurtenis logboek. Nadat u de proxy servers en DC-agents in de controle modus hebt geïmplementeerd, moet u de gevolgen van het wachtwoord beleid voor gebruikers en de omgeving bewaken wanneer het beleid wordt afgedwongen.

Tijdens de controle fase weten veel organisaties dat:

* Ze moeten bestaande operationele processen verbeteren om veiligere wacht woorden te gebruiken.
* Gebruikers gebruiken vaak onbeveiligde wacht woorden.
* Ze moeten gebruikers informeren over de aanstaande wijziging in het afdwingen van beveiliging, mogelijke gevolgen hiervan en het kiezen van veiligere wacht woorden.

Het is ook mogelijk om uw bestaande Active Directory domein controller implementatie automatisering te beïnvloeden. U wordt aangeraden ten minste één DC-promotie en één DC-degradatie uit te voeren tijdens de evaluatie periode, zodat dergelijke problemen vooraf kunnen worden gedetecteerd.  Zie voor meer informatie:

* [Ntdsutil. exe kan geen zwak wacht woord voor de herstel modus van de Directory Services instellen](howto-password-ban-bad-on-premises-troubleshoot.md##ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [De replica promotie van de domein controller is mislukt vanwege een zwak wacht woord voor de herstel modus van de Directory Services](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [De degradatie van de domein controller is mislukt vanwege een zwak lokaal beheerders wachtwoord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Nadat de functie gedurende een redelijke periode in de controle modus is uitgevoerd, kunt u de configuratie van de *controle* wijzigen naar *afdwingen* om veiligere wacht woorden te vereisen. Het is een goed idee om te controleren of er momenteel gerichte bewaking is.

## <a name="deployment-requirements"></a>Implementatievereisten

* Licentie vereisten voor Azure AD-wachtwoord beveiliging vindt u in het artikel [overbodige wacht woorden in uw organisatie elimineren](concept-password-ban-bad.md#license-requirements).
* Alle domein controllers die de DC-Agent service voor Azure AD-wachtwoord beveiliging ophalen, moeten Windows Server 2012 of hoger uitvoeren. Deze vereiste impliceert niet dat het Active Directory domein of forest ook moet het domein of forest-functionaliteits niveau van Windows Server 2012. Zoals vermeld in [ontwerp principes](concept-password-ban-bad-on-premises.md#design-principles), is er geen mini maal DFL of FFL vereist voor het uitvoeren van de DC-agent of proxy software.
* Op alle computers waarop de DC-Agent service is geïnstalleerd, moet .NET 4,5 zijn geïnstalleerd.
* Op alle computers waarop de proxy service voor Azure AD-wachtwoord beveiliging wordt geïnstalleerd, moet Windows Server 2012 R2 of later worden uitgevoerd.
   > [!NOTE]
   > Proxy service-implementatie is een verplichte vereiste voor het implementeren van Azure AD-wachtwoord beveiliging, zelfs als de domein controller mogelijk uitgaande directe Internet connectiviteit heeft. 
   >
* Op alle computers waarop de Azure AD-proxy service voor wachtwoord beveiliging wordt geïnstalleerd, moet .NET 4,7 zijn geïnstalleerd.
  .NET 4,7 moet al zijn geïnstalleerd op een volledig bijgewerkte Windows-Server. Als dat niet het geval is, downloadt en voert u het installatie programma uit dat is gevonden op [het .NET Framework 4,7 offline-installatie programma voor Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Op alle machines, met inbegrip van domein controllers, waarop Azure AD-wachtwoord beveiligings onderdelen worden geïnstalleerd, moet de universele C-runtime zijn geïnstalleerd. U kunt de runtime ophalen door ervoor te zorgen dat u alle updates van Windows Update hebt. U kunt deze ook verkrijgen in een specifiek update pakket. Zie [Update voor Universal C runtime in Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)voor meer informatie.
* Er moet een netwerk verbinding zijn tussen ten minste één domein controller in elk domein en ten minste één server die als host fungeert voor de proxy service voor wachtwoord beveiliging. Deze connectiviteit moet toestaan dat de domein controller toegang krijgt tot de RPC-eindpunttoewijzer poort 135 en de RPC-server poort op de proxy service. De RPC-server poort is standaard een dynamische RPC-poort, maar kan worden geconfigureerd voor het [gebruik van een statische poort](#static).
* Alle computers die de proxy service hosten, moeten netwerk toegang hebben tot de volgende eind punten:

    |**Endpoint**|**Doel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Verificatie aanvragen|
    |`https://enterpriseregistration.windows.net`|Azure AD-functionaliteit voor wachtwoord beveiliging|

* Alle computers die de proxy service voor wachtwoord beveiliging hosten, moeten worden geconfigureerd om domein controllers de mogelijkheid te geven zich aan te melden bij de proxy service. Dit wordt bepaald via de toewijzing van de bevoegdheid toegang tot deze computer vanaf het netwerk.
* Alle computers die de proxy service voor wachtwoord beveiliging hosten, moeten zo worden geconfigureerd dat HTTP-verkeer voor uitgaande TLS 1,2 wordt toegestaan.
* Een algemeen beheerders account voor het registreren van de proxy service voor wachtwoord beveiliging en forest met Azure AD.
* Een account met Active Directory domein beheerders rechten in het forest-hoofd domein om het Windows Server Active Directory forest te registreren bij Azure AD.
* Een Active Directory domein waarop de DC-Agent service software wordt uitgevoerd, moet Distributed File System replicatie (DFSR) voor SYSVOL-replicatie gebruiken.
* De Key Distribution-service moet zijn ingeschakeld op alle domein controllers in het domein waarop Windows Server 2012 wordt uitgevoerd. Deze service wordt standaard ingeschakeld via hand matige trigger start.

## <a name="single-forest-deployment"></a>Implementatie met één forest

In het volgende diagram ziet u hoe de basis onderdelen van Azure AD-wachtwoord beveiliging samen werken in een on-premises Active Directory omgeving.

![Hoe de Azure AD-onderdelen voor wachtwoord beveiliging samen werken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Het is een goed idee om te controleren hoe de software werkt voordat u deze implementeert. Zie [Conceptueel overzicht van Azure AD-wachtwoord beveiliging](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>De software downloaden

Er zijn twee vereiste installatie Programma's voor Azure AD-wachtwoord beveiliging. Ze zijn beschikbaar via het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>De proxy service installeren en configureren voor wachtwoord beveiliging

1. Kies een of meer servers als host voor de proxy service voor wachtwoord beveiliging.
   * Elke service kan alleen wachtwoord beleid bieden voor één forest. De hostmachine moet lid zijn van een domein in dat forest. De basis-en onderliggende domeinen worden beide ondersteund. U hebt een netwerk verbinding nodig tussen ten minste één domein controller in elk domein van het forest en de wachtwoord beveiligings computer.
   * U kunt de proxy service uitvoeren op een domein controller om te testen. Maar die domein controller vereist vervolgens Internet connectiviteit, wat een beveiligings probleem kan zijn. We raden u aan deze configuratie alleen te testen.
   * We raden ten minste twee proxy servers aan voor redundantie. Bekijk [hoge Beschik baarheid](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Installeer de Azure AD-proxy service voor wachtwoord beveiliging `AzureADPasswordProtectionProxySetup.exe` met behulp van het software-installatie programma.
   * Opnieuw opstarten is niet vereist voor de software-installatie. De software-installatie wordt mogelijk geautomatiseerd met behulp van standaard MSI-procedures, bijvoorbeeld:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > De Windows Firewall-service moet worden uitgevoerd voordat u het pakket AzureADPasswordProtectionProxySetup. msi installeert om een installatie fout te voor komen. Als Windows Firewall is geconfigureerd om niet te worden uitgevoerd, kunt u de firewall service tijdelijk inschakelen en uitvoeren tijdens de installatie. De proxy software heeft na de installatie geen specifieke afhankelijkheid van Windows Firewall. Als u een firewall van derden gebruikt, moet deze nog steeds worden geconfigureerd om te voldoen aan de implementatie vereisten. Dit zijn onder andere het toestaan van binnenkomende toegang tot poort 135 en de proxy RPC-server poort. Zie [implementatie vereisten](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Open een Power shell-venster als beheerder.
   * De software voor de wachtwoord beveiligings proxy bevat een nieuwe Power shell-module, *AzureADPasswordProtection*. Met de volgende stappen worden verschillende cmdlets uitgevoerd vanuit deze Power shell-module. Importeer de nieuwe module als volgt:

      ```powershell
      Import-Module AzureADPasswordProtection
      ```

   * Als u wilt controleren of de service wordt uitgevoerd, gebruikt u de volgende Power shell-opdracht:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     In het resultaat moet de **status** ' running ' worden weer gegeven.

1. Registreer de proxy.
   * Nadat stap 3 is voltooid, wordt de proxy service op de computer uitgevoerd. Maar de service heeft nog niet de benodigde referenties om te communiceren met Azure AD. Registratie bij Azure AD is vereist:

     `Register-AzureADPasswordProtectionProxy`

     Voor deze cmdlet zijn globale beheerders referenties voor uw Azure-Tenant vereist. U hebt ook on-premises Active Directory domein Administrator bevoegdheden nodig in het forest-hoofd domein. Wanneer deze opdracht eenmaal is geslaagd voor een proxy service, worden er aanvullende aanroepen van het object uitgevoerd, maar dit is niet nodig.

      De `Register-AzureADPasswordProtectionProxy` cmdlet ondersteunt de volgende drie verificatie modi.

     * Interactieve verificatie modus:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op Server Core-besturings systemen. Gebruik in plaats daarvan een van de volgende verificatie modi. Deze modus kan ook mislukken als verbeterde beveiliging van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te scha kelen, de proxy te registreren en vervolgens weer in te scha kelen.

     * Verificatie modus apparaat-code:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        U kunt de verificatie volt ooien door de weer gegeven instructies op een ander apparaat te volgen.

     * Silent (op wacht woord gebaseerde) verificatie modus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure Multi-Factor Authentication is vereist voor uw account. In dat geval gebruikt u een van de twee voor gaande verificatie modi, of gebruikt u in plaats daarvan een ander account waarvoor MFA niet vereist is.
        >
        > U ziet misschien ook MFA vereist als registratie van Azure-apparaten (die wordt gebruikt onder de voor vallen van Azure AD-wachtwoord beveiliging), is geconfigureerd om wereld wijd MFA te vereisen. U kunt dit probleem omzeilen door een ander account te gebruiken dat MFA ondersteunt met een van de twee vorige verificatie modi, of u kunt ook tijdelijk de MFA-vereiste voor de Azure Device Registration versoepelen. Ga hiervoor naar de Azure-beheer Portal en ga vervolgens naar Azure Active Directory, vervolgens op apparaten, vervolgens op Apparaatinstellingen en stel ' multi-factor Authentication vereisen voor het samen voegen van apparaten ' in op Nee. Zorg ervoor dat u deze instelling opnieuw configureert op Ja zodra de registratie is voltooid.
        >
        > U wordt aangeraden MFA-vereisten alleen voor test doeleinden te gebruiken.

       U hoeft momenteel niet de para meter *-ForestCredential* op te geven. deze is gereserveerd voor toekomstige functionaliteit.

   De registratie van de proxy service voor wachtwoord beveiliging is slechts eenmaal in de levens duur van de service nodig. Daarna voert de proxy service automatisch andere nood zakelijk onderhoud uit.

   > [!TIP]
   > De eerste keer dat deze cmdlet wordt uitgevoerd voor een specifieke Azure-Tenant, kan er een merkbaar vertraging optreden. Als er geen fout wordt gerapporteerd, hoeft u zich geen zorgen te maken over deze vertraging.

1. Registreer het forest.
   * U moet de on-premises Active Directory-forest initialiseren met de benodigde referenties om te communiceren met Azure `Register-AzureADPasswordProtectionForest` met behulp van de Power shell-cmdlet. Voor de cmdlet zijn globale beheerders referenties voor uw Azure-Tenant vereist. Er zijn ook on-premises Active Directory Enter prise-beheerders bevoegdheden vereist. Deze stap wordt eenmaal per forest uitgevoerd.

      De `Register-AzureADPasswordProtectionForest` cmdlet ondersteunt de volgende drie verificatie modi.

     * Interactieve verificatie modus:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op Server Core-besturings systemen. Gebruik in plaats daarvan een van de volgende twee verificatie modi. Deze modus kan ook mislukken als verbeterde beveiliging van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te scha kelen, het forest te registreren en vervolgens weer in te scha kelen.  

     * Verificatie modus apparaat-code:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        U kunt de verificatie volt ooien door de weer gegeven instructies op een ander apparaat te volgen.

     * Silent (op wacht woord gebaseerde) verificatie modus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure Multi-Factor Authentication is vereist voor uw account. In dat geval gebruikt u een van de twee voor gaande verificatie modi, of gebruikt u in plaats daarvan een ander account waarvoor MFA niet vereist is.
        >
        > U ziet misschien ook MFA vereist als registratie van Azure-apparaten (die wordt gebruikt onder de voor vallen van Azure AD-wachtwoord beveiliging), is geconfigureerd om wereld wijd MFA te vereisen. U kunt dit probleem omzeilen door een ander account te gebruiken dat MFA ondersteunt met een van de twee vorige verificatie modi, of u kunt ook tijdelijk de MFA-vereiste voor de Azure Device Registration versoepelen. Ga hiervoor naar de Azure-beheer Portal en ga vervolgens naar Azure Active Directory, vervolgens op apparaten, vervolgens op Apparaatinstellingen en stel ' multi-factor Authentication vereisen voor het samen voegen van apparaten ' in op Nee. Zorg ervoor dat u deze instelling opnieuw configureert op Ja zodra de registratie is voltooid.
        >
        > U wordt aangeraden MFA-vereisten alleen voor test doeleinden te gebruiken.

       Deze voor beelden slagen alleen als de gebruiker die momenteel is aangemeld ook een Active Directory domein beheerder is voor het hoofd domein. Als dit niet het geval is, kunt u alternatieve domein referenties opgeven via de para meter *-ForestCredential* .

   > [!NOTE]
   > Als er meerdere proxy servers in uw omgeving zijn geïnstalleerd, maakt het niet uit welke proxy server u gebruikt om het forest te registreren.
   >
   > [!TIP]
   > De eerste keer dat deze cmdlet wordt uitgevoerd voor een specifieke Azure-Tenant, kan er een merkbaar vertraging optreden. Als er geen fout wordt gerapporteerd, hoeft u zich geen zorgen te maken over deze vertraging.

   Registratie van de Active Directory-forest is slechts eenmaal nodig in de levens duur van het forest. Daarna zullen de domein controller agents in het forest automatisch andere nood zakelijke onderhoud uitvoeren. Wanneer `Register-AzureADPasswordProtectionForest` een forest is uitgevoerd, worden aanvullende aanroepen van de cmdlet wel geslaagd, maar zijn ze niet nodig.

   `Register-AzureADPasswordProtectionForest` Hiervoor moet ten minste één domein controller met Windows Server 2012 of later beschikbaar zijn in het domein van de proxy server. De DC-agent software hoeft echter niet te worden geïnstalleerd op domein controllers vóór deze stap.

1. Configureer de proxy service voor wachtwoord beveiliging om te communiceren via een HTTP-proxy.

   Als uw omgeving het gebruik van een specifieke HTTP-proxy vereist om te communiceren met Azure, gebruikt u deze methode: Maak een bestand *AzureADPasswordProtectionProxy. exe. config* in de map%ProgramFiles%\Azure AD Password Protection Proxy\Service. Voeg de volgende inhoud toe:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Als voor uw HTTP-proxy verificatie is vereist, voegt u de *useDefaultCredentials* -code toe:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Vervang `http://yourhttpproxy.com:8080` in beide gevallen door het adres en de poort van uw specifieke http-proxy server.

   Als uw HTTP-proxy is geconfigureerd voor het gebruik van een autorisatie beleid, moet u toegang verlenen tot het Active Directory computer account van de computer die als host fungeert voor de proxy service voor wachtwoord beveiliging.

   U wordt aangeraden de proxy service te stoppen en opnieuw op te starten nadat u het bestand *AzureADPasswordProtectionProxy. exe. config* hebt gemaakt of bijgewerkt.

   De proxy service biedt geen ondersteuning voor het gebruik van specifieke referenties om verbinding te maken met een HTTP-proxy.

1. Optioneel: Configureer de proxy service voor wachtwoord beveiliging om op een specifieke poort te Luis teren.
   * De DC-agent software voor wachtwoord beveiliging op de domein controllers gebruikt RPC over TCP om te communiceren met de proxy service. Standaard luistert de proxy service naar een beschikbaar dynamisch RPC-eind punt. Maar u kunt de service configureren om te Luis teren naar een specifieke TCP-poort, als dit nodig is vanwege de netwerk topologie of firewall vereisten in uw omgeving.
      * <a id="static" /></a>Als u de service wilt configureren om te worden uitgevoerd onder een statische `Set-AzureADPasswordProtectionProxyConfiguration` poort, gebruikt u de cmdlet.

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > U moet de service stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

      * Als u de service wilt configureren voor uitvoering onder een dynamische poort, gebruikt u dezelfde procedure, maar stelt u *StaticPort* terug in op nul:

         ```powershell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > U moet de service stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

   > [!NOTE]
   > Voor de proxy service voor wachtwoord beveiliging moet hand matig opnieuw worden opgestart na een wijziging in de poort configuratie. U hoeft de DC Agent service-software echter niet opnieuw op domein controllers te starten nadat u deze configuratie wijzigingen hebt aangebracht.

   * Als u een query wilt uitvoeren voor de huidige configuratie van de `Get-AzureADPasswordProtectionProxyConfiguration` service, gebruikt u de cmdlet:

      ```powershell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>De DC-Agent service installeren

   Installeer de DC-Agent service voor wachtwoord beveiliging met behulp van het `AzureADPasswordProtectionDCAgentSetup.msi` pakket.

   De software-installatie, of de installatie ervan, vereist een herstart. De reden hiervoor is dat Dll's voor wachtwoord filters alleen worden geladen of verwijderd door het opnieuw opstarten.

   U kunt de DC-Agent service installeren op een computer die nog geen domein controller is. In dit geval wordt de service gestart en uitgevoerd, maar blijft deze inactief totdat de computer is gepromoveerd tot een domein controller.

   U kunt de software-installatie automatiseren met behulp van standaard MSI-procedures. Bijvoorbeeld:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`

   U kunt de `/norestart` vlag weglaten als u het installatie programma de computer liever automatisch opnieuw opstart.

De installatie is voltooid nadat de DC-agent software is geïnstalleerd op een domein controller en die computer opnieuw wordt opgestart. Er is geen andere configuratie vereist of mogelijk.

## <a name="upgrading-the-proxy-agent"></a>De proxy-agent bijwerken

Wanneer er een nieuwere versie van de Azure AD-proxy software voor wachtwoord beveiliging beschikbaar is, wordt de upgrade uitgevoerd door de meest recente `AzureADPasswordProtectionProxySetup.exe` versie van het installatie programma van de software uit te voeren. De nieuwste versie van de software is beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

Het is niet vereist om de huidige versie van de proxy software te verwijderen. het installatie programma voert een in-place upgrade uit. U hoeft niet opnieuw op te starten om de proxy software bij te werken. De software-upgrade kan worden geautomatiseerd met behulp van standaard `AzureADPasswordProtectionProxySetup.exe /quiet`MSI-procedures, bijvoorbeeld:.

De proxy-agent ondersteunt automatische upgrades. Automatische upgrade maakt gebruik van de Microsoft Azure AD connect agent Updater-service die naast de proxy service is geïnstalleerd. Automatische upgrade is standaard ingeschakeld en kan worden in-of uitgeschakeld met de `Set-AzureADPasswordProtectionProxyConfiguration` cmdlet. De huidige instelling kan worden opgevraagd met behulp `Get-AzureADPasswordProtectionProxyConfiguration` van de cmdlet. Micro soft raadt aan dat de automatische upgrade ingeschakeld blijft.

De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om de software versie van alle geïnstalleerde proxy agenten in een forest op te vragen.

## <a name="upgrading-the-dc-agent"></a>De DC-agent bijwerken

Wanneer er een nieuwere versie van de Azure AD-agent software voor wachtwoord beveiliging beschikbaar is, wordt de upgrade uitgevoerd door de nieuwste versie van `AzureADPasswordProtectionDCAgentSetup.msi` het software pakket uit te voeren. De nieuwste versie van de software is beschikbaar in het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=57071).

Het is niet vereist om de huidige versie van de DC-agent software te verwijderen. het installatie programma voert een in-place upgrade uit. Opnieuw opstarten is altijd vereist bij het upgraden van de DC-agent software. dit wordt veroorzaakt door het kern gedrag van Windows. 

De software-upgrade kan worden geautomatiseerd met behulp van standaard `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`MSI-procedures, bijvoorbeeld:.

U kunt de `/norestart` vlag weglaten als u het installatie programma de computer liever automatisch opnieuw opstart.

De `Get-AzureADPasswordProtectionDCAgent` cmdlet kan worden gebruikt om de software versie van alle geïnstalleerde DC-agents in een forest op te vragen.

## <a name="multiple-forest-deployments"></a>Implementaties met meerdere forests

Er zijn geen aanvullende vereisten voor het implementeren van Azure AD-wachtwoord beveiliging op meerdere forests. Elk forest is onafhankelijk geconfigureerd zoals beschreven in de sectie ' implementatie met één forest '. Elke wachtwoord beveiligings proxy kan alleen domein controllers ondersteunen van het forest waaraan deze is gekoppeld. De software voor wachtwoord beveiliging in een forest is niet op de hoogte van wachtwoord beveiligings software die in andere forests is geïmplementeerd, ongeacht Active Directory vertrouwens configuraties.

## <a name="read-only-domain-controllers"></a>Alleen-lezen domein controllers

Wachtwoord wijzigingen/-sets worden niet verwerkt en bewaard op alleen-lezen domein controllers (RODC). Ze worden doorgestuurd naar Beschrijf bare domein controllers. U hoeft de software van de DC-agent niet te installeren op RODC'S.

## <a name="high-availability"></a>Hoge beschikbaarheid

De belangrijkste Beschik baarheid voor wachtwoord beveiliging is de beschik baarheid van proxy servers wanneer de domein controllers in een forest proberen nieuwe beleids regels of andere gegevens van Azure te downloaden. Elke DC-agent maakt gebruik van een eenvoudig Round Robin-stijl algoritme bij het bepalen welke proxy server moet worden aangeroepen. Proxy servers die niet reageren, worden door de agent overgeslagen. Voor de meeste volledig verbonden Active Directory-implementaties met een goede replicatie van de status van de map en SYSVOL-map zijn er twee proxy servers voldoende om Beschik baarheid te garanderen. Dit resulteert in een tijdige down load van nieuwe beleids regels en andere gegevens. U kunt echter wel aanvullende proxy servers implementeren.

Het ontwerp van de DC-agent software vermindert de gang bare problemen die zijn gekoppeld aan hoge Beschik baarheid. De DC-agent onderhoudt een lokale cache van het laatst gedownloade wachtwoord beleid. Zelfs als alle geregistreerde proxy servers niet meer beschikbaar zijn, blijven de DC-agents het wachtwoord beleid in de cache afdwingen. Een redelijke update frequentie voor wachtwoord beleid in een grote implementatie is doorgaans dagen, niet uren of minder. Korte uitval van de proxy servers heeft dus geen invloed op de beveiliging van Azure AD-wacht woorden.

## <a name="next-steps"></a>Volgende stappen

Nu u de services hebt geïnstalleerd die u nodig hebt voor Azure AD-wachtwoord beveiliging op uw on-premises servers, voert u de [configuratie na installatie uit en verzamelt u rapport gegevens](howto-password-ban-bad-on-premises-operations.md) om uw implementatie te volt ooien.

[Conceptueel overzicht van Azure AD-wachtwoord beveiliging](concept-password-ban-bad-on-premises.md)
