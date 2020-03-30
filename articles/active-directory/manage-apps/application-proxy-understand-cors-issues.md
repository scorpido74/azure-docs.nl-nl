---
title: Azure AD Application Proxy CORS-problemen begrijpen en oplossen
description: Biedt inzicht in CORS in Azure AD Application Proxy en hoe cors-problemen kunnen worden geïdentificeerd en opgelost.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025782"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Azure Active Directory Application Proxy CORS-problemen begrijpen en oplossen

[Cross-origin resource sharing (CORS)](https://www.w3.org/TR/cors/) kan soms uitdagingen opleveren voor de apps en API's die u publiceert via de Azure Active Directory Application Proxy. In dit artikel worden problemen en oplossingen voor Azure AD Application Proxy CORS besproken.

Browserbeveiliging voorkomt meestal dat een webpagina AJAX-verzoeken naar een ander domein kan indienen. Deze beperking wordt het *beleid van dezelfde oorsprong*genoemd en voorkomt dat een schadelijke site gevoelige gegevens van een andere site kan lezen. Soms wilt u echter dat andere sites uw web-API aanroepen. CORS is een W3C-standaard waarmee een server het beleid van dezelfde oorsprong kan versoepelen en sommige cross-origin-aanvragen toestaat terwijl andere worden afgewezen.

## <a name="understand-and-identify-cors-issues"></a>CorS-problemen begrijpen en identificeren

Twee URL's hebben dezelfde oorsprong als ze identieke schema's, hosts en poorten hebben[(RFC 6454),](https://tools.ietf.org/html/rfc6454)zoals:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

De volgende URL's hebben een andere oorsprong dan de vorige twee:

-   http:\//contoso.net - Ander domein
-   http:\//contoso.com:9000/foo.html - Andere poort
-   https:\//contoso.com/foo.html - Andere regeling
-   http:\//www.contoso.com/foo.html - Ander subdomein

Het beleid van dezelfde oorsprong voorkomt dat apps toegang krijgen tot bronnen van andere oorsprong, tenzij ze de juiste toegangsbeheerkoppen gebruiken. Als de CORS-headers afwezig of onjuist zijn, mislukken aanvragen voor cross-origine. 

U CORS-problemen identificeren met behulp van foutopsporingsprogramma's in de browser:

1. Start de browser en blader naar de web-app.
1. Druk op **F12** om de foutopsporingsconsole te brengen.
1. Probeer de transactie te reproduceren en bekijk het consolebericht. Een CORS-schending veroorzaakt een consolefout over de oorsprong.

In de volgende schermafbeelding heeft het selecteren van de knop\/Try **It** een CORS-foutbericht veroorzaakt dat https: /corswebclient-contoso.msappproxy.net niet is gevonden in de header Access-Control-Allow-Origin.

![CORS-probleem](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CORS daagt uit met Application Proxy

In het volgende voorbeeld wordt een typisch Azure AD Application Proxy CORS-scenario weergegeven. De interne server host een **CORSWebService-webAPI-controller** en een **CORSWebClient** die **CORSWebService aanroept.** Er is een AJAX-verzoek van **CORSWebClient** aan **CORSWebService.**

![Verzoek van dezelfde oorsprong](./media/application-proxy-understand-cors-issues/image1.png)

De CORSWebClient-app werkt wanneer u deze on-premises host, maar niet wordt geladen of fouten maakt wanneer deze worden gepubliceerd via Azure AD-toepassingsproxy. Als u de CORSWebClient- en CORSWebService-apps afzonderlijk als verschillende apps hebt gepubliceerd via Application Proxy, worden de twee apps op verschillende domeinen gehost. Een AJAX-verzoek van CORSWebClient aan CORSWebService is een verzoek om cross-origin, en het mislukt.

![Aanvraag Proxy CORS aanvraag](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Oplossingen voor application proxy CORS problemen

U het voorgaande CORS-probleem op verschillende manieren oplossen.

### <a name="option-1-set-up-a-custom-domain"></a>Optie 1: Een aangepast domein instellen

Gebruik een aangepast [azure AD-toepassingsproxydomein](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) om van dezelfde oorsprong te publiceren, zonder dat u wijzigingen hoeft aan te brengen in de oorsprong, code of kopteksten van apps. 

### <a name="option-2-publish-the-parent-directory"></a>Optie 2: De bovenliggende map publiceren

Publiceer de bovenliggende map van beide apps. Deze oplossing werkt vooral goed als u slechts twee apps op de webserver hebt. In plaats van elke app afzonderlijk te publiceren, u de algemene bovenliggende map publiceren, wat resulteert in dezelfde oorsprong.

In de volgende voorbeelden wordt de pagina Azure AD-toepassingsproxy voor de CORSWebClient-app weergegeven.  Wanneer de **interne URL** is ingesteld op *contoso.com/CORSWebClient,* kan de app geen succesvolle aanvragen indienen bij de *contoso.com/CORSWebService-map,* omdat deze cross-origine zijn. 

![App afzonderlijk publiceren](./media/application-proxy-understand-cors-issues/image4.png)

Stel in plaats daarvan de **interne URL** in om de bovenliggende map te publiceren, die zowel de *corswebclient-* als *de CORSWebService-mappen* bevat:

![Bovenliggende map publiceren](./media/application-proxy-understand-cors-issues/image5.png)

De resulterende app-URL's lossen het CORS-probleem effectief op:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Optie 3: HTTP-headers bijwerken

Voeg een aangepaste HTTP-antwoordkop toe aan de webservice om de oorsprongsaanvraag te matchen. Voor websites die worden uitgevoerd in Internet Information Services (IIS), gebruikt u IIS-beheer om de koptekst te wijzigen:

![Aangepaste antwoordkoptekst toevoegen in IIS-beheer](./media/application-proxy-understand-cors-issues/image6.png)

Voor deze wijziging zijn geen wijzigingen in de code vereist. U het verifiëren in de sporen van Fiddler:

**De kopteksttoevoeging plaatsen**\
HTTP/1.1 200 OK\
Cache-control: geen cache\
Pragma: no-cache\
Inhoudstype: tekst/effen; charset=utf-8\
Verloopt: -1\
Variëren: accepteren en coderen\
Server: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin:\:https /corswebclient-contoso.msappproxy.net**\
X-AspNet-versie: 4.0.30319\
X-Powered-By: ASP.NET\
Inhoudslengte: 17

### <a name="option-4-modify-the-app"></a>Optie 4: De app wijzigen

U uw app wijzigen om CORS te ondersteunen door de header Access-Control-Allow-Origin toe te voegen met de juiste waarden. De manier waarop u de koptekst toevoegt, is afhankelijk van de codetaal van de app. Het wijzigen van de code is de minst aanbevolen optie, omdat het de meeste inspanning vereist.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Optie 5: Verleng de levensduur van het toegangstoken

Sommige CORS-problemen kunnen niet worden opgelost, zoals wanneer uw app wordt omgeleid naar *login.microsoftonline.com* om te verifiëren en het toegangstoken verloopt. De CORS-oproep mislukt dan. Een tijdelijke oplossing voor dit scenario is om de levensduur van het toegangstoken te verlengen, om te voorkomen dat het tijdens de sessie van een gebruiker verloopt. Zie [Configureerbare tokenlevensduur in Azure AD](../develop/active-directory-configurable-token-lifetimes.md)voor meer informatie over hoe u dit doen.

## <a name="see-also"></a>Zie ook
- [Zelfstudie: Een on-premises toepassing voor externe toegang toevoegen via toepassingsproxy in Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Een Azure AD-toepassingsproxy-implementatie plannen](application-proxy-deployment-plan.md) 
- [Externe toegang tot on-premises toepassingen via Azure Active Directory Application Proxy](application-proxy.md) 
