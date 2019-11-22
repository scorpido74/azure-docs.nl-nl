---
title: Joker tekens voor toepassingen in de Azure AD-toepassingsproxy
description: Informatie over het gebruik van Wildcard-toepassingen in de Azure Active Directory-toepassingsproxy.
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
ms.openlocfilehash: c5a9e7be5f582051e03cba08733fcbfa697cc8f5
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275034"
---
# <a name="wildcard-applications-in-the-azure-active-directory-application-proxy"></a>Wildcard-toepassingen in de Azure Active Directory-toepassingsproxy

In Azure Active Directory (Azure AD), het configureren van een groot aantal on-premises toepassingen kunnen snel worden onbeheersbare en onnodige risico's op configuratiefouten introduceert als veel van deze nodig hebt met dezelfde instellingen. Met [Azure AD-toepassingsproxy](application-proxy.md), u kunt dit probleem oplossen met behulp van jokertekens toepassing publiceren om te publiceren en beheren van veel toepassingen in één keer. Dit is een oplossing waarmee u kunt:

- Vereenvoudig uw administratieve overhead
- Verminder het aantal mogelijke fouten in de configuratie
- Uw gebruikers veilig toegang krijgen tot meer resources

In dit artikel biedt u de informatie die u wilt configureren van jokertekens toepassing publiceren in uw omgeving.

## <a name="create-a-wildcard-application"></a>Een wildcard-toepassing maken

U kunt een (*)-jokertekentoepassing maken als u een groep van toepassingen met dezelfde configuratie hebben. Mogelijke kandidaten zijn voor een jokertekentoepassing zijn toepassingen delen van de volgende instellingen:

- De groep gebruikers toegang hebben tot deze
- De methode voor eenmalige aanmelding
- De access-protocol (http, https)

U kunt toepassingen met jokertekens publiceren als zowel de interne en externe URL's in de volgende indeling:

> HTTP (s) :/ / *. \<domein\>

Bijvoorbeeld: `http(s)://*.adventure-works.com`.

De interne en externe URL's kunnen verschillende domeinen gebruiken als een best practice, moeten ze niet hetzelfde zijn. Wanneer de toepassing publiceert, ziet u een fout als een van de URL's niet een jokerteken.

Als u aanvullende toepassingen met verschillende configuratie-instellingen hebt, moet u deze uitzonderingen publiceren als afzonderlijke toepassingen te overschrijven de standaardinstellingen voor het jokerteken. Toepassingen zonder een jokerteken altijd voorrang op wildcard-toepassingen. Dit zijn "net" reguliere toepassingen vanuit het perspectief configuratie.

Het maken van een jokertekentoepassing is gebaseerd op dezelfde [toepassing publiceren stroom](application-proxy-add-on-premises-application.md) die is beschikbaar voor alle andere toepassingen. Het enige verschil is dat u een jokerteken in de URL's en mogelijk de SSO-configuratie opnemen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan deze vereisten hebt voldaan om aan de slag te gaan.

### <a name="custom-domains"></a>Aangepaste domeinen

Terwijl [aangepaste domeinen](application-proxy-configure-custom-domain.md) zijn optioneel voor alle andere toepassingen, zijn een vereiste voor wildcard-toepassingen. Het maken van aangepaste domeinen, moet u:

1. Een geverifieerd domein maken in Azure.
1. Upload een SSL-certificaat in de PFX-indeling naar de toepassingsproxy.

U moet een certificaat met jokertekens overwegen zodat deze overeenkomen met de toepassing die u van plan bent om te maken. U kunt ook kunt u ook een certificaat met alleen een lijst met specifieke toepassingen. In dit geval zijn alleen de toepassingen die worden vermeld in het certificaat toegankelijk via deze jokertekentoepassing.

Uit veiligheidsoverwegingen, dit is een vereiste voor vaste en jokertekens wordt niet ondersteund voor toepassingen die een aangepast domein kunnen worden gebruikt voor de externe URL.

### <a name="dns-updates"></a>DNS-updates

Wanneer u aangepaste domeinen, moet u een DNS-vermelding maken met een CNAME-record voor de externe URL (bijvoorbeeld `*.adventure-works.com`) die verwijst naar de externe URL van het eindpunt van de application proxy. Voor jokertekentoepassingen moet de CNAME-record om te verwijzen naar de desbetreffende externe URL's:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Om te bevestigen dat u de CNAME juist hebt geconfigureerd, kunt u [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) op een van de doeleindpunten, bijvoorbeeld `expenses.adventure-works.com`.  Uw antwoord moet de al is vermeld alias bevatten (`<yourAADTenantId>.tenant.runtime.msappproxy.net`).

## <a name="considerations"></a>Overwegingen

Hier volgen enkele overwegingen waar u rekening mee moet houden voor Joker teken toepassingen.

### <a name="accepted-formats"></a>Geaccepteerde indelingen

Voor jokertekentoepassingen, de **interne URL** moeten zijn opgemaakt als `http(s)://*.<domain>`.

![Gebruik voor interne URL de notatie http (s)://*.\<domein >](./media/application-proxy-wildcard/22.png)

Wanneer u configureert een **externe URL**, moet u de volgende indeling: `https://*.<custom domain>`

![Gebruik voor externe URL de notatie https://*.\<aangepast domein >](./media/application-proxy-wildcard/21.png)

Andere functies van het jokerteken, meerdere jokertekens of andere regex-tekenreeksen worden niet ondersteund en fouten veroorzaken.

### <a name="excluding-applications-from-the-wildcard"></a>Met uitzondering van toepassingen van het jokerteken

U kunt een toepassing uitsluiten van de jokertekentoepassing door

- De toepassing van de uitzondering als normale toepassing publiceren
- Het jokerteken alleen voor specifieke toepassingen via de DNS-instellingen inschakelen

Publiceren van een toepassing als normale toepassing is de aanbevolen methode om uit te sluiten van een toepassing van een jokerteken. U moet de uitgesloten toepassingen voordat de wildcard-toepassingen om ervoor te zorgen dat uw uitzonderingen worden toegepast vanaf het begin. De meest specifieke toepassing altijd voorrang: een toepassing die is gepubliceerd als `budgets.finance.adventure-works.com` heeft voorrang op de toepassing `*.finance.adventure-works.com`, die op zijn beurt heeft voorrang op de toepassing `*.adventure-works.com`.

U kunt het jokerteken om te werken alleen voor specifieke toepassingen via uw DNS-beheer beperken. Als een best practice, moet u een CNAME-vermelding bestaat uit een jokerteken en komt overeen met de indeling van de externe URL die u hebt geconfigureerd. U kunt echter in plaats daarvan specifieke URL's verwijzen naar de jokertekens. Bijvoorbeeld, in plaats van `*.adventure-works.com`, wijst u `hr.adventure-works.com`, `expenses.adventure-works.com` en `travel.adventure-works.com individually` naar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Als u deze optie gebruikt, moet u ook een andere CNAME-vermelding voor de waarde `AppId.domain`, bijvoorbeeld `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, ook wijzen naar dezelfde locatie. U vindt de **AppId** op de eigenschappenpagina van de toepassing van de jokertekentoepassing:

![Zoek de toepassings-ID op de eigenschappen pagina van de app](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>De URL van de startpagina voor de MyApps-deelvenster instellen

De jokertekentoepassing wordt weergegeven met slechts één tegel in de [MyApps deelvenster](https://myapps.microsoft.com). Deze tegel is standaard verborgen. Voor het weergeven van de tegel en hebben gebruikers land op een specifieke pagina:

1. Volg de richtlijnen voor [instellen van een URL van de startpagina](application-proxy-configure-custom-home-page.md).
1. Stel **toepassing weergeven** naar **waar** op de eigenschappenpagina van de toepassing.

### <a name="kerberos-constrained-delegation"></a>Kerberos-beperkte overdracht

Voor toepassingen die gebruikmaken van [kerberos-beperkte delegatie (KCD) als de methode voor eenmalige aanmelding](application-proxy-configure-single-sign-on-with-kcd.md), de SPN-naam die worden vermeld voor de methode voor eenmalige aanmelding ook een jokerteken moet mogelijk. Bijvoorbeeld, wordt de SPN mogelijk: `HTTP/*.adventure-works.com`. U moet nog steeds de afzonderlijke SPN's geconfigureerd op de back endservers hebben (bijvoorbeeld `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Algemeen jokertekentoepassing

In dit scenario hebt u drie verschillende toepassingen die u wilt publiceren:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alle drie de toepassingen:

- Worden gebruikt door alle gebruikers
- Gebruik *geïntegreerde Windows-verificatie*
- Hebben de dezelfde eigenschappen

U kunt het jokerteken toepassingen publiceren met de stappen in [toepassingen publiceren die gebruikmaken van Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). In dit scenario wordt ervan uitgegaan dat:

- Een tenant met de volgende ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Een geverifieerd domein met de naam `adventure-works.com` is geconfigureerd.
- Een **CNAME** vermelding die verwijst `*.adventure-works.com` naar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` is gemaakt.

Na de [beschreven stappen](application-proxy-add-on-premises-application.md), u een nieuwe application proxy-toepassing maken in uw tenant. In dit voorbeeld is het jokerteken in de volgende velden:

- Interne URL:

    ![Voor beeld: Joker teken in interne URL](./media/application-proxy-wildcard/42.png)

- Externe URL:

    ![Voor beeld: Joker teken in externe URL](./media/application-proxy-wildcard/43.png)

- SPN voor interne toepassing:

    ![Voor beeld: Joker teken in SPN-configuratie](./media/application-proxy-wildcard/44.png)

Door het jokertekentoepassing publiceert, kunt u nu de drie toepassingen openen door te navigeren naar de URL's die u gewend bent (bijvoorbeeld `travel.adventure-works.com`).

De configuratie implementeert de volgende structuur:

![Toont de structuur die door de voorbeeld configuratie is geïmplementeerd](./media/application-proxy-wildcard/05.png)

| Kleur | Beschrijving |
| ---   | ---         |
| Blauw  | Toepassingen worden expliciet gepubliceerd en zichtbaar in de Azure Portal. |
| Grijs  | Toepassingen kunt u toegankelijk zijn via de bovenliggende toepassing. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Algemene jokertekentoepassing met uitzondering

In dit scenario hebt u bovendien voor de drie algemene toepassingen een andere toepassing `finance.adventure-works.com`, die alleen toegankelijk door de afdeling Financiën moet zijn. Met de huidige toepassingsstructuur toegankelijk uw toepassing Financiën via de toepassingstoewijzingen en door alle werknemers. Om dit te wijzigen, uitsluiten u uw toepassing uit uw jokertekens door het configureren van Financiën als een afzonderlijke toepassing met meer beperkte machtigingen.

U moet ervoor zorgen dat een CNAME-records aanwezig zijn die verwijst `finance.adventure-works.com` aan de specifieke eindpunt van de toepassing, opgegeven op de pagina Application Proxy voor de toepassing. Voor dit scenario `finance.adventure-works.com` verwijst naar `https://finance-awcycles.msappproxy.net/`.

Na de [beschreven stappen](application-proxy-add-on-premises-application.md), in dit scenario vereist de volgende instellingen:

- In de **interne URL**, u **financiën** in plaats van een jokerteken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in interne URL](./media/application-proxy-wildcard/52.png)

- In de **externe URL**, u **financiën** in plaats van een jokerteken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in externe URL](./media/application-proxy-wildcard/53.png)

- Interne toepassing SPN u **financiën** in plaats van een jokerteken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in SPN-configuratie](./media/application-proxy-wildcard/54.png)

Deze configuratie worden geïmplementeerd in het volgende scenario:

![Toont de configuratie die wordt geïmplementeerd door het voorbeeld scenario](./media/application-proxy-wildcard/09.png)

Omdat `finance.adventure-works.com` is een meer specifieke URL dan `*.adventure-works.com`, heeft deze prioriteit. Gebruikers te navigeren naar `finance.adventure-works.com` de ervaring die is opgegeven in de toepassing Financiën Resources hebben. In dit geval alleen medewerkers van Financiën toegang kunnen krijgen tot zijn `finance.adventure-works.com`.

Als u meerdere toepassingen die zijn gepubliceerd voor Financiën hebt en u hebt `finance.adventure-works.com` als een geverifieerd domein, kan u een andere jokertekentoepassing publiceren `*.finance.adventure-works.com`. Omdat dit is ook specifieker dan de algemene `*.adventure-works.com`, heeft deze prioriteit als een gebruiker toegang heeft tot een toepassing in het domein van Financiën.

## <a name="next-steps"></a>Volgende stappen

- Zie [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie over **aangepaste domeinen**.
- Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) voor meer informatie over het **publiceren van toepassingen**
