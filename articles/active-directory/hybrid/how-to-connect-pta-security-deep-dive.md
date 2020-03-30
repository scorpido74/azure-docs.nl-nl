---
title: Azure Active Directory Pass-through Authentication security deep dive| Microsoft Documenten
description: In dit artikel wordt beschreven hoe Azure Active Directory (Azure AD) Pass-through Authentication uw on-premises accounts beschermt
services: active-directory
keywords: Azure AD Connect Pass-Through Authentication, installeer Active Directory, vereiste onderdelen voor Azure AD, SSO, Single Sign-on
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/15/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1ddce8d4d7ca1f03c0a57d0f0c8c41ac122973e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185563"
---
# <a name="azure-active-directory-pass-through-authentication-security-deep-dive"></a>Azure Active Directory Pass-through Authentication security deep dive

In dit artikel vindt u een meer gedetailleerde beschrijving van de werking van Azure Active Directory (Azure AD) Pass-through Authentication. Het richt zich op de beveiligingsaspecten van de functie. Dit artikel is bedoeld voor beveiligings- en IT-beheerders, chief compliance- en security officers en andere IT-professionals die verantwoordelijk zijn voor IT-beveiliging en compliance bij kleine tot middelgrote organisaties of grote ondernemingen.

De behandelde onderwerpen zijn:
- Gedetailleerde technische informatie over het installeren en registreren van de verificatieagents.
- Gedetailleerde technische informatie over de versleuteling van wachtwoorden tijdens het aanmelden van gebruikers.
- De beveiliging van de kanalen tussen on-premises verificatieagents en Azure AD.
- Gedetailleerde technische informatie over het operationeel beveiligen van de verificatieagents.
- Andere beveiligingsgerelateerde onderwerpen.

## <a name="key-security-capabilities"></a>Belangrijkste beveiligingsmogelijkheden

Dit zijn de belangrijkste beveiligingsaspecten van deze functie:
- Het is gebouwd op een veilige multi-tenant architectuur die isolatie van aanmeldingsverzoeken tussen huurders biedt.
- On-premises wachtwoorden worden nooit in welke vorm dan ook in de cloud opgeslagen.
- On-premises verificatiemedewerkers die naar wachtwoordvalidatieverzoeken luisteren en erop reageren, maken alleen uitgaande verbindingen vanuit uw netwerk. Er is geen verplichting om deze verificatieagents in een perimeternetwerk (DMZ) te installeren. Als aanbevolen procedures behandelt u alle servers met verificatieagents als Tier 0-systemen (zie [referentie).](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)
- Alleen standaardpoorten (80 en 443) worden gebruikt voor uitgaande communicatie van de verificatieagents naar Azure AD. U hoeft geen binnenkomende poorten op uw firewall te openen. 
  - Poort 443 wordt gebruikt voor alle geverifieerde uitgaande communicatie.
  - Poort 80 wordt alleen gebruikt voor het downloaden van de Certificate Revocation Lists (CRL's) om ervoor te zorgen dat geen van de certificaten die door deze functie worden gebruikt, is ingetrokken.
  - Zie [Azure Active Directory Pass-through Authentication: Quickstart voor](how-to-connect-pta-quick-start.md#step-1-check-the-prerequisites)de volledige lijst met netwerkvereisten.
- Wachtwoorden die gebruikers opgeven tijdens het aanmelden, worden versleuteld in de cloud voordat de on-premises verificatiemedewerkers deze accepteren voor validatie tegen Active Directory.
- Het HTTPS-kanaal tussen Azure AD en de on-premises verificatieagent wordt beveiligd met wederzijdse verificatie.
- Beschermt uw gebruikersaccounts door naadloos te werken met [azure AD Conditional Access-beleidsregels](../active-directory-conditional-access-azure-portal.md), waaronder Multi-Factor Authentication (MFA), [het blokkeren van verouderde verificatie](../conditional-access/concept-conditional-access-conditions.md) en door [wachtwoordaanvallen van brute force uit te filteren.](../authentication/howto-password-smart-lockout.md)

## <a name="components-involved"></a>Betrokken onderdelen

Zie het [Vertrouwenscentrum](https://azure.microsoft.com/support/trust-center/)voor algemene informatie over de operationele, service- en gegevensbeveiliging van Azure AD . De volgende onderdelen zijn betrokken wanneer u Pass-through Authentication gebruikt voor gebruikersaanmelding:
- **Azure AD STS**: een stateless security token service (STS) die aanmeldingsaanvragen verwerkt en waar nodig beveiligingstokens uitgeeft aan browsers, clients of services van gebruikers.
- **Azure Service Bus**: biedt cloudcommunicatie met zakelijke berichten en stuurt communicatie door waarmee u on-premises oplossingen met de cloud verbinden.
- **Azure AD Connect-verificatieagent:** een on-premises component die naar aanvragen voor wachtwoordvalidatie luistert en reageert.
- **Azure SQL Database:** bevat informatie over de verificatieagents van uw tenant, inclusief hun metagegevens en versleutelingssleutels.
- **Active Directory:** On-premises Active Directory, waar uw gebruikersaccounts en hun wachtwoorden worden opgeslagen.

## <a name="installation-and-registration-of-the-authentication-agents"></a>Installatie en registratie van de verificatieagenten

Verificatieagents worden geïnstalleerd en geregistreerd bij Azure AD wanneer u:
   - [Pass-through-verificatie inschakelen via Azure AD Connect](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-2-enable-the-feature)
   - [Meer verificatiegemachtigden toevoegen om de hoge beschikbaarheid van aanmeldingsverzoeken te garanderen](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-pass-through-authentication-quick-start#step-4-ensure-high-availability) 
   
Het aan de werk laten gaan van een verificatieagent omvat drie hoofdfasen:

1. Installatie van verificatieagent
2. Registratie van verificatieagent
3. Initialisatie van verificatieagent

In de volgende secties worden deze fasen in detail besproken.

### <a name="authentication-agent-installation"></a>Installatie van verificatieagent

Alleen globale beheerders kunnen een verificatieagent (met Azure AD Connect of standalone) installeren op een on-premises server. De installatie voegt twee nieuwe vermeldingen toe aan de lijst > **Programma's** > **en functies van** het **Configuratiescherm:**
- De verificatieagent-toepassing zelf. Deze toepassing wordt uitgevoerd met [NetworkService-bevoegdheden.](https://msdn.microsoft.com/library/windows/desktop/ms684272.aspx)
- De Updater-toepassing die wordt gebruikt om de verificatieagent automatisch bij te werken. Deze toepassing wordt uitgevoerd met [LocalSystem-bevoegdheden.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

### <a name="authentication-agent-registration"></a>Registratie van verificatieagent

Nadat u de verificatieagent hebt geïnstalleerd, moet deze zich registreren bij Azure AD. Azure AD wijst elke verificatieagent een uniek certificaat met digitale identiteit toe dat het kan gebruiken voor veilige communicatie met Azure AD.

De registratieprocedure bindt ook de verificatieagent met uw tenant. Dit zorgt ervoor dat Azure AD weet dat deze specifieke verificatieagent de enige is die gemachtigd is om wachtwoordvalidatieaanvragen voor uw tenant af te handelen. Deze procedure wordt herhaald voor elke nieuwe verificatieagent die u registreert.

De verificatiegemachtigden gebruiken de volgende stappen om zichzelf te registreren bij Azure AD:

![Agentregistratie](./media/how-to-connect-pta-security-deep-dive/pta1.png)

1. Azure AD vraagt eerst dat een globale beheerder zich aanmeldt bij Azure AD met hun referenties. Tijdens het aanmelden verwerft de verificatieagent een toegangstoken dat het kan gebruiken namens de globale beheerder.
2. De verificatieagent genereert vervolgens een sleutelpaar: een openbare sleutel en een privésleutel.
    - Het sleutelpaar wordt gegenereerd door middel van standaard RSA 2048-bit encryptie.
    - De privésleutel blijft op de on-premises server waar de verificatieagent zich bevindt.
3. De verificatieagent voert een 'registratieverzoek' in bij Azure AD via HTTPS, met de volgende onderdelen in het verzoek:
    - Het toegangstoken dat in stap 1 is aangeschaft.
    - De openbare sleutel gegenereerd in stap 2.
    - Een certificaatondertekeningsaanvraag (MVO- of certificaataanvraag). Deze aanvraag geldt voor een digitaal identiteitscertificaat, met Azure AD als certificaatautoriteit (CA).
4. Azure AD valideert het toegangstoken in de registratieaanvraag en controleert of de aanvraag afkomstig is van een globale beheerder.
5. Azure AD ondertekent en stuurt vervolgens een digitaal identiteitscertificaat terug naar de verificatieagent.
    - De hoofd-CA in Azure AD wordt gebruikt om het certificaat te ondertekenen. 

      > [!NOTE]
      > Deze CA bevindt zich _niet_ in het Windows Trusted Root Certificate Authorities store.
    - De CA wordt alleen gebruikt door de functie Pass-through Authentication. De CA wordt alleen gebruikt om CDR's te ondertekenen tijdens de registratie van de verificatieagent.
    -  Geen van de andere Azure AD-services maakt gebruik van deze CA.
    - Het onderwerp van het certificaat (Distinguished Name of DN) is ingesteld op uw tenant-id. Deze DN is een GUID die uw tenant op unieke wijze identificeert. Deze DN biedt het certificaat alleen geschikt voor gebruik bij uw tenant.
6. Azure AD slaat de openbare sleutel van de verificatieagent op in een Azure SQL-database, waartoe alleen Azure AD toegang heeft.
7. Het certificaat (uitgegeven in stap 5) wordt opgeslagen op de on-premises server in het Windows-certificaatarchief (met name op de [CERT_SYSTEM_STORE_LOCAL_MACHINE](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_LOCAL_MACHINE) locatie). Het wordt gebruikt door zowel de Verificatieagent als de Updater-toepassingen.

### <a name="authentication-agent-initialization"></a>Initialisatie van verificatieagent

Wanneer de verificatieagent wordt gestart, voor het eerst na registratie of na een herstart van de server, moet de verificatieagent veilig communiceren met de Azure AD-service en wachtwoordvalidatieverzoeken accepteren.

![Agent initialisatie](./media/how-to-connect-pta-security-deep-dive/pta2.png)

Hier is hoe verificatieagents worden geïnitialiseerd:

1. De verificatieagent doet een outbound bootstrap-verzoek aan Azure AD. 
    - Deze aanvraag wordt gedaan via poort 443 en bevindt zich via een wederzijds geverifieerd HTTPS-kanaal. De aanvraag maakt gebruik van hetzelfde certificaat dat is uitgegeven tijdens de registratie van de verificatieagent.
2. Azure AD reageert op de aanvraag door een toegangssleutel te bieden voor een Azure Service Bus-wachtrij die uniek is voor uw tenant en die wordt geïdentificeerd door uw tenant-id.
3. De verificatieagent maakt een permanente uitgaande HTTPS-verbinding (via poort 443) naar de wachtrij. 
    - De verificatieagent is nu klaar om aanvragen voor wachtwoordvalidatie op te halen en af te handelen.

Als u meerdere verificatieagents op uw tenant hebt geregistreerd, zorgt de initialisatieprocedure ervoor dat elke instantie verbinding maakt met dezelfde servicebuswachtrij. 

## <a name="process-sign-in-requests"></a>Aanmeldingsaanvragen verwerken 

In het volgende diagram ziet u hoe pass-through-verificatie aanmeldingsverzoeken van gebruikers verwerkt.

![Aanmelding proces](./media/how-to-connect-pta-security-deep-dive/pta3.png)

Pass-through Authentication behandelt een aanmeldingsverzoek van een gebruiker als volgt: 

1. Een gebruiker probeert toegang te krijgen tot een toepassing, bijvoorbeeld [Outlook Web App](https://outlook.office365.com/owa).
2. Als de gebruiker nog niet is aangemeld, leidt de toepassing de browser door naar de aanmeldingspagina van Azure AD.
3. De Azure AD STS-service reageert terug met de **aanmeldingspagina gebruiker.**
4. De gebruiker voert zijn gebruikersnaam in op de **aanmeldingspagina van** de gebruiker en selecteert vervolgens de knop **Volgende.**
5. De gebruiker voert zijn wachtwoord in op de **aanmeldingspagina van** de gebruiker en selecteert vervolgens de **aanmeldingsknop.**
6. De gebruikersnaam en het wachtwoord worden in een HTTPS POST-verzoek ingediend bij Azure AD STS.
7. Azure AD STS haalt openbare sleutels op voor alle verificatieagents die op uw tenant zijn geregistreerd uit de Azure SQL-database en versleutelt het wachtwoord met behulp ervan.
    - Het produceert "N" versleutelde wachtwoordwaarden voor "N" Authentication Agents geregistreerd op uw tenant.
8. Azure AD STS plaatst de wachtwoordvalidatieaanvraag, die bestaat uit de gebruikersnaam en de versleutelde wachtwoordwaarden, in de servicebuswachtrij die specifiek is voor uw tenant.
9. Omdat de geïnitialiseerde verificatieagents voortdurend zijn verbonden met de wachtrij voor servicebussen, haalt een van de beschikbare verificatieagents het wachtwoordvalidatieverzoek op.
10. De verificatieagent zoekt de versleutelde wachtwoordwaarde die specifiek is voor de openbare sleutel, met behulp van een id en decodeert deze met behulp van de privésleutel.
11. De verificatieagent probeert de gebruikersnaam en het wachtwoord te valideren ten opzichte van on-premises Active Directory met behulp van de [Win32 LogonUser API](https://msdn.microsoft.com/library/windows/desktop/aa378184.aspx) met de parameter **dwLogonType** ingesteld op **LOGON32_LOGON_NETWORK**. 
    - Deze API is dezelfde API die wordt gebruikt door Active Directory Federation Services (AD FS) om gebruikers aan te melden in een federatief aanmeldingsscenario.
    - Deze API is gebaseerd op het standaardoplossingsproces in Windows Server om de domeincontroller te vinden.
12. De verificatieagent ontvangt het resultaat van Active Directory, zoals succes, gebruikersnaam of wachtwoord onjuist, of wachtwoord is verlopen.

   > [!NOTE]
   > Als de verificatieagent mislukt tijdens het aanmeldingsproces, wordt de hele aanmeldingsaanvraag verwijderd. Er is geen overdracht van aanmeldingsverzoeken van de ene verificatieagent naar een andere verificatieagent on-premises. Deze agenten communiceren alleen met de cloud, en niet met elkaar.
   
13. De verificatieagent stuurt het resultaat terug naar Azure AD STS via een uitgaand wederzijds geverifieerd HTTPS-kanaal via poort 443. Wederzijdse verificatie maakt gebruik van het certificaat dat eerder aan de verificatieagent is uitgegeven tijdens de registratie.
14. Azure AD STS controleert of dit resultaat correleert met de specifieke aanmeldingsaanvraag op uw tenant.
15. Azure AD STS gaat verder met de aanmeldingsprocedure zoals geconfigureerd. Als de wachtwoordvalidatie bijvoorbeeld is geslaagd, kan de gebruiker worden uitgedaagd voor multifactorverificatie of worden doorgestuurd naar de toepassing.

## <a name="operational-security-of-the-authentication-agents"></a>Operationele beveiliging van de verificatieagents

Om ervoor te zorgen dat pass-through-verificatie operationeel beveiligd blijft, vernieuwt Azure AD periodiek de certificaten van verificatieagents. Azure AD activeert de verlengingen. De verlengingen worden niet geregeld door de verificatieagents zelf.

![Operationele beveiliging](./media/how-to-connect-pta-security-deep-dive/pta4.png)

Ga als een te meer meting over de vertrouwensrelatie van een verificatieagent met Azure AD:

1. De verificatieagent pingt om de paar uur regelmatig Azure AD om te controleren of het tijd is om het certificaat te vernieuwen. Het certificaat wordt 30 dagen voor het verstrijken ervan vernieuwd.
    - Deze controle wordt uitgevoerd via een wederzijds geverifieerd HTTPS-kanaal en maakt gebruik van hetzelfde certificaat dat is uitgegeven tijdens de registratie.
2. Als de service aangeeft dat het tijd is om te verlengen, genereert de verificatieagent een nieuw sleutelpaar: een openbare sleutel en een privésleutel.
    - Deze sleutels worden gegenereerd door middel van standaard RSA 2048-bit encryptie.
    - De privésleutel verlaat nooit de on-premises server.
3. De verificatieagent voert vervolgens een aanvraag voor 'certificaatverlenging' in naar Azure AD via HTTPS, met de volgende onderdelen in de aanvraag:
    - Het bestaande certificaat dat wordt opgehaald uit de CERT_SYSTEM_STORE_LOCAL_MACHINE locatie in het Windows-certificaatarchief. Er is geen globale beheerder betrokken bij deze procedure, dus er is geen toegangstoken nodig namens de globale beheerder.
    - De openbare sleutel gegenereerd in stap 2.
    - Een certificaatondertekeningsaanvraag (MVO- of certificaataanvraag). Deze aanvraag geldt voor een nieuw digitaal identiteitscertificaat, met Azure AD als certificaatautoriteit.
4. Azure AD valideert het bestaande certificaat in de certificaatverlengingsaanvraag. Vervolgens wordt gecontroleerd of het verzoek afkomstig is van een verificatieagent die is geregistreerd op uw tenant.
5. Als het bestaande certificaat nog steeds geldig is, tekent Azure AD een nieuw digitaal identiteitscertificaat en geeft het nieuwe certificaat het nieuwe certificaat terug aan de verificatieagent. 
6. Als het bestaande certificaat is verlopen, verwijdert Azure AD de verificatieagent uit de lijst met geregistreerde verificatieagents van uw tenant. Vervolgens moet een globale beheerder handmatig een nieuwe verificatieagent installeren en registreren.
    - Gebruik de Azure AD root CA om het certificaat te ondertekenen.
    - Stel het onderwerp van het certificaat (Distinguished Name of DN) in op uw tenant-id, een GUID die uw tenant op unieke wijze identificeert. De DN scopes het certificaat alleen aan uw tenant.
6. Azure AD slaat de nieuwe openbare sleutel van de verificatieagent op in een Azure SQL-database waartoe alleen toegang is. Het maakt ook de oude openbare sleutel ongeldig die is gekoppeld aan de verificatieagent.
7. Het nieuwe certificaat (uitgegeven in stap 5) wordt vervolgens opgeslagen op de server in het Windows-certificaatarchief (met name op de [CERT_SYSTEM_STORE_CURRENT_USER](https://msdn.microsoft.com/library/windows/desktop/aa388136.aspx#CERT_SYSTEM_STORE_CURRENT_USER) locatie).
    - Omdat de verlengingsprocedure voor vertrouwensrelaties niet-interactief plaatsvindt (zonder de aanwezigheid van de globale beheerder), heeft de verificatieagent geen toegang meer om het bestaande certificaat op de CERT_SYSTEM_STORE_LOCAL_MACHINE-locatie bij te werken. 
    
   > [!NOTE]
   > Met deze procedure wordt het certificaat zelf niet verwijderd van de locatie CERT_SYSTEM_STORE_LOCAL_MACHINE.
8. Het nieuwe certificaat wordt vanaf dit moment gebruikt voor verificatie. Elke volgende verlenging van het certificaat vervangt het certificaat op de CERT_SYSTEM_STORE_LOCAL_MACHINE locatie.

## <a name="auto-update-of-the-authentication-agents"></a>Automatische update van de verificatieagents

De Updater-toepassing werkt de verificatieagent automatisch bij wanneer een nieuwe versie (met bugfixes of prestatieverbeteringen) wordt uitgebracht. De updater-toepassing verwerkt geen wachtwoordvalidatieaanvragen voor uw tenant.

Azure AD host de nieuwe versie van de software als een ondertekend **Windows Installer-pakket (MSI).** De MSI is ondertekend met behulp van [Microsoft Authenticode](https://msdn.microsoft.com/library/ms537359.aspx) met SHA256 als het digest-algoritme. 

![Automatisch bijwerken](./media/how-to-connect-pta-security-deep-dive/pta5.png)

Een verificatieagent automatisch bijwerken:

1. De updater-toepassing pingt elk uur Azure AD om te controleren of er een nieuwe versie van de verificatieagent beschikbaar is. 
    - Deze controle wordt uitgevoerd via een wederzijds geverifieerd HTTPS-kanaal met behulp van hetzelfde certificaat dat is uitgegeven tijdens de registratie. De verificatieagent en de updater delen het certificaat dat op de server is opgeslagen.
2. Als er een nieuwe versie beschikbaar is, retourneert Azure AD de ondertekende MSI terug naar de Updater.
3. De Updater controleert of de MSI is ondertekend door Microsoft.
4. De Updater draait de MSI. Deze actie omvat de volgende stappen:

   > [!NOTE]
   > De Updater wordt uitgevoerd met [lokale systeembevoegdheden.](https://msdn.microsoft.com/library/windows/desktop/ms684190.aspx)

    - Hiermee stopt u de verificatieagentservice
    - Installeert de nieuwe versie van de verificatieagent op de server
    - De verificatieagentservice opnieuw starten

>[!NOTE]
>Als u meerdere verificatieagents op uw tenant hebt geregistreerd, verlengt Azure AD hun certificaten niet tegelijkertijd of werkt deze niet tegelijkertijd bij. In plaats daarvan doet Azure AD dit één voor één om de hoge beschikbaarheid van aanmeldingsaanvragen te garanderen.
>


## <a name="next-steps"></a>Volgende stappen
- [Huidige beperkingen](how-to-connect-pta-current-limitations.md): Ontdek welke scenario's worden ondersteund en welke niet.
- [Snelstart:](how-to-connect-pta-quick-start.md)aan de slag met Azure AD Pass-through Authentication.
- [Migreren van AD FS naar Pass-through Authentication](https://aka.ms/adfstoptadpdownload) - Een gedetailleerde handleiding om te migreren van AD FS (of andere federatietechnologieën) naar Pass-through Authentication.
- [Smart Lockout:](../authentication/howto-password-smart-lockout.md)Configureer de Smart Lockout-mogelijkheid op uw tenant om gebruikersaccounts te beschermen.
- [Hoe het werkt:](how-to-connect-pta-how-it-works.md)leer de basisprincipes van hoe Azure AD Pass-through Authentication werkt.
- [Veelgestelde vragen](how-to-connect-pta-faq.md): Vind antwoorden op veelgestelde vragen.
- [Problemen oplossen:](tshoot-connect-pass-through-authentication.md)meer informatie over het oplossen van veelvoorkomende problemen met de functie Doorgeefverificatie.
- [Azure AD Seamless SSO:](how-to-connect-sso.md)Meer informatie over deze aanvullende functie.
