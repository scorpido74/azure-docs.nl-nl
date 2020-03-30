---
title: Groottelimieten voor webtoepassingen aanvragen en uitsluitingslijsten in Azure Application Gateway - Azure-portal
description: In dit artikel vindt u informatie over de groottelimieten van webtoepassingsfirewallaanvragen en worden uitsluitingslijsten geconfigureerd in Application Gateway met de Azure-portal.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 02/20/2020
ms.author: victorh
ms.topic: conceptual
ms.openlocfilehash: 7244788bbc7431c7f26363b2852babb72d5697e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77526787"
---
# <a name="web-application-firewall-request-size-limits-and-exclusion-lists"></a>Groottelimieten en uitsluitingslijsten voor Web Application Firewall-aanvragen

De Azure Application Gateway Web Application Firewall (WAF) biedt bescherming voor webtoepassingen. In dit artikel worden waf-aanvraaggroottelimieten en uitsluitingslijsten beschreven. Deze instellingen bevinden zich in het WAF-beleid dat is gekoppeld aan uw toepassingsgateway. Zie [Azure Web Application Firewall op Azure Application Gateway](ag-overview.md) en Web Application [Firewall-beleid voor Application Gateway voor](create-waf-policy-ag.md) meer informatie over WAF-beleid voor meer informatie over WAF-beleid

## <a name="waf-exclusion-lists"></a>WAF-uitsluitingslijsten

![Limieten voor de grootte aanvragen](../media/application-gateway-waf-configuration/waf-policy.png)

Met waf-uitsluitingslijsten u bepaalde aanvraagkenmerken weglaten uit een WAF-evaluatie. Een veelvoorkomend voorbeeld is Active Directory-ingevoegde tokens die worden gebruikt voor verificatie- of wachtwoordvelden. Dergelijke kenmerken zijn gevoelig voor speciale tekens die een vals-positief van de WAF-regels kunnen activeren. Zodra een kenmerk is toegevoegd aan de WAF-uitsluitingslijst, wordt het niet beschouwd als een geconfigureerde en actieve WAF-regel. Uitsluitingslijsten zijn globaal van opzet.

De volgende kenmerken kunnen op naam aan uitsluitingslijsten worden toegevoegd. De waarden van het gekozen veld worden niet beoordeeld aan de hand van waf-regels, maar hun namen wel (zie voorbeeld 1 hieronder, de waarde van de header User-Agent is uitgesloten van WAF-evaluatie). De uitsluitingslijsten verwijderen de inspectie van de waarde van het veld.

* Aanvraagheaders
* Cookies aanvragen
* De naam van het kenmerk Aanvragen (args) kan worden toegevoegd als uitsluitingselement, zoals:

   * Naam van formulierveld
   * XML-entiteit
   * JSON-entiteit
   * ARGSvan URL-querytekenreeksen

U een exacte aanvraagkop-, hoofdtekst-, cookie- of querytekenreekskenmerkovereenkomst opgeven.  U ook optioneel gedeeltelijke overeenkomsten opgeven. Uitsluitingsregels zijn globaal van opzet en zijn van toepassing op alle pagina's en alle regels.

Hieronder volgen de operatoren voor ondersteunde wedstrijdcriteria:

- **Gelijk aan**: Deze operator wordt gebruikt voor een exacte overeenkomst. Als voorbeeld, voor het selecteren van een koptekst met de naam **bearerToken,** gebruikt u de gelijken operator met de selector set als **bearerToken**.
- **Begin met**: Deze operator komt overeen met alle velden die beginnen met de opgegeven selectorwaarde.
- **Eindigt met**: Deze operator komt overeen met alle aanvraagvelden die eindigen met de opgegeven selectorwaarde.
- **Bevat:** deze operator komt overeen met alle aanvraagvelden die de opgegeven selectorwaarde bevatten.
- **Is gelijk aan elk**: Deze operator komt overeen met alle aanvraagvelden. * zal de selector waarde.

In alle gevallen is matching geval ongevoelig en reguliere expressie zijn niet toegestaan als selectors.

> [!NOTE]
> Zie [WAF-probleemoplossing](web-application-firewall-troubleshoot.md)voor meer informatie en hulp bij het oplossen van problemen met problemen.

### <a name="examples"></a>Voorbeelden

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

De volgende voorbeelden tonen het gebruik van uitsluitingen aan.

#### <a name="example-1"></a>Voorbeeld 1

In dit voorbeeld wilt u de header van de gebruikersagent uitsluiten. De user-agent request header bevat een karakteristieke tekenreeks waarmee de peers van het netwerkprotocol het toepassingstype, het besturingssysteem, de softwareleverancier of de softwareversie van de gebruikersagent van de aanvragende software kunnen identificeren. Zie [User-Agent voor](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/User-Agent)meer informatie.

Er kunnen verschillende redenen zijn om de evaluatie van deze header uit te schakelen. Er kan een string die de WAF ziet en gaat ervan uit dat het kwaadaardig. Bijvoorbeeld de klassieke SQL-aanval "x=x" in een tekenreeks. In sommige gevallen kan dit legitiem verkeer zijn. Het kan dus nodig zijn om deze header uit te sluiten van WAF evaluatie.

Met de volgende Azure PowerShell-cmdlet wordt de header van de gebruikersagent uitgesloten van evaluatie:

```azurepowershell
$exclusion1 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestHeaderNames" `
   -SelectorMatchOperator "Equals" `
   -Selector "User-Agent"
```
#### <a name="example-2"></a>Voorbeeld 2

In dit voorbeeld wordt de waarde in de *gebruikersparameter* die in de aanvraag wordt doorgegeven via de URL, uitgesloten. Stel dat het in uw omgeving gebruikelijk is dat het gebruikersveld een tekenreeks bevat die de WAF als schadelijke inhoud weergeeft, zodat deze wordt geblokkeerd.  U de gebruikersparameter in dit geval uitsluiten, zodat de WAF niets in het veld evalueert.

Met de volgende Azure PowerShell-cmdlet wordt de gebruikersparameter uitgesloten van evaluatie:

```azurepowershell
$exclusion2 = New-AzApplicationGatewayFirewallExclusionConfig `
   -MatchVariable "RequestArgNames" `
   -SelectorMatchOperator "StartsWith" `
   -Selector "user"
```
Dus als `http://www.contoso.com/?user%281%29=fdafdasfda` de URL wordt doorgegeven aan de WAF, zal het niet evalueren van de string **fdafdasfda**, maar het zal nog steeds evalueren van de parameter naam **gebruiker%281%29**. 

## <a name="waf-request-size-limits"></a>WAF-aanvraaggroottelimieten



Met Web Application Firewall u limieten voor aanvraaggrootte smaken binnen de onder- en bovengrenzen. De volgende configuraties met twee groottelimieten zijn beschikbaar:

- Het veld maximale doelgrootte wordt opgegeven in kilobytes en regelt de totale limiet voor de aanvraaggrootte, exclusief eventuele bestandsuploads. Dit veld kan variëren van minimaal 1-KB tot 128 KB maximale waarde. De standaardwaarde voor de grootte van de hoofdtekst van aanvragen is 128 KB.
- Het veld limiet voor het uploaden van bestanden is opgegeven in MB en regelt de maximaal toegestane bestandsuploadgrootte. Dit veld kan een minimumwaarde van 1 MB en de volgende maxima hebben:

   - 100 MB voor v1 Medium WAF-gateways
   - 500 MB voor v1 Grote WAF-gateways
   - 750 MB voor v2 WAF-gateways 

 De standaardwaarde voor de uploadlimiet van bestanden is 100 MB.

WAF biedt ook een configureerbare knop om de aanvraag body inspectie in of uit te schakelen. Standaard is de inspectie van de aanvraaginstantie ingeschakeld. Als de inspectie van de aanvraaginstantie is uitgeschakeld, evalueert WAF de inhoud van de HTTP-berichtenbody niet. In dergelijke gevallen blijft WAF de WAF-regels afdwingen voor headers, cookies en URI. Als de inspectie van de aanvraaginstantie is uitgeschakeld, is het veld maximale aanvraaginstantiegrootte niet van toepassing en kan deze niet worden ingesteld. Het uitschakelen van de aanvraag lichaam inspectie maakt het mogelijk voor berichten groter dan 128 KB worden verzonden naar WAF, maar het bericht lichaam is niet geïnspecteerd op kwetsbaarheden.

## <a name="next-steps"></a>Volgende stappen

Nadat u uw WAF-instellingen hebt geconfigureerd, u leren hoe u uw WAF-logboeken bekijken. Zie [Application Gateway diagnostics](../../application-gateway/application-gateway-diagnostics.md#diagnostic-logging)voor meer informatie.
