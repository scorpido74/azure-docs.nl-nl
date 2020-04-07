---
title: Implementatieoverwegingen voor het opnieuw instellen van azure Active Directory-zelfservicewachtwoord
description: Meer informatie over implementatieoverwegingen en strategie voor een succesvolle implementatie van azure AD selfservice wachtwoordreset
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: c11521ec074b63843b873c39102b68bf185d2821
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676731"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Een azure Active Directory selfservice-implementatie voor het opnieuw instellen van wachtwoorden plannen

> [!IMPORTANT]
> Dit implementatieplan biedt richtlijnen en aanbevolen procedures voor het implementeren van Azure AD selfservice password reset (SSPR).
>
> **Als u en de eindgebruiker en de noodzaak om [https://aka.ms/sspr](https://aka.ms/sspr)terug te krijgen in uw account, ga naar **.

[Self-Service Password Reset (SSPR)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) is een Azure Active Directory (AD)-functie waarmee gebruikers hun wachtwoorden kunnen resetten zonder contact op te nemen met IT-personeel voor hulp. De gebruikers kunnen zichzelf snel deblokkeren en blijven werken, ongeacht waar ze zijn of het tijdstip van de dag. Door de werknemers deblokkering te laten deblokkeren, kan uw organisatie de niet-productieve tijd en hoge ondersteuningskosten voor de meest voorkomende wachtwoordgerelateerde problemen verlagen.

SSPR heeft de volgende belangrijke mogelijkheden:

* Met selfservice kunnen eindgebruikers hun verlopen of niet-verlopen wachtwoorden opnieuw instellen zonder contact op te nemen met een beheerder of helpdesk voor ondersteuning.
* [Password Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) maakt het beheer van on-premises wachtwoorden en het oplossen van account lockout via de cloud mogelijk.
* Rapporten over wachtwoordbeheeractiviteit geven beheerders inzicht in wachtwoordreset en registratieactiviteit die zich in hun organisatie voordoen.

Deze implementatiehandleiding laat u zien hoe u een Implementatie-implementatie van SSPR plannen en vervolgens testen.

Ga als u snel sspr in actie wilt zien en vervolgens terug wilt komen om aanvullende implementatieoverwegingen te begrijpen:

> [!div class="nextstepaction"]
> [Self-service wachtwoordreset inschakelen (SSPR)](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>Meer informatie over SSPR

Meer informatie over SSPR. Zie [hoe het werkt: Azure AD self-service password reset](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks).

### <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voordelen van het inschakelen van SSPR zijn:

* **Kosten beheren**. SSPR verlaagt de kosten voor IT-ondersteuning doorgebruikers in staat te stellen wachtwoorden zelf te resetten. Het vermindert ook de kosten van verloren tijd als gevolg van verloren wachtwoorden en lock-outs. 

* **Intuïtieve gebruikerservaring.** Het biedt een intuïtief eenmalig gebruikersregistratieproces waarmee gebruikers wachtwoorden kunnen resetten en accounts on-demand kunnen deblokkeren vanaf elk apparaat of locatie. Met SSPR kunnen gebruikers sneller weer aan het werk en productiever zijn.

* **Flexibiliteit en zekerheid.** Met SSPR hebben bedrijven toegang tot de beveiliging en flexibiliteit die een cloudplatform biedt. Beheerders kunnen instellingen wijzigen om aan nieuwe beveiligingsvereisten te voldoen en deze wijzigingen uitrollen naar gebruikers zonder hun aanmelding te verstoren.

* **Robuuste controle en het bijhouden van toepassingen**. Een organisatie kan ervoor zorgen dat de bedrijfssystemen veilig blijven terwijl de gebruikers hun eigen wachtwoorden resetten. Robuuste controlelogboeken bevatten informatie over elke stap van het wachtwoordresetproces. Deze logboeken zijn beschikbaar vanuit een API en stellen de gebruiker in staat om de gegevens te importeren in een SIEM-systeem (Security Incident and Event Monitoring).

### <a name="licensing"></a>Licentieverlening

Azure Active Directory heeft een licentie per gebruiker, wat betekent dat elke gebruiker een geschikte licentie nodig heeft voor de functies die ze gebruiken. We raden groepslicenties voor SSPR aan. 

Zie [Licentievereisten voor Azure AD selfservice wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)om edities en functies te vergelijken en licenties op basis van groepen of gebruikers in te schakelen.

Zie [Azure Active Directory-prijzen](https://azure.microsoft.com/pricing/details/active-directory/)voor meer informatie over prijzen.

### <a name="prerequisites"></a>Vereisten

* Een werkende Azure AD-tenant waarop minimaal een proeflicentie is ingeschakeld. Maak er indien nodig [gratis een.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Een account met de bevoegdheden van een globale beheerder.


### <a name="training-resources"></a>Opleidingsmiddelen

| Resources| Koppeling en beschrijving |
| - | - |
| Video's| [Geef uw gebruikers een betere IT-schaalbaarheid](https://youtu.be/g9RpRnylxS8) 
| |[Wat is selfservice wachtwoord resetten?](https://youtu.be/hc97Yx5PJiM)|
| |[Self-service wachtwoord opnieuw instellen implementeren](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Hoe configureer je selfservice wachtwoordreset voor gebruikers in Azure AD?](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Gebruikers [voorbereiden] om [hun] beveiligingsgegevens te registreren voor Azure Active Directory](https://youtu.be/gXuh0XS18wA) |
| Online cursussen|[Identiteiten beheren in Microsoft Azure Active Directory](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) Gebruik SSPR om uw gebruikers een moderne, beschermde ervaring te bieden. Zie met name de module "[Azure Active Directory Users and Groups](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)beheren". |
|Pluralsight Betaalde cursussen |[De kwesties van identiteits- en toegangsbeheer](https://www.pluralsight.com/courses/identity-access-management-issues) Meer informatie over IAM en beveiligingsproblemen om op de hoogte te zijn van uw organisatie. Zie vooral de module 'Andere verificatiemethoden'.|
| |[Aan de slag met de Microsoft Enterprise Mobility Suite](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) Leer de aanbevolen procedures voor het uitbreiden van on-premises assets naar de cloud op een manier die verificatie, autorisatie, versleuteling en een beveiligde mobiele ervaring mogelijk maakt. Zie met name de module 'Geavanceerde functies van Microsoft Azure Active Directory Premium configureren'.
|Zelfstudies |[Een pilot van een Azure AD-selfservice voor wachtwoordherstel uitrollen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[Wachtwoord terugschrijven inschakelen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Azure AD-wachtwoord opnieuw instellen vanaf het aanmeldingsscherm voor Windows 10](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| Veelgestelde vragen|[Veelgestelde vragen voor wachtwoordbeheer](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>Architectuur voor de oplossing

In het volgende voorbeeld wordt de wachtwoordoplossingsarchitectuur voor veelvoorkomende hybride omgevingen beschreven.

![diagram van oplossingsarchitectuur](./media/howto-sspr-deployment//solutions-architecture.png)

Beschrijving van de werkstroom

Om het wachtwoord opnieuw in te stellen, gaan gebruikers naar de [wachtwoordresetportal.](https://aka.ms/sspr) Zij moeten de eerder geregistreerde verificatiemethode of -methoden verifiëren om hun identiteit te bewijzen. Als ze het wachtwoord opnieuw instellen, beginnen ze het resetproces.

* Voor alleen cloudgebruikers slaat SSPR het nieuwe wachtwoord op in Azure AD. 

* Voor hybride gebruikers schrijft SSPR het wachtwoord terug naar de on-prem Active Directory via de Azure AD Connect-service. 

Opmerking: Voor gebruikers die [PhS-synchronisatie (Password hash synchronization)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) hebben uitgeschakeld, slaat SSPR de wachtwoorden alleen op in de on-prem Active Directory.

### <a name="best-practices"></a>Aanbevolen procedures

U gebruikers helpen om snel te registreren door SSPR te implementeren naast een andere populaire toepassing of service in de organisatie. Deze actie genereert een groot aantal aanmeldingen en zorgt voor registratie.

Voordat u SSPR implementeert, u ervoor kiezen om het aantal en de gemiddelde kosten van elk gesprek voor het opnieuw instellen van wachtwoorden te bepalen. U deze implementatie na het plaatsen van gegevens gebruiken om de waarde aan te geven die SSPR aan de organisatie oplevert.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>Gecombineerde registratie inschakelen voor SSPR en MFA

Microsoft raadt organisaties aan de gecombineerde registratie-ervaring voor SSPR en multi-factor authenticatie in te schakelen. Wanneer u deze gecombineerde registratie-ervaring inschakelt, hoeven gebruikers hun registratiegegevens slechts één keer te selecteren om beide functies in te schakelen.

De gecombineerde registratie-ervaring vereist geen organisaties om zowel SSPR- als Azure Multi-Factor Authentication in te schakelen. Gecombineerde registratie biedt organisaties een betere gebruikerservaring. Zie [Registratie van gecombineerde beveiligingsgegevens (voorbeeld) voor](concept-registration-mfa-sspr-combined.md) meer informatie.

## <a name="plan-the-deployment-project"></a>Het implementatieproject plannen

Houd rekening met uw organisatorische behoeften terwijl u de strategie voor deze implementatie in uw omgeving bepaalt.

### <a name="engage-the-right-stakeholders"></a>Betrek de juiste stakeholders

Wanneer technologieprojecten mislukken, doen ze dit meestal vanwege de onjuiste verwachtingen over impact, resultaten en verantwoordelijkheden. Om deze valkuilen te vermijden, [moet u ervoor zorgen dat u de juiste belanghebbenden in dienst nemen](https://aka.ms/deploymentplans) en dat de rol van belanghebbenden in het project goed wordt begrepen door de belanghebbenden en hun projectinput en accountates te documenteren.

#### <a name="required-administrator-roles"></a>Vereiste beheerdersrollen


| Zakelijke rol/persona| Azure AD-rol (indien nodig) |
| - | - |
| Niveau 1 helpdesk| Wachtwoordbeheerder |
| Niveau 2 helpdesk| Gebruikersbeheerder |
| SSPR-beheerder| Globale beheerder |


### <a name="plan-communications"></a>De communicatie plannen

Communicatie is cruciaal voor het succes van elke nieuwe dienst. U moet proactief communiceren met uw gebruikers hoe hun ervaring zal veranderen, wanneer deze zal veranderen en hoe u ondersteuning krijgen als ze problemen ervaren. Bekijk het uitrolmateriaal voor het [opnieuw instellen van het selfservicewachtwoord in het Microsoft-downloadcentrum](https://www.microsoft.com/download/details.aspx?id=56768) voor ideeën over het plannen van uw communicatiestrategie voor eindgebruikers.

### <a name="plan-a-pilot"></a>Een pilot plannen

We raden u aan dat de eerste configuratie van SSPR zich in een testomgeving bevindt. Begin met een pilotgroep door SSPR in te schakelen voor een subset van gebruikers in uw organisatie. Zie [Aanbevolen procedures voor een pilot.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)

Als u een groep wilt maken, ziet u hoe [u een groep maakt en leden toevoegt in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal). 

## <a name="plan-configuration"></a>Configuratie plannen

De volgende instellingen zijn vereist om SSPR in te schakelen, samen met de aanbevolen waarden.

| Onderwerp | Instelling | Waarde |
| --- | --- | --- |
| **SSPR-eigenschappen** | Selfservice wachtwoord opnieuw instellen ingeschakeld | **Geselecteerde** groep voor pilot / **All** for production |
| **Verificatiemethoden** | Verificatiemethoden die nodig zijn om te registreren | Altijd 1 meer dan nodig is voor reset |
|   | Verificatiemethoden die nodig zijn om opnieuw te worden ingesteld | Een of twee |
| **Registratie** | Vereisen dat gebruiker zich bij aanmelding registreren | Ja |
|   | Het aantal dagen waarna gebruikers wordt gevraagd om de verificatiegegevens opnieuw te bevestigen | 90 – 180 dagen |
| **Meldingen** | Gebruikers een melding tonen over het opnieuw instellen van hun wachtwoord | Ja |
|   | Alle beheerders waarschuwen wanneer andere beheerders hun wachtwoord opnieuw instellen | Ja |
| **Aanpassing** | Helpdeskkoppeling aanpassen | Ja |
|   | Aangepaste helpdesk-e-mail of URL | Ondersteuningssite of e-mailadres |
| **On-premises integratie** | Wachtwoorden terugschrijven naar on-premises AD | Ja |
|   | Gebruikers toestaan om een account te ontgrendelen zonder het wachtwoord opnieuw in te stellen | Ja |

### <a name="sspr-properties"></a>SSPR-eigenschappen

Wanneer u SSPR inschakelt, kiest u een geschikte beveiligingsgroep in de pilotomgeving.

* Om SSPR-registratie voor iedereen af te dwingen, raden we u aan de optie **Alles te** gebruiken.
* Selecteer anders de juiste Azure AD- of AD-beveiligingsgroep.

### <a name="authentication-methods"></a>Verificatiemethoden

Wanneer SSPR is ingeschakeld, kunnen gebruikers hun wachtwoord alleen opnieuw instellen als ze gegevens bevatten in de verificatiemethoden die de beheerder heeft ingeschakeld. Methoden omvatten telefoon, Authenticator app kennisgeving, beveiligingsvragen, enz. Zie [Wat zijn verificatiemethoden voor](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)meer informatie?

We raden de volgende instellingen voor verificatiemethoden aan:

* Stel de **verificatiemethoden in die nodig zijn om te registreren** op ten minste één meer dan het aantal dat nodig is om opnieuw te resetten. Het toestaan van meerdere verificaties geeft gebruikers flexibiliteit wanneer ze moeten resetten.

* Stel **het aantal methoden in dat nodig is om te resetten** op een niveau dat geschikt is voor uw organisatie. Men vereist de minste wrijving, terwijl twee kan uw veiligheid houding te verhogen. 

Opmerking: de gebruiker moet de verificatiemethoden hebben geconfigureerd in het wachtwoordbeleid en de [beperkingen in Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

### <a name="registration-settings"></a>Registratie-instellingen

**Gebruikers vereisen registreren wanneer u zich aanmeldt bij** **Ja**. Deze instelling vereist dat gebruikers zich registreren wanneer ze zich aanmelden, zodat alle gebruikers worden beschermd.

Stel **het aantal dagen in voordat gebruikers wordt gevraagd hun verificatiegegevens opnieuw te bevestigen** tot tussen de **90** en **180** dagen, tenzij uw organisatie een zakelijke behoefte heeft aan een korter tijdsbestek.

### <a name="notifications-settings"></a>Instellingen voor meldingen

Configureer zowel de **Gebruikers op het melden bij wachtwoordresets** als de Alle beheerders op de hoogte stellen wanneer andere beheerders hun wachtwoord opnieuw **instellen** op **Ja.** Als **u Ja** selecteert op beide, verhoogt u de beveiliging door ervoor te zorgen dat gebruikers op de hoogte zijn wanneer hun wachtwoord wordt gereset. Het zorgt er ook voor dat alle beheerders op de hoogte zijn wanneer een beheerder een wachtwoord wijzigt. Als gebruikers of beheerders een melding ontvangen en ze de wijziging niet hebben geïnitieerd, kunnen ze onmiddellijk een mogelijk beveiligingsprobleem melden.

### <a name="customization-settings"></a>Aanpassingsinstellingen

Het is essentieel om de e-mail of URL van de helpdesk aan te passen om ervoor te zorgen dat gebruikers die problemen ondervinden onmiddellijk hulp kunnen krijgen. Stel deze optie in op een algemeen helpdesk-e-mailadres of webpagina die uw gebruikers kennen. 

Zie [De Azure AD-functionaliteit aanpassen voor het opnieuw instellen van het wachtwoord voor selfservice.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

### <a name="password-writeback"></a>Terugschrijven met wachtwoord

**Password Writeback** is ingeschakeld met [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) en schrijft wachtwoordresets in de cloud terug naar een bestaande on-premises directory in realtime. Zie [Wat is Password Writeback voor](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) meer informatie?

We raden de volgende instellingen aan:

* Controleer of **Wachtwoorden terugschrijven naar on-premises AD** is ingesteld op **Ja.** 
* Stel gebruikers **in om een account te ontgrendelen zonder het wachtwoord opnieuw in** te stellen op **Ja.**

Azure AD ontgrendelt standaard accounts wanneer het een wachtwoordreset uitvoert.

### <a name="administrator-password-setting"></a>Instelling voor beheerderswachtwoord

Beheerdersaccounts hebben verhoogde machtigingen. De on-premises bedrijfs- of domeinbeheerders kunnen hun wachtwoorden niet opnieuw instellen via SSPR. On-premises beheerdersaccounts hebben de volgende beperkingen:

* kan alleen hun wachtwoord wijzigen in hun on-prem-omgeving.
* kan nooit gebruik maken van de geheime vragen en antwoorden als een methode om hun wachtwoord te resetten.

We raden u aan uw active directory-beheeraccounts niet te synchroniseren met Azure AD.

### <a name="environments-with-multiple-identity-management-systems"></a>Omgevingen met meerdere identiteitsbeheersystemen

Sommige omgevingen hebben meerdere identiteitsbeheersystemen. On-premises identiteitsmanagers zoals Oracle AM en SiteMinder vereisen synchronisatie met AD voor wachtwoorden. U dit doen met behulp van een tool zoals de Password Change Notification Service (PCNS) met Microsoft Identity Manager (MIM). Zie het artikel De MIM Password [Change Notification Service implementeren op een domeincontroller](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)voor het vinden van informatie over dit complexere scenario.

## <a name="plan-testing-and-support"></a>Testen en ondersteuning plannen

Zorg er in elke fase van uw implementatie van de eerste pilotgroepen tot en met de organisatie voor dat de resultaten zoals verwacht zijn.

### <a name="plan-testing"></a>Testen plannen

Als u ervoor wilt zorgen dat uw implementatie werkt zoals verwacht, plant u een reeks testcases om de implementatie te valideren. Om de testcases te beoordelen, hebt u een niet-beheerderstestgebruiker met een wachtwoord nodig. Zie [Nieuwe gebruikers toevoegen aan Azure Active Directory](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)als u een gebruiker wilt maken.

In de volgende tabel vindt u handige testscenario's die u gebruiken om verwachte resultaten van uw organisatie te documenteren op basis van uw beleid.
<br>


| Business case| Verwachte resultaten |
| - | - |
| SSPR portal is toegankelijk vanuit het bedrijfsnetwerk| Bepaald door uw organisatie |
| SSPR portal is toegankelijk van buiten het bedrijfsnetwerk| Bepaald door uw organisatie |
| Gebruikerswachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker niet is ingeschakeld voor het opnieuw instellen van het wachtwoord| De gebruiker heeft geen toegang tot de wachtwoordresetstroom |
| Gebruikerswachtwoord opnieuw instellen vanuit de browser wanneer de gebruiker zich niet heeft geregistreerd voor het opnieuw instellen van het wachtwoord| De gebruiker heeft geen toegang tot de wachtwoordresetstroom |
| Gebruiker meldt zich aan wanneer deze wordt afgedwongen om de registratie van wachtwoorden opnieuw in te stellen| Vraagt de gebruiker om beveiligingsgegevens te registreren |
| Gebruiker meldt zich aan wanneer de registratie van het wachtwoord opnieuw instellen is voltooid| Vraagt de gebruiker om beveiligingsgegevens te registreren |
| SSPR-portal is toegankelijk wanneer de gebruiker geen licentie heeft| Is toegankelijk |
| Gebruikerswachtwoord opnieuw instellen vanuit Windows 10 Azure AD samengevoegd of hybride Azure AD samengevoegd apparaatvergrendelingsscherm| Gebruiker kan wachtwoord opnieuw instellen |
| SSPR-registratie- en gebruiksgegevens zijn in bijna realtime beschikbaar voor beheerders| Is beschikbaar via auditlogs |

U ook verwijzen naar [Het voltooien van een Azure AD self-service password reset pilot roll](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot). In deze zelfstudie schakelt u een pilot-uitrol van SSPR in uw organisatie in en test u met een niet-beheerdersaccount.

### <a name="plan-support"></a>Ondersteuning plannen

Hoewel SSPR doorgaans geen gebruikersproblemen creëert, is het belangrijk om ondersteunend personeel voor te bereiden op problemen die zich kunnen voordoen. Hoewel een beheerder het wachtwoord voor eindgebruikers kan resetten via de Azure AD-portal, is het beter om het probleem op te lossen via een selfserviceondersteuningsproces.

Om het succes van uw ondersteuningsteam mogelijk te maken, u een veelgestelde vragen maken op basis van vragen die u van uw gebruikers ontvangt. Enkele voorbeelden:

| Scenario's| Beschrijving |
| - | - |
| Gebruiker heeft geen geregistreerde verificatiemethoden beschikbaar| Een gebruiker probeert zijn wachtwoord opnieuw in te stellen, maar heeft geen verificatiemethoden die ze hebben geregistreerd (bijvoorbeeld: ze hebben hun mobiele telefoon thuis gelaten en hebben geen toegang tot e-mail) |
| Gebruiker ontvangt geen sms of oproep op kantoor of mobiele telefoon| Een gebruiker probeert zijn identiteit te verifiëren via sms of oproep, maar ontvangt geen sms/oproep. |
| De gebruiker heeft geen toegang tot de portal voor het opnieuw instellen van wachtwoorden| Een gebruiker wil zijn wachtwoord opnieuw instellen, maar is niet ingeschakeld voor het opnieuw instellen van het wachtwoord en heeft geen toegang tot de pagina om wachtwoorden bij te werken. |
| Gebruiker kan geen nieuw wachtwoord instellen| Een gebruiker voltooit de verificatie tijdens de wachtwoordresetstroom, maar kan geen nieuw wachtwoord instellen. |
| Gebruiker ziet geen koppeling Wachtwoord opnieuw instellen op een Windows 10-apparaat| Een gebruiker probeert het wachtwoord opnieuw in te stellen vanaf het vergrendelingsscherm van Windows 10, maar het apparaat is niet verbonden met Azure AD of het intune-apparaatbeleid is niet ingeschakeld |

### <a name="plan-rollback"></a>Terugdraaien plannen

Ga als u de implementatie terugzet:

* voor één gebruiker, de gebruiker uit de beveiligingsgroep verwijderen 

* voor een groep de groep verwijderen uit de SSPR-configuratie

* Schakel SSPR voor de Azure AD-tenant voor iedereen uit

## <a name="deploy-sspr"></a>SSPR implementeren

Controleer voordat u deze implementeert het volgende:

1. Gemaakt en begonnen met het uitvoeren van uw [communicatieplan.](#plan-communications)

1. De juiste [configuratie-instellingen bepaald.](#plan-configuration)

1. Identificeerde de gebruikers en groepen voor de [pilot-](#plan-a-pilot) en productieomgevingen.

1. [Bepaalde configuratie-instellingen](#plan-configuration) voor registratie en selfservice.

1. [Geconfigureerde wachtwoord terugschrijftekst](#password-writeback) als u een hybride omgeving hebt.


**U bent nu klaar om SSPR in te zetten!**

Zie [Self-service wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) inschakelen voor volledige stapsgewijze aanwijzingen voor het configureren van de volgende gebieden.

1. [Verificatiemethoden](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [Registratie-instellingen](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [Instellingen voor meldingen](#notifications-settings)

1. [Aanpassingsinstellingen](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [On-premises integratie](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>SSPR inschakelen in Windows
Voor machines met Windows 7, 8, 8.1 en 10 u [gebruikers in staat stellen hun wachtwoord opnieuw in te stellen op het windows-aanmeldingsscherm](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows)

## <a name="manage-sspr"></a>SSPR beheren

Azure AD kan aanvullende informatie geven over uw SSPR-prestaties via audits en rapporten.

### <a name="password-management-activity-reports"></a>Activiteitsrapporten voor wachtwoordbeheer 

U vooraf gebouwde rapporten op azure-portal gebruiken om de SSPR-prestaties te meten. Als u een licentie hebt, u ook aangepaste query's maken. Zie [Rapportageopties voor Azure AD-wachtwoordbeheer voor](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) meer informatie

> [!NOTE]
>  U moet [een globale beheerder](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)zijn en u moet zich aanmelden voor het verzamelen van deze gegevens voor uw organisatie. Als u zich wilt aanmelden, moet u ten minste één keer het tabblad Rapportage of de controlelogboeken op de Azure Portal bezoeken. Tot die tijd worden de gegevens niet verzameld voor uw organisatie.

Controlelogboeken voor registratie en wachtwoordreset zijn 30 dagen beschikbaar. Als beveiligingscontrole binnen uw bedrijf een langere bewaring vereist, moeten de logboeken worden geëxporteerd en verbruikt in een SIEM-tool zoals [Azure Sentinel,](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory)Splunk of ArcSight.

![Schermafbeelding van SSPR-rapportage](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>Verificatiemethoden- Gebruik en inzichten

[Met gebruik en inzichten](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) u begrijpen hoe verificatiemethoden voor functies zoals Azure MFA en SSPR in uw organisatie werken. Deze rapportagemogelijkheid biedt uw organisatie de middelen om te begrijpen welke methoden registreren en hoe ze deze moeten gebruiken.

### <a name="troubleshoot"></a>Problemen oplossen

* Raadpleeg [problemen met het handmatige wachtwoord opnieuw instellen](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 

* [Wachtwoordbeheer volgen veelgestelde vragen](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 

### <a name="helpful-documentation"></a>Nuttige documentatie

* [Wat zijn verificatiemethoden?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [Hoe werkt het: Azure AD self-service password reset?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [De Azure AD-functionaliteit aanpassen voor het opnieuw instellen van selfservicewachtwoorden](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Wachtwoordbeleid en beperkingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)

* [Wat is wachtwoord terugschrijven?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure AD selfservice-wachtwoord opnieuw instellen inschakelen](tutorial-enable-sspr.md) voor SSPR als u aan de slag wilt gaan met het implementeren van SSPR

* [Overweeg azure AD-wachtwoordbeveiliging te implementeren](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Overweeg azure AD Smart Lockout te implementeren](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)