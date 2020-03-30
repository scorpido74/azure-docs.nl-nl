---
author: zhangmanling
ms.service: azure-cdn
ms.topic: include
ms.date: 11/21/2018
ms.author: mazha
ms.openlocfilehash: 41f2d4540f665137d34d262546cdc1a2edfbae3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77608752"
---
## <a name="prerequisites"></a>Vereisten
Voordat u CDN-beheercode schrijft, moet u enige voorbereiding doen om de code in staat te stellen te communiceren met Azure Resource Manager. Om deze voorbereiding te doen, moet u:

* Een resourcegroep maken om het CDN-profiel te bevatten dat in deze zelfstudie is gemaakt
* Azure Active Directory configureren om verificatie voor de toepassing te bieden
* Machtigingen toepassen op de brongroep, zodat alleen geautoriseerde gebruikers van uw Azure AD-tenant kunnen communiceren met het CDN-profiel

### <a name="creating-the-resource-group"></a>De resourcegroep maken
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Klik op **Een resource maken**.
3. Zoek naar **resourcegroep** en klik in het groepsvenster Resource op **Maken**.

    ![Een nieuwe resourcegroep maken](./media/cdn-app-dev-prep/cdn-new-rg-1-include.png)
3. Geef de naam *van de brongroep CdnConsoleTutorial*.  Selecteer uw abonnement en kies een locatie bij u in de buurt.  U desgewenst op het selectievakje **Vastmaken aan dashboard** klikken om de brongroep vast te maken aan het dashboard in de portal.  Pinnen maakt het makkelijker om later te vinden.  Nadat u uw selecties hebt gemaakt, klikt u op **Maken**.

    ![De resourcegroep een naam noemen](./media/cdn-app-dev-prep/cdn-new-rg-2-include.png)
4. Nadat de resourcegroep is gemaakt, u deze vinden door op **Bladeren**te klikken en **vervolgens op Resourcegroepen**.  Als u deze wilt openen, klikt u op de brongroep.  Noteer uw **abonnements-id**. We hebben het later nodig.

    ![De resourcegroep een naam noemen](./media/cdn-app-dev-prep/cdn-subscription-id-include.png)

### <a name="creating-the-azure-ad-application-and-applying-permissions"></a>De Azure AD-toepassing maken en machtigingen toepassen
Er zijn twee benaderingen voor app-verificatie met Azure Active Directory: individuele gebruikers of een serviceprincipal. Een serviceprincipal is vergelijkbaar met een serviceaccount in Windows.  In plaats van een bepaalde gebruiker machtigingen te geven voor interactie met de CDN-profielen, worden in plaats daarvan machtigingen verleend aan de serviceprincipal.  Serviceprincipals worden meestal gebruikt voor geautomatiseerde, niet-interactieve processen.  Hoewel deze zelfstudie een interactieve console-app schrijft, richten we ons op de serviceprincipal-benadering.

Het maken van een serviceprincipal bestaat uit verschillende stappen, waaronder het maken van een Azure Active Directory-toepassing.  Om het te maken, gaan we [deze tutorial volgen.](../articles/active-directory/develop/howto-create-service-principal-portal.md)

> [!IMPORTANT]
> Zorg ervoor dat u alle stappen in de [gekoppelde zelfstudie](../articles/active-directory/develop/howto-create-service-principal-portal.md)volgt.  Het is *belangrijk* dat u het precies zo invult zoals beschreven.  Zorg ervoor dat u uw **tenant-id,** **tenantdomeinnaam** (meestal een *.onmicrosoft.com-domein* oplet, tenzij u een aangepast domein), **client-id**en **clientverificatiesleutel**hebt opgegeven, omdat we deze informatie later nodig hebben.  Wees voorzichtig met het bewaken van uw **client-ID** en **client authenticatie sleutel,** omdat deze referenties kunnen worden gebruikt door iedereen om bewerkingen uit te voeren als de service principal.
>
> Wanneer u bij de stap met de naam Configureren met meerdere tenant-toepassing komt, selecteert u **Nee**.
>
> Wanneer u de stap Aangaat Als u de [toepassing aan een rol toewijst,](../articles/active-directory/develop/howto-create-service-principal-portal.md#assign-a-role-to-the-application)gebruikt u de eerder gemaakte brongroep, *CdnConsoleTutorial*, maar wijst u in plaats van de rol **Reader** de rol **CDN-profielbijdrager toe.**  Nadat u de toepassing de rol **CDN-profielbijdrager aan** uw resourcegroep hebt toegewezen, gaat u terug naar deze zelfstudie. 
>
>

Nadat u de serviceprincipal hebt gemaakt en de rol **CDN-profielbijdrager** hebt toegewezen, moet het blad **Gebruikers** voor uw resourcegroep er ongeveer hetzelfde uitzien als de volgende afbeelding.

![Gebruikers blad](./media/cdn-app-dev-prep/cdn-service-principal-include.png)

### <a name="interactive-user-authentication"></a>Interactieve gebruikersverificatie
Als u in plaats van een serviceprincipal liever interactieve individuele gebruikersverificatie hebt, is het proces vergelijkbaar met dat voor een serviceprincipal.  In feite moet je dezelfde procedure te volgen, maar breng een paar kleine wijzigingen.

> [!IMPORTANT]
> Volg deze volgende stappen alleen als u kiest voor individuele gebruikersverificatie in plaats van een serviceprincipal.
>
>

1. Kies bij het maken van uw toepassing in plaats van **webtoepassing**de optie **Native-toepassing**.

    ![Systeemeigen toepassing](./media/cdn-app-dev-prep/cdn-native-application-include.png)
2. Op de volgende pagina wordt u gevraagd om een **omleiding URI**.  De URI wordt niet gevalideerd, maar onthoud wat je hebt ingevoerd. Je hebt het later nodig.
3. Het is niet nodig om een **clientverificatiesleutel**te maken.
4. In plaats van een serviceprincipal toe te wijzen aan de rol **CDN-profielbijdrager,** gaan we individuele gebruikers of groepen toewijzen.  In dit voorbeeld u zien dat ik *CDN Demo-gebruiker* heb toegewezen aan de rol **CDN-profielbijdrager.**  

    ![Individuele gebruikerstoegang](./media/cdn-app-dev-prep/cdn-aad-user-include.png)
