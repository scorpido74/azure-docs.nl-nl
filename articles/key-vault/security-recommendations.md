---
title: Beveiligings aanbevelingen voor Azure Key Vault
description: Beveiligings aanbevelingen voor Azure Key Vault. Als u deze richt lijnen implementeert, kunt u voldoen aan uw beveiligings verplichtingen, zoals beschreven in ons gedeelde verantwoordelijkheids model
services: key-vault
author: barclayn
manager: rkarlin
ms.service: key-vault
ms.topic: article
ms.date: 09/19/2019
ms.author: barclayn
ms.custom: security-recommendations
ms.openlocfilehash: e1c5382e1e55c5c73a5610c1c13f6bfcef65ebc1
ms.sourcegitcommit: b03516d245c90bca8ffac59eb1db522a098fb5e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71148274"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Beveiligings aanbevelingen voor Azure Key Vault

Dit artikel bevat beveiligings aanbevelingen voor Azure App Service. Als u deze aanbevelingen implementeert, kunt u voldoen aan uw beveiligings verplichtingen zoals beschreven in ons gedeelde verantwoordelijkheids model en wordt de algehele beveiliging van uw web-app-oplossingen verbeterd. Lees de [gedeelde verantwoordelijkheden voor Cloud Computing](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf)voor meer informatie over wat micro soft doet aan de verantwoordelijkheden van de service provider.

Enkele van de aanbevelingen in dit artikel kunnen automatisch worden bewaakt door Azure Security Center. Azure Security Center is de eerste verdedigings linie bij het beveiligen van uw resources in Azure. De beveiligings status van uw Azure-resources wordt periodiek geanalyseerd om mogelijke beveiligings problemen te identificeren. Vervolgens krijgt u aanbevelingen over hoe u deze kunt aanpakken.

- Zie [beveiligings aanbevelingen in azure Security Center](../security-center/security-center-recommendations.md)voor meer informatie over Azure Security Center aanbevelingen.
- Zie [Wat is er Azure Security Center?](../security-center/security-center-intro.md) voor informatie over Azure Security Center.

## <a name="recommendations"></a>Aanbevelingen

| Categorie | Aanbeveling | Opmerkingen | Beveiligingscentrum |
|-|-|----|--|
| Gegevensbescherming |Voorlopig verwijderen inschakelen | Met [zacht verwijderen](key-vault-ovw-soft-delete.md) kunt u verwijderde kluizen en kluis objecten herstellen |  - |
| Gegevensbescherming | Toegang tot kluis gegevens beperken  | Het principe van minimale bevoegdheid volgen en beperken welke leden van uw organisatie toegang hebben tot de kluis gegevens |  - |
| Identiteits- en toegangsbeheer | Het aantal gebruikers met toegang tot Inzender beperken | Als een gebruiker Inzender machtigingen heeft voor een sleutel kluis beheer vlak, kan de gebruiker zichzelf toegang verlenen tot het gegevens vlak door een Key Vault toegangs beleid in te stellen. U moet nauw keurig bepalen wie de rol Inzender heeft toegang tot uw sleutel kluizen. Zorg ervoor dat alleen degenen met een nood zaak van toegang die bevoegd is toegang hebben tot uw kluizen en deze kunnen beheren. U kunt [beveiligde toegang tot een sleutel kluis](key-vault-secure-your-key-vault.md) lezen | - |
| Bewaking | Diagnostische logboeken in Key Vault moeten worden ingeschakeld | Logboeken inschakelen en ze maximaal een jaar bewaren. Hiermee kunt u activiteit sporen onderzoek opnieuw maken wanneer een beveiligingsincident voordoet of uw netwerk is gecompromitteerd. | [Ja](../security-center/security-center-identity-access.md) |
| Bewaking | Beperk wie toegang heeft tot uw Azure Key kluis-logboeken | In [Key Vault logboeken](key-vault-logging.md) worden gegevens opgeslagen over de activiteiten die zijn uitgevoerd op uw kluis, zoals het maken of verwijderen van kluizen, sleutels, geheimen en kunnen worden gebruikt tijdens een onderzoek |  - |
| Netwerken |Netwerk risico beperken | Netwerk toegang moet worden beperkt tot de virtuele netwerken die worden gebruikt door oplossingen waarvoor kluis toegang is vereist. Bekijk informatie over de [service-eind punten van het virtuele netwerk voor Azure Key Vault](key-vault-overview-vnet-service-endpoints.md) | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassings provider om na te gaan of er aanvullende beveiligings vereisten gelden. Zie [documentatie voor beveiligde ontwikkel aars](../security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van beveiligde toepassingen.
