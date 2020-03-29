---
title: 'Azure AD Connect: declaratieve inrichtingsexpressies | Microsoft Documenten'
description: Legt de declaratieve inrichtingsuitdrukkingen uit.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: e3ea53c8-3801-4acf-a297-0fb9bb1bf11d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdc7c9dba49bf37db1f039d43b0450c65884c74b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60245508"
---
# <a name="azure-ad-connect-sync-understanding-declarative-provisioning-expressions"></a>Azure AD Connect-synchronisatie: declaratieve inrichtingsexpressies begrijpen
Azure AD Connect-synchronisatie bouwt voort op declaratieve inrichting die voor het eerst is geïntroduceerd in Forefront Identity Manager 2010. Hiermee u uw volledige bedrijfslogica voor identiteitsintegratie implementeren zonder dat u gecompileerde code hoeft te schrijven.

Een essentieel onderdeel van declaratieve inrichting is de expressietaal die wordt gebruikt in kenmerkstromen. De gebruikte taal is een subset van Microsoft® Visual Basic® for Applications (VBA). Deze taal wordt gebruikt in Microsoft Office en gebruikers met ervaring met VBScript zullen deze ook herkennen. De Declaratieve Inrichtingexpressietaal gebruikt alleen functies en is geen gestructureerde taal. Er zijn geen methoden of verklaringen. Functies worden in plaats daarvan genest om de programmastroom uit te drukken.

Zie [Welkom bij de taalverwijzing voor Visual Basic for Applications voor Office 2013 voor](https://msdn.microsoft.com/library/gg264383.aspx)meer informatie.

De attributen zijn sterk getypt. Een functie accepteert alleen kenmerken van het juiste type. Het is ook hoofdlettergevoelig. Zowel functienamen als kenmerknamen moeten een goede behuizing hebben of er wordt een fout gegenereerd.

## <a name="language-definitions-and-identifiers"></a>Taaldefinities en id's
* Functies hebben een naam, gevolgd door argumenten tussen haakjes: FunctieNaam(argument 1, argument N).
* Kenmerken worden geïdentificeerd aan de basis van vierkante haakjes: [attributeName]
* Parameters worden geïdentificeerd door procentuele tekens: %ParameterName%
* String constanten worden omringd door aanhalingstekens: Bijvoorbeeld "Contoso" (Opmerking: moet gebruik maken van rechte aanhalingstekens "" en niet slimme citaten "")
* Numerieke waarden worden uitgedrukt zonder aanhalingstekens en zullen naar verwachting decimaal zijn. Hexadecimale waarden zijn vooraf vastgesteld met &H. Bijvoorbeeld 98052, &HFF
* Booleaanse waarden worden uitgedrukt met constanten: Waar, Onwaar.
* Ingebouwde constanten en literals worden uitgedrukt met alleen hun naam: NULL, CRLF, IgnoreThisFlow

### <a name="functions"></a>Functions
Declaratieve inrichting maakt gebruik van vele functies om de mogelijkheid mogelijk te maken om kenmerkwaarden te transformeren. Deze functies kunnen worden genest, zodat het resultaat van de ene functie wordt doorgegeven aan een andere functie.

`Function1(Function2(Function3()))`

De volledige lijst met functies is te vinden in de [functiereferentie.](reference-connect-sync-functions-reference.md)

### <a name="parameters"></a>Parameters
Een parameter wordt gedefinieerd door een connector of door een beheerder die PowerShell gebruikt. Parameters bevatten meestal waarden die verschillen van systeem tot systeem, bijvoorbeeld de naam van het domein waarin de gebruiker zich bevindt. Deze parameters kunnen worden gebruikt in kenmerkstromen.

De Active Directory Connector heeft de volgende parameters voor binnenkomende synchronisatieregels opgegeven:

| Parameternaam | Opmerking |
| --- | --- |
| Domain.Netbios |Netbios-formaat van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld FABRIKAMSALES |
| Domain.FQDN |FQDN-indeling van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld sales.fabrikam.com |
| Domain.LDAP (Domain.LDAP) |LDAP-indeling van het domein dat momenteel wordt geïmporteerd, bijvoorbeeld DC=sales,DC=fabrikam,DC=com |
| Forest.Netbios |Netbios-formaat van de bosnaam die momenteel wordt ingevoerd, bijvoorbeeld FABRIKAMCORP |
| Forest.FQDN |FQDN-indeling van de forestnaam die momenteel wordt geïmporteerd, bijvoorbeeld fabrikam.com |
| Forest.LDAP |LDAP-indeling van de forestnaam die momenteel wordt geïmporteerd, bijvoorbeeld DC=fabrikam,DC=com |

Het systeem biedt de volgende parameter, die wordt gebruikt om de id van de connector die momenteel wordt uitgevoerd, op te laten draaien:  
`Connector.ID`

Hier volgt een voorbeeld dat het metaverse kenmerkdomein vult met de netbios-naam van het domein waar de gebruiker zich bevindt:  
`domain` <- `%Domain.Netbios%`

### <a name="operators"></a>Operators
De volgende operatoren kunnen worden gebruikt:

* **Vergelijking**: <, <=, <>, =, >, >=
* **Wiskunde**: +, \*-, , -
* **Tekenreeks**: & (concatenate)
* **Logisch**: && (en), || (of)
* **Evaluatievolgorde**: ( )

Operators worden van links tot rechts geëvalueerd en hebben dezelfde evaluatieprioriteit. Dat wil \* zeggen, de (multiplier) wordt niet eerder geëvalueerd - (aftrekken). 2\*(5+3) is niet\*hetzelfde als 2 5+3. De haakjes ( ) worden gebruikt om de evaluatievolgorde te wijzigen wanneer de evaluatievolgorde van links naar rechts niet geschikt is.

## <a name="multi-valued-attributes"></a>Kenmerken met meerdere waarden
De functies kunnen werken op zowel enkelgewaardeerde als multigewaardeerde kenmerken. Voor kenmerken met meerdere waarden werkt de functie over elke waarde en past dezelfde functie toe op elke waarde.

Bijvoorbeeld:  
`Trim([proxyAddresses])`Doe een trim van elke waarde in het kenmerk proxyAddress.  
`Word([proxyAddresses],1,"@") & "@contoso.com"`Voor elke waarde @-signmet een @contoso.com, vervang het domein door .  
`IIF(InStr([proxyAddresses],"SIP:")=1,NULL,[proxyAddresses])`Zoek naar het SIP-adres en verwijder het uit de waarden.

## <a name="next-steps"></a>Volgende stappen
* Lees meer over het configuratiemodel in [Declaratieve provisioning begrijpen.](concept-azure-ad-connect-sync-declarative-provisioning.md)
* Bekijk hoe declaratieve inrichting out-of-box wordt gebruikt in Het begrijpen van [de standaardconfiguratie.](concept-azure-ad-connect-sync-default-configuration.md)
* Zie hoe u een praktische wijziging aanbrengen met declaratieve inrichting in [Hoe u een wijziging in de standaardconfiguratie aanbrengen.](how-to-connect-sync-change-the-configuration.md)

**Overzichtsonderwerpen**

* [Synchronisatie van Azure AD Connect: synchronisatie begrijpen en aanpassen](how-to-connect-sync-whatis.md)
* [Integrating your on-premises identities with Azure Active Directory (Engelstalig)](whatis-hybrid-identity.md)

**Onderwerpen met naslaginformatie**

* [Synchronisatie van Azure AD Connect: naslaginformatie over functies](reference-connect-sync-functions-reference.md)

