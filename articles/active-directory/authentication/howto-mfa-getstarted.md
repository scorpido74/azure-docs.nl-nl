---
title: Azure Multi-Factor Authentication implementeren - Azure Active Directory
description: Implementatieplanning voor implementatie van Microsoft Azure-verificatie met meerdere factoren
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b6da67589b15b4ab043510c0375c26c12f645adb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263879"
---
# <a name="planning-a-cloud-based-azure-multi-factor-authentication-deployment"></a>Planning van een cloudgebaseerde Azure Multi-Factor Authentication-implementatie

Mensen maken verbinding met organisatorische middelen in steeds ingewikkelder scenario's. Mensen maken verbinding vanaf apparaten die eigendom zijn van een organisatie op en buiten het bedrijfsnetwerk met behulp van smartphones, tablets, pc's en laptops, vaak op meerdere platforms. In deze altijd verbonden, multi-device en multi-platform wereld is de beveiliging van gebruikersaccounts belangrijker dan ooit. Wachtwoorden, ongeacht hun complexiteit, die worden gebruikt op apparaten, netwerken en platforms, zijn niet langer voldoende om de veiligheid van het gebruikersaccount te garanderen, vooral wanneer gebruikers de neiging hebben om wachtwoorden tussen accounts te hergebruiken. Geavanceerde phishing en andere social engineering-aanvallen kunnen ertoe leiden dat gebruikersnamen en wachtwoorden worden geplaatst en verkocht op het dark web.

[Azure Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) helpt de toegang tot gegevens en toepassingen te waarborgen. Het biedt een extra beveiligingslaag met behulp van een tweede vorm van verificatie. Organisaties kunnen [Voorwaardelijke toegang](../conditional-access/overview.md) gebruiken om de oplossing aan hun specifieke behoeften te laten voldoen.

## <a name="prerequisites"></a>Vereisten

Voordat u een implementatie van Azure Multi-Factor Authentication start, zijn er vereiste items die moeten worden overwogen.

| Scenario | Vereiste |
| --- | --- |
| **Cloud-only** identiteitsomgeving met moderne verificatie | **Geen extra vereiste taken** |
| **Hybride** identiteitsscenario's | [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) wordt geïmplementeerd en gebruikersidentiteiten worden gesynchroniseerd of gefederiseerd met de on-premises Active Directory Domain Services met Azure Active Directory. |
| On-premises legacy-applicaties gepubliceerd voor cloudtoegang | Azure [AD-toepassingsproxy](../manage-apps/application-proxy.md) is geïmplementeerd. |
| Azure MFA gebruiken met RADIUS-verificatie | Er wordt een [NETWERKbeleidsserver (NPS)](howto-mfa-nps-extension.md) geïmplementeerd. |
| Gebruikers hebben Microsoft Office 2010 of eerder, of Apple Mail voor iOS 11 of eerder | Upgrade naar [Microsoft Office 2013 of hoger](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) en Apple mail voor iOS 12 of hoger. Voorwaardelijke toegang wordt niet ondersteund door verouderde verificatieprotocollen. |

## <a name="plan-user-rollout"></a>Gebruikersimplementatie plannen

Uw MFA-implementatieplan moet een pilot-implementatie bevatten, gevolgd door implementatiegolven die zich binnen uw ondersteuningscapaciteit bevinden. Begin uw implementatie door uw beleid voor voorwaardelijke toegang toe te passen op een kleine groep pilotgebruikers. Nadat u het effect op de pilotgebruikers, het gebruikte proces en registratiegedrag hebt geëvalueerd, u meer groepen aan het beleid toevoegen of meer gebruikers toevoegen aan de bestaande groepen.

### <a name="user-communications"></a>Gebruikerscommunicatie

Het is van cruciaal belang om gebruikers in geplande communicatie te informeren over aankomende wijzigingen, Azure MFA-registratievereisten en eventuele noodzakelijke gebruikersacties. We raden aan dat communicatie wordt ontwikkeld in overleg met vertegenwoordigers van uw organisatie, zoals een afdeling Communicatie, Change Management of Human Resources.

Microsoft biedt [communicatiesjablonen](https://aka.ms/mfatemplates) en [documentatie van eindgebruikers](../user-help/security-info-setup-signin.md) om uw communicatie te helpen opstellen. U gebruikers [https://myprofile.microsoft.com](https://myprofile.microsoft.com) rechtstreeks naar de registratie sturen door de koppelingen **beveiligingsgegevens** op die pagina te selecteren.

## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

Azure Multi-factor Authentication wordt geïmplementeerd door beleid af te dwingen met Voorwaardelijke toegang. Een [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) kan vereisen dat gebruikers multifactorauthenticatie uitvoeren wanneer aan bepaalde criteria wordt voldaan, zoals:

* Alle gebruikers, een specifieke gebruiker, lid van een groep of toegewezen rol
* Specifieke cloudtoepassing die wordt geopend
* Apparaatplatform
* Staat van het apparaat
* Netwerklocatie of ip-adres met geolocatie
* Clienttoepassingen
* Aanmeldingsrisico (vereist identiteitsbescherming)
* Compatibel apparaat
* Hybride Azure AD-apparaat
* Goedgekeurde clienttoepassing

Gebruik de aanpasbare posters en e-mailsjablonen in [uitrolmaterialen voor meervoudige verificatie](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) om multi-factor authenticatie uit te rollen naar uw organisatie.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Meervoudige verificatie inschakelen met voorwaardelijke toegang

Beleid voor voorwaardelijke toegang dwingt registratie af, waardoor niet-geregistreerde gebruikers de registratie bij de eerste aanmelding moeten voltooien, een belangrijke beveiligingsoverweging.

[Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) draagt zowel een registratiebeleid voor als geautomatiseerd risicodetectie- en herstelbeleid bij aan het verhaal Azure Multi-Factor Authentication. Er kan beleid worden gemaakt om wachtwoordwijzigingen af te dwingen wanneer er een dreiging van gecompromitteerde identiteit is of MFA vereist wanneer een aanmelding door de volgende [gebeurtenissen](../reports-monitoring/concept-risk-events.md)als riskant wordt beschouwd:

* Gelekte referenties
* Aanmeldingen vanaf anonieme IP-adressen
* Onmogelijke reis naar ongewone locaties
* Aanmeldingen vanaf onbekende locaties
* Aanmeldingen vanaf geïnfecteerde apparaten
* Aanmeldingen van IP-adressen met verdachte activiteiten

Sommige van de risicodetecties die door Azure Active Directory Identity Protection worden gedetecteerd, vinden in realtime plaats en sommige vereisen offline verwerking. Beheerders kunnen ervoor kiezen om gebruikers te blokkeren die risicovol gedrag vertonen en handmatig te herstellen, een wachtwoordwijziging vereisen of een meervoudige verificatie vereisen als onderdeel van hun beleid voor voorwaardelijke toegang.

## <a name="define-network-locations"></a>Netwerklocaties definiëren

We raden organisaties aan voorwaardelijke toegang te gebruiken om hun netwerk te definiëren met behulp van [benoemde locaties.](../conditional-access/location-condition.md#named-locations) Als uw organisatie identiteitsbeveiliging gebruikt, u overwegen om op risico's gebaseerd beleid te gebruiken in plaats van benoemde locaties.

### <a name="configuring-a-named-location"></a>Een benoemde locatie configureren

1. **Azure Active Directory openen** in de Azure-portal
2. Selecteer **Beveiliging**
3. Kies **Onder Beheren**de optie Benoemde **locaties**
4. Nieuwe **locatie selecteren**
5. Geef in het veld **Naam** een betekenisvolle naam op
6. Selecteren of u de locatie definieert met *IP-bereiken* of *landen/regio's*
   1. Als u *IP-bereiken gebruikt*
      1. Bepaal of u *wilt markeren als vertrouwde locatie*. Aanmelden van een vertrouwde benoemde locaties verlaagt het aanmeldingsrisico van een gebruiker. Markeer deze locatie alleen als vertrouwd als u weet dat de ingevoerde IP-bereiken zijn vastgesteld en geloofwaardig in uw organisatie.
      2. De IP-bereiken opgeven
   2. Als u *landen/regio's gebruikt*
      1. Vouw het vervolgkeuzemenu uit en selecteer de landen of regio's die u wilt definiëren voor deze locatie.
      2. Bepaal of *u onbekende gebieden wilt opnemen.* Onbekende gebieden zijn IP-adressen die niet kunnen worden toegewezen aan een land/regio.
7. Selecteer **Maken**

## <a name="plan-authentication-methods"></a>Verificatiemethoden plannen

Beheerders kunnen de [verificatiemethoden](../authentication/concept-authentication-methods.md) kiezen die ze beschikbaar willen maken voor gebruikers. Het is belangrijk om meer dan één verificatiemethode toe te staan, zodat gebruikers een back-upmethode beschikbaar hebben voor het geval hun primaire methode niet beschikbaar is. Beheerders kunnen de volgende methoden inschakelen:

### <a name="notification-through-mobile-app"></a>Melding via mobiele app

Er wordt een pushmelding verzonden naar de Microsoft Authenticator-app op uw mobiele apparaat. De gebruiker bekijkt de melding en selecteert **Goedkeuren** om verificatie te voltooien. Pushmeldingen via een mobiele app bieden de minst opdringerige optie voor gebruikers. Ze zijn ook de meest betrouwbare en veilige optie omdat ze gebruik maken van een dataverbinding in plaats van telefonie.

> [!NOTE]
> Als uw organisatie personeel heeft dat in Of naar China reist, werkt de **methode Melding via mobiele app** op **Android-apparaten** niet in dat land. Voor deze gebruikers moeten alternatieve methoden beschikbaar worden gesteld.

### <a name="verification-code-from-mobile-app"></a>Verificatiecode van de mobiele app

Een mobiele app zoals de Microsoft Authenticator-app genereert elke 30 seconden een nieuwe OATH-verificatiecode. De gebruiker voert de verificatiecode in op de aanmeldingsinterface. De optie voor mobiele apps kan worden gebruikt, ongeacht of de telefoon een data- of mobiel signaal heeft.

### <a name="call-to-phone"></a>Bellen naar de telefoon

Er wordt een geautomatiseerd telefoongesprek naar de gebruiker gestuurd. De gebruiker beantwoordt de **#** oproep en drukt op het toetsenbord van de telefoon om hun verificatie goed te keuren. Bellen naar een telefoon is een geweldige back-upmethode voor meldings- of verificatiecode vanuit een mobiele app.

### <a name="text-message-to-phone"></a>Sms-bericht naar telefoon

Een sms-bericht dat een verificatiecode bevat, wordt naar de gebruiker verzonden, de gebruiker wordt gevraagd om de verificatiecode in te voeren in de aanmeldingsinterface.

### <a name="choose-verification-options"></a>Verificatieopties kiezen

1. Blader naar **Azure Active Directory**, **Users**, **Multi-Factor Authentication**.

   ![Toegang tot de multi-factor verificatieportal vanuit azure AD-gebruikersblad in Azure-portal](media/howto-mfa-getstarted/users-mfa.png)

1. Blader naar service-instellingen in het nieuwe tabblad dat doorbladert naar **service-instellingen**.
1. Schakel **onder verificatieopties**alle vakken in voor methoden die beschikbaar zijn voor gebruikers.

   ![Verificatiemethoden configureren op het tabblad Instellingen voor verificatie van meerdere factoren](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Klik op **Opslaan**.
1. Sluit het tabblad **Service-instellingen.**

## <a name="plan-registration-policy"></a>Registratiebeleid plannen

Beheerders moeten bepalen hoe gebruikers hun methoden registreren. Organisaties moeten [de nieuwe gecombineerde registratie-ervaring](howto-registration-mfa-sspr-combined.md) voor Azure MFA en selfservice wachtwoordreset (SSPR) inschakelen. Met SSPR kunnen gebruikers hun wachtwoord op een veilige manier opnieuw instellen met dezelfde methoden die ze gebruiken voor multi-factor authenticatie. We raden deze gecombineerde registratie aan, momenteel in openbare preview, omdat het een geweldige ervaring is voor gebruikers, met de mogelijkheid om één keer te registreren voor beide services. Als u dezelfde methoden inschakelt voor SSPR en Azure MFA, kunnen uw gebruikers worden geregistreerd om beide functies te gebruiken.

### <a name="registration-with-identity-protection"></a>Registratie bij identiteitsbescherming

Als uw organisatie Azure Active Directory Identity Protection gebruikt, [configureert u het MFA-registratiebeleid](../identity-protection/howto-mfa-policy.md) om uw gebruikers te vragen zich de volgende keer dat ze zich interactief aanmelden te registreren.

### <a name="registration-without-identity-protection"></a>Registratie zonder identiteitsbescherming

Als uw organisatie geen licenties heeft die identiteitsbeveiliging inschakelen, wordt gebruikers gevraagd zich de volgende keer te registreren dat MFA vereist is bij aanmelding. Gebruikers kunnen niet worden geregistreerd voor MFA als ze geen toepassingen gebruiken die zijn beveiligd met MFA. Het is belangrijk om alle gebruikers geregistreerd te krijgen, zodat slechte acteurs het wachtwoord van een gebruiker niet kunnen raden en zich namens hen kunnen registreren voor MFA, waardoor ze effectief de controle over het account overnemen.

#### <a name="enforcing-registration"></a>Registratie afdwingen

Met de volgende stappen kan een beleid voor voorwaardelijke toegang gebruikers dwingen zich te registreren voor multifactorauthenticatie

1. Maak een groep, voeg alle gebruikers toe die momenteel niet zijn geregistreerd.
2. Met Voorwaardelijke toegang u multifactorauthenticatie afdwingen voor deze groep voor toegang tot alle bronnen.
3. Evalueer het groepslidmaatschap regelmatig opnieuw en verwijder gebruikers die zich hebben geregistreerd uit de groep.

U geregistreerde en niet-geregistreerde Azure MFA-gebruikers identificeren met PowerShell-opdrachten die afhankelijk zijn van de [MSOnline PowerShell-module.](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)

#### <a name="identify-registered-users"></a>Geregistreerde gebruikers identificeren

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Niet-geregistreerde gebruikers identificeren

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gebruikers converteren van MFA per gebruiker naar op voorwaardelijke toegang gebaseerde MFA

Als uw gebruikers zijn ingeschakeld met behulp van azure multifactorauthenticatie per gebruiker, kan de volgende PowerShell u helpen bij het converteren naar op voorwaardelijke toegang gebaseerde Azure Multi-Factor Authentication.

Voer deze PowerShell uit in een ISE-venster of sla op als een . PS1-bestand om lokaal uit te voeren.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> We hebben onlangs het gedrag en PowerShell script hierboven dienovereenkomstig veranderd. Voorheen heeft het script de MFA-methoden opgeslagen, MFA uitgeschakeld en de methoden hersteld. Dit is niet langer nodig nu het standaardgedrag voor uitschakelen de methoden niet meer wist.

## <a name="plan-conditional-access-policies"></a>Beleid voor voorwaardelijke toegang plannen

Als u uw beleidsstrategie voor voorwaardelijke toegang wilt plannen, die bepaalt wanneer MFA en andere besturingselementen vereist zijn, raadpleegt u [Wat is voorwaardelijke toegang in Azure Active Directory?](../conditional-access/overview.md).

Het is belangrijk dat u voorkomt dat u per ongeluk wordt buitengesloten van uw Azure AD-tenant. U de impact van dit onbedoelde gebrek aan beheerderstoegang beperken door [twee of meer noodtoegangsaccounts in uw tenant](../users-groups-roles/directory-emergency-access.md) te maken en deze uit te sluiten van uw beleid voor voorwaardelijke toegang.

### <a name="create-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een algemeen beheerdersaccount.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
   ![Een beleid voor voorwaardelijke toegang maken om MFA in te schakelen voor Azure-portalgebruikers in de pilotgroep](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Geef een betekenisvolle naam op voor uw beleid.
1. Onder **gebruikers en groepen**:
   * Selecteer op het tabblad **Opnemen** de keuzerondje **Alle gebruikers**
   * Schakel op het tabblad **Uitsluiten** het selectievakje voor **gebruikers en groepen** in en kies uw accounts voor noodtoegang.
   * Klik op **Gereed**.
1. Selecteer **onder Cloud-apps**de keuzerondje **voor alle cloud-apps.**
   * OPTIONEEL: Kies op het tabblad **Uitsluiten** de optie cloud-apps waarvoor uw organisatie geen MFA nodig heeft.
   * Klik op **Gereed**.
1. Onder **voorwaarden:**
   * OPTIONEEL: Als u Azure Identity Protection hebt ingeschakeld, u ervoor kiezen om aanmeldingsrisico's te evalueren als onderdeel van het beleid.
   * OPTIONEEL: Als u vertrouwde locaties of benoemde locaties hebt geconfigureerd, u opgeven om deze locaties op te nemen of uit te sluiten van het beleid.
1. Controleer **onder Grant**of de **keuzerondje voor toegangstoegang verlenen** is geselecteerd.
    * Schakel het selectievakje **voor Multi-factor authenticatie vereisen**in .
    * Klik **op Selecteren**.
1. Sla de sectie **Sessie** over.
1. Het **inschakelen van** beleidsomschakeling instellen op **Aan**.
1. Klik **op Maken**.

## <a name="plan-integration-with-on-premises-systems"></a>Integratie plannen met on-premises systemen

Voor sommige verouderde en on-premises toepassingen die niet rechtstreeks tegen Azure AD zijn geverifieerd, zijn aanvullende stappen vereist om MFA te gebruiken, waaronder:

* Verouderde on-premises toepassingen, die moeten gebruiken Application proxy.
* On-premises RADIUS-toepassingen, die mfa-adapter met NPS-server moeten gebruiken.
* On-premises AD FS-toepassingen, die mfa-adapter moeten gebruiken met AD FS 2016 of nieuwer.

Toepassingen die rechtstreeks met Azure AD zijn geverifieerd en moderne verificatie hebben (WS-Fed, SAML, OAuth, OpenID Connect) kunnen rechtstreeks gebruik maken van beleid voor voorwaardelijke toegang.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Azure MFA gebruiken met Azure AD-toepassingsproxy

Toepassingen die on-premises aanwezig zijn, kunnen worden gepubliceerd naar uw Azure AD-tenant via [Azure AD-toepassingsproxy](../manage-apps/application-proxy.md) en kunnen profiteren van Azure Multi-Factor Authentication als ze zijn geconfigureerd om Azure AD-pre-authenticatie te gebruiken.

Deze toepassingen zijn onderworpen aan beleid voor voorwaardelijke toegang dat Azure Multi-Factor Authentication afdwingt, net als elke andere azure AD-geïntegreerde toepassing.

Als Azure Multi-Factor Authentication wordt afgedwongen voor alle aanmeldingen van gebruikers, worden on-premises toepassingen die zijn gepubliceerd met Azure AD Application Proxy, beveiligd.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Azure-multifactorverificatie integreren met netwerkbeleidsserver

De NPS-extensie (Network Policy Server) voor Azure MFA voegt cloudgebaseerde MFA-mogelijkheden toe aan uw verificatie-infrastructuur met behulp van uw bestaande servers. Met de NPS-extensie u telefoongesprekken, sms-berichten of verificatie van de telefoon-app toevoegen aan uw bestaande verificatiestroom. Deze integratie heeft de volgende beperkingen:

* Met het CHAPv2-protocol worden alleen pushmeldingen van authenticator-apps en spraakoproepen ondersteund.
* Beleid voor voorwaardelijke toegang kan niet worden toegepast.

De NPS-extensie fungeert als een adapter tussen RADIUS en cloudgebaseerde Azure MFA om een tweede verificatiefactor te bieden om [VPN-](howto-mfa-nps-extension-vpn.md)of [Remote Desktop Gateway-verbindingen](howto-mfa-nps-extension-rdg.md)of andere RADIUS-compatibele toepassingen te beschermen. Gebruikers die zich in deze omgeving registreren voor Azure MFA worden uitgedaagd voor alle verificatiepogingen, het ontbreken van beleid voor voorwaardelijke toegang betekent dat MFA altijd vereist is.

#### <a name="implementing-your-nps-server"></a>Uw NPS-server implementeren

Als u een NPS-exemplaar hebt geïmplementeerd en al in gebruik is, raadpleegt u [uw bestaande NPS-infrastructuur integreren met Azure Multi-Factor Authentication](howto-mfa-nps-extension.md). Als u NPS voor de eerste keer instelt, raadpleegt u [Network Policy Server (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) voor instructies. Richtlijnen voor het oplossen van problemen vindt u in het artikel [Foutberichten oplossen vanuit de NPS-extensie voor Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>NPS voorbereiden voor gebruikers die niet zijn ingeschreven voor MFA

Kies wat er gebeurt wanneer gebruikers die niet zijn ingeschreven bij MFA proberen te verifiëren. Gebruik de `REQUIRE_USER_MATCH` registerinstelling in `HKLM\Software\Microsoft\AzureMFA` het registerpad om het functiegedrag te beheren. Deze instelling heeft één configuratieoptie.

| Sleutel | Waarde | Standaard |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | WAAR / ONWAAR | Niet ingesteld (gelijk aan WAAR) |

Het doel van deze instelling is om te bepalen wat te doen wanneer een gebruiker niet is ingeschreven voor MFA. De effecten van het wijzigen van deze instelling worden weergegeven in de onderstaande tabel.

| Instellingen | MFA-status van gebruiker | Effecten |
| --- | --- | --- |
| Sleutel bestaat niet | Niet ingeschreven | MFA-uitdaging is mislukt |
| Waarde ingesteld op True / niet ingesteld | Niet ingeschreven | MFA-uitdaging is mislukt |
| Sleutel ingesteld op False | Niet ingeschreven | Verificatie zonder MFA |
| Sleutel ingesteld op Onwaar of Waar | Geregistreerd | Moet verifiëren met MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integreren met Active Directory Federation Services

Als uw organisatie is gefedereerd met Azure AD, u [Azure Multi-Factor Authentication gebruiken om AD FS-resources te beveiligen](multi-factor-authentication-get-started-adfs.md), zowel on-premises als in de cloud. Met Azure MFA u wachtwoorden verminderen en een veiligere manier bieden om te verifiëren. Beginnen met Windows Server 2016, kunt u Azure MFA configureren voor primaire authenticatie.

In tegenstelling tot AD FS in Windows Server 2012 R2 integreert de Azure MFA-adapter ad FS 2016 rechtstreeks met Azure AD en is er geen on-premises Azure MFA-server vereist. De Azure MFA-adapter is ingebouwd in Windows Server 2016 en er is geen extra installatie nodig.

Wanneer u Azure MFA met AD FS 2016 gebruikt en de doeltoepassing onderworpen is aan het beleid voor voorwaardelijke toegang, zijn er aanvullende overwegingen:

* Voorwaardelijke toegang is beschikbaar wanneer de toepassing een relying party is van Azure AD, gefedereerd met AD FS 2016 of nieuwer.
* Voorwaardelijke toegang is niet beschikbaar wanneer de toepassing een relying party is van AD FS 2016 of AD FS 2019 en wordt beheerd of gefedereerd met AD FS 2016 of AD FS 2019.
* Voorwaardelijke toegang is ook niet beschikbaar wanneer AD FS 2016 of AD FS 2019 is geconfigureerd om Azure MFA als primaire verificatiemethode te gebruiken.

#### <a name="ad-fs-logging"></a>AD FS-logboekregistratie

Standaard AD FS 2016 en 2019 die zich aanmelden bij zowel het Windows Security Log als het AD FS-beheerlogboek, bevat informatie over verificatieverzoeken en het succes of falen ervan. Gebeurtenislogboekgegevens binnen deze gebeurtenissen geven aan of Azure MFA is gebruikt. Een AD FS-controlegebeurtenis-id 1200 kan bijvoorbeeld het:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Certificaten vernieuwen en beheren

Op elke AD FS-server, in de lokale computer Mijn winkel, wordt een zelfondertekend Azure MFA-certificaat met de titel OU=Microsoft AD FS Azure MFA geplaatst, dat de vervaldatum van het certificaat bevat. Controleer de geldigheidsperiode van dit certificaat op elke AD FS-server om de vervaldatum te bepalen.

Als de geldigheidsperiode van uw certificaten bijna afloopt, [genereert en verifieert u een nieuw MFA-certificaat op elke AD FS-server.](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers)

In de volgende richtlijnen wordt uitgelegd hoe u de Azure MFA-certificaten op uw AD FS-servers beheert. Wanneer u AD FS configureert met `New-AdfsAzureMfaTenantCertificate` Azure MFA, zijn de certificaten die via de PowerShell-cmdlet worden gegenereerd, twee jaar geldig. Verleng en installeer de vernieuwde certificaten voordat de vervaldatum verloopt om onderbrekingen in de MFA-service te laten vervallen.

## <a name="implement-your-plan"></a>Uw plan implementeren

Nu u uw oplossing hebt gepland, u deze implementeren door de onderstaande stappen te volgen:

1. Voldoen aan de noodzakelijke voorwaarden
   1. [Azure AD Connect implementeren](../hybrid/whatis-hybrid-identity.md) voor hybride scenario's
   1. [Azure AD-toepassingsproxy implementeren](../manage-apps/application-proxy.md) voor on-premises apps die zijn gepubliceerd voor cloudtoegang
   1. [NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) implementeren voor elke RADIUS-verificatie
   1. Ervoor zorgen dat gebruikers een upgrade hebben uitgevoerd naar ondersteunde versies van Microsoft Office met moderne verificatie ingeschakeld
1. Gekozen [verificatiemethoden](#choose-verification-options) configureren
1. Uw [benoemde netwerklocaties definiëren](../conditional-access/location-condition.md#named-locations)
1. Selecteer groepen om MFA uit te rollen.
1. Uw [beleid voor voorwaardelijke toegang configureren](#create-conditional-access-policy)
1. Uw MFA-registratiebeleid configureren
   1. [Gecombineerde MFA en SSPR](howto-registration-mfa-sspr-combined.md)
   1. Met [identiteitsbescherming](../identity-protection/howto-mfa-policy.md)
1. Stuur gebruikersberichten en laat gebruikers zich inschrijven op[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Bijhouden wie er is ingeschreven](#identify-non-registered-users)

> [!TIP]
> Cloudgebruikers van de overheid kunnen zich inschrijven bij[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Uw oplossing beheren

Rapporten voor Azure MFA

Azure Multi-Factor Authentication biedt rapporten via de Azure-portal:

| Rapport | Locatie | Beschrijving |
| --- | --- | --- |
| Meldingen over gebruik en fraude | Azure AD-> aanmeldingen | Biedt informatie over het algemene gebruik, het gebruikersoverzicht en de gebruikersgegevens; evenals een geschiedenis van fraudewaarschuwingen die tijdens het opgegeven datumbereik zijn ingediend. |

## <a name="troubleshoot-mfa-issues"></a>MFA-problemen oplossen

Oplossingen zoeken voor veelvoorkomende problemen met Azure MFA in het [artikel Azure Multi-Factor Authentication oplossen](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) in het Microsoft Support Center.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn verificatiemethoden?](concept-authentication-methods.md)
* [Geconvergeerde registratie inschakelen voor Azure Multi-Factor Authentication en Azure AD selfservice wachtwoord reset](concept-registration-mfa-sspr-converged.md)
* Waarom is een gebruiker gevraagd of niet gevraagd om MFA uit te voeren? Zie de sectie [Azure AD-aanmeldingsrapport in het document Rapporten in Azure Multi-Factor Authentication](howto-mfa-reporting.md#azure-ad-sign-ins-report).
