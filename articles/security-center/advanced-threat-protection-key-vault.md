---
title: Advanced beveiliging tegen bedreigingen instellen voor Azure Key Vault
description: In dit artikel wordt uitgelegd hoe u geavanceerde beveiliging tegen bedreigingen kunt instellen voor Azure Key Vault in Azure Security Center
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 07924201d4208a502aa979dda476c57cf6edd749
ms.sourcegitcommit: dfa543fad47cb2df5a574931ba57d40d6a47daef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2020
ms.locfileid: "77430547"
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
