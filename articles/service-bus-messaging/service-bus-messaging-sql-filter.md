---
title: Syntaxisverwijzing voor SQLFilter-syntaxis van Azure Service Bus | Microsoft Documenten
description: In dit artikel vindt u informatie over sqlfiltergrammatica. Een SqlFilter ondersteunt een subset van de SQL-92-standaard.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: spelluru
ms.openlocfilehash: d5a8e165fcee23c5feecd5935983dd77d3ec6c30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759660"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-syntaxis

Een *SqlFilter-object* is een instantie van de [klasse SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)en vertegenwoordigt een SQL-op taal gebaseerde filterexpressie die wordt geëvalueerd op basis van een [BrokeredMessage.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) Een SqlFilter ondersteunt een subset van de SQL-92-standaard.  
  
 In dit onderwerp vindt u details over sqlfiltergrammatica.  
  
```  
<predicate ::=  
      { NOT <predicate> }  
      | <predicate> AND <predicate>  
      | <predicate> OR <predicate>  
      | <expression> { = | <> | != | > | >= | < | <= } <expression>  
      | <property> IS [NOT] NULL  
      | <expression> [NOT] IN ( <expression> [, ...n] )  
      | <expression> [NOT] LIKE <pattern> [ESCAPE <escape_char>]  
      | EXISTS ( <property> )  
      | ( <predicate> )  
  
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
  
-   `<scope>`is een optionele tekenreeks die `<property_name>`het bereik van de . Geldige waarden `sys` `user`zijn of . De `sys` waarde geeft `<property_name>` het systeembereik aan waar een naam van openbare eigendom van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)is. `user`geeft het `<property_name>` gebruikersbereik aan waar een sleutel van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) is. `user`bereik is het `<scope>` standaardbereik als deze niet is opgegeven.  
  
## <a name="remarks"></a>Opmerkingen

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
  
Deze grammatica betekent elke tekenreeks die begint met een letter en wordt gevolgd door een of meer underscore /letter/digit.  
  
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
  
     Dit zijn voorbeelden van lange constanten:  
  
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

Booleaanse constanten worden weergegeven door de trefwoorden **WAAR** of **ONWAAR**. De waarden worden `System.Boolean`opgeslagen als .  
  
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
  
Overweeg de volgende [SqlFilter-semantiek:](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)  
  
-   Eigendomsnamen zijn hoofdletter-ongevoelig.  
  
-   Operators volgen c# impliciete conversie semantiek waar mogelijk.  
  
-   Systeemeigenschappen zijn openbare eigenschappen die worden weergegeven in [brokeredMessage-exemplaren.](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)  
  
    Denk aan `IS [NOT] NULL` de volgende semantiek:  
  
    -   `property IS NULL`wordt geëvalueerd `true` alsof de eigenschap niet bestaat of de `null`waarde van de eigenschap is.  
  
### <a name="property-evaluation-semantics"></a>Property evaluatie semantiek  
  
- Een poging om een niet-bestaande systeemeigenschap te evalueren, werpt een [uitzondering op Filteruitzondering.](/dotnet/api/microsoft.servicebus.messaging.filterexception)  
  
- Een eigenschap die niet bestaat, wordt intern als **onbekend**beoordeeld.  
  
  Onbekende evaluatie bij rekenkundige operatoren:  
  
- Voor binaire operatoren, als de linker- en/of rechterkant van operands als **onbekend**wordt beoordeeld , is het resultaat **onbekend**.  
  
- Voor unary operatoren, als een operand wordt geëvalueerd als **onbekend**, dan is het resultaat **onbekend**.  
  
  Onbekende evaluatie bij binaire vergelijkingsoperatoren:  
  
- Als de linker- en/of rechterkant van operands als **onbekend**wordt beoordeeld , dan is het resultaat **onbekend**.  
  
  Onbekende evaluatie `[NOT] LIKE`in :  
  
- Als een operand wordt geëvalueerd als **onbekend,** dan is het resultaat **onbekend**.  
  
  Onbekende evaluatie `[NOT] IN`in :  
  
- Als de linker operand wordt geëvalueerd als **onbekend**, dan is het resultaat **onbekend**.  
  
  Onbekende evaluatie in **EN** operator:  
  
```  
+---+---+---+---+  
|AND| T | F | U |  
+---+---+---+---+  
| T | T | F | U |  
+---+---+---+---+  
| F | F | F | F |  
+---+---+---+---+  
| U | U | F | U |  
+---+---+---+---+  
```  
  
 Onbekende evaluatie in **OR-operator:**  
  
```  
+---+---+---+---+  
|OR | T | F | U |  
+---+---+---+---+  
| T | T | T | T |  
+---+---+---+---+  
| F | T | F | U |  
+---+---+---+---+  
| U | T | U | U |  
+---+---+---+---+  
```  
  
### <a name="operator-binding-semantics"></a>Operatorbinding semantiek
  
-   Vergelijkingsoperatoren `>` `>=`zoals `<` `<=`, `!=`, `=` , en volgen dezelfde semantiek als de C#-operator binding in gegevenstypepromoties en impliciete conversies.  
  
-   Rekenkundige operatoren `-` `*`zoals `/` `+`, `%` , , en volgen dezelfde semantiek als de C# operator binding in data type promoties en impliciete conversies.

## <a name="next-steps"></a>Volgende stappen

- [SQLFilter-klasse (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter-klasse (.NET-standaard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [SQLRuleAction, klasse](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
