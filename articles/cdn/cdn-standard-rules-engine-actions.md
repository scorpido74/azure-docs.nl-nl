---
title: Acties in de standaardregelsengine voor Azure CDN | Microsoft Documenten
description: Referentiedocumentatie voor acties in de standaardregelsengine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259949"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Acties in de standaardregelsengine voor Azure CDN

In de [standaardregelsengine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer overeenkomstvoorwaarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de acties die u gebruiken in de standaardregelsengine voor Azure CDN.

Het tweede deel van een regel is een actie. Een actie definieert het gedrag dat wordt toegepast op het verzoektype dat een overeenkomende voorwaarde of een reeks wedstrijdvoorwaarden identificeert.

## <a name="actions"></a>Acties

De volgende acties zijn beschikbaar voor gebruik in de standaardregelsengine voor Azure CDN. 

### <a name="cache-expiration"></a>Cache vervaldatum

Gebruik deze actie om de time-to-live (TTL)-waarde van het eindpunt te overschrijven voor aanvragen die overeenkomen met de voorwaarden.

#### <a name="required-fields"></a>Vereiste velden

Cachegedrag |  Beschrijving              
---------------|----------------
Cache omzeilen | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de inhoud niet in de cache opgeslagen.
Onderdrukken | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de TTL-waarde die van uw oorsprong wordt geretourneerd, overschreven met de waarde die in de actie is opgegeven.
Instellen als deze ontbreken | Wanneer deze optie is geselecteerd en de regel overeenkomt, als er geen TTL-waarde is geretourneerd van uw oorsprong, stelt de regel de TTL in op de waarde die in de actie is opgegeven.

#### <a name="additional-fields"></a>Extra velden

Dagen | Uren | Minuten | Seconden
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Querytekenreeks cachesleutel

Gebruik deze actie om de cachesleutel te wijzigen op basis van querytekenreeksen.

#### <a name="required-fields"></a>Vereiste velden

Gedrag | Beschrijving
---------|------------
Opnemen | Wanneer deze optie is geselecteerd en de regel overeenkomt, worden querytekenreeksen die in de parameters zijn opgegeven, opgenomen wanneer de cachesleutel wordt gegenereerd. 
Elke unieke URL in de cache opslaan | Wanneer deze optie is geselecteerd en de regel overeenkomt, heeft elke unieke URL zijn eigen cachesleutel. 
Exclude | Wanneer deze optie is geselecteerd en de regel overeenkomt, worden querytekenreeksen die in de parameters zijn opgegeven, uitgesloten wanneer de cachesleutel wordt gegenereerd.
Queryreeksen negeren | Wanneer deze optie is geselecteerd en de regel overeenkomt, worden querytekenreeksen niet in aanmerking genomen wanneer de cachesleutel wordt gegenereerd. 

### <a name="modify-request-header"></a>Koptekst van aanvragen wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in aanvragen die naar uw oorsprong worden verzonden.

#### <a name="required-fields"></a>Vereiste velden

Actie | HTTP-koptekstnaam | Waarde
-------|------------------|------
Toevoegen | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de koptekst die is opgegeven in **de naam Koptekst** toegevoegd aan de aanvraag met de opgegeven waarde. Als de koptekst al aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de koptekst die is opgegeven in **de naam Koptekst** toegevoegd aan de aanvraag met de opgegeven waarde. Als de koptekst al aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Wanneer deze optie is geselecteerd, wordt de regel overeenkomen en is de koptekst die in de regel is opgegeven aanwezig, de koptekst uit de aanvraag verwijderd. | Tekenreeks

### <a name="modify-response-header"></a>Reactiekop wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in reacties die aan uw clients zijn geretourneerd.

#### <a name="required-fields"></a>Vereiste velden

Actie | HTTP-koptekstnaam | Waarde
-------|------------------|------
Toevoegen | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de koptekst die is opgegeven in de **naam Koptekst** toegevoegd aan het antwoord met behulp van de opgegeven **waarde**. Als de koptekst al aanwezig is, wordt **waarde** toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Wanneer deze optie is geselecteerd en de regel overeenkomt, wordt de koptekst die is opgegeven in de **naam Koptekst** toegevoegd aan het antwoord met behulp van de opgegeven **waarde**. Als de koptekst al aanwezig is, overschrijft **Waarde** de bestaande waarde. | Tekenreeks
Verwijderen | Wanneer deze optie is geselecteerd, wordt de regel overeenkomen en is de koptekst die in de regel is opgegeven aanwezig, de koptekst uit het antwoord verwijderd. | Tekenreeks

### <a name="url-redirect"></a>URL-omleiding

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Type | Selecteer het antwoordtype dat u wilt terugsturen naar de aanvrager: Gevonden (302), Verplaatst (301), Tijdelijke omleiding (307) en Permanente omleiding (308).
Protocol | Match Request, HTTP, HTTPS.
Hostnaam | Selecteer de hostnaam waarnaar het verzoek moet worden doorgestuurd. Laat leeg om de binnenkomende host te behouden.
Pad | Definieer het pad dat u wilt gebruiken in de omleiding. Laat leeg om het binnenkomende pad te behouden.  
Queryreeks | Definieer de querytekenreeks die wordt gebruikt in de omleiding. Laat leeg om de binnenkomende querytekenreeks te behouden. 
Fragment | Definieer het fragment dat u wilt gebruiken in de omleiding. Laat leeg om het binnenkomende fragment te behouden. 

We raden u ten zeerste aan een absolute URL te gebruiken. Als u een relatieve URL gebruikt, kunnen Azure CDN-URL's worden omgeleid naar een ongeldig pad. 

### <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze actie om het pad van een aanvraag te herschrijven die onderweg is naar uw oorsprong.

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Bronpatroon | Definieer het bronpatroon in het te vervangen URL-pad. Momenteel maakt het bronpatroon gebruik van een op voorvoegsel gebaseerde overeenkomst. Als u alle URL-paden wilt**/** matchen, gebruikt u een voorwaartse slash ( ) als de waarde van het bronpatroon.
Doel | Definieer het doelpad dat u wilt gebruiken in de herschrijven. Het doelpad overschrijft het bronpatroon.
Onovertroffen pad behouden | Als het resterende pad is ingesteld op **Ja,** wordt het resterende pad toegevoegd aan het nieuwe bestemmingspad. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Overeenkomen met de voorwaarden in de standaardregelsmotor](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
