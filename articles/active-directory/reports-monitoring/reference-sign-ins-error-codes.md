---
title: Fout codes in de Azure Active Directory Portal | Microsoft Docs
description: Naslaginformatie over foutcodes voor aanmeldactiviteitenrapporten.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 08/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7a025835275169b260dfd1f91b65341b5ba02ff
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294096"
---
# <a name="sign-in-activity-report-error-codes"></a>Fout codes voor aanmeldings activiteiten rapport 

Met de informatie van het [rapport voor gebruikers aanmeldingen](concept-sign-ins.md)vindt u antwoorden op vragen zoals:

- Wie is aangemeld bij mijn toepassing?
- In welke toepassingen is aangemeld?
- Welke aanmeldingen zijn mislukt en waarom?

Wanneer een aanmelding mislukt, wordt een fout code weer geven die overeenkomt met de fout. In dit artikel vindt u een overzicht van de fout codes en de bijbehorende beschrijvingen, waarbij een aanbevolen actie wordt ondernomen, waar van toepassing. 

## <a name="how-can-i-display-failed-sign-ins"></a>Hoe kan ik mislukte aanmeldingen weergeven? 

Selecteer in het menu [Azure Portal](https://portal.azure.com) **Azure Active Directory**of zoek en selecteer **Azure Active Directory** op een wille keurige pagina.

![Azure Active Directory selecteren](./media/reference-sign-ins-error-codes/select-azure-active-directory.png "Azure Active Directory")

Onder **bewaking**selecteert u **aanmeldingen** om het [rapport aanmeldingen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)te openen.

![Aanmeldings activiteit](./media/reference-sign-ins-error-codes/monitoring-sign-ins-in-azure-active-directory.png "Aanmeldingsactiviteit")

Filter het rapport om alle mislukte aanmeldingen weer te geven door het selectie vakje **fout** te selecteren in de vervolg keuzelijst voor de **aanmeldings status** .

![Aanmeldings activiteit](./media/reference-sign-ins-error-codes/06.png "Aanmeldingsactiviteit")

Als u een item in de gefilterde lijst selecteert, worden de gegevens van de activiteit: de Blade **aanmeldingen** geopend. Deze weer gave bevat aanvullende informatie over de mislukte aanmeld gebeurtenis, inclusief de **fout code** voor het aanmelden en de reden van de **fout**.

![Aanmeldings activiteit](./media/reference-sign-ins-error-codes/05.png "Aanmeldingsactiviteit")

U kunt ook programmatisch toegang krijgen tot de aanmeldings gegevens met behulp van de [rapportage-API](concept-reporting-api.md).

## <a name="error-codes"></a>Foutcodes


|Fout|Beschrijving|
|---|---|
|16000|Dit is een intern implementatie detail en geen fout voorwaarde. U kunt deze referentie negeren.|
|20001|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|20012|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|20033|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|40008|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|40009|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|40014|Er is een probleem met uw federatieve id-provider. Neem contact op met uw IDP om dit probleem op te lossen.|
|50000|Er is een probleem met onze aanmeldingsservice. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om dit probleem op te lossen.|
|50001|De Service Principal Name is niet gevonden in deze tenant. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de Tenant of als de bron-Principal niet is gevonden in de map of ongeldig is.|
|50002|Aanmelden is mislukt vanwege beperkte proxytoegang op de tenant. Als het uw eigen Tenant beleid is, kunt u de instellingen voor de beperkte Tenant wijzigen om dit probleem op te lossen.|
|50003|Aanmelden is mislukt vanwege een ontbrekende handtekeningsleutel of ontbrekend handtekeningcertificaat. Dit kan komen doordat er geen handtekeningsleutel is geconfigureerd in de toepassing. Bekijk de oplossingen die worden beschreven in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Neem contact op met de eigenaar van de toepassing of de toepassings beheerder als het probleem zich blijft voordoen.|
|50005|De gebruiker heeft geprobeerd zich aan te melden bij een apparaat vanaf een platform dat momenteel niet wordt ondersteund via het beleid voor voorwaardelijke toegang.|
|50006| Handtekeningverificatie is mislukt vanwege ongeldige handtekening. Bekijk de oplossing die wordt beschreven in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Neem contact op met de eigenaar van de toepassing of de toepassings beheerder als het probleem zich blijft voordoen.|
|50007|Het partnerversleutelingscertificaat is niet gevonden voor deze toepassing. [Open een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) bij micro soft om dit probleem te verhelpen.|
|50008|SAML-verklaring ontbreekt of is niet juist geconfigureerd in het token. Neem contact op met uw federatieprovider.|
|50010|Validatie van de doelgroep-URI voor de toepassing is mislukt, omdat er geen tokendoelgroepen zijn geconfigureerd. Neem contact op met de eigenaar van de toepassing voor oplossing.|
|50011|Het antwoordadres ontbreekt, is onjuist geconfigureerd of komt niet overeen met de antwoordadressen die voor de toepassing zijn geconfigureerd. Probeer de oplossing die wordt weer gegeven op [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Neem contact op met de eigenaar van de toepassing of de toepassings beheerder als het probleem zich blijft voordoen.|
|50012| Dit is een algemeen fout bericht dat aangeeft dat de verificatie is mislukt. Dit kan gebeuren om redenen zoals ontbrekende of ongeldige referenties of claims in de aanvraag. Zorg ervoor dat de aanvraag wordt verzonden met de juiste referenties en claims. |
|50013|De bevestiging is om verschillende redenen ongeldig. De uitgever van het token komt bijvoorbeeld niet overeen met de API-versie binnen het geldige tijds bereik, het token is verlopen of misvormd of het vernieuwings token in de bevestiging is geen primair vernieuwings token.|
|50017|Validatie van certificaat is mislukt om de volgende redenen:<ul><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Verwacht CrlSegment kan niet worden gevonden</li><li>Kan uitgiftecertificaat niet vinden in de lijst met vertrouwde certificaten</li><li>Delta CRL-distributiepunt is geconfigureerd zonder een bijbehorend CRL-distributiepunt</li><li>Kan geen geldige CRL-segmenten ophalen als gevolg van time-outprobleem</li><li>Kan CRL niet downloaden</li></ul>Neem contact op met de Tenant beheerder.|
|50020|De gebruiker is niet gemachtigd om een van de volgende redenen.<ul><li>De gebruiker probeert zich aan te melden met een MSA-account met het v1-eind punt</li><li>De gebruiker bestaat niet in de Tenant.</li></ul> Neem contact op met de eigenaar van de toepassing.|
|50027|Ongeldig JWT-token om de volgende redenen:<ul><li>bevat geen nonceclaim, subclaim</li><li>onderwerp-id komt niet overeen</li><li>duplicaatclaim in idToken-claims</li><li>onverwachte certificaatverlener</li><li>onverwachte doelgroep</li><li>niet binnen geldig tijdsbereik </li><li>indeling van het token is niet juist</li><li>Handtekeningverificatie van extern ID-token van de verlener is mislukt.</li></ul>Neem contact op met de eigenaar van de toepassing|
|50029|Ongeldige URI: domeinnaam bevat ongeldige tekens. Neem contact op met de Tenant beheerder.|
|50034|De gebruiker bestaat niet in de lijst. Neem contact op met uw Tenant beheerder.|
|50042|Het zout dat vereist is voor het genereren van een Pairwise Identifier ontbreekt in principe. Neem contact op met de Tenant beheerder.|
|50048|Onderwerpverschillen verlenerclaim in de clientverklaring. Neem contact op met de Tenant beheerder.|
|50050|De aanvraag is onjuist gevormd. Neem contact op met de eigenaar van de toepassing.|
|50053|Het account is vergrendeld omdat de gebruiker zich te vaak heeft geprobeerd aan te melden met een onjuiste gebruikers-ID of een onjuist wacht woord.|
|50055|Het opgegeven wachtwoord is ongeldig of verlopen.|
|50056|Ongeldig of leeg wacht woord: het wacht woord bestaat niet in het Archief voor deze gebruiker.|
|50057|Het gebruikersaccount is uitgeschakeld. Het account is uitgeschakeld door een beheerder.|
|50058|De toepassing probeert een stille aanmelding uit te voeren en dit is niet gelukt voor de gebruiker. De toepassing moet een interactieve stroom starten waarmee gebruikers zich kunnen aanmelden. Neem contact op met de eigenaar van de toepassing.|
|50059|De gebruiker bestaat niet in de lijst. Neem contact op met uw Tenant beheerder.|
|50061|De aanvraag voor afmelden is ongeldig. Neem contact op met de eigenaar van de toepassing.|
|50072|De gebruiker moet zich inschrijven voor twee ledige verificatie (interactief).|
|50074|Gebruiker heeft de MFA-controle niet doorstaan.|
|50076|De gebruiker heeft de MFA-uitdaging niet door gegeven (niet interactief).|
|50078|De gepresenteerde multi-factor Authentication is verlopen. u moet uw multi-factor Authentication vernieuwen voor toegang.|
|50079|De gebruiker moet zich inschrijven voor twee ledige verificatie (niet-interactieve aanmeldingen).|
|50085|Vernieuwingstoken vereist een sociale IDP-aanmelding. Meld u opnieuw aan met de gebruikers naam en het wacht woord van de gebruiker.|
|50089|Stroom token verlopen-verificatie is mislukt. Meld u opnieuw aan met de gebruikers naam en het wacht woord van de gebruiker.|
|50097|Verificatie van apparaat vereist. Dit kan gebeuren omdat de claims DeviceId of DeviceAltSecId **Null**zijn of als er geen apparaat bestaat dat overeenkomt met de apparaat-id.|
|50099|JWT-handtekening is ongeldig. Neem contact op met de eigenaar van de toepassing.|
|50105|De aangemelde gebruiker is niet toegewezen aan een rol voor de aangemelde toepassing. Wijs de gebruiker toe aan de toepassing. Voor meer informatie: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|Aangevraagde federation realm-object bestaat niet. Neem contact op met de Tenant beheerder.|
|50120|Probleem met de JWT-header. Neem contact op met de Tenant beheerder.|
|50124|Claimtransformatie bevat een ongeldige invoerparameter. Neem contact op met de Tenant beheerder om het beleid bij te werken.|
|50125|Het aanmelden is onderbroken vanwege een wachtwoord herstel-of wachtwoord registratie vermelding.|
|50126|Ongeldige gebruikers naam of wacht woord, of ongeldige on-premises gebruikers naam of wacht woord.|
|50127|De gebruiker moet een Broker-toepassing installeren om toegang te krijgen tot deze inhoud.|
|50128|Ongeldige domein naam: er zijn geen Tenant gegevens gevonden in de aanvraag of geïmpliceerd door de verstrekte referenties.|
|50129|Het apparaat is niet gekoppeld aan de werk **plek-koppelen** is vereist voor het registreren van het apparaat.|
|50130|Claim waarde kan niet worden geïnterpreteerd als een bekende verificatie methode.|
|50131|Wordt gebruikt in verschillende fout berichten voor voorwaardelijke toegang. Bijvoorbeeld Een slechte Windows-apparaatstatus, geblokkeerde aanvraag vanwege verdachte activiteit, beslissingen voor toegangsbeleid en beveiligingsbeleid.|
|50132|Referenties zijn om de volgende redenen ingetrokken:<ul><li>SSO-artefact is ongeldig of verlopen</li><li>Sessie is niet actueel genoeg voor toepassing</li><li>Een aanvraag voor stille aanmelding is verzonden, maar de gebruikerssessie met Azure AD is ongeldig of verlopen.</li></ul>|
|50133|Sessie is ongeldig omdat het wachtwoord is verlopen of recent is gewijzigd.|
|50135|Het wacht woord moet worden gewijzigd vanwege een account risico.|
|50136|MSA-sessie naar toepassing omleiden-single MSA-sessie gedetecteerd. |
|50140|Deze fout is opgetreden vanwege de interrupt 'Aangemeld blijven' toen de gebruiker bezig was met aanmelden. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en een foutcode voor meer informatie. |
|50143|Sessie komt niet overeen. de sessie is ongeldig omdat de gebruikers Tenant niet overeenkomt met de domein Hint vanwege een andere bron.  [Open een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met correlatie-id, aanvraag-id en fout code voor meer informatie.|
|50144|Het Active Directory-wachtwoord van de gebruiker is verlopen. Genereer een nieuw wacht woord voor de gebruiker of laat de eind gebruiker gebruikmaken van het hulp programma selfservice reset.|
|50146|Deze toepassing moet worden geconfigureerd met een ondertekeningssleutel specifiek voor de toepassing. De toepassing is niet met een dergelijke sleutel geconfigureerd, of de sleutel is verlopen of nog niet geldig. Neem contact op met de eigenaar van de toepassing.|
|50148|De codeverificatie komt niet overeen met de code-uitdaging opgegeven in de autorisatieaanvraag voor PKCE. Neem contact op met de ontwikkelaar van de toepassing. |
|50155|De verificatie van het apparaat is mislukt voor deze gebruiker.|
|50158|Er is niet voldaan aan de externe beveiligings controle.|
|50161|Claims die door een externe provider zijn verzonden, zijn niet voldoende of de claim ontbreekt die is aangevraagd voor de externe provider.|
|50166|Het verzenden van de aanvraag naar de claim provider is mislukt.|
|50169|De realm is niet een geconfigureerde realm van de huidige servicenaamruimte.|
|50172|Externe claimprovider is niet goedgekeurd. Neem contact op met de Tenant beheerder|
|50173|Nieuwe verificatietoken is vereist. Laat de gebruiker opnieuw aanmelden met nieuwe referenties.|
|50177|Externe controle wordt niet ondersteund voor passthrough-gebruikers.|
|50178|Sessie beheer wordt niet ondersteund voor passthrough-gebruikers.|
|50180|Geïntegreerde Windows-verificatie is vereist. Schakel de tenant in voor een naadloze eenmalige aanmelding (SSO).|
|50181|Aan OTP gerelateerde fout tijdens het aanmelden. |
|50201|Deze bericht prompt wordt weer gegeven voor de gebruiker tijdens het aanmelden wanneer aanvullende informatie aan de gebruiker moet worden verstrekt.|
|51001|Er is geen domein Hint aanwezig met on-premises beveiligings-id-on-premises UPN.|
|51004|Gebruikersaccount bestaat niet in de map.|
|51006|Geïntegreerde Windows-verificatie is vereist. Gebruiker heeft zich aangemeld met een sessie token dat ontbreekt via claim. Vraag de gebruiker zich opnieuw aan te melden.|
|52004|Gebruiker heeft geen toestemming gegeven voor toegang tot LinkedIn-resources. |
|53000|Voor beleid voor voorwaardelijke toegang is een compatibel apparaat vereist. Het apparaat is niet compatibel. Laat de gebruiker hun apparaat inschrijven met een goedgekeurde MDM-provider, zoals intune.|
|53001|Voor beleid voor voorwaardelijke toegang is een domeindeelname-apparaat vereist en het apparaat neemt niet deel. Laat de gebruiker een apparaat gebruiken dat lid is van een domein.|
|53002|De gebruikte toepassing is geen goedgekeurde toepassing voor voorwaardelijke toegang. Gebruiker moet een van de apps uit de lijst met goedgekeurde toepassingen gebruiken om toegang te krijgen.|
|53003|De toegang is geblokkeerd vanwege het beleid voor voorwaardelijke toegang.|
|53004|Gebruiker moet multi-factor authentication-registratieproces voltooien om toegang te krijgen tot deze inhoud. Gebruiker moet zich registreren voor multi-factor authentication.|
|65001|Toepassing X heeft geen toegangsmachtiging voor toepassing Y of de machtiging is ingetrokken. Of de gebruiker of beheerder heeft niet toegestaan dat de toepassing wordt gebruikt met id X. Stuur een interactieve autorisatieaanvraag voor deze gebruiker en resource. Of de gebruiker of beheerder heeft niet toegestaan dat de toepassing wordt gebruikt met id X. Stuur een autorisatieaanvraag naar uw tenant-beheerder uit naam van toepassing Y voor resource Z.|
|65004|Gebruiker heeft geweigerd toestemming te geven app-toegang. Laat de gebruiker zich opnieuw aanmelden en toestemming geven voor de app|
|65005|De voor de toepassing vereiste resourcetoegangslijst bevat geen toepassingen die kunnen worden gedetecteerd door de resource, de clienttoepassing heeft toegang aangevraagd tot een resource die niet is opgegeven in de vereiste resourcetoegangslijst, de Graph-service heeft een onjuiste aanvraag geretourneerd of de resource is niet gevonden. Als de toepassing SAML ondersteunt, hebt u mogelijk de toepassing met de verkeerde id (entiteit) geconfigureerd. Probeer de oplossing voor SAML via de koppeling hieronder: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Ongeldige toekenning om de volgende redenen:<ul><li>Aangevraagde SAML 2.0-verklaring heeft een ongeldige bevestigingsmethode voor onderwerpen</li><li>App OnBehalfOf flow wordt niet ondersteund op V2</li><li>Primaire vernieuwingstoken is niet ondertekend met sessiesleutel</li><li>Ongeldig extern vernieuwingstoken</li><li>Het verlenen van toegang is verkregen voor een andere tenant.</li></ul>|
|70001|De toepassing X is niet gevonden in de tenant Y. Dit kan gebeuren als de toepassing X niet is geïnstalleerd door de beheerder van de tenant of er geen toestemming voor is verleend door een gebruiker in de tenant. Mogelijk hebt u de id-waarde voor de toepassing onjuist geconfigureerd of hebt u uw verificatie aanvraag verzonden naar de verkeerde Tenant.|
|70002|De toepassing heeft ongeldige clientreferenties geretourneerd. Neem contact op met de eigenaar van de toepassing.|
|70003|De toepassing heeft een niet-ondersteunde toekenningstype geretourneerd. Neem contact op met de eigenaar van de toepassing.|
|70004|De toepassing heeft een ongeldige omleidings-URI geretourneerd. Het omleidingsadres dat is gespecificeerd door de client komt niet overeen met een geconfigureerd adres of een adres op de OIDC-goedkeuringslijst. Neem contact op met de eigenaar van de toepassing.|
|70005|De toepassing heeft een niet-ondersteund reactietype geretourneerd om de volgende redenen:<ul><li>reactietype 'token' is niet ingeschakeld voor de toepassing</li><li>reactietype 'id_token' vereist 'OpenID'-bereik - bevat een niet-ondersteunde OAuth-parameterwaarde in de gecodeerde wctx</li></ul>Neem contact op met de eigenaar van de toepassing.|
|70007|De toepassing heeft een niet-ondersteunde 'response_mode'-waarde geretourneerd bij het aanvragen van een token. Neem contact op met de eigenaar van de toepassing.|
|70008|De gegeven autorisatie code of het vernieuwings token is verlopen of ingetrokken. De gebruiker moet zich opnieuw aanmelden.|
|70011|Het bereik dat is aangevraagd door de toepassing is ongeldig. Neem contact op met de eigenaar van de toepassing.|
|70012|Er is een serverfout opgetreden tijdens het verifiëren van een MSA-gebruiker (consument). Meld u opnieuw aan en als het probleem zich blijft voordoen, [opent u een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Ongeldige verificatiecode doordat gebruiker verkeerde gebruikerscode typt voor apparaatcodestroom. Autorisatie is niet goedgekeurd.|
|70019|Verificatiecode verlopen. Laat de gebruiker de aanmelding opnieuw proberen.|
|70037|Onjuist antwoord op verificatievraag. Externe verificatiesessie is geweigerd.|
|70043|Het beheer van de voorwaardelijke toegang van Azure zorgt ervoor dat de sessie verloopt|
|70044|Het beheer van de voorwaardelijke toegang van Azure zorgt ervoor dat de sessie verloopt|
|75001|Er is een fout opgetreden tijdens de binding van SAML-bericht.|
|75003|De toepassing heeft een fout met betrekking tot niet-ondersteunde binding geretourneerd (reactie van SAML-protocol kan niet worden verzonden via andere bindingen dan HTTP POST). Neem contact op met de eigenaar van de toepassing.|
|75005|Azure AD biedt geen ondersteuning voor de SAML-aanvraag die door de toepassing is verzonden voor eenmalige aanmelding. Neem contact op met de eigenaar van de toepassing.|
|75008|De aanvraag van de toepassing is geweigerd omdat de SAML-aanvraag een onverwacht doel heeft. Neem contact op met de eigenaar van de toepassing.|
|75011|Verificatiemethode waarmee de gebruiker is geverifieerd met de service komt niet overeen met aangevraagd verificatiemethode. Neem contact op met de eigenaar van de toepassing.|
|75016|SAML2-verificatieverzoek heeft ongeldige NameIdPolicy. Neem contact op met de eigenaar van de toepassing.|
|80001|Verificatieagent kan geen verbinding maken met Active Directory. Zorg ervoor dat de verificatie-agent is geïnstalleerd op een apparaat met domeindeelname met zicht op een DC die de aanmelding van de gebruiker kan regelen.|
|80002|Interne fout. Er is een time-out opgetreden voor wachtwoord validatie aanvraag. De verificatie aanvraag kan niet naar de interne Hybrid Identity-service worden verzonden. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie te krijgen over de fout.|
|80003|Ongeldig antwoord ontvangen door de verificatieagent. Er is een onbekende fout opgetreden tijdens een verificatiepoging op basis van on-premises Active Directory. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie te krijgen over de fout.|
|80005|Verificatieagent: er is een onbekende fout opgetreden tijdens het verwerken van het antwoord van de verificatieagent. [Open een ondersteuningsticket](../fundamentals/active-directory-troubleshooting-support-howto.md) om meer informatie te krijgen over de fout.|
|80007|Verificatieagent kan wachtwoord van gebruiker niet verifiëren.|
|80010|Verificatieagent kan wachtwoord niet ontsleutelen. |
|80011|Verificatieagent kan versleutelingssleutel niet ophalen.|
|80012|De gebruikers hebben geprobeerd zich aan te melden buiten het toegestane aantal uren (dit is opgegeven in AD).|
|80013|De verificatiepoging kan niet worden voltooid vanwege tijdverschil tussen de computer met de verificatieagent en AD. Los het probleem met tijdsynchronisatie op|
|80014|Time-out van verificatie agent. [Open een ondersteunings ticket](../fundamentals/active-directory-troubleshooting-support-howto.md) met de fout code, correlatie-id en datum-tijd om meer informatie over deze fout te krijgen.|
|81001|Kerberos-ticket van de gebruiker is te groot. Dit kan gebeuren als de gebruiker te veel groepen heeft en het Kerberos-ticket dus te veel groepslidmaatschappen bevat. Reduceer het aantal groepslidmaatschappen van de gebruiker en probeer het opnieuw.|
|81005|Verificatie pakket wordt niet ondersteund.|
|81007|De Tenant is niet ingeschakeld voor naadloze SSO.|
|81012|Dit is geen fout. Dit geeft aan dat de gebruiker die probeert zich aan te melden bij Azure AD verschilt van de gebruiker die is aangemeld bij het apparaat. U kunt deze code in de logboeken veilig negeren.|
|90010|De aanvraag kan om verschillende redenen niet worden ondersteund. De aanvraag wordt bijvoorbeeld gemaakt met behulp van een niet-ondersteunde aanvraag methode (alleen POST-methode wordt ondersteund) of het token handtekening algoritme dat is aangevraagd, wordt niet ondersteund. Neem contact op met de ontwikkelaar van de toepassing.|
|90014| Er ontbreekt een vereist veld voor een protocol bericht. Neem contact op met de eigenaar van de toepassing. Als u de eigenaar van de toepassing bent, moet u ervoor zorgen dat u alle benodigde para meters voor de aanmeldings aanvraag hebt. |
|90051| Ongeldig delegerings token. Er is een ongeldige nationale Cloud-ID ({cloudId}) opgegeven.|
|90072| Het account moet eerst worden toegevoegd als externe gebruiker in de tenant. Meld u af en meld u opnieuw aan met een ander Azure AD-account.|
|90094| De app heeft machtigingen aangevraagd waarvoor de aangemelde gebruiker niet toestemming mag geven en de gebruiker is geblokkeerd. |
|90095| De app heeft machtigingen aangevraagd waarvoor de aangemelde gebruiker niet toestemming mag geven en de gebruiker heeft het aanvraag formulier voor de [beheerder toestemming](../manage-apps/configure-admin-consent-workflow.md) gegeven. |
|500011| De resource-principal met de naam <site address> is niet gevonden in de Tenant met de naam <tenant ID>. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de Tenant of is gezonden door een gebruiker in de Tenant. Mogelijk hebt u de verificatieaanvraag naar de verkeerde tenant verzonden.|
|500021| De Tenant wordt beperkt door de bedrijfs proxy. De toegang tot de bron wordt geweigerd.|
|500121| Verificatie is mislukt tijdens sterke verificatie aanvraag.|
|500133| De verklaring valt niet binnen het geldige tijds bereik. Zorg ervoor dat het toegangs token niet is verlopen voordat u het gebruikt voor gebruikers bevestiging of een nieuw token aanvragen.|
|530021|De toepassing voldoet niet aan de goedgekeurde app-vereisten voor voorwaardelijke toegang.|
|530032|Geblokkeerd door beveiligings beleid.| 
|700016|De toepassing met de id {appIdentifier} is niet gevonden in de map {tenantnaam}. Dit kan gebeuren als de toepassing niet is geïnstalleerd door de beheerder van de Tenant of is gezonden door een gebruiker in de Tenant. Mogelijk hebt u uw verificatie aanvraag verzonden naar de verkeerde Tenant.|
|900432|De vertrouwelijke client wordt niet ondersteund in een cross-Cloud aanvraag.|
|5000811|Kan hand tekening van SAML-token niet controleren. De id van de handtekening sleutel komt niet overeen met een geldige geregistreerde sleutel.|
|7000215|Er is een ongeldig client geheim gegeven.|
|7000218|De aanvraag tekst moet de volgende para meter bevatten: ' client_assertion ' of ' client_secret '.|


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van rapporten over aanmeldingen](concept-sign-ins.md)
* [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)
