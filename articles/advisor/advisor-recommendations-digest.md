---
title: Aanbevelingssamenvatting voor Azure Advisor
description: Ontvang periodiek overzicht voor uw actieve aanbevelingen
ms.topic: article
ms.date: 03/16/2020
ms.author: sagupt
ms.openlocfilehash: 98f1bfd42a486e005cd1e777a83f5b615a7c8304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502462"
---
# <a name="configure-periodic-summary-for-recommendations"></a>Periodiek overzicht configureren voor aanbevelingen

**Aanbevelingsoverzichten** van adviseurs bieden een eenvoudige en proactieve manier om op de hoogte te blijven van uw actieve aanbevelingen, in verschillende categorieën. De functie biedt de mogelijkheid om periodieke meldingen te configureren voor het overzicht van al uw actieve aanbevelingen, in verschillende categorieën. Je je gewenste kanaal kiezen voor meldingen zoals e-mail, sms of anderen, met behulp van actiegroepen. In dit artikel ziet u hoe u een **aanbevelingsoverzicht kunt** instellen voor uw aanbevelingen van een adviseur.


## <a name="setting-up-your-recommendation-digest"></a>Uw aanbevelingssamenvatting instellen 

Met de ervaring voor het maken van **aanbevelingssamenvatting** u het overzicht configureren. U onderstaande parameters voor configuraties selecteren:
1. Categorie: We hebben aanbevelingscategorieën zoals kosten, hoge beschikbaarheid, prestaties en operationele uitmuntendheid. De mogelijkheid is nog niet beschikbaar voor beveiligingsaanbevelingen.
2. Frequentie van de samenvatting: Frequentie voor de samenvatting meldingen kunnen wekelijks, tweewekelijks en maandelijks.
3. Actiegroep: U een bestaande actiegroep selecteren of een nieuwe actiegroep maken. Zie Actiegroepen maken en beheren voor meer informatie over [actiegroepen.](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
4. Taal voor de samenvatting
5. Aanbevelingssamenvattingnaam: U een gebruiksvriendelijke tekenreeks gebruiken om de samenvattingen beter bij te houden en te controleren.

## <a name="steps-to-create-recommendation-digest-in-azure-portal"></a>Stappen om aanbevelingssamenvatting te maken in Azure-portal

Hier volgen de stappen om **aanbevelingssamenvatting** te maken:
* **Stap 1:** Ga in de Azure-portal naar **Advisor** en selecteer onder **sectie Controle** de optie **Aanbevelingsoverzicht** 

   ![Toegangspunt voor het verteren van aanbeveling](./media/digest-0.png)

* **Stap 2:** Selecteer **Nieuwe aanbevelingssamenvatting** op de bovenste balk zoals hieronder:

   ![Aanbevelingssamenvatting maken](./media/digest-5.png)

* **Stap 3:** Selecteer in de **scopesectie** het **abonnement** voor uw samenvatting

   ![Aanbevelingsinvoer voor het verteren geven](./media/digest-1.png)

* **Stap 4:** Selecteer **in** de sectie voorwaarde de configuraties zoals **categorie,** **frequentie** en **taal**

   ![Aanbevelingsoverzichtsinvoeromstandigheden bieden](./media/digest-2.png)

* **Stap 5:** Selecteer in de sectie **actiegroep** de **actiegroep** voor de samenvatting. U hier meer informatie vinden - [Actiegroepen maken en beheren](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)

   ![Aanbevelingsgroep voor het verteren van aanbevelingen bieden](./media/digest-3.png)

* **Stap 6:** In dit laatste gedeelte voor **digest-details**u naam en status toewijzen aan uw aanbevelingssamenvatting. Druk **op het maken van aanbevelingssamenvatting** om de set-up te voltooien.
   ![Volledige aanbeveling digest creatie](./media/digest-4.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over aanbevelingen van Advisor:
* [Inleiding tot Azure Advisor](advisor-overview.md)
* [Aan de slag met Advisor](advisor-get-started.md)
* [Aanbevelingen voor kosten van adviseur](advisor-cost-recommendations.md)
* [Aanbevelingen voor prestaties van adviseur](advisor-performance-recommendations.md)
* [Aanbevelingen voor beveiliging van adviseur](advisor-security-recommendations.md)
* [Aanbevelingen adviseur Operational Excellence](advisor-operational-excellence-recommendations.md)
* [Advisor REST API](https://docs.microsoft.com/rest/api/advisor/)
