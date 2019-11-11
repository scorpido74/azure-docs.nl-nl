---
title: Veelgestelde vragen over de configuratie van Azure-app | Microsoft Docs
description: Veelgestelde vragen over Azure-app configuratie
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: lcozzens
ms.custom: mvc
ms.openlocfilehash: 8d286cbab33a1fb6a2d2a2cb70caed11b21af735
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904096"
---
# <a name="azure-app-configuration-faq"></a>Veelgestelde vragen over Azure-app configuratie

In dit artikel vindt u veelgestelde vragen over Azure-app configuratie.

## <a name="how-is-app-configuration-different-from-azure-key-vault"></a>In welk opzicht verschilt App Configuration van Azure Key Vault?

App-configuratie is ontworpen voor een afzonderlijke reeks use cases: Hiermee kunnen ontwikkel aars toepassings instellingen beheren en de beschik baarheid van functies bepalen. Het doel is om veel van de taken van het werken met complexe configuratie gegevens te vereenvoudigen.

App-configuratie ondersteunt:

- Hiërarchische naam ruimten
- Labels
- Uitgebreide query's
- Batch ophalen
- Gespecialiseerde beheer bewerkingen
- Een gebruikers interface voor het beheer van functies

App-configuratie is complementair met Key Vault en de twee moeten naast elkaar worden gebruikt in de meeste toepassings implementaties.

## <a name="should-i-store-secrets-in-app-configuration"></a>Moet ik geheimen opslaan in de app-configuratie?

Hoewel app-configuratie een beveiligde beveiliging biedt, is Key Vault nog steeds de beste plaats voor het opslaan van toepassings geheimen. Key Vault biedt versleuteling op hardwareniveau, granulair toegangs beleid en beheer bewerkingen, zoals het draaien van certificaten.

U kunt app-configuratie waarden maken die verwijzen naar geheimen die zijn opgeslagen in Key Vault. Zie [Key Vault verwijzingen gebruiken in een ASP.net core-app](./use-key-vault-references-dotnet-core.md)voor meer informatie.

## <a name="does-app-configuration-encrypt-my-data"></a>Versleutelen de app-configuratie mijn gegevens?

Ja. App-configuratie versleutelt alle sleutel waarden die worden bewaard en versleutelt de netwerk communicatie. Sleutel namen worden gebruikt als indexen voor het ophalen van configuratie gegevens en worden niet versleuteld.

## <a name="how-is-app-configuration-different-from-azure-app-service-settings"></a>Hoe wijkt de configuratie van de app af van Azure App Service instellingen?

Met Azure App Service kunt u app-instellingen definiëren voor elk App Service exemplaar. Deze instellingen worden door gegeven als omgevings variabelen aan de toepassings code. U kunt desgewenst een instelling koppelen aan een specifieke implementatie site, indien gewenst. Zie [app-instellingen configureren](/azure/app-service/configure-common#configure-app-settings)voor meer informatie.

Azure-app configuratie daarentegen kunt u instellingen definiëren die kunnen worden gedeeld tussen meerdere apps, waaronder apps die worden uitgevoerd in App Service. Deze instellingen zijn toegankelijk via de configuratie providers voor .NET en Java via de Azure SDK, of rechtstreeks via REST Api's.

U kunt ook instellingen importeren en exporteren tussen App Service en app-configuratie. Zo kunt u snel een nieuw app-configuratie archief instellen op basis van bestaande App Service instellingen, of de configuratie eenvoudig delen met een bestaande app die afhankelijk is van App Service-instellingen.

## <a name="are-there-any-size-limitations-on-keys-and-values-stored-in-app-configuration"></a>Zijn er beperkingen voor de grootte van sleutels en waarden die zijn opgeslagen in de app-configuratie?

Er geldt een limiet van 10 KB voor een item met enkele sleutel waarde.

## <a name="how-should-i-store-configurations-for-multiple-environments-test-staging-production-and-so-on"></a>Hoe kan ik configuraties voor meerdere omgevingen (testen, fase ring, productie, enzovoort) opslaan?

Momenteel kunt u bepalen wie toegang heeft tot de app-configuratie op een niveau per opslag. Gebruik een afzonderlijke opslag voor elke omgeving waarvoor verschillende machtigingen zijn vereist. Deze benadering biedt u de best mogelijke beveiligings isolatie.

## <a name="what-are-the-recommended-ways-to-use-app-configuration"></a>Wat zijn de aanbevolen manieren om app-configuratie te gebruiken?

Zie [Aanbevolen procedures](./howto-best-practices.md).

## <a name="how-much-does-app-configuration-cost"></a>Wat kost de configuratie van de app?

De service is gratis voor gebruik tijdens de open bare preview-versie.

## <a name="how-can-i-receive-announcements-on-new-releases-and-other-information-related-to-app-configuration"></a>Hoe kan ik aankondigingen ontvangen over nieuwe releases en andere informatie met betrekking tot app-configuratie?

Abonneer u op onze [github aankondigingen opslag plaats](https://github.com/Azure/AppConfiguration-Announcements).

## <a name="how-can-i-report-an-issue-or-give-a-suggestion"></a>Hoe kan ik een probleem melden of een suggestie geven?

U kunt ons rechtstreeks bereiken op [github](https://github.com/Azure/AppConfiguration/issues).

## <a name="next-steps"></a>Volgende stappen

* [Over Azure-app configuratie](./overview.md)
