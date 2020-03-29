---
title: Bescherming tegen bedreigingen voor Azure Key Vault
description: In dit artikel wordt uitgelegd hoe u geavanceerde bedreigingsbeveiliging voor Azure Key Vault in Azure Security Center instellen
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 449096590df6145c9f80dcf2c97726931909a2ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914803"
---
# <a name="threat-protection-for-azure-key-vault-preview"></a>Bescherming tegen bedreigingen voor Azure Key Vault (voorbeeld)

Geavanceerde bescherming van bedreigingen voor Azure Key Vault biedt een extra laag beveiligingsinformatie. Deze tool detecteert mogelijk schadelijke pogingen om toegang te krijgen tot key vault-accounts of deze te exploiteren. Met behulp van de native geavanceerde bedreigingsbeveiliging in Azure Security Center u bedreigingen aanpakken zonder beveiligingsexpert te zijn en zonder aanvullende beveiligingsbewakingssystemen te leren.

Wanneer Security Center afwijkende activiteit detecteert, worden waarschuwingen weergegeven. Het e-mailt ook de abonnementsbeheerder met details van de verdachte activiteit en aanbevelingen voor het onderzoeken en herstellen van de geïdentificeerde bedreigingen.

## <a name="configuring-threat-protection-from-security-center"></a>Bedreigingsbeveiliging configureren vanuit Security Center

Standaard is geavanceerde bescherming tegen bedreigingen ingeschakeld voor al uw Key Vault-accounts wanneer u zich abonneert op de standaardprijscategorie van security center. Zie [Prijzen voor](security-center-pricing.md)meer informatie.

Ga als lid in of schakel de beveiliging voor een specifiek abonnement uit:

1. Selecteer **prijzen & instellingen**in het linkerdeelvenster in het beveiligingscentrum.

1. Selecteer het abonnement met de opslagaccounts waarvoor u bedreigingsbeveiliging wilt in- of uitschakelen.

1. Selecteer **Prijsniveau**.

1. Zoek in de **categorie Prijsselecteren op resourcegroep** de rij **Sleutelkluizen** en selecteer **Ingeschakeld** of **uitgeschakeld**.

    [![Geavanceerde bedreigingsbeveiliging voor Key Vault inazure security center inschakelen of uitschakelen](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png)](media/advanced-threat-protection-key-vault/atp-for-akv-enable-atp-for-akv.png#lightbox)

1. Selecteer **Opslaan**.


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u geavanceerde bedreigingsbeveiliging voor Azure Key Vault inschakelen en uitschakelen. 

Zie voor gerelateerd materiaal de volgende artikelen:

- [Bescherming van bedreigingen in Azure Security Center](threat-protection.md)--Dit artikel beschrijft de bronnen van beveiligingswaarschuwingen in Azure Security Center.
- [Key Vault-beveiligingswaarschuwingen](alerts-reference.md#alerts-azurekv)--Het gedeelte Sleutelkluis van de referentietabel voor alle Azure Security Center-waarschuwingen