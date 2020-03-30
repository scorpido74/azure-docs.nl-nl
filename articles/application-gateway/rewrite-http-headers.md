---
title: HTTP-headers opnieuw schrijven met Azure Application Gateway | Microsoft Documenten
description: In dit artikel vindt u een overzicht van het herschrijven van HTTP-headers in Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 08/08/2019
ms.author: absha
ms.openlocfilehash: d0b28770940f0e1adeec16aa89cd087299bd4abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133001"
---
# <a name="rewrite-http-headers-with-application-gateway"></a>HTTP-headers opnieuw schrijven met Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Met HTTP-headers kunnen een client en server aanvullende informatie doorgeven met een verzoek of antwoord. Door deze kopteksten te herschrijven, u belangrijke taken uitvoeren, zoals het toevoegen van beveiligingsgerelateerde koptekstvelden zoals HSTS/ X-XSS-Protection, het verwijderen van antwoordkopvelden die gevoelige informatie kunnen onthullen en het verwijderen van poortinformatie uit X-Forwarded-For-headers.

In Application Gateway kunt u headers van HTTP-aanvragen en -antwoorden toevoegen, verwijderen of bijwerken terwijl aanvraag- en antwoordpakketten zich verplaatsen tussen de client en back-endpools. En u kunt voorwaarden toevoegen om ervoor te zorgen dat de opgegeven headers alleen worden herschreven wanneer is voldaan aan bepaalde voorwaarden.

Application Gateway ondersteunt ook verschillende [servervariabelen](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers#server-variables) waarmee u aanvullende informatie over aanvragen en antwoorden opslaan. Dit maakt het makkelijker voor u om krachtige herschrijfregels te maken.

> [!NOTE]
>
> De http-header herschrijven ondersteuning is alleen beschikbaar voor de [Standard_V2 en WAF_v2 SKU](application-gateway-autoscaling-zone-redundant.md).

![Kopteksten herschrijven](media/rewrite-http-headers/rewrite-headers.png)

## <a name="supported-headers"></a>Ondersteunde kopteksten

U alle kopteksten in aanvragen en antwoorden opnieuw schrijven, behalve de koppen Host, Connection en Upgrade. U de toepassingsgateway ook gebruiken om aangepaste kopteksten te maken en deze toe te voegen aan de aanvragen en antwoorden die erdoorheen worden gerouteerd.

## <a name="rewrite-conditions"></a>Voorwaarden herschrijven

U herschrijfvoorwaarden gebruiken om de inhoud van HTTP(S)-aanvragen en -antwoorden te evalueren en een header alleen opnieuw te schrijven wanneer aan een of meer voorwaarden is voldaan. De toepassingsgateway gebruikt dit soort variabelen om de inhoud van HTTP(S)-aanvragen en -antwoorden te evalueren:

- HTTP-headers in de aanvraag.
- HTTP-headers in het antwoord.
- Toepassingsgatewayservervariabelen.

U een voorwaarde gebruiken om te beoordelen of een bepaalde variabele aanwezig is, of een opgegeven variabele overeenkomt met een specifieke waarde of dat een bepaalde variabele overeenkomt met een specifiek patroon. U gebruikt de [PCRE-bibliotheek (Perl Compatible Regular Expressions)](https://www.pcre.org/) om de aanpassing van het reguliere expressiepatroon in de voorwaarden in te stellen. Zie de [hoofdpagina voor reguliere expressies](https://perldoc.perl.org/perlre.html)voor meer informatie over de syntaxis van reguliere expressies .

## <a name="rewrite-actions"></a>Acties herschrijven

U gebruikt herschrijfacties om de aanvraag- en antwoordkoppen op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U een nieuwe koptekst maken, de waarde van een bestaande koptekst wijzigen of een bestaande koptekst verwijderen. De waarde van een nieuwe koptekst of een bestaande koptekst kan worden ingesteld op dit soort waarden:

- Tekst.
- Koptekst aanvragen. Als u een aanvraagkop wilt opgeven, moet u de syntaxis {http_req_*headerName*}.
- Antwoordheader. Als u een antwoordkop wilt opgeven, moet u de syntaxis {http_resp_*headerName*}.
- Servervariabele. Als u een servervariabele wilt opgeven, moet u de syntaxis {var_*serverVariabele*}.
- Een combinatie van tekst, een aanvraagkoptekst, een antwoordkop en een servervariabele.

## <a name="server-variables"></a>Servervariabelen

Application Gateway gebruikt servervariabelen om nuttige informatie over de server, de verbinding met de client en de huidige aanvraag op de verbinding op te slaan. Voorbeelden van opgeslagen informatie zijn het IP-adres van de client en het type webbrowser. Servervariabelen veranderen dynamisch, bijvoorbeeld wanneer een nieuwe pagina wordt geladen of wanneer een formulier wordt geplaatst. U deze variabelen gebruiken om de herschrijfvoorwaarden te evalueren en kopteksten te herschrijven.

Application gateway ondersteunt deze servervariabelen:

| Naam van de variabele | Beschrijving                                                  |
| -------------------------- | :----------------------------------------------------------- |
| add_x_forwarded_for_proxy  | Het headerveld X-Forwarded-For client `client_ip` request met de variabele (zie uitleg later in deze tabel) is toegevoegd in het formaat IP1, IP2, IP3, enzovoort. Als het veld X-Forwarded-For zich niet in `add_x_forwarded_for_proxy` de header clientaanvraag bevindt, is de variabele gelijk aan de `$client_ip` variabele. Deze variabele is vooral handig wanneer u de X-Forwarded-For-header die is ingesteld door Application Gateway wilt herschrijven, zodat de koptekst alleen het IP-adres zonder de poortgegevens bevat. |
| ciphers_supported          | Een lijst van de cijfers ondersteund door de klant.          |
| ciphers_used               | De reeks cijfers die worden gebruikt voor een gevestigde TLS-verbinding. |
| client_ip                  | Het IP-adres van de client van waaruit de toepassingsgateway het verzoek heeft ontvangen. Als er een omgekeerde proxy is voor de toepassingsgateway en de oorspronkelijke client, geeft *client_ip* het IP-adres van de omgekeerde proxy terug. |
| client_port                | De clientpoort.                                                  |
| client_tcp_rtt             | Informatie over de TCP-clientverbinding. Beschikbaar op systemen die de TCP_INFO socket optie ondersteunen. |
| client_user                | Wanneer HTTP-verificatie wordt gebruikt, wordt de gebruikersnaam geleverd voor verificatie. |
| host                       | In deze rangorde: de hostnaam van de aanvraagregel, de hostnaam van het headerveld Hostrequest of de servernaam die overeenkomt met een aanvraag. |
| cookie_*naam*              | De *naam* cookie.                                            |
| http_method                | De methode die wordt gebruikt om de URL-aanvraag te maken. Bijvoorbeeld, GET of POST. |
| http_status                | De sessiestatus. Bijvoorbeeld 200, 400 of 403.                       |
| http_version               | Het aanvraagprotocol. Meestal HTTP/1.0, HTTP/1.1 of HTTP/2.0. |
| query_string               | De lijst met variabele/waardeparen die de "?" in de gevraagde URL volgt. |
| received_bytes             | De lengte van de aanvraag (inclusief de aanvraagregel, koptekst en aanvraaginstantie). |
| request_query              | De argumenten in de aanvraagregel.                                |
| request_scheme             | Het aanvraagschema: http of https.                            |
| request_uri                | De volledige oorspronkelijke aanvraag URI (met argumenten).                   |
| sent_bytes                 | Het aantal bytes dat naar een client wordt verzonden.                             |
| server_port                | De poort van de server die een verzoek heeft geaccepteerd.                 |
| ssl_connection_protocol    | Het protocol van een gevestigde TLS-verbinding.        |
| ssl_enabled                | "Aan" als de verbinding werkt in de TLS-modus. Anders een lege string. |

## <a name="rewrite-configuration"></a>Configuratie opnieuw schrijven

Als u http-headerherschrijven wilt configureren, moet u deze stappen uitvoeren.

1. Maak de objecten die nodig zijn voor het herschrijven van HTTP-koppen:

   - **Actie herschrijven:** wordt gebruikt om de koptekstvelden voor aanvragen en aanvragen op te geven die u wilt herschrijven en de nieuwe waarde voor de kopteksten. U een of meer herschrijfvoorwaarden koppelen aan een herschrijfactie.

   - **Voorwaarde herschrijven**: Een optionele configuratie. Voorwaarden herschrijven evalueerde de inhoud van de HTTP(S)-aanvragen en -antwoorden. De herschrijfactie vindt plaats als het HTTP(S)-verzoek of -antwoord overeenkomt met de herschrijfvoorwaarde.

     Als u meer dan één voorwaarde aan een actie koppelt, vindt de actie alleen plaats wanneer aan alle voorwaarden is voldaan. Met andere woorden, de bewerking is een logische EN operatie.

   - **Regel herschrijven**: Bevat meerdere herschrijfactie / herschrijven van conditiecombinaties.

   - **Regelvolgorde:** hiermee u bepalen in welke volgorde de herschrijfregels worden uitgevoerd. Deze configuratie is handig wanneer u meerdere herschrijfregels in een herschrijfset hebt. Een herschrijfregel met een lagere regelvolgordewaarde wordt eerst uitgevoerd. Als u dezelfde regelreeks toewijst aan twee herschrijfregels, is de uitvoeringsvolgorde niet-deterministisch.

   - **Set herschrijven:** bevat meerdere herschrijfregels die worden gekoppeld aan een regel voor het routeren van aanvragen.

2. Voeg de herschrijfset *(herschrijven RuleSet)* toe aan een routeringsregel. De herschrijfconfiguratie is via de routeringsregel gekoppeld aan de bronlistener. Wanneer u een basisrouteringsregel gebruikt, wordt de configuratie van de koptekst herschrijven gekoppeld aan een bronlistener en is een globale koptekst herschrijven. Wanneer u een routeringsregel op basis van paden gebruikt, wordt de configuratie van de koptekst herschrijven gedefinieerd op de URL-padkaart. In dat geval is het alleen van toepassing op het specifieke padgebied van een site.
   > [!NOTE]
   > URL De kopteksten opnieuw schrijven; het verandert de URL voor het pad niet.

U meerdere HTTP-koptekstsets opnieuw schrijven en elke herschrijfset toepassen op meerdere listeners. U echter slechts één herschrijvenset toepassen op een specifieke listener.

## <a name="common-scenarios"></a>Algemene scenario's

Hier volgen enkele veelvoorkomende scenario's voor het herschrijven van kopteksten.

### <a name="remove-port-information-from-the-x-forwarded-for-header"></a>Poortgegevens verwijderen uit de koptekst X-Forwarded-For

Application Gateway voegt een X-Forwarded-For-header in alle aanvragen in voordat de aanvragen naar de backend worden doorgestuurd. Deze header is een door komma's gescheiden lijst met IP-poorten. Er kunnen scenario's zijn waarin de back-endservers alleen de headers nodig hebben om IP-adressen te bevatten. U header herschrijven gebruiken om de poortgegevens te verwijderen uit de koptekst X-Forwarded-For. Een manier om dit te doen is om de header in te stellen op de add_x_forwarded_for_proxy server variabele:

![Poort verwijderen](media/rewrite-http-headers/remove-port.png)

### <a name="modify-a-redirection-url"></a>Een omleidings-URL wijzigen

Wanneer een back-endtoepassing een omleidingsreactie verzendt, u de client omleiden naar een andere URL dan die welke is opgegeven door de back-endtoepassing. U dit bijvoorbeeld doen wanneer een app-service achter een toepassingsgateway wordt gehost en de client een omleiding naar het relatieve pad moet doen. (Bijvoorbeeld een omleiding van contoso.azurewebsites.net/path1 naar contoso.azurewebsites.net/path2.)

Omdat App Service een multitenant-service is, wordt de hostkopin het verzoek gebruikt om het verzoek naar het juiste eindpunt te leiden. App-services hebben een standaarddomeinnaam van *.azurewebsites.net (bijvoorbeeld contoso.azurewebsites.net) die verschilt van de domeinnaam van de toepassingsgateway (zeg contoso.com). Omdat de oorspronkelijke aanvraag van de client de domeinnaam van de toepassingsgateway (contoso.com) als hostnaam heeft, wijzigt de toepassingsgateway de hostnaam in contoso.azurewebsites.net. Het maakt deze wijziging, zodat de app-service het verzoek kan routeren naar het juiste eindpunt.

Wanneer de app-service een omleidingsreactie verzendt, gebruikt deze dezelfde hostnaam in de locatiekop van het antwoord als die in het verzoek dat wordt ontvangen van de toepassingsgateway. Dus de client zal het verzoek rechtstreeks aan contoso.azurewebsites.net/path2 in plaats van te gaan via de applicatie gateway (contoso.com/path2). Het omzeilen van de toepassingsgateway is niet wenselijk.

U dit probleem oplossen door de hostnaam in de locatiekoptekst in te stellen op de domeinnaam van de toepassingsgateway.

Dit zijn de stappen om de hostnaam te vervangen:

1. Maak een herschrijfregel met een voorwaarde die evalueert of de locatiekoptekst in het antwoord azurewebsites.net bevat. Voer het `(https?):\/\/.*azurewebsites\.net(.*)$`patroon in .
1. Voer een actie uit om de locatiekoptekst te herschrijven, zodat de hostnaam van de toepassingsgateway is. Doe dit `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` door in te voeren als de kopwaarde.

![Locatiekoptekst wijzigen](media/rewrite-http-headers/app-service-redirection.png)

### <a name="implement-security-http-headers-to-prevent-vulnerabilities"></a>Implementeren van HTTP-headers voor beveiliging om kwetsbaarheden te voorkomen

U verschillende beveiligingsproblemen oplossen door de benodigde headers in de reactie van de toepassing te implementeren. Deze beveiligingsheaders omvatten X-XSS-Protection, Strict-Transport-Security en Content-Security-Policy. U Application Gateway gebruiken om deze kopteksten in te stellen voor alle reacties.

![Beveiligingskoptekst](media/rewrite-http-headers/security-header.png)

### <a name="delete-unwanted-headers"></a>Ongewenste kopteksten verwijderen

U kopteksten verwijderen die gevoelige informatie uit een HTTP-antwoord weergeven. U bijvoorbeeld informatie verwijderen, zoals de naam van de back-endserver, het besturingssysteem of de bibliotheekdetails. U de toepassingsgateway gebruiken om deze kopteksten te verwijderen:

![Koptekst verwijderen](media/rewrite-http-headers/remove-headers.png)

### <a name="check-for-the-presence-of-a-header"></a>Controleren op de aanwezigheid van een koptekst

U een HTTP-aanvraag- of antwoordkop evalueren op de aanwezigheid van een kop- of servervariabele. Deze evaluatie is handig wanneer u een koptekst alleen wilt herschrijven wanneer een bepaalde koptekst aanwezig is.

![De aanwezigheid van een koptekst controleren](media/rewrite-http-headers/check-presence.png)

## <a name="limitations"></a>Beperkingen

- Als een antwoord meer dan één koptekst met dezelfde naam heeft, zal het herschrijven van de waarde van een van die kopteksten resulteren in het laten vallen van de andere kopteksten in het antwoord. Dit kan meestal gebeuren met set-cookie header, omdat je meer dan een Set-Cookie header in een reactie. Een dergelijk scenario is wanneer u een app-service met een toepassingsgateway gebruikt en sessieaffiniteit op basis van cookies hebt geconfigureerd op de toepassingsgateway. In dit geval bevat het antwoord twee Set-Cookie-headers: een `Set-Cookie: ARRAffinity=ba127f1caf6ac822b2347cc18bba0364d699ca1ad44d20e0ec01ea80cda2a735;Path=/;HttpOnly;Domain=sitename.azurewebsites.net` die wordt gebruikt door de `Set-Cookie: ApplicationGatewayAffinity=c1a2bd51lfd396387f96bl9cc3d2c516; Path=/`app-service, bijvoorbeeld: en een andere voor affiniteit met toepassingsgateway, bijvoorbeeld . Als u in dit scenario een van de set-cookie-headers herschrijft, kan dit ertoe leiden dat de andere set-cookie-header uit het antwoord wordt verwijderd.

- Het herschrijven van de kopteksten Verbinding, Upgrade en Host wordt momenteel niet ondersteund.

- Koptekstnamen kunnen alfanumerieke tekens en specifieke symbolen bevatten zoals gedefinieerd in [RFC 7230](https://tools.ietf.org/html/rfc7230#page-27). We ondersteunen momenteel niet de\_underscore ( ) speciaal karakter in Header namen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het herschrijven van HTTP-headers:

- [HTTP-headers opnieuw schrijven met Azure-portal](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers-portal)
- [HTTP-headers opnieuw schrijven met Azure PowerShell](add-http-header-rewrite-rule-powershell.md)
