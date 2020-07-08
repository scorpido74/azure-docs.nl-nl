---
title: Geavanceerde bedreigingen beveiliging configureren
titleSuffix: Azure Storage
description: Geavanceerde bedreigings beveiliging configureren voor Azure Storage om afwijkingen in de account activiteit te detecteren en op de hoogte te worden gesteld van mogelijk schadelijke pogingen om toegang te krijgen tot uw account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 04/16/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: f69301bdbc0af9334858940fbfd3d7d0a0a63153
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807650"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Geavanceerde bedreigings beveiliging voor Azure Storage configureren

Advanced Threat Protection voor Azure Storage biedt een extra beveiligingslaag waarmee ongebruikelijke en mogelijk schadelijke pogingen voor het openen of exploiteren van opslag accounts worden gedetecteerd. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder beveiligings expert of beveiligings bewakings systemen te beheren.

Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. Deze beveiligings waarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

De service neemt bron logboeken van lees-, schrijf-en verwijderings aanvragen op Blob Storage voor detectie van bedreigingen. Als u de waarschuwingen van Advanced Threat Protection wilt onderzoeken, kunt u gerelateerde opslag activiteiten bekijken met Opslaganalyse logboek registratie. Zie **logboek registratie configureren** in [een opslag account in het Azure Portal bewaken](storage-monitor-storage-account.md#configure-logging)voor meer informatie.

## <a name="availability"></a>Beschikbaarheid

Advanced Threat Protection voor Azure Storage is momenteel alleen beschikbaar voor [Blob Storage](https://azure.microsoft.com/services/storage/blobs/). Account typen die ondersteuning bieden voor geavanceerde beveiliging tegen bedreigingen zijn: v2-, blok-Blob-en Blob Storage-accounts voor algemeen gebruik. Advanced Threat Protection is beschikbaar in alle open bare Clouds en Amerikaanse overheids Clouds, maar niet in andere soevereine of Azure Government-Cloud regio's.

Voor prijs informatie, inclusief een gratis proef versie van 30 dagen, raadpleegt u de [pagina met Azure Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/).


## <a name="set-up-advanced-threat-protection"></a>Geavanceerde bedreigings beveiliging instellen

U kunt geavanceerde beveiliging tegen bedreigingen op verschillende manieren configureren, zoals beschreven in de volgende secties.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Start de [Azure Portal](https://portal.azure.com/).
1. Navigeer naar uw Azure Storage-account. Selecteer onder **instellingen**de optie **geavanceerde beveiliging**.
1. Selecteer de koppeling **instellingen** op de pagina Geavanceerde beveiligings configuratie.
1. Stel **geavanceerde beveiliging** in **op on**.
1. Klik op **Opslaan** om het nieuwe of bijgewerkte beleid op te slaan.

    ![Azure Storage Advanced Threat Protection inschakelen](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Wanneer u zich abonneert op de Standard-laag in Azure Security Center, wordt geavanceerde beveiliging tegen bedreigingen automatisch ingesteld op al uw opslag accounts. U kunt de geavanceerde beveiliging tegen bedreigingen voor uw opslag accounts onder een specifiek abonnement als volgt in-of uitschakelen:

1. Start **Azure Security Center** in het [Azure Portal](https://portal.azure.com).
1. Klik in het hoofd menu op **prijzen & instellingen**.
1. Klik op het abonnement dat u wilt in-of uitschakelen bedreigings beveiliging voor de bijbehorende opslag accounts.

    ![Abonnement selecteren](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klik op **prijs categorie**.
1. Klik in de sectie **prijs categorie op resource type selecteren** in de rij **opslag accounts** op **ingeschakeld** of **uitgeschakeld**.

    ![ATP in Security Center inschakelen](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klik op **Opslaan**.

### <a name="template"></a>[Sjabloon](#tab/template)

Gebruik een Azure Resource Manager sjabloon om een Azure Storage account te implementeren waarop Advanced Threat Protection is ingeschakeld. Zie [opslag account met geavanceerde beveiliging tegen bedreigingen](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)voor meer informatie.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Gebruik een Azure Policy om geavanceerde bedreigings beveiliging in te scha kelen voor opslag accounts onder een specifiek abonnement of resource groep.

1. Start de pagina Azure **Policy-Definitions** .

1. Zoek naar het beleid **geavanceerde beveiliging tegen bedreigingen implementeren op opslag accounts** .

     ![Zoek beleid](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecteer een Azure-abonnement of resource groep.

    ![Abonnement of groep selecteren](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Wijs het beleid toe.

    ![Pagina beleids definities](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Gebruik rest API-opdrachten om de geavanceerde instelling voor beveiliging tegen bedreigingen te maken, bij te werken of op te halen voor een specifiek opslag account.

* [Advanced Threat Protection-maken](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Advanced Threat Protection-ophalen](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-cmdlets:

* [Advanced Threat Protection inschakelen](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Geavanceerde beveiliging tegen bedreigingen verkrijgen](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Geavanceerde bedreigings beveiliging uitschakelen](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Beveiligings afwijkingen verkennen

Wanneer er afwijkingen optreden bij de opslag activiteit, ontvangt u een e-mail melding met informatie over de verdachte beveiligings gebeurtenis. Details van de gebeurtenis zijn onder andere:

* De aard van de anomalie
* Naam van het opslagaccount
* De tijd van de gebeurtenis
* Het opslag type
* De mogelijke oorzaken
* De onderzoek stappen
* De herstels tappen

Het e-mail bericht bevat ook informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van mogelijke bedreigingen.

![E-mail bericht over de geavanceerde beveiliging tegen bedreigingen Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

U kunt uw huidige beveiligings waarschuwingen controleren en beheren via de [tegel beveiligings waarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md)van Azure Security Center. Als u op een specifieke waarschuwing klikt, vindt u details en acties voor het onderzoeken van de huidige dreiging en het adresseren van toekomstige bedreigingen.

![E-mail bericht over de geavanceerde beveiliging tegen bedreigingen Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Zie de sectie **opslag** in [bedreigings beveiliging voor gegevens Services in azure Security Center](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)voor een lijst met waarschuwingen voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logboeken in azure Storage-accounts](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Meer informatie over [Azure Security Center](../../security-center/security-center-intro.md)
