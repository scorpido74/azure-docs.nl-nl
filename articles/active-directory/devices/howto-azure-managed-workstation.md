---
title: Azure-beheerde werkstations implementeren - Azure Active Directory
description: Meer informatie over het implementeren van beveiligde, door Azure beheerde werkstations om het risico op inbreuk als gevolg van verkeerde configuratie of compromissen te verminderen.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: frasim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d02b0299b6267fdd9d880d5bc0fe8c93d0edadc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672606"
---
# <a name="deploy-a-secure-azure-managed-workstation"></a>Een veilig, door Azure beheerd werkstation implementeren

Nu u [beveiligde werkstations begrijpt,](concept-azure-managed-workstation.md)is het tijd om het implementatieproces te starten. Met deze richtlijnen gebruikt u gedefinieerde profielen om een werkstation te maken dat vanaf het begin veiliger is.

![Implementatie van een beveiligd werkstation](./media/howto-azure-managed-workstation/deploying-secure-workstations.png)

Selecteer een profiel voordat u de oplossing implementeert. U meerdere profielen tegelijk gebruiken in een implementatie en deze toewijzen met tags of groepen.

> [!NOTE]
> Pas een van de profielen toe naar behoefte aan uw vereisten. U naar een ander profiel gaan door het toe te stellen in Microsoft Intune.

| Profiel | Laag | Verbeterd | Hoog | Gespecialiseerd | Beveiligd | Geïsoleerd |
| :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| Gebruiker in Azure AD | Ja | Ja | Ja | Ja | Ja | Ja |
| Intune-beheerd | Ja | Ja | Ja | Ja | Ja | Ja |
| Apparaat - Azure AD geregistreerd | Ja |  |  |  |  | |   |
| Apparaat - Azure AD is lid geworden |   | Ja | Ja | Ja | Ja | Ja |
| Intune security baseline toegepast |   | Ja <br> (Verbeterd) | Ja <br> (HighSecurity) | Ja <br> (NCSC) | Ja <br> (Beveiligd) | N.v.t. |
| Hardware voldoet aan veilige Windows 10-normen |   | Ja | Ja | Ja | Ja | Ja |
| Microsoft Defender ATP ingeschakeld |   | Ja  | Ja | Ja | Ja | Ja |
| Verwijdering van beheerdersrechten |   |   | Ja  | Ja | Ja | Ja |
| Implementatie met Microsoft Autopilot |   |   | Ja  | Ja | Ja | Ja |
| Apps die alleen door Intune zijn geïnstalleerd |   |   |   | Ja | Ja |Ja |
| URL's die zijn beperkt tot goedgekeurde lijst |   |   |   | Ja | Ja |Ja |
| Internet geblokkeerd (binnenkomend/uitgaand) |   |   |   |  |  |Ja |

> [!NOTE]
> In de beveiligde workstationbegeleiding worden **begeleidingsapparaten** toegewezen met profielen en beleidsregels. Gebruikers hebben het beleid niet rechtstreeks op hen toegepast, waardoor het delen van apparaten (gedeelde apparaten) van kracht is. Als een beveiligd werkstation niet wordt gedeeld in uw implementatie of als er een individueel gebruikersbeleid nodig is, kan toewijzing van de gebruikersbeleidsprofielen aan de gebruiker en het apparaat worden toegewezen. 

## <a name="license-requirements"></a>Licentievereisten

De concepten die in deze handleiding worden behandeld, gaan ervan uit dat u Microsoft 365 Enterprise E5 of een gelijkwaardige SKU hebt. Sommige van de aanbevelingen in deze gids kunnen worden uitgevoerd met lagere SKU's. Zie [Microsoft 365 Enterprise-licenties voor](https://www.microsoft.com/licensing/product-licensing/microsoft-365-enterprise)meer informatie.

Als u het inrichten van licenties voor licenties voor uw gebruikers wilt automatiseren, u [groepslicenties](../users-groups-roles/licensing-groups-assign.md) voor uw gebruikers overwegen.

## <a name="azure-active-directory-configuration"></a>Azure Active Directory-configuratie

Azure Active Directory (Azure AD) beheert gebruikers, groepen en apparaten voor uw beheerderswerkstations. Schakel identiteitsservices en -functies in met een [beheerdersaccount](../users-groups-roles/directory-assign-admin-roles.md).

Wanneer u het beveiligde account voor werkstationbeheerders maakt, stelt u het account bloot aan uw huidige werkstation. Zorg ervoor dat u een bekend veilig apparaat gebruikt om deze eerste configuratie en alle algemene configuratie uit te voeren. Om de blootstelling aan aanvallen voor de eerste keer ervaring te verminderen, overweeg dan het volgen van de [richtlijnen om malware-infecties te voorkomen.](/windows/security/threat-protection/intelligence/prevent-malware-infection)

Vereist multi-factor authenticatie, althans voor uw beheerders. Zie [Mfa implementeren in de cloud](../authentication/howto-mfa-getstarted.md) voor implementatierichtlijnen.

### <a name="azure-ad-users-and-groups"></a>Azure AD-gebruikers en -groepen

1. Blader vanuit de Azure-portal naar **Azure Active Directory** > **Users** > **New user**.
1. Maak uw apparaatbeheerder door de stappen in de [zelfstudie van de gebruiker maken](/Intune/quickstart-create-user)te volgen.
1. Voer het volgende in:

   * **Naam** - Secure Workstation Administrator
   * **Gebruikersnaam** - `secure-ws-admin@identityitpro.com`
   * **Directoryrol** - **Beperkte beheerder** en selecteer de rol **Intune Administrator.**

1. Selecteer **Maken**.

Vervolgens maakt u twee groepen: gebruikers van workstations en workstationapparaten.

Blader vanuit de Azure-portal naar de**nieuwe groep** **Azure Active Directory** > **Groups** > .

1. Voor de groep werkstationsgebruikers u [groepslicenties](../users-groups-roles/licensing-groups-assign.md) configureren om de inrichting van licenties voor gebruikers te automatiseren.
1. Voer voor de groep gebruikers van workstations het volgende in:

   * **Groepstype** - Beveiliging
   * **Groepsnaam** - Gebruikers van beveiligde werkstations
   * **Lidmaatschapstype** - Toegewezen

1. Voeg de gebruiker van uw beveiligde werkstationbeheerder toe:`secure-ws-admin@identityitpro.com`
1. U andere gebruikers toevoegen die beveiligde werkstations beheren.
1. Selecteer **Maken**.
1. Voer voor de groep workstationapparaten het volgende in:

   * **Groepstype** - Beveiliging
   * **Groepsnaam** - Beveiligde werkstations
   * **Lidmaatschapstype** - Toegewezen

1. Selecteer **Maken**.

### <a name="azure-ad-device-configuration"></a>Azure AD-apparaatconfiguratie

#### <a name="specify-who-can-join-devices-to-azure-ad"></a>Opgeven wie apparaten kan deelnemen aan Azure AD

Configureer de instelling van uw apparaten in Active Directory zodat uw beheerbeveiligingsgroep apparaten kan aansluiten bij uw domein. Ga als volgt te werk om deze instelling te configureren vanuit de Azure-portal:

1. Ga naar **azure Active Directory** > **Devices** > **Device-instellingen**.
1. Kies **Geselecteerd** onder **Gebruikers kunnen apparaten deelnemen aan Azure AD**en selecteer vervolgens de groep 'Veilige werkstationgebruikers'.

#### <a name="removal-of-local-admin-rights"></a>Verwijdering van lokale beheerdersrechten

Deze methode vereist dat gebruikers van de VIP-, DevOps- en secure-level werkstations geen beheerdersrechten op hun machines hebben. Ga als volgt te werk om deze instelling te configureren vanuit de Azure-portal:

1. Ga naar **azure Active Directory** > **Devices** > **Device-instellingen**.
1. Selecteer **Geen** onder **Extra lokale beheerders op Azure AD-apparaten**.

#### <a name="require-multi-factor-authentication-to-join-devices"></a>Meervoudige verificatie vereisen om apparaten aan te sluiten

Ga als volgende over het proces om apparaten te verbinden met Azure AD verder te versterken:

1. Ga naar **azure Active Directory** > **Devices** > **Device-instellingen**.
1. Selecteer **Ja** onder **Multifactor Auth vereisen om apparaten aan te sluiten**.
1. Selecteer **Opslaan**.

#### <a name="configure-mobile-device-management"></a>Beheer van mobiele apparaten configureren

Vanuit de Azure-portal:

1. Blader naar **Azure Active Directory** > **Mobility (MDM en MAM)** > **Microsoft Intune**.
1. Wijzig de **mdm-gebruikersbereikinstelling** in **Alles**.
1. Selecteer **Opslaan**.

Met deze stappen u elk apparaat beheren met Intune. Zie [Snelstart inafstemmen: Automatische inschrijving instellen voor Windows 10-apparaten](/Intune/quickstart-setup-auto-enrollment)voor meer informatie. U maakt in een volgende stap intune-configuratie- en nalevingsbeleid.

#### <a name="azure-ad-conditional-access"></a>Voorwaardelijke toegang voor Azure AD

Azure AD Conditional Access kan helpen bevoorrechte beheertaken te beperken tot compatibele apparaten. Vooraf gedefinieerde leden van de groep **Secure Workstation Users** moeten multi-factor authenticatie uitvoeren wanneer ze zich aanmelden bij cloudtoepassingen. Een goede praktijk is om noodtoegangsaccounts uit te sluiten van het beleid. Zie [Accounts voor noodtoegang beheren in Azure AD](../users-groups-roles/directory-emergency-access.md)voor meer informatie.

## <a name="intune-configuration"></a>Intune-configuratie

### <a name="configure-enrollment-status"></a>Inschrijvingsstatus configureren

Het is belangrijk om ervoor te zorgen dat uw beveiligde werkstation een vertrouwd schoon apparaat is. Bij de aankoop van nieuwe apparaten u erop aandringen dat ze in de fabriek zijn ingesteld op [Windows 10 Pro in de S-modus](/Windows/deployment/Windows-10-pro-in-s-mode), wat de blootstelling aan kwetsbaarheden tijdens het beheer van de supply chain beperkt. Nadat u een apparaat van uw leverancier hebt ontvangen, u Autopilot gebruiken om het te wijzigen in de S-modus. De volgende richtlijnen geven details over het toepassen van het transformatieproces.

Om ervoor te zorgen dat apparaten volledig zijn geconfigureerd voor gebruik, biedt Intune een middel om het apparaatgebruik te **blokkeren totdat alle apps en profielen zijn geïnstalleerd.**

Vanuit de **Azure-portal:**

1. Ga naar **Microsoft Intune** > **Device inschrijving** > **Windows inschrijving** > **inschrijving Sinschrijving Status Page** > **Default** > **.**
1. De **installatievoortgang van app-profiel weergeven instellen** op **Ja**.
1. Stel **het gebruik van blokkeringsapparaten in totdat alle apps en profielen zijn geïnstalleerd op** **Ja.**

### <a name="create-an-autopilot-deployment-profile"></a>Een Autopilot-implementatieprofiel maken

Nadat u een apparaatgroep hebt gemaakt, moet u een implementatieprofiel maken om de Autopilot-apparaten te configureren.

In-tune in de Azure-portal:

1. Selecteer **Apparaatinschrijving** > **Windows-inschrijvingsprofielen** > **Maak** > **profiel aan**.
1. Voer het volgende in:

   * Naam - **Veilig implementatieprofiel voor werkstations**.
   * Beschrijving - **Implementatie van beveiligde werkstations**.
   * Stel **Alle doelapparaten converteren naar Autopilot** in op **Ja**. Deze instelling zorgt ervoor dat alle apparaten in de lijst worden ingeschreven bij de Autopilot-implementatieservice. Het kan 48 uur duren voordat de registratie is verwerkt.

1. Selecteer **Volgende**.

   * Kies Voor **implementatiemodus**de optie **Zelf implementeren (Voorbeeld)**. Apparaten met dit profiel zijn gekoppeld aan de gebruiker die het apparaat inschrijft. Er zijn gebruikersreferenties vereist om het apparaat te kunnen registreren. Het is essentieel om op te merken dat het implementeren van een apparaat in de **Self-Deploying-modus** u in staat stelt om laptops te implementeren in een gedeeld model. Er vindt geen gebruikerstoewijzing plaats totdat het apparaat voor de eerste keer aan een gebruiker is toegewezen. Als gevolg hiervan wordt gebruikersbeleid zoals BitLocker pas ingeschakeld als een gebruikerstoewijzing is voltooid. Zie [geselecteerde profielen voor](/intune/device-profile-assign)meer informatie over hoe u zich aanmelden bij een beveiligd apparaat.
   * In het vak **Join naar Azure AD als** moet Azure AD worden weergegeven **en** wordt grijs weergegeven.
   * Selecteer uw taal (regio), standaard voor **gebruikersaccounttypen**. 

1. Selecteer **Volgende**.

   * Selecteer een scopetag als u er een vooraf hebt geconfigureerd.

1. Selecteer **Volgende**.
1. Kies **Toewijzingen** > **toewijzen aan** > geselecteerde**groepen**. Kies In **Groepen selecteren om op te nemen,** kies Beveiligde **werkstations**.
1. Selecteer **Volgende**.
1. Selecteer **Maken** om het profiel te maken. Het Autopilot-implementatieprofiel is nu klaar om te worden toegewezen aan apparaten.

Apparaatinschrijving in Autopilot biedt een andere gebruikerservaring op basis van apparaattype en rol. In ons voorbeeld van implementatie illustreren we een model waarbij de beveiligde apparaten bulkworden geïmplementeerd en kunnen worden gedeeld, maar wanneer het apparaat voor de eerste keer wordt gebruikt, wordt het apparaat toegewezen aan een gebruiker. Zie [Inschrijving van het Intune Autopilot-apparaat voor](/intune/device-enrollment)meer informatie .

### <a name="configure-windows-update"></a>Windows Update configureren

Windows 10 up-to-date houden is een van de belangrijkste dingen die u doen. Als u Windows in een veilige staat wilt houden, implementeert u een updatering om het tempo te beheren dat updates worden toegepast op werkstations. 

Met deze richtlijnen wordt aanbevolen een nieuwe updatering te maken en de volgende standaardinstellingen te wijzigen:

In Azure Portal:

1. Ga naar **Microsoft Intune** > **Software-updates** > **Windows 10 Update Ringen**.
1. Voer het volgende in:

   * Naam - **Azure-beheerde werkstationupdates**
   * Servicekanaal - **Windows Insider - Snel**
   * Uitstel van kwaliteitsupdate (dagen) - **3**
   * Uitstelperiode voor functie-updates (dagen) - **3**
   * Automatisch updategedrag - **Automatisch installeren en opnieuw opstarten zonder controle van de eindgebruiker**
   * Gebruiker blokkeren voor het onderbreken van Windows-updates - **Blokkeren**
   * Goedkeuring van de gebruiker vereisen om buiten de werkuren opnieuw te starten - **Vereist**
   * Gebruiker opnieuw starten (opnieuw opstarten) - **Vereist**
   * Gebruikers overschakelen naar een herstart na een automatische herstart (dagen) - **3**
   * Snooze ingeschakeld herstart herinnering (dagen) - **3**
   * Deadline instellen voor in behandeling zijnde herstart (dagen) - **3**

1. Selecteer **Maken**.
1. Voeg op het tabblad **Toewijzingen** de groep **Beveiligde werkstations** toe.

Zie [Beleid CSP - Bijwerken](/windows/client-management/mdm/policy-csp-update)voor meer informatie over het beleid voor Windows Update.

### <a name="windows-defender-atp-intune-integration"></a>Windows Defender ATP Intune-integratie

Windows Defender ATP en Microsoft Intune werken samen om inbreuken op de beveiliging te voorkomen. Ze kunnen ook de impact van inbreuken beperken. ATP-mogelijkheden bieden real-time detectie van bedreigingen en maken uitgebreide controle en registratie van de end-point-apparaten mogelijk.

Als u de integratie van Windows Defender ATP en Intune wilt configureren, gaat u naar de Azure-portal.

1. Blader naar **Microsoft Intune** > **Device compliance** > **Windows Defender ATP**.
1. Selecteer In stap 1 onder **Configureren van Windows Defender ATP**de optie Windows Defender ATP verbinden met Microsoft **Intune in het Windows Defender Security Center**.
1. In het Windows Defender Beveiligingscentrum:

   1. Selecteer**Geavanceerde functies** **Instellingen** > .
   1. Kies Voor **Microsoft Intune-verbinding**De optie **Aan**.
   1. Selecteer **Voorkeuren opslaan**.

1. Nadat een verbinding is gemaakt, gaat u terug naar Intune en selecteert **u Vernieuwen** bovenaan.
1. Stel **Windows-apparaten met versie 10.0.15063 en hoger verbinden met Windows Defender ATP** in op **Aan**.
1. Selecteer **Opslaan**.

Zie [Windows Defender Advanced Threat Protection](/Windows/security/threat-protection/windows-defender-atp/windows-defender-advanced-threat-protection)voor meer informatie.

### <a name="finish-workstation-profile-hardening"></a>Werkstationprofielverharding voltooien

Om de verharding van de oplossing succesvol te voltooien, download en voer u het juiste script uit. Vind de download links voor uw gewenste **profiel niveau:**

| Profiel | Downloadlocatie | Bestandsnaam |
| --- | --- | --- |
| Lage beveiliging | N.v.t. | N.v.t. |
| Verbeterde beveiliging | https://aka.ms/securedworkstationgit | Verbeterde workstation-Windows10-(1809).ps1 |
| Hoge beveiliging | https://aka.ms/securedworkstationgit | HighSecurityWorkstation-Windows10-(1809).ps1 |
| Gespecialiseerd | https://github.com/pelarsen/IntunePowerShellAutomation | DeviceConfiguration_NCSC - Windows10 (1803) SecurityBaseline.ps1 |
| Gespecialiseerde naleving* | https://aka.ms/securedworkstationgit | DeviceCompliance_NCSC-Windows10(1803).ps1 |
| Beveiligd | https://aka.ms/securedworkstationgit | Secure-Workstation-Windows10-(1809)-SecurityBaseline.ps1 |

\*Specialized Compliance is een script dat de gespecialiseerde configuratie afdwingt die wordt geleverd in de NCSC Windows10 SecurityBaseline.

Nadat het script is uitgevoerd, u profielen en beleidsregels in Intune aanpassen. De scripts voor uitgebreide en beveiligde profielen maken beleid en profielen voor u, maar u moet het beleid toewijzen aan uw **apparaatgroep Secure Workstations.**

* Hier vindt u de intune-apparaatconfiguratieprofielen die zijn gemaakt door de scripts: **Azure-portal** > **Microsoft Intune** > **Device-configuratieprofielen.** > **Profiles**
* Hier vindt u het intune-beleid voor apparaatnaleving dat is gemaakt door de scripts: **Azure portal** > **Microsoft Intune** > **Device Compliance** > **Policies**.

Als u wijzigingen van de scripts wilt bekijken, u de profielen exporteren. Op deze manier u aanvullende verharding bepalen die nodig kan zijn, zoals beschreven in de [SECCON-documentatie.](/windows/security/threat-protection/windows-security-configuration-framework/windows-security-configuration-framework)

Voer het intune-gegevensexportscript `DeviceConfiguration_Export.ps1` uit de [DeviceConfiguration GiuHub-opslagplaats](https://github.com/microsoftgraph/powershell-intune-samples/tree/master/DeviceConfiguration) uit om alle huidige Intune-profielen te exporteren.

## <a name="additional-configurations-and-hardening-to-consider"></a>Aanvullende configuraties en verharding om te overwegen

Door de richtlijnen hier te volgen, hebt u een beveiligd werkstation geïmplementeerd. U moet echter ook rekening houden met extra besturingselementen. Bijvoorbeeld:

* toegang tot alternatieve browsers beperken
* uitgaande HTTP toestaan
* bepaalde websites blokkeren
* machtigingen instellen voor het uitvoeren van aangepaste PowerShell-scripts

### <a name="set-rules-in-the-firewall-configuration-service-provider-csp"></a>Regels instellen in de firewallconfiguratieserviceprovider (CSP)

U indien nodig aanvullende wijzigingen aanbrengen in het beheer van zowel inkomende als uitgaande regels voor uw toegestane en geblokkeerde eindpunten. Terwijl u het beveiligde werkstation blijft verharden, u de beperking losmaken die al het inkomende en uitgaande verkeer ontkent. U toegestane uitgaande sites toevoegen aan algemene en vertrouwde websites. Zie [Firewall-configuratieservice](/Windows/client-management/mdm/firewall-csp)voor meer informatie .

Restrictief URL-verkeerbeheer omvat:

* Al het binnenkomende verkeer weigeren - Instellen in het beveiligde werkstationprofielscript.
* Alle uitgaande, behalve geselecteerde Azure- en Microsoft-services, waaronder Azure Cloud Shell, weigeren en de mogelijkheid om Azure Password Reset toe te staan.

```
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings]
"ProxyEnable"=dword:00000001
"ProxyServer"="127.0.0.1:80"
"ProxyOverride"="*.azure.com;*.azure.net;*.microsoft.com;*.windowsupdate.com;*.microsoftonline.com;*.microsoftonline.cn;*.windows.net;*.windowsazure.com;*.windowsazure.cn;*.azure.cn;*.loganalytics.io;*.applicationinsights.io;*.vsassets.io;*.azure-automation.net;*.visualstudio.com,portal.office.com;*.aspnetcdn.com;*.sharepointonline.com;*.msecnd.net;*.msocdn.com;*.webtrends.com"
"AutoDetect"=dword:00000000
```

Als u op zoek bent naar meer granulariteit aan uw blokkeringsregels, u verwijzen naar het Spamhaus-project dat [de domeinbloklijst (DBL)](https://www.spamhaus.org/dbl/)onderhoudt: een goede bron om te gebruiken als een geavanceerde set regels die u moet implementeren voor het blokkeren van sites.

### <a name="manage-local-applications"></a>Lokale toepassingen beheren

Het beveiligde werkstation wordt echt gehard wanneer lokale toepassingen worden verwijderd, inclusief productiviteitstoepassingen. Hier voegt u Chrome toe als de standaardbrowser en gebruikt u Intune om de mogelijkheid van een gebruiker om de browser en de plug-ins te wijzigen te beperken.

#### <a name="deploy-applications-using-intune"></a>Toepassingen implementeren met Intune

In sommige situaties zijn toepassingen zoals de Google Chrome-browser vereist op het beveiligde werkstation. In het volgende voorbeeld worden instructies gegeven om Chrome te installeren op apparaten in de beveiligingsgroep **Secure Workstations.**

1. Download de offline installer [Chrome-bundel voor Windows 64-bits.](https://cloud.google.com/chrome-enterprise/browser/download/)
1. Haal de bestanden eruit en noteer de locatie van het `GoogleChromeStandaloneEnterprise64.msi` bestand.
1. Blader in de **Azure-portal** naar **Microsoft Intune** > **Client apps** > **Apps** > **Toevoegen**.
1. Kies **onder App-type** **Line-of-business**.
1. Selecteer **onder App-pakketbestand**het `GoogleChromeStandaloneEnterprise64.msi` bestand op de uitgepakte locatie en selecteer **OK**.
1. Geef **onder App-informatie**een beschrijving en een uitgever. Selecteer **OK**.
1. Selecteer **Toevoegen**.
1. Selecteer op het tabblad Toewijzingen de optie **Beschikbaar voor ingeschreven apparaten** onder **Toewijzingstype** . **Assignment type**
1. Voeg onder **Opgenomen groepen**de groep **Veilige werkstations** toe.
1. Selecteer **OK**en selecteer **Opslaan**.

Zie [Chrome-browser beheren met Microsoft Intune](https://support.google.com/chrome/a/answer/9102677)voor meer informatie over het configureren van Chrome-instellingen.

#### <a name="configuring-the-company-portal-for-custom-apps"></a>Het bedrijfsportaal configureren voor aangepaste apps

In een beveiligde modus is de installatie van toepassingen beperkt tot het intune-bedrijfsportaal. Het installeren van de portal vereist echter toegang tot de Microsoft Store. In uw beveiligde oplossing u het bedrijfsportaal beschikbaar maken voor alle apparaten via een offlinemodus.

Een door Intune beheerde kopie van de [Bedrijfsportal](/Intune/store-apps-company-portal-app) geeft u on-demand toegang tot extra tools die u pushen naar gebruikers van de beveiligde werkstations.

Mogelijk moet u Windows 32-bits apps of andere apps installeren waarvan de implementatie speciale voorbereidingen vereist. In dergelijke gevallen kan het [microsoft win32-hulpprogramma voor het voorbereiden van inhoud](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) een gebruiksklaar `.intunewin` formaatbestand bieden voor installatie.

### <a name="conditional-access-only-allowing-secured-workstation-ability-to-access-azure-portal"></a>Voorwaardelijke toegang biedt alleen beveiligde werkstationmogelijkheid om toegang te krijgen tot Azure-portal

Azure AD biedt de mogelijkheid om te beheren en te beperken, wie en wat toegang heeft tot uw Azure-cloudbeheerportal. Als u [Voorwaardelijke toegang](../conditional-access/overview.md) inschakelt, u ervoor zorgen dat alleen uw beveiligde werkstation resources kan beheren of wijzigen. Het is essentieel dat u bij het implementeren van deze functie overweegt of de functionaliteit [voor noodtoegang](../users-groups-roles/directory-emergency-access.md) alleen kan of moet worden gebruikt voor extreme gevallen en het account dat wordt beheerd via het beleid.

> [!NOTE]
> U moet een gebruikersgroep maken en uw noodgebruiker opnemen die het beleid voor voorwaardelijke toegang kan omzeilen. Voor ons voorbeeld hebben we een security groep genaamd **Emergency BreakGlass**

1. Blader naar de **Azure-portal** > **Microsoft Intune** > **Conditional Access - Nieuw**beleid voor > **beleid**.
1. Geef een **naam** op voor het beleid.
1. **Gebruikers en groepen** > **selecteren Selecteren gebruikers en groepen selecteren** 
1. Selecteer**Maprollen** **opnemen** > > De rollen kiezen > Globale beheerder, Privileged Role Administrator, Privileged Authentication Administrator, Security Administrator, Compliance Administrator, Conditional Access Administrator, Application Administrator, Cloud Application Administrator, Intune Service Administrator
1. Selecteer **>** **Gebruikers en groepen** uitsluiten kiezen > Selectie **uitgesloten gebruikers** selecteren > Selecteer uw **emergency breakglass-groep.**
1. Selecteer **Cloud-apps of -acties** > **Alle cloud-apps selecteren**
1. Selecteer **Voorwaarden** > **Selecteer apparaatplatforms** > **Configureer Ja** > Selecteer Selecteer **Apparaatplatforms** kiezen **Windows**
1. Selecteer **Access-besturingselementen** > **Selecteer Toegang tot toegang** **toestaan** > kies Apparaat vereisen dat als compatibel moet **zijn gemarkeerd**. 
1. Selecteer **Beleid** > inschakelen**op**
 
Met deze beleidsset moet ervoor zorgen dat uw beheerders een compatibel Windows-apparaat moeten gebruiken, dat is ingesteld door Intune en WDATP. 

Organisaties moeten ook overwegen oudere verificatieprotocollen in hun omgeving te blokkeren. Er zijn meerdere manieren om deze taak uit te voeren, voor meer informatie over het blokkeren van verouderde verificatieprotocollen zie het [artikel, Hoe te: Verouderde verificatie blokkeren naar Azure AD met voorwaardelijke toegang](../conditional-access/block-legacy-authentication.md).

### <a name="use-powershell-to-create-custom-settings"></a>PowerShell gebruiken om aangepaste instellingen te maken

U PowerShell ook gebruiken om de mogelijkheden voor hostbeheer uit te breiden. In dit voorbeeldscript wordt een standaardachtergrond op de host ingesteld. Het is een mogelijkheid die ook beschikbaar is via de Azure-portal en profielen. Het voorbeeldscript dient alleen om de PowerShell-functionaliteit te illustreren.

Mogelijk moet u een aantal aangepaste besturingselementen en instellingen instellen op uw beveiligde werkstations. In dit voorbeeld wordt de achtergrond van het werkstation gewijzigd door de mogelijkheid van Powershell te gebruiken om het apparaat eenvoudig te identificeren als een gebruiksklaar, veilig werkstation.

Met [het Script SetDesktopBackground.ps1](https://gallery.technet.microsoft.com/scriptcenter/Set-Desktop-Image-using-5430c9fb/) van het Microsoft Scripting Center kan Windows deze [gratis, algemene achtergrondafbeelding](https://i.imgur.com/OAJ28zO.png) bij het opstarten laden.

1. Download het script naar een lokaal apparaat.
1. Update de customerXXXX en de downloadlocatie van de achtergrondafbeelding. In ons voorbeeld vervangen we customerXXXX naar achtergronden.
1. Blader naar de **Azure-portal** > **Microsoft Intune** > **Device-configuratie** > **PowerShell-scripts** > **Toevoegen**.
1. Geef een **naam** op voor het script en geef de **Script-locatie**op.
1. Selecteer **Configureren**.
   1. **Stel Dit script uitvoeren in met de aangemelde referenties** op **Ja**.
   1. Selecteer **OK**.
1. Selecteer **Maken**.
1. Selecteer **Groepen** > **Selecteren.**
   1. Voeg de beveiligingsgroep **Secure Workstations**toe.
   1. Selecteer **Opslaan**.

## <a name="enroll-and-validate-your-first-device"></a>Uw eerste apparaat inschrijven en valideren

1. Als u uw apparaat wilt inschrijven, hebt u de volgende informatie nodig:
   * **Serienummer** - gevonden op het chassis van het apparaat.
   * **Windows Product ID** - gevonden onder **Systeeminformatie** > **About** in het menu Windows-instellingen.
   * U [Get-WindowsAutoPilotInfo](https://aka.ms/Autopilotshell) uitvoeren om een CSV-hashbestand te krijgen met alle vereiste informatie voor apparaatinschrijving.
   
     Voer `Get-WindowsAutoPilotInfo – outputfile device1.csv` uit om de informatie uit te voeren als een CSV-bestand dat u importeren in Intune.

     > [!NOTE]
     > Het script vereist verhoogde rechten. Het loopt als op afstand ondertekend. Met `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned` de opdracht kan het script correct worden uitgevoerd.

   * U deze informatie verzamelen door u aan te melden bij een Windows 10-versie 1809 of hoger apparaat. Uw hardware reseller kan deze informatie ook verstrekken.
1. Ga in de **Azure-portal**naar **Microsoft Intune** > **Device inschrijving** > **Windows-inschrijvingsapparaten** > **- Windows Autopilot-apparaten beheren**.
1. Selecteer **Importeren** en kies uw CSV-bestand.
1. Voeg het apparaat toe aan de beveiligingsgroep **Secure Workstations.**
1. Ga op het Windows 10-apparaat dat u wilt configureren naar **Windows Settings** > **Update & Security** > **Recovery.**
   1. Kies **Aan de slag** onder Deze pc opnieuw **instellen**.
   1. Volg de aanwijzingen om het apparaat opnieuw in te stellen en opnieuw te configureren met het profiel- en nalevingsbeleid dat is geconfigureerd.

Nadat u het apparaat hebt geconfigureerd, voert u een beoordeling uit en controleert u de configuratie. Controleer of het eerste apparaat correct is geconfigureerd voordat u verder gaat met de implementatie.

## <a name="assign-devices"></a>Apparaten toewijzen

Als u apparaten en gebruikers wilt toewijzen, moet u de [geselecteerde profielen](/intune/device-profile-assign) toewijzen aan uw beveiligingsgroep. Alle nieuwe gebruikers die machtigingen voor de service nodig hebben, moeten ook aan de beveiligingsgroep worden toegevoegd.

## <a name="using-sentinel-and-windows-defender-atp-to-monitor-and-respond-to-security-incidents"></a>Sentinel en Windows Defender ATP gebruiken om beveiligingsincidenten te monitoren en erop te reageren

Monitoring van de veilige implementatie van werkstations kan worden bereikt door [Sentinel] in te schakelen en gebruik te maken van [Threat and Vulnerability Management](/windows/security/threat-protection/microsoft-defender-atp/next-gen-threat-and-vuln-mgt) De richtlijnen bieden geen uitputtende bedreigingsjacht, maar bieden goede verstandsinspanningen om potentiële beveiligingsincidenten te monitoren en te reageren.

We gebruiken **Azure Sentinel** om: 

* Gegevens verzamelen van Intune, Azure-portal en Azure AD voor gebruikers- en apparaatbewaking
* Help verdachte activiteit van gebruikers- en apparaatconfiguratie te onderzoeken
* En de mogelijkheid stimuleren om beveiligingsonderzoeken te verkennen met WDATP

Sentinel-monitoring vereist dat connectoren voor uw gegevensbronnen zoals Azure AD worden ingesteld.

1. Ga in de **Azure-portal**naar **Azure Sentinel (Preview)** > **Toevoegen selecteren**
1. Selecteer **een** nieuwe werkruimte kiezen in de werkruimte kiezen die u wilt toevoegen aan Azure Sentinel Selecteer een nieuwe werkruimte **maken**
1. Voer het volgende in:
   * **Log Analytics Workspace** - 'Secure Workstation Monitoring'
   * **Abonnement** - Selecteer uw actieve abonnement
   * **Resourcegroep** - selecteer de >-> Secure Workstation RG > **Ok** maken**
   * **Locatie** - Selecteer de locatie die geografisch het meest geschikt is voor uw implementatie
   * **Prijslaag** - Selecteer **per GB (2018)**
1. Selecteer **Ok**.

Vervolgens zullen we beschikbare beveiligde workstationgegevensbronnen aansluiten op de monitoring.

1. Ga in de **Azure portal**naar **Azure Sentinel-werkruimte** > **Secure Workstation Monitoring workspace** selecteren
1. **Gegevensconnectors selecteren**
1. Kies **Azure Active Directory** > Open Connector Page > Na het bekijken van de vereiste. Ga naar Configuratie en selecteer **Verbinding** maken voor zowel Azure AD-aanmeldingslogboeken als Azure AD-controlelogboeken.
1. Kies **Azure Activity** > Open Connector Page > Na het bekijken van de vereiste. Ga naar Azure Activity Logs configureren > Uw abonnement selecteren > **Connect selecteren**

Als gegevens worden verzameld door Sentinel, u activiteit observeren door **Azure-portal**te selecteren, ga naar **Azure Sentinel-overzicht** 

We gebruiken **Windows Defender ATP (WDATP)** om:

* Voortdurend observeren en bewaken van kwetsbaarheden en verkeerde configuraties
* Gebruik WDATP om dynamische bedreigingen in het wild te prioriteren
* Correlatie van kwetsbaarheden stimuleren met EDR-waarschuwingen (Endpoint Detection and Response)
* Gebruik het dashboard om kwetsbaarheid op machineniveau te identificeren tijdens onderzoeken
* Herstelacties naar Intune pushen

Configureer uw [Defender ATP-dashboard.](https://securitycenter.windows.com/machines) Met behulp van richtlijnen op [Threat & Vulnerability Management dashboard overzicht](/windows/security/threat-protection/microsoft-defender-atp/tvm-dashboard-insights).

## <a name="monitoring-application-activity-using-microsoft-monitoring-agent-mma"></a>Toepassingsactiviteit controleren met Microsoft Monitoring Agent (MMA)
Vanaf het Specialized-werkstation is app locker ingeschakeld voor het bewaken van toepassingsactiviteit op een werkstation. Om de monitoring in uw Log Analytics-werkruimte te integreren, moeten een MMA-agent en configuratie worden gevolgd. 

> [!NOTE]
> Het gespecialiseerde werkstationprofiel bevat het applocker-bewakingsbeleid. Implementatie van het beleid is vereist voor het bewaken van toepassingsactiviteit op een client

De MMA-agent implementeren met Intune PowerShell-script

1. Download het [installatiescript naar een lokaal apparaat.](https://aka.ms/securedworkstationgit)
1. De **parameters, $WorkSpaceID** en **$WorkSpaceKey bijwerken**
1. Blader naar de **Azure-portal** > **Microsoft Intune** > **Device-configuratie** > **PowerShell-scripts** > **Toevoegen**.
1. Geef een **naam** op voor het script en geef de **Script-locatie**op.
1. Selecteer **Configureren**.
   1. **Stel Dit script uitvoeren in met de aangemelde referenties** op **Ja**.
   1. Selecteer **OK**.
1. Selecteer **Maken**.
1. Selecteer **Groepen** > **Selecteren.**
   1. Voeg de beveiligingsgroep **Secure Workstations**toe.
   1. Selecteer **Opslaan**.

Vervolgens moet u Log Analytics instellen om de nieuwe logboeken te ontvangen
1. Ga in de **Azure-portal**naar **Log Analytics Workspace** > Select - 'Secure Workstation Monitoring'
1. **Geavanceerde instellingen** > **Gegevens** > **Windows-gebeurtenislogboeken selecteren**
1. In **Gebeurtenissen verzamelen uit de volgende gebeurtenislogboeken** 
1. Voer het volgende in:
   * 'Microsoft-Windows-AppLocker/EXE en DLL' > **Informatieongedaan e-selectie opheffen**
   * 'Microsoft-Windows-AppLocker/MSI and Script' > **Informatieniet selecteren**
   * 'Microsoft-Windows-AppLocker/Packaged app-Deployment' > **Informatieniet selecteren**
   * 'Microsoft-Windows-AppLocker/Packaged app-Execution' > **Informatieongedaan maken**
1. Selecteer **Opslaan**

Toepassingslogboekregistratie is beschikbaar in de geselecteerde Log Analytics-werkruimte.

## <a name="monitoring"></a>Bewaking

* Meer informatie over het [detecteren van bedreigingen met Azure Sentinel](/azure/sentinel/tutorial-detect-threats)
* [Incidenten met Azure Sentinel onderzoeken](/azure/sentinel/tutorial-investigate-cases)
* [Geautomatiseerde bedreigingsreacties instellen in Azure Sentinel](/azure/sentinel/tutorial-respond-threats-playbook)
* Begrijp hoe u uw [belichtingsscore bekijken](/windows/security/threat-protection/microsoft-defender-atp/tvm-exposure-score)
* [Beveiligingsaanbeveling controleren](/windows/security/threat-protection/microsoft-defender-atp/tvm-security-recommendation)
* [Beveiligingsherstels](/windows/security/threat-protection/microsoft-defender-atp/tvm-remediation) beheren
* [Eindpuntdetectie en -respons](/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response) beheren
* Monitor profielen met [Intune profielbewaking.](/intune/device-profile-monitor)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Microsoft Intune](/intune/index).
* Azure [AD begrijpen](../index.yml).
* Werken met [geavanceerde bedreigingsbeveiliging van Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection)
* [Ontdek Azure Sentinel](/azure/sentinel/)
