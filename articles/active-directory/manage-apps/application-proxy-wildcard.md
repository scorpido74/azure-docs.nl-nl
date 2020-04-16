---
title: Wildcard-toepassingen in de Azure AD-toepassingsproxy
description: Meer informatie over het gebruik van Wildcard-toepassingen in de proxy van azure Active Directory-toepassingen.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/06/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e17cc4bf6f70901e3bb5d250f0a55f073ad8d99e
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406735"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Jokertekens in de proxy van Azure Active Directory-toepassing

In Azure Active Directory (Azure AD) kan het configureren van een groot aantal on-premises toepassingen snel onhandelbaar worden en onnodige risico's voor configuratiefouten met zich meebrengen als veel van hen dezelfde instellingen vereisen. Met [Azure AD Application Proxy](application-proxy.md)u dit probleem verhelpen door het publiceren van wildcard-toepassingen te gebruiken om veel toepassingen tegelijk te publiceren en te beheren. Dit is een oplossing waarmee u:

- Vereenvoudig uw administratieve overhead
- Het aantal potentiële configuratiefouten verminderen
- Uw gebruikers in staat stellen veilig toegang te krijgen tot meer bronnen

In dit artikel vindt u de informatie die u nodig hebt om het publiceren van wildcardtoepassingen in uw omgeving te configureren.

## <a name="create-a-wildcard-application"></a>Een wildcardtoepassing maken

U een wildcardtoepassing (*) maken als u een groep toepassingen met dezelfde configuratie hebt. Potentiële kandidaten voor een wildcard-aanvraag zijn toepassingen die de volgende instellingen delen:

- De groep gebruikers die er toegang toe heeft
- De SSO-methode
- Het toegangsprotocol (http, https)

U toepassingen met jokertekens publiceren als beide interne en externe URL's in de volgende indeling staan:

> http(s)://*. \<domein\>

Bijvoorbeeld: `http(s)://*.adventure-works.com`.

Hoewel de interne en externe URL's verschillende domeinen kunnen gebruiken, moeten ze als best practice hetzelfde zijn. Bij het publiceren van de toepassing ziet u een fout als een van de URL's geen wildcard heeft.

Als u extra toepassingen met verschillende configuratie-instellingen hebt, moet u deze uitzonderingen publiceren als afzonderlijke toepassingen om de standaardinstellingen voor de joker te overschrijven. Toepassingen zonder wildcard hebben altijd voorrang op wildcardtoepassingen. Vanuit het oogpunt van configuratie zijn dit "gewoon" reguliere toepassingen.

Het maken van een wildcardtoepassing is gebaseerd op dezelfde [publicatiestroom](application-proxy-add-on-premises-application.md) voor toepassingen die beschikbaar is voor alle andere toepassingen. Het enige verschil is dat u een wildcard opneemt in de URL's en mogelijk de SSO-configuratie.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat je aan deze vereisten voldoet om aan de slag te gaan.

### <a name="custom-domains"></a>Aangepaste domeinen

Hoewel [aangepaste domeinen](application-proxy-configure-custom-domain.md) optioneel zijn voor alle andere toepassingen, zijn ze een voorwaarde voor wildcardtoepassingen. Voor het maken van aangepaste domeinen moet u:

1. Maak een geverifieerd domein binnen Azure.
1. Upload een TLS/SSL-certificaat in de PFX-indeling naar uw toepassingsproxy.

U moet overwegen een wildcardcertificaat te gebruiken dat overeenkomt met de toepassing die u wilt maken. U ook een certificaat gebruiken waarin alleen specifieke toepassingen worden vermeld. In dit geval zijn alleen de toepassingen in het certificaat toegankelijk via deze wildcardtoepassing.

Om veiligheidsredenen is dit een harde vereiste en we zullen geen wildcards ondersteunen voor toepassingen die geen aangepast domein kunnen gebruiken voor de externe URL.

### <a name="dns-updates"></a>DNS-updates

Wanneer u aangepaste domeinen gebruikt, moet u een DNS-vermelding maken met `*.adventure-works.com`een CNAME-record voor de externe URL (bijvoorbeeld ) die wijst op de externe URL van het eindpunt van de toepassingsproxy. Voor wildcardtoepassingen moet de CNAME-record wijzen op de relevante externe URL's:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Om te bevestigen dat u uw CNAME correct hebt geconfigureerd, u [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) `expenses.adventure-works.com`gebruiken op een van de doeleindpunten, bijvoorbeeld.  Uw antwoord moet de reeds`<yourAADTenantId>.tenant.runtime.msappproxy.net`genoemde alias ( ).

## <a name="considerations"></a>Overwegingen

Hier volgen enkele overwegingen die u moet meemaken voor wildcardtoepassingen.

### <a name="accepted-formats"></a>Geaccepteerde indelingen

Voor wildcardtoepassingen moet de **interne** URL `http(s)://*.<domain>`worden opgemaakt als .

![Voor interne URL gebruikt u de indeling http(s)://*. \<domein>](./media/application-proxy-wildcard/22.png)

Wanneer u een **externe URL**configureert, moet u de volgende indeling gebruiken:`https://*.<custom domain>`

![Voor externe URL gebruikt u de indeling https://*. \<aangepaste domein>](./media/application-proxy-wildcard/21.png)

Andere posities van de wildcard, meerdere wildcards of andere regex-tekenreeksen worden niet ondersteund en veroorzaken fouten.

### <a name="excluding-applications-from-the-wildcard"></a>Aanvragen uitsluiten van de wildcard

U een toepassing uitsluiten van de wildcardtoepassing door

- De uitzonderingstoepassing publiceren als gewone toepassing
- De wildcard alleen inschakelen voor specifieke toepassingen via uw DNS-instellingen

Het publiceren van een toepassing als reguliere toepassing is de voorkeursmethode om een toepassing uit te sluiten van een wildcard. U moet de uitgesloten toepassingen vóór de wildcardtoepassingen publiceren om ervoor te zorgen dat uw uitzonderingen vanaf het begin worden afgedwongen. De meest specifieke toepassing heeft altijd voorrang `budgets.finance.adventure-works.com` – een toepassing `*.finance.adventure-works.com`die wordt gepubliceerd als voorrang `*.adventure-works.com`op de toepassing , die op zijn beurt voorrang heeft op de toepassing .

U de wildcard ook beperken tot alleen werken voor specifieke toepassingen via uw DNS-beheer. Als aanbevolen praktijk moet u een CNAME-vermelding maken die een wildcard bevat en overeenkomt met de indeling van de externe URL die u hebt geconfigureerd. U echter in plaats daarvan specifieke toepassings-URL's naar de jokertekens wijzen. Bijvoorbeeld, in `*.adventure-works.com`plaats `hr.adventure-works.com`van `expenses.adventure-works.com` `travel.adventure-works.com individually` , `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`punt , en naar .

Als u deze optie gebruikt, hebt u ook `AppId.domain`een andere `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`CNAME-vermelding nodig voor de waarde, bijvoorbeeld, die ook naar dezelfde locatie wijst. U de **AppId** vinden op de pagina met toepassingseigenschappen van de wildcardtoepassing:

![De toepassings-id zoeken op de eigenschappenpagina van de app](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>De URL van de startpagina instellen voor het deelvenster MyApps

De wildcard-toepassing wordt weergegeven met slechts één tegel in het [deelvenster MyApps.](https://myapps.microsoft.com) Standaard is deze tegel verborgen. Ga als lid van het nieuws om de tegel weer te geven en gebruikers op een specifieke pagina te laten landen:

1. Volg de richtlijnen voor [het instellen van een URL van een startpagina](application-proxy-configure-custom-home-page.md).
1. **Toepassing weergeven** **instellen op true** op de pagina toepassingseigenschappen.

### <a name="kerberos-constrained-delegation"></a>Kerberos beperkte delegatie

Voor toepassingen die [kerberos constrained delegation (KCD) als SSO-methode](application-proxy-configure-single-sign-on-with-kcd.md)gebruiken, kan de SPN die voor de SSO-methode wordt vermeld, ook een wildcard nodig hebben. De SPN zou bijvoorbeeld `HTTP/*.adventure-works.com`kunnen zijn: . U moet nog steeds de afzonderlijke SPN's hebben geconfigureerd `HTTP/expenses.adventure-works.com and HTTP/travel.adventure-works.com`op uw backendservers (bijvoorbeeld).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Algemene wildcardtoepassing

In dit scenario hebt u drie verschillende toepassingen die u wilt publiceren:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alle drie de toepassingen:

- Worden gebruikt door al uw gebruikers
- *Geïntegreerde Windows-verificatie gebruiken*
- Dezelfde eigenschappen hebben

U de wildcardtoepassing publiceren met behulp van de stappen die zijn beschreven in [Publicatietoepassingen met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). In dit scenario wordt ervan uitgegaan:

- Een tenant met de volgende id:`000aa000-11b1-2ccc-d333-4444eee4444e`
- Er is een `adventure-works.com` geverifieerd domein geconfigureerd.
- Er is een `*.adventure-works.com` **CNAME-vermelding** gemaakt waarnaar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` wordt verwijst.

Na de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)maakt u een nieuwe toepassingsproxytoepassing in uw tenant. In dit voorbeeld bevindt de joker in de volgende velden:

- Interne URL:

    ![Voorbeeld: Jokerteken in interne URL](./media/application-proxy-wildcard/42.png)

- Externe URL:

    ![Voorbeeld: Jokerteken in externe URL](./media/application-proxy-wildcard/43.png)

- Interne toepassing SPN:

    ![Voorbeeld: Jokerteken in SPN-configuratie](./media/application-proxy-wildcard/44.png)

Door de wildcardtoepassing te publiceren, u nu toegang krijgen tot uw drie `travel.adventure-works.com`toepassingen door te navigeren naar de URL's die u gewend bent (bijvoorbeeld).

De configuratie implementeert de volgende structuur:

![Toont de structuur die is geïmplementeerd door de voorbeeldconfiguratie](./media/application-proxy-wildcard/05.png)

| Kleur | Beschrijving |
| ---   | ---         |
| Blauw  | Toepassingen die expliciet zijn gepubliceerd en zichtbaar zijn in de Azure-portal. |
| Grijs  | Toepassingen die u toegankelijk maken via de bovenliggende toepassing. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Algemene wildcardtoepassing met uitzondering

In dit scenario hebt u naast de drie `finance.adventure-works.com`algemene toepassingen een andere toepassing, die alleen toegankelijk zou moeten zijn voor de afdeling Financiën. Met de huidige toepassingsstructuur zou uw financiële aanvraag toegankelijk zijn via de wildcard-toepassing en door alle medewerkers. Als u dit wilt wijzigen, sluit u uw toepassing uit van uw wildcard door Finance te configureren als een afzonderlijke toepassing met meer beperkende machtigingen.

U moet ervoor zorgen dat er een `finance.adventure-works.com` CNAME-records bestaan die naar het specifieke eindpunt van de toepassing verwijst, opgegeven op de pagina Application Proxy voor de toepassing. Voor dit `finance.adventure-works.com` scenario `https://finance-awcycles.msappproxy.net/`wordt gewezen op .

Na de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)vereist dit scenario de volgende instellingen:

- In de **interne URL**stelt u **financiën** in in plaats van een wildcard.

    ![Voorbeeld: Financiering instellen in plaats van een wildcard in interne URL](./media/application-proxy-wildcard/52.png)

- In de **externe URL**stelt u **financiering** in in plaats van een wildcard.

    ![Voorbeeld: Financiering instellen in plaats van een wildcard in externe URL](./media/application-proxy-wildcard/53.png)

- Interne toepassing SPN u **instelt financiën** in plaats van een wildcard.

    ![Voorbeeld: Financiering instellen in plaats van een wildcard in SPN-configuratie](./media/application-proxy-wildcard/54.png)

Met deze configuratie wordt het volgende scenario geïmplementeerd:

![Toont de configuratie die is geïmplementeerd door het voorbeeldscenario](./media/application-proxy-wildcard/09.png)

Want `finance.adventure-works.com` is een meer `*.adventure-works.com`specifieke URL dan , het heeft voorrang. Gebruikers die `finance.adventure-works.com` navigeren om de ervaring te hebben opgegeven in de toepassing Financiële resources. In dit geval hebben alleen financiële `finance.adventure-works.com`medewerkers toegang tot .

Als u meerdere aanvragen hebt gepubliceerd `finance.adventure-works.com` voor financiën en u als geverifieerd `*.finance.adventure-works.com`domein hebt, u een andere wildcardtoepassing publiceren. Omdat dit specifieker is `*.adventure-works.com`dan het algemene , heeft het voorrang als een gebruiker toegang heeft tot een toepassing in het financiële domein.

## <a name="next-steps"></a>Volgende stappen

- Zie Werken met aangepaste [domeinen in Azure AD Application Proxy](application-proxy-configure-custom-domain.md)voor meer informatie over aangepaste **domeinen.**
- Zie Toepassingen publiceren met [Azure AD-toepassingsproxy voor](application-proxy-add-on-premises-application.md) meer informatie over **publicatietoepassingen**
