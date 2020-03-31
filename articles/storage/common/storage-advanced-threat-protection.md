---
title: Geavanceerde bedreigingsbeveiliging configureren
titleSuffix: Azure Storage
description: Configureer geavanceerde bedreigingsbeveiliging voor Azure Storage om afwijkingen in accountactiviteit te detecteren en op de hoogte te worden gesteld van mogelijk schadelijke pogingen om toegang te krijgen tot uw account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 02/04/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: 27860b8761c565c45a604253efdff5f77606606e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061301"
---
# <a name="configure-advanced-threat-protection-for-azure-storage"></a>Geavanceerde bedreigingsbeveiliging configureren voor Azure Storage

Geavanceerde bedreigingsbeveiliging voor Azure Storage biedt een extra laag beveiligingsinformatie die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot opslagaccounts of deze te exploiteren. Met deze beveiligingslaag u bedreigingen aanpakken zonder beveiligingsexpert te zijn of beveiligingsbewakingssystemen te beheren.

Beveiligingswaarschuwingen worden geactiveerd wanneer er afwijkingen in de activiteit optreden. Deze beveiligingswaarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnementsbeheerders, met details over verdachte activiteiten en aanbevelingen over het onderzoeken en oplossen van bedreigingen.

> [!NOTE]
> Geavanceerde bedreigingsbeveiliging voor Azure Storage is momenteel alleen beschikbaar voor Blob-opslag. Het is niet beschikbaar in Azure-overheids- en soevereine cloudregio's. Zie de [prijspagina]( https://azure.microsoft.com/pricing/details/security-center/)van azure security center voor prijsgegevens, waaronder een gratis proefperiode van 30 dagen.

Geavanceerde bedreigingsbeveiliging voor Azure Storage neemt diagnostische logboeken van lezen, schrijven en verwijderen van aanvragen voor Blob-opslag voor bedreigingsdetectie in. Als u de waarschuwingen van geavanceerde bedreigingsbeveiliging wilt onderzoeken, u gerelateerde opslagactiviteiten bekijken met Storage Analytics Logging. Zie **Logboekregistratie configureren** in [Monitor een opslagaccount in de Azure-portal](storage-monitor-storage-account.md#configure-logging)configureren voor meer informatie.

## <a name="set-up-advanced-threat-protection"></a>Geavanceerde bescherming tegen bedreigingen instellen

U geavanceerde bedreigingsbeveiliging op verschillende manieren configureren, beschreven in de volgende secties.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Start de [Azure-portal](https://portal.azure.com/).
1. Navigeer naar uw Azure Storage-account. Selecteer **Onder Instellingen**de optie Geavanceerde **beveiliging**.
1. Selecteer de koppeling **Instellingen** op de pagina geavanceerde beveiligingsconfiguratie.
1. **Geavanceerde beveiliging** instellen op **AAN**.
1. Klik **op Opslaan** om het nieuwe of bijgewerkte beleid op te slaan.

    ![Geavanceerde bedreigingsbeveiliging van Azure Storage inschakelen](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-turn-on.png)

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Wanneer u zich abonneert op de standaardlaag in Azure Security Center, wordt geavanceerde bedreigingsbeveiliging automatisch ingesteld op al uw opslagaccounts. U geavanceerde bedreigingsbeveiliging voor uw opslagaccounts onder een specifiek abonnement als volgt in- of uitschakelen:

1. **Azure Security Center starten** in de [Azure-portal.](https://portal.azure.com)
1. Klik in het hoofdmenu op **Prijzen & instellingen**.
1. Klik op het abonnement dat u wilt inschakelen of uitschakelen van bedreigingsbeveiliging voor de opslagaccounts.

    ![Abonnement selecteren](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-subscription.png)

1. Klik **op Prijslaag**.
1. Klik in de sectie **Prijscategorie selecteren op resourcetype** in de rij **Opslagaccounts** op **Ingeschakeld** of **Uitgeschakeld**.

    ![ATP inschakelen in beveiligingscentrum](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-pricing2.png)
1. Klik op **Opslaan**.

### <a name="template"></a>[Sjabloon](#tab/template)

Gebruik een Azure Resource Manager-sjabloon om een Azure Storage-account te implementeren met ingeschakelde geavanceerde bedreigingsbeveiliging. Zie [Opslagaccount met geavanceerde bedreigingsbeveiliging](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)voor meer informatie .

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Gebruik een Azure-beleid om geavanceerde bedreigingsbeveiliging in te schakelen voor opslagaccounts onder een specifiek abonnement of brongroep.

1. Start de pagina Azure **Policy - Definitions.**

1. Zoek naar het beleid **Geavanceerde bedreigingsbeveiliging implementeren voor opslagaccounts.**

     ![Zoekbeleid](./media/storage-advanced-threat-protection/storage-atp-policy-definitions.png)

1. Selecteer een Azure-abonnement of -brongroep.

    ![Abonnement of groep selecteren](./media/storage-advanced-threat-protection/storage-atp-policy2.png)

1. Wijs het beleid toe.

    ![Pagina Beleidsdefinities](./media/storage-advanced-threat-protection/storage-atp-policy1.png)

### <a name="rest-api"></a>[REST-API](#tab/rest-api)

Gebruik Rest API-opdrachten om de geavanceerde instelling voor bedreigingsbeveiliging voor een specifiek opslagaccount te maken, bij te werken of te krijgen.

* [Geavanceerde bescherming tegen bedreigingen - Maak](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
* [Geavanceerde bescherming tegen bedreigingen - Get](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Gebruik de volgende PowerShell-cmdlets:

* [Geavanceerde bescherming tegen bedreigingen inschakelen](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
* [Krijg geavanceerde bescherming tegen bedreigingen](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
* [Geavanceerde bedreigingsbescherming uitschakelen](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Beveiligingsafwijkingen verkennen

Wanneer er afwijkingen van opslagactiviteiten optreden, ontvangt u een e-mailmelding met informatie over de verdachte beveiligingsgebeurtenis. Details van het evenement zijn:

* De aard van de anomalie
* Naam van het opslagaccount
* De gebeurtenistijd
* Het opslagtype
* De mogelijke oorzaken
* De onderzoeksstappen
* De herstelstappen

De e-mail bevat ook details over mogelijke oorzaken en aanbevolen acties om de potentiële bedreiging te onderzoeken en te beperken.

![E-mail met waarschuwing voor geavanceerde bedreigingsbeveiliging van Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert-email.png)

U uw huidige beveiligingswaarschuwingen bekijken en beheren via de [tegel Beveiligingswaarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md)van Azure Security Center. Als u op een specifieke waarschuwing klikt, worden details en acties gegeven voor het onderzoeken van de huidige dreiging en het aanpakken van toekomstige bedreigingen.

![E-mail met waarschuwing voor geavanceerde bedreigingsbeveiliging van Azure Storage](./media/storage-advanced-threat-protection/storage-advanced-threat-protection-alert.png)

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om toegang te krijgen tot opslagaccounts of deze te exploiteren. Zie de sectie **Opslag** in [Bedreigingsbeveiliging voor gegevensservices in Azure Security Center voor](https://docs.microsoft.com/azure/security-center/alerts-reference#alerts-azurestorage)een lijst met waarschuwingen voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [logboeken in Azure Storage-accounts](/rest/api/storageservices/About-Storage-Analytics-Logging)
* Meer informatie over [Azure Security Center](../../security-center/security-center-intro.md)
