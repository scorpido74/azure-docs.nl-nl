---
title: Foutcodeverwijzing
titleSuffix: Azure AD B2C
description: Een lijst met de foutcodes die kunnen worden geretourneerd door de Azure Active Directory B2C-service.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188661"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Foutcodes: Azure Active Directory B2C

De volgende fouten kunnen worden geretourneerd door de Azure Active Directory B2C-service.

| Foutcode | Bericht |
| ---------- | ------- |
| `AADB2C90002` | De CORS-bron '{0}' keerde een 404 niet gevonden. |
| `AADB2C90006` | De omleiding{0}URI ' ' verstrekt in het verzoek{1}is niet geregistreerd voor de client id '' ' ' |
| `AADB2C90007` | De toepassing die is{0}gekoppeld aan client-id ' ' heeft geen geregistreerde omleiding URI's. |
| `AADB2C90008` | De aanvraag bevat geen parameter client-id. |
| `AADB2C90010` | De aanvraag bevat geen bereikparameter. |
| `AADB2C90011` | De klant-id '{0}' verstrekt in het{1}verzoek komt niet overeen met client id ' ' ' geregistreerd in de polis. |
| `AADB2C90012` | De in{0}de aanvraag geboden scope wordt niet ondersteund. |
| `AADB2C90013` | Het gevraagde antwoordtype{0}' ' in het verzoek wordt niet ondersteund. |
| `AADB2C90014` | De gevraagde reactiemodus{0}' ' in het verzoek wordt niet ondersteund. |
| `AADB2C90016` | Het gevraagde clientbevestigingstype{0}' ' komt niet{1}overeen met het verwachte type ' '. |
| `AADB2C90017` | De bewering van de klant in het verzoek is ongeldig:{0} |
| `AADB2C90018` | De client{0}id ' ' gespecificeerd in de{1}aanvraag is niet geregistreerd in huurder ' '. |
| `AADB2C90019` | De sleutelcontainer met{0}id '{1}in huurder ' heeft geen geldige sleutel. Reden: {2}. |
| `AADB2C90021` | Het technische{0}profiel ' ' bestaat{1}niet in{2}het beleid ' ' van huurder ''. |
| `AADB2C90022` | Kan geen metagegevens{0}retourneren voor{1}het beleid ' in tenant '. |
| `AADB2C90023` | Profiel{0}' bevat niet de vereiste{1}metadata key '' '. |
| `AADB2C90025` | Profiel{0}' in{1}beleid '{2}' in tenant ' '{3}bevat niet de vereiste cryptografische sleutel '. |
| `AADB2C90027` | Basisreferenties die zijn{0}opgegeven voor ' ' zijn ongeldig. Controleer of de referenties correct zijn en of de toegang is verleend door de resource. |
| `AADB2C90028` | Clientcertificaat dat{0}is opgegeven voor ' ' is ongeldig. Controleer of het certificaat correct is, een privésleutel bevat en of de toegang is verleend door de resource. |
| `AADB2C90031` | Beleid{0}' geeft geen standaardgebruikersreis op. Zorg ervoor dat het beleid of de ouders een standaardgebruikersreis opgeven als onderdeel van een sectie van een relying party. |
| `AADB2C90035` | De service is tijdelijk niet beschikbaar. Probeer het na een paar minuten opnieuw. |
| `AADB2C90036` | De aanvraag bevat geen URI om de gebruiker om te leiden naar een afmelding. Geef een URI op in het parameterveld post_logout_redirect_uri. |
| `AADB2C90037` | Er is een fout opgetreden tijdens het verwerken van de aanvraag. Neem contact op met de beheerder van de site die u probeert te openen. |
| `AADB2C90039` | De aanvraag bevat een bewering van{0}de klant,{1}maar het opgegeven beleid ' ' in huurder ' ontbreekt een client_secret in RelyingPartyPolicy. |
| `AADB2C90040` | User journey{0}' ' ' bevat geen send claims stap. |
| `AADB2C90043` | De prompt in de aanvraag bevat ongeldige waarden. Verwacht 'geen', 'login', 'toestemming' of 'select_account'. |
| `AADB2C90044` | De claim{0}' ' wordt niet ondersteund{1}door de claim resolver ' '. |
| `AADB2C90046` | We hebben problemen met het laden van uw huidige status. Misschien wilt u proberen uw sessie vanaf het begin opnieuw te starten. |
| `AADB2C90047` | De resource{0}' ' bevat scriptfouten die voorkomen dat deze wordt geladen. |
| `AADB2C90048` | Er is een niet-afgehandelde uitzondering op getreden op de server. |
| `AADB2C90051` | Er werden geen geschikte claimproviders gevonden. |
| `AADB2C90052` | Ongeldige gebruikersnaam of wachtwoord. |
| `AADB2C90053` | Een gebruiker met de opgegeven referentie kan niet worden gevonden. |
| `AADB2C90054` | Ongeldige gebruikersnaam of wachtwoord. |
| `AADB2C90055` | In het{0}toepassingsgebied ' ''' dathttps://example.com/calendar.readop verzoek is verstrekt, moet een resource worden vermeld, zoals ' |
| `AADB2C90057` | De meegeleverde toepassing is niet geconfigureerd om de OAuth Implicit flow toe te staan. |
| `AADB2C90058` | De meegeleverde toepassing is niet geconfigureerd om openbare clients toe te staan. |
| `AADB2C90067` | De post-uitloging{0}redirect URI ' heeft een ongeldige indeling. Geef een op httpshttps://example.com/returngebaseerde URL op, zoals ' of voor native clients gebruik de IETF native client URI 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | De meegeleverde applicatie{0}met ID ' ' is niet geldig voor deze service. Gebruik een applicatie die is gemaakt via de B2C portal en probeer het opnieuw. |
| `AADB2C90075` | De claimuitwisseling{0}' '{1}gespecificeerd in stap '{2}' geretourneerd{3}HTTP-foutreactie met Code ' en Reden ''. |
| `AADB2C90077` | De gebruiker heeft geen bestaande sessie en de{0}promptparameter heeft een waarde van ' '. |
| `AADB2C90079` | Klanten moeten een client_secret sturen bij het inwisselen van een vertrouwelijke subsidie. |
| `AADB2C90080` | De verstrekte subsidie is verlopen. Verifieer opnieuw en probeer het opnieuw. Huidige tijd: {0}, Grant {1}uitgegeven tijd: , {2}Grant schuifvenster vervaldatum: . |
| `AADB2C90081` | De opgegeven client_secret komt niet overeen met de verwachte waarde voor deze client. Corrigeer de client_secret en probeer het opnieuw. |
| `AADB2C90083` | De vereiste parameter ontbreekt: {0}. |
| `AADB2C90084` | Overheidscliënten mogen geen client_secret sturen bij het inwisselen van een openbaar verworven subsidie. |
| `AADB2C90085` | Er is een interne fout opgetreden in de service. Gelieve opnieuw te verifiëren en probeer het opnieuw. |
| `AADB2C90086` | De geleverde grant_type{0}[ ] wordt niet ondersteund. |
| `AADB2C90087` | De verstrekte subsidie is niet verstrekt voor deze versie van het protocoleindpunt. |
| `AADB2C90088` | De verstrekte subsidie is niet verstrekt voor dit eindpunt. Werkelijke waarde {0} : en verwachte waarde :{1} |
| `AADB2C90092` | De meegeleverde applicatie{0}met ID ' '{1}is uitgeschakeld voor de huurder ' '. Schakel de toepassing in en probeer het opnieuw. |
| `AADB2C90107` | De toepassing met{0}ID ' ' kan ook geen ID-token krijgen omdat de openid-scope niet in het verzoek is opgenomen of omdat de toepassing er niet voor is geautoriseerd. |
| `AADB2C90108` | De orchestration{0}step ' ' geeft geen CpimIssuerTechnicalProfileReferenceId op wanneer er een werd verwacht. |
| `AADB2C90110` | De scopeparameter moet 'openid' bevatten bij het aanvragen van een response_type die 'id_token' bevat. |
| `AADB2C90111` | Je account is vergrendeld. Neem contact op met uw ondersteuningspersoon om het te ontgrendelen en probeer het opnieuw. |
| `AADB2C90114` | Uw account is tijdelijk vergrendeld om onbevoegd gebruik te voorkomen. Probeer het later opnieuw. |
| `AADB2C90115` | Bij het aanvragen van de 'code' response_type moet de scopeparameter een bron- of client-id voor toegangstokens en 'openid' voor ID-tokens bevatten. Voeg daarnaast 'offline_access' toe voor vernieuwingstokens. |
| `AADB2C90117` | De scope{0}' '' die in het verzoek wordt gegeven, wordt niet ondersteund. |
| `AADB2C90118` | De gebruiker is zijn wachtwoord vergeten. |
| `AADB2C90120` | De in de{0}aanvraag opgegeven maximale leeftijdsparameter ' ' is ongeldig. De maxleeftijd moet een{1}geheel{2}zijn tussen ' en ' inclusief. |
| `AADB2C90122` | Invoer voor{0}' ' ontvangen in de aanvraag is mislukt HTTP-aanvraagvalidatie. Zorg ervoor dat de invoer geen tekens bevat zoals < of &. |
| `AADB2C90128` | De rekening die aan deze subsidie is gekoppeld, bestaat niet meer. Gelieve opnieuw te verifiëren en probeer het opnieuw. |
| `AADB2C90129` | De verstrekte subsidie is ingetrokken. Gelieve opnieuw te verifiëren en probeer het opnieuw. |
| `AADB2C90145` | Er zijn geen niet-geverifieerde telefoonnummers gevonden en het beleid staat niet toe dat een gebruiker een ingevoerd nummer heeft ingevoerd. |
| `AADB2C90146` | De scope{0}' '' 'in aanvraag geeft meer dan één bron op voor een toegangstoken, die niet wordt ondersteund. |
| `AADB2C90149` | Script{0}' ' niet te laden. |
| `AADB2C90151` | De gebruiker heeft het maximumaantal voor nieuwe pogingen voor meervoudige verificatie overschreden. |
| `AADB2C90152` | Een multi-factor poll aanvraag kan geen antwoord krijgen van de service. |
| `AADB2C90154` | Een verificatieverzoek voor meerdere factoren kan geen sessie-id van de service krijgen. |
| `AADB2C90155` | Een multi-factor verificatieverzoek is{0}mislukt met reden ' '. |
| `AADB2C90156` | Een validatieverzoek met meerdere factoren{0}is mislukt met reden ' '. |
| `AADB2C90157` | Gebruiker heeft het maximumaantal voor nieuwe pogingen voor een zelfverklaarde stap overschreden. |
| `AADB2C90158` | Een zelf-beweerde validatie verzoek is{0}mislukt met reden ' '. |
| `AADB2C90159` | Een zelfgeclaimde verificatieverzoek is mislukt{0}met reden ' '. |
| `AADB2C90161` | Een zelf-beweerde verzendenreactie heeft met{0}reden ontbroken '' . |
| `AADB2C90165` | Het SAML-initiërende{0}bericht met id ' kan niet worden gevonden in de staat. |
| `AADB2C90168` | De HTTP-Redirect-aanvraag bevat niet{0}de vereiste parameter ' ' voor een ondertekende aanvraag. |
| `AADB2C90178` | Het ondertekeningscertificaat '{0}' heeft geen privésleutel. |
| `AADB2C90182` | De geleverde code_verifier komt niet overeen met de bijbehorende code_challenge |
| `AADB2C90183` | De geleverde code_verifier is ongeldig |
| `AADB2C90184` | De meegeleverde code_challenge_method wordt niet ondersteund. Ondersteunde waarden zijn effen of S256 |
| `AADB2C90188` | Het SAML-technische{0}profiel ' ' specificeert een PartnerEntity URL van{1}{2}' ', maar het ophalen van de metadata mislukt met reden ' '. |
| `AADB2C90194` | Claim{0}' ' ' opgegeven voor de drager token is niet aanwezig in de beschikbare claims. Beschikbare claims{1}' '. |
| `AADB2C90205` | Deze toepassing heeft niet voldoende machtigingen voor deze webbron om de bewerking uit te voeren. |
| `AADB2C90206` | Er is een time-out opgetreden voor de initialisatie van de client. |
| `AADB2C90208` | De opgegeven id_token_hint parameter is verlopen. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90209` | De opgegeven id_token_hint parameter bevat geen geaccepteerd publiek. Geldige publiekswaarden: '{0}'. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90210` | De meegeleverde id_token_hint parameter kon niet worden gevalideerd. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90211` | Het verzoek bevatte een onvolledige staatscookie. |
| `AADB2C90212` | Het verzoek bevatte een ongeldige statuscookie. |
| `AADB2C90220` | De sleutelcontainer in{0}tenant '{1}met opslag-id ' bestaat, maar bevat geen geldig certificaat. Het certificaat kan verlopen zijn of uw certificaat kan in de toekomst actief worden (nbf). |
| `AADB2C90223` | Er is een fout opgetreden bij het ontsmetten van de CORS-bron. |
| `AADB2C90224` | De stroom van resource-eigenaar is niet ingeschakeld voor de toepassing. |
| `AADB2C90225` | De gebruikersnaam of het wachtwoord in het verzoek zijn ongeldig. |
| `AADB2C90226` | De opgegeven tokenuitwisseling wordt alleen ondersteund via HTTP POST. |
| `AADB2C90232` | De opgegeven id_token_hint parameter bevat geen geaccepteerde emittent. Geldige emittenten:{0}' '. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90233` | De opgegeven id_token_hint parameter is mislukt handtekening validatie. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90235` | De verstrekte id_token is verlopen. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90237` | De verstrekte id_token bevat geen geldig publiek. Geldige publiekswaarden: '{0}'. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90238` | De verstrekte id_token bevat geen geldige emittent. Geldige emittentwaarden:{0}' '. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90239` | De meegeleverde id_token mislukte handtekeningvalidatie. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90240` | De meegeleverde id_token is misvormd en kon niet worden ontleed. Geef een ander token op en probeer het opnieuw. |
| `AADB2C90242` | Het SAML-technisch{0}profiel ' ' specificeert PartnerEntity CDATA die om reden niet kan worden geladen ''.{1} |
| `AADB2C90243` | De clientsleutel/-geheim van de IDP is niet goed geconfigureerd. |
| `AADB2C90244` | Er zijn te veel verzoeken op dit moment. Wacht even en probeer het opnieuw. |
| `AADB2C90248` | Broneigenaarstroom kan alleen worden gebruikt door toepassingen die zijn gemaakt via de B2C-beheerportal. |
| `AADB2C90250` | Het algemene aanmeldingseindpunt wordt niet ondersteund. |
| `AADB2C90255` | De in technisch profiel{0}gespecificeerde schadewisseling '' voldeed niet zoals verwacht. Misschien wilt u proberen uw sessie vanaf het begin opnieuw te starten. |
| `AADB2C90261` | De claimuitwisseling{0}' '{1}gespecificeerd in stap ' ' geretourneerdHTTP-foutreactie die niet kon worden ontleed. |
| `AADB2C90272` | De parameter id_token_hint is niet opgegeven in de aanvraag. Geef token en probeer het opnieuw. |
| `AADB2C90273` | Er is een ongeldige reactie ontvangen : '{0} |
| `AADB2C90274` | De metagegevens van de provider geven geen enkele afmeldservice op of de endpointbinding is niet een van 'urn:oasis:names:tc:SAML:2.0:bindingen:HTTP-Redirect' of 'urn:oasis:names:tc:SAML:2.0:bindingen:HTTP-POST'. |
| `AADB2C90276` | De aanvraag komt niet overeen{0}met{1}de controleinstelling{2}' '{3}' in{4}technischProfiel ' ' voor beleid ' ' tenant ''. |
| `AADB2C90277` | De orchestration{0}step ' van{1}user journey{2}' ' van beleid ' ' bevat geen verwijzing naar de definitie van inhoud. |
| `AADB2C90279` | De opgegeven client{0}id ' ' ' komt niet overeen met de client id die de subsidie uitgegeven. |
| `AADB2C90284` | De toepassing met{0}id ' ' is geen toestemming verleend en kan niet worden gebruikt voor lokale accounts. |
| `AADB2C90285` | De toepassing met{0}id ' ' werd niet gevonden. |
| `AADB2C90288` | UserJourney met{0}id ' ' verwezen{1}in TechnicalProfile '{2}' voor vernieuwen token{3}inwisseling voor tenant ' bestaat niet in beleid ' of een van de basisbeleid. |
| `AADB2C90289` | We hebben een fout ondervonden bij het maken van een verbinding met de identiteitsprovider. Probeer het later opnieuw. |
| `AADB2C90296` | Toepassing is niet correct geconfigureerd. Neem contact op met de beheerder van de site die u probeert te openen. |
| `AADB2C99005` | De aanvraag bevat een ongeldige scope{0}parameter die een illegaal karakter bevat ' '. |
| `AADB2C99006` | Azure AD B2C kan de extensies-app met app-id{0}niet vinden. Bezoek https://go.microsoft.com/fwlink/?linkid=851224 voor meer informatie. |
| `AADB2C99011` | De metagegevenswaarde '{0}' is{1}niet gespecificeerd{2}in TechnicalProfile ' ' in beleid '. |
| `AADB2C99013` | De meegeleverde{0}grant_type [{1}] en token_type [ ] combinatie wordt niet ondersteund. |
| `AADB2C99015` | Profiel{0}' in{1}beleid '{2}' in tenant ' ' ontbreekt alle InputClaims die nodig zijn voor de referentiestroom van het wachtwoord voor resources. |
