---
title: PaaS-web & mobiele toepassingen beveiligen
titleSuffix: Azure App Service
description: 'Meer informatie over de aanbevolen procedures voor azure App Service-beveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: c3f3c7fbaa043a03b70ab770c06e493716c70daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500287"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Aanbevolen procedures voor het beveiligen van PaaS-web- en mobiele toepassingen met Azure App Service

In dit artikel bespreken we een verzameling best practices voor [Azure App Service-beveiliging](/azure/app-service/overview) voor het beveiligen van uw PaaS-web- en mobiele toepassingen. Deze best practices zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten zoals uzelf.

Azure App Service is een PaaS-platform-as-a-service-aanbod waarmee u web- en mobiele apps maken voor elk platform of apparaat en overal, in de cloud of on-premises verbinding maken met gegevens. App Service omvat de web- en mobiele mogelijkheden die voorheen afzonderlijk werden geleverd als Azure Websites en Azure Mobile Services. Deze service bevat ook nieuwe mogelijkheden voor het automatiseren van bedrijfsprocessen en het hosten van cloud-API's. Als één geïntegreerde service biedt App Service een uitgebreide reeks mogelijkheden voor web-, mobiele en integratiescenario's.

## <a name="authenticate-through-azure-active-directory-ad"></a>Verifiëren via Azure Active Directory (AD)
App Service biedt een OAuth 2.0-service voor uw identiteitsprovider. OAuth 2.0 richt zich op de eenvoud van clientontwikkelaars en biedt specifieke autorisatiestromen voor webtoepassingen, desktoptoepassingen en mobiele telefoons. Azure AD gebruikt OAuth 2.0 om u in staat te stellen toegang tot mobiele en webtoepassingen te autoriseren. Zie [Verificatie en autorisatie in Azure App Service](../../app-service/overview-authentication-authorization.md)voor meer informatie.

## <a name="restrict-access-based-on-role"></a>Toegang beperken op basis van rol
Het beperken van de toegang is noodzakelijk voor organisaties die beveiligingsbeleid voor gegevenstoegang willen afdwingen. U RBAC (Role-based access control) gebruiken om machtigingen toe te wijzen aan gebruikers, groepen en toepassingen op een bepaald bereik, zoals de noodzaak om te weten en de minste beveiligingsprincipes voor bevoegdheden. Zie [Wat is op rolgebaseerde toegangscontrole voor](/azure/role-based-access-control/overview)meer informatie over het verlenen van gebruikers toegang tot toepassingen.

## <a name="protect-your-keys"></a>Bescherm uw sleutels
Het maakt niet uit hoe goed uw beveiliging is als u uw abonnementssleutels verliest. Met Azure Key Vault kunt u de cryptografische sleutels en geheimen beveiligen die door cloudtoepassingen en -services worden gebruikt. Met Key Vault u sleutels en geheimen versleutelen (zoals verificatiesleutels, opslagaccountsleutels, gegevensversleutelingssleutels,. PFX-bestanden en wachtwoorden met behulp van sleutels die worden beschermd door hardwarebeveiligingsmodules (HSM's). Voor extra zekerheid kunt u de sleutels importeren of genereren in HSM's. U Key Vault ook gebruiken om uw TLS-certificaten te beheren met automatische verlenging. Zie [Wat is Azure Key Vault](../../key-vault/key-vault-overview.md) voor meer informatie.

## <a name="restrict-incoming-source-ip-addresses"></a>IP-adressen van binnenkomende bron beperken
[App Service Environments](../../app-service/environment/intro.md) heeft een virtuele netwerkintegratiefunctie waarmee u ip-adressen van binnenkomende bronnen beperken via netwerkbeveiligingsgroepen (NSG's). Als u niet bekend bent met Azure Virtual Networks (VNETs), is dit een mogelijkheid waarmee u veel van uw Azure-bronnen in een niet-internet, routeerbaar netwerk plaatsen waarmee u de toegang tot u beheert. Zie Uw [app integreren met een Azure Virtual Network](../../app-service/web-sites-integrate-with-vnet.md)voor meer informatie.

Voor App Service op Windows u IP-adressen ook dynamisch beperken door de web.config te configureren. Zie [Dynamische IP-beveiliging](/iis/configuration/system.webServer/security/dynamicIpSecurity/)voor meer informatie .


## <a name="next-steps"></a>Volgende stappen
In dit artikel wordt u kennis gemaakt met een verzameling best practices voor app-servicebeveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-databases beveiligen in Azure](paas-applications-using-sql.md)
