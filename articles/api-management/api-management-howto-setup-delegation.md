---
title: Gebruikersregistratie en productabonnement delegeren
description: Meer informatie over het delegeren van gebruikersregistratie en productabonnement aan een derde partij in Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.assetid: 8b7ad5ee-a873-4966-a400-7e508bbbe158
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/04/2019
ms.author: apimpm
ms.openlocfilehash: a69babdf2fffb4cb9d963f1806f3c85755e50294
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74454353"
---
# <a name="how-to-delegate-user-registration-and-product-subscription"></a>Gebruikersregistratie en productabonnement delegeren

Met Delegeren u uw bestaande website gebruiken voor het verwerken van aanmeldings-/aanmeldings- en abonnementen op producten, in tegenstelling tot het gebruik van de ingebouwde functionaliteit in de ontwikkelaarsportal. Het stelt uw website in staat om de gebruikersgegevens te bezitten en de validatie van deze stappen op een aangepaste manier uit te voeren.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="delegating-developer-sign-in-and-sign-up"></a><a name="delegate-signin-up"> </a>Aanmelden en aanmelden voor ontwikkelaars delegeren

Als u ontwikkelaars wilt delegeren, zich wilt aanmelden en u wilt aanmelden bij uw bestaande website, moet u een speciaal eindpunt voor de delegatie op uw site maken. Het moet fungeren als het toegangspunt voor een dergelijk verzoek dat is gestart vanuit de API Management-ontwikkelaarsportal.

De uiteindelijke workflow ziet er als volgt uit:

1. Ontwikkelaar klikt op de aanmeldings- of aanmeldingskoppeling op de API Management-ontwikkelaarsportal
2. Browser wordt doorgestuurd naar het eindpunt van de delegatie
3. Eindpunt van de delegatie in ruil daarvoor wordt omgeleid naar of presenteert ui met het verzoek aan de gebruiker zich aan te melden of aan te melden
4. Bij succes wordt de gebruiker doorgestuurd naar de API Management developer portal pagina die ze zijn gestart vanaf

Laten we om te beginnen API-beheer eerst instellen om aanvragen via uw delegatieeindpunt te routeren. Zoek in de Azure-portal naar **Beveiliging** in uw API-beheerbron en klik vervolgens op het **item Delegeren.** Klik op het selectievakje om 'Aanmelden voor gemachtigde in & inschakelen'.

![Delegatiepagina][api-management-delegation-signin-up]

* Bepaal wat de URL van uw speciale delegatieeindpunt is en voer deze in het veld URL van het **eindpunt van de delegatie** in. 
* Voer binnen het veld Verificatiesleutel van de leger een geheim in dat wordt gebruikt om een handtekening te berekenen die u ter verificatie heeft verstrekt om ervoor te zorgen dat de aanvraag inderdaad afkomstig is van Azure API Management. U op de **knop Genereren** klikken om API-beheer willekeurig een sleutel voor u te laten genereren.

Nu moet u het **eindpunt van**de delegatie maken. Het moet een aantal acties uitvoeren:

1. Ontvang een aanvraag in het volgende formulier:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation=SignIn&returnUrl={URL of source page}&salt={string}&sig={string}*
   > 
   > 
   
    Queryparameters voor de aanmeldingsaanvraag/ aanmeldingsaanvraag:
   
   * **operatie**: geeft aan welk type delegatie verzoek het is - het kan alleen **SignIn** in dit geval
   * **retourURL:** de URL van de pagina waar de gebruiker op een aanmeldings- of aanmeldingskoppeling heeft geklikt
   * **zout**: een speciale zoutstring die wordt gebruikt voor het berekenen van een beveiligingshash
   * **sig**: een berekende beveiligingshash die moet worden gebruikt voor vergelijking met uw eigen berekende hash
2. Controleren of de aanvraag afkomstig is van Azure API Management (optioneel, maar sterk aanbevolen voor beveiliging)
   
   * Bereken een HMAC-SHA512 hash van een tekenreeks op basis van de **parameters returnUrl** en **salt** query[(voorbeeldcode hieronder):]
     
     > HMAC(**zout** + '\n' + **returnUrl**)
     > 
     > 
   * Vergelijk de hierboven berekende hash met de waarde van de **parameter sig** query. Als de twee hashes overeenkomen, gaat u verder met de volgende stap, anders wordt het verzoek niet meer ingediend.
3. Controleer of u een aanvraag voor aanmelden/aanmelden ontvangt: de parameter **bewerkingsquery** wordt ingesteld op **" SignIn**".
4. De gebruiker een gebruikersinterface geven om zich aan te melden of zich aan te melden
5. Als de gebruiker zich aanmeldt, moet u een overeenkomstig account voor hen maken in API Management. [Maak een gebruiker] met de API Management REST API. Zorg er daarbij voor dat u de gebruikersnaam instelt op dezelfde waarde als in uw gebruikerswinkel of op een id die u bijhouden.
6. Wanneer de gebruiker is geverifieerd:
   
   * [een SSO-token (single-sign-on) aanvragen] via de API Management REST API
   * een returnUrl-queryparameter toevoegen aan de SSO-URL die u hebt ontvangen van de API-aanroep hierboven:
     
     > bijvoorbeeld,https://customer.portal.azure-api.net/signin-sso?token&returnUrl=/return/url 
     > 
     > 
   * de gebruiker doorverwijzen naar de hierboven geproduceerde URL

Naast de **SignIn-bewerking** u ook accountbeheer uitvoeren door de vorige stappen te volgen en een van de volgende bewerkingen te gebruiken:

* **Changepassword**
* **ChangeProfile**
* **CloseAccount**

U moet de volgende queryparameters doorgeven voor accountbeheerbewerkingen.

* **bewerking**: geeft aan welk type delegatieaanvraag het is (ChangePassword, ChangeProfile of CloseAccount)
* **userId**: de gebruikersnaam van het account te beheren
* **zout**: een speciale zoutstring die wordt gebruikt voor het berekenen van een beveiligingshash
* **sig**: een berekende beveiligingshash die moet worden gebruikt voor vergelijking met uw eigen berekende hash

## <a name="delegating-product-subscription"></a><a name="delegate-product-subscription"> </a>Productabonnement delegeren
Het delegeren van productabonnementwerkt op dezelfde manier als het delegeren van het aanmelden/ophalen van gebruikers. De uiteindelijke workflow zou als volgt zijn:

1. De ontwikkelaar selecteert een product in de ontwikkelaarsportal API Management en klikt op de knop Abonneren.
2. Browser wordt doorgestuurd naar het eindpunt van de delegatie.
3. Het eindpunt van Delegeren voert de vereiste productabonnementsstappen uit. Het is aan jou om de stappen te ontwerpen. Het kan gaan om het doorverwijzen naar een andere pagina om factureringsgegevens op te vragen, aanvullende vragen te stellen of gewoon de informatie op te slaan en geen actie van de gebruiker te vereisen.

Als u de functionaliteit wilt inschakelen, klikt u op de pagina **Delegatie** op **Productabonnement delegeren**.

Zorg er vervolgens voor dat het eindpunt van de delegatie de volgende acties onderneemt:

1. Ontvang een aanvraag in het volgende formulier:
   
   > *http:\//www.yourwebsite.com/apimdelegation?operation={operation}&productId={product om zich te abonneren op}&userId={user making request}&salt={string}&={string}*
   >
   
    Queryparameters voor de productabonnementsaanvraag:
   
   * **operatie**: geeft aan welk type delegatie dit verzoek is. Voor productabonnementsaanvragen zijn de geldige opties:
     * "Subscribe": een verzoek om de gebruiker te abonneren op een bepaald product met een opgegeven ID (zie hieronder)
     * 'Afmelden': een verzoek om een gebruiker uit te schrijven voor een product
     * "Verlengen": een verzoek om een abonnement te verlengen (bijvoorbeeld dat kan aflopen)
   * **productId**: de id van het product waarop de gebruiker zich heeft willen abonneren
   * **abonnementId**: op *Afmelden* en *verlengen* - de id van het productabonnement
   * **userId**: de ID van de gebruiker waarvoor het verzoek is ingediend
   * **zout**: een speciale zoutstring die wordt gebruikt voor het berekenen van een beveiligingshash
   * **sig**: een berekende beveiligingshash die moet worden gebruikt voor vergelijking met uw eigen berekende hash

2. Controleren of de aanvraag afkomstig is van Azure API Management (optioneel, maar sterk aanbevolen voor beveiliging)
   
   * Bereken een HMAC-SHA512 van een tekenreeks op basis van de **productId,userId**en **userId** **zoutqueryparameters:**
     
     > HMAC(**zout** + '\n' + **productId** + '\n' + **userId**)
     > 
     > 
   * Vergelijk de hierboven berekende hash met de waarde van de **parameter sig** query. Als de twee hashes overeenkomen, gaat u verder met de volgende stap, anders wordt het verzoek niet meer ingediend.
3. Productabonnement verwerken op basis van het type bewerking dat in **bedrijf** wordt aangevraagd - bijvoorbeeld facturering, verdere vragen, enz.
4. Als u de gebruiker met succes op het product aan uw zijde wilt abonneren, abonneert u de gebruiker op het API-beheerproduct door [de REST API voor abonnementen aan te roepen.]

## <a name="example-code"></a><a name="delegate-example-code"> </a> Voorbeeldcode

Deze codevoorbeelden laten zien hoe u:

* Neem de *validatiesleutel voor delegatie*, die is ingesteld in het scherm Delegatie van de uitgeversportal
* Maak een HMAC, die vervolgens wordt gebruikt om de handtekening te valideren, waaruit de geldigheid van de doorgegeven returnUrl.

Dezelfde code werkt voor de productId en userId met lichte wijziging.

**C# code om hash van returnUrl te genereren**

```csharp
using System.Security.Cryptography;

string key = "delegation validation key";
string returnUrl = "returnUrl query parameter";
string salt = "salt query parameter";
string signature;
using (var encoder = new HMACSHA512(Convert.FromBase64String(key)))
{
    signature = Convert.ToBase64String(encoder.ComputeHash(Encoding.UTF8.GetBytes(salt + "\n" + returnUrl)));
    // change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
    // compare signature to sig query parameter
}
```

**NodeJS-code om hash van returnUrl te genereren**

```
var crypto = require('crypto');

var key = 'delegation validation key'; 
var returnUrl = 'returnUrl query parameter';
var salt = 'salt query parameter';

var hmac = crypto.createHmac('sha512', new Buffer(key, 'base64'));
var digest = hmac.update(salt + '\n' + returnUrl).digest();
// change to (salt + "\n" + productId + "\n" + userId) when delegating product subscription
// compare signature to sig query parameter

var signature = digest.toString('base64');
```

> [!IMPORTANT]
> U moet [de ontwikkelaarsportal opnieuw publiceren](api-management-howto-developer-portal-customize.md#publish) om de delegatiewijzigingen van kracht te laten worden.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende video voor meer informatie over dedelegatie:

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Delegating-User-Authentication-and-Product-Subscription-to-a-3rd-Party-Site/player]
> 
> 

[Delegating developer sign in and sign up]: #delegate-signin-up
[Delegating product subscription]: #delegate-product-subscription
[een SSO-token (single-sign-on) aanvragen]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/User/GenerateSsoUrl
[een gebruiker maken]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/user/createorupdate
[de REST-API voor abonnementen aanroepen]: https://docs.microsoft.com/rest/api/apimanagement/2019-01-01/subscription/createorupdate
[Next steps]: #next-steps
[voorbeeldcode hieronder]: #delegate-example-code

[api-management-delegation-signin-up]: ./media/api-management-howto-setup-delegation/api-management-delegation-signin-up.png 
