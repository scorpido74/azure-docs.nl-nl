---
title: Beveiligingsaanbevelingen voor Azure Key Vault
description: Beveiligingsaanbevelingen voor Azure Key Vault. De implementatie van deze richtlijnen zal u helpen uw beveiligingsverplichtingen na te komen, zoals beschreven in ons gedeelde verantwoordelijkheidsmodel
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: article
ms.date: 09/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 0da1a3019124f62aba6a959ce9104c85bd85d3fc
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616486"
---
# <a name="security-recommendations-for-azure-key-vault"></a>Beveiligingsaanbevelingen voor Azure Key Vault

Dit artikel bevat beveiligingsaanbevelingen voor Azure Key Vault. Door deze aanbevelingen uit te voeren, u voldoen aan uw beveiligingsverplichtingen zoals beschreven in ons gedeelde verantwoordelijkheidsmodel. Lees [Gedeelde verantwoordelijkheden voor cloud computing voor](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)meer informatie over wat Microsoft doet om de verantwoordelijkheden van serviceproviders te vervullen.

Sommige aanbevelingen in dit artikel kunnen automatisch worden gecontroleerd door Azure Security Center. Azure Security Center is de eerste verdedigingslinie bij het beveiligen van uw resources in Azure. Het analyseert periodiek de beveiligingsstatus van uw Azure-bronnen om potentiële beveiligingsproblemen te identificeren. Het geeft u vervolgens aanbevelingen over hoe ze aan te pakken.

- Zie [Beveiligingsaanbevelingen in Azure Security Center](../../security-center/security-center-recommendations.md)voor meer informatie over aanbevelingen voor Azure Security Center.
- Zie Wat is Azure Security Center voor informatie over Azure Security [Center?](../../security-center/security-center-intro.md)

## <a name="data-protection"></a>Gegevensbeveiliging

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
|Soft-delete inschakelen | [Soft-delete](overview-soft-delete.md)) u verwijderde kluizen en kluisobjecten herstellen |  - |
| Toegang tot kluisgegevens beperken  | Volg het principe van de minste bevoegdheden en beperken welke leden van uw organisatie toegang hebben tot kluisgegevens |  - |

## <a name="identity-and-access-management"></a>Identiteits- en toegangsbeheer

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
| Het aantal gebruikers beperken met toegang tot bijdragen | Als een gebruiker machtigingen voor bijdragen heeft aan een belangrijk vault management vliegtuig, kan de gebruiker zichzelf toegang verlenen tot het gegevensvlak door een Key Vault toegangsbeleid in te stellen. U moet goed bepalen wie de rol van Contributor toegang heeft tot uw sleutelkluizen. Zorg ervoor dat alleen personen met een behoefte aan toegang tot geautoriseerde personen toegang hebben tot uw kluizen en deze beheren. U [Secure access to a key vault](secure-your-key-vault.md)lezen) | - |

## <a name="monitoring"></a>Bewaking

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
 Diagnostische logboeken in Key Vault moeten zijn ingeschakeld | Schakel logboeken in en bewaar ze tot een jaar. Hiermee u activiteitssporen opnieuw maken voor onderzoeksdoeleinden wanneer zich een beveiligingsincident voordoet of uw netwerk is aangetast. | [Ja](../../security-center/security-center-identity-access.md) |
| Beperken wie toegang heeft tot uw Azure Key-kluislogboeken | [Key Vault-logboeken](logging.md)) informatie opslaan over de activiteiten die in uw kluis worden uitgevoerd, zoals het aanmaken of verwijderen van kluizen, sleutels, geheimen en kunnen worden gebruikt tijdens een onderzoek |  - |

## <a name="networking"></a>Netwerken

| Aanbeveling | Opmerkingen | Security Center |
|-|----|--|
|Netwerkblootstelling beperken | De toegang tot het netwerk moet worden beperkt tot de virtuele netwerken die worden gebruikt door oplossingen die toegang tot kluizen vereisen. Informatie over [eindpunten voor virtuele netwerkservices voor Azure Key Vault](overview-vnet-service-endpoints.md)) | - |

## <a name="next-steps"></a>Volgende stappen

Neem contact op met uw toepassingsprovider om te zien of er aanvullende beveiligingsvereisten zijn. Zie [Beveiligde ontwikkelingsdocumentatie](../../security/fundamentals/abstract-develop-secure-apps.md)voor meer informatie over het ontwikkelen van veilige toepassingen.
