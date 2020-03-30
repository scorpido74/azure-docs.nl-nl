---
title: Een beleid voor voorwaardelijke toegang configureren in de modus alleen voor rapport - Azure Active Directory
description: Gebruik van rapport-only modus in voorwaardelijke toegang tot hulp bij adoptie
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d9bec829d7fb3e76eb243bda73423303670585e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295115"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode-preview"></a>Een beleid voor voorwaardelijke toegang configureren in de modus Alleen rapport (Voorbeeld)

Ga als lid van het rapport voor het configureren van een beleid voor voorwaardelijke toegang:

> [!IMPORTANT]
> Als uw organisatie dat nog niet heeft gedaan, [stelt u Azure Monitor-integratie in met Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Dit proces moet plaatsvinden voordat gegevens beschikbaar zijn om te controleren.

1. Meld u aan bij de **Azure-portal** als beheerder van voorwaardelijke toegang, beveiligingsbeheerder of globale beheerder.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Configureer de beleidsvoorwaarden en vereiste subsidiebesturingselementen indien nodig.
1. Stel **onder Beleid inschakelen** de schakelin in op de modus **Alleen-rapporteren.**
1. Selecteer **Opslaan**

> [!TIP]
> U de **beleidsstatus van** een bestaand beleid inschakelen van **Op** naar **Alleen-rapport,** maar hierdoor wordt de handhaving van het beleid uitgeschakeld. 

Rapportresultaat weergeven in Azure AD-aanmeldingslogboeken.

Ga als resultaat van een beleid voor alleen-het-rapport voor een bepaalde aanmelding:

1. Meld u aan bij de **Azure-portal** als rapportenlezer, beveiligingslezer, beveiligingsbeheerder of globale beheerder.
1. Blader naar Azure Active > **Directory-aanmeldingen**. **Azure Active Directory**
1. Selecteer een aanmelding of voeg filters toe aan smalle resultaten.
1. Selecteer in het **lade Details** het tabblad **Alleen rapport (Voorbeeld)** om het beleid weer te geven dat tijdens het aanmelden is geëvalueerd.

> [!NOTE]
> Wanneer u de logboeken voor aanmeldingslogboeken downloadt, kiest u de JSON-indeling om alleen resultatengegevens voor voorwaardelijke toegang op te nemen.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Azure Monitor-integratie instellen met Azure AD

Als u de totale impact van beleid voor voorwaardelijke toegang wilt weergeven met de nieuwe werkmap Voorwaardelijke toegang tot insights, moet u Azure Monitor integreren met Azure AD en de aanmeldingslogboeken exporteren. Er zijn twee stappen om deze integratie in te stellen: 

1. [Meld u aan voor een Azure Monitor-abonnement en maak een werkruimte.](/azure/azure-monitor/learn/quick-create-workspace)
1. [Exporteer de aanmeldingslogboeken van Azure AD naar Azure Monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Meer informatie over azure monitor-prijzen vindt u op de [prijspagina van Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/). Bronnen om kosten te schatten, een dagelijkse limiet in te stellen of de bewaarperiode voor gegevens aan te passen, vindt u in het artikel [Beheer gebruik en kosten met Azure Monitor Logs.](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment)

## <a name="view-conditional-access-insights-workbook"></a>Werkmap Voorwaardelijke toegang insights weergeven

Zodra u uw Azure AD-logboeken hebt geïntegreerd met Azure Monitor, u de impact van beleid voor voorwaardelijke toegang controleren met behulp van de nieuwe werkmappen met voorwaardelijke toegang.

1. Meld u aan bij de **Azure-portal** als beveiligingsbeheerder of globale beheerder.
1. Blader naar Azure Active > **Directory-werkmappen**. **Azure Active Directory**
1. Selecteer **Voorwaardelijke toegangsinzichten (voorbeeld)**.
1. Selecteer een of meer beleidsregels in de vervolgkeuzelijst **Voor voorwaardelijke toegang.** Alle ingeschakelde beleidsregels zijn standaard geselecteerd.
1. Selecteer een tijdsbereik (als het tijdsbereik de beschikbare gegevensset overschrijdt, worden in het rapport alle beschikbare gegevens weergegeven). Zodra u de parameters **Voor voorwaardelijke toegang** en het **tijdbereik** hebt ingesteld, wordt het rapport geladen.
   1. Zoek eventueel naar afzonderlijke **gebruikers** of **apps** om het bereik van het rapport te verkleinen.
1. Kies tussen het weergeven van de gegevens in het tijdsbereik op basis van het aantal gebruikers of het aantal aanmeldingen.
1. Afhankelijk van de **gegevensweergave**wordt in het **overzicht van** de impact het aantal gebruikers of aanmeldingen weergegeven in het bereik van de gekozen parameters, gegroepeerd op Totaal aantal, **Succes**, **Falen**, **Vereiste gebruikersactie**en **Niet toegepast**. Selecteer een tegel om aanmeldingen van een bepaald resultaattype te onderzoeken. 
   1. Als u de werkmapparameters hebt gewijzigd, u ervoor kiezen om een kopie op te slaan voor toekomstig gebruik. Selecteer het pictogram Opslaan boven aan het rapport en geef een naam en locatie op om op te slaan.
1. Scroll naar beneden om de uitsplitsing van aanmeldingen voor elke voorwaarde weer te geven.
1. Bekijk de **aanmeldingsgegevens** onder aan het rapport om afzonderlijke aanmeldingsgebeurtenissen te onderzoeken die zijn gefilterd op selecties hierboven.

> [!TIP] 
> Wilt u inzoomen op een bepaalde query of de aanmeldingsgegevens exporteren? Selecteer de knop rechts van een query om de query te openen in Log Analytics. Selecteer Exporteren om te exporteren naar CSV of Power BI.

## <a name="common-problems-and-solutions"></a>Veelvoorkomende problemen en oplossingen

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Waarom mislukken de query's in de werkmap?

Klanten hebben gemerkt dat query's soms mislukken als de verkeerde of meerdere werkruimten zijn gekoppeld aan de werkmap. Als u dit probleem wilt oplossen, klikt u boven aan de werkmap **bewerken** en vervolgens op de versnelling Instellingen. Werkmap en verwijder werkruimten die niet aan de werkmap zijn gekoppeld. Er mag slechts één werkruimte aan elke werkmap zijn gekoppeld.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Waarom bevat de parameter Conditional Access Policies niet mijn beleid?

De vervolgkeuzelijst Voor voorwaardelijke toegangsbeleid wordt ingevuld door de meest recente aanmeldingen op te vragen over een periode van 4 uur. Als een huurder in de afgelopen 4 uur geen aanmeldingen heeft, is het mogelijk dat de vervolgkeuzelijst leeg is. Als deze vertraging een hardnekkig probleem is, zoals bij kleine tenants met zeldzame aanmeldingen, kunnen beheerders de query voor de vervolgkeuzelijst Voor voorwaardelijke toegang beleid bewerken en de tijd voor de query verlengen tot een tijd langer dan 4 uur.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

Zie het [artikel: Azure Monitor-werkmappen gebruiken voor Azure Active Directory-rapporten voor](../reports-monitoring/howto-use-azure-monitor-workbooks.md)meer informatie over Azure AD-werkmappen.
