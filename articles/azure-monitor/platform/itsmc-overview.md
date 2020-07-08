---
title: IT Service Management-connector in azure Log Analytics | Microsoft Docs
description: Dit artikel bevat een overzicht van IT Service Management-connector (ITSMC) en informatie over hoe u deze oplossing kunt gebruiken om de ITSM-werk items in azure Log Analytics centraal te controleren en te beheren, en eventuele problemen snel op te lossen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: b0ae621791fb989fbb1fdab6a045bc8e9707bc74
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83846525"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Verbinding maken tussen Azure en ITSM-hulpprogramma's met behulp van IT Service Management-connector

![IT Service Management-connector-symbool](media/itsmc-overview/itsmc-symbol.png)

Met de IT Service Management-connector (ITSMC) kunt u Azure verbinden met een ondersteund ITSM-product/service (IT-servicebeheer).

Azure-Services zoals Log Analytics en Azure Monitor bieden hulp middelen voor het detecteren, analyseren en oplossen van problemen met uw Azure-en niet-Azure-resources. De werk items die betrekking hebben op een probleem bevinden zich meestal echter in een ITSM-product/-service. De ITSM-connector biedt een bidirectionele verbinding tussen Azure-en ITSM-hulpprogram ma's die u helpen problemen sneller op te lossen.

ITSMC ondersteunt verbindingen met de volgende ITSM-hulpprogram ma's:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Met ITSMC kunt u het volgende doen:

-  Werk items maken in ITSM-hulp programma, op basis van uw Azure-waarschuwingen (metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en Log Analytics waarschuwingen).
-  U kunt eventueel uw incident synchroniseren en gegevens van uw ITSM-hulp programma wijzigen in een Azure Log Analytics-werk ruimte.

Lees meer over de [juridische voor waarden en het privacybeleid](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

U kunt de ITSM-connector gaan gebruiken door de volgende stappen uit te voeren:

1.  [De oplossing voor ITSM-connector toevoegen](#adding-the-it-service-management-connector-solution)
2.  Een ITSM-verbinding maken
3.  [De verbinding gebruiken](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>De IT Service Management-connector-oplossing toevoegen

Voordat u een verbinding kunt maken, moet u de ITSM-connector-oplossing toevoegen.

1. Klik in Azure Portal op **+ Nieuw** pictogram.

   ![Nieuwe Azure-resource](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT Service Management-connector** in Marketplace en klik op **maken**.

   ![ITSMC-oplossing toevoegen](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **OMS-werk ruimte** de Azure log Analytics-werk ruimte waar u de oplossing wilt installeren.
   >[!NOTE]
   > * Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als Log Analytics.
   > * De ITSM-connector kan alleen worden geïnstalleerd in Log Analytics-werk ruimten in de volgende regio's: VS-Oost, West VS2, Zuid-Centraal VS, VS-West-Centraal, Fairfax, Centraal-Canada, Europa-west, Zuid-Brittannië, Zuidoost-Azië, Oost-Japan, Centraal-India, Zuidoost-Australië.

4. Selecteer in de sectie **OMS-werk ruimte** de ResourceGroup waar u de oplossings resource wilt maken.

   ![ITSMC-werk ruimte](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Als onderdeel van de doorlopende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS-werk ruimten nu aangeduid als Log Analytics.

5. Klik op **Create**.

Wanneer de oplossings resource is geïmplementeerd, verschijnt er een melding in de rechter bovenhoek van het venster.


## <a name="creating-an-itsm--connection"></a>Een ITSM-verbinding maken

Nadat u de oplossing hebt geïnstalleerd, kunt u een verbinding maken.

Voor het maken van een verbinding moet u uw ITSM-hulp programma voorbereiden om de verbinding van de ITSM-connector oplossing toe te staan.  

Gebruik de volgende stappen, afhankelijk van het ITSM-product waarmee u verbinding maakt:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Nadat u uw ITSM-hulp middelen hebt bereid, volgt u de onderstaande stappen om een verbinding te maken:

1. Ga naar **alle resources**, zoek naar **Service Desk (YourWorkspaceName)**.
2. Klik onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster op **ITSM-verbindingen**.
   ![ITSM-verbindingen](media/itsmc-overview/itsm-connections.png)

   Op deze pagina wordt de lijst met verbindingen weer gegeven.
3. Klik op **verbinding toevoegen**.

   ![ITSM-verbinding toevoegen](media/itsmc-overview/add-new-itsm-connection.png)

4. Geef de verbindings instellingen op, zoals beschreven in [de ITSMC-verbinding configureren met het artikel ITSM Products/Services](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > Standaard worden de configuratie gegevens van de verbinding in elke 24 uur in ITSMC vernieuwd. Als u de gegevens van uw verbinding direct wilt vernieuwen voor wijzigingen of sjabloon updates die u aanbrengt, klikt u op de knop **synchroniseren** op de Blade van de verbinding.

   ![Verbinding vernieuwen](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>De oplossing gebruiken
   U kunt met behulp van de ITSM-connector-oplossing werk items maken van Azure-waarschuwingen, Log Analytics waarschuwingen en Log Analytics logboek records.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werk items maken op basis van Azure-waarschuwingen

Zodra u uw ITSM-verbinding hebt gemaakt, kunt u een of meer werk items maken in uw ITSM-hulp programma op basis van Azure-waarschuwingen met behulp van de **actie ITSM** in **actie groepen**.

Actie groepen bieden een modulaire en herbruikbare manier om acties voor uw Azure-waarschuwingen te activeren. U kunt actie groepen met metrische waarschuwingen, waarschuwingen voor activiteiten logboeken en waarschuwingen voor Azure Log Analytics gebruiken in Azure Portal.

Gebruik de volgende procedure:

1. Klik in Azure Portal op **monitor**.
2. Klik in het linkerdeel venster op **actie groepen**. Het venster **actie groep toevoegen** wordt weer gegeven.

    ![Actiegroepen](media/itsmc-overview/action-groups.png)

3. Geef de **naam** en **kortheid** voor uw actie groep op. Selecteer de **resource groep** en het **abonnement** waar u uw actie groep wilt maken.

    ![Details van actie groepen](media/itsmc-overview/action-groups-details.png)

4. Selecteer in de lijst acties de optie **ITSM** in de vervolg keuzelijst voor het **actie type**. Geef een **naam** op voor de actie en klik op **Details bewerken**.
5. Selecteer het **abonnement** waarin uw log Analytics-werk ruimte zich bevindt. Selecteer de naam van de **verbinding** (uw ITSM-connector naam), gevolgd door de naam van uw werk ruimte. Bijvoorbeeld ' MyITSMMConnector (MyWorkspace) '.

    ![Details van ITSM-actie](media/itsmc-overview/itsm-action-details.png)

6. Selecteer type **werk item** in de vervolg keuzelijst.
   Kies of u een bestaande sjabloon wilt gebruiken of vul de velden in die vereist zijn voor uw ITSM-product.
7. Klik op **OK**.

Wanneer u een Azure-waarschuwings regel maakt/bewerkt, gebruikt u een actie groep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt werk item gemaakt/bijgewerkt in het ITSM-hulp programma.

> [!NOTE]
>
> Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/monitor/) voor actie groepen voor meer informatie over de prijzen van de ITSM-actie.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Het incident en de gegevens van de wijzigings aanvraag visualiseren en analyseren

Op basis van uw configuratie bij het instellen van een verbinding, kan ITSM-connector tot 120 dagen aan incidenten en wijzigings aanvraag gegevens synchroniseren. Het logboek record schema voor deze gegevens vindt u in de [volgende sectie](#additional-information).

De incident-en wijzigings aanvraag gegevens kunnen worden gevisualiseerd met behulp van het ITSM-connector dash board in de oplossing.

![Log Analytics scherm](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dash board bevat ook informatie over connector status die kan worden gebruikt als uitgangs punt voor het analyseren van problemen met de verbindingen.

U kunt de incidenten die zijn gesynchroniseerd met de betrokken computers ook visualiseren binnen de Servicetoewijzing oplossing.

Servicetoewijzing detecteert automatisch de toepassings onderdelen op Windows-en Linux-systemen en wijst de communicatie tussen services toe. Zo kunt u uw servers beschouwen zoals u ze voor ogen ziet: als onderling verbonden systemen die kritieke services verlenen. Servicetoewijzing toont verbindingen tussen servers, processen en poorten in alle via TCP verbonden architectuur. U hoeft hiervoor niets meer te doen dan het installeren van een agent. [Meer informatie](../../azure-monitor/insights/service-map.md).

Als u de Servicetoewijzing oplossing gebruikt, kunt u de Service Desk-items weer geven die zijn gemaakt in de ITSM-oplossingen, zoals wordt weer gegeven in het volgende voor beeld:

![Log Analytics scherm](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Meer informatie: [servicetoewijzing](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Aanvullende informatie

### <a name="data-synced-from-itsm-product"></a>Gegevens die zijn gesynchroniseerd vanuit het ITSM-product
Incidenten en wijzigings aanvragen worden gesynchroniseerd vanuit uw ITSM-product naar uw Log Analytics-werk ruimte op basis van de configuratie van de verbinding.

In de volgende informatie ziet u voor beelden van gegevens die zijn verzameld door ITSMC:

> [!NOTE]
>
> Afhankelijk van het type werk item dat is geïmporteerd in Log Analytics, bevat **ServiceDesk_CL** de volgende velden:

**Werk item:** **incidenten**  
ServiceDeskWorkItemType_s = "incident"

**Fields**

- ServiceDeskConnectionName
- Service Desk-ID
- Status
- Urgentie
- Impact
- Prioriteit
- Escalatie
- Created By
- Opgelost door
- Gesloten door
- Bron
- Toegewezen aan
- Categorie
- Titel
- Beschrijving
- Gemaakt op
- Datum gesloten
- Datum opgelost
- Datum laatst gewijzigd
- Computer


**Werk item:** **wijzigings aanvragen**

ServiceDeskWorkItemType_s = "ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- Service Desk-ID
- Created By
- Gesloten door
- Bron
- Toegewezen aan
- Titel
- Type
- Categorie
- Status
- Escalatie
- Conflict status
- Urgentie
- Prioriteit
- Risico
- Impact
- Toegewezen aan
- Gemaakt op
- Datum gesloten
- Datum laatst gewijzigd
- Aangevraagde datum
- Geplande begin datum
- Geplande eind datum
- Begin datum van werk
- Eind datum van werk
- Description
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Uitvoer gegevens voor een ServiceNow-incident

| Log Analytics veld | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| IncidentState_s | Status |
| Urgency_s |Urgentie |
| Impact_s |Impact|
| Priority_s | Prioriteit |
| CreatedBy_s | Geopend door |
| ResolvedBy_s | Opgelost door|
| ClosedBy_s  | Gesloten door |
| Source_s| Type contact |
| AssignedTo_s | Toegewezen aan  |
| Category_s | Categorie |
| Title_s|  Korte beschrijving |
| Description_s|  Notities |
| CreatedDate_t|  Had |
| ClosedDate_t| gesloten|
| ResolvedDate_t|Opgelost|
| Computer  | Configuratie-item |

## <a name="output-data-for-a-servicenow-change-request"></a>Uitvoer gegevens voor een wijzigings aanvraag voor een ServiceNow

| Log Analytics | Het veld ServiceNow |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| CreatedBy_s | Aangevraagd door |
| ClosedBy_s | Gesloten door |
| AssignedTo_s | Toegewezen aan  |
| Title_s|  Korte beschrijving |
| Type_s|  Type |
| Category_s|  Categorie |
| CRState_s|  Status|
| Urgency_s|  Urgentie |
| Priority_s| Prioriteit|
| Risk_s| Risico|
| Impact_s| Impact|
| RequestedDate_t  | Aangevraagd door datum |
| ClosedDate_t | Gesloten datum |
| PlannedStartDate_t  |     Geplande begin datum |
| PlannedEndDate_t  |   Geplande eind datum |
| WorkStartDate_t  | Werkelijke begin datum |
| WorkEndDate_t | Werkelijke eind datum|
| Description_s | Description |
| Computer  | Configuratie-item |


## <a name="troubleshoot-itsm-connections"></a>Problemen met ITSM-verbindingen oplossen
1. Als er geen verbinding kan worden gemaakt met de gebruikers interface van de verbonden bron met een **Fout bij het opslaan** van het verbindings bericht, voert u de volgende stappen uit:
   - Voor ServiceNow-, Cher well-en Provance-verbindingen,  
   - Zorg ervoor dat u de gebruikers naam, het wacht woord, de client-ID en het client geheim correct hebt ingevoerd voor elk van de verbindingen.  
   - Controleer of u voldoende bevoegdheden hebt in het bijbehorende ITSM-product om de verbinding tot stand te brengen.  
   - Voor Service Manager verbindingen,  
   - Zorg ervoor dat de web-app is geïmplementeerd en dat er een hybride verbinding is gemaakt. Als u wilt controleren of de verbinding tot stand is gebracht met behulp van de on-premises Service Manager machine, gaat u naar de web-app-URL zoals beschreven in de documentatie voor het maken van de [hybride verbinding](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection).  

2. Als gegevens van ServiceNow niet worden gesynchroniseerd met Log Analytics, moet u ervoor zorgen dat het ServiceNow-exemplaar niet in de slaap stand staat. ServiceNow dev-instanties gaan soms naar de slaap stand als deze gedurende een lange periode niet actief zijn. Anders meldt u het probleem.
3. Als Log Analytics waarschuwingen wordt geactiveerd, maar er geen werk items worden gemaakt in ITSM product of configuratie-items niet zijn gemaakt/gekoppeld aan werk items of voor andere algemene informatie, kijkt u op de volgende locaties:
   -  ITSMC: de oplossing toont een samen vatting van verbindingen/werk items/computers, enzovoort. Klik op de tegel met de status van de **connector**, waarmee u de **Zoek opdracht kunt vastleggen** in een logboek met de relevante query. Bekijk de logboek records met LogType_S als fout voor meer informatie.
   - **Zoek pagina voor logboeken** : Bekijk de fout/gerelateerde informatie rechtstreeks met behulp van de query `*` ServiceDeskLog_CL `*` .

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Problemen met de implementatie van Service Manager web-app oplossen
1.  In het geval van problemen met de implementatie van web-apps, moet u ervoor zorgen dat u voldoende machtigingen hebt in het abonnement dat wordt vermeld voor het maken/implementeren van resources.
2.  Als u de fout **' object verwijzing is niet ingesteld op exemplaar van een object '** krijgt wanneer u het [script](itsmc-service-manager-script.md)uitvoert, moet u ervoor zorgen dat u geldige waarden hebt opgegeven onder **gebruikers configuratie** .
3.  Als u de service bus relay-naam ruimte niet hebt gemaakt, moet u ervoor zorgen dat de vereiste resource provider is geregistreerd in het abonnement. Als u dit niet hebt geregistreerd, maakt u hand matig een service bus relay-naam ruimte vanuit het Azure Portal. U kunt deze ook maken tijdens [het maken van de hybride verbinding](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) van de Azure Portal.


## <a name="contact-us"></a>Contact opnemen

Neem voor eventuele query's of feedback over de IT Service Management-connector contact met ons op [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com) .

## <a name="next-steps"></a>Volgende stappen
[Voeg ITSM-producten/-services toe aan IT Service Management-connector](../../azure-monitor/platform/itsmc-connections.md).
