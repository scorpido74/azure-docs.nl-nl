---
title: Aanbevelingen van Azure Advisor weergeven die voor u van belang zijn
description: Aanbevelingen voor Azure Advisor weergeven en filteren om ruis te verminderen.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422373"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>Aanbevelingen van Azure Advisor weergeven die voor u van belang zijn

Azure Advisor biedt aanbevelingen om u te helpen bij het optimaliseren van uw Azure-implementaties. Binnen Advisor heeft u toegang tot een aantal functies die u helpen om uw aanbevelingen te beperken tot alleen die welke voor u van belang zijn.

## <a name="configure-subscriptions-and-resource-groups"></a>Abonnementen en resourcegroepen configureren

Advisor geeft u de mogelijkheid om abonnementen en resourcegroepen te selecteren die belangrijk zijn voor u en uw organisatie. U ziet alleen aanbevelingen voor de abonnementen en resourcegroepen die u selecteert. Standaard zijn ze allemaal geselecteerd. Configuratie-instellingen zijn van toepassing op het abonnement of de resourcegroep, dus dezelfde instellingen zijn van toepassing op iedereen die toegang heeft tot dat abonnement of die brongroep. Configuratie-instellingen kunnen worden gewijzigd in de Azure-portal of programmatisch.

Ga als bedoeld als het gaat om wijzigingen aan te brengen in de Azure-portal:

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in de Azure-portal.

1. Selecteer **Configuratie** in het menu.

   ![Configuratiemenu van adviseur](./media/view-recommendations/configuration.png)

1. Schakel het selectievakje in de kolom **Opnemen** in voor abonnementen of resourcegroepen om aanbevelingen van Advisor te ontvangen. Als het vak is uitgeschakeld, hebt u mogelijk geen toestemming om een configuratiewijziging aan te brengen in dat abonnement of die brongroep. Meer informatie over [machtigingen in Azure Advisor](permissions.md).

1. Klik op **Onderaan toepassen** nadat u een wijziging hebt aangebracht.

## <a name="filtering-your-view-in-the-azure-portal"></a>Uw weergave filteren in de Azure-portal

Configuratie-instellingen blijven actief totdat ze worden gewijzigd. Als u de weergave van aanbevelingen voor één weergave wilt beperken, u de vervolgkeuzelijst boven aan het deelvenster Adviseur gebruiken. In de deelvensters Overzicht, Hoge beschikbaarheid, Beveiliging, Prestaties, Kosten en Alle aanbevelingsopties u de abonnementen, brontypen en aanbevelingsstatus selecteren die u wilt zien.

   ![Filtermenu van adviseur](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>Aanbevelingen afwijzen en uitstellen

Met Azure Advisor u aanbevelingen voor één resource verwijderen of uitstellen. Als u een aanbeveling afwijst, ziet u deze niet meer, tenzij u deze handmatig activeert. Als u een aanbeveling uitstelt, u echter een duur opgeven waarna de aanbeveling automatisch opnieuw wordt geactiveerd. Uitstel kan worden gedaan in de Azure-portal of programmatisch.

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>Eén aanbeveling in de Azure-portal uitstellen 

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in de Azure-portal.
1. Selecteer een aanbevelingscategorie om uw aanbevelingen weer te geven
1. Selecteer een aanbeveling in de lijst met aanbevelingen
1. Selecteer Uitstellen of afwijzen voor de aanbeveling die u wilt uitstellen of verwijderen

     ![Filtermenu van adviseur](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>Meerdere aanbevelingen in de Azure-portal uitstellen of verwijderen

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in de Azure-portal.
1. Selecteer een aanbevelingscategorie om uw aanbevelingen te bekijken.
1. Selecteer een aanbeveling in de lijst met aanbevelingen.
1. Schakel het selectievakje links van de rij in voor alle resources die u wilt uitstellen of de aanbeveling wilt verwijderen.
1. Selecteer **Uitzetten** of **Uitstellen** linksboven in de tabel.

     ![Filtermenu van adviseur](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> U hebt toestemming van inzender of eigenaar nodig om een aanbeveling te verwijderen of uit te stellen. Meer informatie over machtigingen in Azure Advisor.

> [!NOTE]
> Als de selectievakken zijn uitgeschakeld, kunnen aanbevelingen nog steeds worden geladen. Wacht tot alle aanbevelingen zijn geladen voordat u probeert uit te stellen of te verwijderen.

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>Een uitgestelde of afgewezen aanbeveling opnieuw activeren

U een aanbeveling activeren die is uitgesteld of afgewezen. Deze actie kan worden uitgevoerd in de Azure-portal of programmatisch. In Azure Portal:

1. Open [Azure Advisor](https://aka.ms/azureadvisordashboard) in de Azure-portal.

1. Wijzig het filter in het deelvenster Overzicht in **Uitgesteld**. De adviseur geeft vervolgens uitgestelde of afgewezen aanbevelingen weer.

    ![Filtermenu van adviseur](./media/view-recommendations/activate-postponed.png)

1. Selecteer een categorie die **u wilt zien Uitgestelde** en **Afgewezen** aanbevelingen.

1. Selecteer een aanbeveling in de lijst met aanbevelingen. Hiermee worden aanbevelingen geopend met het tabblad **Uitgesteld & afgewezen** dat al is geselecteerd om de resources weer te geven waarvoor deze aanbeveling is uitgesteld of verworpen.

1. Klik op **Activeren** aan het einde van de rij. Eenmaal geklikt, is de aanbeveling actief voor die bron en dus verwijderd uit deze tabel. De aanbeveling is nu zichtbaar op het tabblad **Actief.**
 
     ![Filtermenu van adviseur](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u aanbevelingen weergeven die voor u van belang zijn in Azure Advisor. Zie voor meer informatie over Advisor: 

- [Wat is Azure Advisor?](advisor-overview.md)
- [Aan de slag met Adviseur](advisor-get-started.md)
- [Machtigingen in Azure Advisor](permissions.md)



