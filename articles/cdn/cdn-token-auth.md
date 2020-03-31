---
title: Azure CDN-assets beveiligen met tokenverificatie| Microsoft Documenten
description: Meer informatie over het gebruik van tokenverificatie om toegang te krijgen tot uw Azure CDN-assets.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: fa71f472294b91baebc2a6075ddb2b50123e545d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593396"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Azure CDN-elementen beveiligen met tokenverificatie

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Overzicht

Tokenverificatie is een mechanisme waarmee u voorkomen dat het Azure Content Delivery Network (CDN) assets aan onbevoegde clients kan leveren. Tokenverificatie wordt meestal gedaan om *hotlinking* van inhoud te voorkomen, waarbij een andere website, zoals een prikbord, uw assets zonder toestemming gebruikt. Hotlinking kan van invloed zijn op de leveringskosten van uw inhoud. Door tokenverificatie op CDN in te schakelen, worden aanvragen geverifieerd door cdn-edgeserver voordat het CDN de inhoud levert. 

## <a name="how-it-works"></a>Hoe werkt het?

Tokenverificatie controleert of aanvragen worden gegenereerd door een vertrouwde site door dat aanvragen een tokenwaarde moeten bevatten die gecodeerde informatie over de aanvrager bevat. Inhoud wordt alleen aan een aanvrager aangeboden als de gecodeerde informatie aan de vereisten voldoet; anders worden verzoeken geweigerd. U de vereisten instellen met behulp van een of meer van de volgende parameters:

- Land: Verzoeken toestaan of weigeren die afkomstig zijn uit de landen/regio's die zijn opgegeven door hun [landcode](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Sta alleen aanvragen toe die overeenkomen met het opgegeven element of pad.
- Host: aanvragen toestaan of weigeren die de opgegeven hosts in de aanvraagkop gebruiken.
- Verwijzer: Verzoek van de opgegeven verwijzer toestaan of weigeren.
- IP-adres: Sta alleen aanvragen toe die afkomstig zijn van een specifiek IP-adres of IP-subnet.
- Protocol: Aanvragen toestaan of weigeren op basis van het protocol dat wordt gebruikt om de inhoud op te vragen.
- Verlooptijd: wijs een datum en tijdsperiode toe om ervoor te zorgen dat een koppeling slechts voor een beperkte periode geldig blijft.

Zie voor meer informatie de gedetailleerde configuratievoorbeelden voor elke parameter in [Het instellen van tokenverificatie](#setting-up-token-authentication).

>[!IMPORTANT] 
> Als tokenautorisatie is ingeschakeld voor elk pad in dit account, is de standaardcachemodus de enige modus die kan worden gebruikt voor het caching van querytekenreeksen. Zie [Cachegedrag in Azure CDN bepalen met queryreeksen](cdn-query-string-premium.md) voor meer informatie.

## <a name="reference-architecture"></a>Referentiearchitectuur

In het volgende werkstroomdiagram wordt beschreven hoe het CDN tokenverificatie gebruikt om met uw web-app te werken.

![Workflow voor cdn-tokenverificatie](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Tokenvalidatielogica op CDN-eindpunt
    
In het volgende stroomdiagram wordt beschreven hoe Azure CDN een clientaanvraag valideert wanneer tokenverificatie is geconfigureerd op CDN-eindpunt.

![Logica voor cdn-tokenvalidatie](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Tokenverificatie instellen

1. Blader vanuit de [Azure-portal](https://portal.azure.com)naar uw CDN-profiel en selecteer **Beheren** om de aanvullende portal te starten.

    ![Knop CDN-profiel beheren](./media/cdn-token-auth/cdn-manage-btn.png)

2. Plaats de plaats boven **HTTP Large**en selecteer **Token Auth** in de flyout. U vervolgens de coderingssleutel- en versleutelingsparameters als volgt instellen:

   1. Maak een of meer versleutelingssleutels. Een versleutelingssleutel is hoofdlettergevoelig en kan elke combinatie van alfanumerieke tekens bevatten. Andere typen tekens, inclusief spaties, zijn niet toegestaan. De maximale lengte is 250 tekens. Om ervoor te zorgen dat uw versleutelingssleutels willekeurig zijn, wordt aanbevolen deze te maken met behulp van het [OpenSSL-hulpprogramma.](https://www.openssl.org/) 

      Het gereedschap OpenSSL heeft de volgende syntaxis:

      ```rand -hex <key length>```

      Bijvoorbeeld:

      ```OpenSSL> rand -hex 32``` 

      Maak zowel een primaire als een back-upsleutel om downtime te voorkomen. Een back-upsleutel biedt ononderbroken toegang tot uw inhoud wanneer uw primaire sleutel wordt bijgewerkt.
    
   2. Voer een unieke versleutelingssleutel in het vak **Primaire sleutel** in en voer optioneel een back-upsleutel in het vak **Back-upsleutel** in.

   3. Selecteer de minimale versleutelingsversie voor elke sleutel in de lijst **Minimale versleutelingsversie** en selecteer **Bijwerken:**
      - **V2**: Geeft aan dat de sleutel kan worden gebruikt om versie 2.0 en 3.0-tokens te genereren. Gebruik deze optie alleen als u overstapt van een verouderde versie 2.0-versleutelingssleutel naar een versie 3.0-toets.
      - **V3**: (Aanbevolen) Geeft aan dat de sleutel alleen kan worden gebruikt om versie 3.0-tokens te genereren.

      ![CDN-token auth-installatiesleutel](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Gebruik het versleutelingsgereedschap om versleutelingsparameters in te stellen en een token te genereren. Met de versleutelingstool u aanvragen toestaan of weigeren op basis van verlooptijd, land/regio, verwijzer, protocol en client-IP (in elke combinatie). Hoewel er geen limiet is aan het aantal en de combinatie van parameters die kunnen worden gecombineerd tot een token, is de totale lengte van een token beperkt tot 512 tekens. 

      ![Gereedschap CDN-versleuteling](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Voer waarden in voor een of meer van de volgende versleutelingsparameters in de sectie **Gereedschap versleutelen:** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Parameternaam</th> 
      >   <th>Beschrijving</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Wijst een vervaldatum toe aan een token, waarna het token verloopt. Aanvragen die na de vervaldatum worden ingediend, worden geweigerd. Deze parameter maakt gebruik van een Unix-tijdstempel, die is gebaseerd `1/1/1970 00:00:00 GMT`op het aantal seconden sinds het standaard Unix-tijdperk van . (U online tools gebruiken om te converteren tussen standaardtijd en Unix-tijd.)> 
      >    Als u bijvoorbeeld wilt dat het `12/31/2016 12:00:00 GMT`token verloopt bij , `1483185600`voert u de waarde van de Unix-tijdstempel in, . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Hiermee u tokens afstemmen op een bepaald actief of pad. Het beperkt de toegang tot aanvragen waarvan de URL begint met een specifiek relatief pad. URL's zijn hoofdlettergevoelig. Voer meerdere paden in door elk pad te scheiden met een komma; voeg geen spaties toe. Afhankelijk van uw vereisten u verschillende waarden instellen om een ander toegangsniveau te bieden.> 
      >    Voor de URL `http://www.mydomain.com/pictures/city/strasbourg.png`zijn deze aanvragen bijvoorbeeld toegestaan voor de volgende invoerwaarden: 
      >    <ul>
      >       <li>Invoerwaarde `/`: Alle aanvragen zijn toegestaan.</li>
      >       <li>Invoerwaarde `/pictures`, de volgende aanvragen zijn toegestaan: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Invoerwaarde: `/pictures/`Alleen aanvragen `/pictures/` die het pad bevatten, zijn toegestaan. Bijvoorbeeld `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Invoerwaarde `/pictures/city/strasbourg.png`: Alleen aanvragen voor dit specifieke pad en deze asset zijn toegestaan.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Hiermee worden alleen aanvragen mogelijk die afkomstig zijn uit een of meer specifieke landen/regio's. Aanvragen die afkomstig zijn uit alle andere landen/regio's worden geweigerd. Gebruik voor elk land een [tweeletterige ISO 3166-landcode](/previous-versions/azure/mt761717(v=azure.100)) en scheid elk land met een komma; voeg geen spatie toe. Als u bijvoorbeeld alleen toegang wilt toestaan vanuit de `US,FR`Verenigde Staten en Frankrijk, voert u .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Weigert verzoeken die afkomstig zijn uit een of meer specifieke landen/regio's. Aanvragen die afkomstig zijn uit alle andere landen/regio's zijn toegestaan. De implementatie is hetzelfde als de <b>parameter ec_country_allow.</b> Als een landcode aanwezig is in zowel de <b>ec_country_allow-</b> als <b>ec_country_deny</b> parameters, heeft de <b>parameter ec_country_allow</b> voorrang.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Hiermee worden alleen aanvragen van de opgegeven verwijzer mogelijk. Een verwijzer identificeert de URL van de webpagina die is gekoppeld aan de gevraagde bron. Neem het protocol niet op in de parameterwaarde.> 
      >    De volgende typen invoer zijn toegestaan:
      >    <ul>
      >       <li>Een hostnaam of een hostnaam en een pad.</li>
      >       <li>Meerdere verwijzers. Als u meerdere verwijzers wilt toevoegen, scheidt u elke verwijzer met een komma; voeg geen spatie toe. Als u een verwijzerwaarde opgeeft, maar de verwijzerinformatie niet wordt verzonden in het verzoek vanwege de browserconfiguratie, wordt het verzoek standaard geweigerd.</li> 
      >       <li>Verzoeken met ontbrekende of lege verwijzerinformatie. Standaard blokkeert de <b>parameter ec_ref_allow</b> dit soort aanvragen. Als u deze aanvragen wilt toestaan, voert u de tekst 'ontbrekend' in of voert u een lege waarde in (met behulp van een slepende komma).</li> 
      >       <li>Subdomeinen. Als u subdomeinen wilt toestaan,\*voert u een sterretje in ( ). Bijvoorbeeld om alle subdomeinen `contoso.com`van `*.contoso.com`, enter .</li>
      >    </ul> 
      >    Als u bijvoorbeeld toegang wilt `www.contoso.com`verlenen voor `contoso2.com`aanvragen van alle subdomeinen onder `www.contoso.com,*.contoso.com,missing`en aanvragen met lege of ontbrekende verwijzers, voert u in .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Weigert verzoeken van de opgegeven verwijzer. De implementatie is hetzelfde als de <b>parameter ec_ref_allow.</b> Als een verwijzer aanwezig is in zowel de <b>ec_ref_allow</b> als <b>ec_ref_deny</b> parameters, heeft de <b>parameter ec_ref_allow</b> voorrang.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Hiermee kunnen alleen aanvragen van het opgegeven protocol worden ingediend. Geldige waarden `http` `https`zijn `http,https`, , of .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Weigert verzoeken van het opgegeven protocol. De implementatie is hetzelfde als de <b>parameter ec_proto_allow.</b> Als een protocol aanwezig is in zowel de <b>ec_proto_allow-</b> als <b>ec_proto_deny</b> parameters, heeft de <b>parameter ec_proto_allow</b> voorrang.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Beperkt de toegang tot het IP-adres van de opgegeven aanvrager. Zowel IPV4 als IPV6 worden ondersteund. U één IP-adres met één aanvraag of IP-adressen opgeven die zijn gekoppeld aan een specifiek subnet. Hiermee worden `11.22.33.0/22` bijvoorbeeld aanvragen van IP-adressen 11.22.32.1 tot 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Nadat u klaar bent met het invoeren van parameterwaarden voor versleuteling, selecteert u een sleutel om te versleutelen (als u zowel een primaire als een back-upsleutel hebt gemaakt) in de lijst **Sleutel tot versleutelen.**
    
   6. Selecteer een versleutelingsversie in de lijst **Versleutelingsversie:** **V2** voor versie 2 of **V3** voor versie 3 (aanbevolen). 

   7. Selecteer **Versleutelen** om het token te genereren.

      Nadat het token is gegenereerd, wordt het weergegeven in het vak **Gegenereerde token.** Als u het token wilt gebruiken, wordt het toegevoegd als een querytekenreeks aan het einde van het bestand in uw URL-pad. Bijvoorbeeld `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Test eventueel uw token met het gereedschap decoderen, zodat u de parameters van uw token bekijken. Plak de tokenwaarde in het vak **Token naar Decoderen.** Selecteer de versleutelingssleutel die u wilt gebruiken in de lijst **Sleutel tot decoderen** en selecteer **Vervolgens Decoderen**.

      Nadat het token is gedecodeerd, worden de parameters weergegeven in het vak **Oorspronkelijke parameters.**

   9. Past eventueel het type antwoordcode aan dat wordt geretourneerd wanneer een aanvraag wordt geweigerd. Selecteer **Ingeschakeld**en selecteer vervolgens de antwoordcode in de lijst **Antwoordcode.** **Koptekstnaam** wordt automatisch ingesteld op **Locatie**. Selecteer **Opslaan** om de nieuwe antwoordcode te implementeren. Voor bepaalde antwoordcodes moet u ook de URL van uw foutpagina invoeren in het vak **Koptekstwaarde.** De **responscode van 403** (Verboden) is standaard geselecteerd. 

3. Selecteer Rules **Engine**onder **HTTP Large**. U gebruikt de regelsengine om paden te definiëren om de functie toe te passen, de tokenverificatiefunctie in te schakelen en extra tokenverificatiegerelateerde mogelijkheden in te schakelen. Zie [Verwijzing van de engine rules .](cdn-rules-engine-reference.md)

   1. Selecteer een bestaande regel of maak een nieuwe regel om het element of pad te definiëren waarvoor u tokenverificatie wilt toepassen. 
   2. Als u tokenverificatie op een regel wilt inschakelen, selecteert u **[Token Auth](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** in de lijst **Onderdelen** en selecteert u **Ingeschakeld**. Selecteer **Bijwerken** als u een regel bijwerkt of **Toevoegen** als u een regel maakt.
        
      ![CDN-regels engine token authenticatie inschakelen voorbeeld](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. In de regelsengine u ook extra tokenverificatiegerelateerde functies inschakelen. Als u een van de volgende functies wilt inschakelen, selecteert u deze in de lijst **Onderdelen** en selecteert u **Ingeschakeld**.
    
   - **[Token Auth Denial Code:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)** bepaalt het type reactie dat wordt geretourneerd aan een gebruiker wanneer een verzoek wordt geweigerd. Regels die hier zijn ingesteld, overschrijven de antwoordcode die is ingesteld in de sectie **Aangepaste ontkenningsafhandeling** op de verificatiepagina op basis van tokens.

   - **[Url-aanvraag voor token-auth negeren:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)** bepaalt of de URL die wordt gebruikt om het token te valideren, hoofdlettergevoelig is.

   - **[Token Auth-parameter:](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)** wijzigt de parameter token auth-querytekenreeks die wordt weergegeven in de gevraagde URL. 
        
     ![Voorbeeld van het voorbeeld van de instellingen voor verificatie van de cdn-regels-token](./media/cdn-token-auth/cdn-rules-engine2.png)

5. U uw token aanpassen door toegang te krijgen tot broncode in [GitHub.](https://github.com/VerizonDigital/ectoken)
   Beschikbare talen zijn onder andere:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Azure CDN-functies en providerprijzen

Zie [Azure CDN-productfuncties](cdn-features.md)voor informatie over functies. Zie Prijzen van [het Content Delivery Network voor](https://azure.microsoft.com/pricing/details/cdn/)informatie over prijzen.
