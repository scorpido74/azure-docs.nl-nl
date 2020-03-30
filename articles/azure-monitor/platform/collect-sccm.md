---
title: Configuratiebeheer verbinden met Azure-monitor | Microsoft Documenten
description: In dit artikel worden de stappen weergegeven om Configuration Manager te verbinden met werkruimte in Azure Monitor en gegevens te analyseren.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/28/2019
ms.openlocfilehash: 3140c0de6fbe090e3d040202cd581c455f03b6d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655253"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuratiebeheer verbinden met Azure-monitor
U uw Microsoft Endpoint Configuration Manager-omgeving verbinden met Azure Monitor om gegevens over het verzamelen van apparaten te synchroniseren en deze verzamelingen te beheren in Azure Monitor en Azure Automation.  

## <a name="prerequisites"></a>Vereisten

Azure Monitor ondersteunt Configuration Manager huidige branch, versie 1606 en hoger.

>[!NOTE]
>De functie om Configuration Manager te verbinden met een Log Analytics-werkruimte is optioneel en standaard niet ingeschakeld. U moet deze functie inschakelen voordat u deze gebruikt. Zie voor meer informatie [Enable optional features from updates](https://docs.microsoft.com/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Configuratieoverzicht

In de volgende stappen worden de stappen samengevat om de integratie van Configuration Manager met Azure Monitor te configureren.  

1. Registreer in Azure Active Directory Configuration Manager als webtoepassing en/of web-API-app en zorg ervoor dat u de client-id en de clientgeheime sleutel uit de registratie van Azure Active Directory hebt. Zie [Portal gebruiken om Active Directory-toepassing en serviceprincipal te maken die toegang hebben tot bronnen](../../active-directory/develop/howto-create-service-principal-portal.md) voor gedetailleerde informatie over hoe u deze stap uitvoeren.

2. [Geef configuration manager (de geregistreerde web-app) in](#grant-configuration-manager-with-permissions-to-log-analytics)Azure Active Directory toestemming om toegang te krijgen tot Azure Monitor.

3. Voeg in Configuratiebeheer een verbinding toe met de wizard **Azure Services.**

4. [Download en installeer de loganalyse-agent voor Windows](#download-and-install-the-agent) op de computer waarop de functie van het configuratiebeheerserviceverbindingspuntsitesysteem wordt uitgevoerd. De agent stuurt Configuratiebeheergegevens naar de werkruimte Log Analytics in Azure Monitor.

5. Importeer in Azure Monitor [verzamelingen uit Configuration Manager](#import-collections) als computergroepen.

6. Bekijk in Azure Monitor gegevens van Configuration Manager als [computergroepen](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Configuratiebeheer verlenen met machtigingen voor Logboekanalyse

In de volgende procedure verleent u de functie *Inzender* in uw werkruimte Log Analytics aan de AD-toepassings- en serviceprincipal die u eerder hebt gemaakt voor Configuration Manager. Zie Een werkruimte maken in [Azure Monitor](../../azure-monitor/learn/quick-create-workspace.md) voordat u verdergaat als u nog geen werkruimte hebt. Hierdoor kan Configuration Manager zich verifiëren en verbinding maken met uw Log Analytics-werkruimte.  

> [!NOTE]
> U moet machtigingen opgeven in de werkruimte Log Analytics voor Configuratiebeheer. Anders ontvangt u een foutbericht wanneer u de configuratiewizard gebruikt in Configuratiebeheer.
>

1. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics**.

2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte die u wilt wijzigen.

3. Selecteer in het linkerdeelvenster **het toegangsbesturingselement (IAM).**

4. Klik op de pagina Toegangsbesturingselement (IAM) op **Roltoewijzing toevoegen** en het deelvenster **Roltoewijzing toevoegen** wordt weergegeven.

5. Selecteer in het deelvenster **Roltoewijzing toevoegen** onder de vervolgkeuzelijst **Rol** bijdragen de rol **Inzender.**  

6. Selecteer **onder** de vervolgkeuzelijst Toegang toewijzen tot de vervolgkeuzelijst De toepassing Configuratiebeheer die eerder in AD is gemaakt en klik op **OK**.  

## <a name="download-and-install-the-agent"></a>De agent downloaden en installeren

Bekijk het artikel [Windows-computers verbinden met Azure Monitor in Azure](agent-windows.md) om inzicht te krijgen in de beschikbare methoden voor het installeren van de loganalytics-agent voor Windows op de computer die de functie van het configuratiebeheerserviceverbindingspuntsitesysteem host.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuratiebeheer verbinden met de werkruimte Log Analytics

>[!NOTE]
> Als u een Logboekanalyse-verbinding wilt toevoegen, moet in uw configuratiebeheeromgeving een [serviceverbindingspunt](https://docs.microsoft.com/configmgr/core/servers/deploy/configure/about-the-service-connection-point) zijn geconfigureerd voor de onlinemodus.

> [!NOTE]
> U moet de site van de bovenste laag in uw hiërarchie verbinden met Azure Monitor. Als u een zelfstandige primaire site aansluit op Azure Monitor en vervolgens een centrale beheersite aan uw omgeving toevoegt, moet u de verbinding binnen de nieuwe hiërarchie verwijderen en opnieuw maken.

1. Selecteer **clouds** **services** in de werkruimte Beheer van Configuration Manager en selecteer **Azure Services**. 

2. Klik met de rechtermuisknop op **Azure Services** en selecteer Azure **Services configureren**. De pagina **Azure Services configureren** wordt weergegeven. 
   
3. Controleer in het scherm **Algemeen** of u de volgende acties hebt uitgevoerd en dat u details voor elk item hebt en selecteer **Volgende**.

4. Ga als op de pagina Azure Services van de wizard Azure Services:

    1. Geef een **naam** op voor het object in Configuration Manager.
    2. Geef een optionele **beschrijving op** om u te helpen de service te identificeren.
    3. Selecteer de **Azure-service OMS-connector**.

    >[!NOTE]
    >OMS wordt nu log analytics genoemd, wat een functie is van Azure Monitor.

5. Selecteer **Volgende** om door te gaan naar de pagina Eigenschappen van Azure-apps van de wizard Azure Services.

6. Selecteer op de **app-pagina** van de wizard Azure Services eerst de Azure-omgeving in de lijst en klik vervolgens op **Importeren**.

7. Geef op de pagina **Apps importeren** de volgende gegevens op:

    1. Geef de **Azure AD-tenantnaam** voor de app op.

    2. Geef voor **Azure AD-tenantid** de Azure AD-tenant op. U deze informatie vinden op de pagina Azure Active Directory **Properties.** 

    3. Geef voor **Toepassingsnaam** de toepassingsnaam op.

    4. Geef voor **client-id**de toepassings-id op van de eerder gemaakte Azure AD-app.

    5. Geef op **voor De sleutel Geheim**, de geheime clientsleutel van de gemaakte Azure AD-app.

    6. Geef op voor **het verlopen van**de geheime sleutel , de vervaldatum van uw sleutel.

    7. Geef op voor **App ID URI**, de App ID URI van de eerder gemaakte Azure AD-app.

    8. Selecteer **Verifiëren** en rechts moeten de resultaten **worden weergegeven met succes geverifieerd!**.

8. Controleer op de pagina **Configuratie** de gegevens om de **Azure-abonnementen,** **Azure-brongroep**en **Operations Management Suite-werkruimtevelden** te verifiëren, vooraf ingevuld, wat aangeeft dat de Azure AD-toepassing voldoende machtigingen heeft in de brongroep. Als de velden leeg zijn, geeft dit aan dat uw toepassing niet over de vereiste rechten beschikt. Selecteer de apparaatverzamelingen die u wilt verzamelen en doorsturen naar de werkruimte en selecteer **Vervolgens Toevoegen**.

9. Controleer de opties op de pagina **De instellingen bevestigen** en selecteer **Volgende** om te beginnen met het maken en configureren van de verbinding.

10. Wanneer de configuratie is voltooid, wordt de pagina **Voltooiing** weergegeven. Selecteer **Sluiten**. 

Nadat u Configuration Manager aan Azure Monitor hebt gekoppeld, u verzamelingen toevoegen of verwijderen en de eigenschappen van de verbinding weergeven.

## <a name="update-log-analytics-workspace-connection-properties"></a>Eigenschappen van de verbindingseigenschappen van Log Analytics-werkruimte bijwerken

Als een wachtwoord of clientgeheime sleutel verloopt of verloren gaat, moet u de eigenschappen van de Log Analytics-verbinding handmatig bijwerken.

1. Selecteer **in** de werkruimte Beheer van Configuration Manager **CloudServices** en selecteer **vervolgens OMS-connector** om de pagina **OMS-verbindingseigenschappen** te openen.
2. Klik op deze pagina op het tabblad **Azure Active Directory** om de vervaldatum van uw **tenant**, **client-id**, **clientgeheime sleutel**weer te geven. **Controleer** de **geheime sleutel van** uw client als deze is verlopen.

## <a name="import-collections"></a>Verzamelingen importeren

Nadat u een Logboekanalyseverbinding hebt toegevoegd aan Configuration Manager en de agent op de computer hebt geïnstalleerd waarop de functie van het configuratiebeheerverbindingspuntsitesysteem wordt uitgevoerd, is de volgende stap het importeren van verzamelingen uit Configuration Manager in Azure Monitor als computergroepen.

Nadat u de eerste configuratie hebt voltooid om apparaatverzamelingen uit uw hiërarchie te importeren, wordt de verzamelingsgegevens elke 3 uur opgehaald om het lidmaatschap actueel te houden. U ervoor kiezen om dit op elk gewenst moment uit te schakelen.

1. Klik in Azure Portal in de linkerbovenhoek op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Logboekanalysewerkruimten**.
2. Selecteer in uw lijst met Log Analytics-werkruimten de werkruimte waarmee Configuratiebeheer is geregistreerd.  
3. Selecteer **Geavanceerde instellingen**.
4. Selecteer **Computergroepen** en selecteer **vervolgens SCCM**.  
5. Selecteer **Lidmaatschappen van de verzameling voor configuratiebeheer importeren** en klik op **Opslaan**.  
   
    ![Computergroepen - tabblad SCCM](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Gegevens van Configuratiebeheer weergeven

Nadat u een Logboekanalyseverbinding hebt toegevoegd aan Configuration Manager en de agent op de computer hebt geïnstalleerd waarop de sitesysteemrol configuration manager service connection point-site wordt uitgevoerd, worden gegevens van de agent verzonden naar de werkruimte Log Analytics in Azure Monitor. In Azure Monitor worden uw configuratiebeheerverzamelingen weergegeven als [computergroepen](../../azure-monitor/platform/computer-groups.md). U de groepen bekijken vanaf de pagina **Configuratiebeheer** onder **Instellingen\Computergroepen**.

Nadat de verzamelingen zijn geïmporteerd, u zien hoeveel computers met verzamelingslidmaatschappen zijn gedetecteerd. U ook het aantal verzamelingen zien dat is geïmporteerd.

![Computergroepen - tabblad SCCM](./media/collect-sccm/sccm-computer-groups02.png)

Wanneer u op een van beide klikt, wordt de logboekqueryeditor geopend waarin alle geïmporteerde groepen of alle computers worden weergegeven die tot elke groep behoren. Met Behulp van [Log Search](../../azure-monitor/log-query/log-query-overview.md)u de verzamelingslidmaatschapsgegevens verder diepgaand analyseren.

## <a name="next-steps"></a>Volgende stappen

Gebruik [Logboekzoeken](../../azure-monitor/log-query/log-query-overview.md) om gedetailleerde informatie over uw configuratiebeheergegevens weer te geven.
