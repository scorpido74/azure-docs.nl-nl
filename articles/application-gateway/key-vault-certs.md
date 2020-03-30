---
title: SSL-beëindiging met Azure Key Vault-certificaten
description: Meer informatie over hoe u Azure Application Gateway integreren met Key Vault voor servercertificaten die zijn gekoppeld aan luisteraars met HTTPS-functie.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 5633dd7b72f4de22cd34b7d093e8ec4d9cb411f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77137706"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-beëindiging met Key Vault-certificaten

[Azure Key Vault](../key-vault/key-vault-overview.md) is een door een platform beheerde geheime winkel die u gebruiken om geheimen, sleutels en SSL-certificaten te beveiligen. Azure Application Gateway ondersteunt integratie met Key Vault voor servercertificaten die zijn gekoppeld aan luisteraars met HTTPS-functie. Deze ondersteuning is beperkt tot de v2 SKU of Application Gateway.

Key Vault-integratie biedt twee modellen voor SSL-beëindiging:

- U expliciet SSL-certificaten aan de listener verstrekken. Dit model is de traditionele manier om SSL-certificaten door te geven aan Application Gateway voor SSL-beëindiging.
- U optioneel een verwijzing naar een bestaand Key Vault-certificaat of geheim geven wanneer u een naar HTTPS-enabled listener maakt.

Application Gateway-integratie met Key Vault biedt vele voordelen, waaronder:

- Betere beveiliging, omdat SSL-certificaten niet direct worden afgehandeld door het toepassingsontwikkelingsteam. Integratie maakt het mogelijk om een apart beveiligingsteam te laten:
  * Toepassingsgateways instellen.
  * Levenscyclus van toepassingsgateways beheren.
  * Geef machtigingen aan geselecteerde toepassingsgateways om toegang te krijgen tot certificaten die zijn opgeslagen in uw sleutelkluis.
- Ondersteuning voor het importeren van bestaande certificaten in uw sleutelkluis. Of gebruik Key Vault API's om nieuwe certificaten te maken en te beheren met een van de vertrouwde Key Vault-partners.
- Ondersteuning voor automatische verlenging van certificaten die zijn opgeslagen in uw sleutelkluis.

Application Gateway ondersteunt momenteel alleen softwaregevalideerde certificaten. Certificaten die door hardwarebeveiligingsmodule (HSM) zijn gevalideerd, worden niet ondersteund. Nadat Application Gateway is geconfigureerd om Key Vault-certificaten te gebruiken, halen de exemplaren het certificaat op uit Key Vault en installeren ze lokaal voor SSL-beëindiging. De instanties peilen Key Vault ook met intervallen van 24 uur om een vernieuwde versie van het certificaat op te halen, als deze bestaat. Als er een bijgewerkt certificaat wordt gevonden, wordt het SSL-certificaat dat momenteel is gekoppeld aan de HTTPS-listener automatisch geroteerd.

> [!NOTE]
> De Azure-portal ondersteunt alleen KeyVault-certificaten, geen geheimen. Application Gateway ondersteunt nog steeds verwijzingen naar geheimen van KeyVault, maar alleen via niet-Portal-bronnen zoals PowerShell, CLI, API, ARM-sjablonen, enz. 

## <a name="how-integration-works"></a>Hoe de integratie werkt

Application Gateway-integratie met Key Vault vereist een configuratieproces in drie stappen:

1. **Een door de gebruiker toegewezen beheerde identiteit maken**

   U maakt of hergebruikt een bestaande door de gebruiker toegewezen beheerde identiteit, die Application Gateway gebruikt om namens u certificaten op te halen uit Key Vault. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie. Met deze stap wordt een nieuwe identiteit gemaakt in de Azure Active Directory-tenant. De identiteit wordt vertrouwd door het abonnement dat wordt gebruikt om de identiteit te maken.

1. **Uw sleutelkluis configureren**

   Vervolgens importeert u een bestaand certificaat of maakt u een nieuw certificaat in uw sleutelkluis. Het certificaat wordt gebruikt door toepassingen die via de toepassingsgateway worden uitgevoerd. In deze stap u ook een sleutelkluisgeheim gebruiken dat is opgeslagen als een 64-gecodeerd PFX-bestand zonder wachtwoord. We raden u aan een certificaattype te gebruiken vanwege de mogelijkheid voor automatische vernieuwing die beschikbaar is met certificaattypeobjecten in de sleutelkluis. Nadat u een certificaat of een geheim hebt gemaakt, definieert u toegangsbeleid in de sleutelkluis zodat de identiteit toegang *krijgt* tot het geheim.

1. **De toepassingsgateway configureren**

   Nadat u de twee voorgaande stappen hebt voltooid, u een bestaande toepassingsgateway instellen of wijzigen om de door de gebruiker toegewezen beheerde identiteit te gebruiken. U ook het SSL-certificaat van de HTTP-listener configureren om te wijzen op de volledige URI van het Key Vault-certificaat of de geheime ID.

   ![Key vault certificaten](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Volgende stappen

[SSL-beëindiging configureren met Key Vault-certificaten met Azure PowerShell](configure-keyvault-ps.md)
