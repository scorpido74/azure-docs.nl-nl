---
title: DNS-namen configureren voor apps die gebruikmaken van Traffic Manager-Azure App Service
description: Gebruik een aangepaste domein naam voor een web-app in Azure App Service die Traffic Manager voor taak verdeling bevat.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/17/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6a59d519ae8bb515ab16632bd39509682959f50
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074034"
---
# <a name="configuring-a-custom-domain-name-for-a-web-app-in-azure-app-service-using-traffic-manager"></a>Het configureren van een aangepaste domein naam voor een web-app in Azure App Service met behulp van Traffic Manager
[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

[!INCLUDE [intro](../../includes/custom-dns-web-site-intro-traffic-manager.md)]

Dit artikel bevat algemene instructies voor het gebruik van een aangepaste domein naam met een [app service](overview.md) -app die is geïntegreerd met [Traffic Manager](../traffic-manager/traffic-manager-overview.md) voor taak verdeling.

[!INCLUDE [tmwebsitefooter](../../includes/custom-dns-web-site-traffic-manager-notes.md)]

[!INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

<a name="understanding-records"></a>

## <a name="understanding-dns-records"></a>Informatie over DNS-records
[!INCLUDE [understandingdns](../../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<a name="bkmk_configsharedmode"></a>

## <a name="configure-your-web-apps-for-standard-mode"></a>Uw web-apps configureren voor de standaard modus
[!INCLUDE [modes](../../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## <a name="add-a-dns-record-for-your-custom-domain"></a>Een DNS-record voor uw aangepaste domein toevoegen
> [!NOTE]
> Als u het domein via Azure App Service Web Apps hebt aangeschaft, slaat u de volgende stappen over en raadpleegt u de laatste stap van het [domein aanschaffen voor web apps](manage-custom-dns-buy-domain.md) artikel.
> 
> 

Als u uw aangepaste domein wilt koppelen aan een web-app in Azure App Service, moet u een nieuw item toevoegen aan de DNS-tabel voor uw aangepaste domein. U kunt dit doen met behulp van de beheer hulpprogramma's van uw domein provider.

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

Hoewel de specifieke kenmerken van elke domein provider verschillen, kunt u toewijzen *vanuit* uw aangepaste domein naam (zoals **contoso.com**) *aan* de Traffic Manager domein naam (**contoso.trafficmanager.net**) die is geïntegreerd met uw web-app.

> [!NOTE]
> Als er al een record wordt gebruikt en u uw apps aan het preventief moet koppelen, kunt u een extra CNAME-record maken. Als u bijvoorbeeld preventief **www\.contoso.com** wilt binden aan uw web-app, maakt u een CNAME-record van **awverify. www** in **contoso.trafficmanager.net**. U kunt vervolgens ' www\.contoso.com ' toevoegen aan uw web-app zonder de ' www ' CNAME-record te wijzigen. Zie [DNS-records voor een web-app maken in een aangepast domein][CREATEDNS]voor meer informatie.

Zodra u klaar bent met het toevoegen of wijzigen van DNS-records bij uw domein provider, slaat u de wijzigingen op.

<a name="enabledomain"></a>

## <a name="enable-traffic-manager"></a>Traffic Manager inschakelen
[!INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

## <a name="next-steps"></a>Volgende stappen
Zie het [Node.js Developer Center](https://azure.microsoft.com/develop/nodejs/) voor meer informatie.

<!-- URL List -->

[CREATEDNS]: ../dns/dns-web-sites-custom-domain.md
