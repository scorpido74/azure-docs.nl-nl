---
title: Aan boord van een root- of apex-domein naar een bestaande Front Door - Azure-portal
description: Meer informatie over het aan boord van een root- of apex-domein naar een bestaande voordeur met behulp van de Azure-portal.
services: front-door
author: sharad4u
ms.service: frontdoor
ms.topic: article
ms.date: 5/21/2019
ms.author: sharadag
ms.openlocfilehash: 4b74338f22a82d76ef13126ee0862b841bd89a99
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878881"
---
# <a name="onboard-a-root-or-apex-domain-on-your-front-door"></a>Aan boord van een root- of apex-domein op je voordeur
Azure Front Door gebruikt CNAME-records om domeineigendom te valideren voor onboarding van aangepaste domeinen. Front Door stelt ook niet het ip-adres van frontend bloot dat is gekoppeld aan uw frontdoor-profiel en u uw apex-domein dus niet toewijzen aan een IP-adres, als de bedoeling is om het aan boord te nemen naar Azure Front Door.

Het DNS-protocol voorkomt de toewijzing van CNAME-records op de zonetop. Bijvoorbeeld, als uw `contoso.com`domein is ; U CNAME-records maken voor `somelabel.contoso.com`; maar je geen CNAME voor `contoso.com` zichzelf maken. Deze beperking vormt een probleem voor toepassingseigenaren die load-balanced applicaties achter Azure Front Door hebben. Aangezien het gebruik van een Front Door profiel vereist het maken van een CNAME record, is het niet mogelijk om te wijzen op de Voordeur profiel van de zone apex.

Dit probleem wordt opgelost met aliasrecords op Azure DNS. In tegenstelling tot CNAME-records worden aliasrecords gemaakt op de zonetop en kunnen toepassingseigenaren deze gebruiken om hun zonetoprecord te wijzen op een Front Door-profiel met openbare eindpunten. Toepassingseigenaren wijzen naar hetzelfde frontdoorprofiel dat wordt gebruikt voor een ander domein binnen hun DNS-zone. Bijvoorbeeld, `contoso.com` en `www.contoso.com` kan wijzen op dezelfde voordeur profiel. 

Het toewijzen van uw apex of root-domein aan uw Front Door-profiel vereist in principe CNAME-afvlakking of DNS-achtervolging, wat een mechanisme is waarbij in de DNS-provider de CNAME-vermelding opnieuw wordt opgelost totdat het een IP-adres raakt. Deze functionaliteit wordt ondersteund door Azure DNS voor Front Door-eindpunten. 

> [!NOTE]
> Er zijn ook andere DNS-providers die Ondersteuning bieden voor CNAME-afvlakking of DNS-achtervolging, maar Azure Front Door raadt aan Azure DNS te gebruiken voor zijn klanten voor het hosten van hun domeinen.

U de Azure-portal gebruiken om een apex-domein op uw voordeur aan boord te nemen en HTTPS op uw front door het te koppelen aan een certificaat voor TLS-beëindiging. Apex domeinen worden ook wel root of naakte domeinen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Een aliasrecord maken die naar uw voordeurprofiel verwijst
> * Het hoofddomein toevoegen aan de voordeur
> * HTTPS instellen op het hoofddomein

> [!NOTE]
> Deze zelfstudie vereist dat u al een voordeurprofiel hebt gemaakt. Verwijs andere tutorials zoals [Quickstart: Maak een voordeur](./quickstart-create-front-door.md) of [Maak een voordeur met HTTP naar HTTPS omleiding](./front-door-how-to-redirect-https.md) om aan de slag te gaan.

## <a name="create-an-alias-record-for-zone-apex"></a>Een aliasrecord maken voor zoneapex

1. Open **Azure DNS-configuratie** voor het domein dat aan boord moet worden genomen.
2. Maak of bewerk de record voor zone apex.
3. Selecteer het **recordtype** als _Een_ record en selecteer _Vervolgens Ja_ voor **aliasrecordset**. **Het type alias** moet worden ingesteld op _Azure-bron_.
4. Kies het Azure-abonnement waarbij uw voordeurprofiel wordt gehost en selecteer vervolgens de frontdoor-bron in de vervolgkeuzelijst **azure-bron.**
5. Klik op **OK** om uw wijzigingen in te dienen.

    ![Alias record voor zone apex](./media/front-door-apex-domain/front-door-apex-alias-record.png)

6. De bovenstaande stap zal een zone apex record wijzen op uw Front Door bron en `afdverify.contosonews.com`ook `afdverify.<name>.azurefd.net` een CNAME record mapping 'afdverify' (voorbeeld - ) waarop zal worden gebruikt voor onboarding van het domein op uw Front Door profiel.

## <a name="onboard-the-custom-domain-on-your-front-door"></a>Aan boord van het aangepaste domein op uw voordeur

1. Klik op het tabblad Voordeurontwerper op het pictogram '+' in de sectie Frontend hosts om een nieuw aangepast domein toe te voegen.
2. Voer de hoofd- of apex-domeinnaam in `contosonews.com`het aangepaste hostnaamveld in, bijvoorbeeld .
3. Zodra de CNAME-toewijzing van het domein naar uw voordeur is gevalideerd, klikt u op **Toevoegen** om het aangepaste domein toe te voegen.
4. Klik **op Opslaan** om de wijzigingen in te dienen.

![Menu voor aangepaste domeinen](./media/front-door-apex-domain/front-door-onboard-apex-domain.png)

## <a name="enable-https-on-your-custom-domain"></a>HTTPS inschakelen op uw aangepaste domein

1. Klik op het aangepaste domein dat is toegevoegd en wijzig onder de sectie **Aangepast domein HTTPS**de status in **Ingeschakeld**.
2. Selecteer het **type certificaatbeheer** om _'Mijn eigen certificaat gebruiken'_.

> [!WARNING]
> Front Door managed certificate management type wordt momenteel niet ondersteund voor apex of root domeinen. De enige optie die beschikbaar is voor het inschakelen van HTTPS op een apex- of hoofddomein voor Front Door, is het gebruik van uw eigen aangepaste TLS/SSL-certificaat dat wordt gehost op Azure Key Vault.

3. Zorg ervoor dat u de juiste machtigingen voor Voordeur hebt ingesteld om toegang te krijgen tot uw sleutelkluis zoals aangegeven in de gebruikersinterface, voordat u doorgaat naar de volgende stap.
4. Kies een **Key Vault-account** uit uw huidige abonnement en selecteer vervolgens de juiste **secret-** en **geheime versie** om naar het juiste certificaat te brengen.
5. Klik op **Bijwerken** om de selectie op te slaan en klik vervolgens op **Opslaan**.
6. Klik na een paar minuten op **Vernieuwen** en klik vervolgens opnieuw op het aangepaste domein om de voortgang van certificaatinrichting te bekijken. 

> [!WARNING]
> Zorg ervoor dat u de juiste routeringsregels hebt gemaakt voor uw apex-domein of het domein hebt toegevoegd aan bestaande routeringsregels.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).