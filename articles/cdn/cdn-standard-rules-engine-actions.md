---
title: Acties in de standaard regels-engine voor Azure CDN | Microsoft Docs
description: Referentie documentatie voor acties in de Standard Rules engine voor Azure Content Delivery Network (Azure CDN).
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: allensu
ms.openlocfilehash: 29138b4fc6716ae5361cc4d7f97ceba41b90c2da
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81259949"
---
# <a name="actions-in-the-standard-rules-engine-for-azure-cdn"></a>Acties in de standaard regels-engine voor Azure CDN

In de [standaard regels-engine](cdn-standard-rules-engine.md) voor Azure Content Delivery Network (Azure CDN) bestaat een regel uit een of meer match-voor waarden en een actie. In dit artikel vindt u gedetailleerde beschrijvingen van de acties die u kunt gebruiken in de Standard Rules engine voor Azure CDN.

Het tweede deel van een regel is een actie. Een actie definieert het gedrag dat wordt toegepast op het aanvraag type dat overeenkomt met de voor waarde of set van match-voor waarden.

## <a name="actions"></a>Acties

De volgende acties zijn beschikbaar voor gebruik in de standaard regels-engine voor Azure CDN. 

### <a name="cache-expiration"></a>Verval datum van cache

Gebruik deze actie om de TTL-waarde (time to Live) van het eind punt te overschrijven voor aanvragen die voldoen aan de regels voor waarden.

#### <a name="required-fields"></a>Vereiste velden

Cache gedrag |  Beschrijving              
---------------|----------------
Cache overs Laan | Als deze optie is geselecteerd en de regel overeenkomt, wordt de inhoud niet in de cache opgeslagen.
Onderdrukken | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL-waarde die wordt geretourneerd door uw oorsprong overschreven door de waarde die is opgegeven in de actie.
Instellen als ontbreekt | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL ingesteld op de waarde die is opgegeven in de actie als er geen TTL-waarde is geretourneerd van uw oorsprong.

#### <a name="additional-fields"></a>Aanvullende velden

Dagen | Uren | Minuten | Seconden
-----|-------|---------|--------
Int | Int | Int | Int 

### <a name="cache-key-query-string"></a>Query reeks voor de cache sleutel

Gebruik deze actie om de cache sleutel te wijzigen op basis van query teken reeksen.

#### <a name="required-fields"></a>Vereiste velden

Gedrag | Beschrijving
---------|------------
Opnemen | Als deze optie is geselecteerd en de regel overeenkomt, worden in de para meters opgegeven query reeksen opgenomen wanneer de cache sleutel wordt gegenereerd. 
Elke unieke URL in de cache opslaan | Als deze optie is geselecteerd en de regel overeenkomt, heeft elke unieke URL een eigen cache sleutel. 
Exclude | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen die zijn opgegeven in de para meters uitgesloten wanneer de cache sleutel wordt gegenereerd.
Queryreeksen negeren | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen niet meegenomen wanneer de cache sleutel wordt gegenereerd. 

### <a name="modify-request-header"></a>Aanvraag header wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in aanvragen die worden verzonden naar uw oorsprong.

#### <a name="required-fields"></a>Vereiste velden

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de **header** toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de **header** toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, overschrijft de opgegeven waarde de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel komt overeen met de opgegeven koptekst in de regel, wordt de header uit de aanvraag verwijderd. | Tekenreeks

### <a name="modify-response-header"></a>Antwoord header wijzigen

Gebruik deze actie om kopteksten te wijzigen die aanwezig zijn in antwoorden die aan uw clients worden geretourneerd.

#### <a name="required-fields"></a>Vereiste velden

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de **header naam** , aan het antwoord toegevoegd met behulp van de opgegeven **waarde**. Als de header al aanwezig is, wordt de **waarde** aan de bestaande waarde toegevoegd. | Tekenreeks
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de **header naam** , aan het antwoord toegevoegd met behulp van de opgegeven **waarde**. Als de header al aanwezig is, overschrijft de **waarde** de bestaande waarde. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd, de regel komt overeen met de opgegeven koptekst in de regel, wordt de header uit het antwoord verwijderd. | Tekenreeks

### <a name="url-redirect"></a>URL-omleiding

Gebruik deze actie om clients om te leiden naar een nieuwe URL. 

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Type | Selecteer het antwoord type dat u wilt terugsturen naar de aanvrager: gevonden (302), verplaatst (301), tijdelijke omleiding (307) en permanente omleiding (308).
Protocol | Match-aanvraag, HTTP, HTTPS.
Hostnaam | Selecteer de hostnaam waarvan u de aanvraag wilt omleiden. Laat het veld leeg om de binnenkomende host te bewaren.
Pad | Definieer het pad dat in de omleiding moet worden gebruikt. Laat het veld leeg als u het binnenkomende pad wilt behouden.  
Queryreeks | Definieer de query teken reeks die in de omleiding wordt gebruikt. Laat het veld leeg om de binnenkomende query reeks te bewaren. 
Fragment | Definieer het fragment dat in de omleiding moet worden gebruikt. Laat het veld leeg om het binnenkomende fragment te bewaren. 

We raden u ten zeerste aan een absolute URL te gebruiken. Het gebruik van een relatieve URL kan Azure CDN Url's omleiden naar een ongeldig pad. 

### <a name="url-rewrite"></a>URL opnieuw genereren

Gebruik deze actie om het pad van een aanvraag die naar uw oorsprong is doorgestuurd, opnieuw te schrijven.

#### <a name="required-fields"></a>Vereiste velden

Veld | Beschrijving 
------|------------
Bron patroon | Definieer het bron patroon in het URL-pad dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen,**/** gebruikt u een slash () als bron patroon waarde.
Doel | Definieer het doelpad dat moet worden gebruikt in de herschrijf bewerking. Het doelpad overschrijft het bron patroon.
Niet-overeenkomend pad behouden | Als deze instelling is ingesteld op **Ja**, wordt het resterende pad na het bron patroon toegevoegd aan het nieuwe doelpad. 

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure CDN](cdn-overview.md)
- [Naslaginformatie over standaardregelengine](cdn-standard-rules-engine-reference.md)
- [Voldoen aan de voor waarden in de standaard regels-engine](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS afdwingen met de standaardregelengine](cdn-standard-rules-engine.md)
