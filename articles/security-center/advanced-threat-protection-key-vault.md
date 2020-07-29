---
title: Bedreigings beveiliging voor Azure Key Vault
description: In dit artikel wordt uitgelegd hoe u geavanceerde beveiliging tegen bedreigingen kunt instellen voor Azure Key Vault in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "77914803"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Bedreigings beveiliging voor Azure Key Vault (preview-versie)

Advanced Threat Protection voor Azure Key Vault biedt een extra laag beveiligings informatie. Dit hulp programma detecteert mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Met behulp van de systeem eigen geavanceerde beveiliging tegen bedreigingen in Azure Security Center kunt u bedreigingen verhelpen zonder dat u een beveiligings expert bent en zonder dat u extra beveiligings bewakings systemen hoeft te leren.

Wanneer Security Center afwijkende activiteiten detecteert, worden er waarschuwingen weer gegeven. Er wordt ook een e-mail verzonden naar de abonnements beheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de geïdentificeerde bedreigingen.

## <a name="configuring-threat-protection-from-security-center"></a>Beveiliging tegen bedreigingen configureren vanaf Security Center

Advanced Threat Protection is standaard ingeschakeld voor al uw Key Vault accounts wanneer u zich abonneert op de prijs categorie Standard van Security Center. Zie [prijzen](security-center-pricing.md)voor meer informatie.

De beveiliging van een specifiek abonnement in-of uitschakelen:

1. Selecteer in het linkerdeel venster in Security Center **prijzen & instellingen**.

1. Selecteer het abonnement met de opslag accounts waarvoor u bedreigings beveiliging wilt in-of uitschakelen.

1. Selecteer de **prijs categorie**.

1. Zoek in de groep **prijs categorie selecteren per resource type** de rij **sleutel kluizen** en selecteer **ingeschakeld** of **uitgeschakeld**.

    [![Geavanceerde beveiliging tegen bedreigingen in-of uitschakelen voor Key Vault in Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecteer **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Advanced Threat Protection voor Azure Key Vault kunt in-en uitschakelen. 

Raadpleeg de volgende artikelen voor gerelateerde materialen:

- [Beveiliging tegen bedreigingen in azure Security Center](threat-protection.md): in dit artikel worden de bronnen van beveiligings waarschuwingen in azure Security Center beschreven.
- [Beveiligings waarschuwingen Key Vault](alerts-reference.md#alerts-azurekv): de sectie Key Vault van de verwijzings tabel voor alle Azure Security Center waarschuwingen