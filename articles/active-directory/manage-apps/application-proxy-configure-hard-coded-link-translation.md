---
title: Koppelingen en URL's van Azure AD-app proxy vertalen | Microsoft Documenten
description: Dekt de basisprincipes van Azure AD Application Proxy-connectors.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa0dc2081aff5a24fb830b756131cccd5c6ce810
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69533696"
---
# <a name="redirect-hardcoded-links-for-apps-published-with-azure-ad-application-proxy"></a>Hardcoded koppelingen omleiden voor apps die zijn gepubliceerd met Azure AD-toepassingsproxy

Azure AD Application Proxy maakt uw on-premises apps beschikbaar voor gebruikers die op afstand of op hun eigen apparaten staan. Sommige apps zijn echter ontwikkeld met lokale koppelingen die in de HTML zijn ingebed. Deze koppelingen werken niet goed wanneer de app op afstand wordt gebruikt. Wanneer u meerdere on-premises toepassingen naar elkaar wijst, verwachten uw gebruikers dat de koppelingen blijven werken wanneer ze niet op kantoor zijn. 

De beste manier om ervoor te zorgen dat koppelingen zowel binnen als buiten uw bedrijfsnetwerk hetzelfde werken, is door de externe URL's van uw apps te configureren als dezelfde als hun interne URL's. Gebruik [aangepaste domeinen](application-proxy-configure-custom-domain.md) om uw externe URL's te configureren om uw bedrijfsdomeinnaam te hebben in plaats van het standaardproxydomein van de toepassing.


Als u geen aangepaste domeinen in uw tenant gebruiken, zijn er verschillende andere opties voor het bieden van deze functionaliteit. Al deze zijn ook compatibel met aangepaste domeinen en elkaar, zodat u aangepaste domeinen en andere oplossingen configureren indien nodig.

> [!NOTE]
> Linkvertaling wordt niet ondersteund voor hard-coded interne URL's gegenereerd via Javascript.

**Optie 1: Gebruik de beheerde browser of Microsoft Edge** : deze oplossing is alleen van toepassing als u van plan bent gebruikers toegang te geven tot de toepassing via de Intune Managed Browser of Microsoft Edge Browser. Het zal alle gepubliceerde URL's verwerken. 

**Optie 2: Gebruik de MyApps-extensie** - Deze oplossing vereist dat gebruikers een browserextensie aan de clientzijde installeren, maar het zal alle gepubliceerde URL's verwerken en werken met de meest populaire browsers. 

**Optie 3: Gebruik de instelling voor koppelingsvertaling** - Dit is een beheerzijdedie onzichtbaar is voor gebruikers. Het zal echter alleen URL's verwerken in HTML en CSS.   

Deze drie functies houden uw links werken, ongeacht waar uw gebruikers zijn. Wanneer u apps hebt die rechtstreeks naar interne eindpunten of poorten wijzen, u deze interne URL's toewijzen aan de gepubliceerde URL's van externe toepassingsproxy.When you have apps that point directly to internal endpoints or ports, you can map these internal URLLs to the published external Application Proxy URLLs. 

 
> [!NOTE]
> De laatste optie is alleen voor tenants die om welke reden dan ook geen aangepaste domeinen kunnen gebruiken om dezelfde interne en externe URL's voor hun apps te hebben. Voordat u deze functie inschakelt, moet u zien of [aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md) voor u kunnen werken. 
> 
> Of als de toepassing die u met koppelingsvertaling moet configureren SharePoint is, [raadpleegt u Alternatieve toegangstoewijzingen configureren voor SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx) voor een andere benadering van toewijzingskoppelingen. 

 
### <a name="option-1-intune-managed-browser-and-microsoft-edge-integration"></a>Optie 1: Intune Managed Browser en Microsoft Edge Integration 

U de Intune Managed Browser of Microsoft Edge gebruiken om uw toepassing en inhoud verder te beschermen. Als u deze oplossing wilt gebruiken, moet u gebruikers vereisen/aanbevelen toegang te krijgen tot de toepassing via de Intune Managed Browser. Alle interne URL's die met application proxy worden gepubliceerd, worden door de beheerde browser herkend en doorgestuurd naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hard-coded interne URL's werken, en als een gebruiker naar de browser gaat en de interne URL direct typt, werkt het zelfs als de gebruiker op afstand is.  

Zie de documentatie [van beheerde browser](https://docs.microsoft.com/intune/app-configuration-managed-browser) voor meer informatie, inclusief het configureren van deze optie.  

### <a name="option-2-myapps-browser-extension"></a>Optie 2: MyApps-browserextensie 

Met de MyApps Browser Extensie worden alle interne URL's gepubliceerd met Application Proxy herkend door de extensie en doorgestuurd naar de bijbehorende externe URL. Dit zorgt ervoor dat alle hard-coded interne URL's werken, en als een gebruiker naar de adresbalk van de browser gaat en de interne URL direct typt, werkt deze zelfs als de gebruiker op afstand is.  

Om deze functie te kunnen gebruiken, moet de gebruiker de extensie downloaden en ingelogd zijn. Er is geen andere configuratie nodig voor beheerders of de gebruikers. 

Zie de documentatie van de [MyApps-browserextensie](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access#download-and-install-the-my-apps-secure-sign-in-extension) voor meer informatie, inclusief het configureren van deze optie.

### <a name="option-3-link-translation-setting"></a>Optie 3: Instelling voor koppelingsvertaling 

Wanneer koppelingsvertaling is ingeschakeld, zoekt de application proxy-service door HTML en CSS naar gepubliceerde interne koppelingen en vertaalt deze zodat uw gebruikers een ononderbroken ervaring krijgen. Het gebruik van de MyApps Browser Extensie heeft de voorkeur boven de Link Translation Setting, omdat het een meer performante ervaring geeft aan gebruikers.

> [!NOTE]
> Als u optie 2 of 3 gebruikt, moet slechts één van deze tegelijk worden ingeschakeld.

## <a name="how-link-translation-works"></a>Hoe linkvertaling werkt

Na verificatie, wanneer de proxyserver de toepassingsgegevens doorgeeft aan de gebruiker, scant Application Proxy de toepassing op hardcoded koppelingen en vervangt deze door hun respectievelijke, gepubliceerde externe URL's.

Application Proxy gaat ervan uit dat toepassingen zijn gecodeerd in UTF-8. Als dat niet het geval is, geeft u het coderingstype op in een http-antwoordkop, zoals `Content-Type:text/html;charset=utf-8`.

### <a name="which-links-are-affected"></a>Welke links worden beïnvloed?

De functie voor linkvertaling zoekt alleen naar koppelingen in codetags in de behuizing van een app. Application Proxy heeft een aparte functie voor het vertalen van cookies of URL's in headers. 

Er zijn twee veelvoorkomende typen interne koppelingen in on-premises toepassingen:

- **Relatieve interne koppelingen** die verwijzen naar een gedeelde `/claims/claims.html`bron in een lokale bestandsstructuur zoals . Deze koppelingen werken automatisch in apps die worden gepubliceerd via Application Proxy en blijven werken met of zonder linkvertaling. 
- **Hardcoded interne links** naar andere `http://expenses` on-premises `http://expenses/logo.jpg`apps zoals of gepubliceerde bestanden zoals . De functie voor koppelingsvertaling werkt op hardcoded interne koppelingen en wijzigt deze om te verwijzen naar de externe URL's die externe gebruikers moeten doorlopen.

De volledige lijst met HTML-codetags waarvoor toepassingsproxy koppelingsvertaling ondersteunt, omvat:
* a
* audio
* base
* knop
* div
* Insluiten
* Formulier
* Frame
* Hoofd
* html
* iframe
* afbeelding
* input
* koppelen
* Menuitem
* Meta
* object
* uit
* source
* bijhouden
* video

Bovendien wordt binnen CSS ook het KENMERK URL vertaald.

### <a name="how-do-apps-link-to-each-other"></a>Hoe koppelen apps aan elkaar?

Linkvertaling is ingeschakeld voor elke toepassing, zodat u controle hebt over de gebruikerservaring op het niveau per app. Schakel linkvertaling voor een app in wanneer u de koppelingen *van* die app wilt vertalen, geen koppelingen *naar* die app. 

Stel dat u drie toepassingen hebt gepubliceerd via Application Proxy die allemaal met elkaar zijn gekoppeld: voordelen, onkosten en reizen. Er is een vierde app, Feedback, die niet wordt gepubliceerd via Application Proxy.

Wanneer u koppelingsvertaling inschakelt voor de app Voordelen, worden de koppelingen naar Onkosten en Reizen doorgestuurd naar de externe URL's voor die apps, maar wordt de koppeling naar feedback niet doorgestuurd omdat er geen externe URL is. Koppelingen van onkosten en reis terug naar voordelen werken niet, omdat linkvertaling niet is ingeschakeld voor deze twee apps.

![Koppelingen van voordelen naar andere apps wanneer linkvertaling is ingeschakeld](./media/application-proxy-configure-hard-coded-link-translation/one_app.png)

### <a name="which-links-arent-translated"></a>Welke links worden niet vertaald?

Om de prestaties en beveiliging te verbeteren, worden sommige koppelingen niet vertaald:

- Koppelingen die niet in codetags zitten. 
- Koppelingen niet in HTML of CSS. 
- Koppelingen in URL-gecodeerde indeling.
- Interne links geopend van andere programma's. Links die via e-mail of chatbericht worden verzonden of in andere documenten zijn opgenomen, worden niet vertaald. De gebruikers moeten weten om naar de externe URL te gaan.

Als u een van deze twee scenario's moet ondersteunen, gebruikt u dezelfde interne en externe URL's in plaats van koppelingsvertaling.  

## <a name="enable-link-translation"></a>Koppelingsvertaling inschakelen

Aan de slag met linkvertaling is net zo eenvoudig als op een knop klikken:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als beheerder.
2. Ga naar **Azure Active Directory** > **Enterprise-toepassingen** > **Alle toepassingen** > de app selecteren die u wilt beheren > **toepassingsproxy.**
3. **Url's vertalen in de toepassingstekst** omzetten in **Ja**.

   ![Ja selecteren om URL's in toepassingstekst te vertalen](./media/application-proxy-configure-hard-coded-link-translation/select_yes.png)
4. Klik op **Opslaan** om uw wijzigingen toe te passen.

Wanneer uw gebruikers nu toegang hebben tot deze toepassing, scant de proxy automatisch naar interne URL's die zijn gepubliceerd via Application Proxy op uw tenant.

## <a name="send-feedback"></a>Feedback verzenden

We willen uw hulp om deze functie te laten werken voor al uw apps. We zoeken meer dan 30 tags in HTML en CSS. Als u een voorbeeld hebt van gegenereerde koppelingen die niet worden vertaald, stuurt u een codefragment naar [feedback van toepassingsproxy](mailto:aadapfeedback@microsoft.com). 

## <a name="next-steps"></a>Volgende stappen
[Aangepaste domeinen met Azure AD-toepassingsproxy gebruiken](application-proxy-configure-custom-domain.md) om dezelfde interne en externe URL te hebben

[Alternatieve toegangstoewijzingen configureren voor SharePoint 2013](https://technet.microsoft.com/library/cc263208.aspx)
