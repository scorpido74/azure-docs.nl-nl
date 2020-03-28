---
title: Waarschuwingen verzenden vanuit Azure Application Insights | Microsoft Docs
description: Zelfstudie over het verzenden van waarschuwingen als reactie op fouten in uw toepassing met behulp van Azure Application Insights.
ms.subservice: application-insights
ms.topic: tutorial
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/10/2019
ms.custom: mvc
ms.openlocfilehash: 7195de1cf58e5dd2e1d0b49b309f3afc718cca92
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77656259"
---
# <a name="monitor-and-alert-on-application-health-with-azure-application-insights"></a>Toepassingsstatus bewaken en waarschuwingen erover verzenden met Azure Application Insights

Met Azure Application Insights kunt u uw toepassing bewaken en waarschuwingen ontvangen wanneer deze niet beschikbaar is, wanneer er fouten optreden, of wanneer er sprake is van prestatieproblemen.  Deze zelfstudie neemt u mee door het proces van het maken van tests om continu de beschikbaarheid van uw toepassing te controleren.

Procedures voor:

> [!div class="checklist"]
> * Beschikbaarheidstest maken voor het continu controleren van de reacties van de toepassing
> * E-mail verzenden naar beheerders wanneer zich een probleem voordoet

## <a name="prerequisites"></a>Vereisten

Vereisten om deze zelfstudie te voltooien:

Een [application insights-bron maken.](https://docs.microsoft.com/azure/azure-monitor/learn/dotnetcore-quick-start#enable-application-insights)

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij [https://portal.azure.com](https://portal.azure.com)de Azure-portal op .

## <a name="create-availability-test"></a>Beschikbaarheidstest maken

Met beschikbaarheidstests in Application Insights kunt u uw toepassing automatisch testen vanuit verschillende locaties over de hele wereld.   In deze zelfstudie voert u een url-test uit om ervoor te zorgen dat uw webtoepassing beschikbaar is.  U kunt ook een volledig overzicht maken om de werking van de toepassing gedetailleerd te testen. 

1. Selecteer **Application Insights** en selecteer vervolgens uw abonnement.  

2. Selecteer **Beschikbaarheid** onder het menu **Onderzoek** en klik op **Test maken**.

    ![Beschikbaarheidstest toevoegen](media/tutorial-alert/add-test-001.png)

3. Typ een naam voor de test en laat de andere standaardinstellingen ongewijzigd.  Deze selectie activeert aanvragen voor de url van de toepassing elke 5 minuten vanaf vijf verschillende geografische locaties.

4. Selecteer **Waarschuwingen** om de **vervolgkeuzelijst Waarschuwingen** te openen, waar u details definiëren voor hoe u reageren als de test mislukt. Kies **Bijna realtime** en stel de status in op **Ingeschakeld.**

    Typ een e-mailadres waarnaar een melding moet worden verzonden, wanneer wordt voldaan aan de waarschuwingscriteria.  U kunt optioneel ook het adres van een webhook typen dat moet worden aangeroepen wanneer wordt voldaan aan de waarschuwingscriteria.

    ![Test maken](media/tutorial-alert/create-test-001.png)

5. Ga terug naar het testpaneel, selecteer de ellipsen en bewerk de waarschuwing om de configuratie in te voeren voor uw bijna realtime waarschuwing.

    ![Waarschuwing bewerken](media/tutorial-alert/edit-alert-001.png)

6. Mislukte locaties instellen op groter dan of gelijk aan 3. Maak een [actiegroep](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) om te configureren wie een melding krijgt wanneer de waarschuwingsdrempel wordt overschreden.

    ![Waarschuwing-gebruikersinterface opslaan](media/tutorial-alert/save-alert-001.png)

7. Zodra u uw waarschuwing hebt geconfigureerd, klikt u op de testnaam om details van elke locatie weer te geven. Tests kunnen worden bekeken in zowel lijngrafiek als spreidingsplotformaat om het succes/de fouten voor een bepaald tijdbereik te visualiseren.

    ![Testdetails](media/tutorial-alert/test-details-001.png)

8. U inzoomen op de details van een test door op de stip in de spreidingsgrafiek te klikken. Hiermee wordt de end-to-end transactiedetailsweergave gestart. In het onderstaande voorbeeld ziet u de details van een mislukte aanvraag.

    ![Testresultaat](media/tutorial-alert/test-result-001.png)
  
## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u waarschuwingen voor problemen kunt genereren, gaat u verder met de volgende zelfstudie waarin u leert hoe u hoe kunt analyseren hoe uw gebruikers de toepassing gebruiken.

> [!div class="nextstepaction"]
> [Gebruikers begrijpen](../../azure-monitor/learn/tutorial-users.md)
