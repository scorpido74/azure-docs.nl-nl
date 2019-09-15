---
title: SSL-beëindiging met Azure Key Vault certificaten
description: Meer informatie over hoe u Azure-toepassing gateway kunt integreren met Key Vault voor server certificaten die zijn gekoppeld aan listeners met HTTPS-functionaliteit.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/25/2019
ms.author: victorh
ms.openlocfilehash: 725a9d67e6a6412fc48a4278b5a8a163272e5133
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000985"
---
# <a name="ssl-termination-with-key-vault-certificates"></a>SSL-beëindiging met Key Vault certificaten

[Azure Key Vault](../key-vault/key-vault-overview.md) is een door een platform beheerd geheim archief dat u kunt gebruiken voor het beveiligen van geheimen, sleutels en SSL-certificaten. Azure-toepassing gateway ondersteunt de integratie met Key Vault (in open bare preview) voor server certificaten die zijn gekoppeld aan listeners waarvoor HTTPS is ingeschakeld. Deze ondersteuning is beperkt tot de v2-SKU van Application Gateway.

> [!IMPORTANT]
> Integratie van Application Gateway met Key Vault is momenteel beschikbaar als open bare preview. Deze preview-versie wordt aangeboden zonder een Service Level Agreement (SLA) en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Deze open bare preview biedt twee modellen voor SSL-beëindiging:

- U kunt expliciet SSL-certificaten die zijn gekoppeld aan de listener opgeven. Dit model is de traditionele manier om SSL-certificaten door te geven aan Application Gateway voor SSL-beëindiging.
- U kunt eventueel een verwijzing naar een bestaand Key Vault certificaat of geheim opgeven wanneer u een HTTPS-listener maakt.

Application Gateway integratie met Key Vault biedt veel voor delen, waaronder:

- Sterkere beveiliging, omdat SSL-certificaten niet rechtstreeks worden afgehandeld door het team voor toepassings ontwikkeling. Met integratie kan een afzonderlijk beveiligings team het volgende doen:
  * Stel toepassings gateways in.
  * De levens cyclus van de toepassings gateway beheren.
  * Machtigingen verlenen aan geselecteerde toepassings gateways om toegang te krijgen tot certificaten die zijn opgeslagen in de sleutel kluis.
- Ondersteuning voor het importeren van bestaande certificaten in uw sleutel kluis. Of gebruik Key Vault Api's om nieuwe certificaten te maken en te beheren met een van de vertrouwde Key Vault partners.
- Ondersteuning voor het automatisch verlengen van certificaten die zijn opgeslagen in uw sleutel kluis.

Application Gateway ondersteunt momenteel alleen door software gevalideerde certificaten. HSM (Hardware Security module)-gevalideerde certificaten worden niet ondersteund. Nadat Application Gateway is geconfigureerd voor het gebruik van Key Vault certificaten, haalt de instanties het certificaat op Key Vault en installeert ze lokaal voor SSL-beëindiging. De exemplaren pollen ook Key Vault met een interval van 24 uur om een vernieuwde versie van het certificaat op te halen, als dit bestaat. Als er een bijgewerkt certificaat wordt gevonden, wordt het SSL-certificaat dat momenteel is gekoppeld aan de HTTPS-listener automatisch gedraaid.

## <a name="how-integration-works"></a>De werking van integratie

Voor de integratie van Application Gateway met Key Vault is een configuratie proces in drie stappen vereist:

1. **Een door de gebruiker toegewezen beheerde identiteit maken**

   U maakt of hergebruikt een bestaande door de gebruiker toegewezen beheerde identiteit, die Application Gateway gebruikt om certificaten uit Key Vault namens u op te halen. Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) voor meer informatie. Met deze stap maakt u een nieuwe identiteit in de Azure Active Directory Tenant. De identiteit wordt vertrouwd door het abonnement dat wordt gebruikt om de identiteit te maken.

1. **Uw sleutel kluis configureren**

   Vervolgens importeert u een bestaand certificaat of maakt u een nieuwe in uw sleutel kluis. Het certificaat wordt gebruikt door toepassingen die via de toepassings gateway worden uitgevoerd. In deze stap kunt u ook een sleutel kluis geheim gebruiken dat is opgeslagen als een met een wacht woord beperkt, base 64-gecodeerd PFX-bestand. We raden u aan om een certificaat type te gebruiken vanwege de mogelijkheid tot het vernieuwen van de sleutel die beschikbaar is voor de objecten van het type certificaat. Nadat u een certificaat of een geheim hebt gemaakt, definieert u het toegangs beleid in de sleutel kluis zodat de identiteit kan worden toegekend toegang tot het geheim *te krijgen.*

1. **De toepassings gateway configureren**

   Nadat u de twee voor gaande stappen hebt voltooid, kunt u een bestaande toepassings gateway instellen of wijzigen voor het gebruik van de door de gebruiker toegewezen beheerde identiteit. U kunt ook het SSL-certificaat van de HTTP-listener zo configureren dat deze verwijst naar de volledige URI van het Key Vault certificaat of de geheim-ID.

   ![Sleutel kluis certificaten](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>Volgende stappen

[SSL-beëindiging met Key Vault certificaten configureren met behulp van Azure PowerShell](configure-keyvault-ps.md)
