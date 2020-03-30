---
title: Azure FarmBeats installeren
description: In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 0761db6b73c6fcfeb1ef6fda729a68c9644bbc72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479556"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats installeren

In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement.

Azure FarmBeats is een business-to-business-aanbod dat beschikbaar is in Azure Marketplace. Het maakt aggregatie van landbouwgegevensverzamelingen over aanbieders en het genereren van bruikbare inzichten mogelijk. Azure FarmBeats doet dit door u in staat te stellen kunstmatige intelligentie (AI) of machine learning (ML) modellen te bouwen op basis van gefuseerde gegevenssets. De twee belangrijkste onderdelen van Azure FarmBeats zijn:

- **Datahub**: een API-laag die aggregatie, normalisatie en contextualisatie van verschillende landbouwgegevenssets tussen verschillende providers mogelijk maakt.

- **Accelerator**: Webapplicatie die bovenop Datahub is gebouwd. Het jump-starts uw model ontwikkeling en visualisatie. De accelerator maakt gebruik van Azure FarmBeats API's om visualisatie van ingenomen sensorgegevens aan te tonen als grafieken en visualisatie van modeluitvoer als kaarten.

## <a name="general-information"></a>Algemene informatie

### <a name="components-installed"></a>Geïnstalleerde onderdelen

Wanneer u Azure FarmBeats installeert, worden de volgende resources in uw Azure-abonnement ingericht:

| Azure-bronnen geïnstalleerd  | Azure FarmBeats-component  |
|---------|---------|
| Application Insights   |      Datahub & Accelerator      |
| App Service     |     Datahub & Accelerator     |
| App Service-plan   | Datahub & Accelerator  |
| API-verbinding    |  Datahub       |
| Azure Cache voor Redis       | Datahub      |
| Azure Cosmos DB   |  Datahub       |
| Azure Data Factory V2       |     Datahub & Accelerator      |
| Azure Batch-account    | Datahub   |
| Azure Key Vault |  Datahub & Accelerator        |
| Azure Maps-account       |     Accelerator    |
| Naamruimte voor gebeurtenishub    |     Datahub      |
| Logische apps      |  Datahub       |
| Opslagaccount      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Kosten

De kosten van Azure FarmBeats zijn een totaal van de kosten van de onderliggende Azure-services. Prijsgegevens voor Azure-services kunnen worden berekend met behulp van de [prijscalculator](https://azure.microsoft.com/pricing/calculator). De werkelijke kosten van de totale installatie variëren afhankelijk van het gebruik. De steady state kosten voor de twee componenten is:

- Datahub - minder dan $ 10 per dag
- Accelerator - minder dan $ 2 per dag

### <a name="regions-supported"></a>Ondersteunde regio's

Azure FarmBeats wordt momenteel ondersteund in openbare cloudomgevingen in de volgende regio's:

- Australië - oost
- VS - centraal
- VS - oost
- VS - oost 2
- VS - west
- VS - west 2
- Europa - noord
- Europa -west
- Azië - oost
- Zuidoost-Azië

### <a name="time-taken"></a>Tijd genomen

De volledige installatie van Azure FarmBeats, inclusief de voorbereiding en installatie, duurt minder dan een uur.

## <a name="prerequisites"></a>Vereisten

U moet de volgende stappen uitvoeren voordat u de daadwerkelijke installatie van Azure FarmBeats start:

### <a name="verify-permissions"></a>Machtigingen verifiëren

U hebt de volgende machtigingen in de Azure-tenant nodig om Azure FarmBeats te installeren:

- Tenant - AAD-appmaker
- Abonnement - Eigenaar
- Resourcegroep waarin FarmBeats wordt geïnstalleerd - Eigenaar

De eerste twee machtigingen zijn nodig voor [het maken van de AAD-toepassingsstap.](#create-an-aad-application) Indien nodig u iemand met de juiste machtigingen ertoe leiden dat de AAD-toepassing wordt gemaakt.

De persoon die de FarmBeats-installatie vanaf marktplaats uitvoert, moet eigenaar zijn van de Resource Group waarin FarmBeats wordt geïnstalleerd. Voor abonnementseigenaren gebeurt dit automatisch wanneer Resource Group wordt gemaakt. Voor anderen u de resourcegroep vooraf maken en de eigenaar van het abonnement vragen om u eigenaar te maken van de resourcegroep.

U uw toegangsmachtigingen in de Azure-portal verifiëren door de instructies voor [op rollen gebaseerd toegangsbeheer te](https://docs.microsoft.com/azure/role-based-access-control/check-access)volgen.

### <a name="decide-subscription-and-region"></a>Abonnement en regio bepalen

U hebt de Azure-abonnements-ID en de regio waar u Azure FarmBeats wilt installeren, nodig. Kies een van de regio's die worden vermeld in de [door Regio's ondersteunde](#regions-supported) sectie.

Noteer de **Azure Subscription ID** en de **Azure Region**.

### <a name="create-an-aad-application"></a>Een AAD-toepassing maken

Azure FarmBeats vereist het maken en registreren van Azure Active Directory-toepassingen. Als u het AAD-creatiescript wilt uitvoeren, zijn de volgende machtigingen nodig:

- Tenant - AAD-appmaker
- Abonnement - Eigenaar

Voer de volgende stappen uit in een Cloud Shell-exemplaar met de PowerShell-omgeving. Nieuwe gebruikers wordt gevraagd een abonnement te selecteren en een opslagaccount aan te maken. Voltooi de installatie zoals geïnstrueerd.

1. Het [generatorscript van de AAD-app downloaden](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. Standaard wordt het bestand gedownload naar uw thuismap. Navigeer naar de map.

    ```azurepowershell-interactive
        cd
    ```

3. Het AAD-script uitvoeren

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. Het script vraagt om de volgende drie ingangen:

    - **FarmBeats Website Naam:** Dit is het unieke URL-voorvoegsel voor uw FarmBeats-webtoepassing. In het geval dat het voorvoegsel al is genomen, wordt het script fout loos. Eenmaal geïnstalleerd, zal uw FarmBeats-implementatie toegankelijk zijn vanaf https://\<FarmBeats-websitenaam>.azurewebsites.net\<en de branie-API's zullen worden weergegeven op https:// FarmBeats-website-naam>-api.azurewebsites.net

    - **Azure-inlog-id**: Geef Azure-inlog-id op voor de gebruiker die u als beheerder van FarmBeats wilt toevoegen. Deze gebruiker kan vervolgens toegang verlenen tot de webtoepassing FarmBeats aan andere gebruikers. De inlog-id is john.doe@domain.comover het algemeen van het formulier . Azure UPN wordt ook ondersteund.

    - **Abonnements-ID**: dit is de id van het abonnement waarin u Azure FarmBeats wilt installeren

5. Het AAD-script duurt ongeveer 2 minuten om waarden uit te voeren en uit te voeren op het scherm en naar een json-bestand in dezelfde map. Als je iemand anders het script liet uitvoeren, vraag hem of haar deze uitvoer met je te delen.

### <a name="create-sentinel-account"></a>Sentinel-account maken

Met uw Azure FarmBeats-opstelling u satellietbeelden krijgen van de [Sentinel-2-satellietmissie](https://scihub.copernicus.eu/) van de European Space Agency voor uw boerderij. Om deze instelling te configureren, hebt u een Sentinel-account nodig.

Volg de stappen om een gratis account aan te maken bij Sentinel:

1. Ga naar de officiële [aanmeldingspagina.](https://aka.ms/SentinelRegistration)
2. Geef de vereiste gegevens op (voornaam, achternaam, gebruikersnaam, wachtwoord en e-mail-id) en vul het formulier in.
3. Er wordt een verificatielink verzonden naar de geregistreerde e-id. Selecteer de koppeling in de e-mail en voltooi de verificatie.

Uw registratieproces is voltooid. Noteer uw **Sentinel gebruikersnaam** en **Sentinel Wachtwoord,** zodra de verificatie ook is voltooid.

## <a name="install"></a>Installeren

U bent nu klaar om FarmBeats te installeren. Volg de onderstaande stappen om de installatie te starten:

1. Meld u aan bij Azure Portal. Selecteer uw account in de rechterbovenhoek en schakel over naar de Azure AD-tenant waar u Azure FarmBeats wilt installeren.

2. Ga naar Azure Marketplace binnen de portal en zoek naar **Azure FarmBeats** in de Marketplace.

3. Er verschijnt een nieuw venster met een overzicht van Azure FarmBeats. Selecteer **Maken**.

4. Een nieuw venster wordt weergegeven. Voltooi het aanmeldingsproces door het juiste abonnement, de resourcegroep en de locatie te kiezen waarop u Azure FarmBeats wilt installeren.

5. Geef het e-mailadres op dat servicewaarschuwingen met betrekking tot Azure FarmBeats moet ontvangen in de sectie **FarmBeats-servicewaarschuwingen.** Selecteer **Volgende** onder aan de pagina om naar het tabblad **Afhankelijkheden** te gaan.

    ![Tabblad Basisbeginselen](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. Kopieer de afzonderlijke vermeldingen uit de uitvoer van [AAD-script](#create-an-aad-application) naar de ingangen in de aad-toepassingssectie.

7. Voer de gebruikersnaam en het wachtwoord van het [Sentinel-account](#create-sentinel-account) in in de sectie Sentinel-account. Selecteer **Volgende** om naar het tabblad **Controleren + Maken te** gaan.

    ![Tabblad Afhankelijkheden](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. Zodra de ingevoerde gegevens zijn gevalideerd, selecteert u **OK**. De pagina Gebruiksvoorwaarden wordt weergegeven. Bekijk de voorwaarden en selecteer **Maken** om de installatie te starten. U wordt doorgestuurd naar de pagina waar u de voortgang van de installatie volgen.

Zodra de installatie is voltooid, u de installatie verifiëren en farmbeats-portal gebruiken\<door te navigeren naar de websitenaam die u tijdens de installatie hebt opgegeven: https:// FarmBeats-websitenaam>.azurewebsites.net. U ziet de gebruikersinterface FarmBeats met een optie om Farms te maken.

**Datahub** is te\<vinden op https:// FarmBeats-website-naam>-api.azurewebsites.net/swagger. Hier ziet u de verschillende FarmBeats API-objecten en voert u REST-bewerkingen uit op de API's.

## <a name="upgrade"></a>Upgraden

Als u FarmBeats wilt upgraden naar de nieuwste versie, voert u de volgende stappen uit in een Cloud Shell-exemplaar met behulp van de PowerShell-omgeving. De gebruiker moet de eigenaar zijn van het abonnement waarin FarmBeats is geïnstalleerd.

Nieuwe gebruikers wordt gevraagd een abonnement te selecteren en een opslagaccount aan te maken. Voltooi de installatie zoals geïnstrueerd.

1. Het [upgradescript downloaden](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. Standaard wordt het bestand gedownload naar uw thuismap. Navigeer naar de map.

    ```azurepowershell-interactive
        cd
    ```

3. Het upgradescript uitvoeren

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

Het pad naar het bestand input.json is optioneel. Indien niet opgegeven, zal het script vragen om alle vereiste ingangen. De upgrade moet eindigen in ongeveer 30 minuten.

## <a name="uninstall"></a>Verwijderen

Voer de volgende stappen uit om Azure FarmBeats Datahub of Accelerator te verwijderen:

1. Meld u aan bij de Azure-portal en **verwijder de brongroepen** waarin deze componenten zijn geïnstalleerd.

2. Ga naar Azure Active Directory & **de Azure AD-toepassing verwijderen** die is gekoppeld aan de Azure FarmBeats-installatie.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u Azure FarmBeats installeren in uw Azure-abonnement. Lees nu hoe u [gebruikers toevoegt](manage-users-in-azure-farmbeats.md#manage-users) aan uw Azure FarmBeats-exemplaar.
