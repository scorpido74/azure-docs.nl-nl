---
author: baanders
description: bestand insluiten voor mogelijke aanvullende vereisten in azure Digital Apparaatdubbels Setup
ms.service: digital-twins
ms.topic: include
ms.date: 7/22/2020
ms.author: baanders
ms.openlocfilehash: 230304677b78f00b2d1288c846f8bf704cd8a497
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407445"
---
Het is mogelijk dat uw organisatie aanvullende acties vereist van abonnements eigenaren om een app-registratie te kunnen instellen (en dus om het instellen van een bruikbaar Azure Digital Apparaatdubbels-exemplaar te volt ooien). De vereiste stappen kunnen variëren afhankelijk van de specifieke instellingen van uw organisatie.

Hier volgen enkele veelvoorkomende mogelijke activiteiten die een eigenaar mogelijk moet uitvoeren. Deze en andere bewerkingen kunnen worden uitgevoerd vanaf de pagina [*Azure AD-App registraties*](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) in de Azure Portal.
* Toestemming geven voor de beheerder voor de registratie van de app. Uw organisatie heeft mogelijk *toestemming van de beheerder nodig* die wereld wijd is ingeschakeld in azure AD voor alle app-registraties in uw abonnement. Als dit het geval is, moet de eigenaar deze knop voor uw bedrijf selecteren op de pagina *API-machtigingen* van de app-registratie om de app-registratie geldig te maken:

    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/grant-admin-consent.png" alt-text="Portal weergave van de knop ' toestemming verlenen aan beheerder ' onder API-machtigingen":::
  - Als toestemming is verleend, moet de vermelding voor Azure Digital Apparaatdubbels de *status* waarde van _verleende voor **(uw bedrijf)** _ weer geven
   
    :::image type="content" source="../articles/digital-twins/media/how-to-set-up-instance/portal/granted-admin-consent-done.png" alt-text="Portal weergave van de beheerder toestemming verleend voor het bedrijf onder API-machtigingen":::
* Open bare client toegang activeren
* Specifieke antwoord-Url's instellen voor web-en bureaublad toegang
* Impliciete OAuth2-verificatie stromen toestaan

Zie [*een toepassing registreren bij het micro soft-identiteits platform*](https://docs.microsoft.com/graph/auth-register-app-v2)voor meer informatie over app-registratie en de verschillende installatie opties.

U hebt nu een Azure Digital Apparaatdubbels-exemplaar klaar om te gaan, machtigingen hebt toegewezen om het te beheren en om machtigingen in te stellen voor een client-app om deze te openen.