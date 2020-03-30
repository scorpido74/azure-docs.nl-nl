---
title: Azure Monitor voor VM's-kaart integreren met Operations Manager | Microsoft Documenten
description: Azure Monitor voor VM's detecteert automatisch toepassingsonderdelen op Windows- en Linux-systemen en brengt de communicatie tussen services in kaart. In dit artikel wordt besproken dat u de functie Kaart gebruikt om automatisch gedistribueerde toepassingsdiagrammen te maken in Operations Manager.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: 112413720f969474369555a74bc89846666e2ef9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77663450"
---
# <a name="integrate-system-center-operations-manager-with-azure-monitor-for-vms-map-feature"></a>System Center Operations Manager integreren met azure-monitor voor vms-kaartfunctie

In Azure Monitor voor VM's u gedetecteerde toepassingsonderdelen weergeven op virtuele Windows- en Linux-virtuele machines (VM's) die worden uitgevoerd in Azure of uw omgeving. Met deze integratie tussen de mapfunctie en System Center Operations Manager u automatisch gedistribueerde toepassingsdiagrammen maken in Operations Manager die zijn gebaseerd op de dynamische afhankelijkheidskaarten in Azure Monitor voor VM's. In dit artikel wordt beschreven hoe u de beheergroep Van Operations Manager van het Systeemcentrum configureert om deze functie te ondersteunen.

>[!NOTE]
>Als u ServiceMap al hebt geïmplementeerd, u uw kaarten bekijken in Azure Monitor voor VM's, die extra functies bevatten om de VM-status en -prestaties te controleren. De mapfunctie van Azure Monitor voor VM's is bedoeld om de zelfstandige Service Map-oplossing te vervangen. Zie overzicht [van Azure Monitor voor VM's voor](vminsights-overview.md)meer informatie.

## <a name="prerequisites"></a>Vereisten

* Een beheergroep van System Center Operations Manager (2012 R2 of hoger).
* Een Log Analytics-werkruimte die is geconfigureerd om Azure Monitor voor VM's te ondersteunen.
* Een of meer virtuele Windows- en Linux-machines of fysieke computers die door Operations Manager worden bewaakt en gegevens naar uw Log Analytics-werkruimte verzenden. Linux-servers die worden gemeld aan een Operations Manager-beheergroep, moeten worden geconfigureerd om rechtstreeks verbinding te maken met Azure Monitor. Bekijk het overzicht in [Loggegevens verzamelen met de loganalyse-agent](../platform/log-analytics-agent.md)voor meer informatie.
* Een serviceprincipal met toegang tot het Azure-abonnement dat is gekoppeld aan de werkruimte Log Analytics. Ga voor meer informatie naar [Een serviceprincipal maken.](#create-a-service-principal)

## <a name="install-the-service-map-management-pack"></a>Het beheerpakket servicekaart installeren

U schakelt de integratie tussen Operations Manager en de mapfunctie in door de Microsoft.SystemCenter.ServiceMap-beheerpakketbundel (Microsoft.SystemCenter.ServiceMap.mpb) te importeren. U de managementpackbundel downloaden vanuit het [Microsoft Download Center.](https://www.microsoft.com/download/details.aspx?id=55763) De bundel bevat de volgende managementpacks:

* Weergave van microsoft-servicekaarttoepassingen
* Microsoft System Center Service Map Intern
* Overschrijvingen van Microsoft System Center-servicetoewijzingen
* Servicekaart van Microsoft System Center

## <a name="configure-integration"></a>Integratie configureren

Nadat u het beheerpakket servicekaarten hebt geïnstalleerd, wordt een nieuw knooppunt, **Servicemap,** weergegeven onder **Operations Management Suite** in het **beheervenster** van uw Operations Operations-console.

>[!NOTE]
>[Operations Management Suite is een verzameling services](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) die Log Analytics omvatte, en maakt nu deel uit van Azure [Monitor.](../overview.md)

Ga als volgt te werk om Azure Monitor voor vms-kaartintegratie te configureren:

1. Als u de wizard Configuratie wilt openen, klikt u in het deelvenster **Overzicht van servicekaarten** op **Werkruimte toevoegen**.  

    ![Overzichtsvenster Servicemap](media/service-map-scom/scom-configuration.png)

2. Voer in het venster **Verbindingsconfiguratie** de tenantnaam of -id, toepassings-id (ook wel gebruikersnaam of clientID genoemd) en wachtwoord van de serviceprincipal in en klik op **Volgende**. Ga voor meer informatie naar Een serviceprincipal maken.

    ![Het venster Verbindingsconfiguratie](media/service-map-scom/scom-config-spn.png)

3. Selecteer **in** het venster Selectie van abonnementen het Azure-abonnement, de Azure-brongroep (de werkruimte log-analyse) en de werkruimte Log Analytics en klik op **Volgende**.

    ![De configuratiewerkruimte operations manager](media/service-map-scom/scom-config-workspace.png)

4. In het venster **Machinegroepselectie** kiest u welke machinegroepen voor servicetoewijzingen u wilt synchroniseren met Operations Manager. Klik **op Machinegroepen toevoegen/verwijderen,** kies groepen in de lijst **met beschikbare machinegroepen**en klik op **Toevoegen**.  Wanneer u klaar bent met het selecteren van groepen, klikt u op **Ok** om te voltooien.

    ![De configuratiemachinegroepen operations manager](media/service-map-scom/scom-config-machine-groups.png)

5. In het venster **Serverselectie** configureert u de groep Servicekaartservers met de servers die u wilt synchroniseren tussen Operations Manager en de functie Kaart. Klik **op Servers toevoegen/verwijderen**.

    Voor de integratie om een gedistribueerd toepassingsdiagram voor een server te maken, moet de server:

   * Gecontroleerd door Operations Manager
   * Geconfigureerd om te rapporteren aan de Werkruimte Log Analytics die is geconfigureerd met Azure Monitor voor VM's
   * Vermeld in de groep Servicemapservers

     ![De configuratiegroep Operations Manager](media/service-map-scom/scom-config-group.png)

6. Optioneel: selecteer de resourcegroep Alle beheerservers om te communiceren met Logboekanalyse en klik op **Werkruimte toevoegen**.

    ![De groep Operations Manager Configuration Resource](media/service-map-scom/scom-config-pool.png)

    Het kan even duren voordat u de werkruimte Log Analytics configureert en registreert. Nadat deze is geconfigureerd, initieert Operations Manager de eerste kaartsynchronisatie.

    ![De groep Operations Manager Configuration Resource](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integratie controleren

Nadat de werkruimte Log Analytics is verbonden, wordt een nieuwe map, Servicemap, weergegeven in het **deelvenster Controle** van de Operations Operations-console van Operations.

![Het deelvenster Operations Manager-controle](media/service-map-scom/scom-monitoring.png)

De map Servicemap heeft vier knooppunten:

* **Actieve waarschuwingen:** geeft een overzicht van alle actieve waarschuwingen over de communicatie tussen Operations Manager en Azure Monitor.  

  >[!NOTE]
  >Deze waarschuwingen zijn geen Log Analytics-waarschuwingen die zijn gesynchroniseerd met Operations Manager, ze worden gegenereerd in de beheergroep op basis van workflows die zijn gedefinieerd in het beheerpakket servicekaart.

* **Servers:** geeft een overzicht van de bewaakte servers die zijn geconfigureerd om te synchroniseren vanuit de functie Azure Monitor for VM's Map.

    ![Het deelvenster Operations Manager-bewakingsservers](media/service-map-scom/scom-monitoring-servers.png)

* **Afhankelijkheidsweergaven voor machinegroepen:** geeft een overzicht van alle machinegroepen die zijn gesynchroniseerd vanuit de functie Kaart. U op een groep klikken om het gedistribueerde toepassingsdiagram weer te geven.

    ![Het toepassingsdiagram operations manager gedistribueerd](media/service-map-scom/scom-group-dad.png)

* **Serverafhankelijkheidsweergaven:** geeft een overzicht van alle servers die zijn gesynchroniseerd vanuit de functie Kaart. U op elke server klikken om het gedistribueerde toepassingsdiagram weer te geven.

    ![Het toepassingsdiagram operations manager gedistribueerd](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>De werkruimte bewerken of verwijderen

U de geconfigureerde werkruimte bewerken of verwijderen via het deelvenster **Overzicht van servicekaarten** **(beheervenster** > **Operations Management Suite** > **Service Map).**

>[!NOTE]
>[Operations Management Suite was een verzameling services](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand) met Log Analytics, die nu onderdeel is van Azure [Monitor.](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/overview.md)

U in deze huidige versie slechts één Log Analytics-werkruimte configureren.

![Het deelvenster Werkruimte bewerken van Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Regels en overschrijvingen configureren

Een regel, *Microsoft.SystemCenter.ServiceMapImport.Rule*, haalt periodiek informatie op uit de functie Azure Monitor for VMs Map. Als u het synchronisatie-interval wilt wijzigen, u de regel overschrijven en de waarde voor de parameter **IntervalMinutes**wijzigen.

![Het venster Operations Manager overschrijft eigenschappen](media/service-map-scom/scom-overrides.png)

* **Ingeschakeld**: Automatische updates inschakelen of uitschakelen.
* **IntervalMinutes:** hiermee geeft u de tijd tussen updates op. Het standaardinterval is een uur. Als u kaarten vaker wilt synchroniseren, u de waarde wijzigen.
* **Time-outSeconden:** geeft de tijdsduur op voordat de aanvraag een time-out heeft.
* **TimeWindowMinutes:** hiermee geeft u het tijdvenster op voor het opvragen van gegevens. De standaardinstelling is 60 minuten, wat het maximaal toegestane interval is.

## <a name="known-issues-and-limitations"></a>Bekende problemen en beperkingen

Het huidige ontwerp presenteert de volgende problemen en beperkingen:

* U slechts verbinding maken met één Log Analytics-werkruimte.
* Hoewel u servers handmatig toevoegen aan de groep Servicemapservers via het deelvenster **Ontwerpen,** worden de kaarten voor die servers niet onmiddellijk gesynchroniseerd. Ze worden tijdens de volgende synchronisatiecyclus gesynchroniseerd vanuit de functie Azure Monitor for VMs Map.
* Als u wijzigingen aanbrengt in de gedistribueerde toepassingsdiagrammen die door het beheerpakket zijn gemaakt, worden deze wijzigingen waarschijnlijk overschreven op de volgende synchronisatie met Azure Monitor voor VM's.

## <a name="create-a-service-principal"></a>Een service-principal maken

Zie voor officiële Azure-documentatie over het maken van een serviceprincipal:

* [Een serviceprincipal maken met PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)
* [Een serviceprincipal maken met Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal-cli)
* [Een serviceprincipal maken met behulp van de Azure-portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)

### <a name="feedback"></a>Feedback
Heeft u feedback voor ons over integratie met de functie Azure Monitor for VMs Map of deze documentatie? Ga naar onze [user voice-pagina,](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)waar u functies voorstellen of stemmen op bestaande suggesties.
