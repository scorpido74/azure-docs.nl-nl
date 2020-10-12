---
title: Azure Defender voor opslag configureren
titleSuffix: Azure Storage
description: Configureer Azure Defender voor opslag om afwijkingen in de account activiteit te detecteren en op de hoogte te worden gesteld van mogelijk schadelijke pogingen om toegang te krijgen tot uw account.
services: storage
author: tamram
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: tamram
ms.reviewer: ozgun
ms.openlocfilehash: c7e0c9aee1ce6b4a2524ac756673784b63be3b31
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91289766"
---
# <a name="configure-azure-defender-for-storage"></a>Azure Defender voor opslag configureren

Azure Defender voor opslag biedt een extra beveiligingslaag die ongebruikelijke en mogelijk schadelijke pogingen detecteert om toegang te krijgen tot of misbruik te maken van opslag accounts. Deze beveiligingslaag biedt u de mogelijkheid bedreigingen te verhelpen zonder beveiligings expert of beveiligings bewakings systemen te beheren.

Beveiligings waarschuwingen worden geactiveerd wanneer afwijkingen in de activiteit optreden. Deze beveiligings waarschuwingen zijn geïntegreerd met [Azure Security Center](https://azure.microsoft.com/services/security-center/)en worden ook via e-mail verzonden naar abonnements beheerders, met details over verdachte activiteiten en aanbevelingen voor het onderzoeken en oplossen van bedreigingen.

De service neemt bron logboeken op met lees-, schrijf-en verwijder aanvragen van Blob-opslag en Azure Files voor detectie van bedreigingen. Als u waarschuwingen van Azure Defender wilt onderzoeken, kunt u gerelateerde opslag activiteiten bekijken met Opslaganalyse logboek registratie. Zie **logboek registratie configureren** in [een opslag account in het Azure Portal bewaken](storage-monitor-storage-account.md#configure-logging)voor meer informatie.

## <a name="availability"></a>Beschikbaarheid

Azure Defender voor opslag is momenteel beschikbaar voor Blob Storage, Azure Files en Azure Data Lake Storage Gen2. Account typen die ondersteuning bieden voor Azure Defender zijn: v2-, blok-Blob-en Blob Storage-accounts voor algemeen gebruik. Azure Defender voor opslag is beschikbaar in alle open bare Clouds en Amerikaanse overheids Clouds, maar niet in andere soevereine of Azure Government Cloud regio's.

Accounts met hiërarchische naam ruimten die zijn ingeschakeld voor Data Lake Storage ondersteunings transacties met behulp van de Azure Blob Storage-Api's en de Data Lake Storage-Api's. Azure-bestands shares ondersteunen trans acties via SMB.

Voor prijs informatie, inclusief een gratis proef versie van 30 dagen, raadpleegt u de [pagina met Azure Security Center prijzen](https://azure.microsoft.com/pricing/details/security-center/).

De volgende lijst bevat een overzicht van de beschik baarheid van Azure Defender voor opslag:

- Releasestatus:
  - [Blob Storage](https://azure.microsoft.com/services/storage/blobs/) (algemene Beschik baarheid)
  - [Azure files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) (algemene Beschik baarheid)
  - Azure Data Lake Storage Gen2 (algemene Beschik baarheid)
- Clouds:<br>
    ✔ Commerciële Clouds<br>
    ✔ US Gov<br>
    ✘ China gov, andere gov

## <a name="set-up-azure-defender"></a>Azure Defender instellen

U kunt Azure Defender voor opslag op verschillende manieren configureren, zoals beschreven in de volgende secties.

### <a name="azure-security-center"></a>[Azure Security Center](#tab/azure-security-center)

Wanneer u zich abonneert op de Standard-laag in Azure Security Center, wordt Azure Defender automatisch ingesteld op al uw opslag accounts. U kunt Azure Defender voor uw opslag accounts onder een specifiek abonnement als volgt in-of uitschakelen:

1. Start **Azure Security Center** in het [Azure Portal](https://portal.azure.com).
1. Selecteer in het hoofd menu onder **beheer**de optie **prijzen & instellingen**.
1. Selecteer het abonnement waarvoor u Azure Defender wilt in-of uitschakelen.
1. Selecteer **Azure Defender op** om Azure Defender in te scha kelen voor het abonnement.
1. Ga naar het **resource type Azure Defender-plan selecteren**, zoek de rij **Storage** en selecteer **ingeschakeld** in de kolom **plan** .
1. Sla uw wijzigingen op.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-security-center.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

Azure Defender is nu ingeschakeld voor alle opslag accounts in dit abonnement.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Start de [Azure Portal](https://portal.azure.com/).
1. Ga naar uw opslagaccount. Selecteer onder **instellingen**de optie **geavanceerde beveiliging**.
1. Selecteer **Azure Defender inschakelen voor opslag**.

    :::image type="content" source="media/azure-defender-storage-configure/enable-azure-defender-portal.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

Azure Defender is nu ingeschakeld voor dit opslag account.

### <a name="template"></a>[Sjabloon](#tab/template)

Gebruik een Azure Resource Manager sjabloon om een Azure Storage account te implementeren waarop Azure Defender is ingeschakeld. Zie [opslag account met geavanceerde beveiliging tegen bedreigingen](https://azure.microsoft.com/resources/templates/201-storage-advanced-threat-protection-create/)voor meer informatie.

### <a name="azure-policy"></a>[Azure Policy](#tab/azure-policy)

Gebruik een Azure Policy om Azure Defender in te scha kelen voor opslag accounts onder een specifiek abonnement of resource groep.

1. Start de pagina Azure **Policy-Definitions** .
1. Zoek het beleid **Azure Defender implementeren op opslag accounts** .

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy-definitions.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

1. Selecteer een Azure-abonnement of resource groep.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy2.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

1. Wijs het beleid toe.

    :::image type="content" source="media/azure-defender-storage-configure/storage-atp-policy1.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

### <a name="rest-api"></a>[REST API](#tab/rest-api)

Gebruik rest API-opdrachten om de Azure Defender-instelling voor een specifiek opslag account te maken, bij te werken of op te halen.

- [Advanced Threat Protection-maken](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/create)
- [Advanced Threat Protection-ophalen](https://docs.microsoft.com/rest/api/securitycenter/advancedthreatprotection/get)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik de volgende Power shell-cmdlets:

- [Advanced Threat Protection inschakelen](https://docs.microsoft.com/powershell/module/az.security/enable-azsecurityadvancedthreatprotection)
- [Geavanceerde beveiliging tegen bedreigingen verkrijgen](https://docs.microsoft.com/powershell/module/az.security/get-azsecurityadvancedthreatprotection)
- [Geavanceerde bedreigings beveiliging uitschakelen](https://docs.microsoft.com/powershell/module/az.security/disable-azsecurityadvancedthreatprotection)

---

## <a name="explore-security-anomalies"></a>Beveiligings afwijkingen verkennen

Wanneer er afwijkingen optreden bij de opslag activiteit, ontvangt u een e-mail melding met informatie over de verdachte beveiligings gebeurtenis. Details van de gebeurtenis zijn onder andere:

- De aard van de anomalie
- Naam van het opslagaccount
- De tijd van de gebeurtenis
- Het opslag type
- De mogelijke oorzaken
- De onderzoek stappen
- De herstels tappen

Het e-mail bericht bevat ook informatie over mogelijke oorzaken en aanbevolen acties voor het onderzoeken en oplossen van mogelijke bedreigingen.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert-email.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

U kunt uw huidige beveiligings waarschuwingen controleren en beheren via de [tegel beveiligings waarschuwingen](../../security-center/security-center-managing-and-responding-alerts.md)van Azure Security Center. Als u op een specifieke waarschuwing klikt, vindt u details en acties voor het onderzoeken van de huidige dreiging en het adresseren van toekomstige bedreigingen.

:::image type="content" source="media/azure-defender-storage-configure/storage-advanced-threat-protection-alert.png" alt-text="Scherm afbeelding die laat zien hoe u Azure Defender inschakelt voor opslag in Security Center":::

## <a name="security-alerts"></a>Beveiligingswaarschuwingen

Waarschuwingen worden gegenereerd door ongebruikelijke en mogelijk schadelijke pogingen om opslag accounts te openen of misbruik te maken. Zie [waarschuwingen voor Azure Storage](../../security-center/alerts-reference.md#alerts-azurestorage)voor een lijst met waarschuwingen voor Azure Storage.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Logboeken in azure Storage-accounts](/rest/api/storageservices/About-Storage-Analytics-Logging)
- Meer informatie over [Azure Security Center](../../security-center/security-center-intro.md)
