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

In Azure Active Directory (Azure AD), het configureren van een groot aantal on-premises toepassingen kunnen snel worden onbeheersbare en onnodige risico's op configuratiefouten introduceert als veel van deze nodig hebt met dezelfde instellingen. Met [Azure AD-toepassingsproxy](application-proxy.md)kunt u dit probleem oplossen met behulp van de functie voor het publiceren van joker tekens voor het publiceren en beheren van veel toepassingen tegelijk. Dit is een oplossing waarmee u kunt:

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

> http (s)://*.\<domein\>

Bijvoorbeeld: `http(s)://*.adventure-works.com`.

De interne en externe URL's kunnen verschillende domeinen gebruiken als een best practice, moeten ze niet hetzelfde zijn. Wanneer de toepassing publiceert, ziet u een fout als een van de URL's niet een jokerteken.

Als u aanvullende toepassingen met verschillende configuratie-instellingen hebt, moet u deze uitzonderingen publiceren als afzonderlijke toepassingen te overschrijven de standaardinstellingen voor het jokerteken. Toepassingen zonder een jokerteken altijd voorrang op wildcard-toepassingen. Dit zijn "net" reguliere toepassingen vanuit het perspectief configuratie.

Het maken van een Joker teken toepassing is gebaseerd op dezelfde [toepassings publicatie stroom](application-proxy-add-on-premises-application.md) die beschikbaar is voor alle andere toepassingen. Het enige verschil is dat u een jokerteken in de URL's en mogelijk de SSO-configuratie opnemen.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan deze vereisten hebt voldaan om aan de slag te gaan.

### <a name="custom-domains"></a>Aangepaste domeinen

Hoewel [aangepaste domeinen](application-proxy-configure-custom-domain.md) optioneel zijn voor alle andere toepassingen, zijn ze een vereiste voor Joker teken toepassingen. Het maken van aangepaste domeinen, moet u:

1. Een geverifieerd domein maken in Azure.
1. Upload een SSL-certificaat in de PFX-indeling naar de toepassingsproxy.

U moet een certificaat met jokertekens overwegen zodat deze overeenkomen met de toepassing die u van plan bent om te maken. U kunt ook kunt u ook een certificaat met alleen een lijst met specifieke toepassingen. In dit geval zijn alleen de toepassingen die worden vermeld in het certificaat toegankelijk via deze jokertekentoepassing.

Uit veiligheidsoverwegingen, dit is een vereiste voor vaste en jokertekens wordt niet ondersteund voor toepassingen die een aangepast domein kunnen worden gebruikt voor de externe URL.

### <a name="dns-updates"></a>DNS-updates

Wanneer u aangepaste domeinen gebruikt, moet u een DNS-vermelding maken met een CNAME-record voor de externe URL (bijvoorbeeld `*.adventure-works.com`) die verwijst naar de externe URL van het eind punt van de toepassings proxy. Voor Joker teken toepassingen moet de CNAME-record verwijzen naar de relevante externe Url's:

> `<yourAADTenantId>.tenant.runtime.msappproxy.net`

Om te bevestigen dat u uw CNAME correct hebt geconfigureerd, kunt u [nslookup](https://docs.microsoft.com/windows-server/administration/windows-commands/nslookup) op een van de doel eindpunten gebruiken, bijvoorbeeld `expenses.adventure-works.com`.  Uw antwoord moet de reeds vermelde alias (`<yourAADTenantId>.tenant.runtime.msappproxy.net`) bevatten.

## <a name="considerations"></a>Overwegingen

Hier volgen enkele overwegingen waar u rekening mee moet houden voor Joker teken toepassingen.

### <a name="accepted-formats"></a>Geaccepteerde indelingen

Voor Joker teken toepassingen moet de **interne URL** worden opgemaakt als `http(s)://*.<domain>`.

![Gebruik voor interne URL de notatie http (s)://*.\<domein >](./media/application-proxy-wildcard/22.png)

Wanneer u een **externe URL**configureert, moet u de volgende indeling gebruiken: `https://*.<custom domain>`

![Gebruik voor externe URL de notatie https://*.\<aangepast domein >](./media/application-proxy-wildcard/21.png)

Andere functies van het jokerteken, meerdere jokertekens of andere regex-tekenreeksen worden niet ondersteund en fouten veroorzaken.

### <a name="excluding-applications-from-the-wildcard"></a>Met uitzondering van toepassingen van het jokerteken

U kunt een toepassing uitsluiten van de jokertekentoepassing door

- De toepassing van de uitzondering als normale toepassing publiceren
- Het jokerteken alleen voor specifieke toepassingen via de DNS-instellingen inschakelen

Publiceren van een toepassing als normale toepassing is de aanbevolen methode om uit te sluiten van een toepassing van een jokerteken. U moet de uitgesloten toepassingen voordat de wildcard-toepassingen om ervoor te zorgen dat uw uitzonderingen worden toegepast vanaf het begin. De meest specifieke toepassing heeft altijd voor rang: een toepassing die is gepubliceerd als `budgets.finance.adventure-works.com` heeft voor rang op de toepassing `*.finance.adventure-works.com`, die op zijn beurt voor rang krijgt op de toepassing `*.adventure-works.com`.

U kunt het jokerteken om te werken alleen voor specifieke toepassingen via uw DNS-beheer beperken. Als een best practice, moet u een CNAME-vermelding bestaat uit een jokerteken en komt overeen met de indeling van de externe URL die u hebt geconfigureerd. U kunt echter in plaats daarvan specifieke URL's verwijzen naar de jokertekens. Bijvoorbeeld, in plaats van `*.adventure-works.com`, punt `hr.adventure-works.com`, `expenses.adventure-works.com` en `travel.adventure-works.com individually` naar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net`.

Als u deze optie gebruikt, moet u ook een andere CNAME-vermelding voor de waarde `AppId.domain`, bijvoorbeeld `00000000-1a11-22b2-c333-444d4d4dd444.adventure-works.com`, naar dezelfde locatie verwijzen. U kunt de **AppId** vinden op de pagina eigenschappen van toepassing van de toepassing voor joker tekens:

![Zoek de toepassings-ID op de eigenschappen pagina van de app](./media/application-proxy-wildcard/01.png)

### <a name="setting-the-homepage-url-for-the-myapps-panel"></a>De URL van de startpagina voor de MyApps-deelvenster instellen

De Joker toepassing wordt weer gegeven met slechts één tegel in het [deel venster MyApps](https://myapps.microsoft.com). Deze tegel is standaard verborgen. Voor het weergeven van de tegel en hebben gebruikers land op een specifieke pagina:

1. Volg de richt lijnen voor het [instellen van een start pagina-URL](application-proxy-configure-custom-home-page.md).
1. Stel **toepassing weer geven** op **waar** in op de eigenschappen pagina van de toepassing.

### <a name="kerberos-constrained-delegation"></a>Kerberos-beperkte overdracht

Voor toepassingen die gebruikmaken van [Kerberos-beperkte delegering (KCD) als SSO-methode](application-proxy-configure-single-sign-on-with-kcd.md), kan de SPN die wordt vermeld voor de SSO-methode ook een Joker teken hebben. De SPN kan er bijvoorbeeld als volgt uit`HTTP/*.adventure-works.com`. U moet nog steeds de afzonderlijke Spn's configureren op uw back-endservers (bijvoorbeeld `http://expenses.adventure-works.com and HTTP/travel.adventure-works.com`).

## <a name="scenario-1-general-wildcard-application"></a>Scenario 1: Algemeen jokertekentoepassing

In dit scenario hebt u drie verschillende toepassingen die u wilt publiceren:

- `expenses.adventure-works.com`
- `hr.adventure-works.com`
- `travel.adventure-works.com`

Alle drie de toepassingen:

- Worden gebruikt door alle gebruikers
- *Geïntegreerde Windows-verificatie* gebruiken
- Hebben de dezelfde eigenschappen

U kunt de toepassing voor joker tekens publiceren met behulp van de stappen die worden beschreven in [toepassingen publiceren met behulp van Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md). In dit scenario wordt ervan uitgegaan dat:

- Een Tenant met de volgende ID: `000aa000-11b1-2ccc-d333-4444eee4444e`
- Er is een geverifieerd domein met de naam `adventure-works.com` geconfigureerd.
- Een **CNAME** -vermelding die verwijst naar `*.adventure-works.com` naar `000aa000-11b1-2ccc-d333-4444eee4444e.tenant.runtime.msappproxy.net` is gemaakt.

Als u de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)volgt, maakt u een nieuwe toepassings proxy toepassing in uw Tenant. In dit voorbeeld is het jokerteken in de volgende velden:

- Interne URL:

    ![Voor beeld: Joker teken in interne URL](./media/application-proxy-wildcard/42.png)

- Externe URL:

    ![Voor beeld: Joker teken in externe URL](./media/application-proxy-wildcard/43.png)

- SPN voor interne toepassing:

    ![Voor beeld: Joker teken in SPN-configuratie](./media/application-proxy-wildcard/44.png)

Als u de toepassing voor joker tekens publiceert, kunt u toegang krijgen tot uw drie toepassingen door te navigeren naar de Url's die u gebruikt (bijvoorbeeld `travel.adventure-works.com`).

De configuratie implementeert de volgende structuur:

![Toont de structuur die door de voorbeeld configuratie is geïmplementeerd](./media/application-proxy-wildcard/05.png)

| Kleur | Beschrijving |
| ---   | ---         |
| Blauw  | Toepassingen worden expliciet gepubliceerd en zichtbaar in de Azure Portal. |
| Grijs  | Toepassingen kunt u toegankelijk zijn via de bovenliggende toepassing. |

## <a name="scenario-2-general-wildcard-application-with-exception"></a>Scenario 2: Algemene jokertekentoepassing met uitzondering

In dit scenario hebt u naast de drie algemene toepassingen een andere toepassing, `finance.adventure-works.com`, die alleen toegankelijk moet zijn voor de financiële afdeling. Met de huidige toepassingsstructuur toegankelijk uw toepassing Financiën via de toepassingstoewijzingen en door alle werknemers. Om dit te wijzigen, uitsluiten u uw toepassing uit uw jokertekens door het configureren van Financiën als een afzonderlijke toepassing met meer beperkte machtigingen.

U moet ervoor zorgen dat er een CNAME-records bestaan die `finance.adventure-works.com` naar het toepassings-specifieke eind punt, opgegeven op de pagina Toepassings proxy voor de toepassing. Voor dit scenario `finance.adventure-works.com` verwijst naar `https://finance-awcycles.msappproxy.net/`.

Aan de hand van de [gedocumenteerde stappen](application-proxy-add-on-premises-application.md)zijn voor dit scenario de volgende instellingen vereist:

- In de **interne URL**stelt u **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in interne URL](./media/application-proxy-wildcard/52.png)

- In de **externe URL**stelt u **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in externe URL](./media/application-proxy-wildcard/53.png)

- Interne SPN van de toepassing u stelt **Financiën** in in plaats van een Joker teken.

    ![Voor beeld: Finance instellen in plaats van een Joker teken in SPN-configuratie](./media/application-proxy-wildcard/54.png)

Deze configuratie worden geïmplementeerd in het volgende scenario:

![Toont de configuratie die wordt geïmplementeerd door het voorbeeld scenario](./media/application-proxy-wildcard/09.png)

Omdat `finance.adventure-works.com` een meer specifieke URL is dan `*.adventure-works.com`, heeft dit prioriteit. Gebruikers die naar `finance.adventure-works.com` navigeren, hebben de ervaring die is opgegeven in de toepassing Financiën resources. In dit geval hebben alleen werk nemers met Financiën toegang tot `finance.adventure-works.com`.

Als u meerdere toepassingen hebt gepubliceerd voor financiering en u `finance.adventure-works.com` als geverifieerd domein, kunt u een andere toepassing voor joker tekens `*.finance.adventure-works.com`publiceren. Omdat dit specifieker is dan het algemene `*.adventure-works.com`, heeft dit een prioriteit als een gebruiker toegang heeft tot een toepassing in het domein Finance.

## <a name="next-steps"></a>Volgende stappen

- Zie [werken met aangepaste domeinen in Azure AD-toepassingsproxy](application-proxy-configure-custom-domain.md)voor meer informatie over **aangepaste domeinen**.
- Zie [toepassingen publiceren met Azure AD-toepassingsproxy](application-proxy-add-on-premises-application.md) voor meer informatie over het **publiceren van toepassingen**
