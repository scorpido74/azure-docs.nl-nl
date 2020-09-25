---
title: 'Azure Defender voor Key Vault: de voor delen en functies'
description: Meer informatie over de voor delen en functies van Azure Defender voor Key Vault.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: 78a522922f8580003e2d2ff588cbf10dbf5fff9d
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301768"
---
# <a name="introduction-to-azure-defender-for-key-vault"></a>Inleiding tot Azure Defender voor Key Vault

Azure Key Vault is een Cloud service die versleutelings sleutels en geheimen beveiligt, zoals certificaten, verbindings reeksen en wacht woorden. 

Schakel **Azure Defender voor Key Vault** in voor de Azure-systeem eigen, geavanceerde bedreigings beveiliging voor Azure Key Vault en bied een extra beveiligingslaag. 

## <a name="availability"></a>Beschikbaarheid

|Aspect|Details|
|----|:----|
|Release status:|Algemeen beschikbaar (GA)|
|Koers|**Azure Defender voor Key Vault** wordt gefactureerd zoals weer gegeven op [de pagina met prijzen](security-center-pricing.md)|
|Clouds|![Yes](./media/icons/yes-icon.png) Commerciële Clouds<br>![No](./media/icons/no-icon.png) National/soeverein (US Gov, China gov, andere gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-key-vault"></a>Wat zijn de voor delen van Azure Defender voor Key Vault?

Azure Defender detecteert ongebruikelijke en mogelijk schadelijke pogingen om Key Vault accounts te openen of misbruik te maken. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder een beveiligings expert en zonder de nood zaak om beveiligings bewakings systemen van derden te beheren.  

Als er afwijkende activiteiten optreden, worden in azure Defender waarschuwingen weer gegeven en worden ze optioneel verzonden via e-mail naar de relevante leden van uw organisatie. Deze waarschuwingen omvatten de details van de verdachte activiteit en aanbevelingen voor het onderzoeken en oplossen van bedreigingen. 

## <a name="azure-defender-for-key-vault-alerts"></a>Waarschuwingen voor Azure Defender voor Key Vault
Wanneer u een waarschuwing ontvangt van Azure Defender voor Key Vault, raden we u aan de waarschuwing te onderzoeken en erop te reageren zoals beschreven in [reageren op Azure Defender voor Key Vault](defender-for-key-vault-usage.md). Azure Defender voor Key Vault beveiligt toepassingen en referenties, zelfs als u bekend bent met de toepassing of gebruiker die de waarschuwing heeft geactiveerd, is het belang rijk om de situatie rondom elke waarschuwing te controleren.

De waarschuwingen worden weer gegeven op de pagina **beveiliging** van Key Vault, het dash board van Azure Defender en de pagina waarschuwingen van Security Center.

:::image type="content" source="./media/defender-for-key-vault-intro/key-vault-security-page.png" alt-text="Beveiligings pagina van Azure Key Vault":::


> [!TIP]
> U kunt Azure Defender for Key Vault-waarschuwingen simuleren door de instructies te volgen in het [valideren van Azure Key Vault detectie van bedreigingen in azure Security Center](https://techcommunity.microsoft.com/t5/azure-security-center/validating-azure-key-vault-threat-detection-in-azure-security/ba-p/1220336).


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Defender voor Key Vault.

Raadpleeg de volgende artikelen voor gerelateerde materialen: 

- [Beveiligings waarschuwingen Key Vault](alerts-reference.md#alerts-azurekv): de sectie Key Vault van de verwijzings tabel voor alle Azure Security Center waarschuwingen
- [Waarschuwingen exporteren naar een SIEM](continuous-export.md)
- [Waarschuwingen van Azure Defender onderdrukken](alerts-suppression-rules.md)