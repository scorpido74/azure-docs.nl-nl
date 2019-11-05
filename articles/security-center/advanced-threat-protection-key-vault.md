---
title: Geavanceerde bedreigings beveiliging instellen voor Azure Key Vault | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u geavanceerde beveiliging tegen bedreigingen kunt instellen voor Azure Key Vault in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: b28f03f0997b6bb2c494c35cee9695a478906c47
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73521876"
---
# <a name="how-to-set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Advanced Threat Protection voor Azure Key Vault instellen (preview)

Advanced Threat Protection voor Azure Key Vault biedt een extra laag beveiligings informatie. Dit hulp programma detecteert mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Met behulp van de systeem eigen Advanced Threat Protection van Security Center kunt u bedreigingen verhelpen zonder dat u een beveiligings expert bent, zonder dat u aanvullende systemen voor beveiligings bewaking hoeft te leren.

Wanneer Security Center afwijkende activiteiten detecteert, worden er waarschuwingen weer gegeven. Er wordt ook een e-mail verzonden naar de abonnements beheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de geïdentificeerde bedreigingen. 

> [!NOTE]
> Advanced Threat Protection voor Azure Key Vault is momenteel alleen beschikbaar in Noord-Amerika regio's.

## <a name="to-set-up-advanced-threat-protection-from-azure-security-center"></a>Geavanceerde beveiliging tegen bedreigingen instellen vanaf Azure Security Center

Advanced Threat Protection is standaard ingeschakeld voor al uw Key Vault-accounts wanneer u zich abonneert op de Standard-laag van Security Center (Zie [prijzen](security-center-pricing.md)). 

De beveiliging van een specifiek abonnement in-of uitschakelen:

1. Klik in de zijbalk van Security Center op **prijzen & instellingen**.
1. Selecteer het abonnement met de opslag accounts waarvoor u bedreigings beveiliging wilt in-of uitschakelen.
1. Klik op **prijs categorie**.
1. Zoek in de groep **prijs categorie selecteren per resource type** de rij sleutel kluizen en klik op **ingeschakeld** of **uitgeschakeld**.
    [![het in-of uitschakelen van de geavanceerde beveiliging tegen bedreigingen voor Key Vault in Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Klik op **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Advanced Threat Protection voor Azure Key Vault kunt in-en uitschakelen. 

Zie het volgende artikel voor meer gerelateerde materialen:

- [Detectie van bedreigingen voor de Azure Services-lagen in Security Center: in](security-center-alerts-service-layer.md) dit artikel worden de waarschuwingen beschreven die betrekking hebben op Advanced threat protection voor Azure Key Vault