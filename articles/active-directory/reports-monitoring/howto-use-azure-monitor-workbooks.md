---
title: Azure Monitor werkmappen gebruiken voor Azure Active Directory rapporten | Microsoft Docs
description: Meer informatie over het gebruik van Azure Monitor werkmappen voor Azure Active Directory rapporten.
services: active-directory
author: cawrites
manager: daveba
ms.assetid: 4066725c-c430-42b8-a75b-fe2360699b82
ms.service: active-directory
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.openlocfilehash: 9bea8da4f0d694be3a39a8f5dfaca8e54ce2773d
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255658"
---
# <a name="how-to-use-azure-monitor-workbooks-for-azure-active-directory-reports"></a>Azure Monitor werkmappen gebruiken voor Azure Active Directory rapporten

Wilt u:

- Meer informatie over het effect van uw [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) voor de aanmeldings ervaring van uw gebruikers?

- Problemen met aanmelden oplossen om een beter beeld te krijgen van de status van aanmelding van uw organisatie en om problemen snel op te lossen?

- Weet wie een verouderde verificatie gebruikt om u aan te melden bij uw omgeving? (Door [verouderde verificatie te blok keren](../conditional-access/block-legacy-authentication.md), kunt u de beveiliging van uw Tenant verbeteren.)

Active Directory biedt werkmappen voor het bewaken van deze vragen. [Azure monitor-werkmappen](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks) combi neer tekst, analyse query's, metrische gegevens en para meters in uitgebreide interactieve rapporten. 

Dit artikel:

- Er wordt van uitgegaan dat u bekend bent met het [maken van interactieve rapporten met behulp van werk bladen controleren](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).

- In dit artikel wordt uitgelegd hoe u Bewaak werkmappen gebruikt om inzicht te krijgen in het effect van uw beleid voor voorwaardelijke toegang, problemen met aanmelding op te lossen en verouderde authenticaties te identificeren.
 


## <a name="prerequisites"></a>Vereisten

Als u werkmappen wilt gebruiken, hebt u het volgende nodig:

- Een Active Directory-Tenant met een Premium-licentie (P1 of P2). Meer informatie over het [verkrijgen van een Premium-licentie](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-get-started-premium).

- Een [log Analytics-werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).

- [Toegang](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) tot de log Analytics-werk ruimte
- De volgende rollen in Azure Active Directory (als u Log Analytics via Azure Active Directory Portal opent)
    - Beveiligingsbeheerder
    - Beveiligings lezer
    - Rapport lezer
    - Globale beheerder

## <a name="roles"></a>Rollen
U moet een van de volgende rollen hebben en toegang hebben tot de [onderliggende log Analytics](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/manage-access#manage-access-using-azure-permissions) werk ruimte om de werkmappen te beheren:
-   Globale beheerder
-   Beveiligingsbeheerder
-   Beveiligings lezer
-   Rapport lezer
-   Toepassings beheerder


## <a name="workbook-access"></a>Werkmap toegang 

Voor toegang tot werkmappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

2. Selecteer **Azure Active Directory**in het navigatie deel venster aan de linkerkant.

3. Selecteer in de sectie **controle** de optie **werkmappen**. 

    ![Inzichten selecteren](./media/howto-use-azure-monitor-workbooks/41.png)

4. Selecteer een rapport of sjabloon of selecteer **openen**op de werk balk. 

    ![Selecteer openen](./media/howto-use-azure-monitor-workbooks/42.png)


## <a name="sign-in-analysis"></a>Aanmeldings analyse

Als u toegang wilt krijgen tot de werkmap voor het aanmelden, selecteert u **aanmeldingen**in de sectie **gebruik** . 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd

- Gebruikers actie in behandeling

- Fout

U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldings analyse](./media/howto-use-azure-monitor-workbooks/43.png)


Voor elke trend krijgt u een uitsplitsing van de volgende categorieën:

- Locatie

    ![Aanmeldingen per locatie](./media/howto-use-azure-monitor-workbooks/45.png)

- Apparaat

    ![Aanmeldingen per apparaat](./media/howto-use-azure-monitor-workbooks/46.png)


## <a name="sign-ins-using-legacy-authentication"></a>Aanmeldingen met behulp van verouderde verificatie 


Voor toegang tot de werkmap voor aanmeldingen die gebruikmaken van [verouderde verificatie](../conditional-access/block-legacy-authentication.md), selecteert u in de sectie **gebruik** **aanmeldingen met behulp van verouderde verificatie**. 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

- Protocollen

![Aanmeldingen op verouderde verificatie](./media/howto-use-azure-monitor-workbooks/47.png)


Voor elke trend krijgt u een uitsplitsing per app en protocol.

![Aanmeldingen voor verouderde authenticatie per app en protocol](./media/howto-use-azure-monitor-workbooks/48.png)



## <a name="sign-ins-by-conditional-access"></a>Aanmeldingen op voorwaardelijke toegang 


Als u toegang wilt krijgen tot de werkmap voor aanmeldingen via [beleids regels voor voorwaardelijke toegang](../conditional-access/overview.md), selecteert u in de sectie **voorwaardelijke toegang** de optie **aanmeldingen via voorwaardelijke toegang**. 

In deze werkmap ziet u de trends voor uitgeschakelde aanmeldingen. U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen via voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/49.png)


Bij uitgeschakelde aanmeldingen krijgt u een uitsplitsing van de status van de voorwaardelijke toegang.

![Status van voorwaardelijke toegang](./media/howto-use-azure-monitor-workbooks/conditional-access-status.png)








## <a name="sign-ins-by-grant-controls"></a>Aanmeldingen door besturings elementen te verlenen

Als u de werkmap voor aanmeldingen wilt openen door [besturings elementen te verlenen](../conditional-access/controls.md), selecteert u in de sectie **voorwaardelijke toegang** de optie **aanmeldingen door besturings elementen verlenen**. 

In deze werkmap worden de volgende uitgeschakelde aanmeldings trends weer gegeven:

- MFA vereisen
 
- Gebruiksvoorwaarden vereisen

- Privacyverklaring vereist

- Overige


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Aanmeldingen door besturings elementen te verlenen](./media/howto-use-azure-monitor-workbooks/50.png)


Voor elke trend krijgt u een uitsplitsing per app en protocol.

![Uitsplitsing van recente aanmeldingen](./media/howto-use-azure-monitor-workbooks/51.png)




## <a name="sign-ins-failure-analysis"></a>Analyse van mislukte aanmeldingen

Gebruik de werk werkmap **Fout analyse voor aanmeldingen** om fouten op te lossen met het volgende:

- Aanmeldingen
- Beleid voor voorwaardelijke toegang
- Verouderde verificatie 


Als u toegang wilt krijgen tot de aanmeldingen via gegevens voor voorwaardelijke toegang, selecteert u in de sectie **probleem oplossing** **aanmeldingen met behulp van verouderde verificatie**. 

In deze werkmap worden de volgende trends voor aanmelden weer gegeven:

- Alle aanmeldingen

- Geslaagd

- Actie in behandeling

- Fout


U kunt elke trend filteren op de volgende categorieën:

- Tijdsbereik

- Apps

- Gebruikers

![Problemen met aanmeldingen oplossen](./media/howto-use-azure-monitor-workbooks/52.png)


Azure Monitor biedt u een uitsplitsing van de volgende categorieën om u te helpen bij het oplossen van aanmeldingen:

- Belangrijkste fouten

    ![Samen vatting van de belangrijkste fouten](./media/howto-use-azure-monitor-workbooks/53.png)

- Aanmeldingen die wachten op gebruikers actie

    ![Samen vatting van de aanmeldingen die wachten op gebruikers actie](./media/howto-use-azure-monitor-workbooks/54.png)






## <a name="next-steps"></a>Volgende stappen

[Interactieve rapporten maken met behulp van werk bladen bewaken](https://docs.microsoft.com/azure/azure-monitor/app/usage-workbooks).
