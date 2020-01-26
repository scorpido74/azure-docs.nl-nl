---
title: Naslag informatie over Azure Service Bus SQLFilter | Microsoft Docs
description: Dit artikel bevat informatie over SQLFilter-grammatica. Een SqlFilter ondersteunt een subset van de SQL-92-standaard.
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
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76759660"
---
# <a name="sqlfilter-syntax"></a>SQLFilter-syntaxis

Een *SqlFilter* -object is een instantie van de [klasse SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)en vertegenwoordigt een op SQL-taal gebaseerde filter expressie die wordt geëvalueerd op basis van een [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). Een SqlFilter ondersteunt een subset van de SQL-92-standaard.  
  
 In dit onderwerp vindt u meer informatie over SqlFilter-grammatica.  
  
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
  
-   `<scope>` is een optionele teken reeks die het bereik van de `<property_name>`aangeeft. Geldige waarden zijn `sys` of `user`. De `sys` waarde geeft de systeem Scope aan waarbij `<property_name>` een naam van een open bare eigenschap is van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). `user` geeft het gebruikers bereik aan waarbij `<property_name>` een sleutel is van de hoofdwoorden lijst van de [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) . `user` bereik is het standaard bereik als `<scope>` niet is opgegeven.  
  
## <a name="remarks"></a>Opmerkingen

Er is een fout opgetreden bij een poging toegang te krijgen tot een niet-bestaande systeem eigenschap. een poging om toegang te krijgen tot een niet-bestaande gebruikers eigenschap is geen fout. In plaats daarvan wordt een niet-bestaande gebruikers eigenschap intern geëvalueerd als een onbekende waarde. Een onbekende waarde wordt speciaal behandeld tijdens de evaluatie van de operator.  
  
## <a name="property_name"></a>property_name  
  
```  
<property_name> ::=  
     <identifier>  
     | <delimited_identifier>  
  
<identifier> ::=  
     <regular_identifier> | <quoted_identifier> | <delimited_identifier>  
  
```  
  
### <a name="arguments"></a>Argumenten  

 `<regular_identifier>` is een teken reeks die wordt vertegenwoordigd door de volgende reguliere expressie:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
Deze grammatica betekent dat een wille keurige teken reeks begint met een letter en wordt gevolgd door een of meer onderstrepings tekens/letter/cijfer.  
  
`[:IsLetter:]` betekent een Unicode-teken dat is gecategoriseerd als Unicode-letter. `System.Char.IsLetter(c)` retourneert `true` als `c` een Unicode-letter is.  
  
`[:IsDigit:]` betekent een Unicode-teken dat is gecategoriseerd als een decimaal getal. `System.Char.IsDigit(c)` retourneert `true` als `c` een Unicode-cijfer is.  
  
Een `<regular_identifier>` kan geen gereserveerd tref woord zijn.  
  
`<delimited_identifier>` is een wille keurige teken reeks die wordt Inge sloten met vier Kante haken links/rechts ([]). Een rechter rechte haak wordt weer gegeven als twee rechter rechte haken. Hier volgen enkele voor beelden van `<delimited_identifier>`:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
`<quoted_identifier>` is een teken reeks die tussen dubbele aanhalings tekens is geplaatst. Een dubbel aanhalings teken in id wordt weer gegeven als twee dubbele aanhalings tekens. Het is niet raadzaam om id's van aanhalings tekens te gebruiken omdat deze eenvoudig kunnen worden verward met een teken reeks constante. Gebruik, indien mogelijk, een gescheiden id. Hier volgt een voor beeld van `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
`<pattern>` moet een expressie zijn die als een teken reeks wordt geëvalueerd. Deze wordt gebruikt als een patroon voor de LIKE-operator.      De naam kan de volgende joker tekens bevatten:  
  
-   `%`: een wille keurige teken reeks van nul of meer tekens.  
  
-   `_`: één wille keurig teken.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen  

`<escape_char>` moet een expressie zijn die als een teken reeks met een lengte van 1 wordt geëvalueerd. Deze wordt gebruikt als escape-teken voor de operator LIKE.  
  
 `property LIKE 'ABC\%' ESCAPE '\'` komt bijvoorbeeld overeen met `ABC%` in plaats van een teken reeks die begint met `ABC`.  
  
## <a name="constant"></a>constante  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenten  
  
-   `<integer_constant>` is een teken reeks met getallen die niet tussen aanhalings tekens staan en die geen decimale punten bevatten. De waarden worden opgeslagen als `System.Int64` intern en volgen hetzelfde bereik.  
  
     Dit zijn voor beelden van lange constanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>` is een teken reeks met getallen die niet tussen aanhalings tekens staan en die een decimaal teken bevatten. De waarden worden opgeslagen als `System.Double` intern en volgen hetzelfde bereik/dezelfde precisie.  
  
     In een toekomstige versie kan dit nummer worden opgeslagen in een ander gegevens type ter ondersteuning van nauw keurige semantiek. Daarom moet u niet vertrouwen op het feit dat het onderliggende gegevens type `System.Double` is voor `<decimal_constant>`.  
  
     Hier volgen enkele voor beelden van decimale constanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>` is een getal dat is geschreven in een weten schappelijke notatie. De waarden worden opgeslagen als `System.Double` intern en volgen hetzelfde bereik/dezelfde precisie. Hier volgen enkele voor beelden van constanten met een benaderende waarde:  
  
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

Booleaanse constanten worden vertegenwoordigd door de tref woorden **True** of **False**. De waarden worden opgeslagen als `System.Boolean`.  
  
## <a name="string_constant"></a>string_constant  
  
```  
<string_constant>  
```  
  
### <a name="remarks"></a>Opmerkingen  

Teken reeks constanten worden tussen enkele aanhalings tekens geplaatst en bevatten geldige Unicode-tekens. Een enkel aanhalings teken in een teken reeks constante wordt weer gegeven als twee enkele aanhalings tekens.  
  
## <a name="function"></a>functieassembly  
  
```  
<function> :=  
      newid() |  
      property(name) | p(name)  
```  
  
### <a name="remarks"></a>Opmerkingen
  
De functie `newid()` retourneert een **System. GUID** die wordt gegenereerd door de methode `System.Guid.NewGuid()`.  
  
De functie `property(name)` retourneert de waarde van de eigenschap waarnaar wordt verwezen door `name`. De `name`-waarde kan een geldige expressie zijn die een teken reeks waarde retourneert.  
  
## <a name="considerations"></a>Overwegingen
  
Houd rekening met de volgende [SqlFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) -semantiek:  
  
-   Eigenschaps namen zijn niet hoofdletter gevoelig.  
  
-   Opera tors C# volgen impliciete semantische conversie wanneer dat mogelijk is.  
  
-   Systeem eigenschappen zijn open bare eigenschappen die worden weer gegeven in [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) -instanties.  
  
    Houd rekening met de volgende `IS [NOT] NULL` semantiek:  
  
    -   `property IS NULL` wordt geëvalueerd als `true` als de eigenschap niet bestaat of de waarde van de eigenschap is `null`.  
  
### <a name="property-evaluation-semantics"></a>Semantische eigenschappen van eigenschaps evaluatie  
  
- Een poging om een niet-bestaande systeem eigenschap te evalueren, genereert een [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) -uitzonde ring.  
  
- Een eigenschap die niet bestaat, wordt intern geëvalueerd als **onbekend**.  
  
  Onbekende evaluatie van reken kundige Opera tors:  
  
- Voor binaire Opera tors geldt dat als de linker-en/of rechter zijde van operands wordt geëvalueerd als **onbekend**, het resultaat **onbekend**is.  
  
- Voor unaire Opera tors geldt dat als een operand als **onbekend**wordt geëvalueerd, het resultaat **onbekend**is.  
  
  Onbekende evaluatie van Opera tors voor binaire vergelijkings operatoren:  
  
- Als de linker-en/of rechter zijde van operands wordt geëvalueerd als **onbekend**, is het resultaat **onbekend**.  
  
  Onbekende evaluatie in `[NOT] LIKE`:  
  
- Als een operand als **onbekend**wordt geëvalueerd, is het resultaat **onbekend**.  
  
  Onbekende evaluatie in `[NOT] IN`:  
  
- Als de linkeroperand wordt geëvalueerd als **onbekend**, is het resultaat **onbekend**.  
  
  Onbekende evaluatie in **en** -operator:  
  
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
  
 Onbekende evaluatie in **of** -operator:  
  
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
  
### <a name="operator-binding-semantics"></a>Semantiek van operator binding
  
-   Vergelijkings operatoren zoals `>`, `>=`, `<`, `<=`, `!=`en `=` volgen dezelfde semantiek als de operator binding C# in het gegevens type promoties en impliciete conversies.  
  
-   Reken kundige Opera tors zoals `+`, `-`, `*`, `/`en `%` volgen dezelfde semantiek als de operator C# binding in het gegevens type promoties en impliciete conversies.

## <a name="next-steps"></a>Volgende stappen

- [SQLFilter-klasse (.NET Framework)](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
- [SQLFilter-klasse (.NET Standard)](/dotnet/api/microsoft.azure.servicebus.sqlfilter)
- [Klasse SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
