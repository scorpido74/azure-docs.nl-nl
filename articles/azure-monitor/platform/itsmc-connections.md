---
title: IT-servicebeheerconnector in Azure-monitor
description: In dit artikel vindt u informatie over het centraal koppelen van uw ITSM-producten/-services met de IT Service Management Connector (ITSMC) in Azure Monitor om de ITSM-werkitems centraal te controleren en te beheren.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 05/24/2018
ms.openlocfilehash: 0773492c3042a6f8c906aa6ba1bc3c76ea8c0d8f
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870594"
---
# <a name="connect-itsm-productsservices-with-it-service-management-connector"></a>ITSM-producten/-services verbinden met IT-servicebeheerconnector
In dit artikel vindt u informatie over het configureren van de verbinding tussen uw ITSM-product/-service en de IT Service Management Connector (ITSMC) in Log Analytics om uw werkitems centraal te beheren. Zie [Overzicht](../../azure-monitor/platform/itsmc-overview.md)voor meer informatie over ITSMC.

De volgende ITSM-producten/-diensten worden ondersteund. Selecteer het product om gedetailleerde informatie te bekijken over hoe u het product met ITSMC verbinden.

- [System Center Service Manager](#connect-system-center-service-manager-to-it-service-management-connector-in-azure)
- [ServiceNow](#connect-servicenow-to-it-service-management-connector-in-azure)
- [Provance](#connect-provance-to-it-service-management-connector-in-azure)
- [Cherwell](#connect-cherwell-to-it-service-management-connector-in-azure)

> [!NOTE]
> 
> ITSM Connector kan alleen verbinding maken met cloudgebaseerde ServiceNow-exemplaren. On-premises ServiceNow-exemplaren worden momenteel niet ondersteund.

## <a name="connect-system-center-service-manager-to-it-service-management-connector-in-azure"></a>System Center Service Manager verbinden met IT-servicebeheerconnector in Azure

In de volgende secties vindt u informatie over het aansluiten van uw System Center Service Manager-product op ITSMC in Azure.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende voorwaarden wordt voldaan:

- ITSMC geïnstalleerd. Meer informatie: [De IT Service Management Connector-oplossing toevoegen.](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)
- De Web-app ServiceManager (Web-app) wordt geïmplementeerd en geconfigureerd. Informatie over web-app is [hier](#create-and-deploy-service-manager-web-app-service).
- Hybride verbinding gemaakt en geconfigureerd. Meer informatie: [Configureer de hybride verbinding](#configure-the-hybrid-connection).
- Ondersteunde versies van Service Manager: 2012 R2 of 2016.
- Gebruikersrol: [Geavanceerde operator](https://technet.microsoft.com/library/ff461054.aspx).

### <a name="connection-procedure"></a>Verbindingsprocedure

Gebruik de volgende procedure om de instantie System Center Service Manager te verbinden met ITSMC:

1. Ga in Azure-portal naar **Alle bronnen** en zoek naar **ServiceDesk(YourWorkspaceName)**

2.  Klik **onder GEGEVENSBRONNEN** VOOR WERKRUIMTE op **ITSM-verbindingen**.

    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven aan het rechterdeelvenster op **Toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
> Al deze parameters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het systeemcentrumservicebeheer-exemplaar dat u met ITSMC wilt verbinden.  U gebruikt deze naam later wanneer u werkitems configureert in dit exemplaar/ gedetailleerde logboekanalyses bekijkt. |
| **Partnertype**   | Selecteer **System Center Service Manager**. |
| **Server-URL**   | Typ de URL van de Web-app ServiceManager. Meer informatie over Service Manager Web app is [hier](#create-and-deploy-service-manager-web-app-service).
| **Client-id**   | Typ de client-id die u hebt gegenereerd (met behulp van het automatische script) voor het verifiëren van de web-app. Meer informatie over het geautomatiseerde script vindt [u hier.](../../azure-monitor/platform/itsmc-service-manager-script.md)|
| **Client Secret**   | Typ het clientgeheim, gegenereerd voor deze ID.   |
| **Gegevens synchroniseren**   | Selecteer de werkitems servicebeheer die u via ITSMC wilt synchroniseren.  Deze werkitems worden geïmporteerd in Log Analytics. **Opties:**  Incidenten, wijzigingsverzoeken.|
| **Scope voor gegevenssynchronisatie** | Typ het aantal afgelopen dagen waarvan u de gegevens wilt hebben. **Maximumlimiet**: 120 dagen. |
| **Nieuw configuratieitem maken in ITSM-oplossing** | Selecteer deze optie als u de configuratie-items in het ITSM-product wilt maken. Wanneer log Analytics is geselecteerd, worden de getroffen C's als configuratie-items (in het geval van niet-bestaande IS's) in het ondersteunde ITSM-systeem gemaakt. **Standaard:** uitgeschakeld. |

![Servicemanager-verbinding](media/itsmc-connections/service-manager-connection.png)

**Wanneer u verbinding hebt gemaakt en gesynchroniseerd:**

- Geselecteerde werkitems uit Servicebeheer worden geïmporteerd in Azure **Log Analytics.** U het overzicht van deze werkitems bekijken op de tegel IT-servicebeheerconnector.

- U incidenten maken via Log Analytics-waarschuwingen of uit logboekrecords of vanuit Azure-waarschuwingen in dit instantie Service Beheer.


Meer informatie: [ITSM-werkitems maken vanuit Azure-waarschuwingen](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-and-deploy-service-manager-web-app-service"></a>Service Manager-webappservice maken en implementeren

Om on-premises Service Manager te verbinden met ITSMC in Azure, heeft Microsoft een Service Manager Web-app gemaakt op de GitHub.

Ga als volgt te werk om de ITSM-web-app voor uw Service Manager in te stellen:

- **De web-app implementeren** : implementeer de web-app, stel de eigenschappen in en verifieer met Azure AD. U de web-app implementeren met behulp van het [geautomatiseerde script](../../azure-monitor/platform/itsmc-service-manager-script.md) dat Microsoft u heeft verstrekt.
- **De hybride verbinding** - configureren[Configureer deze verbinding](#configure-the-hybrid-connection)handmatig.

#### <a name="deploy-the-web-app"></a>De web-app implementeren
Gebruik het geautomatiseerde [script](../../azure-monitor/platform/itsmc-service-manager-script.md) om de web-app te implementeren, de eigenschappen in te stellen en te verifiëren met Azure AD.

Voer het script uit door de volgende vereiste details op te geven:

- Azure-abonnementsgegevens
- Naam van de resourcegroep
- Locatie
- Service Manager-servergegevens (servernaam, domein, gebruikersnaam en wachtwoord)
- Voorvoegsel sitenaam voor uw web-app
- ServiceBus Naamruimte.

Het script maakt de web-app met de naam die u hebt opgegeven (samen met enkele extra tekenreeksen om deze uniek te maken). Het genereert de URL van de **web-app,** **client-ID** en **clientgeheim.**

Sla de waarden op, u gebruikt ze wanneer u een verbinding met ITSMC maakt.

**De installatie van de web-app controleren**

1. Ga naar **Azure-portalbronnen** > **Resources**.
2. Selecteer de web-app en klik op **Instellingen** > **Instellingen .**
3. Bevestig de informatie over het instantie Service Manager die u hebt opgegeven op het moment dat u de app via het script implementeert.

### <a name="configure-the-hybrid-connection"></a>De hybride verbinding configureren

Gebruik de volgende procedure om de hybride verbinding te configureren die de instantie Servicebeheer verbindt met ITSMC in Azure.

1. Zoek de Web-app Servicebeheer onder **Azure Resources**.
2. Klik **op Instellingen** > **netwerken**.
3. Klik onder **Hybride verbindingen**op **Eindpunten voor hybride verbinding configureren**.

    ![Netwerken voor hybride verbindingen](media/itsmc-connections/itsmc-hybrid-connection-networking-and-end-points.png)
4. Klik in het blad **Hybride verbindingen** op Hybride **verbinding toevoegen**.

    ![Hybride verbinding toevoegen](media/itsmc-connections/itsmc-new-hybrid-connection-add.png)

5. Klik **in** het blad Hybride verbindingen toevoegen op **Nieuwe hybride verbinding maken**.

    ![Nieuwe hybride verbinding](media/itsmc-connections/itsmc-create-new-hybrid-connection.png)

6. Typ de volgende waarden:

   - **EndPoint-naam:** geef een naam op voor de nieuwe hybride verbinding.
   - **EndPoint Host**: FQDN van de Service Manager-beheerserver.
   - **EndPoint-poort**: Type 5724
   - **Servicebus naamruimte**: Gebruik een bestaande servicebus naamruimte of maak een nieuwe.
   - **Locatie**: selecteer de locatie.
   - **Naam:** Geef een naam op aan de servicebus als u deze maakt.

     ![Hybride verbindingswaarden](media/itsmc-connections/itsmc-new-hybrid-connection-values.png)
6. Klik op **OK** om het **hybride verbindingsblad maken** te sluiten en de hybride verbinding te maken.

    Zodra de hybride verbinding is gemaakt, wordt deze onder het blad weergegeven.

7. Nadat de hybride verbinding is gemaakt, selecteert u de verbinding en klikt u op **Geselecteerde hybride verbinding toevoegen**.

    ![Nieuwe hybride verbinding](media/itsmc-connections/itsmc-new-hybrid-connection-added.png)

#### <a name="configure-the-listener-setup"></a>De listener-instelling configureren

Gebruik de volgende procedure om de listener-installatie voor de hybride verbinding te configureren.

1. Klik in het blad **Hybride verbindingen** op **Verbindingsbeheer downloaden** en installeer het op de machine waar het exemplaar System Center Service Manager wordt uitgevoerd.

    Zodra de installatie is voltooid, is de optie **Gebruikersinterface voor Hybride Verbindingsbeheer-beheer** beschikbaar onder het menu **Start.**

2. Klik **op De gebruikersinterface van Hybrid Connection Manager** , u wordt gevraagd om uw Azure-referenties.

3. Log in met uw Azure-referenties en selecteer uw abonnement waar de hybride verbinding is gemaakt.

4. Klik op **Opslaan**.

Uw hybride verbinding is met succes verbonden.

![succesvolle hybride verbinding](media/itsmc-connections/itsmc-hybrid-connection-listener-set-up-successful.png)
> [!NOTE]
> 
> Nadat de hybride verbinding is gemaakt, u de verbinding verifiëren en testen door naar de geïmplementeerde Web-app servicebeheer te gaan. Zorg ervoor dat de verbinding succesvol is voordat u probeert verbinding te maken met ITSMC in Azure.

In de volgende voorbeeldafbeelding worden de details van een geslaagde verbinding weergegeven:

![Hybride verbindingstest](media/itsmc-connections/itsmc-hybrid-connection-test.png)

## <a name="connect-servicenow-to-it-service-management-connector-in-azure"></a>ServiceNow verbinden met IT-servicebeheerconnector in Azure

In de volgende secties vindt u informatie over het verbinden van uw ServiceNow-product met ITSMC in Azure.

### <a name="prerequisites"></a>Vereisten
Zorg ervoor dat aan de volgende voorwaarden wordt voldaan:
- ITSMC geïnstalleerd. Meer informatie: [De IT Service Management Connector-oplossing toevoegen.](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)
- ServiceNow ondersteunde versies: New York, Madrid, Londen, Kingston, Jakarta, Istanbul, Helsinki, Genève.
> [!NOTE]
> ITSMC ondersteunt alleen het officiële SaaS-aanbod van Service Now. Privé-implementaties van Service Now worden niet ondersteund. 

**ServiceNow-beheerders moeten het volgende doen in hun ServiceNow-exemplaar:**
- Genereer client-ID en klantgeheim voor het ServiceNow-product. Zie de volgende informatie zoals vereist voor informatie over het genereren van client-ID en geheim:

    - [OAuth instellen voor New York](https://docs.servicenow.com/bundle/newyork-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Madrid](https://docs.servicenow.com/bundle/madrid-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Londen](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Kingston](https://docs.servicenow.com/bundle/kingston-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Jakarta](https://docs.servicenow.com/bundle/jakarta-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Istanbul](https://docs.servicenow.com/bundle/istanbul-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Helsinki](https://docs.servicenow.com/bundle/helsinki-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
    - [OAuth instellen voor Genève](https://docs.servicenow.com/bundle/geneva-servicenow-platform/page/administer/security/task/t_SettingUpOAuth.html)
> [!NOTE]
> Als onderdeel van de definitie van de "Set up OAuth" raden we aan:
>
> 1) **Werk de levensduur van het vernieuwingstoken bij tot 90 dagen (7.776.000 seconden):** Als onderdeel van het instellen van [OAuth](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_SettingUpOAuth.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696739125&sdata=Q7mF6Ej8MCupKaEJpabTM56EDZ1T8vFVyihhoM594aA%3D&reserved=0) in fase 2: [Maak een eindpunt voor clients om toegang te krijgen tot de instantie](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.servicenow.com%2Fbundle%2Fnewyork-platform-administration%2Fpage%2Fadminister%2Fsecurity%2Ftask%2Ft_CreateEndpointforExternalClients.html&data=02%7C01%7CNoga.Lavi%40microsoft.com%7C2c6812e429a549e71cdd08d7d1b148d8%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637208431696749123&sdata=hoAJHJAFgUeszYCX1Q%2FXr4N%2FAKiFcm5WV7mwR2UqeWA%3D&reserved=0) Na de definitie van het eindpunt, in ServiceNow blade search voor System OAuth dan selecteer Application Registry. Kies de naam van de OAuth die is gedefinieerd en werk het veld van Vernieuwingstokenlevensduur bij naar 7.776.000 (90 dagen in seconden).
> Klik aan het einde op bijwerken.
> 2) **Wij raden aan een interne procedure in te stellen om ervoor te zorgen dat de verbinding in leven blijft:** Volgens de Vernieuwingstokenlevensduur om het token te vernieuwen. Zorg ervoor dat u de volgende bewerkingen voorafgaand vernieuwen token verwachte vervaldatum (Paar dagen voordat de vernieuwing token levensduur verloopt raden we aan):
>
>>  1) [Een handmatig synchronisatieproces voltooien voor itsm-connectorconfiguratie](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-resync-servicenow)
 >> 2) Trek in bij het oude vernieuwingstoken, omdat het om veiligheidsredenen niet wordt aanbevolen om oude sleutels te bewaren. Selecteer in ServiceNow blade search naar System OAuth de optie Tokens beheren. Kies het oude token uit de lijst op basis van de OAuth-naam en vervaldatum. Klik op Toegang intrekken en dan op Intrekken.

- Installeer de integratie van de gebruikersapp voor Microsoft Log Analytics (ServiceNow-app). [Meer informatie](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1 ).
- Maak de gebruikersrol voor integratie voor de geïnstalleerde gebruikersapp. Informatie over het maken van de rol van de integratiegebruiker vindt [u hier.](#create-integration-user-role-in-servicenow-app)

### <a name="connection-procedure"></a>**Verbindingsprocedure**
Gebruik de volgende procedure om een ServiceNow-verbinding te maken:


1. Ga in Azure-portal naar **Alle bronnen** en zoek naar **ServiceDesk(YourWorkspaceName)**

2.  Klik **onder GEGEVENSBRONNEN** VOOR WERKRUIMTE op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven aan het rechterdeelvenster op **Toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.


> [!NOTE]
> Al deze parameters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het instantie ServiceNow die u met ITSMC wilt verbinden.  U gebruikt deze naam later in Log Analytics wanneer u werkitems configureert in deze ITSM/view detailed log analytics. |
| **Partnertype**   | Selecteer **ServiceNow**. |
| **Gebruikersnaam**   | Typ de integratiegebruikersnaam die u hebt gemaakt in de ServiceNow-app om de verbinding met ITSMC te ondersteunen. Meer informatie: [Gebruikersrol van de ServiceNow-app maken](#create-integration-user-role-in-servicenow-app).|
| **Wachtwoord**   | Typ het wachtwoord dat aan deze gebruikersnaam is gekoppeld. **Opmerking:** Gebruikersnaam en wachtwoord worden alleen gebruikt voor het genereren van verificatietokens en worden nergens binnen de ITSMC-service opgeslagen.  |
| **Server-URL**   | Typ de URL van het instantie ServiceNow die u met ITSMC wilt verbinden. De URL moet wijzen op een ondersteunde SaaS-versie met achtervoegsel ".servicenow.com".|
| **Client-id**   | Typ de client-id die u wilt gebruiken voor OAuth2-verificatie, die u eerder hebt gegenereerd.  Meer informatie over het genereren van client-ID en geheim: [OAuth Setup](https://wiki.servicenow.com/index.php?title=OAuth_Setup). |
| **Client Secret**   | Typ het clientgeheim, gegenereerd voor deze ID.   |
| **Scope voor gegevenssynchronisatie**   | Selecteer de ServiceNow-werkitems die u wilt synchroniseren met Azure Log Analytics, via de ITSMC.  De geselecteerde waarden worden geïmporteerd in logboekanalyses.   **Opties:**  Incidenten en wijzigingsverzoeken.|
| **Gegevens synchroniseren** | Typ het aantal afgelopen dagen waarvan u de gegevens wilt hebben. **Maximumlimiet**: 120 dagen. |
| **Nieuw configuratieitem maken in ITSM-oplossing** | Selecteer deze optie als u de configuratie-items in het ITSM-product wilt maken. Wanneer itsmc is geselecteerd, worden de getroffen CI's als configuratie-items (in het geval van niet-bestaande IS's) in het ondersteunde ITSM-systeem ge.When selected, ITSMC creates the affected CI as configuration items (in case of non-existing CI) in the supported ITSM system. **Standaard:** uitgeschakeld. |

![ServiceNow-verbinding](media/itsmc-connections/itsm-connection-servicenow-connection-latest.png)

**Wanneer u verbinding hebt gemaakt en gesynchroniseerd:**

- Geselecteerde werkitems uit de instantie ServiceNow worden geïmporteerd in Azure **Log Analytics.** U het overzicht van deze werkitems bekijken op de tegel IT-servicebeheerconnector.

- U incidenten maken via Log Analytics-waarschuwingen of uit logboekrecords of vanuit Azure-waarschuwingen in dit instantie ServiceNow-exemplaar.

Meer informatie: [ITSM-werkitems maken vanuit Azure-waarschuwingen](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="create-integration-user-role-in-servicenow-app"></a>Integratiegebruikersrol maken in De App ServiceNow

Gebruiker de volgende procedure:

1. Ga naar de [ServiceNow-winkel](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) en installeer de **Gebruikersapp voor ServiceNow en Microsoft OMS-integratie** in uw ServiceNow-exemplaar.
   
   >[!NOTE]
   >Als onderdeel van de voortdurende overgang van Microsoft Operations Management Suite (OMS) naar Azure Monitor wordt OMS nu Log Analytics genoemd.     
2. Ga na de installatie naar de linkernavigatiebalk van het exemplaar ServiceNow, zoek en selecteer Microsoft OMS-integrator.  
3. Klik **op Checklist voor installatie**.

   De status wordt weergegeven als **Niet volledig** als de gebruikersrol nog moet worden gemaakt.

4. Voer in de tekstvakken naast **Integratiegebruiker maken**de gebruikersnaam in voor de gebruiker die verbinding kan maken met ITSMC in Azure.
5. Voer het wachtwoord voor deze gebruiker in en klik op **OK**.  

> [!NOTE]
> 
> U gebruikt deze referenties om de ServiceNow-verbinding in Azure te maken.

De nieuw gemaakte gebruiker wordt weergegeven met de toegewezen standaardrollen.

**Standaardrollen:**
- personalize_choices
- import_transformer
-   x_mioms_microsoft.gebruiker
-   Itil
-   template_editor
-   view_changer

Zodra de gebruiker is gemaakt, wordt de status van **checklist voor het controleren** verplaatst naar Voltooid, waarin de details van de gebruikersrol die voor de app is gemaakt, worden vermeld.

> [!NOTE]
> 
> ITSM Connector kan incidenten naar ServiceNow sturen zonder dat er andere modules op uw ServiceNow-exemplaar zijn geïnstalleerd. Als u de module EventManagement gebruikt in uw Instantie ServiceNow en gebeurtenissen of waarschuwingen in ServiceNow wilt maken met behulp van de connector, voegt u de volgende rollen toe aan de integratiegebruiker:
> 
>    - evt_mgmt_integration
>    - evt_mgmt_operator  


## <a name="connect-provance-to-it-service-management-connector-in-azure"></a>Provance verbinden met IT-servicebeheerconnector in Azure

In de volgende secties vindt u informatie over het verbinden van uw Provance-product met ITSMC in Azure.


### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende voorwaarden wordt voldaan:


- ITSMC geïnstalleerd. Meer informatie: [De IT Service Management Connector-oplossing toevoegen.](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)
- Provance App moet worden geregistreerd bij Azure AD - en client-ID wordt beschikbaar gesteld. Zie voor gedetailleerde informatie [hoe u active directory-verificatie configureert](../../app-service/configure-authentication-provider-aad.md).

- Gebruikersrol: Beheerder.

### <a name="connection-procedure"></a>Verbindingsprocedure

Gebruik de volgende procedure om een Provance-verbinding te maken:

1. Ga in Azure-portal naar **Alle bronnen** en zoek naar **ServiceDesk(YourWorkspaceName)**

2.  Klik **onder GEGEVENSBRONNEN** VOOR WERKRUIMTE op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven aan het rechterdeelvenster op **Toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
> Al deze parameters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor het Provance-exemplaar dat u met ITSMC wilt verbinden.  U gebruikt deze naam later wanneer u werkitems configureert in deze ITSM/view gedetailleerde loganalyses. |
| **Partnertype**   | Selecteer **Provance**. |
| **Gebruikersnaam**   | Typ de gebruikersnaam die verbinding kan maken met ITSMC.    |
| **Wachtwoord**   | Typ het wachtwoord dat aan deze gebruikersnaam is gekoppeld. **Let op:** Gebruikersnaam en wachtwoord worden alleen gebruikt voor het genereren van verificatietokens en worden nergens binnen de ITSMC-service opgeslagen._|
| **Server-URL**   | Typ de URL van uw Provance-instantie die u met ITSMC wilt verbinden. |
| **Client-id**   | Typ de client-ID voor het verifiëren van deze verbinding, die u hebt gegenereerd in uw Provance-instantie.  Meer informatie over client-id, zie [hoe u active directory-verificatie configureert.](../../app-service/configure-authentication-provider-aad.md) |
| **Scope voor gegevenssynchronisatie**   | Selecteer de Provance-werkitems die u wilt synchroniseren met Azure Log Analytics, via ITSMC.  Deze werkitems worden geïmporteerd in logboekanalyses.   **Opties:**   Incidenten, wijzigingsverzoeken.|
| **Gegevens synchroniseren** | Typ het aantal afgelopen dagen waarvan u de gegevens wilt hebben. **Maximumlimiet**: 120 dagen. |
| **Nieuw configuratieitem maken in ITSM-oplossing** | Selecteer deze optie als u de configuratie-items in het ITSM-product wilt maken. Wanneer itsmc is geselecteerd, worden de getroffen CI's als configuratie-items (in het geval van niet-bestaande IS's) in het ondersteunde ITSM-systeem ge.When selected, ITSMC creates the affected CI as configuration items (in case of non-existing CI) in the supported ITSM system. **Standaard:** uitgeschakeld.|

![Provance-verbinding](media/itsmc-connections/itsm-connections-provance-latest.png)

**Wanneer u verbinding hebt gemaakt en gesynchroniseerd:**

- Geselecteerde werkitems uit deze Provance-instantie worden geïmporteerd in Azure **Log Analytics.** U het overzicht van deze werkitems bekijken op de tegel IT-servicebeheerconnector.

- U incidenten maken via Log Analytics-waarschuwingen of uit logboekrecords of van Azure-waarschuwingen in dit Provance-exemplaar.

Meer informatie: [ITSM-werkitems maken vanuit Azure-waarschuwingen](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

## <a name="connect-cherwell-to-it-service-management-connector-in-azure"></a>Cherwell verbinden met IT-servicebeheerconnector in Azure

In de volgende secties vindt u informatie over het verbinden van uw Cherwell-product met ITSMC in Azure.

### <a name="prerequisites"></a>Vereisten

Zorg ervoor dat aan de volgende voorwaarden wordt voldaan:

- ITSMC geïnstalleerd. Meer informatie: [De IT Service Management Connector-oplossing toevoegen.](../../azure-monitor/platform/itsmc-overview.md#adding-the-it-service-management-connector-solution)
- Client-ID gegenereerd. Meer informatie: [Client ID genereren voor Cherwell](#generate-client-id-for-cherwell).
- Gebruikersrol: Beheerder.

### <a name="connection-procedure"></a>Verbindingsprocedure

Gebruik de volgende procedure om een Provance-verbinding te maken:

1. Ga in Azure-portal naar **Alle bronnen** en zoek naar **ServiceDesk(YourWorkspaceName)**

2.  Klik **onder GEGEVENSBRONNEN** VOOR WERKRUIMTE op **ITSM-verbindingen**.
    ![Nieuwe verbinding](media/itsmc-connections/add-new-itsm-connection.png)

3. Klik boven aan het rechterdeelvenster op **Toevoegen**.

4. Geef de informatie op zoals beschreven in de volgende tabel en klik op **OK** om de verbinding te maken.

> [!NOTE]
> 
> Al deze parameters zijn verplicht.

| **Veld** | **Beschrijving** |
| --- | --- |
| **Verbindingsnaam**   | Typ een naam voor de Cherwell-instantie die u met ITSMC wilt verbinden.  U gebruikt deze naam later wanneer u werkitems configureert in deze ITSM/view gedetailleerde loganalyses. |
| **Partnertype**   | Selecteer **Cherwell.** |
| **Gebruikersnaam**   | Typ de Cherwell-gebruikersnaam die verbinding kan maken met ITSMC. |
| **Wachtwoord**   | Typ het wachtwoord dat aan deze gebruikersnaam is gekoppeld. **Let op:** Gebruikersnaam en wachtwoord worden alleen gebruikt voor het genereren van verificatietokens en worden nergens binnen de ITSMC-service opgeslagen.|
| **Server-URL**   | Typ de URL van uw Cherwell-exemplaar waarmee u verbinding wilt maken met ITSMC. |
| **Client-id**   | Typ de client-ID voor het verifiëren van deze verbinding, die u hebt gegenereerd in uw Cherwell-instantie.   |
| **Scope voor gegevenssynchronisatie**   | Selecteer de Cherwell-werkitems die u via ITSMC wilt synchroniseren.  Deze werkitems worden geïmporteerd in logboekanalyses.   **Opties:**  Incidenten, wijzigingsverzoeken. |
| **Gegevens synchroniseren** | Typ het aantal afgelopen dagen waarvan u de gegevens wilt hebben. **Maximumlimiet**: 120 dagen. |
| **Nieuw configuratieitem maken in ITSM-oplossing** | Selecteer deze optie als u de configuratie-items in het ITSM-product wilt maken. Wanneer itsmc is geselecteerd, worden de getroffen CI's als configuratie-items (in het geval van niet-bestaande IS's) in het ondersteunde ITSM-systeem ge.When selected, ITSMC creates the affected CI as configuration items (in case of non-existing CI) in the supported ITSM system. **Standaard:** uitgeschakeld. |


![Provance-verbinding](media/itsmc-connections/itsm-connections-cherwell-latest.png)

**Wanneer u verbinding hebt gemaakt en gesynchroniseerd:**

- Geselecteerde werkitems uit deze Cherwell-instantie worden geïmporteerd in Azure **Log Analytics.** U het overzicht van deze werkitems bekijken op de tegel IT-servicebeheerconnector.

- U incidenten maken via Log Analytics-waarschuwingen of uit logboekrecords of van Azure-waarschuwingen in dit cherwell-exemplaar.

Meer informatie: [ITSM-werkitems maken vanuit Azure-waarschuwingen](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts).

### <a name="generate-client-id-for-cherwell"></a>Client-ID genereren voor Cherwell

Gebruik de volgende procedure om de client-id/sleutel voor Cherwell te genereren:

1. Log in bij uw Cherwell-instantie als beheerder.
2. Klik **op Instellingen voor REST** > **API-client bewerken**.
3. Selecteer **Nieuw clientgeheim** > **maken**.

    ![Cherwell gebruikersnaam](media/itsmc-connections/itsmc-cherwell-client-id.png)


## <a name="next-steps"></a>Volgende stappen
 - [ITSM-werkitems maken op basis van Azure-waarschuwingen](../../azure-monitor/platform/itsmc-overview.md#create-itsm-work-items-from-azure-alerts)
