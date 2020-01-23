---
title: Configuration Manager verbinding maken met Azure Monitor | Microsoft Docs
description: In dit artikel worden de stappen beschreven om Configuration Manager te verbinden met de werk ruimte in Azure Monitor en om gegevens te analyseren.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 5b5af034b116ec1cdcefc811630683c9f560c840
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513648"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager verbinden met Azure Monitor
U kunt uw micro soft endpoint Configuration Manager-omgeving verbinden met Azure Monitor om gegevens van de apparaatgegevens te synchroniseren en naar deze verzamelingen te verwijzen in Azure Monitor en Azure Automation.  

## <a name="prerequisites"></a>Vereisten

Azure Monitor ondersteunt Configuration Manager huidige vertakking versie 1606 en hoger.

>[!NOTE]
>De functie voor het verbinden van Configuration Manager met een Log Analytics-werk ruimte is optioneel en is niet standaard ingeschakeld. U moet deze functie inschakelen voordat u deze kunt gebruiken. Zie voor meer informatie [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Configuratieoverzicht

In de volgende stappen vindt u een overzicht van de stappen voor het configureren van Configuration Manager integratie met Azure Monitor.  

1. Registreer Configuration Manager in Azure Active Directory als webtoepassing en/of Web-API-app en zorg ervoor dat u beschikt over de client-ID en de geheime sleutel van de client van de registratie van Azure Active Directory. Zie [portal gebruiken voor het maken van Active Directory toepassing en service-principal die toegang hebben tot resources](../../active-directory/develop/howto-create-service-principal-portal.md) voor gedetailleerde informatie over het uitvoeren van deze stap.

2. Ken in Azure Active Directory [Configuration Manager toe (de geregistreerde Web-app) met machtigingen voor toegang tot Azure monitor](#grant-configuration-manager-with-permissions-to-log-analytics).

3. Voeg in Configuration Manager een verbinding toe met behulp van de wizard **Azure-Services** .

4. [Down load en installeer de log Analytics-agent voor Windows](#download-and-install-the-agent) op de computer met de site systeemrol Configuration Manager service aansluitpunt. De agent verzendt Configuration Manager gegevens naar de werk ruimte Log Analytics in Azure Monitor.

5. Importeer in Azure Monitor [verzamelingen uit Configuration Manager](#import-collections) als computer groepen.

6. In Azure Monitor gegevens weer geven van Configuration Manager als [computer groepen](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Verleen Configuration Manager met machtigingen voor Log Analytics

In de volgende procedure, verleent u de *Inzender* rol in uw Log Analytics-werkruimte aan de Active Directory-toepassing en service-principal die u eerder hebt gemaakt voor Configuration Manager. Als u nog geen werk ruimte hebt, raadpleegt u [een werk ruimte maken in azure monitor](../../azure-monitor/learn/quick-create-workspace.md) voordat u doorgaat. Hiermee kunt Configuration Manager om te verifiëren en verbinding maken met uw Log Analytics-werkruimte.  

> [!NOTE]
> U moet machtigingen opgeven in de werk ruimte Log Analytics voor Configuration Manager. Anders ontvangt u een foutbericht weergegeven wanneer u de configuratiewizard in Configuration Manager gebruiken.
>

1. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics**.

2. In de lijst met Log Analytics-werkruimten, selecteer de werkruimte te wijzigen.

3. Selecteer in het linkerdeelvenster **toegangsbeheer (IAM)** .

4. Klik in de Access control (IAM)-pagina op **roltoewijzing toevoegen** en de **roltoewijzing toevoegen** deelvenster wordt weergegeven.

5. In de **roltoewijzing toevoegen** deelvenster onder de **rol** vervolgkeuzelijst, selecteer de **Inzender** rol.  

6. Onder de **toegang toewijzen aan** vervolgkeuzelijst, selecteer de Configuration Manager-toepassing eerder hebt gemaakt in AD, en klik vervolgens op **OK**.  

## <a name="download-and-install-the-agent"></a>De agent downloaden en installeren

Raadpleeg het artikel [Windows-computers verbinden met Azure monitor in azure](agent-windows.md) om inzicht te krijgen in de methoden die beschikbaar zijn voor het installeren van de log Analytics agent voor Windows op de computer die als host fungeert voor de site systeemrol Configuration Manager service aansluitpunt.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuration Manager verbinden met Log Analytics werk ruimte

>[!NOTE]
> Om een Log Analytics verbinding toe te voegen, moet uw Configuration Manager omgeving een [service verbindings punt](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) hebben dat is geconfigureerd voor de online modus.

> [!NOTE]
> U moet de site op het hoogste niveau in uw hiërarchie verbinden met Azure Monitor. Als u een zelfstandige primaire site verbindt met Azure Monitor en vervolgens een centrale beheer site toevoegt aan uw omgeving, moet u de verbinding in de nieuwe hiërarchie verwijderen en opnieuw maken.

1. Selecteer **Cloud Services** in de werk ruimte **beheer** van Configuration Manager en selecteer vervolgens **Azure-Services**. 

2. Klik met de rechter muisknop op **Azure-Services** en selecteer vervolgens **Azure-Services configureren**. De pagina **Azure-Services configureren** wordt weer gegeven. 
   
3. Op de **algemene** scherm, bevestigt u dat u de volgende acties hebt uitgevoerd en dat u details voor elk item hebben, en selecteer vervolgens **volgende**.

4. Op de pagina Azure-Services van de wizard Azure-Services:

    1. Geef een **naam** op voor het object in Configuration Manager.
    2. Geef een optionele **Beschrijving** op die u kan helpen bij het identificeren van de service.
    3. Selecteer de Azure service **OMS-connector**.

    >[!NOTE]
    >OMS wordt nu Log Analytics genoemd. Dit is een functie van Azure Monitor.

5. Selecteer **volgende** om door te gaan naar de pagina met eigenschappen van Azure app van de wizard Azure-Services.

6. Selecteer op de pagina **app** van de wizard Azure-Services eerst de Azure-omgeving in de lijst en klik vervolgens op **importeren**.

7. Geef op de pagina **apps importeren** de volgende informatie op:

    1. Geef de **Azure AD-Tenant naam** voor de app op.

    2. Geef een **Azure AD-Tenant-id** op voor de Azure AD-Tenant. U kunt deze informatie vinden op de pagina met Azure Active Directory **Eigenschappen** . 

    3. Geef de naam van de toepassing op voor de **toepassings** naam.

    4. Geef voor **client-id**de toepassings-id op van de gemaakte Azure AD-app die u eerder hebt gemaakt.

    5. Geef voor **geheime sleutel**de geheime sleutel van de client op van de Azure AD-app die u hebt gemaakt.

    6. Geef de verval datum van de sleutel op voor het verlopen van een **geheime sleutel**.

    7. Geef de App-ID-URI op van de **URI**van de app die u eerder hebt gemaakt.

    8. Selecteer **controleren** en rechts de resultaten moeten **zijn geverifieerd**.

8. Controleer op de pagina **configuratie** de informatie voor het controleren van de velden **Azure-abonnementen**, **Azure-resource groep**en **Operations Management Suite-werk ruimte** , die aangeven dat de Azure AD-toepassing voldoende machtigingen heeft in de resource groep. Als de velden leeg zijn, duidt dit erop dat uw toepassing niet over de vereiste rechten beschikt. Selecteer de apparaat-verzamelingen die u wilt verzamelen en door sturen naar de werk ruimte en selecteer vervolgens **toevoegen**.

9. Bekijk de opties op de pagina **Bevestig de instellingen** en selecteer **volgende** om te beginnen met het maken en configureren van de verbinding.

10. Wanneer de configuratie is voltooid, wordt de pagina **voltooiing** weer gegeven. Selecteer **Sluiten**. 

Nadat u Configuration Manager aan Azure Monitor hebt gekoppeld, kunt u verzamelingen toevoegen of verwijderen en de eigenschappen van de verbinding weer geven.

## <a name="update-log-analytics-workspace-connection-properties"></a>Eigenschappen van de verbinding voor Log Analytics werk ruimte bijwerken

Als een wacht woord of geheime sleutel van de client verloopt of verloren is gegaan, moet u de Log Analytics verbindings eigenschappen hand matig bijwerken.

1. Selecteer in de werk ruimte **beheer** van Configuration Manager **Cloud Services** en selecteer vervolgens **OMS-connector** om de **Eigenschappen** pagina van OMS-verbinding te openen.
2. Op deze pagina, klikt u op de **Azure Active Directory** tabblad om uw **Tenant**, **Client-ID**, **Client geheime sleutel verloopt**. **Controleer of** uw **geheime sleutel van de Client** als deze is verlopen.

## <a name="import-collections"></a>Verzamelingen importeren

Nadat u een Log Analytics verbinding hebt toegevoegd met Configuration Manager en de agent hebt geïnstalleerd op de computer met de site systeemrol Configuration Manager service aansluitpunt, is de volgende stap het importeren van verzamelingen uit Configuration Manager in azure Bewaken als computer groepen.

Nadat u de initiële configuratie voor het importeren van Apparaatsets vanuit uw hiërarchie hebt voltooid, worden de verzamelings gegevens om de drie uur opgehaald om het lidmaatschap actueel te laten blijven. U kunt dit op elk moment uitschakelen.

1. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **log Analytics-werk ruimten**.
2. Selecteer in de lijst met Log Analytics-werkruimten, de Configuration Manager is geregistreerd bij de werkruimte.  
3. Selecteer **Geavanceerde instellingen**.
4. Selecteer **computergroepen** en selecteer vervolgens **SCCM**.  
5. Selecteer **Import Configuration Manager-verzamelingslidmaatschappen** en klik vervolgens op **opslaan**.  
   
    ![Computergroepen - SCCM-tabblad](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Gegevens weergeven uit Configuration Manager

Nadat u een Log Analytics verbinding hebt toegevoegd aan Configuration Manager en de agent hebt geïnstalleerd op de computer met de site systeemrol Configuration Manager service aansluitpunt, worden de gegevens van de agent verzonden naar de werk ruimte Log Analytics in Azure Monitor. In Azure Monitor worden uw Configuration Manager verzamelingen weer gegeven als [computer groepen](../../azure-monitor/platform/computer-groups.md). U vindt de groepen van de **Configuration Manager** pagina onder **Settings\Computer groepen**.

Nadat de verzamelingen die zijn geïmporteerd, kunt u zien hoeveel computers met verzamelingslidmaatschappen gedetecteerd. U ziet ook het aantal verzamelingen die zijn geïmporteerd.

![Computergroepen - SCCM-tabblad](./media/collect-sccm/sccm-computer-groups02.png)

Wanneer u op een van beide klikt, worden alle geïmporteerde groepen of alle computers die deel uitmaken van elke groep weer gegeven. Met [Zoeken in Logboeken](../../azure-monitor/log-query/log-query-overview.md)kunt u verdere uitgebreide analyse van de gegevens van het lidmaatschap van de verzameling uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Gebruik [zoeken in logboeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie over uw Configuration Manager-gegevens weer te geven.
