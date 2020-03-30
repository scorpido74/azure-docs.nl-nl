---
title: 'Azure AD Connect: aanmelding voor gebruikers | Microsoft Documenten'
description: Aanmelden voor Azure AD Connect-gebruikers voor aangepaste instellingen.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 547b118e-7282-4c7f-be87-c035561001df
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/31/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: a08120b98c7a08bca50453df59df313b1645c5c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331272"
---
# <a name="azure-ad-connect-user-sign-in-options"></a>Aanmeldingsopties voor Azure AD Connect-gebruikers
Azure Active Directory (Azure AD) Connect stelt uw gebruikers in staat zich aan te melden bij zowel cloud- als on-premises bronnen met dezelfde wachtwoorden. In dit artikel worden de belangrijkste concepten voor elk identiteitsmodel beschreven om u te helpen de identiteit te kiezen die u wilt gebruiken voor het aanmelden bij Azure AD.

Als u al bekend bent met het Azure AD-identiteitsmodel en meer wilt weten over een specifieke methode, raadpleegt u de juiste koppeling:

* [Synchronisatie van wachtwoordhash](#password-hash-synchronization) met [Naadloze Single Sign-on (SSO)](how-to-connect-sso.md)
* [Doorreisverificatie](how-to-connect-pta.md) met [Naadloze Single Sign-on (SSO)](how-to-connect-sso.md)
* [Federatieve SSO (met Active Directory Federation Services (AD FS))](#federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2)
* [Federatie met PingFederate](#federation-with-pingfederate)

> [!NOTE] 
> Het is belangrijk om te onthouden dat u door federatie voor Azure AD te configureren, vertrouwen opstelt tussen uw Azure AD-tenant en uw federatieve domeinen. Met deze vertrouwensfederatie hebben domeingebruikers toegang tot Azure AD-cloudbronnen binnen de tenant.  
>

## <a name="choosing-the-user-sign-in-method-for-your-organization"></a>De aanmeldingsmethode voor gebruikers voor uw organisatie kiezen
De eerste beslissing van de implementatie van Azure AD Connect is het kiezen van welke verificatiemethode uw gebruikers zullen gebruiken om zich aan te melden. Het is belangrijk om ervoor te zorgen dat u de juiste methode kiest die voldoet aan de beveiliging en geavanceerde vereisten van uw organisatie. Verificatie is essentieel, omdat het de identiteit van gebruikers zal valideren om toegang te krijgen tot apps en gegevens in de cloud. Als u de juiste verificatiemethode wilt kiezen, moet u rekening houden met de tijd, bestaande infrastructuur, complexiteit en kosten van het implementeren van uw keuze. Deze factoren zijn verschillend voor elke organisatie en kunnen in de loop van de tijd veranderen.

Azure AD ondersteunt de volgende verificatiemethoden: 

* **Cloudverificatie** : wanneer u deze verificatiemethode kiest, verwerkt Azure AD het verificatieproces voor de aanmelding van de gebruiker. Met cloudverificatie u kiezen uit twee opties: 
   * **Password hash synchronization (PHS)** - Password Hash Sync stelt gebruikers in staat om dezelfde gebruikersnaam en wachtwoord te gebruiken die ze on-premises gebruiken zonder extra infrastructuur te hoeven implementeren naast Azure AD Connect. 
   * **Pass-through authentication (PTA)** - Deze optie is vergelijkbaar met wachtwoordhashsynchronisatie, maar biedt een eenvoudige wachtwoordvalidatie met behulp van on-premises softwareagents voor organisaties met een sterk beveiligings- en nalevingsbeleid.
* **Federatieve verificatie** - Wanneer u deze verificatiemethode kiest, geeft Azure AD het verificatieproces af aan een afzonderlijk vertrouwd verificatiesysteem, zoals AD FS of een federatiesysteem van derden, om de aanmelding van de gebruiker te valideren. 

Voor de meeste organisaties die zich alleen aanmelden voor gebruikers bij Office 365, SaaS-toepassingen en andere Azure AD-bronnen, raden we de standaard optie voor synchronisatie van wachtwoordhash aan.
 
Zie [De juiste verificatiemethode kiezen voor uw hybride identiteitsoplossing](../../security/fundamentals/choose-ad-authn.md) voor Azure Active Directory voor gedetailleerde informatie over het kiezen van een verificatiemethode.

### <a name="password-hash-synchronization"></a>Synchronisatie van wachtwoord-hashes
Met wachtwoordhashsynchronisatie worden hashes van gebruikerswachtwoorden gesynchroniseerd van on-premises Active Directory naar Azure AD. Wanneer wachtwoorden worden gewijzigd of on-premises worden gereset, worden de nieuwe wachtwoordhashes onmiddellijk gesynchroniseerd met Azure AD, zodat uw gebruikers altijd hetzelfde wachtwoord kunnen gebruiken voor cloudbronnen en on-premises bronnen. De wachtwoorden worden nooit in duidelijke tekst naar Azure AD verzonden of in Azure AD opgeslagen. U wachtwoordhashsynchronisatie gebruiken in combinatie met het terugschrijven van wachtwoorden om het opnieuw instellen van selfservicewachtwoorden in Azure AD in te schakelen.

Daarnaast u [Seamless SSO](how-to-connect-sso.md) inschakelen voor gebruikers op met domeinen verbonden machines die zich op het bedrijfsnetwerk bevinden. Met één aanmelding hoeven ingeschakelde gebruikers alleen een gebruikersnaam in te voeren om hen te helpen veilig toegang te krijgen tot cloudbronnen.

![Synchronisatie van wachtwoord-hashes](./media/plan-connect-user-signin/passwordhash.png)

Zie het artikel [over wachtwoordhashsynchronisatie](how-to-connect-password-hash-synchronization.md) voor meer informatie.

### <a name="pass-through-authentication"></a>Pass-through-verificatie
Met pass-through-verificatie wordt het wachtwoord van de gebruiker gevalideerd ten opzichte van de on-premises Active Directory-controller. Het wachtwoord hoeft in geen enkele vorm aanwezig te zijn in Azure AD. Hierdoor kan on-premises beleidsregels, zoals aanmeldingsuurbeperkingen, worden geëvalueerd tijdens verificatie naar cloudservices.

Pass-through-verificatie maakt gebruik van een eenvoudige agent op een Windows Server 2012 R2-apparaat dat is verbonden met een domein in de on-premises omgeving. Deze agent luistert naar wachtwoordvalidatieverzoeken. Er zijn geen binnenkomende poorten nodig die open staan voor het internet.

Daarnaast u ook eenmalige aanmelding inschakelen voor gebruikers op met een domein verbonden machines die zich in het bedrijfsnetwerk bevinden. Met één aanmelding hoeven ingeschakelde gebruikers alleen een gebruikersnaam in te voeren om hen te helpen veilig toegang te krijgen tot cloudbronnen.
![Pass-through-verificatie](./media/plan-connect-user-signin/pta.png)

Zie voor meer informatie:
- [Doorgeefverificatie](how-to-connect-pta.md)
- [Eenmalige aanmelding](how-to-connect-sso.md)

### <a name="federation-that-uses-a-new-or-existing-farm-with-ad-fs-in-windows-server-2012-r2"></a>Federatie die een nieuwe of bestaande farm met AD FS gebruikt in Windows Server 2012 R2
Met federatieve aanmelding kunnen uw gebruikers zich aanmelden bij Azure AD-services met hun on-premises wachtwoorden. Terwijl ze zich in het bedrijfsnetwerk bevinden, hoeven ze niet eens hun wachtwoorden in te voeren. Door de federatieoptie met AD FS te gebruiken, u een nieuwe of bestaande farm met AD FS implementeren in Windows Server 2012 R2. Als u ervoor kiest een bestaande farm op te geven, configureert Azure AD Connect de vertrouwensrelatie tussen uw farm en Azure AD, zodat uw gebruikers zich kunnen aanmelden.

<center>

![Federatie met AD FS in Windows Server 2012 R2](./media/plan-connect-user-signin/federatedsignin.png)</center>

#### <a name="deploy-federation-with-ad-fs-in-windows-server-2012-r2"></a>Federatie implementeren met AD FS in Windows Server 2012 R2

Als u een nieuwe farm implementeert, hebt u het volgende nodig:

* Een Windows Server 2012 R2-server voor de federatieserver.
* Een Windows Server 2012 R2-server voor de proxy van de webtoepassing.
* Een .pfx-bestand met één TLS/SSL-certificaat voor de beoogde federatieservicenaam. Bijvoorbeeld: fs.contoso.com.

Als u een nieuwe farm implementeert of een bestaande farm gebruikt, moet u het volgende doen:

* Lokale beheerdersreferenties op uw federatieservers.
* Referenties van lokale beheerders op alle werkgroepservers (niet met het domein verbonden) waarop u de functie Proxy voor webtoepassingen wilt implementeren.
* De machine waarop u de wizard uitvoert om verbinding te kunnen maken met andere machines waarop u AD FS of WebApplication Proxy wilt installeren met Windows Remote Management.

Zie [SSO configureren met AD FS](how-to-connect-install-custom.md#configuring-federation-with-ad-fs)voor meer informatie.

### <a name="federation-with-pingfederate"></a>Federatie met PingFederate
Met federatieve aanmelding kunnen uw gebruikers zich aanmelden bij Azure AD-services met hun on-premises wachtwoorden. Terwijl ze zich in het bedrijfsnetwerk bevinden, hoeven ze niet eens hun wachtwoorden in te voeren.

Zie [PingFederate-integratie met Azure Active Directory en Office 365](https://www.pingidentity.com/AzureADConnect) voor meer informatie over het configureren van PingFederate voor gebruik met Azure Active Directory

Zie Aangepaste installatie van Azure [AD Connect](how-to-connect-install-custom.md#configuring-federation-with-pingfederate) voor informatie over het instellen van Azure AD Connect met PingFederate

#### <a name="sign-in-by-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Meld u aan met een eerdere versie van AD FS of een oplossing van derden
Als u zich al hebt aangemeld bij de cloud met een eerdere versie van AD FS (zoals AD FS 2.0) of een externe federatieprovider, u ervoor kiezen om de aanmeldingsconfiguratie van gebruikers over te slaan via Azure AD Connect. Hiermee u de nieuwste synchronisatie en andere mogelijkheden van Azure AD Connect krijgen terwijl u uw bestaande oplossing nog steeds gebruikt voor aanmelding.

Zie de [compatibiliteitslijst van Azure AD-federaties van derden](how-to-connect-fed-compatibility.md)voor meer informatie .


## <a name="user-sign-in-and-user-principal-name"></a>Aanmeldings- en gebruikersnaam
### <a name="understanding-user-principal-name"></a>Gebruikersnaam begrijpen
In Active Directory is het upn-achtervoegsel (default user principal name) de DNS-naam van het domein waar het gebruikersaccount is gemaakt. In de meeste gevallen is dit de domeinnaam die is geregistreerd als het bedrijfsdomein op internet. U echter meer UPN-achtervoegsels toevoegen met Active Directory-domeinen en vertrouwensrelaties.

De UPN van de username@domaingebruiker heeft het formaat . Voor een Active Directory-domein met de naam 'contoso.com' kanjohn@contoso.comeen gebruiker met de naam John bijvoorbeeld de UPN " "hebben. De UPN van de gebruiker is gebaseerd op RFC 822. Hoewel de UPN en e-mail dezelfde indeling delen, is de waarde van de UPN voor een gebruiker mogelijk wel of niet hetzelfde als het e-mailadres van de gebruiker.

### <a name="user-principal-name-in-azure-ad"></a>Naam van de gebruiker in Azure AD
De wizard Azure AD Connect gebruikt het kenmerk userPrincipalName of laat u het kenmerk (in een aangepaste installatie) opgeven dat vanaf on-premises als de hoofdnaam van de gebruiker in Azure AD moet worden gebruikt. Dit is de waarde die wordt gebruikt voor het aanmelden bij Azure AD. Als de waarde van het kenmerk userPrincipalName niet overeenkomt met een geverifieerd domein in Azure AD, vervangt Azure AD deze door een standaard waarde van onmicrosoft.com.

Elke map in Azure Active Directory wordt geleverd met een ingebouwde domeinnaam, met de indeling contoso.onmicrosoft.com, waarmee u aan de slag gaan met Azure of andere Microsoft-services. U de aanmeldingservaring verbeteren en vereenvoudigen door aangepaste domeinen te gebruiken. Zie [Uw aangepaste domeinnaam toevoegen aan Azure Active Directory voor](../fundamentals/add-custom-domain.md)informatie over aangepaste domeinnamen in Azure AD en het verifiëren van een domein.

## <a name="azure-ad-sign-in-configuration"></a>Aanmeldconfiguratie Azure AD
### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD-aanmeldingsconfiguratie met Azure AD Connect
De aanmeldingservaring van Azure AD is afhankelijk van de vraag of Azure AD het achtervoegsel van de gebruikersnaam kan overeenkomen met een gebruiker die wordt gesynchroniseerd met een van de aangepaste domeinen die zijn geverifieerd in de Azure AD-map. Azure AD Connect biedt hulp bij het configureren van azure AD-aanmeldingsinstellingen, zodat de aanmeldingservaring van de gebruiker in de cloud vergelijkbaar is met de on-premises ervaring.

Azure AD Connect bevat de UPN-achtervoegsels die zijn gedefinieerd voor de domeinen en probeert deze te koppelen aan een aangepast domein in Azure AD. Dan helpt het u met de juiste actie die moet worden genomen.
De aanmeldingspagina van Azure AD bevat de UPN-achtervoegsels die zijn gedefinieerd voor on-premises Active Directory en geeft de bijbehorende status weer voor elk achtervoegsel. De statuswaarden kunnen een van de volgende waarden zijn:

| Status | Beschrijving | Actie nodig |
|:--- |:--- |:--- |
| Geverifieerd |Azure AD Connect heeft een overeenkomend geverifieerd domein gevonden in Azure AD. Alle gebruikers voor dit domein kunnen zich aanmelden met behulp van hun on-premises referenties. |Er is geen actie nodig. |
| Niet geverifieerd |Azure AD Connect heeft een overeenkomend aangepast domein gevonden in Azure AD, maar dit is niet geverifieerd. Het UPN-achtervoegsel van de gebruikers van dit domein wordt gewijzigd in het standaard achtervoegsel van onmicrosoft.com na synchronisatie als het domein niet is geverifieerd. | [Controleer het aangepaste domein in Azure AD.](../fundamentals/add-custom-domain.md#verify-your-custom-domain-name) |
| Niet toegevoegd |Azure AD Connect heeft geen aangepast domein gevonden dat overeenkwam met het UPN-achtervoegsel. Het UPN-achtervoegsel van de gebruikers van dit domein wordt gewijzigd in het standaard achtervoegsel .onmicrosoft.com als het domein niet is toegevoegd en geverifieerd in Azure. | [Voeg een aangepast domein toe en verifieer deze dat overeenkomt met het UPN-achtervoegsel.](../fundamentals/add-custom-domain.md) |

De aanmeldingspagina van Azure AD bevat de UPN-achtervoegsels die zijn gedefinieerd voor on-premises Active Directory en het bijbehorende aangepaste domein in Azure AD met de huidige verificatiestatus. In een aangepaste installatie u nu het kenmerk voor de hoofdnaam van de gebruiker selecteren op de **aanmeldingspagina van Azure AD.**

![Aanmeldingspagina azure AD](./media/plan-connect-user-signin/custom_azure_sign_in.png)

U op de vernieuwingsknop klikken om de laatste status van de aangepaste domeinen opnieuw op te halen uit Azure AD.

### <a name="selecting-the-attribute-for-the-user-principal-name-in-azure-ad"></a>Het kenmerk voor de hoofdnaam van de gebruiker selecteren in Azure AD
Het kenmerk userPrincipalName is het kenmerk dat gebruikers gebruiken wanneer ze zich aanmelden bij Azure AD en Office 365. U moet de domeinen (ook wel UPN-achtervoegsels genoemd) verifiëren die in Azure AD worden gebruikt voordat de gebruikers worden gesynchroniseerd.

We raden u ten zeerste aan het standaardkenmerk userPrincipalName te behouden. Als dit kenmerk niet-routable is en niet kan worden geverifieerd, is het mogelijk om een ander kenmerk (bijvoorbeeld e-mail) te selecteren als het kenmerk dat de aanmeldings-id bevat. Dit staat bekend als de Alternatieve ID. De kenmerkwaarde Alternatieve ID moet de RFC 822-standaard volgen. U een alternatieve id met zowel wachtwoord SSO als federatie SSO gebruiken als aanmeldingsoplossing.

> [!NOTE]
> Het gebruik van een alternatieve id is niet compatibel met alle Office 365-workloads. Zie [Configuring Alternate Login ID](https://technet.microsoft.com/library/dn659436.aspx) (Engelstalig) voor meer informatie.
>
>

#### <a name="different-custom-domain-states-and-their-effect-on-the-azure-sign-in-experience"></a>Verschillende aangepaste domeinstatussen en het effect ervan op de Azure-aanmeldingservaring
Het is erg belangrijk om de relatie tussen de aangepaste domeinstatussen in uw Azure AD-map en de UPN-achtervoegsels die on-premises zijn gedefinieerd, te begrijpen. Laten we de verschillende mogelijke Azure-aanmeldingservaringen doorlopen wanneer u synchronisatie instelt met Azure AD Connect.

Voor de volgende informatie, laten we aannemen dat we bezorgd zijn over de UPN achtervoegsel contoso.com, die wordt user@contoso.comgebruikt in de on-premises directory als onderdeel van UPN - bijvoorbeeld .

###### <a name="express-settingspassword-hash-synchronization"></a>Expresinstellingen/Hash-synchronisatie van wachtwoorden

| Status | Effect op aanmeldingservaring van gebruikers Azure |
|:---:|:--- |
| Niet toegevoegd |In dit geval is er geen aangepast domein voor contoso.com toegevoegd in de Azure AD-map. Gebruikers die UPN on-premises met @contoso.com het achtervoegsel hebben, kunnen hun on-premises UPN niet gebruiken om zich aan te melden bij Azure. In plaats daarvan moeten ze een nieuwe UPN gebruiken die door Azure AD aan hen wordt geleverd door het achtervoegsel toe te voegen voor de standaard AD-map azure. Als u bijvoorbeeld gebruikers synchroniseert met de Azure AD-map azurecontoso.onmicrosoft.com, user@contoso.com krijgt de on-premises gebruiker een UPN van user@azurecontoso.onmicrosoft.com. |
| Niet geverifieerd |In dit geval hebben we een aangepast domein contoso.com dat is toegevoegd in de Azure AD-map. Het is echter nog niet geverifieerd. Als u doorgaat met het synchroniseren van gebruikers zonder het domein te verifiëren, krijgen de gebruikers een nieuwe UPN toegewezen van Azure AD, net als in het scenario 'Niet toegevoegd'. |
| Geverifieerd |In dit geval hebben we een aangepast domein contoso.com dat al is toegevoegd en geverifieerd in Azure AD voor het UPN-achtervoegsel. Gebruikers kunnen bijvoorbeeld hun on-premises gebruikersnaam user@contoso.comgebruiken om zich aan te melden bij Azure nadat ze zijn gesynchroniseerd met Azure AD. |

###### <a name="ad-fs-federation"></a>AD FS-federatie
U geen federatie maken met het standaard .onmicrosoft.com-domein in Azure AD of een niet-geverifieerd aangepast domein in Azure AD. Wanneer u de wizard Azure AD Connect uitvoert, als u een niet-geverifieerd domein selecteert waarmee u een federatie wilt maken, wordt u in Azure AD Connect gevraagd de benodigde records te maken waar uw DNS voor het domein wordt gehost. Zie [Het Azure AD-domein verifiëren dat is geselecteerd voor federatie voor](how-to-connect-install-custom.md#verify-the-azure-ad-domain-selected-for-federation)meer informatie.

Als u de gebruikersaanmeldingsoptie **Federation met AD FS**hebt geselecteerd, moet u een aangepast domein hebben om een federatie in Azure AD te blijven maken. Voor onze discussie betekent dit dat we een aangepast domein moeten hebben contoso.com toegevoegd in de Azure AD-map.

| Status | Effect op de aanmeldingservaring van azure voor gebruikers |
|:---:|:--- |
| Niet toegevoegd |In dit geval heeft Azure AD Connect geen overeenkomend aangepast domein gevonden voor het UPN-achtervoegsel contoso.com in de Azure AD-map. U moet een aangepast domein toevoegen contoso.com als u gebruikers nodig hebt om zich aan user@contoso.comte melden met behulp van AD FS met hun on-premises UPN (zoals ). |
| Niet geverifieerd |In dit geval vraagt Azure AD Connect u de juiste informatie over hoe u uw domein in een later stadium verifiëren. |
| Geverifieerd |In dit geval u doorgaan met de configuratie zonder verdere actie. |

## <a name="changing-the-user-sign-in-method"></a>De aanmeldingsmethode voor gebruikers wijzigen
U de aanmeldingsmethode van de gebruiker wijzigen van federatie, wachtwoordhashsynchronisatie of doorberekeningsverificatie met behulp van de taken die beschikbaar zijn in Azure AD Connect na de eerste configuratie van Azure AD Connect met de wizard. Voer de wizard Azure AD Connect opnieuw uit en u ziet een lijst met taken die u uitvoeren. Selecteer **Aanmelding** voor gebruikers wijzigen in de lijst met taken.

![Aanmelding van gebruikers wijzigen](./media/plan-connect-user-signin/changeusersignin.png)

Op de volgende pagina wordt u gevraagd de referenties voor Azure AD op te geven.

![Verbinding maken met Azure AD](./media/plan-connect-user-signin/changeusersignin2.png)

Selecteer op de **aanmeldingspagina gebruiker** de gewenste aanmelding van de gebruiker.

![Verbinding maken met Azure AD](./media/plan-connect-user-signin/changeusersignin2a.png)

> [!NOTE]
> Als u slechts tijdelijk overstapt op wachtwoordhashsynchronisatie, schakelt u het selectievakje **Gebruikersaccounts niet converteren** in. Het niet controleren van de optie zal elke gebruiker converteren naar federated, en het kan enkele uren duren.
>
>

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [het integreren van uw on-premises identiteiten met Azure Active Directory](whatis-hybrid-identity.md).
- Meer informatie over [Azure AD Connect-ontwerpconcepten](plan-connect-design-concepts.md).
