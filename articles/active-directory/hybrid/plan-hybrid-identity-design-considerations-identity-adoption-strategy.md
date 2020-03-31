---
title: Hybride identiteitsontwerp - adoptiestrategie Azure | Microsoft Documenten
description: Met Conditional Access-beheer controleert Azure Active Directory de specifieke voorwaarden die u kiest bij het verifiëren van de gebruiker en voordat u toegang tot de toepassing toestaat. Zodra aan deze voorwaarden is voldaan, wordt de gebruiker geverifieerd en toegang tot de toepassing toegestaan.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109336"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Een strategie voor hybride identiteitsacceptatie definiëren
In deze taak definieert u de hybride identiteitsacceptatiestrategie voor uw hybride identiteitsoplossing om te voldoen aan de bedrijfsvereisten die zijn besproken in:

* [Bepalen van de zakelijke behoeften](plan-hybrid-identity-design-considerations-business-needs.md)
* [Adreslijstsynchronisatievereisten bepalen](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Vereisten voor meervoudige verificatie bepalen](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definieer de strategie voor bedrijfsbehoeften
De eerste taak is gericht op het bepalen van de behoeften van organisaties die bedrijven.  Dit kan zeer breed zijn en scope creep kan optreden als je niet voorzichtig bent.  In het begin, houd het eenvoudig, maar altijd onthouden om te plannen voor een ontwerp dat geschikt is voor en verandering te vergemakkelijken in de toekomst.  Of het nu gaat om een eenvoudig ontwerp of een uiterst complex ontwerp, Azure Active Directory is het Microsoft Identity-platform dat Office 365, Microsoft Online Services en cloudbewuste toepassingen ondersteunt.

## <a name="define-an-integration-strategy"></a>Een integratiestrategie definiëren
Microsoft heeft drie belangrijke integratiescenario's die cloudidentiteiten, gesynchroniseerde identiteiten en federatieve identiteiten zijn.  U moet van plan zijn een van deze integratiestrategieën aan te nemen.  De strategie die u kiest kan variëren en de beslissingen bij het kiezen van een kan omvatten, wat voor soort gebruikerservaring u wilt bieden, heeft u een bestaande infrastructuur, en wat is de meest kosteneffectieve.  

![integratiescenario's](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

De scenario's die in het bovenstaande figuur zijn gedefinieerd zijn:

* **Cloudidentiteiten**: dit zijn identiteiten die uitsluitend in de cloud bestaan.  In het geval van Azure AD bevinden ze zich specifiek in uw Azure AD-map.
* **Gesynchroniseerd**: dit zijn identiteiten die on-premises en in de cloud bestaan.  Met Azure AD Connect worden deze gebruikers gemaakt of samengevoegd met bestaande Azure AD-accounts.  De wachtwoordhash van de gebruiker wordt gesynchroniseerd van de on-premises omgeving naar de cloud in wat een wachtwoordhash wordt genoemd.  Bij het gebruik van gesynchroniseerd is het enige voorbehoud dat als een gebruiker is uitgeschakeld in de on-premises omgeving, het tot drie uur kan duren voordat die accountstatus wordt weergegeven in Azure AD.  Dit is te wijten aan het synchronisatietijdinterval.
* **Federated**: deze identiteiten bestaan zowel on-premises als in de cloud.  Met Azure AD Connect worden deze gebruikers gemaakt of samengevoegd met bestaande Azure AD-accounts.  

> [!NOTE]
> Lees Uw [on-premises identiteiten integreren met Azure Active Directory](whatis-hybrid-identity.md)voor meer informatie over de synchronisatieopties.
> 
> 

De volgende tabel helpt bij het bepalen van de voor- en nadelen van elk van de volgende strategieën:

| Strategie | Voordelen | Nadelen |
| --- | --- | --- |
| **Cloudidentiteiten** |Eenvoudiger te beheren voor kleine organisaties. <br> Niets om on-premises te installeren. Geen extra hardware nodig<br>Gemakkelijk uitgeschakeld als de gebruiker het bedrijf verlaat |Gebruikers moeten zich aanmelden wanneer ze toegang krijgen tot workloads in de cloud <br> Wachtwoorden kunnen al dan niet hetzelfde zijn voor cloud- en on-premises identiteiten |
| **Gesynchroniseerd** |On-premises wachtwoord verifieert zowel on-premises als cloudmappen <br>Eenvoudiger te beheren voor kleine, middelgrote of grote organisaties <br>Gebruikers kunnen eenmalige aanmelding (SSO) hebben voor sommige bronnen <br> Microsoft-voorkeursmethode voor synchronisatie <br> Eenvoudiger te beheren |Sommige klanten zijn mogelijk terughoudend om hun mappen te synchroniseren met de cloud vanwege de politie van het specifieke bedrijf |
| **Federatief** |Gebruikers kunnen eenmalige aanmelding (SSO) <br>Als een gebruiker wordt beëindigd of vertrekt, kan het account onmiddellijk worden uitgeschakeld en kan de toegang worden ingetrokken,<br> Ondersteunt geavanceerde scenario's die niet kunnen worden uitgevoerd met gesynchroniseerd |Meer stappen voor het instellen en configureren <br> Hoger onderhoud <br> Mogelijk extra hardware nodig voor de STS-infrastructuur <br> Mogelijk is extra hardware nodig om de federatieserver te installeren. Aanvullende software is vereist als AD FS wordt gebruikt <br> Vereisen uitgebreide setup voor SSO <br> Kritiek punt van storing als de federatieserver is uitgeschakeld, kunnen gebruikers zich niet verifiëren |

### <a name="client-experience"></a>Clientervaring
De strategie die u gebruikt, bepaalt de aanmeldingservaring van de gebruiker.  In de volgende tabellen vindt u informatie over wat de gebruikers moeten verwachten van hun aanmeldingservaring.  Niet alle federatieve identiteitsproviders ondersteunen SSO in alle scenario's.

**Doman-joined en private netwerk applicaties:**

|  | Gesynchroniseerde identiteit | Federatieve identiteit |
| --- | --- | --- |
| Webbrowsers |Verificatie op basis van formulieren |eenmalige aanmelding, soms vereist om organisatie-ID te leveren |
| Outlook |Vragen om referenties |Vragen om referenties |
| Skype voor Bedrijven (Lync) |Vragen om referenties |eenmalige aanmelding voor Lync, gevraagde referenties voor Exchange |
| OneDrive voor Bedrijven |Vragen om referenties |eenmalige aanmelding |
| Office Pro Plus-abonnement |Vragen om referenties |eenmalige aanmelding |

**Externe of niet-vertrouwde bronnen:**

|  | Gesynchroniseerde identiteit | Federatieve identiteit |
| --- | --- | --- |
| Webbrowsers |Verificatie op basis van formulieren |Verificatie op basis van formulieren |
| Outlook, Skype voor Bedrijven (Lync), OneDrive voor Bedrijven, Office-abonnement |Vragen om referenties |Vragen om referenties |
| Exchange ActiveSync |Vragen om referenties |eenmalige aanmelding voor Lync, gevraagde referenties voor Exchange |
| Mobiele apps |Vragen om referenties |Vragen om referenties |

Als u uit taak 1 hebt vastgesteld dat u een idp van derden hebt of er een gaat gebruiken om federatie te voorzien van Azure AD, moet u op de hoogte zijn van de volgende ondersteunde mogelijkheden:

* Elke SAML 2.0-provider die compatibel is met het SP-Lite-profiel, kan verificatie naar Azure AD en bijbehorende toepassingen ondersteunen
* Ondersteunt passieve authenticatie, die authenticatie naar OWA, SPO, enz.
* Exchange Online-klanten kunnen worden ondersteund via het SAML 2.0 Enhanced Client Profile (ECP)

U moet zich ook bewust zijn van welke mogelijkheden niet beschikbaar zijn:

* Zonder WS-Trust/Federation-ondersteuning breken alle andere actieve klanten
  * Dat betekent geen Lync-client, OneDrive-client, Office-abonnement, Office Mobile vóór Office 2016
* De overgang van Office naar passieve verificatie stelt hen in staat om pure SAML 2.0 IdPs te ondersteunen, maar ondersteuning is nog steeds client-by-client basis

> [!NOTE]
> Lees voor de meest recente lijst de [compatibiliteitslijst van de Azure AD-federatie](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Synchronisatiestrategie definiëren
In deze taak definieert u de hulpprogramma's die worden gebruikt om de on-premises gegevens van de organisatie te synchroniseren met de cloud en welke topologie u moet gebruiken.  Omdat de meeste organisaties Active Directory gebruiken, wordt informatie over het gebruik van Azure AD Connect om de bovenstaande vragen te beantwoorden, tot in detail weergegeven.  Voor omgevingen die geen Active Directory hebben, is er informatie over het gebruik van FIM 2010 R2 of MIM 2016 om deze strategie te helpen plannen.  Toekomstige versies van Azure AD Connect ondersteunen echter LDAP-mappen, dus afhankelijk van uw tijdlijn kan deze informatie mogelijk worden ondersteund.

### <a name="synchronization-tools"></a>Synchronisatiegereedschappen
In de loop der jaren hebben verschillende synchronisatietools bestaan en gebruikt voor verschillende scenario's.  Momenteel is Azure AD Connect het programma ga naar keuze voor alle ondersteunde scenario's.  AAD Sync en DirSync zijn ook nog steeds rond en kunnen zelfs aanwezig zijn in uw omgeving nu. 

> [!NOTE]
> Lees het [vergelijkingsartikel directory-integratietools](plan-hybrid-identity-design-considerations-tools-comparison.md) voor de meest recente informatie over de ondersteunde mogelijkheden van elk hulpprogramma.  
> 
> 

### <a name="supported-topologies"></a>Ondersteunde topologieën
Bij het definiëren van een synchronisatiestrategie moet de gebruikte topologie worden bepaald. Afhankelijk van de informatie die in stap 2 is bepaald, u bepalen welke topologie de juiste is om te gebruiken. De enkele forest- en azure AD-topologie is de meest voorkomende en bestaat uit één Active Directory-forest en één exemplaar van Azure AD.  Dit zal worden gebruikt in een meerderheid van de scenario's en is de verwachte topologie bij het gebruik van Azure AD Connect Express-installatie, zoals weergegeven in de onderstaande afbeelding.

![Ondersteunde topologieën](./media/plan-hybrid-identity-design-considerations/single-forest.png) Single Forest Scenario Het is gebruikelijk dat grote en zelfs kleine organisaties meerdere forests hebben, zoals te zien is in figuur 5.

> [!NOTE]
> Lees het artikel [Topologieën voor Azure AD Connect voor](plan-connect-topologies.md)meer informatie over de verschillende on-premises en Azure AD-topologieën met Azure AD Connect-synchronisatie.
> 
> 

![multi-forest topologie](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Scenario voor meerdere bossen

Als dit het geval is, moet de multi-forest single Azure AD-topologie worden overwogen als de volgende items waar zijn:

* Gebruikers hebben slechts 1 identiteit in alle forests - de unieke gebruikers sectie hieronder beschrijft dit in meer detail.
* De gebruiker verifieert naar het forest waarin zijn identiteit zich bevindt
* UPN en Bronanker (onveranderlijke id) komen uit dit bos
* Alle forests zijn toegankelijk via Azure AD Connect - dit betekent dat het niet hoeft te worden samengevoegd en kan worden geplaatst in een DMZ als dit dit vergemakkelijkt.
* Gebruikers hebben slechts één postvak
* Het forest dat het postvak van een gebruiker host, heeft de beste gegevenskwaliteit voor kenmerken die zichtbaar zijn in de Exchange Global Address List (GAL)
* Als er geen postvak op de gebruiker, dan is een bos kan worden gebruikt om deze waarden bij te dragen
* Als u een gekoppeld postvak hebt, is er ook een ander account in een ander forest dat wordt gebruikt om u aan te melden.

> [!NOTE]
> Objecten die zowel on-premises als in de cloud bestaan, worden "verbonden" via een unieke id. In het kader van Directory Synchronisatie wordt deze unieke id aangeduid als sourceanchor. In het kader van Single Sign-On wordt dit aangeduid als de ImmutableId. [Ontwerp concepten voor Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) voor meer overwegingen met betrekking tot het gebruik van SourceAnchor.
> 
> 

Als het bovenstaande niet waar is en u meer dan één actief account of meer dan één postvak hebt, kiest Azure AD Connect het ene en negeert het andere.  Als u gekoppelde postvakken hebt, maar geen ander account, worden deze accounts niet geëxporteerd naar Azure AD en is die gebruiker geen lid van groepen.  Dit is anders dan hoe het was in het verleden met DirSync en is opzettelijk om beter te ondersteunen deze multi-forest scenario's. Een scenario met meerdere bossen wordt weergegeven in de onderstaande afbeelding.

![meerdere Azure AD-tenants](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Meerdere Azure AD-scenario's voor meerdere forest's**

Het wordt aanbevolen om slechts één map in Azure AD voor een organisatie te hebben, maar er wordt een 1:1-relatie tussen een Azure AD Connect-synchronisatieserver en een Azure AD-map onderhouden.  Voor elk exemplaar van Azure AD hebt u een installatie van Azure AD Connect nodig.  Ook Azure AD, door het ontwerp is geïsoleerd en gebruikers in een exemplaar van Azure AD niet in staat zijn om gebruikers te zien in een ander geval.

Het is mogelijk en ondersteund om één on-premises instantie van Active Directory te verbinden met meerdere Azure AD-mappen, zoals in de onderstaande afbeelding wordt weergegeven:

![filteren op één forest](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Filterscenario voor één forest**

Om dit te doen, moet het volgende waar zijn:

* Azure AD Connect-synchronisatieservers moeten zijn geconfigureerd voor filtering, zodat ze elk een wederzijds exclusieve set objecten hebben.  Dit bijvoorbeeld door elke server naar een bepaald domein of ou te scoping.
* Een DNS-domein kan alleen worden geregistreerd in één Azure AD-map, zodat de UPN's van de gebruikers in het on-premises AD afzonderlijke naamruimten moeten gebruiken
* Gebruikers in één exemplaar van Azure AD kunnen alleen gebruikers zien vanuit hun instantie.  Ze kunnen gebruikers niet zien in de andere exemplaren
* Slechts één van de Azure AD-mappen kan Exchange-hybride inschakelen met het on-premises AD
* Wederzijdse exclusiviteit geldt ook voor terugschrijven.  Dit maakt een aantal terugschrijffuncties die niet worden ondersteund met deze topologie, omdat deze uitgaan van een enkele on-premises configuratie.  Dit omvat:
  * Terugschrijven groeperen met standaardconfiguratie
  * Apparaat terugschrijven

Het volgende wordt niet ondersteund en mag niet worden gekozen als een uitvoering:

* Er wordt niet ondersteund voor het feit dat meerdere Azure AD Connect-synchronisatieservers verbinding maken met dezelfde Azure AD-map, zelfs als ze zijn geconfigureerd om wederzijds exclusieve set objecten te synchroniseren
* Het wordt niet ondersteund om dezelfde gebruiker te synchroniseren met meerdere Azure AD-mappen. 
* Het wordt ook niet ondersteund om een configuratiewijziging aan te brengen om ervoor te zorgen dat gebruikers in een Azure AD worden weergegeven als contactpersonen in een andere Azure AD-map. 
* Het wordt ook niet ondersteund om azure AD Connect-synchronisatie te wijzigen om verbinding te maken met meerdere Azure AD-mappen.
* Azure AD-mappen zijn door ontwerp geïsoleerd. Het wordt niet ondersteund om de configuratie van Azure AD Connect-synchronisatie te wijzigen om gegevens uit een andere Azure AD-map te lezen in een poging om een gemeenschappelijke en uniforme GAL tussen de mappen te bouwen. Het wordt ook niet ondersteund voor het exporteren van gebruikers als contactpersonen naar een ander on-premises AD met Azure AD Connect-synchronisatie.

> [!NOTE]
> Als uw organisatie computers in uw netwerk beperkt om verbinding te maken met internet, worden in dit artikel de eindpunten (FQDN's, IPv4- en IPv6-adresbereiken) weergegeven die u moet opnemen in uw uitgaande stalijsten en de Internet Explorer Trusted Sites Zone van de client computers om ervoor te zorgen dat uw computers Office 365 kunnen gebruiken. Lees voor meer informatie [Office 365-URL's en IP-adresbereiken](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definieer multi-factor authenticatie strategie
In deze taak definieert u de multi-factor authenticatie strategie te gebruiken.  Azure Multi-Factor Authentication wordt geleverd in twee verschillende versies.  De ene is een cloud-based en de andere is on-premises gebaseerd met behulp van de Azure MFA Server.  Op basis van de evaluatie die u hierboven hebt uitgevoerd, u bepalen welke oplossing de juiste is voor uw strategie.  Gebruik de onderstaande tabel om te bepalen welke ontwerpoptie het beste voldoet aan de beveiligingsvereisten van uw bedrijf:

Multi-factor ontwerpopties:

| Asset om te beveiligen | MFA in de cloud | MFA on-premises |
| --- | --- | --- |
| Microsoft-apps |ja |ja |
| SaaS-apps in de app-galerie |ja |ja |
| IIS-toepassingen die zijn gepubliceerd via toepassingsproxy van Azure AD |ja |ja |
| IIS-toepassingen die niet zijn gepubliceerd via de Azure AD App-proxy |nee |ja |
| Remote access als VPN, RDG |nee |ja |

Hoewel u misschien een oplossing voor uw strategie hebt opgelost, moet u de evaluatie nog steeds van bovenaf gebruiken waar uw gebruikers zich bevinden.  Dit kan ertoe leiden dat de oplossing verandert.  Gebruik de onderstaande tabel om u te helpen dit te bepalen:

| Gebruikerslocatie | Voorkeursontwerpoptie |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthentication in de cloud |
| Azure AD en on-premises AD dat gebruikmaakt van federatie met AD FS |Beide |
| Azure AD en on-premises AD met Azure AD Connect geen wachtwoordsynchronisatie |Beide |
| Azure AD en on-premises met Azure AD Connect met wachtwoordsynchronisatie |Beide |
| On-premises AD |Multi-Factor Authentication-server |

> [!NOTE]
> U moet er ook voor zorgen dat de multi-factor authenticatie ontwerpoptie die u hebt geselecteerd, de functies ondersteunt die nodig zijn voor uw ontwerp.  Voor meer informatie [lees Kies de multi-factor beveiligingsoplossing voor u.](../authentication/concept-mfa-howitworks.md)
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth Provider
Meervoudige verificatie is standaard beschikbaar voor globale beheerders die een Azure Active Directory-tenant hebben. Als u echter multi-factor authenticatie wilt uitbreiden naar al uw gebruikers en/of wilt u uw globale beheerders om te kunnen profiteren van functies zoals de beheerportal, aangepaste begroetingen en rapporten, dan moet u multi-factor authentication provider kopen en configureren.

> [!NOTE]
> U moet er ook voor zorgen dat de multi-factor authenticatie ontwerpoptie die u hebt geselecteerd, de functies ondersteunt die nodig zijn voor uw ontwerp. 
> 
> 

## <a name="next-steps"></a>Volgende stappen
[Vereisten voor gegevensbescherming bepalen](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Zie ook
[Overzicht ontwerpoverwegingen](plan-hybrid-identity-design-considerations-overview.md)

