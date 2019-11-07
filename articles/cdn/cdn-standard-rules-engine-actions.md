---
title: Azure CDN van de engine acties van micro soft Standard Rules | Microsoft Docs
description: Referentie documentatie voor Azure CDN van de engine acties van micro soft Standard Rules.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: dbde93cc7ffd21e341653407e6e4f910e4620974
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615986"
---
# <a name="azure-cdn-from-microsoft-standard-rules-engine-actions"></a>Azure CDN van de engine acties van micro soft Standard Rules

In dit artikel vindt u gedetailleerde beschrijvingen van de beschik bare acties voor Azure Content Delivery Network (CDN) van de micro soft [Standard Rules engine](cdn-standard-rules-engine.md).

Het tweede deel van een regel is een actie. Een actie definieert het gedrag dat wordt toegepast op het aanvraag type dat wordt geïdentificeerd door een set matching voorwaarden.

## <a name="actions"></a>Acties

De volgende acties zijn beschikbaar voor gebruik. 

## <a name="cache-expiration"></a>Verval datum van cache

Met deze actie kunt u de TTL van het eind punt overschrijven voor aanvragen die zijn opgegeven met de voor waarden van de regels.

**Vereiste velden**

Cache gedrag |                
---------------|----------------
Cache overs Laan | Als deze optie is geselecteerd en de regel overeenkomt, wordt de inhoud niet in de cache opgeslagen.
Overschrijven | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL-waarde die is geretourneerd door de oorsprong, overschreven met de waarde die is opgegeven in de actie.
Instellen als ontbreekt | Als deze optie is geselecteerd en de regel overeenkomt, wordt de TTL ingesteld op de waarde die is opgegeven in de actie als er geen TTL-waarde is geretourneerd van de oorsprong.

**Aanvullende velden**

Dagen | Uren | Minuten | Seconden
-----|-------|---------|--------
integer | integer | integer | integer 

## <a name="cache-key-query-string"></a>Query reeks voor de cache sleutel

Met deze actie kunt u de cache sleutel aanpassen op basis van query teken reeksen.

**Vereiste velden**

Gedrag | Beschrijving
---------|------------
behoort | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen die zijn opgegeven in de para meters opgenomen bij het genereren van de cache sleutel. 
Elke unieke URL in de cache opslaan | Als deze optie is geselecteerd en de regel overeenkomt, heeft elke unieke URL een eigen cache sleutel. 
Exclude | Als deze optie is geselecteerd en de regel overeenkomt, worden query reeksen die zijn opgegeven in de para meters uitgesloten bij het genereren van de cache sleutel.
Queryreeksen negeren | Als deze optie is geselecteerd en de regel overeenkomt, worden er geen query reeksen meer in rekening gehouden bij het genereren van de cache sleutel. 

## <a name="modify-request-header"></a>Aanvraag header wijzigen

Met deze actie kunt u de kopteksten wijzigen van aanvragen die zijn verzonden naar uw oorsprong.

**Vereiste velden**

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de header toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Schrijft | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de header toegevoegd aan de aanvraag met de opgegeven waarde. Als de header al aanwezig is, zal de waarde de bestaande waarde overschrijven. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd en de regel overeenkomt en de header die in de regel is opgegeven, aanwezig is, wordt deze uit de aanvraag verwijderd. | Tekenreeks

## <a name="modify-response-header"></a>Antwoord header wijzigen

Met deze actie kunt u kopteksten wijzigen die aanwezig zijn in antwoorden die worden geretourneerd naar uw eind clients

**Vereiste velden**

Bewerking | Naam van HTTP-header | Waarde
-------|------------------|------
Toevoegen | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de header toegevoegd aan het antwoord met de opgegeven waarde. Als de header al aanwezig is, wordt de waarde toegevoegd aan de bestaande waarde. | Tekenreeks
Schrijft | Als deze optie is geselecteerd en de regel overeenkomt, wordt de header die is opgegeven in de naam van de header toegevoegd aan het antwoord met de opgegeven waarde. Als de header al aanwezig is, zal de waarde de bestaande waarde overschrijven. | Tekenreeks
Verwijderen | Als deze optie is geselecteerd en de regel overeenkomt en de header die in de regel is opgegeven, aanwezig is, wordt deze uit het antwoord verwijderd. | Tekenreeks

## <a name="url-redirect"></a>URL-omleiding

Met deze actie kunt u eind clients omleiden naar een nieuwe URL. 

**Vereiste velden**

Veld | Beschrijving 
------|------------
Type | Selecteer het antwoord type dat wordt geretourneerd naar de aanvrager. Opties zijn-302 gevonden, 301 verplaatst, 307 tijdelijke omleiding en 308 permanente omleiding
Protocol | Overeenkomende aanvraag, HTTP of HTTPS
Hostnaam | Selecteer de hostnaam waarnaar de aanvraag wordt omgeleid. Laat leeg om de binnenkomende host te bewaren.
Pad | Definieer het pad dat in de omleiding moet worden gebruikt. Laat leeg om het binnenkomende pad te bewaren.  
Queryreeksen | Definieer de query teken reeks die in de omleiding wordt gebruikt. Laat de waarde leeg om de binnenkomende query reeks te bewaren. 
Fragment | Definieer het fragment dat in de omleiding moet worden gebruikt. Laat leeg om het binnenkomende fragment te bewaren. 

Het is raadzaam om een absolute URL te gebruiken. Het gebruik van een relatieve URL kan CDN-Url's omleiden naar een ongeldig pad. 

## <a name="url-rewrite"></a>URL opnieuw genereren

Met deze actie kunt u het pad van een aanvraag en route naar uw oorsprong herschrijven.

**Vereiste velden**

Veld | Beschrijving 
------|------------
Bron patroon | Definieer het bron patroon in het URL-pad dat moet worden vervangen. Op dit moment gebruikt het bron patroon een overeenkomst op basis van voor voegsels. Als u wilt dat alle URL-paden overeenkomen, gebruikt u/als de waarde van het bron patroon.
Doel | Definieer het doelpad dat moet worden gebruikt voor herschrijven. Hiermee wordt het bron patroon overschreven
Niet-overeenkomend pad behouden | Zo ja, dan wordt het resterende pad na het bron patroon toegevoegd aan het nieuwe doelpad. 


[Terug naar boven](#actions)

</br>

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van Azure Content Delivery Network](cdn-overview.md)
- [Verwijzing regelengine](cdn-standard-rules-engine-reference.md)
- [Criteria voor overeenkomst regelengine](cdn-standard-rules-engine-match-conditions.md)
- [HTTPS afdwingen met de standaard regels-engine](cdn-standard-rules-engine.md)
