---
title: 'Snelstart: aan de slag met Azure Sentinel'
description: Azure Sentinel Quickstart - Aan de slag met Azure Sentinel
services: sentinel
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 95dcc135593c566eb1319ed52df3df6c1ada6609
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80067684"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Snelstart: aan de slag met Azure Sentinel




In deze quickstart leert u hoe u snel bekijken en controleren wat er in uw omgeving gebeurt met Azure Sentinel. Nadat u uw gegevensbronnen hebt gekoppeld aan Azure Sentinel, krijgt u direct visualisatie en analyse van gegevens, zodat u weet wat er gebeurt in al uw verbonden gegevensbronnen. Azure Sentinel biedt u werkmappen die u voorzien van de volledige kracht van hulpprogramma's die al beschikbaar zijn in Azure, evenals tabellen en grafieken die zijn ingebouwd om u te voorzien van analyses voor uw logboeken en query's. U ingebouwde werkmappen gebruiken of eenvoudig een nieuwe werkmap maken, helemaal opnieuw of op basis van een bestaande werkmap. 

## <a name="get-visualization"></a>Visualisatie bekijken

Om te visualiseren en te analyseren wat er gebeurt op uw omgeving, neem eerst een kijkje op het overzichtsdashboard om een idee te krijgen van de beveiligingshouding van uw organisatie. U op elk element van deze tegels klikken om in te zoomen op de ruwe gegevens waaruit ze zijn gemaakt. Om u te helpen ruis te verminderen en het aantal waarschuwingen te minimaliseren dat u moet controleren en onderzoeken, gebruikt Azure Sentinel een fusietechniek om waarschuwingen te correleren tot incidenten. **incidenten** zijn groepen gerelateerde waarschuwingen die samen een actievol incident creëren dat u onderzoeken en oplossen.

- Selecteer Azure Sentinel in de Azure-portal en selecteer vervolgens de werkruimte die u wilt controleren.

  ![Overzicht van Azure Sentinel](./media/qs-get-visibility/overview.png)

- De werkbalk aan de bovenkant geeft aan hoeveel gebeurtenissen u in de geselecteerde periode hebt gekregen en vergelijkt deze met de vorige 24 uur. De werkbalk vertelt u van deze gebeurtenissen, de waarschuwingen die zijn geactiveerd (het kleine aantal vertegenwoordigt verandering in de afgelopen 24 uur), en vervolgens vertelt u voor deze gebeurtenissen, hoeveel zijn geopend, in uitvoering en gesloten. Controleer of er geen dramatische toename of daling van het aantal gebeurtenissen is. Als er een daling is, kan het zijn dat een verbinding is gestopt met rapporteren aan Azure Sentinel. Als er een toename is, kan er iets verdachts gebeurd zijn. Controleer of u nieuwe waarschuwingen hebt.

   ![Azure Sentinel-trechter](./media/qs-get-visibility/funnel.png)

De hoofdtekst van de overzichtspagina geeft in één oogopslag inzicht in de beveiligingsstatus van uw werkruimte:

- **Gebeurtenissen en waarschuwingen in**de loop van de tijd : geeft een overzicht van het aantal gebeurtenissen en het aantal waarschuwingen dat van deze gebeurtenissen is gemaakt. Als u een piek ziet die ongebruikelijk is, ziet u er waarschuwingen voor - als er iets ongewoons is waar er een piek in gebeurtenissen is, maar u geen waarschuwingen ziet, kan dit reden tot bezorgdheid zijn.

- **Mogelijke schadelijke gebeurtenissen:** Wanneer verkeer wordt gedetecteerd uit bronnen waarvan bekend is dat ze schadelijk zijn, waarschuwt Azure Sentinel u op de kaart. Als u oranje ziet, is het binnenkomend verkeer: iemand probeert toegang te krijgen tot uw organisatie vanaf een bekend kwaadaardig IP-adres. Als u Uitgaande (rode) activiteit ziet, betekent dit dat gegevens van uw netwerk vanuit uw organisatie worden gestreamd naar een bekend kwaadaardig IP-adres.

   ![Azure Sentinel-kaart](./media/qs-get-visibility/map.png)


- **Recente incidenten**: Om uw recente incidenten, de ernst ervan en het aantal waarschuwingen in verband met het incident te bekijken. Als u ziet als plotselinge piek in een specifiek type waarschuwing, kan dit betekenen dat er momenteel een actieve aanval wordt uitgevoerd. Als u bijvoorbeeld een plotselinge piek van 20 Pass-the-hash-gebeurtenissen van Azure ATP hebt, is het mogelijk dat iemand u momenteel probeert aan te vallen.

- **Gegevensbronafwijkingen**: De gegevensanalisten van Microsoft hebben modellen gemaakt die voortdurend zoeken naar gegevens uit uw gegevensbronnen op afwijkingen. Als er geen afwijkingen zijn, wordt er niets weergegeven. Als afwijkingen worden gedetecteerd, moet je er diep in duiken om te zien wat er is gebeurd. Klik bijvoorbeeld op de piek in Azure Activity. U op **Grafiek** klikken om te zien wanneer de piek is gebeurd en vervolgens filteren op activiteiten die zich in die periode hebben voorgedaan om te zien wat de piek heeft veroorzaakt.

   ![Azure Sentinel-kaart](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken<a name="dashboards"></a>

Ingebouwde werkmappen bieden geïntegreerde gegevens uit uw verbonden gegevensbronnen om u een diepe duik te laten nemen in de gebeurtenissen die in die services worden gegenereerd. De ingebouwde werkmappen bevatten Azure AD, Azure-activiteitsgebeurtenissen en on-premises, gegevens van Windows-gebeurtenissen van servers, van waarschuwingen van derden, van derden, waaronder firewallverkeerslogboeken, Office 365 en onveilige protocollen op basis van Windows Gebeurtenissen. De werkmappen zijn gebaseerd op Azure Monitor Workbooks om u te voorzien van verbeterde aanpasbaarheid en flexibiliteit bij het ontwerpen van uw eigen werkmap. Zie [Werkmappen voor](../azure-monitor/app/usage-workbooks.md)meer informatie.

1. Selecteer **Werkmappen**onder **Instellingen**. Onder **Geïnstalleerd**u al uw geïnstalleerde werkmap bekijken. Onder **Alles**ziet u de hele galerij met ingebouwde werkmappen die beschikbaar zijn voor installatie. 
2. Zoek naar een specifieke werkmap om de hele lijst en beschrijving van wat elk biedt te zien. 
3. Ervan uitgaande dat u Azure AD gebruikt om aan de slag te gaan met Azure Sentinel, raden we u aan ten minste de volgende werkmappen te installeren:
   - **Azure AD:** gebruik een van de volgende opties:
       - **Azure AD-aanmeldingen** analyseert aanmeldingen in de loop van de tijd om te zien of er afwijkingen zijn. Deze werkmappen bieden mislukte aanmeldingen per toepassing, apparaten en locaties, zodat u in één oogopslag zien of er iets ongewoons gebeurt. Besteed aandacht aan meerdere mislukte aanmeldingen. 
       - **Azure AD-controlelogboeken** analyseert beheeractiviteiten, zoals wijzigingen in gebruikers (toevoegen, verwijderen, enz.), groepscreatie en wijzigingen.  

   - Voeg een werkmap toe voor uw firewall. Voeg bijvoorbeeld de werkmap Palo Alto toe. De werkmap analyseert uw firewallverkeer, biedt u correlaties tussen uw firewallgegevens en bedreigingsgebeurtenissen en markeert verdachte gebeurtenissen tussen entiteiten. Werkmappen bieden u informatie over trends in uw verkeer en laten u inzoomen op en filteren resultaten. 

      ![Pal Alto-dashboard](./media/qs-get-visibility/palo-alto-week-query.png)


U de werkmappen aanpassen door de ![](./media/qs-get-visibility/edit-query-button.png)hoofdqueryknop te bewerken. U op ![](./media/qs-get-visibility/go-to-la-button.png) de knop klikken om naar Log Analytics te gaan [om de query daar te bewerken](../azure-monitor/log-query/get-started-portal.md), en u de ellips (...) selecteren en **tegelgegevens aanpassen**, waarmee u het hoofdtijdfilter bewerken of de specifieke tegels uit de werkmap verwijderen.

Zie [Zelfstudie: Visuele gegevens in Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md) voor meer informatie over het werken met query's

### <a name="add-a-new-tile"></a>Een nieuwe tegel toevoegen

Als u een nieuwe tegel wilt toevoegen, u deze toevoegen aan een bestaande werkmap, een tegel die u maakt of een ingebouwde werkmap van Azure Sentinel. 
1. Maak in Log Analytics een tegel met de instructies in [Zelfstudie: Visuele gegevens in Log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Nadat de tegel is gemaakt, selecteert u onder **Vastmaken**de werkmap waarin u de tegel wilt weergeven.

## <a name="create-new-workbooks"></a>Nieuwe werkmappen maken
U een nieuwe werkmap helemaal opnieuw maken of een ingebouwde werkmap gebruiken als basis voor uw nieuwe werkmap.

1. Als u een nieuwe werkmap helemaal opnieuw wilt maken, selecteert u **Werkmappen** en **vervolgens +Nieuwe werkmap**.
2. Selecteer het abonnement waarin de werkmap is gemaakt en geef het een beschrijvende naam. Elke werkmap is een Azure-bron als alle andere en u deze rollen (RBAC) toewijzen om te definiëren en te beperken wie toegang heeft. 
3. Om deze in te schakelen om in uw werkmappen te worden weergegeven om visualisaties vast te maken, moet u deze delen. Klik **op Delen** en vervolgens gebruikers **beheren**. 
 
1. Gebruik de **toewijzingen** **voor toegang en** rol controleren zoals u dat zou doen voor elke andere Azure-bron. Zie [Azure-werkmappen delen met RBAC](../azure-portal/azure-portal-dashboard-share-access.md)voor meer informatie.


## <a name="new-workbook-examples"></a>Voorbeelden van nieuwe werkmappen

Met de volgende voorbeeldquery u trends in het verkeer over weken vergelijken. U eenvoudig schakelen op welke apparaatleverancier en gegevensbron u de query uitvoert. In dit voorbeeld wordt SecurityEvent van Windows gebruikt, u deze inschakelen om op AzureActivity of CommonSecurityLog op een andere firewall uit te voeren.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


U een query maken waarin gegevens uit meerdere bronnen zijn verwerkt. U een query maken waarin wordt gekeken naar Azure Active Directory-controlelogboeken voor nieuwe gebruikers die net zijn gemaakt, en vervolgens uw Azure-logboeken controleren om te zien of de gebruiker binnen 24 uur na het maken is begonnen met het aanbrengen van wijzigingen in roltoewijzing. Die verdachte activiteit zou verschijnen op dit dashboard:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

U verschillende werkmappen maken op basis van de rol van de persoon die naar de gegevens kijkt en wat ze zoeken. U bijvoorbeeld een werkmap maken voor uw netwerkbeheerder die de firewallgegevens bevat. U ook werkmappen maken op basis van hoe vaak u ze wilt bekijken, of er dingen zijn die u dagelijks wilt bekijken en andere items die u eenmaal per uur wilt controleren, bijvoorbeeld, u uw Azure AD-aanmeldingen elk uur bekijken om te zoeken naar afwijkingen . 

## <a name="create-new-detections"></a>Nieuwe detecties maken

Genereer detecties op de [gegevensbronnen die u hebt verbonden met Azure Sentinel](connect-data-sources.md) om bedreigingen in uw organisatie te onderzoeken.

Wanneer u een nieuwe detectie maakt, u gebruikmaken van de ingebouwde detecties die zijn gemaakt door beveiligingsonderzoekers van Microsoft die zijn afgestemd op de gegevensbronnen die u hebt aangesloten.

Als u alle kant-en-klare detecties wilt weergeven, gaat u naar **Analytics** en **vervolgens regelsjablonen**. Dit tabblad bevat alle ingebouwde Azure Sentinel-regels.

   ![Gebruik ingebouwde detecties om bedreigingen te vinden met Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Zie [Zelfstudie: Get built-in-analytics](tutorial-detect-threats-built-in.md)voor meer informatie over het verkrijgen van out-of-the-box detecties.
 
## <a name="next-steps"></a>Volgende stappen
In deze quickstart hebt u geleerd hoe u aan de slag met Azure Sentinel. Ga verder naar de zelfstudie voor [het detecteren van bedreigingen.](tutorial-detect-threats-built-in.md)
> [!div class="nextstepaction"]
> [Maak aangepaste regels voor bedreigingsdetectie](tutorial-detect-threats-custom.md) om uw reacties op bedreigingen te automatiseren.

