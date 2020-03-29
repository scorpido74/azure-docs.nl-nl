---
title: Syntaxisverwijzing SQLRuleAction in Azure Service Bus
description: In dit artikel vindt u een verwijzing naar de syntaxis van SQLRuleAction. De acties zijn geschreven in SQL-taal gebaseerde syntaxis die wordt uitgevoerd tegen een brokered bericht.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 37615e39577ef60cccc9df91b61a6aa24ca794d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759625"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>SQLRuleAction-syntaxisverwijzing voor Azure Service Bus

Een *SqlRuleAction* is een instantie van de klasse [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) en vertegenwoordigt een reeks acties die zijn geschreven in SQL-taalgebaseerde syntaxis die wordt uitgevoerd tegen een [BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)   
  
In dit artikel vindt u details over de grammatica van de SQL-regelactie.  
  
```  
<statements> ::=
    <statement> [, ...n]  
  
```  
  
```  
<statement> ::=
    <action> [;]
    Remarks
    -------
    Semicolon is optional.  
  
```  
  
```  
<action> ::=
    SET <property> = <expression>
    REMOVE <property>  
```

```
<expression> ::=
    <constant>
    | <function>
    | <property>
    | <expression> { + | - | * | / | % } <expression>
    | { + | - } <expression>
    | ( <expression> )
``` 

```
<property> := 
    [<scope> .] <property_name>
``` 
  
## <a name="arguments"></a>Argumenten  
  
-   `<scope>`is een optionele tekenreeks die `<property_name>`het bereik van de . Geldige waarden `sys` `user`zijn of . De `sys` waarde geeft `<property_name>` het systeembereik aan waar een openbare eigendomsnaam van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)is. `user`geeft het `<property_name>` gebruikersbereik aan waar een sleutel van het woordenboek Van de [BrokeredMessage-klasse](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) is. `user`bereik is het `<scope>` standaardbereik als deze niet is opgegeven.  
  
### <a name="remarks"></a>Opmerkingen  

Een poging om toegang te krijgen tot een niet-bestaande systeemeigenschap is een fout, terwijl een poging om toegang te krijgen tot een niet-bestaande eigenschap van de gebruiker geen fout is. In plaats daarvan wordt een niet-bestaande gebruikerseigenschap intern geëvalueerd als een onbekende waarde. Een onbekende waarde wordt speciaal behandeld tijdens de evaluatie van de operator.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenten  
 `<regular_identifier>`is een tekenreeks die wordt weergegeven door de volgende reguliere expressie:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Dit betekent dat elke tekenreeks die begint met een letter en wordt gevolgd door een of meer underscore / letter / cijfer.  
  
 `[:IsLetter:]`betekent elk Unicode-teken dat is gecategoriseerd als een Unicode-letter. `System.Char.IsLetter(c)`retourneert `true` als `c` het een Unicode-brief is.  
  
 `[:IsDigit:]`betekent elk Unicode-teken dat is gecategoriseerd als een cijfer achter de komma. `System.Char.IsDigit(c)`retourneert `true` als `c` een Unicode-cijfer is.  
  
 A `<regular_identifier>` kan geen gereserveerd trefwoord zijn.  
  
 `<delimited_identifier>`is een tekenreeks die is omsloten met linker/rechter vierkante haakjes ([]). Een rechter vierkante beugel wordt weergegeven als twee rechter vierkante haakjes. Hieronder volgen voorbeelden `<delimited_identifier>`van :  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`is een tekenreeks die is ingesloten met dubbele aanhalingstekens. Een dubbel aanhalingsteken in id wordt weergegeven als twee dubbele aanhalingstekens. Het wordt afgeraden om geciteerde id's te gebruiken, omdat deze gemakkelijk kunnen worden verward met een tekenreeksconstante. Gebruik indien mogelijk een afgebakende id. Het volgende is `<quoted_identifier>`een voorbeeld van:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>Patroon  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<pattern>`moet een expressie zijn die als tekenreeks wordt geëvalueerd. Het wordt gebruikt als een patroon voor de LIKE operator.      Het kan de volgende jokertekens bevatten:  
  
-   `%`: Elke reeks van nul of meer tekens.  
  
-   `_`: Elk teken.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<escape_char>`moet een expressie zijn die wordt geëvalueerd als een tekenreeks van lengte 1. Het wordt gebruikt als een escape karakter voor de LIKE operator.  
  
 Bijvoorbeeld overeenkomsten `property LIKE 'ABC\%' ESCAPE '\'` `ABC%` in plaats van een `ABC`tekenreeks die begint met .  
  
## <a name="constant"></a>Constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenten  
  
-   `<integer_constant>`is een tekenreeks van getallen die niet zijn ingesloten tussen aanhalingstekens en geen decimalen bevatten. De waarden worden `System.Int64` als intern opgeslagen en volgen hetzelfde bereik.  
  
     Hieronder volgen voorbeelden van lange constanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`is een tekenreeks van getallen die niet zijn ingesloten tussen aanhalingstekens en een decimaal punt bevatten. De waarden worden `System.Double` opgeslagen als intern en volgen hetzelfde bereik/precisie.  
  
     In een toekomstige versie kan dit nummer worden opgeslagen in een ander gegevenstype om exacte getalsemantiek `System.Double` te `<decimal_constant>`ondersteunen, dus u moet niet vertrouwen op het feit dat het onderliggende gegevenstype voor .  
  
     Hieronder volgen voorbeelden van decimale constanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`is een getal geschreven in wetenschappelijke notatie. De waarden worden `System.Double` opgeslagen als intern en volgen hetzelfde bereik/precisie. Hieronder volgen voorbeelden van geschatte aantalconstanten:  
  
    ```  
    101.5E5  
    0.5E-2  
    ```  
  
## <a name="boolean_constant"></a>boolean_constant  
  
```  
<boolean_constant> :=  
      TRUE | FALSE  
```  
  
### <a name="remarks"></a>Opmerkingen
  
Booleaanse constanten worden weergegeven `TRUE` door `FALSE`de trefwoorden of . De waarden worden `System.Boolean`opgeslagen als .  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
Tekenreeksconstanten zijn ingesloten in afzonderlijke aanhalingstekens en bevatten geldige Unicode-tekens. Een aanhalingsteken dat is ingesloten in een tekenreeksconstante, wordt weergegeven als twee aanhalingstekens.  
  
## <a name="function"></a>Functie  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Opmerkingen  

De `newid()` functie retourneert een **System.Guid** gegenereerd door de `System.Guid.NewGuid()` methode.  
  
De `property(name)` functie geeft de waarde van `name`de eigenschap waarnaar wordt verwezen door . De `name` waarde kan elke geldige expressie zijn die een tekenreekswaarde retourneert.  
  
## <a name="considerations"></a>Overwegingen

- SET wordt gebruikt om een nieuwe eigenschap te maken of de waarde van een bestaande eigenschap bij te werken.
- REMOVE wordt gebruikt om een eigenschap te verwijderen.
- SET voert indien mogelijk impliciete conversie uit wanneer het expressietype en het bestaande eigenschapstype verschillend zijn.
- Actie mislukt als er naar niet-bestaande systeemeigenschappen is verwezen.
- Actie mislukt niet als er naar niet-bestaande gebruikerseigenschappen is verwezen.
- Een niet-bestaande eigenschap van de gebruiker wordt intern geëvalueerd als 'Onbekend', volgens dezelfde semantiek als [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) bij het evalueren van operators.

## <a name="next-steps"></a>Volgende stappen

- [SQLRuleAction, klasse](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [SQLFilter, klasse](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
