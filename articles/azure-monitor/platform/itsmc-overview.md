---
title: IT-servicebeheerconnector in Azure Log Analytics | Microsoft Documenten
description: In dit artikel vindt u een overzicht van DE Service Management Connector (ITSMC) en informatie over het centraal beheren van de ITSM-werkitems in Azure Log Analytics en het snel oplossen van eventuele problemen.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 50bab4c26046059b993c19a030a8f840ae336ef2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274539"
---
# <a name="connect-azure-to-itsm-tools-using-it-service-management-connector"></a>Azure verbinden met ITSM-hulpprogramma's met IT-servicebeheerconnector

![IT-servicebeheerconnectorsymbool](media/itsmc-overview/itsmc-symbol.png)

Met de IT Service Management Connector (ITSMC) u Azure verbinden met een ondersteunde IT Service Management (ITSM) product/service.

Azure-services zoals Log Analytics en Azure Monitor bieden hulpprogramma's voor het detecteren, analyseren en oplossen van problemen met uw Azure- en niet-Azure-bronnen. De werkartikelen die betrekking hebben op een probleem bevinden zich echter meestal in een ITSM-product/-service. De ITSM-connector biedt een bidirectionele verbinding tussen Azure- en ITSM-hulpprogramma's om problemen sneller op te lossen.

ITSMC ondersteunt verbindingen met de volgende ITSM-tools:

-   ServiceNow
-   System Center Service Manager
-   Provance
-   Cherwell

Met ITSMC u:

-  Werkitems maken in itsm-tool op basis van uw Azure-waarschuwingen (metrische waarschuwingen, waarschuwingen voor activiteitenlogboeken en logboekanalyses).
-  Optioneel u uw incident synchroniseren en aanvraaggegevens van uw ITSM-tool wijzigen in een Azure Log Analytics-werkruimte.

Lees meer over de [juridische voorwaarden en het privacybeleid](https://go.microsoft.com/fwLink/?LinkID=522330&clcid=0x9).

U de ITSM-connector gebruiken met de volgende stappen:

1.  [De ITSM-connectoroplossing toevoegen](#adding-the-it-service-management-connector-solution)
2.  Een ITSM-verbinding maken
3.  [De verbinding gebruiken](#using-the-solution)


##  <a name="adding-the-it-service-management-connector-solution"></a>De IT-servicebeheerconnectoroplossing toevoegen

Voordat u een verbinding maken, moet u de ITSM-connectoroplossing toevoegen.

1. Klik in Azure-portal op **+ Pictogram Nieuw.**

   ![Nieuwe Azure-bron](media/itsmc-overview/azure-add-new-resource.png)

2. Zoek naar **IT-servicebeheerconnector** in de Marketplace en klik op **Maken**.

   ![ITSMC-oplossing toevoegen](media/itsmc-overview/add-itsmc-solution.png)

3. Selecteer in de sectie **OMS Workspace** de azure log analytics-werkruimte waar u de oplossing wilt installeren.
   >[!NOTE]
   > * Als onderdeel van de voortdurende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS Workspaces nu log analytics-werkruimten genoemd.
   > * De ITSM-connector kan alleen worden geïnstalleerd in Log Analytics-werkruimten in de volgende regio's: Oost-VS, West-US2, South Central US, West Central US, Centraal-Canada, West-Europa, Zuid-Groot-Brittannië, Zuidoost-Azië, Oost-Japan, Centraal-India, Zuidoost-Australië.

4. Selecteer in de sectie **OMS-werkruimteinstellingen** de resourcegroep waar u de oplossingsbron wilt maken.

   ![ITSMC-werkruimte](media/itsmc-overview/itsmc-solution-workspace.png)
   >[!NOTE]
   >Als onderdeel van de voortdurende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor worden OMS Workspaces nu log analytics-werkruimten genoemd.

5. Klik **op Maken**.

Wanneer de oplossingsbron wordt geïmplementeerd, wordt rechtsboven in het venster een melding weergegeven.


## <a name="creating-an-itsm--connection"></a>Een ITSM-verbinding maken

Zodra u de oplossing hebt geïnstalleerd, u een verbinding maken.

Voor het maken van een verbinding moet u uw ITSM-tool voorbereiden om de verbinding van de ITSM Connector-oplossing mogelijk te maken.  

Afhankelijk van het ITSM-product waaru verbinding mee maakt, gebruikt u de volgende stappen:

- [System Center Service Manager (SCSM)](../../azure-monitor/platform/itsmc-connections.md#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](../../azure-monitor/platform/itsmc-connections.md#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](../../azure-monitor/platform/itsmc-connections.md#connect-provance-to-it-service-management-connector-in-azure)  
- [Cherwell](../../azure-monitor/platform/itsmc-connections.md#connect-cherwell-to-it-service-management-connector-in-azure)

Zodra u uw ITSM-tools hebt voorbereid, volgt u de onderstaande stappen om een verbinding te maken:

1. Ga naar **Alle bronnen,** zoek naar **ServiceDesk(YourWorkspaceName).**
2. Klik **onder WERKRUIMTEGEGEVENSBRONNEN** in het linkerdeelvenster op **ITSM-verbindingen**.
   ![ITSM-verbindingen](media/itsmc-overview/itsm-connections.png)

   Op deze pagina wordt de lijst met verbindingen weergegeven.
3. Klik **op Verbinding toevoegen**.

   ![ITSM-verbinding toevoegen](media/itsmc-overview/add-new-itsm-connection.png)

4. Geef de verbindingsinstellingen op zoals beschreven in [Het configureren van de ITSMC-verbinding met uw ITSM-producten/-servicesartikel](../../azure-monitor/platform/itsmc-connections.md).

   > [!NOTE]
   >
   > ItsMC vernieuwt standaard één keer in de 24 uur de configuratiegegevens van de verbinding. Als u de gegevens van uw verbinding direct wilt vernieuwen voor bewerkingen of sjabloonupdates die u maakt, klikt u op de knop **Synchroniseren** op het blad van uw verbinding.

   ![Verbinding vernieuwen](media/itsmc-overview/itsmc-connections-refresh.png)


## <a name="using-the-solution"></a>De oplossing gebruiken
   Met de ITSM Connector-oplossing u werkitems maken van Azure-waarschuwingen, Logboekanalysewaarschuwingen en Logboekanalyselogboekrecords.

## <a name="create-itsm-work-items-from-azure-alerts"></a>ITSM-werkitems maken op basis van Azure-waarschuwingen

Zodra u uw ITSM-verbinding hebt gemaakt, u werkitem(s) maken in uw ITSM-tool op basis van Azure-waarschuwingen, met behulp van de **ITSM-actie** in **actiegroepen**.

Actiegroepen bieden een modulaire en herbruikbare manier om acties voor uw Azure Alerts te activeren. U actiegroepen gebruiken met metrische waarschuwingen, waarschuwingen voor activiteitenlogboeken en Azure Log Analytics-waarschuwingen in Azure-portal.

Gebruik de volgende procedure:

1. Klik in Azure-portal op **Monitor**.
2. Klik in het linkerdeelvenster op **Actiegroepen**. Het venster **Actiegroep toevoegen** wordt weergegeven.

    ![Actiegroepen](media/itsmc-overview/action-groups.png)

3. Geef **naam** en **shortname** op voor uw actiegroep. Selecteer de **resourcegroep** en **het abonnement** waar u uw actiegroep wilt maken.

    ![Details actiegroepen](media/itsmc-overview/action-groups-details.png)

4. Selecteer **ITSM** in de lijst Acties in het vervolgkeuzemenu **voor Actietype**. Geef een **naam** op voor de actie en klik op **Details bewerken**.
5. Selecteer het **abonnement** op de plaats waar uw Log Analytics-werkruimte zich bevindt. Selecteer de **naam verbinding** (de naam VAN de ITSM-connector) gevolgd door de naam van uw werkruimte. Bijvoorbeeld 'MyITSMMConnector(MyWorkspace).'

    ![ITSM Actie details](media/itsmc-overview/itsm-action-details.png)

6. Selecteer **Werkitemtype** in het vervolgkeuzemenu.
   Kies voor het gebruik van een bestaande sjabloon of vul de velden op die vereist zijn voor uw ITSM-product.
7. Klik op **OK**.

Wanneer u een Azure-waarschuwingsregel maakt/bewerkt, gebruikt u een actiegroep met een ITSM-actie. Wanneer de waarschuwing wordt geactiveerd, wordt het werkitem gemaakt/bijgewerkt in het gereedschap ITSM.

> [!NOTE]
>
> Zie de [prijspagina](https://azure.microsoft.com/pricing/details/monitor/) voor actiegroepen voor informatie over de prijzen van ITSM Action.


## <a name="visualize-and-analyze-the-incident-and-change-request-data"></a>Het incident visualiseren en analyseren en de aanvraaggegevens wijzigen

Op basis van uw configuratie bij het instellen van een verbinding kan itsm-connector tot 120 dagen incident- en wijzigingsgegevens synchroniseren. Het logboekrecordschema voor deze gegevens wordt in de [volgende sectie](#additional-information)vermeld.

De incident- en wijzigingsaanvraaggegevens kunnen worden gevisualiseerd met behulp van het ITSM Connector-dashboard in de oplossing.

![Scherm Log Analytics](media/itsmc-overview/itsmc-overview-sample-log-analytics.png)

Het dashboard biedt ook informatie over de status van de connector die kan worden gebruikt als uitgangspunt om eventuele problemen met de verbindingen te analyseren.

U de incidenten die zijn gesynchroniseerd met de getroffen computers ook visualiseren in de Service Map-oplossing.

Service Map detecteert automatisch de toepassingscomponenten op Windows- en Linux-systemen en brengt de communicatie tussen services in kaart. Zo kunt u uw servers beschouwen zoals u ze voor ogen ziet: als onderling verbonden systemen die kritieke services verlenen. Servicetoewijzing toont verbindingen tussen servers, processen en poorten in alle via TCP verbonden architectuur. U hoeft hiervoor niets meer te doen dan het installeren van een agent. [Meer informatie](../../azure-monitor/insights/service-map.md).

Als u de servicekaartoplossing gebruikt, u de servicedesk-items bekijken die zijn gemaakt in de ITSM-oplossingen, zoals in het volgende voorbeeld wordt weergegeven:

![Scherm Log Analytics](media/itsmc-overview/itsmc-overview-integrated-solutions.png)

Meer informatie: [Servicemap](../../azure-monitor/insights/service-map.md)


## <a name="additional-information"></a>Aanvullende informatie

### <a name="data-synced-from-itsm-product"></a>Gegevens gesynchroniseerd met ITSM-product
Incidenten en wijzigingsaanvragen worden gesynchroniseerd van uw ITSM-product naar uw Log Analytics-werkruimte op basis van de configuratie van de verbinding.

De volgende informatie bevat voorbeelden van gegevens die zijn verzameld door ITSMC:

> [!NOTE]
>
> Afhankelijk van het type werkitem dat is geïmporteerd in Log Analytics, bevat **ServiceDesk_CL** de volgende velden:

**Werkitem:** **Incidenten**  
ServiceDeskWorkItemType_s="Incident"

**Fields**

- ServiceDeskConnectionName
- ServiceDesk-id
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


**Werkitem:** **Aanvragen wijzigen**

ServiceDeskWorkItemType_s="ChangeRequest"

**Fields**
- ServiceDeskConnectionName
- ServiceDesk-id
- Created By
- Gesloten door
- Bron
- Toegewezen aan
- Titel
- Type
- Categorie
- Status
- Escalatie
- Conflictstatus
- Urgentie
- Prioriteit
- Risico
- Impact
- Toegewezen aan
- Gemaakt op
- Datum gesloten
- Datum laatst gewijzigd
- Gevraagde datum
- Geplande begindatum
- Geplande einddatum
- Begindatum voor werk
- Einddatum werk
- Beschrijving
- Computer

## <a name="output-data-for-a-servicenow-incident"></a>Uitvoergegevens voor een ServiceNow-incident

| Veld Logboekanalyse | ServiceNow-veld |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| IncidentState_s | Status |
| Urgency_s |Urgentie |
| Impact_s |Impact|
| Priority_s | Prioriteit |
| CreatedBy_s | Geopend door |
| ResolvedBy_s | Opgelost door|
| ClosedBy_s  | Gesloten door |
| Source_s| Contacttype |
| AssignedTo_s | Toegewezen aan  |
| Category_s | Categorie |
| Title_s|  Korte beschrijving |
| Description_s|  Opmerkingen |
| CreatedDate_t|  Geopend |
| ClosedDate_t| gesloten|
| ResolvedDate_t|Opgelost|
| Computer  | Configuratie-item |

## <a name="output-data-for-a-servicenow-change-request"></a>Uitvoergegevens voor een ServiceNow-wijzigingsaanvraag

| Log Analytics | ServiceNow-veld |
|:--- |:--- |
| ServiceDeskId_s| Aantal |
| CreatedBy_s | Gevraagd door |
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
| RequestedDate_t  | Aangevraagd op datum |
| ClosedDate_t | Gesloten datum |
| PlannedStartDate_t  |     Geplande begindatum |
| PlannedEndDate_t  |   Geplande einddatum |
| WorkStartDate_t  | Werkelijke begindatum |
| WorkEndDate_t | Werkelijke einddatum|
| Description_s | Beschrijving |
| Computer  | Configuratie-item |


## <a name="troubleshoot-itsm-connections"></a>Problemen met ITSM-verbindingen oplossen
1. Als de verbinding mislukt van de gebruikersinterface van verbonden bron met een **fout bij het opslaan van verbindingsberichten,** neemt u de volgende stappen:
   - Voor ServiceNow-, Cherwell- en Provance-verbindingen  
   - ervoor zorgen dat u de gebruikersnaam, het wachtwoord, de client-id en het clientgeheim voor elk van de verbindingen correct hebt ingevoerd.  
   - controleer of u voldoende bevoegdheden hebt in het bijbehorende ITSM-product om de verbinding te maken.  
   - Voor servicebeheerverbindingen  
   - ervoor zorgen dat de web-app is geïmplementeerd en dat een hybride verbinding wordt gemaakt. Als u wilt controleren of de verbinding met de on-premises servicebeheermachine tot stand is gekomen, gaat u naar de URL van de web-app zoals beschreven in de documentatie voor het maken van de [hybride verbinding.](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection)  

2. Als gegevens van ServiceNow niet worden gesynchroniseerd met Log Analytics, moet u ervoor zorgen dat het instantie ServiceNow niet slaapt. ServiceNow Dev Instances gaan soms in de slaapstand als ze gedurende een lange periode niet actief zijn. Anders, meld het probleem.
3. Als Log Analytics brand waarschuwt maar werkitems niet worden gemaakt in ITSM-product- of configuratieartikelen, worden ze niet gemaakt/gekoppeld aan werkitems of naar andere algemene informatie, kijk dan op de volgende plaatsen:
   -  ITSMC: De oplossing toont een samenvatting van verbindingen/werkitems/computers enz. Klik op de tegel met **connectorstatus**, waarmee u zoeken met de relevante query **registreren.** Kijk naar de logboekrecords met LogType_S als FOUT voor meer informatie.
   - **Pagina Zoeken in** logboek: bekijk de fouten/gerelateerde informatie rechtstreeks met behulp van de query `*`ServiceDeskLog_CL`*`.

## <a name="troubleshoot-service-manager-web-app-deployment"></a>Problemen oplossen met de implementatie van Service Manager Web App
1.  Als u problemen hebt met de implementatie van webapps, moet u ervoor zorgen dat u over voldoende machtigingen beschikt in het genoemde abonnement om resources te maken/implementeren.
2.  Als u een fout **'Objectverwijzing niet instellen op instantie van een object'** krijgt wanneer u het [script](itsmc-service-manager-script.md)uitvoert, moet u ervoor zorgen dat u geldige waarden hebt ingevoerd onder sectie **Gebruikersconfiguratie.**
3.  Als u de naamruimte voor servicebusrelais niet maakt, moet u ervoor zorgen dat de vereiste resourceprovider is geregistreerd in het abonnement. Als deze niet is geregistreerd, maakt u handmatig de naamruimte voor servicebusrelais van de Azure-portal. U deze ook maken tijdens het [maken van de hybride verbinding](../../azure-monitor/platform/itsmc-connections.md#configure-the-hybrid-connection) vanuit de Azure-portal.


## <a name="contact-us"></a>Contact opnemen

Voor vragen of feedback over de IT Service [omsitsmfeedback@microsoft.com](mailto:omsitsmfeedback@microsoft.com)Management Connector u contact met ons opnemen op.

## <a name="next-steps"></a>Volgende stappen
[VOEG ITSM-producten/-services toe aan IT Service Management Connector](../../azure-monitor/platform/itsmc-connections.md).
