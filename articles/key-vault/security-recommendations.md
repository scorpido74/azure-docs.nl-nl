---
title: Beveiligings aanbevelingen voor Azure Key Vault
description: Beveiligings aanbevelingen voor Azure Key Vault. Als u deze richt lijnen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/30/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: 5aa3ebc602396bc2d7f83150c66535039b1389e6
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73171338"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Beveiligings aanbevelingen voor Azure Key Vault

Dit artikel bevat beveiligings aanbevelingen voor Azure Key Vault. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model. Lees de [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. De beveiligings status van uw Azure-resources wordt periodiek geanalyseerd om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken.

- Zie [beveiligings aanbevelingen in azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.
- Zie [Wat is er Azure Security Center?](../security-center/security-center-intro.md) voor informatie over Azure Security Center.

## <a name="data-protection"></a>Databeveiliging

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
|Voorlopig verwijderen inschakelen | Met [zacht verwijderen](key-vault-ovw-soft-delete.md) kunt u verwijderde kluizen en kluis objecten herstellen |  - |
| Toegang tot kluis gegevens beperken  | Het principe van minimale bevoegdheid volgen en beperken welke leden van uw organisatie toegang hebben tot de kluis gegevens |  - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
| Het aantal gebruikers met toegang tot Inzender beperken | Als een gebruiker Inzender machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie de rol Inzender heeft toegang tot uw sleutel kluizen. Zorg ervoor dat alleen degenen met een nood zaak van toegang die bevoegd is toegang hebben tot uw kluizen en deze kunnen beheren. U kunt [beveiligde toegang tot een sleutel kluis](key-vault-secure-your-key-vault.md) lezen | - |

## <a name="monitoring"></a>Controleren

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
 Diagnostische logboeken in Key Vault moeten worden ingeschakeld | Logboeken inschakelen en ze tot een jaar bewaren. Zo kunt u een activiteiten spoor voor onderzoek doeleinden opnieuw maken wanneer er een beveiligings incident optreedt of uw netwerk is aangetast. | [Ja](../security-center/security-center-identity-access.md) |
| Beperk wie toegang heeft tot uw Azure Key kluis-logboeken | In [Key Vault logboeken](key-vault-logging.md) worden gegevens opgeslagen over de activiteiten die zijn uitgevoerd op uw kluis, zoals het maken of verwijderen van kluizen, sleutels, geheimen en kunnen worden gebruikt tijdens een onderzoek |  - |

## <a name="networking"></a>Networking

| Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|----|--|
|Netwerk risico beperken | Netwerk toegang moet worden beperkt tot de virtuele netwerken die worden gebruikt door oplossingen waarvoor kluis toegang is vereist. Bekijk informatie over de [service-eind punten van het virtuele netwerk voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassings provider om na te gaan of er aanvullende beveiligings vereisten gelden. Zie [documentatie voor beveiligde ontwikkel aars](../security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van beveiligde toepassingen.
