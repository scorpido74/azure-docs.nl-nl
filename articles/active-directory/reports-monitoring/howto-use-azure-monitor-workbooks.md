---
title: Azure Monitor-werkmappen voor rapporten | Microsoft Documenten
description: Meer informatie over het gebruik van Azure Monitor-werkmappen voor Azure Active Directory-rapporten.
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/30/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2e94d9f56a865999f9169650f621a6af892c27ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014363"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor-werkmappen gebruiken voor Azure Active Directory-rapporten

> [!IMPORTANT]
> Als u de onderliggende query's in deze werkmap wilt optimaliseren, klikt u op 'Bewerken', klikt u op het pictogram Instellingen en selecteert u de werkruimte waar u deze query's wilt uitvoeren. In werkmappen worden standaard alle werkruimten geselecteerd waar u uw Azure AD-logboeken routeert. 

Wilt u:

- Begrijpt u het effect van uw [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) op de aanmeldingservaring van uw gebruikers?

- Problemen met aanmeldingsproblemen oplossen om een beter beeld te krijgen van de aanmeldingsstatus van uw organisatie en om problemen snel op te lossen?

- Weet u wie legacy-verificaties gebruikt om u aan te melden bij uw omgeving? (Door [verouderde verificatie te blokkeren,](../conditional-access/block-legacy-authentication.md)u de beveiliging van uw tenant verbeteren.)

- Moet u de impact van beleid voor voorwaardelijke toegang in uw tenant begrijpen?

- Wilt u de mogelijkheid om te controleren: aanmeldingslogboekquery's, de werkmap rapporteert hoeveel gebruikers toegang hebben gekregen of geweigerd, en hoeveel gebruikers het beleid voor voorwaardelijke toegang hebben omzeild bij het openen van bronnen?

- Geïnteresseerd in het ontwikkelen van een dieper begrip van: de werkmap details per voorwaarde, zodat de impact van een beleid kan worden gecontextualiseerd per voorwaarde, met inbegrip van apparaat platform, apparaat staat, client app, aanmeldingsrisico, locatie en toepassing?

- Krijg meer inzicht in aanmeldingslogboekquery's, de werkmap rapporteert hoeveel gebruikers toegang hebben gekregen of geweigerd, en hoeveel gebruikers het beleid voor voorwaardelijke toegang hebben omzeild bij het openen van bronnen.

- Om u te helpen deze vragen aan te pakken, biedt Active Directory werkmappen voor bewaking. [Azure Monitor-werkmappen](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combineren tekst, analysequery's, statistieken en parameters in uitgebreide interactieve rapporten.



Dit artikel:

- Hiermee wordt ervan uitgegaan dat u bekend bent met het [maken van interactieve rapporten met behulp van Monitor-werkmappen.](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks)

- Hier wordt uitgelegd hoe u Werkmappen controleren gebruikt om inzicht te krijgen in het effect van uw beleid voor voorwaardelijke toegang, om aanmeldingsfouten op te lossen en om verouderde verificaties te identificeren.
 


## <a name="prerequisites"></a>Vereisten

Als u Werkmappen controleren wilt gebruiken, moet u het sein

- Een Active Directory-tenant met een premiumlicentie (P1 of P2). Meer informatie over het [krijgen van een premium licentie.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium)

- Een [werkruimte log analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Toegang tot](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) de werkruimte voor logboekanalyse
- Rollen volgen in Azure Active Directory (als u Log Analytics opent via Azure Active Directory-portal)
    - Beveiligingsbeheerder
    - Beveiligingslezer
    - Rapportlezer
    - Globale beheerder

## <a name="roles"></a>Rollen
U moet zich in een van de volgende rollen bevinden en toegang hebben [tot de onderliggende Werkruimte Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) om de werkmappen te beheren:
-   Globale beheerder
-   Beveiligingsbeheerder
-   Beveiligingslezer
-   Rapportlezer
-   Toepassingsbeheerder

## <a name="workbook-access"></a>Toegang tot werkmap 

Ga als u naar werkmappen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Navigeer naar **Azure Active Directory** > **Monitoring** > **Workbooks**. 

1. Selecteer een rapport of sjabloon of selecteer Op de werkbalk **Openen**. 

![De Azure Monitor-werkmappen zoeken in Azure AD](./media/howto-use-azure-monitor-workbooks/azure-monitor-workbooks-in-azure-ad.png)

## <a name="sign-in-analysis"></a>Aanmeldingsanalyse

Als u de aanmeldingsanalysewerkmap wilt openen, selecteert u In de sectie **Gebruik** de optie **Aanmelding .** 

Deze werkmap toont de volgende aanmeldingstrends:

- Alle aanmeldingen

- Geslaagd

- Actie in behandeling in behandeling

- Fout

U elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingsanalyse](./media/howto-use-azure-monitor-workbooks/43.png)


Voor elke trend krijgt u een uitsplitsing naar de volgende categorieën:

- Locatie

    ![Aanmeldingen per locatie](./media/howto-use-azure-monitor-workbooks/45.png)

- Apparaat

    ![Aanmeldingen per apparaat](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Aanmeldingen met verouderde verificatie 


Als u de werkmap wilt openen voor aanmeldingen die [oudere verificatie](../conditional-access/block-legacy-authentication.md)gebruiken, selecteert u In de sectie **Gebruik** de optie **Aanmelding met verouderde verificatie**. 

Deze werkmap toont de volgende aanmeldingstrends:

- Alle aanmeldingen

- Geslaagd


U elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

- Protocollen

![Aanmeldingen via verouderde verificatie](./media/howto-use-azure-monitor-workbooks/47.png)


Voor elke trend krijg je een uitsplitsing per app en protocol.

![Aanmeldingen voor verouderde verificatie per app en protocol](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Aanmeldingen op voorwaarde 


Als u de werkmap voor aanmeldingen wilt openen via [beleid voor voorwaardelijke toegang,](../conditional-access/overview.md)selecteert u in de sectie **Voorwaardelijke toegang** **aanmeldingen op voorwaardelijke toegang**. 

Deze werkmap toont de trends voor aanmeldingen met een handicap. U elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen via voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/49.png)


Voor aanmeldingen voor uitgeschakelde aanvragen krijgt u een uitsplitsing naar de status Voorwaardelijke toegang.

![Status voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)


## <a name="conditional-access-insights"></a>Voorwaardelijke toegang in Insights

### <a name="overview"></a>Overzicht

Werkmappen bevatten aanmeldingslogboekquery's waarmee IT-beheerders de impact van beleid voor voorwaardelijke toegang in hun tenant kunnen controleren. U rapporteren hoeveel gebruikers zouden zijn toegestaan of de toegang zou hebben geweigerd. De werkmap bevat inzichten over hoeveel gebruikers het beleid voor voorwaardelijke toegang zouden hebben omzeild op basis van de kenmerken van die gebruikers op het moment van aanmelding. Het bevat details per voorwaarde, zodat de impact van een beleid per voorwaarde kan worden gecontextualiseerd, inclusief apparaatplatform, apparaatstatus, client-app, aanmeldingsrisico, locatie en toepassing.

### <a name="instructions"></a>Instructies 
Als u de werkmap voor voorwaardelijke toegangsinzichten wilt openen, selecteert u de werkmap **Voorwaardelijke toegangtot statistieken** in de sectie Voorwaardelijke toegang. Deze werkmap toont de verwachte impact van elk beleid voor voorwaardelijke toegang in uw tenant. Selecteer een of meer beleid voor voorwaardelijke toegang in de vervolgkeuzelijst en verklein het bereik van de werkmap door de volgende filters toe te passen: 

- **Tijdbereik**

- **Gebruiker**

- **Apps**

- **Gegevensweergave**

![Status voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/access-insights.png)


In het overzicht van de impact wordt het aantal gebruikers of aanmeldingen weergegeven waarvoor het geselecteerde beleid een bepaald resultaat heeft opgeleverd. Totaal is het aantal gebruikers of aanmeldingen waarvoor het geselecteerde beleid is geëvalueerd in het geselecteerde tijdbereik. Klik op een tegel om de gegevens in de werkmap te filteren op dat resultaattype. 

![Status voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/impact-summary.png)

Deze werkmap toont ook de impact van het geselecteerde beleid, onderverdeeld naar elk van de zes voorwaarden: 
- **Apparaatstatus**
- **Apparaatplatform**
- **Client-apps**
- **Aanmeldingsrisico**
- **Locatie**
- **Toepassingen**

![Status voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/device-platform.png)

U ook afzonderlijke aanmeldingen onderzoeken, gefilterd op de parameters die in de werkmap zijn geselecteerd. Zoek naar individuele gebruikers, gesorteerd op aanmeldingsfrequentie, en bekijk de bijbehorende aanmeldingsgebeurtenissen. 

![Status voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/filtered.png)





## <a name="sign-ins-by-grant-controls"></a>Aanmeldingen door subsidiebesturingselementen

Als u de werkmap voor aanmeldingen wilt openen door [subsidiebesturingselementen](../conditional-access/controls.md)toe te kennen, selecteert u In de sectie **Voorwaardelijke toegang** **aanmeldingen door Subsidiebesturingselementen**. 

Deze werkmap toont de volgende aanmeldingstrends voor uitgeschakelden:

- MFA vereisen
 
- Gebruiksvoorwaarden vereisen

- Privacyverklaring vereisen

- Overige


U elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen door subsidiebesturingselementen](./media/howto-use-azure-monitor-workbooks/50.png)


Voor elke trend krijg je een uitsplitsing per app en protocol.

![Uitsplitsing van recente aanmeldingen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse van mislukte aanmeldingen

Gebruik de **werkmap Voor het analyseren van aanmeldingen** voor aanmeldingen om fouten op te lossen met:

- Aanmeldingen
- Beleid voor voorwaardelijke toegang
- Verouderde verificatie 


Als u de aanmeldingen wilt openen via voorwaardelijke toegangsgegevens, selecteert u in de sectie **Problemen oplossen** de optie **Aanmelding met verouderde verificatie**. 

Deze werkmap toont de volgende aanmeldingstrends:

- Alle aanmeldingen

- Geslaagd

- In behandeling zijnde actie

- Fout


U elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen oplossen](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor geeft u een overzicht van de volgende categorieën om u te helpen bij het oplossen van aanmeldingen:

- Topfouten

    ![Overzicht van topfouten](./media/howto-use-azure-monitor-workbooks/53.png)

- Aanmeldingen die wachten op gebruikersactie

    ![Overzicht van aanmeldingen die wachten op gebruikersactie](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Volgende stappen

[Maak interactieve rapporten met behulp van Werkmappen controleren](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
