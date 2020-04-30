---
title: 'Snelstartgids: aan de slag met Azure Sentinel'
description: 'Azure Sentinel Snelstartgids: aan de slag met Azure Sentinel'
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80067684"
---
# <a name="quickstart-get-started-with-azure-sentinel"></a>Snelstartgids: aan de slag met Azure Sentinel




In deze Quick Start leert u hoe u snel kunt zien wat er in uw omgeving gebeurt met Azure Sentinel. Nadat u uw gegevens bronnen aan Azure Sentinel hebt gekoppeld, krijgt u een snelle visualisatie en analyse van de gegevens, zodat u kunt zien wat er gebeurt in al uw verbonden gegevens bronnen. Met Azure Sentinel beschikt u over de volledige kracht van hulpprogram ma's die al beschikbaar zijn in azure, evenals tabellen en grafieken die zijn ingebouwd om u te voorzien van analyses voor uw logboeken en query's. U kunt de ingebouwde werkmappen gebruiken of een nieuwe werkmap maken, helemaal zelf of op basis van een bestaande werkmap. 

## <a name="get-visualization"></a>Visualisatie ophalen

Bekijk eerst het overzichts dashboard om een idee te krijgen van de beveiligings postuur van uw organisatie voor het visualiseren en ophalen van analyses van wat er gebeurt in uw omgeving. U kunt op elk element van deze tegels klikken om in te zoomen op de onbewerkte gegevens van waaruit ze zijn gemaakt. Om het geluid te verminderen en het aantal waarschuwingen dat u moet beoordelen en te minimaliseren, gebruikt Azure Sentinel een fusie techniek om waarschuwingen in incidenten te correleren. **incidenten** zijn groepen gerelateerde waarschuwingen die samen een actie kunnen ondernemen die u kunt onderzoeken en oplossen.

- Selecteer in de Azure Portal Azure Sentinel en selecteer vervolgens de werk ruimte die u wilt bewaken.

  ![Overzicht van Azure Sentinel](./media/qs-get-visibility/overview.png)

- De werk balk aan de bovenkant geeft aan hoeveel gebeurtenissen u tijdens de geselecteerde periode hebt geselecteerd en vergelijkt deze met de voor gaande 24 uur. Op de werk balk wordt u van deze gebeurtenissen verteld, de waarschuwingen die zijn geactiveerd (het kleine getal vertegenwoordigt een wijziging in de afgelopen 24 uur), en vervolgens wordt u op de hoogte gebracht van die gebeurtenissen, hoeveel openstaan, in uitvoering en gesloten. Controleer of het aantal gebeurtenissen geen aanzienlijke toename of drop heeft. Als er een drop is, kan het zijn dat een verbinding is gestopt met het melden van Azure Sentinel. Als er een verhoging is, is er mogelijk een verdacht probleem opgetreden. Controleer of er nieuwe waarschuwingen zijn.

   ![Azure Sentinel trechter](./media/qs-get-visibility/funnel.png)

De hoofd tekst van de overzichts pagina geeft inzicht in de beveiligings status van uw werk ruimte:

- **Gebeurtenissen en waarschuwingen**in de loop van de tijd: hier wordt het aantal gebeurtenissen weer gegeven en hoeveel waarschuwingen zijn gemaakt op basis van deze gebeurtenissen. Als er sprake is van een ongebruikelijke piek, ziet u er waarschuwingen voor. als er iets ongebruikelijk is waarbij er sprake is van een Prikker, maar u geen waarschuwingen ziet, kan dit de oorzaak van het probleem zijn.

- **Mogelijke schadelijke gebeurtenissen**: wanneer er verkeer wordt gedetecteerd van bronnen waarvan bekend is dat ze schadelijk zijn, wordt u door Azure Sentinel gewaarschuwd op de kaart. Als oranje wordt weer geven, is het inkomend verkeer: iemand probeert toegang te krijgen tot uw organisatie vanaf een bekend schadelijk IP-adres. Als de activiteit uitgaand (rood) wordt weer gegeven, betekent dit dat de gegevens van uw netwerk worden gestreamd uit uw organisatie naar een bekend schadelijk IP-adres.

   ![Azure-Sentinel-toewijzing](./media/qs-get-visibility/map.png)


- **Recente incidenten**: als u uw recente incidenten wilt weer geven, zijn de ernst en het aantal waarschuwingen dat is gekoppeld aan het incident. Als u de onverwachte piek in een specifiek type waarschuwing ziet, kan dit betekenen dat er momenteel een actieve aanval actief is. Als u bijvoorbeeld een plotselinge piek van 20 Pass-the-hash-gebeurtenissen van Azure ATP hebt, is het mogelijk dat iemand een aanval uitvoert.

- **Gegevens bron afwijkingen**: de gegevens analisten van micro soft hebben modellen gemaakt die de gegevens van uw gegevens bronnen voortdurend doorzoeken op afwijkingen. Als er geen afwijkingen zijn, wordt er niets weer gegeven. Als er afwijkingen worden gedetecteerd, moet u zich diep vinden om te zien wat er is gebeurd. Klik bijvoorbeeld op de Prikker in azure activity. U kunt klikken op de **grafiek** om te zien wanneer de Prikker zich voordeed en vervolgens filteren op activiteiten die tijdens die periode zijn opgetreden om te zien wat de oorzaak van de Prikker heeft.

   ![Azure-Sentinel-toewijzing](./media/qs-get-visibility/anomolies.png)

## <a name="use-built-in-workbooks"></a>Ingebouwde werkmappen gebruiken<a name="dashboards"></a>

Ingebouwde werkmappen bieden geïntegreerde gegevens van uw verbonden gegevens bronnen om u te laten overgaan op de gebeurtenissen die in deze services worden gegenereerd. De ingebouwde werkmappen omvatten Azure AD, gebeurtenissen voor Azure-activiteiten en on-premises, die gegevens kunnen bevatten van Windows-gebeurtenissen van servers, van de eerste partij, van derden, waaronder firewall verkeers logboeken, Office 365 en onveilige protocollen op basis van Windows-gebeurtenissen. De werkmappen zijn gebaseerd op Azure Monitor werkmappen om u te voorzien van verbeterde aanpassings mogelijkheden en flexibiliteit bij het ontwerpen van uw eigen werkmap. Zie [werkmappen](../azure-monitor/app/usage-workbooks.md)voor meer informatie.

1. Onder **instellingen**selecteert u **werkmappen**. Onder **geïnstalleerd**ziet u alle geïnstalleerde werkmappen. Onder **alle**kunt u de volledige galerie met ingebouwde werkmappen zien die beschikbaar zijn voor installatie. 
2. Zoek naar een specifieke werkmap om de hele lijst en een beschrijving van de Voorst Ellen te bekijken. 
3. Als u Azure AD gebruikt om aan de slag te gaan met Azure Sentinel, raden we u aan ten minste de volgende werkmappen te installeren:
   - **Azure AD**: gebruik een van de volgende of beide:
       - Met **Azure AD-aanmeldingen** worden aanmeldingen in de loop van de tijd geanalyseerd om te zien of er afwijkingen zijn. Deze werkmappen bevat mislukte aanmeldingen door toepassingen, apparaten en locaties, zodat u in één oogopslag kunt zien of er iets ongebruikelijk is. Let op meerdere mislukte aanmeldingen. 
       - Met **Azure AD-controle logboeken** worden beheer activiteiten geanalyseerd, zoals wijzigingen in gebruikers (toevoegen, verwijderen, enz.), het maken van groepen en het wijzigen van wijzigingen.  

   - Voeg een werkmap toe voor uw firewall. Voeg bijvoorbeeld de Palo Alto-werkmap toe. De werkmap analyseert het verkeer van uw firewall, met een correlatie tussen uw firewall gegevens en bedreigings gebeurtenissen, en verlaagt verdachte gebeurtenissen tussen entiteiten. Werkmappen bieden informatie over trends in uw verkeer en u kunt inzoomen op en filteren op resultaten. 

      ![PAL Alto-dash board](./media/qs-get-visibility/palo-alto-week-query.png)


U kunt de werkmappen aanpassen door de knop ![](./media/qs-get-visibility/edit-query-button.png)hoofd query te bewerken. U kunt klikken op de ![knop](./media/qs-get-visibility/go-to-la-button.png) om naar [log Analytics te gaan om de query daar te bewerken](../azure-monitor/log-query/get-started-portal.md), en u kunt het weglatings teken (...) selecteren en **tegel gegevens aanpassen**selecteren, zodat u het filter voor de hoofdtijden kan bewerken of de specifieke tegels uit de werkmap moet verwijderen.

Zie [zelf studie: visuele gegevens in log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md) voor meer informatie over het werken met query's

### <a name="add-a-new-tile"></a>Een nieuwe tegel toevoegen

Als u een nieuwe tegel wilt toevoegen, kunt u deze toevoegen aan een bestaande werkmap, een van de elementen die u maakt of een ingebouwde Azure-Sentinel-werkmap. 
1. Maak in Log Analytics een tegel met behulp van de instructies in de [zelf studie: visuele gegevens in log Analytics](../azure-monitor/learn/tutorial-logs-dashboards.md). 
2. Nadat de tegel is gemaakt, selecteert u onder **pincode**de werkmap waarin u de tegel wilt weer geven.

## <a name="create-new-workbooks"></a>Nieuwe werkmappen maken
U kunt een volledig nieuwe werkmap maken of een ingebouwde werkmap gebruiken als basis voor de nieuwe werkmap.

1. Als u een volledig nieuwe werkmap wilt maken, selecteert u **werkmappen** en vervolgens **+ nieuwe werkmap**.
2. Selecteer het abonnement waarin de werkmap is gemaakt en geef deze een beschrijvende naam. Elke werkmap is een Azure-resource, zoals andere, en u kunt IT-rollen (RBAC) toewijzen om te bepalen wie toegang heeft. 
3. Als u wilt dat deze wordt weer gegeven in uw werkmappen om visualisaties vast te maken, moet u deze delen. Klik op **delen** en vervolgens op **gebruikers beheren**. 
 
1. Gebruik de **optie toegang** en **roltoewijzingen** controleren, net zoals u zou doen voor andere Azure-resources. Zie voor meer informatie [Azure-werkmappen delen met behulp van RBAC](../azure-portal/azure-portal-dashboard-share-access.md).


## <a name="new-workbook-examples"></a>Voorbeelden van nieuwe werkmappen

Met de volgende voorbeeld query kunt u trends van verkeer tussen weken vergelijken. U kunt eenvoudig overschakelen naar de leverancier en gegevens bron van het apparaat waarop u de query uitvoert. In dit voor beeld maakt gebruik van SecurityEvent van Windows. u kunt deze switch zo wijzigen dat deze wordt uitgevoerd op AzureActivity of CommonSecurityLog op elke andere firewall.

     |where DeviceVendor == "Palo Alto Networks":
      // week over week query
      SecurityEvent
      | where TimeGenerated > ago(14d)
      | summarize count() by bin(TimeGenerated, 1d)
      | extend Week = iff(TimeGenerated>ago(7d), "This Week", "Last Week"), TimeGenerated = iff(TimeGenerated>ago(7d), TimeGenerated, TimeGenerated + 7d)


Mogelijk wilt u een query maken die gegevens uit meerdere bronnen bevat. U kunt een query maken om Azure Active Directory audit logboeken te bekijken voor nieuwe gebruikers die zojuist zijn gemaakt. vervolgens controleert u de logboeken van uw Azure om na te gaan of de gebruiker is begonnen met het wijzigen van de roltoewijzing binnen 24 uur na het maken van de toewijzing. Deze verdachte activiteit zou op dit dash board worden weer gegeven:

    AuditLogs
    | where OperationName == "Add user"
    | project AddedTime = TimeGenerated, user = tostring(TargetResources[0].userPrincipalName)
    | join (AzureActivity
    | where OperationName == "Create role assignment"
    | project OperationName, RoleAssignmentTime = TimeGenerated, user = Caller) on user
    | project-away user1

U kunt verschillende werkmappen maken op basis van de rol van de persoon die de gegevens bekijkt en wat ze zoeken. U kunt bijvoorbeeld een werkmap maken voor uw netwerk beheerder die de firewall gegevens bevat. U kunt ook werkmappen maken op basis van hoe vaak u deze wilt bekijken, of u nu dagelijks wilt controleren, en andere items die u eenmaal per uur wilt controleren. u kunt bijvoorbeeld elk uur uw Azure AD-aanmeldingen bekijken om te zoeken naar afwijkingen. 

## <a name="create-new-detections"></a>Nieuwe detecties maken

Genereer detecties op de [gegevens bronnen die u hebt verbonden met Azure Sentinel](connect-data-sources.md) om bedreigingen in uw organisatie te onderzoeken.

Wanneer u een nieuwe detectie maakt, moet u gebruikmaken van de ingebouwde detecties die zijn gemaakt door micro soft-beveiligings onderzoekers die zijn afgestemd op de gegevens bronnen die u hebt verbonden.

Als u alle opstaande detecties wilt weer geven, gaat u naar **Analytics** en vervolgens op **regel sjablonen**. Dit tabblad bevat alle ingebouwde Azure Sentinel-regels.

   ![Ingebouwde detecties gebruiken om bedreigingen te vinden met Azure Sentinel](media/tutorial-detect-built-in/view-oob-detections.png)

Zie [zelf studie: ingebouwde analyses ophalen](tutorial-detect-threats-built-in.md)voor meer informatie over het verkrijgen van out-of-the-box-detecties.
 
## <a name="next-steps"></a>Volgende stappen
In deze Snelstartgids hebt u geleerd hoe u aan de slag kunt met Azure Sentinel. Ga door naar de zelf studie voor [het detecteren van bedreigingen](tutorial-detect-threats-built-in.md).
> [!div class="nextstepaction"]
> [Aangepaste regels voor detectie van bedreigingen maken](tutorial-detect-threats-custom.md) om uw reacties op bedreigingen te automatiseren.

