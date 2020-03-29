---
title: On-premises Azure AD-wachtwoordbeveiliging implementeren
description: Meer informatie over het plannen en implementeren van Azure AD Password Protection in een on-premises Active Directory Domain Services-omgeving
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: a977eac19128886dd3c379e200f7cb78066a06af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671748"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>On-premises Azure Active Directory Password Protection plannen en implementeren

Gebruikers maken vaak wachtwoorden die veelvoorkomende lokale woorden gebruiken, zoals een school, sportteam of beroemde persoon. Deze wachtwoorden zijn gemakkelijk te raden, en zwak tegen woordenboek-gebaseerde aanvallen. Als u sterke wachtwoorden in uw organisatie wilt afdwingen, biedt Azure Active Directory (Azure AD) Wachtwoordbeveiliging een algemene en aangepaste lijst met geblokkeerde wachtwoorden. Een verzoek om wachtwoordwijziging mislukt als er een overeenkomst is in deze lijst met verboden wachtwoorden.

Als u uw on-premises ACTIVE Directory Domain Services-omgeving (AD DS) wilt beveiligen, u Azure AD-wachtwoordbeveiliging installeren en configureren om te werken met uw on-prem DC. In dit artikel ziet u hoe u de proxyservice Azure AD Password Protection en de Azure AD Password Protection DC-agent installeert en registreert in uw on-premises omgeving.

Zie [Azure AD Password Protection for Se](concept-password-ban-bad-on-premises.md)in n.P. voor meer informatie over de werking van Azure AD Password Protection in een on-premises omgeving.

## <a name="deployment-strategy"></a>Implementatiestrategie

In het volgende diagram ziet u hoe de basiscomponenten van Azure AD Password Protection samenwerken in een on-premises Active Directory-omgeving:

![Hoe azure AD Password Protection-componenten samenwerken](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Het is een goed idee om te bekijken hoe de software werkt voordat u deze implementeert. Zie [Conceptueel overzicht van Azure AD Password Protection](concept-password-ban-bad-on-premises.md)voor meer informatie.

We raden u aan implementaties in *de auditmodus te* starten. De controlemodus is de standaardinstelling, waarin wachtwoorden kunnen worden ingesteld. Wachtwoorden die zouden worden geblokkeerd, worden geregistreerd in het gebeurtenislogboek. Nadat u de proxyservers en DC-agents in de controlemodus hebt geïmplementeerd, controleert u de impact die het wachtwoordbeleid heeft op gebruikers wanneer het beleid wordt afgedwongen.

Tijdens de auditfase vinden veel organisaties dat de volgende situaties van toepassing zijn:

* Ze moeten bestaande operationele processen verbeteren om veiligere wachtwoorden te gebruiken.
* Gebruikers gebruiken vaak onveilige wachtwoorden.
* Ze moeten gebruikers informeren over de komende wijziging in de handhaving van de beveiliging, mogelijke gevolgen voor hen en hoe ze veiligere wachtwoorden kunnen kiezen.

Het is ook mogelijk om een sterkere wachtwoordvalidatie te beïnvloeden voor de implementatievan uw bestaande Active Directory-domeincontroller. We raden aan dat ten minste één DC-promotie en één DC-degradatie plaatsvinden tijdens de evaluatie van de auditperiode om dergelijke problemen aan het licht te brengen. Raadpleeg voor meer informatie de volgende artikelen:

* [Ntdsutil.exe kan geen zwak wachtwoord voor de reparatiemodus van Directory Services instellen](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Replicapromotie voor domeincontrollers mislukt vanwege een zwak wachtwoord voor de reparatiemodus van Directory Services](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Dedegradatie van de domeincontroller mislukt vanwege een zwak lokaal beheerderswachtwoord](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Nadat de functie gedurende een redelijke periode in de controlemodus is uitgevoerd, u de configuratie overschakelen van *Controle* naar *Afdwingen* om veiligere wachtwoorden te vereisen. Extra monitoring in deze tijd is een goed idee.

### <a name="multiple-forest-considerations"></a>Meerdere bosoverwegingen

Er zijn geen aanvullende vereisten voor het implementeren van Azure AD Password Protection in meerdere forests.

Elk forest is onafhankelijk geconfigureerd, zoals beschreven in de volgende sectie om [on-prem Azure AD Password Protection](#download-required-software)te implementeren. Elke Azure AD Password Protection-proxy kan alleen domeincontrollers ondersteunen vanuit het forest waarmee deze is verbonden.

De Azure AD Password Protection-software in elk forest is niet op de hoogte van wachtwoordbeveiligingssoftware die in andere forests wordt geïmplementeerd, ongeacht de vertrouwensconfiguraties van Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Overwegingen voor alleen-lezen domeincontroller

Wachtwoordwijziging of setgebeurtenissen worden niet verwerkt en blijven bestaan op alleen-lezen domeincontrollers (RODC's). In plaats daarvan worden ze doorgestuurd naar beschrijfbare domeincontrollers. U hoeft de Azure AD Password Protection DC-agentsoftware niet te installeren op RODC's.

Verder wordt het niet ondersteund om de azure AD Password Protection-proxyservice uit te voeren op een alleen-lezen domeincontroller.

### <a name="high-availability-considerations"></a>Overwegingen met hoge beschikbaarheid

De belangrijkste zorg voor wachtwoordbeveiliging is de beschikbaarheid van Azure AD Password Protection proxyservers wanneer de DC's in een forest nieuwe beleidsregels of andere gegevens van Azure proberen te downloaden. Elke Azure AD Password Protection DC-agent gebruikt een eenvoudig round-robin-stijl algoritme bij het bepalen welke proxyserver u wilt aanroepen. De agent slaat proxyservers over die niet reageren.

Voor de meeste volledig verbonden Active Directory-implementaties met een gezonde replicatie van zowel de status van de map als de sysvol-map, volstaan twee proxyservers voor Azure AD Password Protection om beschikbaarheid te garanderen. Deze configuratie resulteert in tijdige download van nieuw beleid en andere gegevens. U desgewenst aanvullende Azure AD Password Protection-proxyservers implementeren.

Het ontwerp van de Azure AD Password Protection DC-agentsoftware vermindert de gebruikelijke problemen die gepaard gaan met hoge beschikbaarheid. De DC-agent Azure AD Password Protection onderhoudt een lokale cache van het meest recent gedownloade wachtwoordbeleid. Zelfs als alle geregistreerde proxyservers niet meer beschikbaar zijn, blijven de AZURE AD Password Protection DC-agents hun wachtwoordbeleid in de cache afdwingen.

Een redelijke updatefrequentie voor wachtwoordbeleid in een grote implementatie is meestal dagen, geen uren of minder. Korte uitval van de proxyservers heeft dus geen grote invloed op Azure AD Password Protection.

## <a name="deployment-requirements"></a>Implementatievereisten

Zie azure AD [Password Protection-licentievereisten](concept-password-ban-bad.md#license-requirements)voor informatie over licenties.

De volgende kerneisen zijn van toepassing:

* Alle machines, inclusief domeincontrollers, waarop Azure AD Password Protection-componenten zijn geïnstalleerd, moeten de Universal C Runtime hebben geïnstalleerd.
    * U de runtime krijgen door ervoor te zorgen dat u alle updates van Windows Update hebt. Of je het krijgen in een OS-specifieke update pakket. Zie [Bijwerken voor Universele C-runtime in Windows voor](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows)meer informatie.
* U hebt een account nodig met Active Directory-domeinbeheerdersrechten in het foresthoofddomein om het Active Directory-forest van Windows Server te registreren bij Azure AD.
* De key distribution Service moet zijn ingeschakeld op alle domeincontrollers in het domein waarop Windows Server 2012 wordt uitgevoerd. Standaard is deze service ingeschakeld via handmatige triggerstart.
* Netwerkconnectiviteit moet bestaan tussen ten minste één domeincontroller in elk domein en ten minste één server die de proxyservice host voor Azure AD Password Protection. Met deze connectiviteit moet de domeincontroller toegang hebben tot RPC-eindpuntmapperpoort 135 en de RPC-serverpoort op de proxyservice.
    * Standaard is de RPC-serverpoort een dynamische RPC-poort, maar deze kan worden geconfigureerd om een statische poort te [gebruiken.](#static)
* Alle machines waarop de Azure AD Password Protection Proxy-service is geïnstalleerd, moeten netwerktoegang hebben tot de volgende eindpunten:

    |**Eindpunt**|**Doel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Verificatieaanvragen|
    |`https://enterpriseregistration.windows.net`|Azure AD Password Protection-functionaliteit|

### <a name="azure-ad-password-protection-dc-agent"></a>Azure AD Password Protection DC-agent

De volgende vereisten zijn van toepassing op de DC-agent Azure AD Password Protection:

* Alle machines waarop de Azure AD Password Protection DC-agentsoftware wordt geïnstalleerd, moeten Windows Server 2012 of hoger uitvoeren.
    * Het Active Directory-domein of -forest hoeft zich niet op Het domeinfunctionaliteitsniveau (Windows Server 2012- of forestfunctionaliteitsniveau( FFL) te bevinden. Zoals vermeld in [Design Principles,](concept-password-ban-bad-on-premises.md#design-principles)is er geen minimum DFL of FFL vereist voor zowel de DC-agent of proxy software uit te voeren.
* Alle machines waarop de DC-agent Azure AD Password Protection dc draait, moet .NET 4.5 hebben geïnstalleerd.
* Elk Active Directory-domein waarop de DC-agentservice azure AD Password Protection wordt uitgevoerd, moet DFSR (Distributed File System Replication) gebruiken voor sysvol-replicatie.
   * Als uw domein DFSR nog niet gebruikt, moet u migreren voordat u Azure AD-wachtwoordbeveiliging installeert. Zie [SYSVOL-replicatiemigratiehandleiding: FRS naar DFS-replicatie](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10)) voor meer informatie

    > [!WARNING]
    > De Azure AD Password Protection DC-agentsoftware wordt momenteel geïnstalleerd op domeincontrollers in domeinen die frs (de voorgangertechnologie tot DFSR) nog steeds gebruiken voor sysvol-replicatie, maar de software werkt niet goed in deze omgeving.
    >
    > Extra negatieve bijwerkingen zijn afzonderlijke bestanden die niet worden gerepliceerd en sysvol-herstelprocedures lijken te slagen, maar slagen er in stilte niet in om alle bestanden te repliceren.
    >
    > Migreer uw domein om DFSR zo snel mogelijk te gebruiken, zowel voor de inherente voordelen van DFSR als om de implementatie van Azure AD Password Protection te deblokkeren. Toekomstige versies van de software worden automatisch uitgeschakeld wanneer ze worden uitgevoerd in een domein dat frs nog steeds gebruikt.

### <a name="azure-ad-password-protection-proxy-service"></a>Proxyservice Azure AD-wachtwoordbeveiliging

De volgende vereisten zijn van toepassing op de proxyservice Azure AD Password Protection:

* Alle machines waarop de proxyservice Azure AD Password Protection wordt geïnstalleerd, moeten Windows Server 2012 R2 of hoger uitvoeren.

    > [!NOTE]
    > De proxyservice voor Azure AD Password Protection is een verplichte vereiste voor het implementeren van Azure AD Password Protection, ook al heeft de domeincontroller mogelijk uitgaande directe internetverbinding.

* Alle machines waarop de proxyservice Azure AD Password Protection is geïnstalleerd, moeten .NET 4.7 hebben geïnstalleerd.
    * .NET 4.7 moet al worden geïnstalleerd op een volledig bijgewerkte Windows Server. Download en voer indien nodig de installer te downloaden en uit te voeren die is gevonden bij [De .NET Framework 4.7 offline installer voor Windows.](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows)
* Alle machines die de proxyservice Azure AD Password Protection hosten, moeten zijn geconfigureerd om domeincontrollers de mogelijkheid te bieden zich aan te melden bij de proxyservice. Deze mogelijkheid wordt beheerd via de bevoegdheidstoewijzing "Toegang tot deze computer vanaf het netwerk".
* Alle machines die de proxyservice Azure AD Password Protection hosten, moeten zijn geconfigureerd om uitgaand TLS 1.2 HTTP-verkeer toe te staan.
* Een *Global Administrator-account* om de proxyservice azure AD Password Protection en forest te registreren bij Azure AD.
* Netwerktoegang moet zijn ingeschakeld voor de set poorten en URL's die zijn opgegeven in de [installatieprocedures voor](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment)de toepassingsproxy .

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Vereisten voor Microsoft Azure AD Connect Agent Updater

De Microsoft Azure AD Connect Agent Updater-service is naast de Azure AD Password Protection Proxy-service geïnstalleerd. Aanvullende configuratie is vereist om de Microsoft Azure AD Connect Agent Updater-service te laten functioneren:

* Als uw omgeving een HTTP-proxyserver gebruikt, volgt u de richtlijnen die zijn opgegeven in [Werken met bestaande on-premises proxyservers.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)
* De Microsoft Azure AD Connect Agent Updater-service vereist ook de TLS 1.2-stappen die zijn opgegeven in [TLS-vereisten.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#tls-requirements)

> [!WARNING]
> Azure AD Password Protection proxy en Azure AD Application Proxy installeren verschillende versies van de Microsoft Azure AD Connect Agent Updater-service, daarom verwijzen de instructies naar application proxy-inhoud. Deze verschillende versies zijn niet compatibel wanneer ze naast elkaar worden geïnstalleerd, dus het wordt niet aanbevolen om Azure AD Password Protection Proxy en Application Proxy op dezelfde machine te installeren.

## <a name="download-required-software"></a>Vereiste software downloaden

Er zijn twee vereiste installateurs voor een on-premises Azure AD Password Protection-implementatie:

* Azure AD Password Protection DC-agent *(AzureADPasswordProtectionDCAgentSetup.msi*)
* Azure AD Password Protection proxy (AzureADPasswordProtectionProxySetup.exe ) Azure AD Password Protection proxy (AzureADPasswordProtectionProxySetup.exe ) Azure AD Password Protection proxy *(AzureADPasswordProtectionProxySetup.exe*) Azure AD

Download beide installateurs uit het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57071)

## <a name="install-and-configure-the-proxy-service"></a>De proxyservice installeren en configureren

De proxyservice Azure AD Password Protection bevindt zich meestal op een lidserver in uw on-premises AD DS-omgeving. Eenmaal geïnstalleerd, communiceert de proxyservice Azure AD Password Protection met Azure AD om een kopie van de algemene en door de klant geblokkeerde wachtwoordlijsten voor uw Azure AD-tenant te onderhouden.

In het volgende gedeelte installeert u de Azure AD Password Protection DC-agents op domeincontrollers in uw on-premises AD DS-omgeving. Deze DC-agents communiceren met de proxyservice om de nieuwste verboden wachtwoordlijsten te krijgen voor gebruik bij het verwerken van gebeurtenissen met wachtwoordwijziging binnen het domein.

Kies een of meer servers om de proxyservice Azure AD Password Protection te hosten. Voor de server(s) gelden de volgende overwegingen:

* Elke dergelijke service kan slechts wachtwoordbeleid voor één forest bieden. De hostmachine moet worden verbonden met een domein in dat forest. Root- en onderliggende domeinen worden beide ondersteund. U hebt netwerkconnectiviteit nodig tussen ten minste één DC in elk domein van het forest en de wachtwoordbeveiligingsmachine.
* U de proxyservice Azure AD Password Protection uitvoeren op een domeincontroller om te testen, maar die domeincontroller vereist vervolgens internetverbinding. Deze connectiviteit kan een beveiligingsprobleem zijn. We raden deze configuratie alleen aan om te testen.
* We raden ten minste twee Azure AD Password Protection proxy servers aan voor redundantie, zoals opgemerkt in het vorige gedeelte over overwegingen met [hoge beschikbaarheid.](#high-availability-considerations)
* De proxyservice Azure AD Password Protection wordt niet ondersteund voor het uitvoeren van de proxyservice Azure AD Password Protection op een alleen-lezen domeincontroller.

Voer de volgende stappen uit om de proxyservice Azure AD Password Protection te installeren:

1. Als u de proxyservice Azure AD `AzureADPasswordProtectionProxySetup.exe` Password Protection wilt installeren, voert u het software-installatieprogramma uit.

    De software-installatie vereist geen herstart en kan worden geautomatiseerd met behulp van standaard MSI-procedures, zoals in het volgende voorbeeld:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > De Windows Firewall-service moet worden `AzureADPasswordProtectionProxySetup.exe` uitgevoerd voordat u het pakket installeert om een installatiefout te voorkomen.
    >
    > Als Windows Firewall is geconfigureerd om niet uit te voeren, moet de tijdelijke oplossing de Firewall-service tijdelijk inschakelen en uitvoeren tijdens de installatie. De proxysoftware heeft geen specifieke afhankelijkheid van Windows Firewall na installatie.
    >
    > Als u een firewall van derden gebruikt, moet deze nog steeds worden geconfigureerd om aan de implementatievereisten te voldoen. Deze omvatten het toestaan van inkomende toegang tot poort 135 en de proxy RPC-serverpoort. Zie het vorige gedeelte over [implementatievereisten](#deployment-requirements)voor meer informatie .

1. De azure AD Password Protection proxy software `AzureADPasswordProtection`bevat een nieuwe PowerShell module, . De volgende stappen worden uitgevoerd verschillende cmdlets van deze PowerShell module.

    Als u deze module wilt gebruiken, opent u een PowerShell-venster als beheerder en importeert u de nieuwe module als volgt:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Als u wilt controleren of de proxyservice Azure AD Password Protection wordt uitgevoerd, gebruikt u de volgende PowerShell-opdracht:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Het resultaat moet een **status** van *het uitvoeren*weergeven .

1. De proxyservice wordt uitgevoerd op de machine, maar heeft geen referenties om te communiceren met Azure AD. Registreer de Azure AD Password Protection proxy `Register-AzureADPasswordProtectionProxy` server met Azure AD met behulp van de cmdlet.

    Deze cmdlet vereist globale beheerdersreferenties voor uw Azure-tenant. U hebt ook on-premises Active Directory-domeinbeheerdersbevoegdheden nodig in het foresthoofddomein. Deze cmdlet moet ook worden uitgevoerd met een account met lokale beheerdersbevoegdheden:

    Nadat deze opdracht eenmaal is geslaagd voor een Azure AD Password Protection-proxyservice, slagen extra aanroepingen ervan, maar zijn niet nodig.

    De `Register-AzureADPasswordProtectionProxy` cmdlet ondersteunt de volgende drie verificatiemodi. De eerste twee modi ondersteunen Azure Multi-Factor Authentication, maar de derde modus niet.

    > [!TIP]
    > Er kan een merkbare vertraging optreden voordat deze cmdlet voor de eerste keer wordt voltooid voor een specifieke Azure-tenant. Tenzij er een storing wordt gemeld, hoeft u zich geen zorgen te maken over deze vertraging.

     * Interactieve verificatiemodus:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op besturingssystemen van Server Core. Gebruik in plaats daarvan een van de volgende verificatiemodi. Deze modus kan ook mislukken als de verbeterde beveiligingsconfiguratie van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te schakelen, de proxy te registreren en deze vervolgens opnieuw in te schakelen.

     * Verificatiemodus voor apparaatcodes:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Wanneer u wordt gevraagd, volgt u de koppeling om een webbrowser te openen en de verificatiecode in te voeren.

     * Stille (op wachtwoorden gebaseerde) verificatiemodus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure Multi-Factor Authentication vereist is voor uw account. Gebruik in dat geval een van de vorige twee verificatiemodi of gebruik in plaats daarvan een ander account waarvoor geen MFA vereist is.
        >
        > U ook MFA zien die vereist is als Azure Device Registration (die wordt gebruikt onder de dekking en Azure AD Password Protection) is geconfigureerd om mfa wereldwijd te vereisen. Als u deze vereiste wilt omzeilen, u een ander account gebruiken dat MFA ondersteunt met een van de vorige twee verificatiemodi, of u ook de MFA-vereiste voor Azure-apparaatregistratie tijdelijk versoepelen.
        >
        > Als u deze wijziging wilt aanbrengen, zoekt en selecteert u **Azure Active Directory** in de Azure-portal en selecteert u Vervolgens Apparaten > **apparaatinstellingen**. **Stel Multi-Factor Auth in om apparaten aan** te sluiten op *Nr.* Zorg ervoor dat u deze instelling opnieuw configureert naar *Ja* zodra de registratie is voltooid.
        >
        > Wij raden aan om mfa-vereisten alleen voor testdoeleinden te omzeilen.

    U hoeft momenteel niet de parameter *ForestCredential* op te geven, die is gereserveerd voor toekomstige functionaliteit.

    Registratie van de proxyservice Azure AD Password Protection is slechts één keer nodig in de levensduur van de service. Daarna voert de proxyservice Azure AD Password Protection automatisch elk ander noodzakelijk onderhoud uit.

1. Registreer nu het on-premises Active Directory-forest met de benodigde referenties om met Azure te communiceren met de `Register-AzureADPasswordProtectionForest` PowerShell-cmdlet.

    > [!NOTE]
    > Als er meerdere Azure AD Password Protection-proxyservers in uw omgeving zijn geïnstalleerd, maakt het niet uit welke proxyserver u gebruikt om het forest te registreren.

    De cmdlet vereist globale beheerdersreferenties voor uw Azure-tenant. U moet deze cmdlet ook uitvoeren met een account met lokale beheerdersbevoegdheden. Hiervoor zijn ook on-premises Active Directory Enterprise Administrator-bevoegdheden vereist. Deze stap wordt één keer per bos uitgevoerd.

    De `Register-AzureADPasswordProtectionForest` cmdlet ondersteunt de volgende drie verificatiemodi. De eerste twee modi ondersteunen Azure Multi-Factor Authentication, maar de derde modus niet.

    > [!TIP]
    > Er kan een merkbare vertraging optreden voordat deze cmdlet voor de eerste keer wordt voltooid voor een specifieke Azure-tenant. Tenzij er een storing wordt gemeld, hoeft u zich geen zorgen te maken over deze vertraging.

     * Interactieve verificatiemodus:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Deze modus werkt niet op besturingssystemen van Server Core. Gebruik in plaats daarvan een van de volgende twee verificatiemodi. Deze modus kan ook mislukken als de verbeterde beveiligingsconfiguratie van Internet Explorer is ingeschakeld. De tijdelijke oplossing is om die configuratie uit te schakelen, het forest te registreren en deze vervolgens opnieuw in te schakelen.  

     * Verificatiemodus voor apparaatcodes:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Wanneer u wordt gevraagd, volgt u de koppeling om een webbrowser te openen en de verificatiecode in te voeren.

     * Stille (op wachtwoorden gebaseerde) verificatiemodus:

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Deze modus mislukt als Azure Multi-Factor Authentication vereist is voor uw account. Gebruik in dat geval een van de vorige twee verificatiemodi of gebruik in plaats daarvan een ander account waarvoor geen MFA vereist is.
        >
        > U ook MFA zien die vereist is als Azure Device Registration (die wordt gebruikt onder de dekking en Azure AD Password Protection) is geconfigureerd om mfa wereldwijd te vereisen. Als u deze vereiste wilt omzeilen, u een ander account gebruiken dat MFA ondersteunt met een van de vorige twee verificatiemodi, of u ook de MFA-vereiste voor Azure-apparaatregistratie tijdelijk versoepelen.
        >
        > Als u deze wijziging wilt aanbrengen, zoekt en selecteert u **Azure Active Directory** in de Azure-portal en selecteert u Vervolgens Apparaten > **apparaatinstellingen**. **Stel Multi-Factor Auth in om apparaten aan** te sluiten op *Nr.* Zorg ervoor dat u deze instelling opnieuw configureert naar *Ja* zodra de registratie is voltooid.
        >
        > Wij raden aan om mfa-vereisten alleen voor testdoeleinden te omzeilen.

       Deze voorbeelden slagen alleen als de momenteel aangemelde gebruiker ook een Active Directory-domeinbeheerder voor het hoofddomein is. Als dit niet het geval is, u alternatieve domeinreferenties leveren via de parameter *-ForestCredential.*

    Registratie van het Active Directory-forest is slechts één keer nodig in de levensduur van het forest. Daarna voeren de DC-agents azure AD Password Protection in het forest automatisch ander noodzakelijk onderhoud uit. Na `Register-AzureADPasswordProtectionForest` looppas met succes voor een bos, slagen de extra aanroepingen van cmdlet, maar zijn onnodig.
    
    Om `Register-AzureADPasswordProtectionForest` te slagen moet ten minste één DC met Windows Server 2012 of hoger beschikbaar zijn in het domein van de Azure AD Password Protection proxy server. De Azure AD Password Protection DC-agentsoftware hoeft vóór deze stap niet op domeincontrollers te worden geïnstalleerd.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>De proxyservice configureren om te communiceren via een HTTP-proxy

Als voor uw omgeving het gebruik van een specifieke HTTP-proxy vereist is om met Azure te communiceren, gebruikt u de volgende stappen om de Azure AD Password Protection-service te configureren.

Maak een *AzureADPasswordProtectionProxy.exe.config-bestand* in de `%ProgramFiles%\Azure AD Password Protection Proxy\Service` map. Voeg de volgende inhoud toe:

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

Als uw HTTP-proxy verificatie vereist, voegt u de *tag useDefaultCredentials* toe:

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

Vervang in `http://yourhttpproxy.com:8080` beide gevallen het adres en de poort van uw specifieke HTTP-proxyserver.

Als uw HTTP-proxy is geconfigureerd om een autorisatiebeleid te gebruiken, moet u toegang verlenen tot het Active Directory-computeraccount van de machine die de proxyservice host voor wachtwoordbeveiliging.

We raden u aan de proxyservice Azure AD Password Protection te stoppen en opnieuw te starten nadat u het *AzureADPasswordProtectionProxy.exe.config-bestand* hebt gemaakt of bijgewerkt.

De proxyservice biedt geen ondersteuning voor het gebruik van specifieke referenties voor het maken van verbinding met een HTTP-proxy.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>De proxyservice configureren om te luisteren op een specifieke poort

De Azure AD Password Protection DC-agentsoftware gebruikt RPC via TCP om te communiceren met de proxyservice. Standaard luistert de proxyservice Azure AD Password Protection op elk beschikbaar dynamisch RPC-eindpunt. U de service zo configureren dat deze op een specifieke TCP-poort kan luisteren, indien nodig vanwege netwerktopologie of firewallvereisten in uw omgeving.

<a id="static" /></a>Als u wilt configureren dat de service `Set-AzureADPasswordProtectionProxyConfiguration` onder een statische poort wordt uitgevoerd, gebruikt u de cmdlet als volgt:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> U moet de proxyservice Azure AD Password Protection stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

Als u wilt configureren dat de service onder een dynamische poort wordt uitgevoerd, gebruikt u dezelfde procedure, maar stelt *u StaticPort* terug op nul:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> U moet de proxyservice Azure AD Password Protection stoppen en opnieuw starten om deze wijzigingen van kracht te laten worden.

De proxyservice Azure AD Password Protection vereist een handmatige herstart na elke wijziging in de poortconfiguratie. U hoeft de Azure AD Password Protection DC-agentservice op domeincontrollers niet opnieuw te starten nadat u deze configuratiewijzigingen hebt aangebracht.

Als u wilt zoeken naar de `Get-AzureADPasswordProtectionProxyConfiguration` huidige configuratie van de service, gebruikt u de cmdlet zoals weergegeven in het volgende voorbeeld

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

In de volgende voorbeelduitvoer wordt weergegeven dat de proxyservice Azure AD Password Protection een dynamische poort gebruikt:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>De DC-agentservice installeren

Als u de DC-agentservice Azure `AzureADPasswordProtectionDCAgentSetup.msi` AD Password Protection-agent wilt installeren, voert u het pakket uit.

U de software-installatie automatiseren met behulp van standaard MSI-procedures, zoals in het volgende voorbeeld wordt weergegeven:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

De `/norestart` vlag kan worden weggelaten als u de installatieinstallatie liever automatisch de machine laat herstarten.

De software-installatie, of het verwijderen van de installatie, vereist een herstart. Deze vereiste komt omdat wachtwoordfilter DLL's alleen worden geladen of gelost door een herstart.

De installatie van on-prem Azure AD Password Protection is voltooid nadat de DC-agentsoftware is geïnstalleerd op een domeincontroller en die computer opnieuw is opgestart. Er is geen andere configuratie vereist of mogelijk. Gebeurtenissen voor wachtwoordwijzigingen ten opzichte van de on-prem DC's gebruiken de geconfigureerde lijsten met verboden wachtwoorden van Azure AD.

Zie [On-premises Azure AD](howto-password-ban-bad-on-premises-operations.md)Password Protection inschakelen om azure AD-wachtwoordbeveiliging in te schakelen vanuit de Azure-portal of aangepaste geblokkeerde wachtwoorden te configureren.

> [!TIP]
> U de AZURE AD Password Protection DC-agent installeren op een machine die nog geen domeincontroller is. In dit geval start en wordt de service gestart en uitgevoerd, maar blijft deze inactief totdat de machine wordt gepromoot als domeincontroller.

## <a name="upgrading-the-proxy-service"></a>De proxyservice upgraden

De proxyservice Azure AD Password Protection ondersteunt automatische upgrade. Automatische upgrade maakt gebruik van de Microsoft Azure AD Connect Agent Updater-service, die naast de proxyservice is geïnstalleerd. De automatische upgrade is standaard ingeschakeld en kan met `Set-AzureADPasswordProtectionProxyConfiguration` de cmdlet worden ingeschakeld of uitgeschakeld.

De huidige instelling kan worden `Get-AzureADPasswordProtectionProxyConfiguration` opgevraagd met de cmdlet. We raden u aan dat de automatische upgrade-instelling altijd is ingeschakeld.

De `Get-AzureADPasswordProtectionProxy` cmdlet kan worden gebruikt om de softwareversie van alle momenteel geïnstalleerde Azure AD Password Protection-proxyservers in een forest op te vragen.

### <a name="manual-upgrade-process"></a>Handmatig upgradeproces

Een handmatige upgrade wordt uitgevoerd door `AzureADPasswordProtectionProxySetup.exe` het uitvoeren van de nieuwste versie van de software installer. De nieuwste versie van de software is beschikbaar in het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57071)

Het is niet verplicht om de huidige versie van de Azure AD Password Protection-proxyservice te verwijderen - het installatieprogramma voert een in-place upgrade uit. Er moet geen reboot nodig zijn bij het upgraden van de proxyservice. De software-upgrade kan worden geautomatiseerd met `AzureADPasswordProtectionProxySetup.exe /quiet`behulp van standaard MSI-procedures, zoals .

## <a name="upgrading-the-dc-agent"></a>Upgraden van de DC-agent

Wanneer een nieuwere versie van de Azure AD Password Protection DC-agentsoftware beschikbaar is, `AzureADPasswordProtectionDCAgentSetup.msi` wordt de upgrade uitgevoerd door de nieuwste versie van het softwarepakket uit te voeren. De nieuwste versie van de software is beschikbaar in het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=57071)

Het is niet nodig om de huidige versie van de DC-agentsoftware te verwijderen - de installateur voert een in-place upgrade uit. Een reboot is altijd vereist bij het upgraden van de DC-agent software - deze vereiste wordt veroorzaakt door het belangrijkste Windows-gedrag.

De software-upgrade kan worden geautomatiseerd met `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart`behulp van standaard MSI-procedures, zoals .

U de `/norestart` vlag weglaten als u de installatieinstallatie verkiest de machine automatisch opnieuw op te starten.

De `Get-AzureADPasswordProtectionDCAgent` cmdlet kan worden gebruikt om de softwareversie van alle momenteel geïnstalleerde Azure AD Password Protection DC-agents in een forest op te vragen.

## <a name="next-steps"></a>Volgende stappen

Nu u de services hebt geïnstalleerd die u nodig hebt voor Azure AD Password Protection op uw on-premises servers, [schakelt u azure AD-wachtwoordbeveiliging in de Azure-portal in](howto-password-ban-bad-on-premises-operations.md) om uw implementatie te voltooien.
