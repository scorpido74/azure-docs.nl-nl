---
title: Veelgestelde vragen over Azure Active Directory-apparaatbeheer | Microsoft Documenten
description: Veelgestelde vragen over Azure Active Directory-apparaatbeheer.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: c238600d412e53ad665214492e292aa395655b78
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497515"
---
# <a name="azure-active-directory-device-management-faq"></a>Veelgestelde vragen over Azure Active Directory-apparaatbeheer

## <a name="general-faq"></a>Algemene faq

### <a name="q-i-registered-the-device-recently-why-cant-i-see-the-device-under-my-user-info-in-the-azure-portal-or-why-is-the-device-owner-marked-as-na-for-hybrid-azure-active-directory-azure-ad-joined-devices"></a>V: Ik heb het apparaat onlangs geregistreerd. Waarom kan ik het apparaat niet onder mijn gebruikersgegevens zien in de Azure-portal? Of waarom is de apparaateigenaar gemarkeerd als N/A voor hybride Azure Active Directory (Azure AD) samengevoegde apparaten?

**A:** Windows 10-apparaten waarvan hybride Azure AD is toegetreden, worden niet weergegeven onder **GEBRUIKERSapparaten.**
Gebruik de weergave **Alle apparaten** in de Azure-portal. U ook een PowerShell [Get-MsolDevice-cmdlet](/powershell/module/msonline/get-msoldevice?view=azureadps-1.0) gebruiken.

Alleen de volgende apparaten worden vermeld onder **USER-apparaten:**

- Alle persoonlijke apparaten die niet hybride Azure AD zijn, zijn lid geworden. 
- Alle niet-Windows 10- of Windows Server 2016-apparaten.
- Alle niet-Windows-apparaten. 

---

### <a name="q-how-do-i-know-what-the-device-registration-state-of-the-client-is"></a>V: Hoe weet ik wat de status van de apparaatregistratie van de client is?

**A:** Ga in de Azure-portal naar **Alle apparaten**. Zoek naar het apparaat met behulp van de apparaat-ID. Controleer de waarde onder de kolom Jointype. Soms kan het apparaat worden gereset of opnieuw worden weergegeven. Het is dus essentieel om ook de status van de registratie van het apparaat op het apparaat te controleren:

- Voer voor apparaten van Windows 10 en Windows `dsregcmd.exe /status`Server 2016 of hoger uit.
- Voer voor down-level `%programFiles%\Microsoft Workplace Join\autoworkplace.exe`OS-versies uit .

**A:** Zie de volgende artikelen voor informatie over het oplossen van problemen:
- [Probleemoplossing voor apparaten met de opdracht dsregcmd](troubleshoot-device-dsregcmd.md)
- [Problemen met hybride Azure Active Directory mogelijk maken met Windows 10- en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure Active Directory mogelijk maken van apparaten op downniveau](troubleshoot-hybrid-join-windows-legacy.md)

---

### <a name="q-i-see-the-device-record-under-the-user-info-in-the-azure-portal-and-i-see-the-state-as-registered-on-the-device-am-i-set-up-correctly-to-use-conditional-access"></a>V: Ik zie de apparaatrecord onder de gebruikersgegevens in de Azure-portal. En ik zie de staat als geregistreerd op het apparaat. Ben ik correct ingesteld om voorwaardelijke toegang te gebruiken?

**A:** De status van de samengevoegde apparaat, weergegeven door **deviceID,** moet overeenkomen met de status van Azure AD en voldoen aan eventuele evaluatiecriteria voor voorwaardelijke toegang. Zie [Beheerde apparaten vereisen voor toegang tot de cloud-app met voorwaardelijke toegang](../conditional-access/require-managed-devices.md)voor meer informatie.

---

### <a name="q-why-do-my-users-see-an-error-message-saying-your-organization-has-deleted-the-device-or-your-organization-has-disabled-the-device-on-their-windows-10-devices"></a>V: Waarom zien mijn gebruikers een foutbericht met de tekst 'Uw organisatie heeft het apparaat verwijderd' of 'Uw organisatie heeft het apparaat uitgeschakeld' op hun Windows 10-apparaten?

**A:** Op Windows 10-apparaten die zijn samengevoegd of geregistreerd bij Azure AD, krijgen gebruikers een [PRT (Primary refresh token)](concept-primary-refresh-token.md) waarmee eenmalig aanmelden mogelijk is. De geldigheid van de PRT is gebaseerd op de geldigheid van het apparaat zelf. Gebruikers zien dit bericht als het apparaat is verwijderd of uitgeschakeld in Azure AD zonder de actie vanaf het apparaat zelf te starten. Een apparaat kan in Azure AD worden verwijderd of uitgeschakeld, een van de volgende scenario's: 

- De gebruiker schakelt het apparaat uit vanuit de mijn apps-portal. 
- Een beheerder (of gebruiker) verwijdert of schakelt het apparaat uit in de Azure-portal of met PowerShell
- Hybride Azure AD is alleen lid geworden: een beheerder verwijdert de apparaat-organisatie-eenheid uit de synchronisatiebereik, waardoor de apparaten worden verwijderd uit Azure AD
- Azure AD upgraden verbinding maken met versie 1.4.xx.x. [Inzicht in Azure AD Connect 1.4.xx.x en het verdwijnen van apparaten](/azure/active-directory/hybrid/reference-connect-device-disappearance).


Zie hieronder hoe deze acties kunnen worden rechtgezet.

---

### <a name="q-i-disabled-or-deleted-my-device-in-the-azure-portal-or-by-using-windows-powershell-but-the-local-state-on-the-device-says-its-still-registered-what-should-i-do"></a>V: Ik heb mijn apparaat uitgeschakeld of verwijderd in de Azure-portal of met Windows PowerShell. Maar de lokale staat op het apparaat zegt dat het nog steeds geregistreerd is. Wat moet ik doen?

**A:** Deze bewerking is door het ontwerp. In dit geval heeft het apparaat geen toegang tot bronnen in de cloud. Beheerders kunnen deze actie uitvoeren voor verouderde, verloren of gestolen apparaten om onbevoegde toegang te voorkomen. Als deze actie onbedoeld is uitgevoerd, moet u het apparaat opnieuw in- of opnieuw registreren zoals hieronder beschreven

- Als het apparaat is uitgeschakeld in Azure AD, kan een beheerder met voldoende bevoegdheden het inschakelen vanuit de Azure AD-portal  
  > [!NOTE]
  > Als u apparaten synchroniseert met Azure AD Connect, worden hybride Azure AD-apparaten automatisch opnieuw ingeschakeld tijdens de volgende synchronisatiecyclus. Als u een hybride Azure AD-apparaat moet uitschakelen, moet u het apparaat dus uitschakelen vanuit uw on-premises AD

 - Als het apparaat wordt verwijderd in Azure AD, moet u het apparaat opnieuw registreren. Als u zich opnieuw wilt registreren, moet u een handmatige actie uitvoeren op het apparaat. Zie hieronder voor instructies voor herregistratie op basis van de apparaatstatus. 

      Als u hybride Azure AD opnieuw wilt registreren, gaat u als volgt te werk om hybride Azure AD opnieuw te registreren bij Windows 10- en Windows Server 2016/2019-apparaten:

      1. Open de opdrachtprompt als beheerder.
      1. Voer `dsregcmd.exe /debug /leave` in.
      1. Meld u af en meld u aan om de geplande taak te activeren die het apparaat opnieuw registreert met Azure AD. 

      Ga de volgende stappen uit voor Windows OS-versies op benedenniveau die hybride Azure AD zijn:

      1. Open de opdrachtprompt als beheerder.
      1. Voer `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /l"` in.
      1. Voer `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe /j"` in.

      Ga voor Azure AD-apparaten Windows 10-apparaten de volgende stappen uit:

      1. De opdrachtprompt openen als beheerder
      1. Enter `dsregcmd /forcerecovery` (Opmerking: u moet een beheerder zijn om deze actie uit te voeren).
      1. Klik op 'Aanmelden' in het dialoogvenster dat wordt geopend en ga verder met het aanmeldingsproces.
      1. Meld u af en meld u af bij het apparaat om het herstel te voltooien.

      Ga voor door Azure AD-geregistreerde Windows 10-apparaten de volgende stappen uit:

      1. Ga naar **Instellingen** > **Accounts** > **Toegang werk of school**. 
      1. Selecteer het account en selecteer **Verbinding verbreken**.
      1. Klik op "+ Connect" en registreer het apparaat opnieuw door het aanmeldingsproces te doorlopen.

---

### <a name="q-why-do-i-see-duplicate-device-entries-in-the-azure-portal"></a>V: Waarom zie ik dubbele apparaatvermeldingen in de Azure-portal?

**A:**

- Voor Windows 10 en Windows Server 2016 kunnen herhaalde pogingen om de join en de terugkeer van hetzelfde apparaat los te maken, dubbele vermeldingen veroorzaken. 
- Elke Windows-gebruiker die **Werk of schoolaccount toevoegen** gebruikt, maakt een nieuwe apparaatrecord met dezelfde apparaatnaam.
- Voor down-level Windows OS-versies die on-premises Azure Directory-domein zijn samengevoegd, maakt automatische registratie een nieuwe apparaatrecord met dezelfde apparaatnaam voor elke domeingebruiker die zich aanmeldt bij het apparaat. 
- Een Azure AD-gekoppelde machine die is gewist, opnieuw is geïnstalleerd en opnieuw is verbonden met dezelfde naam, wordt weergegeven als een andere record met dezelfde apparaatnaam.

---

### <a name="q-does-windows-10-device-registration-in-azure-ad-support-tpms-in-fips-mode"></a>V: Ondersteunt Windows 10-apparaatregistratie in Azure AD TPM's in de FIPS-modus?

**A:** Windows 10-apparaatregistratie wordt alleen ondersteund voor TPM 2.0 die voldoet aan FIPS-compatibel en wordt niet ondersteund voor TPM 1.2. Als uw apparaten FIPS-compatibele TPM 1.2 hebben, moet u ze uitschakelen voordat u verdergaat met Azure AD join of Hybrid Azure AD join. Microsoft biedt geen tools voor het uitschakelen van de FIPS-modus voor TPM's, omdat deze afhankelijk is van de TPM-fabrikant. Neem contact op met uw hardware-OEM voor ondersteuning. 

---

**V: Waarom heeft een gebruiker nog steeds toegang tot bronnen vanaf een apparaat dat ik heb uitgeschakeld in de Azure-portal?**

**A:** Het duurt maximaal een uur voordat een intrek wordt toegepast vanaf het moment dat het Azure AD-apparaat is gemarkeerd als uitgeschakeld.

>[!NOTE] 
>Voor geregistreerde apparaten raden we u aan het apparaat te wissen om ervoor te zorgen dat gebruikers geen toegang hebben tot de bronnen. Zie [Wat is apparaatinschrijving?](/mem/intune/user-help/use-managed-devices-to-get-work-done) 

---

### <a name="q-why-are-there-devices-marked-as-pending-under-the-registered-column-in-the-azure-portal"></a>V: Waarom zijn er apparaten gemarkeerd als 'In behandeling' onder de kolom GEREGISTREERD in de Azure-portal?

**A:** In behandeling geeft aan dat het apparaat niet is geregistreerd. Deze status geeft aan dat een apparaat is gesynchroniseerd met Azure AD-verbinding vanuit een on-premises AD en klaar is voor apparaatregistratie. Deze apparaten hebben het JOIN TYPE ingesteld op 'Hybrid Azure AD joined'. Meer informatie over [het plannen van uw hybride Azure Active Directory join-implementatie](hybrid-azuread-join-plan.md).

>[!NOTE]
>Een apparaat kan ook veranderen van het hebben van een geregistreerde status naar 'In behandeling'
>* Als een apparaat eerst uit Azure AD wordt verwijderd en opnieuw wordt gesynchroniseerd vanuit een on-premises AD.
>* Als een apparaat wordt verwijderd uit een synchronisatiebereik op Azure AD Connect en weer wordt toegevoegd.
>
>In beide gevallen moet u het apparaat handmatig opnieuw registreren op elk van deze apparaten. Als u wilt controleren of het apparaat eerder is geregistreerd, u [apparaten oplossen met de opdracht dsregcmd.](troubleshoot-device-dsregcmd.md)

---
## <a name="azure-ad-join-faq"></a>Azure AD doet lid van veelgestelde vragen

### <a name="q-how-do-i-unjoin-an-azure-ad-joined-device-locally-on-the-device"></a>V: Hoe kan ik de anfstandsbediening voor een Azure AD-apparaat lokaal op het apparaat ongedaan maken?

**A:** Controleer voor pure Azure AD-apparaten of u een offline lokaal beheerdersaccount hebt of maakt er een. U zich niet aanmelden met azure AD-gebruikersreferenties. Ga vervolgens naar **Instellingen** > **accounts** > **Toegang werk of school**. Selecteer uw account en selecteer **Verbinding verbreken**. Volg de aanwijzingen en geef de lokale beheerdersreferenties op wanneer daarom wordt gevraagd. Start het apparaat opnieuw op om het ongedaan maken proces te voltooien.

---

### <a name="q-can-my-users-sign-in-to-azure-ad-joined-devices-that-are-deleted-or-disabled-in-azure-ad"></a>V: Kunnen de aanmeldingen van mijn gebruikers bij Azure AD worden samengevoegd met apparaten die zijn verwijderd of uitgeschakeld in Azure AD?

**A:** Ja. Windows heeft een gebruikersnaam en wachtwoordmogelijkheid in de cache waarmee gebruikers die zich eerder hebben aangemeld, snel toegang hebben tot het bureaublad, zelfs zonder netwerkconnectiviteit. 

Wanneer een apparaat wordt verwijderd of uitgeschakeld in Azure AD, is het niet bekend bij het Windows-apparaat. Gebruikers die zich eerder hebben aangemeld, blijven dus toegang krijgen tot het bureaublad met de gebruikersnaam en het wachtwoord in de cache. Maar als het apparaat wordt verwijderd of uitgeschakeld, hebben gebruikers geen toegang tot bronnen die worden beschermd door voorwaardelijke toegang op het apparaat. 

Gebruikers die zich niet eerder hebben aangemeld, hebben geen toegang tot het apparaat. Er is geen gebruikersnaam en wachtwoord in de cache ingeschakeld voor hen. 

---

### <a name="q-can-a-disabled-or-deleted-user-sign-in-to-an-azure-ad-joined-devices"></a>V: Kan een uitgeschakelde of verwijderde gebruiker zich aanmelden bij een Azure AD-apparaten

**A:** Ja, maar slechts voor een beperkte tijd. Wanneer een gebruiker wordt verwijderd of uitgeschakeld in Azure AD, is deze niet onmiddellijk bekend bij het Windows-apparaat. Gebruikers die zich eerder hebben aangemeld, hebben dus toegang tot het bureaublad met de gebruikersnaam en het wachtwoord in de cache. 

Doorgaans is het apparaat zich binnen vier uur bewust van de gebruikersstatus. Vervolgens blokkeert Windows de toegang van die gebruikers tot het bureaublad. Als de gebruiker wordt verwijderd of uitgeschakeld in Azure AD, worden al hun tokens ingetrokken. Dus ze hebben geen toegang tot bronnen. 

Verwijderde of uitgeschakelde gebruikers die zich niet eerder hebben aangemeld, hebben geen toegang tot een apparaat. Er is geen gebruikersnaam en wachtwoord in de cache ingeschakeld voor hen. 

---

### <a name="q-why-do-my-users-have-issues-on-azure-ad-joined-devices-after-changing-their-upn"></a>V: Waarom hebben mijn gebruikers problemen op Azure AD-apparaten nadat ze hun UPN hebben gewijzigd?

**A:** Op dit moment worden UPN-wijzigingen niet volledig ondersteund op azure AD-apparaten. Hun verificatie met Azure AD mislukt dus nadat hun UPN is gewijzigd. Als gevolg hiervan hebben gebruikers SSO- en Conditional Access-problemen op hun apparaten. Op dit moment moeten gebruikers zich aanmelden bij Windows via de tegel 'Andere gebruiker' met behulp van hun nieuwe UPN om dit probleem op te lossen. We zijn momenteel bezig met het aanpakken van dit probleem. Gebruikers die zich aanmelden bij Windows Hello for Business hebben echter geen te maken met dit probleem. 

---

### <a name="q-my-users-cant-search-printers-from-azure-ad-joined-devices-how-can-i-enable-printing-from-those-devices"></a>V: Mijn gebruikers kunnen niet zoeken in printers van Azure AD-apparaten. Hoe kan ik afdrukken vanaf die apparaten inschakelen?

**A:** Zie [Windows Server Hybrid Cloud Print implementeren met pre-authenticatie](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy)als u printers voor Azure AD-apparaten wilt implementeren. U hebt een on-premises Windows Server nodig om hybride cloudprint te implementeren. Momenteel is de cloudgebaseerde afdrukservice niet beschikbaar. 

---

### <a name="q-how-do-i-connect-to-a-remote-azure-ad-joined-device"></a>V: Hoe maak ik verbinding met een extern Azure AD-apparaat?

**A:** Zie [Verbinding maken met externe Azure Active Directory-aangesloten pc](/windows/client-management/connect-to-remote-aadj-pc).

---

### <a name="q-why-do-my-users-see-you-cant-get-there-from-here"></a>V: Waarom zien mijn gebruikers *dat je er vanaf hier niet komen?*

**A:** Hebt u bepaalde regels voor voorwaardelijke toegang geconfigureerd om een specifieke apparaatstatus te vereisen? Als het apparaat niet aan de criteria voldoet, worden gebruikers geblokkeerd en zien ze dat bericht. Evalueer de beleidsregels voor voorwaardelijke toegang. Zorg ervoor dat het apparaat voldoet aan de criteria om het bericht te vermijden.

---

### <a name="q-why-dont-some-of-my-users-get-azure-multi-factor-authentication-prompts-on-azure-ad-joined-devices"></a>V: Waarom krijgen sommige van mijn gebruikers geen Azure Multi-Factor Authentication-prompts op Azure AD-apparaten?

**A:** Een gebruiker kan lid worden of een apparaat registreren bij Azure AD met behulp van Multi-Factor Authentication. Dan wordt het apparaat zelf een vertrouwde tweede factor voor die gebruiker. Wanneer dezelfde gebruiker zich aanmeldt bij het apparaat en toegang heeft tot een toepassing, beschouwt Azure AD het apparaat als een tweede factor. Het stelt die gebruiker in staat om naadloos toegang te krijgen tot toepassingen zonder extra multi-factor authenticatie prompts. 

Dit gedrag:

- Is van toepassing op Azure AD-samengevoegde en Azure AD-geregistreerde apparaten , maar niet voor hybride Azure AD-apparaten.
- Is niet van toepassing op andere gebruikers die zich bij dat apparaat aanmeldt. Dus alle andere gebruikers die toegang hebben tot dat apparaat krijgen een Multi-Factor Authentication uitdaging. Vervolgens hebben ze toegang tot toepassingen waarvoor multi-factorauthenticatie vereist is.

---

### <a name="q-why-do-i-get-a-username-or-password-is-incorrect-message-for-a-device-i-just-joined-to-azure-ad"></a>V: Waarom krijg ik een *gebruikersnaam of wachtwoord is onjuist* bericht voor een apparaat waar ik net lid van Azure AD?

**A:** Veelvoorkomende redenen voor dit scenario zijn:

- Uw gebruikersreferenties zijn niet langer geldig.
- Uw computer kan niet communiceren met Azure Active Directory. Controleer op problemen met de netwerkverbinding.
- Federatieve aanmeldingen vereisen dat uw federatieserver WS-Trust-eindpunten ondersteunt die zijn ingeschakeld en toegankelijk zijn. 
- U hebt pass-through-verificatie ingeschakeld. Uw tijdelijke wachtwoord moet dus worden gewijzigd wanneer u zich aanmeldt.

---

### <a name="q-why-do-i-see-the-oops-an-error-occurred-dialog-when-i-try-to-azure-ad-join-my-pc"></a>V: Waarom zie ik de *Oeps... er is een fout opgetreden!* dialoogvenster wanneer ik azure AD probeer toe te voegen aan mijn pc?

**A:** Deze fout treedt op wanneer u Azure Active Directory-inschrijving instelt bij Intune. Zorg ervoor dat de gebruiker die Azure AD probeert aan te sluiten, de juiste Intune-licentie heeft toegewezen. Zie [Inschrijving instellen voor Windows-apparaten voor](/intune/windows-enroll)meer informatie .  

---

### <a name="q-why-did-my-attempt-to-azure-ad-join-a-pc-fail-although-i-didnt-get-any-error-information"></a>V: Waarom is mijn poging om Azure AD lid te maken van een pc mislukt, hoewel ik geen foutgegevens heb gekregen?

**A:** Een waarschijnlijke oorzaak is dat u zich bij het apparaat hebt aangemeld met het lokale ingebouwde beheerdersaccount. Maak een ander lokaal account voordat u Azure Active Directory join gebruikt om de installatie te voltooien. 

---

### <a name="qwhat-are-the-ms-organization-p2p-access-certificates-present-on-our-windows-10-devices"></a>V: Wat zijn de MS-Organization-P2P-Access-certificaten aanwezig op onze Windows 10-apparaten?

**A:** De MS-Organization-P2P-Access-certificaten worden uitgegeven door Azure AD aan beide, Azure AD samengevoegd en hybride Azure AD samengevoegde apparaten. Deze certificaten worden gebruikt om vertrouwen tussen apparaten in dezelfde tenant in te schakelen voor extern bureaublad-scenario's. Een certificaat wordt afgegeven aan het apparaat en een ander wordt afgegeven aan de gebruiker. Het apparaatcertificaat is `Local Computer\Personal\Certificates` aanwezig en is één dag geldig. Dit certificaat wordt vernieuwd (door een nieuw certificaat uit te geven) als het apparaat nog steeds actief is in Azure AD. Het gebruikerscertificaat is `Current User\Personal\Certificates` aanwezig en dit certificaat is ook één dag geldig, maar het wordt on-demand uitgegeven wanneer een gebruiker een externe desktopsessie probeert naar een ander Azure AD-apparaat. Het wordt niet verlengd bij het verstrijken. Beide certificaten worden uitgegeven met behulp van het MS-Organization-P2P-Access certificaat aanwezig in de `Local Computer\AAD Token Issuer\Certificates`. Dit certificaat wordt uitgegeven door Azure AD tijdens de registratie van het apparaat. 

---

### <a name="qwhy-do-i-see-multiple-expired-certificates-issued-by-ms-organization-p2p-access-on-our-windows-10-devices-how-can-i-delete-them"></a>V: Waarom zie ik meerdere verlopen certificaten die zijn uitgegeven door MS-Organization-P2P-Access op onze Windows 10-apparaten? Hoe kan ik ze verwijderen?

**A:** Er was een probleem dat werd geïdentificeerd op Windows 10 versie 1709 en lager waar verlopen MS-Organization-P2P-Access-certificaten bleven bestaan op de computer winkel als gevolg van cryptografische problemen. Uw gebruikers kunnen problemen ondervinden met de netwerkconnectiviteit als u VPN-clients gebruikt (bijvoorbeeld Cisco AnyConnect) die het grote aantal verlopen certificaten niet aankunnen. Dit probleem is opgelost in windows 10 1803-release om dergelijke verlopen MS-Organization-P2P-Access-certificaten automatisch te verwijderen. U dit probleem oplossen door uw apparaten bij te werken naar Windows 10 1803. Als u niet updaten, u deze certificaten zonder nadelige gevolgen verwijderen.  

---

## <a name="hybrid-azure-ad-join-faq"></a>Hybride Azure AD deelnemen aan veelgestelde vragen

### <a name="q-how-do-i-unjoin-a-hybrid-azure-ad-joined-device-locally-on-the-device"></a>V: Hoe kan ik de join van een hybride Azure AD-apparaat lokaal op het apparaat losmaken?

**A:** Voor hybride Azure AD-apparaten moet u de automatische registratie uitschakelen. Vervolgens registreert de geplande taak het apparaat niet opnieuw. Open vervolgens een opdrachtprompt als `dsregcmd.exe /debug /leave`beheerder en voer . Of voer deze opdracht uit als een script op verschillende apparaten om de deelname in bulk ongedaan te maken.

### <a name="q-where-can-i-find-troubleshooting-information-to-diagnose-hybrid-azure-ad-join-failures"></a>V: Waar kan ik probleemoplossingsgegevens vinden om hybride Azure AD-joinfouten te diagnosticeren?

**A:** Zie de volgende artikelen voor informatie over het oplossen van problemen:

- [Problemen met hybride Azure Active Directory mogelijk maken met Windows 10- en Windows Server 2016-apparaten](troubleshoot-hybrid-join-windows-current.md)
- [Problemen met hybride Azure Active Directory mogelijk maken van apparaten op downniveau](troubleshoot-hybrid-join-windows-legacy.md)
 
### <a name="q-why-do-i-see-a-duplicate-azure-ad-registered-record-for-my-windows-10-hybrid-azure-ad-joined-device-in-the-azure-ad-devices-list"></a>V: Waarom zie ik een dubbele Azure AD-record voor mijn Windows 10 hybride Azure AD-apparaat in de lijst met Azure AD-apparaten?

**A:** Wanneer uw gebruikers hun accounts toevoegen aan apps op een apparaat dat is verbonden met een domein, wordt deze mogelijk gevraagd met **Account toevoegen aan Windows?** Als ze **Ja** invoeren op de prompt, registreert het apparaat zich bij Azure AD. Het type vertrouwenstype is gemarkeerd als Azure AD-geregistreerd. Nadat u hybride Azure AD hebt ingeschakeld, wordt het apparaat ook hybride Azure AD samengevoegd. Dan verschijnen er twee apparaatstatussen voor hetzelfde apparaat. 

Hybride Azure AD-join heeft voorrang op de status azure AD.Hybrid Azure AD join heeft voorrang op de azure AD-status. Uw apparaat wordt dus beschouwd als hybride Azure AD dat is samengevoegd voor verificatie en evaluatie van voorwaardelijke toegang. U de azure AD-record voor geregistreerde apparaten veilig verwijderen uit de Azure AD-portal. Leer [deze dubbele status op de Windows 10-machine te vermijden of op te ruimen.](hybrid-azuread-join-plan.md#review-things-you-should-know) 

---

### <a name="q-why-do-my-users-have-issues-on-windows-10-hybrid-azure-ad-joined-devices-after-changing-their-upn"></a>V: Waarom hebben mijn gebruikers problemen met windows 10 hybride Azure AD-apparaten nadat ze hun UPN hebben gewijzigd?

**A:** Momenteel worden UPN-wijzigingen niet volledig ondersteund met hybride Azure AD-apparaten. Hoewel gebruikers zich bij het apparaat kunnen aanmelden en toegang hebben tot hun on-premises toepassingen, mislukt verificatie met Azure AD na een UPN-wijziging. Als gevolg hiervan hebben gebruikers SSO- en Conditional Access-problemen op hun apparaten. Op dit moment moet u de a-up van het apparaat vanaf Azure AD (dsregcmd /leave" met verhoogde bevoegdheden uitvoeren) en opnieuw deelnemen (gebeurt automatisch) om het probleem op te lossen. We zijn momenteel bezig met het aanpakken van dit probleem. Gebruikers die zich aanmelden bij Windows Hello for Business hebben echter geen te maken met dit probleem. 

---

### <a name="q-do-windows-10-hybrid-azure-ad-joined-devices-require-line-of-sight-to-the-domain-controller-to-get-access-to-cloud-resources"></a>V: Hebben windows 10 hybride Azure AD-apparaten een zichtlijn naar de domeincontroller nodig om toegang te krijgen tot cloudbronnen?

**A:** Nee, behalve wanneer het wachtwoord van de gebruiker wordt gewijzigd. Nadat windows 10 hybride Azure AD-deelname is voltooid en de gebruiker zich ten minste één keer heeft aangemeld, heeft het apparaat geen zichtlijn nodig voor de domeincontroller om toegang te krijgen tot cloudbronnen. Windows 10 kan eenmalig aanmelden voor Azure AD-toepassingen overal met een internetverbinding krijgen, behalve wanneer een wachtwoord wordt gewijzigd. Gebruikers die zich aanmelden bij Windows Hello for Business blijven eenmalige aanmelding bij Azure AD-toepassingen ontvangen, zelfs na een wachtwoordwijziging, zelfs als ze geen zichtlijn hebben op hun domeincontroller. 

---

### <a name="q-what-happens-if-a-user-changes-their-password-and-tries-to-login-to-their-windows-10-hybrid-azure-ad-joined-device-outside-the-corporate-network"></a>V: Wat gebeurt er als een gebruiker zijn wachtwoord wijzigt en probeert in te loggen op zijn windows 10 hybride Azure AD-apparaat buiten het bedrijfsnetwerk?

**A:** Als een wachtwoord buiten het bedrijfsnetwerk wordt gewijzigd (bijvoorbeeld door Azure AD SSPR te gebruiken), mislukt de gebruiker met het nieuwe wachtwoord. Voor hybride Azure AD-apparaten is on-premises Active Directory de primaire autoriteit. Wanneer een apparaat geen zichtlijn heeft voor de domeincontroller, kan het het nieuwe wachtwoord niet worden gevalideerd. De gebruiker moet dus verbinding maken met de domeincontroller (via VPN of in het bedrijfsnetwerk) voordat hij zich met zijn nieuwe wachtwoord bij het apparaat kan aanmelden. Anders kunnen ze zich alleen aanmelden met hun oude wachtwoord vanwege de in de cache opgeslagen aanmeldingsmogelijkheid in Windows. Het oude wachtwoord wordt echter ongeldig gemaakt door Azure AD tijdens tokenaanvragen en voorkomt daarom eenmalige aanmelding en voldoet niet aan een op apparaten gebaseerd beleid voor voorwaardelijke toegang. Dit probleem treedt niet op als u Windows Hello for Business gebruikt. 

---

## <a name="azure-ad-register-faq"></a>Veelgestelde vragen over Azure AD-register

### <a name="q-how-do-i-remove-an-azure-ad-registered-state-for-a-device-locally"></a>V: Hoe verwijder ik een azure AD-status die is geregistreerd voor een apparaat lokaal?

**A:** 
- Ga voor door Windows 10 Azure AD-apparaten geregistreerde apparaten naar **Instellingen** > **Voor Toegang** > **tot accounts of School**. Selecteer uw account en selecteer **Verbinding verbreken**. Apparaatregistratie is per gebruikersprofiel op Windows 10.
- Voor iOS en Android u de **Settings** > **apparaatregistratie** voor microsoft authenticator-toepassingen gebruiken en **apparaat uitschrijven**selecteren.
- Voor macOS u de Microsoft Intune Company Portal-toepassing gebruiken om het apparaat uit te schrijven voor beheer en elke registratie te verwijderen. 

---
### <a name="q-how-can-i-block-users-from-adding-additional-work-accounts-azure-ad-registered-on-my-corporate-windows-10-devices"></a>V: Hoe kan ik voorkomen dat gebruikers extra werkaccounts (Azure AD-geregistreerd) toevoegen op mijn zakelijke Windows 10-apparaten?

**A:** Schakel het volgende register in om te voorkomen dat uw gebruikers extra werkaccounts toevoegen aan uw bedrijfsdomein, Azure AD is lid geworden of hybride Azure AD is lid geworden van Windows 10-apparaten. Dit beleid kan ook worden gebruikt om domeinsamengevoegde machines te blokkeren, omdat azure AD per ongeluk is geregistreerd met hetzelfde gebruikersaccount. 

`HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001`

---
### <a name="q-can-i-register-android-or-ios-byod-devices"></a>V: Kan ik Android- of iOS BYOD-apparaten registreren?

**A:** Ja, maar alleen met de Azure-apparaatregistratieservice en voor hybride klanten. Het wordt niet ondersteund met de on-premises apparaatregistratieservice in Active Directory Federation Services (AD FS).

---
### <a name="q-how-can-i-register-a-macos-device"></a>V: Hoe kan ik een macOS-apparaat registreren?

**A:** Neem de volgende stappen:

1.    [Een nalevingsbeleid maken](/intune/compliance-policy-create-mac-os)
1.    [Een beleid voor voorwaardelijke toegang definiëren voor macOS-apparaten](../active-directory-conditional-access-azure-portal.md) 

**Opmerkingen:**

- De gebruikers die zijn opgenomen in uw beleid voor voorwaardelijke toegang hebben een [ondersteunde versie van Office voor macOS](../conditional-access/concept-conditional-access-conditions.md) nodig om toegang te krijgen tot bronnen. 
- Tijdens de eerste toegangspoging wordt uw gebruikers gevraagd het apparaat in te schrijven via de bedrijfsportal.

---
## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [azure AD-geregistreerde apparaten](concept-azure-ad-register.md)
- Meer informatie over [azure AD-apparaten](concept-azure-ad-join.md)
- Meer informatie over [hybride Azure AD-apparaten](concept-azure-ad-join-hybrid.md)
