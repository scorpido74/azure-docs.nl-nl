---
title: Beveiligings incidenten beheren in Azure Security Center | Microsoft Docs
description: Dit document helpt u bij het gebruik van Azure Security Center voor het beheren van beveiligings incidenten.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: memildin
ms.openlocfilehash: 1a6dbaeac5355d50edb93a7f215d7f8e88231e98
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615974"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Beveiligings incidenten beheren in Azure Security Center

Het sorteren en onderzoeken van beveiligings waarschuwingen kan enige tijd in beslag nemen voor zelfs de meest ervaren beveiligings analisten, en in veel gevallen is het lastig om zelfs te weten waar u moet beginnen. Met behulp van [analytics](security-center-detection-capabilities.md) om de informatie tussen afzonderlijke [beveiligingswaarschuwingen](security-center-managing-and-responding-alerts.md) te verbinden kan het Beveiligingscentrum u één overzicht geven van de aanval en alle gerelateerde waarschuwingen - zo begrijpt u snel wat de aanvaller deed en welke resources betrokken zijn.

In dit onderwerp vindt u informatie over incidenten in Security Center en hoe u de waarschuwingen herstelt.

## <a name="what-is-a-security-incident"></a>Wat is een beveiligingsincident?

In het Beveiligingscentrum is een beveiligingsincident een samenloop van alle waarschuwingen voor een resource die overeenstemt met [kill chain](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/)-patronen. Incidenten worden weer gegeven in de lijst met [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) . Klik op een incident om de gerelateerde waarschuwingen weer te geven, waarmee u meer informatie over elk exemplaar kunt verkrijgen.

## <a name="managing-security-incidents"></a>Beveiligingsincidenten beheren

1. Klik op het Security Center dash board op de tegel **beveiligings waarschuwingen** . De incidenten en waarschuwingen worden weer gegeven. Merk op dat de beschrijving van een beveiligingsincident een ander pictogram heeft vergeleken met andere waarschuwingen.

    ![Beveiligings incidenten weer geven](./media/security-center-managing-and-responding-alerts/security-center-manage-alerts.png)

1. Als u details wilt weer geven, klikt u op een incident. Op de Blade **beveiligings incident gedetecteerd** wordt meer informatie weer gegeven. De sectie **algemene informatie** kan inzicht bieden in wat de beveiligings waarschuwing heeft geactiveerd. Hierin worden gegevens weer gegeven, zoals de doel resource, het bron-IP-adres (indien van toepassing), als de waarschuwing nog steeds actief is en aanbevelingen voor het oplossen van problemen.  

    ![Reageren op beveiligings incidenten in Azure Security Center](./media/security-center-managing-and-responding-alerts/security-center-alert-incident.png)

1. Klik op een waarschuwing om meer informatie over elke waarschuwing te krijgen. Het herstel dat door Security Center wordt voorgesteld, verschilt per beveiligingswaarschuwing.

   > [!NOTE]
   > Dezelfde waarschuwing kan bestaan als onderdeel van een incident, en als een zelfstandige waarschuwing worden weer gegeven.

    ![Meldingsdetails](./media/security-center-incident/security-center-incident-alert.png)

1. Volg de stappen voor herstel die zijn opgegeven voor elke waarschuwing.

Voor meer informatie over waarschuwingen, [het beheren van en reageren op beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md).

De volgende onderwerpen helpen u bij het door lopen van de verschillende waarschuwingen, volgens de resource typen:

* [Waarschuwingen voor IaaS Windows-computers](threat-protection.md#windows-machines)
* [Waarschuwingen voor IaaS Linux-machines](threat-protection.md#linux-machines)
* [Waarschuwingen voor Azure App Service](threat-protection.md#app-services)
* [Waarschuwingen voor Azure-containers](threat-protection.md#azure-containers)
* [Waarschuwingen voor SQL Database en SQL Data Warehouse](threat-protection.md#data-sql)
* [Waarschuwingen voor Azure Storage](threat-protection.md#azure-storage)
* [Waarschuwingen voor Cosmos DB](threat-protection.md#cosmos-db)

In de volgende onderwerpen wordt uitgelegd hoe Security Center de verschillende telemetrie gebruikt die wordt verzameld van integratie met de Azure-infra structuur, om extra beveiligings lagen toe te passen voor resources die in azure zijn geïmplementeerd:

* [Waarschuwingen voor Azure Management Layer (Azure Resource Manager) (preview)](threat-protection.md#management-layer)
* [Waarschuwingen voor Azure Key Vault (preview-versie)](threat-protection.md#azure-keyvault)
* [Waarschuwingen voor Azure Network Layer](threat-protection.md#network-layer)
* [Waarschuwingen van andere services](threat-protection.md#alerts-other)

## <a name="see-also"></a>Zie ook
In dit document hebt u kunnen lezen hoe u de mogelijkheid beveiligingsincidenten in Azure Security Center gebruikt. Zie de volgende onderwerpen voor meer informatie over het Beveiligingscentrum:

* [Beveiligings waarschuwingen in azure Security Center](security-center-alerts-overview.md).
* [Beveiligingswaarschuwingen beheren](security-center-managing-and-responding-alerts.md)
* [Plannings- en bedieningsgids voor Azure Security Center](security-center-planning-and-operations-guide.md)
