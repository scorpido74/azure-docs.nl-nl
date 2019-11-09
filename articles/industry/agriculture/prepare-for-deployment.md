---
title: Azure-FarmBeats implementeren
description: Hierin wordt beschreven hoe u FarmBeats implementeert
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 10ff3cc940ac3d11154f1dec6c06ff3681328d38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73890940"
---
# <a name="deploy-farmbeats"></a>FarmBeats implementeren

In dit artikel wordt beschreven hoe u Azure FarmBeats instelt.

Azure FarmBeats is een branchespecifieke, uitbreid bare oplossing voor gegevensgestuurde productie, waarmee naadloze inrichting en sensor apparaten verbinding kunnen maken met Azure-Cloud, gegevensverzameling van telemetriegegevens en aggregatie. Azure FarmBeats heeft verschillende Sens oren, zoals camera's, drones, bodem sensoren en het beheer van apparaten uit de Cloud, waaronder de infra structuur en services in azure voor de IoT Ready (Internet of Things)-apparaten aan een Extendible web en mobiele app die u kunt bieden visualisatie, waarschuwingen en inzichten.

> [!NOTE]
> Azure FarmBeats wordt alleen ondersteund in open bare Cloud omgevingen. Zie [Azure](https://azure.microsoft.com/overview/what-is-a-public-cloud/)voor meer informatie over de cloud omgeving.

Azure FarmBeats heeft de volgende twee onderdelen:

- **Data hub** -data hub is de platform-laag van Azure FarmBeats waarmee u gegevens kunt bouwen, opslaan, verwerken en inzicht krijgen in bestaande of nieuwe gegevens pijplijnen. Deze platform laag is handig voor het uitvoeren en bouwen van uw gegevens pijplijnen en modellen voor de land bouw.

- **Accelerator** -Accelerator is de oplossings-laag van Azure FarmBeats die een ingebouwde toepassing heeft voor het illustreren van de mogelijkheden van Azure FarmBeats met behulp van de vooraf gemaakte landbouw modellen. Met deze oplossing slaag kunt u grenzen in de farm maken en inzicht krijgen in de gegevens van de land bouw in de context van de grens van de farm.

Een snelle implementatie van Azure FarmBeats moet minder dan een uur duren. De kosten voor de data hub en de Accelerator variëren afhankelijk van het gebruik.

## <a name="deployed-resources"></a>Geïmplementeerde resources

De Azure FarmBeats-implementatie maakt de hieronder vermelde resources binnen uw abonnement:

|S.Nee  |Resourcenaam  |Azure FarmBeats-onderdeel  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Data hub       |
|2  |    Application Insights      |     Data hub/Accelerator     |
|3  |Azure Cache voor Redis   |Data hub   |
|4  |       Azure-sleutel kluis    |  Data hub/Accelerator        |
|5  |    Time Series Insights       |     Data hub      |
|6 |      EventHub-naam ruimte    |  Data hub       |
|7  |    Azure Data Factory V2       |     Data hub/Accelerator      |
|8  |Batch-account    |Data hub   |
|9  |       Storage-account     |  Data hub/Accelerator        |
|10  |    Logische apps        |     Data hub      |
|11  |    API-verbinding        |     Data hub      |
|12|      App service      |  Data hub/Accelerator       |
|13 |    app service-plan        |     Data hub/Accelerator      |
|14 |Azure Maps-account     |Snelle    |
|15 |       Time Series Insights      |  Data hub     |

Azure FarmBeats is beschikbaar om te downloaden van Azure Marketplace. U kunt deze rechtstreeks openen vanuit Azure Portal.  

## <a name="create-azure-farmbeats-offer-on-marketplace"></a>Een Azure FarmBeats-aanbieding maken op Marketplace

Volg deze stappen voor het maken van een Azure FarmBeats-aanbieding in Marketplace:

1. Meld u aan bij de Azure Portal en selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant waar u Microsoft Azure FarmBeats wilt implementeren.
2. Azure FarmBeats is beschikbaar op Azure Marketplace. Op de Marketplace-pagina selecteert u op nu downloaden.
3. Selecteer maken en voer de volgende gegevens in:
  - Abonnements naam.
  - een bestaande resource groeps naam (alleen lege resource groep) of een nieuwe resource groep maken voor het implementeren van Azure FarmBeats. Noteer deze resource groep in de volgende secties.
4. De regio waarvoor u Azure FarmBeats wilt installeren. Momenteel FarmBeats de volgende regio's: VS-centraal, Europa-west, VS-Oost 2, Europa-noord, VS-West, Zuidoost-Azië, VS-Oost, Australië-oost, VS-West 2.
5. Selecteer **OK**.
De pagina Gebruiksvoorwaarden wordt weer gegeven. Bekijk de Standard Marketplace-voor waarden of selecteer de Hyper link om de gebruiks voorwaarden te bekijken.
6. Selecteer **sluiten**en vervolgens het selectie vakje Ik ga akkoord en selecteer vervolgens **maken**.
7. U hebt nu de gebruiksrecht overeenkomst (EULA) van Azure FarmBeats ondertekend op Marketplace.  
7. Volg de volgende stappen in deze hand leiding om door te gaan met de implementatie.

## <a name="prepare"></a>Voorbereiden

U hebt de volgende machtigingen nodig voor de implementatie van Azure FarmBeats:

- Tenant: Lees toegang
- Abonnement: Inzender of eigenaar
- Resource groep: eigenaar

## <a name="before-you-begin"></a>Voordat u begint

Voordat u de implementatie start, controleert u of u het volgende hebt gedaan:

- Verklikker account
- Azure Active Directory (app-registratie)
- Azure-FarmBeats

## <a name="create-a-sentinel-account"></a>Een verklikker account maken    

Een account met Sentinel helpt u bij het downloaden van de installatie kopie van de verklikker satelliet van de officiële website naar uw apparaat. Voer de volgende stappen uit om een gratis account te maken:

1. Ga naar https://scihub.copernicus.eu/dhus/#/self-registration. Geef op de pagina registratie een voor naam, achternaam, gebruikers naam, wacht woord en e-mail adres op.
Er wordt een verificatie-e-mail verzonden naar het geregistreerde e-mail adres voor bevestiging. Selecteer de koppeling en bevestig dit. Het registratie proces is voltooid.

## <a name="create-azure-ad-app-registration"></a>Azure AD-App-registratie maken

Voor verificatie en autorisatie op Azure FarmBeats moet u een Azure Active Directory-toepassings registratie hebben die:

- Voor beeld 1: het installatie programma kan automatisch worden gemaakt (u hebt de vereiste toegangs machtigingen voor de Tenant, het abonnement en de resource groep).
- Voor beeld 2: u kunt maken en configureren voordat u Azure FarmBeats implementeert (hiervoor moeten hand matige stappen worden uitgevoerd).

Voor **Beeld 1**:: als u toegang hebt om een Aad-app-registratie te maken, kunt u deze stap overs Laan en de app-registratie laten maken met het installatie programma. Ga door naar de volgende sectie: [input. JSON-bestand voorbereiden](#prepare-input-json-file)

Als u al een abonnement hebt, kunt u rechtstreeks naar de volgende procedure verplaatsen.

Voor **Beeld 2**: deze methode is de voorkeurs stap wanneer u niet voldoende rechten hebt om een Azure AD-App-registratie binnen uw abonnement te maken en te configureren. Vraag uw beheerder om het [aangepaste script](https://aka.ms/FarmBeatsMarketplace)te gebruiken. Hiermee kunnen IT-beheerders de registratie van Azure AD-apps op het Azure Portal automatisch genereren en configureren. Als uitvoer voor het uitvoeren van dit aangepaste script met behulp van Power shell-omgeving moet de IT-beheerder een Azure Active Directory toepassings-client-ID en wachtwoord geheim met u delen. Noteer deze waarden.

Gebruik de volgende stappen om het Azure AD-toepassings registratie script uit te voeren:

1. [Script](https://aka.ms/FarmBeatsAADScript)downloaden.
2. Meld u aan bij Azure Portal en selecteer uw abonnement en de AD-Tenant.
3. Open Cloud Shell via de bovenste navigatiebalk van de Azure Portal.

    ![Maten van project-Farm](./media/prepare-for-deployment/navigation-bar-1.png)


4. De eerste keer dat gebruikers worden gevraagd om een abonnement te selecteren voor het maken van een opslag account en een Microsoft Azure bestands share. Selecteer **Opslag maken**.
5. De eerste keer dat gebruikers worden gevraagd met een keuze uit de voorkeurs shell-ervaring-bash of Power shell. Kies Power shell.
6. Upload het script (uit stap 1) naar de Cloud Shell en noteer de locatie van het geüploade bestand.

    > [!NOTE]
    > Standaard wordt deze geüpload naar uw basismap.

    Gebruik het volgende script:

    ```azurepowershell-interactive
    ./create_aad_script.ps1
    ```
7. Noteer de Azure AD-toepassings-ID en het client geheim om te delen met de persoon die Azure FarmBeats implementeert.

### <a name="prepare-input-json-file"></a>Invoer-JSON-bestand voorbereiden

Als onderdeel van de installatie maakt u een input. JSON-bestand als volgt:

    ```json
    {  
       "sku":"both",
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
       "datahubResourceGroup":"dummy-test-dh1",
       "location":"westus2",
       "datahubWebsiteName":"dummy-test-dh1",
       "acceleratorResourceGroup":" dummy-test-acc1",
       "acceleratorWebsiteName":" dummy-test-acc1",
       "sentinelUsername":"dummy-dev",
       "notificationEmailAddress":"dummy@yourorg.com",
       "updateIfExists":true
    }
    ```

Dit bestand is uw invoer bestand voor Azure Cloud Shell en para meters waarvan de waarden worden gebruikt tijdens de installatie. Alle para meters in de JSON moeten worden vervangen door de juiste waarden of worden verwijderd. Als u dit verwijdert, wordt u door het installatie programma gevraagd tijdens de installatie


> [!NOTE]
> Dit bestand invoer waarden die moeten worden Azure Cloud Shell.  Als u tijdens de implementatie tijd wilt besparen, wordt u niet gevraagd om de para meters die u aan dit bestand toevoegt. U wordt gevraagd om ontbrekende para meters.

Controleer de para meters voordat u het bestand voorbereidt.

|Opdracht | Beschrijving|
|--- | ---|
|sku  | Biedt een keuze om een of beide onderdelen van Azure FarmBeats te downloaden. Hiermee geeft u op welke onderdelen moeten worden gedownload. Als u alleen data hub wilt installeren, gebruikt u ' onlydatabhub '. Als u data hub en Accelerator wilt installeren, gebruikt u ' beide '|
|Abonnements  | Hiermee geeft u het abonnement voor het installeren van FarmBeats|
|"datahubResourceGroup"  | Naam van de resource groep voor data hub-resources|
|"acceleratorWebsiteName"  |Unieke URL-voor voegsel om uw gegevenshub een naam te gegeven|
|"acceleratorResourceGroup"  | Unieke URL-voor voegsel voor de naam van uw website voor Accelerators.|
|"datahubWebsiteName"  | UUnique URL-voor voegsel voor de naam van uw data hub-website. |
|''sentinelUsername'' | gebruikers naam voor aanmelding: https://scihub.copernicus.eu/dhus/#/self-registration.|
|"notificationEmailAddress"  | E-mail adres voor het ontvangen van meldingen voor waarschuwingen die u configureert in data hub.|
|"updateIfExists" "  |Beschrijving De para meter die moet worden opgenomen in input. json alleen als u een bestaand FarmBeats-exemplaar wilt bijwerken. Voor een upgrade worden hier andere details beschreven. de namen van de resource groepen, locaties enzovoort, moeten hetzelfde zijn.|
|"aadAppClientId"  | [**Optioneel**] De para meter die moet worden opgenomen in de invoer. json alleen als de Azure AD-app al bestaat.  |
|"aadAppClientSecret"   | [**Optioneel**] De para meter die moet worden opgenomen in de invoer. json alleen als de Azure AD-app al bestaat.|

## <a name="deploy-within-cloud-shell-browser-based-command-line"></a>Implementeren in Cloud Shell-opdracht regel op basis van een browser

Als onderdeel van de werk stroom voor Marketplace hierboven moet u één resource groep hebben gemaakt en de gebruiksrecht overeenkomst ondertekenen, die opnieuw kan worden gecontroleerd als onderdeel van de daad werkelijke implementatie. De implementatie kan worden uitgevoerd via Azure Cloud Shell (op een browser gebaseerde opdracht regel) met behulp van de bash-omgeving. Ga door naar de volgende secties om te implementeren via de Cloud Shell.

> [!NOTE]
> Inactieve Cloud Shell sessies verlopen na 20 minuten. Probeer de implementatie binnen deze tijd te volt ooien.

1. Meld u aan bij Azure Portal en selecteer het gewenste abonnement en de AD-Tenant.
2. Open Cloud Shell via de bovenste navigatiebalk van de Azure Portal.
3. Als u de Cloud Shell voor het eerst gebruikt, wordt u gevraagd een abonnement te selecteren voor het maken van een opslag account en Microsoft Azure bestands share.
4. Selecteer **opslag maken**.  

Selecteer de omgeving als bash (en niet Power shell).

## <a name="deployment-scenario-1"></a>Implementatie scenario 1

Het installatie programma maakt de Azure AD-app registratie (case 1 hierboven)

1. Kopieer de volgende sjabloon en geef deze de naam input. json.  
Voor beeld van JSON-invoer:

    ```json
    {  
       "sku":"both", 
       "subscriptionId": "da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup": "dummy-test-dh1", 
       "datahubLocation": "westus2", 
       "datahubWebsiteName": "dummy-test-dh1", 
       "acceleratorResourceGroup": "dummy-test-acc1", 
       "acceleratorLocation": "westus2", 
       "acceleratorWebsiteName": "dummy-test-acc1", 
       "sentinelUsername": "dummy-dev", 
       "farmbeatsAppId": "c3cb3xxx-27xx-4xxb-8xx6-3xxx2xxdxxx5c", 
       "notificationEmailAddress": "dummy@microsoft.com", 
       "updateIfExists": true
    }
    ```

2. Sla het bestand op en noteer het pad (op uw lokale computer).
3. Ga naar Azure Cloud Shell en selecteer na geslaagde verificatie de optie uploaden (Zie gemarkeerd pictogram in de onderstaande afbeelding) en upload het bestand input. json naar Cloud Shell Storage.  

    ![Maten van project-Farm](./media/prepare-for-deployment/bash-2-1.png)

4. Ga naar de basismap in de Cloud shell. Het is standaard/Home/<username>
5. Typ of plak de volgende twee opdrachten in de Cloud Shell. Zorg ervoor dat u het pad naar de invoer wijzigt. JSON-bestand en druk op ENTER.

      ```azurepowershell-interactive
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScriptbash farmbeats-installer.sh /home/<username>/input.json
     ```
     Het installatie programma downloadt automatisch alle afhankelijkheden en bouwt de deployer. U wordt gevraagd om akkoord te gaan met de gebruiksrecht overeenkomst voor Azure FarmBeats (EULA).

     - Voer ' Y ' in als u akkoord gaat en ga door naar de volgende stap.
     - Voer ' N ' in als u niet akkoord gaat met de voor waarden en de implementatie wordt beëindigd.

6. Vervolgens wordt u gevraagd om een toegangs token in te voeren voor de implementatie. Kopieer de gegenereerde code en meld u aan https://microsoft.com/devicelogin met uw Azure-referenties.

    > [!NOTE]
    > Het token verloopt na 60 minuten. Wanneer het verloopt, kunt u opnieuw opstarten door de implementatie opdracht opnieuw te typen.

7. Voer het wacht woord van uw Sentinel-account in wanneer dit wordt gevraagd.
8. Het installatie programma valideert nu en begint met implementeren, wat ongeveer 20 minuten kan duren.
9. Zodra de implementatie is voltooid, ontvangt u de onderstaande uitvoer koppelingen:

 - **Data hub-URL**: Swagger-koppeling naar probeer Azure FarmBeats-api's.
 - **URL van Accelerator**: gebruikers interface voor het verkennen van de Azure FarmBeats Smart-Farm Accelerator.
 - Bestand van het **deployer**-logboek bestand-logbestand dat is gemaakt tijdens de implementatie. Het kan worden gebruikt voor het oplossen van problemen, indien nodig.

## <a name="deployment-scenario-2"></a>Implementatie scenario 2

Bestaande registratie van Azure Active Directory-app wordt gebruikt voor implementatie (Case 2 hierboven)

1. Kopieer het onderstaande JSON-bestand, dat de Azure-toepassing client-ID en het wacht woord bevat in de invoer. json, en sla het op.

    ```json
   {

   "sku":"both",
   "subscriptionId":"daxx9xxx-d18f-4xxc-9c21-5xx3exxxxx45",
   "datahubResourceGroup":"dummy-test-dh1",   
   "location":"westus2",   
   "datahubWebsiteName":"dummy-test-dh1",   
   "acceleratorResourceGroup":"dummy-test-acc1",   
   "acceleratorWebsiteName":"dummy-test-acc1",   
   "sentinelUsername":"dummy-dev",
   "notificationEmailAddress":"dummyuser@org1.com",
   "updateIfExists": true,
   "aadAppClientId": "lmtlemlemylmylkmerkywmkm823",
   "aadAppClientSecret": "Kxxxqxxxxu*kxcxxx3Yxxu5xx/db[xxx"

   }
   ```

Volg de overige stappen:

2. Noteer het pad naar het bestand input. json (op de lokale computer).
3. Ga opnieuw naar Azure Cloud Shell en u bent geverifieerd, selecteert u de knop uploaden (Zie gemarkeerd pictogram in de onderstaande afbeelding) en uploadt u het bestand input. json voor Cloud Shell opslag.

    ![Maten van project-Farm](./media/prepare-for-deployment/bash-2-1.png)

4. Ga naar de basismap in de Cloud shell. Het is standaard/Home/<username>
5. Typ of plak de volgende twee opdrachten in de Cloud Shell. Zorg ervoor dat u het pad naar de invoer wijzigt. JSON-bestand en druk op ENTER.

    ```azurepowershell-interactive
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScriptbash farmbeats-installer.sh /home/<username>/input.json
    ```

Volg de instructies op het scherm.

6. Met het script worden alle afhankelijkheden automatisch gedownload en wordt de deployer gebouwd.
7. U wordt gevraagd om de gebruiksrecht overeenkomst voor Azure FarmBeats te lezen en ermee akkoord te gaan.

    - Voer ' Y ' in als u akkoord gaat en u verder gaat met de volgende stap.
    - Voer ' N ' in als u niet akkoord gaat met de voor waarden en de implementatie wordt beëindigd.

8. U wordt gevraagd om een toegangs token voor de implementatie op te geven. Kopieer de gegenereerde code en meld u aan bij https://microsoft.com/devicelogin met uw Azure-referenties.
9. Het installatie programma valideert nu het maken en starten van de resources, wat ongeveer 20 minuten kan duren. De sessie actief blijven op Cloud Shell gedurende deze periode.
10. Zodra de implementatie is voltooid, ontvangt u de onderstaande uitvoer koppelingen:

 - **Data hub-URL**: Swagger-koppeling om FarmBeats-api's uit te proberen.
 - **URL van Accelerator**: gebruikers interface voor het verkennen van FarmBeats Smart Farm Accelerator.
 - Bestand voor **deployer logboek**: logboek bestand gemaakt tijdens implementatie. Het kan worden gebruikt voor het oplossen van problemen, indien nodig.

Raadpleeg [problemen oplossen](troubleshoot-project-farmbeats.md)als u problemen ondervindt.


## <a name="validate-deployment"></a>Implementatie valideren

### <a name="data-hub"></a>Data hub

Zodra de data hub-installatie is voltooid, ontvangt u de URL voor toegang tot Azure FarmBeats Api's via de Swagger-interface met de volgende indeling: https://\<yourdatahub-website-name >. azurewebsites. net

1. Als u zich wilt aanmelden via Swagger, kopieert en plakt u de URL in de browser.
2. Meld u aan met Azure Portal referenties.
3. Sanity-test (optioneel)

     - U kunt zich met de data hub-koppeling aanmelden bij de Swagger-Portal, die u hebt ontvangen als uitvoer voor een geslaagde implementatie.
     - Uitgebreide typen Get API: Selecteer ' Probeer het uit '/Execute '
     - U ontvangt de server respons code 200 en geen uitzonde ring zoals 403 "niet-geautoriseerde gebruiker".

### <a name="accelerator"></a>Snelle

Zodra de installatie van de Accelerator is voltooid, ontvangt u de URL voor toegang tot de FarmBeats-gebruikers interface in de indeling: https://\<Accelerator-website-name >. azurewebsites. net

1. Als u zich wilt aanmelden vanuit de Accelerator, kopieert en plakt u de URL in de browser.
2. Meld u aan met Azure Portal referenties.
3. Voer een optionele Sanity-test uit.

    - Controleer of u zich kunt aanmelden bij de Accelerator Portal met behulp van de koppeling die u hebt ontvangen als uitvoer voor een geslaagde implementatie.
    - Selecteer **farm maken**.
    - Open de FarmBeats-hand leiding onder het pictogram '? ' met behulp van de knop **aan de slag** .

## <a name="upgrade"></a>Upgraden

De stappen voor de upgrade zijn vergelijkbaar met de installatie van de eerste keer. Volg deze stappen:

1. Meld u aan bij Azure Portal en selecteer het gewenste abonnement en de AD-Tenant.
2. Open Cloud Shell via de bovenste navigatiebalk van de Azure Portal.

   ![Maten van project-Farm](./media/prepare-for-deployment/navigation-bar-1.png)

3. Selecteer de omgeving ' bash ' in de vervolg keuzelijst aan de linkerkant van de shell.
4. Breng wijzigingen alleen aan in het bestand input. json als dat nodig is en upload het naar het Azure Cloud Shell. U kunt bijvoorbeeld uw e-mail adres bijwerken voor de melding die u wilt ontvangen.
5. Upload het bestand input. json naar Azure Cloud Shell.
6. Typ of plak de volgende twee opdrachten in de Cloud Shell. Zorg ervoor dat u het pad naar het bestand input. json wijzigt en druk op ENTER.

    ```azurepowershell-interactive
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScriptbash farmbeats-installer.sh /home/<username>/input.json
    ```
Volg de instructies op het scherm:

7. Het installatie programma vraagt automatisch de vereiste invoer op tijdens runtime:
8. Voer een toegangs token in voor de implementatie. Kopieer de gegenereerde code en meld u aan bij https://microsoft.com/devicelogin met uw Azure-referenties.
9. Verklikker wachtwoord
10. Het installatie programma valideert nu en begint met het maken van de resources, wat ongeveer 20 minuten kan duren.
11. Zodra de implementatie is voltooid, ontvangt u de onderstaande uitvoer koppelingen:
 - **Data hub-URL**: Swagger-koppeling om FarmBeats-api's uit te proberen.
 - **URL van Accelerator**: gebruikers interface voor het verkennen van FarmBeats Smart Farm Accelerator.
 - **Bestand van deployer-logboek**: slaat logboeken op tijdens de implementatie. Het kan worden gebruikt voor het oplossen van problemen.

> [!NOTE]
> Noteer de bovenstaande waarden voor toekomstig gebruik.


## <a name="uninstall"></a>Verwijderen

Micro soft biedt momenteel geen ondersteuning voor het automatisch verwijderen van FarmBeats met behulp van het installatie programma. Als u de data hub of Accelerator wilt verwijderen, verwijdert u in de Azure Portal de resource groep waarin deze onderdelen zijn geïnstalleerd of verwijdert u de resources hand matig.

Als u bijvoorbeeld data hub en Accelerator hebt geïmplementeerd in twee verschillende resource groepen, verwijdert u deze resource groepen als volgt:

1. Meld u aan bij Azure Portal.
2. Selecteer uw account in de rechter bovenhoek en schakel over naar de gewenste Azure AD-Tenant waar u micro soft FarmBeats wilt implementeren.

   > [!NOTE]
   > Data hub is vereist voor een juiste werking van de versnelling. Het verwijderen van de data hub wordt niet aanbevolen zonder dat de Accelerator wordt verwijderd.

3. Selecteer resource groepen en typ de naam van de data hub of Accelerator resource groep die u wilt verwijderen.
4. Selecteer de naam van de resourcegroep. Typ de naam opnieuw om deze te controleren en selecteer verwijderen om de resource groep en alle onderliggende resources te verwijderen.
5. U kunt ook elke resource hand matig verwijderen. dit wordt niet aanbevolen.
7. Als u data hub wilt verwijderen/verwijderen, gaat u rechtstreeks naar de resource groep in Azure en verwijdert u de resource groep.

## <a name="next-steps"></a>Volgende stappen

U hebt Azure FarmBeats geïmplementeerd. Nu leert u hoe u [Farms maakt](manage-farms.md#create-farms).
