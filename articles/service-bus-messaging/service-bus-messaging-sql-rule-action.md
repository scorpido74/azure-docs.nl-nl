---
title: Naslag informatie over de SQLRuleAction-syntaxis in Azure Service Bus
description: Dit artikel bevat een verwijzing naar de SQLRuleAction-syntaxis. De acties worden geschreven in de syntaxis op basis van SQL-taal die wordt uitgevoerd voor een brokered bericht.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76759625"
---
# <a name="sqlruleaction-syntax-reference-for-azure-service-bus"></a>Naslag informatie voor SQLRuleAction-syntaxis voor Azure Service Bus

Een *SqlRuleAction* is een instantie van de klasse [SqlRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction) en vertegenwoordigt een set acties die zijn geschreven in de SQL-taal syntaxis die wordt uitgevoerd op een [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).   
  
Dit artikel bevat informatie over de grammatica van de SQL-regel actie.  
  
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
  
-   `<scope>`is een optionele teken reeks die het bereik van `<property_name>`de aangeeft. Geldige waarden zijn `sys` of `user`. De `sys` waarde geeft de systeem Scope `<property_name>` aan waar de naam van een open bare eigenschap van de [klasse BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage)is. `user`Hiermee wordt het gebruikers `<property_name>` bereik aangegeven waarbij een sleutel van de [BrokeredMessage-klassen](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) woordenlijst is. `user`Scope is het standaard bereik als `<scope>` niet is opgegeven.  
  
### <a name="remarks"></a>Opmerkingen  

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
 `<regular_identifier>`is een teken reeks die wordt vertegenwoordigd door de volgende reguliere expressie:  
  
```  
[[:IsLetter:]][_[:IsLetter:][:IsDigit:]]*  
```  
  
 Dit betekent een wille keurige teken reeks die begint met een letter en wordt gevolgd door een of meer onderstrepings tekens/letter/cijfer.  
  
 `[:IsLetter:]`betekent elk Unicode-teken dat is gecategoriseerd als Unicode-letter. `System.Char.IsLetter(c)``c` retourneert `true` een Unicode-letter.  
  
 `[:IsDigit:]`betekent elk Unicode-teken dat is gecategoriseerd als een decimaal cijfer. `System.Char.IsDigit(c)`retourneert `true` of `c` een Unicode-cijfer is.  
  
 Een `<regular_identifier>` kan geen gereserveerd tref woord zijn.  
  
 `<delimited_identifier>`is een teken reeks die wordt Inge sloten met vier Kante haken links/rechts ([]). Een rechter rechte haak wordt weer gegeven als twee rechter rechte haken. Hier volgen enkele voor beelden `<delimited_identifier>`van:  
  
```  
[Property With Space]  
[HR-EmployeeID]  
  
```  
  
 `<quoted_identifier>`is een teken reeks die tussen dubbele aanhalings tekens is geplaatst. Een dubbel aanhalings teken in id wordt weer gegeven als twee dubbele aanhalings tekens. Het is niet raadzaam om id's van aanhalings tekens te gebruiken omdat deze eenvoudig kunnen worden verward met een teken reeks constante. Gebruik, indien mogelijk, een gescheiden id. Hier volgt een voor beeld van `<quoted_identifier>`:  
  
```  
"Contoso & Northwind"  
```  
  
## <a name="pattern"></a>pattern  
  
```  
<pattern> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<pattern>`moet een expressie zijn die als een teken reeks wordt geëvalueerd. Deze wordt gebruikt als een patroon voor de LIKE-operator.      De naam kan de volgende joker tekens bevatten:  
  
-   `%`: Een wille keurige teken reeks van nul of meer tekens.  
  
-   `_`: Eén wille keurig teken.  
  
## <a name="escape_char"></a>escape_char  
  
```  
<escape_char> ::=  
      <expression>  
```  
  
### <a name="remarks"></a>Opmerkingen
  
 `<escape_char>`moet een expressie zijn die als een teken reeks met een lengte van 1 wordt geëvalueerd. Deze wordt gebruikt als escape-teken voor de operator LIKE.  
  
 Bijvoorbeeld, `property LIKE 'ABC\%' ESCAPE '\'` komt overeen `ABC%` met een teken reeks die begint met `ABC`.  
  
## <a name="constant"></a>bedrag  
  
```  
<constant> ::=  
      <integer_constant> | <decimal_constant> | <approximate_number_constant> | <boolean_constant> | NULL  
```  
  
### <a name="arguments"></a>Argumenten  
  
-   `<integer_constant>`is een teken reeks die niet tussen aanhalings tekens staat en geen decimale punten bevat. De waarden worden `System.Int64` intern opgeslagen en volgen hetzelfde bereik.  
  
     Hier volgen enkele voor beelden van lange constanten:  
  
    ```  
    1894  
    2  
    ```  
  
-   `<decimal_constant>`is een teken reeks die niet tussen aanhalings tekens staat en een decimaal teken bevat. De waarden worden `System.Double` intern opgeslagen en volgen hetzelfde bereik/dezelfde precisie.  
  
     In een toekomstige versie kan dit nummer worden opgeslagen in een ander gegevens type ter ondersteuning van nauw keurige semantiek, dus moet u niet vertrouwen op het feit dat het onderliggende gegevens type `System.Double` voor `<decimal_constant>`is.  
  
     Hier volgen enkele voor beelden van decimale constanten:  
  
    ```  
    1894.1204  
    2.0  
    ```  
  
-   `<approximate_number_constant>`is een getal dat is geschreven in een weten schappelijke notatie. De waarden worden `System.Double` intern opgeslagen en volgen hetzelfde bereik/dezelfde precisie. Hier volgen enkele voor beelden van constanten met een benaderende waarde:  
  
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
  
Booleaanse constanten worden vertegenwoordigd door de tref `TRUE` woorden `FALSE`of. De waarden worden opgeslagen als `System.Boolean`.  
  
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

De `newid()` functie retourneert een **System. GUID** die is gegenereerd `System.Guid.NewGuid()` door de-methode.  
  
De `property(name)` functie retourneert de waarde van de eigenschap waarnaar wordt verwezen `name`door. De `name` waarde kan een geldige expressie zijn die een teken reeks waarde retourneert.  
  
## <a name="considerations"></a>Overwegingen

- SET wordt gebruikt om een nieuwe eigenschap te maken of de waarde van een bestaande eigenschap bij te werken.
- VERWIJDEREN wordt gebruikt om een eigenschap te verwijderen.
- SET voert impliciete omzetting uit indien mogelijk wanneer het expressie type en het bestaande eigenschaps type verschillend zijn.
- De actie mislukt als er naar niet-bestaande systeem eigenschappen wordt verwezen.
- De actie mislukt als er niet-bestaande gebruikers eigenschappen naar worden verwezen.
- Een niet-bestaande gebruikers eigenschap wordt intern geëvalueerd als ' onbekend ', volgens dezelfde semantiek als [SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter) bij het evalueren van Opera tors.

## <a name="next-steps"></a>Volgende stappen

- [Klasse SQLRuleAction](/dotnet/api/microsoft.servicebus.messaging.sqlruleaction)
- [Klasse SQLFilter](/dotnet/api/microsoft.servicebus.messaging.sqlfilter)
