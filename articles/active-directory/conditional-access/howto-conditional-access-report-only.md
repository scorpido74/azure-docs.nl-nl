---
title: Configureer een beleid voor voorwaardelijke toegang in de modus alleen rapport-Azure Active Directory
description: Modus alleen rapport gebruiken in voorwaardelijke toegang tot de hulp bij de acceptatie
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/01/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb13eb699f770d4d7f896b8179888d1514b1c46d
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690359"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Een beleid voor voorwaardelijke toegang configureren in de modus alleen rapport

Een beleid voor voorwaardelijke toegang configureren in de modus alleen rapport:

> [!IMPORTANT]
> Als uw organisatie nog niet is, [stelt u Azure monitor integratie in met Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Dit proces moet worden uitgevoerd voordat er gegevens beschikbaar zijn om te worden gecontroleerd.

1. Meld u aan bij de **Azure Portal** als beheerder voor voorwaardelijke toegang, beveiligings beheerder of globale beheerder.
1. Blader naar **Azure Active Directory** > **beveiligings** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Configureer de beleids voorwaarden en de vereiste granting-besturings elementen als dat nodig is.
1. Stel onder **beleid inschakelen** de modus voor **alleen rapport** in.
1. Selecteer **Opslaan**

> [!TIP]
> U kunt de beleids status van een bestaand beleid in **-** of uitschakelen, maar **Hiermee schakelt u** het afdwingen van **beleid uit.** 

Alleen rapport weer geven als resultaat in Logboeken van Azure AD-aanmelding.

Het resultaat van een alleen-rapport beleid voor een bepaalde aanmelding weer geven:

1. Meld u aan bij de **Azure Portal** als een rapport lezer, beveiligings lezer, beveiligings beheerder of globale beheerder.
1. Blader naar **Azure Active Directory** > **aanmeldingen**.
1. Selecteer een aanmelding of Voeg filters toe om de resultaten te beperken.
1. Selecteer in de **detail** lade het tabblad **alleen rapport** om het beleid weer te geven dat is geëvalueerd tijdens het aanmelden.

> [!NOTE]
> Wanneer u de logboeken wilt downloaden, kiest u JSON-indeling om alleen rapport gegevens van het type voorwaardelijke toegang toe te voegen.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure Monitor integratie met Azure AD instellen

Als u de cumulatieve impact van beleids regels voor voorwaardelijke toegang met behulp van de nieuwe voorwaardelijke Access Insights-werkmap wilt weer geven, moet u Azure Monitor integreren met Azure AD en de aanmeld logboeken exporteren. Er zijn twee stappen voor het instellen van deze integratie: 

1. [Meld u aan voor een Azure monitor-abonnement en maak een werk ruimte](/azure/azure-monitor/learn/quick-create-workspace).
1. [Exporteer de aanmeldings logboeken van Azure AD naar Azure monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Meer informatie over Azure Monitor prijzen vindt u op de [pagina met Azure monitor prijzen](https://azure.microsoft.com/pricing/details/monitor/). Resources om kosten te schatten, een daglimiet in te stellen of de Bewaar periode voor gegevens aan te passen, kunt u vinden in het artikel, het [gebruik en de kosten beheren met Azure monitor logboeken](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Insights-werkmap voor voorwaardelijke toegang weer geven

Wanneer u uw Azure AD-Logboeken hebt geïntegreerd met Azure Monitor, kunt u de impact van het beleid voor voorwaardelijke toegang bewaken met behulp van de nieuwe voorwaardelijke toegang-werkmappen.

1. Meld u aan bij de **Azure Portal** als beveiligings beheerder of globale beheerder.
1. Blader naar **Azure Active Directory** > -**werkmappen**.
1. Selecteer **inzicht in voorwaardelijke toegang**.
1. Selecteer een of meer beleids regels uit de vervolg keuzelijst **voorwaardelijk toegangs beleid** . Alle ingeschakelde beleids regels zijn standaard geselecteerd.
1. Selecteer een tijds bereik (als het tijds bereik de beschik bare gegevensset overschrijdt, worden alle beschik bare gegevens in het rapport weer gegeven). Wanneer u het beleid voor **voorwaardelijke toegang** en het **tijds bereik** hebt ingesteld, wordt het rapport geladen.
   1. U kunt ook zoeken naar afzonderlijke **gebruikers** of **apps** om het bereik van het rapport te beperken.
1. Selecteren tussen het weer geven van de gegevens in het tijds bereik met het aantal gebruikers of het aantal aanmeldingen.
1. Afhankelijk van de **gegevens weergave**bevat de overzicht van de **impact** het aantal gebruikers of aanmeldingen in het bereik van de gekozen para meters, gegroepeerd op totaal aantal **, geslaagd**, **mislukt**, **gebruikers actie vereist**en **niet toegepast**. Selecteer een tegel om de aanmeldingen van een bepaald resultaat type te onderzoeken. 
   1. Als u de werkmap parameters hebt gewijzigd, kunt u ervoor kiezen om een kopie op te slaan voor toekomstig gebruik. Selecteer het pictogram opslaan boven aan het rapport en geef een naam en locatie op om op te slaan.
1. Schuif omlaag om de uitsplitsing van aanmeldingen voor elke voor waarde weer te geven.
1. Bekijk de **aanmeldings gegevens** aan de onderkant van het rapport om afzonderlijke aanmeldings gebeurtenissen te onderzoeken, gefilterd op bovenstaande selecties.

> [!TIP] 
> Moet u inzoomen op een bepaalde query of de details van de aanmelding exporteren? Selecteer de knop rechts van een query om de query te openen in Log Analytics. Selecteer exporteren om naar CSV of Power BI te exporteren.

## <a name="common-problems-and-solutions"></a>Veelvoorkomende problemen en oplossingen

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Waarom worden de query's in de werkmap niet uitgevoerd?

Klanten hebben opgemerkt dat query's soms mislukken als er verkeerde of meerdere werk ruimten zijn gekoppeld aan de werkmap. Om dit probleem op te lossen, klikt u boven aan de werkmap op **bewerken** en vervolgens op het vistuig instellingen. Selecteer en verwijder werk ruimten die niet aan de werkmap zijn gekoppeld. Er mag slechts één werk ruimte aan elke werkmap zijn gekoppeld.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Waarom bevat de vervolg keuzelijst voor het beleid voor voorwaardelijke toegang mijn beleid niet?

De vervolg keuzelijst beleid voor voorwaardelijke toegang wordt ingevuld door de meest recente aanmeldingen te doorzoeken gedurende een periode van vier uur. Als een Tenant in de afgelopen vier uur geen aanmeldingen heeft, is het mogelijk dat de vervolg keuzelijst leeg is. Als deze vertraging een persistent probleem is, zoals bij kleine tenants met incidentele aanmeldingen, kunnen beheerders de query voor de vervolg keuzelijst voor het beleid voor voorwaardelijke toegang bewerken en de tijd voor de query verlengen naar een tijd die langer is dan 4 uur.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

Voor meer informatie over Azure AD-werkmappen raadpleegt u het artikel [How to use Azure monitor Workbooks for Azure Active Directory Reports](../reports-monitoring/howto-use-azure-monitor-workbooks.md)(Engelstalig).
