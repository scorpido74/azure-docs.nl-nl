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
ms.openlocfilehash: f7f716734875d652de575991a4dc1b7bfe948ae3
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773523"
---
# <a name="set-up-advanced-threat-protection-for-azure-key-vault-preview"></a>Advanced Threat Protection voor Azure Key Vault instellen (preview)

Advanced Threat Protection voor Azure Key Vault biedt een extra laag beveiligings informatie. Dit hulp programma detecteert mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Met behulp van de systeem eigen geavanceerde beveiliging tegen bedreigingen in Azure Security Center kunt u bedreigingen verhelpen zonder dat u een beveiligings expert bent en zonder dat u extra beveiligings bewakings systemen hoeft te leren.

Wanneer Security Center afwijkende activiteiten detecteert, worden er waarschuwingen weer gegeven. Er wordt ook een e-mail verzonden naar de abonnements beheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van de geïdentificeerde bedreigingen.

## <a name="set-up-advanced-threat-protection-from-azure-security-center"></a>Geavanceerde bedreigingen beveiliging instellen vanaf Azure Security Center

Advanced Threat Protection is standaard ingeschakeld voor al uw Key Vault-accounts wanneer u zich abonneert op de Security Center Standard-laag. Zie [prijzen](security-center-pricing.md)voor meer informatie.

Voer de volgende stappen uit om de beveiliging van een specifiek abonnement in of uit te scha kelen.

1. Selecteer in het linkerdeel venster in Security Center **prijzen & instellingen**.
1. Selecteer het abonnement met de opslag accounts waarvoor u bedreigings beveiliging wilt in-of uitschakelen.
1. Selecteer **Prijscategorie**.
1. Zoek in de groep **prijs categorie selecteren per resource type** de rij **sleutel kluizen** en selecteer **ingeschakeld** of **uitgeschakeld**.

    [![geavanceerde beveiliging tegen bedreigingen in-of uitschakelen voor Key Vault in Azure Security Center](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)
1. Selecteer **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Advanced Threat Protection voor Azure Key Vault kunt in-en uitschakelen. 

Zie het volgende artikel voor meer gerelateerde materialen:

- [Detectie van bedreigingen voor de Azure Services-lagen in Security Center: in](security-center-alerts-service-layer.md)dit artikel worden de waarschuwingen beschreven die betrekking hebben op geavanceerde beveiliging tegen bedreigingen voor Azure Key Vault.
