---
title: Azure-FarmBeats implementeren
description: In dit artikel wordt beschreven hoe u Azure FarmBeats implementeert.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: c29bba81132df15fcea9ff0df7be6a8cea94c9a0
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851125"
---
# <a name="deploy-azure-farmbeats"></a>Azure-FarmBeats implementeren

In dit artikel wordt beschreven hoe u Azure FarmBeats instelt.

Azure FarmBeats is een branchespecifieke, uitbreid bare oplossing voor gegevensgestuurde productie, waarmee naadloze inrichting en connectiviteit van sensor apparaten mogelijk is voor de Azure-Cloud, gegevensverzameling van telemetriegegevens en aggregatie. Azure FarmBeats omvat diverse Sens oren, zoals camera's, drones en bodem Sens oren. Met Azure FarmBeats kunt u apparaten beheren vanuit de Cloud, waaronder het beheren van infra structuur en services in azure voor IoT-Ready-apparaten en het gebruik van een uitbreid bare web-en mobiele app om visualisatie, waarschuwingen en inzichten te bieden.

> [!NOTE]
> Azure FarmBeats wordt alleen ondersteund in open bare Cloud omgevingen. Zie [Wat is een open bare Cloud?](https://azure.microsoft.com/overview/what-is-a-public-cloud/)voor meer informatie.

Azure FarmBeats heeft de volgende twee onderdelen:

- **Datahub**: de platform laag van Azure FarmBeats waarmee u gegevens kunt bouwen, opslaan, verwerken en inzicht krijgen in bestaande of nieuwe gegevens pijplijnen. Deze platform laag is handig voor het uitvoeren en bouwen van uw gegevens pijplijnen en modellen voor de land bouw.

- **Accelerator**: de FarmBeats van Azure, die een ingebouwde toepassing heeft voor het illustreren van de mogelijkheden van Azure FarmBeats met behulp van de vooraf gemaakte landbouw modellen. Met deze oplossing slaag kunt u grenzen in de farm maken en inzicht krijgen in de gegevens van de land bouw in de context van de grens van de farm.

Een snelle implementatie van Azure FarmBeats moet minder dan een uur duren. De kosten voor Datahub en Accelerator variëren, afhankelijk van het gebruik.

## <a name="deployed-resources"></a>Geïmplementeerde resources

Azure FarmBeats Deployment maakt de volgende resources binnen uw abonnement:

| S. nr.  | Resourcenaam  | Onderdeel FarmBeats  |
|---------|---------|---------|
|1  |       Azure Cosmos DB   |  Datahub       |
|2  |    Application Insights      |     Datahub/Accelerator     |
|3  |Azure Cache voor Redis   |Datahub   |
|4  |       Azure Key Vault    |  Datahub/Accelerator        |
|5  |    Azure Time Series Insights       |     Datahub      |
|6 |      Azure Event hub-naam ruimte    |  Datahub       |
|7  |    Azure Data Factory V2       |     Datahub/Accelerator      |
|8  |Batch-account    |Datahub   |
|9  |       Opslagaccount     |  Datahub/Accelerator        |
|10  |    Logische apps        |     Datahub      |
|11  |    API-verbinding        |     Datahub      |
|12|      Azure App Service      |  Datahub/Accelerator       |
|13 |    App Service-abonnement        |     Datahub/Accelerator      |
|14 |Azure Maps-account     |Snelle    |
|15 |       Azure Time Series Insights      |  Datahub     |

Azure FarmBeats is beschikbaar voor down loads in azure Marketplace, die u rechtstreeks vanuit de Azure Portal kunt openen.  

## <a name="create-an-azure-farmbeats-offer-in-azure-marketplace"></a>Een Azure FarmBeats-aanbieding maken in azure Marketplace

Ga als volgt te werk om een Azure FarmBeats-aanbieding te maken in azure Marketplace:

1. Meld u aan bij de Azure Portal, Selecteer rechtsboven uw account en schakel vervolgens over naar de Azure Active Directory (Azure AD)-Tenant waar u Azure FarmBeats wilt implementeren.
1. Azure FarmBeats is beschikbaar in azure Marketplace. Op de pagina **Azure Marketplace** selecteert **u nu ophalen**.
1. Selecteer **Maken**.

> [!NOTE]
> Het volt ooien van de aanbieding in azure Marketplace is slechts een onderdeel van de installatie. Volg de instructies in de volgende secties om de implementatie van Azure FarmBeats in uw Azure-abonnement te volt ooien.

## <a name="prepare"></a>Voorbereiden

Voordat u Azure FarmBeats kunt implementeren, moet u de volgende machtigingen hebben:

- **Tenant**: Lees toegang
- **Abonnement**: Inzender of eigenaar
- **Resource groep**: eigenaar

## <a name="before-you-begin"></a>Voordat u begint

Voordat u met de implementatie begint, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan:

- Een verklikker account
- Een Azure AD-App registreren

## <a name="create-a-sentinel-account"></a>Een verklikker account maken    

Een account met Sentinel helpt u bij het downloaden van de installatie kopie van de verklikker satelliet van de officiële website naar uw apparaat. Als u een gratis account wilt maken, gaat u als volgt te werk:

1. Ga naar de [registratie pagina voor verklikker accounts](https://scihub.copernicus.eu/dhus/#/self-registration). 
1. Geef op het registratie formulier uw voor naam, achternaam, gebruikers naam, wacht woord en e-mail adres op.

Er wordt een verificatie-e-mail verzonden naar het geregistreerde e-mail adres voor bevestiging. Selecteer de koppeling om uw e-mail adres te bevestigen. Het registratie proces is voltooid.

## <a name="create-an-azure-ad-app-registration"></a>Een Azure AD-App-registratie maken

Voor verificatie en autorisatie op Azure FarmBeats moet u een Azure AD-App registreren. U kunt de app op twee manieren maken:

* **Optie 1**: het installatie programma kan de registratie automatisch maken, mits u de vereiste toegangs machtigingen voor de Tenant, het abonnement en de resource groep hebt. Als deze machtigingen zijn ingesteld, gaat u verder met de sectie [' de invoer. JSON-bestand voorbereiden '](#prepare-the-inputjson-file) .

* **Optie 2**: u kunt de registratie hand matig maken en configureren voordat u Azure FarmBeats implementeert. We raden deze methode aan wanneer u niet over de vereiste machtigingen beschikt om een Azure AD-App-registratie binnen uw abonnement te maken en te configureren. Vraag uw beheerder om het [aangepaste script](https://aka.ms/FarmBeatsAADScript)te gebruiken. Hiermee kan de IT-beheerder de registratie van Azure AD-apps op het Azure Portal automatisch genereren en configureren. Als uitvoer voor het uitvoeren van dit aangepaste script met de Power shell-omgeving, moet de IT-beheerder een client-ID van de Azure AD-toepassing en het wachtwoord geheim met u delen. Noteer deze waarden.

    Ga als volgt te werk om het Azure AD-App-registratie script uit te voeren:

    1. Down load het [script](https://aka.ms/FarmBeatsAADScript).
    1. Meld u aan bij de Azure Portal en selecteer uw abonnement en de Azure AD-Tenant.
    1. Start Cloud Shell vanuit de werk balk boven aan het Azure Portal.

        ![Project FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

    1. Als u een eerste keer gebruiker bent, wordt u gevraagd om een abonnement te selecteren voor het maken van een opslag account en Microsoft Azure bestands share. Selecteer **Opslag maken**.
    1. De eerste keer dat gebruikers de shell-ervaringen, bash of Power shell kunnen kiezen. Kies Power shell.
    1. Upload het script (uit stap 1) naar Cloud Shell en noteer de locatie van het geüploade bestand.

        > [!NOTE]
        > Standaard wordt het bestand geüpload naar uw basismap.

        Gebruik het volgende script:

        ```azurepowershell-interactive
        ./create_aad_script.ps1
        ```
    1. Noteer de Azure AD-toepassings-ID en het client geheim om te delen met de persoon die Azure FarmBeats implementeert.

### <a name="prepare-the-inputjson-file"></a>Het bestand input. json voorbereiden

Als onderdeel van de installatie maakt u een *input. json* -bestand, zoals hier wordt weer gegeven:

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

Dit bestand is het invoer bestand dat moet worden Azure Cloud Shell. Het bevat de para meters waarvan de waarden worden gebruikt tijdens de installatie. Alle para meters in het JSON-bestand moeten worden vervangen door de juiste waarden of worden verwijderd. Deze worden verwijderd, het installatie programma vraagt u tijdens de installatie.

Voordat u het bestand voorbereidt, controleert u de para meters in de volgende tabel:

|Opdracht | Beschrijving|
|--- | ---|
|sku  | Biedt een keuze om een of beide van de onderdelen van Azure FarmBeats te downloaden. Hiermee geeft u op welke onderdelen moeten worden gedownload. Als u alleen Datahub wilt installeren, gebruikt u ' onlydatabhub '. Als u Datahub en Accelerator wilt installeren, gebruikt u beide.|
|subscriptionId | Hiermee geeft u het abonnement op voor het installeren van Azure FarmBeats.|
|datahubResourceGroup| De naam van de resource groep voor uw Datahub-resources.|
|location |De locatie waar u de resources wilt maken.|
|acceleratorWebsiteName |Het unieke URL-voor voegsel voor de naam van uw Datahub-exemplaar.|
|acceleratorResourceGroup  | Het unieke URL-voor voegsel voor de naam van uw website voor Accelerators.|
|datahubWebsiteName  | Het unieke URL-voor voegsel voor de naam van uw Datahub-website. |
|sentinelUsername | De gebruikers naam waarmee u zich kunt aanmelden (bijvoorbeeld `https://scihub.copernicus.eu/dhus/#/self-registration`).|
|notificationEmailAddress  | Het e-mail adres voor het ontvangen van meldingen voor waarschuwingen die u configureert in uw Datahub-exemplaar.|
|updateIfExists| Beschrijving Een para meter die alleen in het bestand *input. json* moet worden ingevoegd als u een bestaand exemplaar van Azure FarmBeats wilt bijwerken. Voor een upgrade moeten andere details, zoals de namen en locaties van de resource groep, hetzelfde zijn.|
|aadAppClientId | Beschrijving Een para meter die alleen in het bestand *input. json* moet worden ingevoegd als de Azure AD-app al bestaat.  |
|aadAppClientSecret  | Beschrijving Een para meter die alleen in het bestand *input. json* moet worden ingevoegd als de Azure AD-app al bestaat.|

## <a name="deploy-the-app-within-cloud-shell"></a>De app implementeren in Cloud Shell

Als onderdeel van de eerder besproken Marketplace-werk stroom moet u één resource groep hebben gemaakt en de gebruiksrecht overeenkomst hebben ondertekend, die u opnieuw kunt controleren als onderdeel van de huidige implementatie. U kunt de app implementeren via de Cloud Shell opdracht regel interface op basis van een browser met behulp van de bash-omgeving. Ga door naar de volgende secties om te implementeren via Cloud Shell.

> [!NOTE]
> Inactieve Cloud Shell sessies verlopen na 20 minuten. Probeer de implementatie binnen deze tijd te volt ooien.

1. Meld u aan bij de Azure Portal en selecteer het abonnement en de Azure-Tenant die u wilt gebruiken.
1. Start Cloud Shell vanuit de werk balk boven aan het Azure Portal.
1. Als u Cloud Shell voor het eerst gebruikt, wordt u gevraagd om een abonnement te selecteren voor het maken van een opslag account en een Azure Files share.
1. Selecteer **opslag maken**.  
1. Selecteer **bash** (niet Power shell) voor de omgeving.

## <a name="deployment-scenario-1"></a>Implementatie scenario 1

In dit scenario, dat eerder is beschreven in ' optie 1 ', wordt de Azure AD-App-registratie automatisch gemaakt. U implementeert FarmBeats door het volgende te doen:

1. Kopieer de volgende JSON-voorbeeld sjabloon en sla deze op als *invoer. json*. Zorg ervoor dat u het bestandspad op de lokale computer noteert.

    ```json
    {  
       "sku":"both", 
       "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx", 
       "datahubResourceGroup":"dummy-test-dh1", 
       "location":"eastus2", 
       "datahubWebsiteName":"dummy-test-dh1", 
       "acceleratorResourceGroup":" dummy-test-acc1",   
       "acceleratorWebsiteName":" dummy-test-acc1", 
       "sentinelUsername":"dummy-dev", 
       "notificationEmailAddress":" dummy@microsoft.com", 
       "updateIfExists":true 
    }
    ```

1. Open Azure Cloud Shell. Nadat de verificatie is geslaagd, selecteert u de knop **uploaden** (gemarkeerd in de volgende afbeelding) en uploadt u het bestand *input. json* voor Cloud shell opslag.  

    ![Project FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Ga naar de basismap in Cloud Shell. De naam van de Directory is standaard */home/\<gebruikers naam >* .
1. Voer de volgende opdracht in Cloud Shell in. Zorg ervoor dat u het pad naar het bestand *input. json* wijzigt en selecteer vervolgens de sleutel ENTER.

   ```bash
      wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
     Het installatie programma downloadt automatisch alle afhankelijkheden en bouwt de deployer. U wordt gevraagd om akkoord te gaan met de licentie voorwaarden voor Azure FarmBeats.

     - Als u akkoord gaat, voert u **Y** in om door te gaan naar de volgende stap.
     - Als u niet akkoord gaat, voert u **N**in en wordt de implementatie beëindigd.

1. Voer bij de prompt een toegangs token in voor de implementatie. Kopieer de gegenereerde code en meld u aan bij de [aanmeldings pagina](https://microsoft.com/devicelogin) van het apparaat met uw Azure-referenties.

    > [!NOTE]
    > Het token verloopt na 60 minuten. Als de service is verlopen, kunt u deze opnieuw starten door de implementatie opdracht opnieuw in te voeren.

1. Voer bij de prompt het wacht woord van uw Sentinel-account in.
   
   Het installatie programma valideert en start de implementatie, wat ongeveer 20 minuten kan duren.

   Nadat de implementatie is voltooid, ontvangt u de volgende uitvoer koppelingen:

    - **DATAHUB URL**: de Swagger-koppeling voor het proberen van Azure FarmBeats-api's.
    - **URL voor Accelerator**: de gebruikers interface voor het verkennen van de Azure FarmBeats Smart-Farm Accelerator.
    - **Bestand**van de implementatie-logbestand: het logboek bestand dat tijdens de implementatie wordt gemaakt. U kunt dit zo nodig voor het oplossen van problemen gebruiken.

## <a name="deployment-scenario-2"></a>Implementatie scenario 2

In dit scenario, dat eerder is beschreven in ' optie 2 ', gebruikt u uw bestaande registratie voor Azure Active Directory-apps om FarmBeats te implementeren. Hiertoe gaat u als volgt te werk:

1. Kopieer het volgende JSON-bestand, dat de Azure-toepassing client-ID en het wacht woord bevat in het bestand *input. json* , en sla het op. Zorg ervoor dat u het bestandspad op de lokale computer noteert.

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

1. Ga opnieuw naar Azure Cloud Shell en u bent nu geverifieerd.
1. Selecteer de knop **uploaden** (gemarkeerd in de volgende afbeelding) en upload het bestand *input. json* naar Cloud shell Storage.

    ![Project FarmBeats](./media/prepare-for-deployment/bash-2-1.png)

1. Ga naar de basismap in Cloud Shell. De naam van de Directory is standaard */home/\<gebruikers naam >* .
1. Voer de volgende opdracht in Cloud Shell in. Zorg ervoor dat u het pad naar het bestand *input. json* wijzigt en selecteer vervolgens de sleutel ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

     Het installatie programma downloadt automatisch alle afhankelijkheden en bouwt de deployer. U wordt gevraagd om akkoord te gaan met de licentie voorwaarden voor Azure FarmBeats.

     - Als u akkoord gaat, voert u **Y** in om door te gaan naar de volgende stap.
     - Als u niet akkoord gaat, voert u **N**in en wordt de implementatie beëindigd.

1. Voer bij de prompt een toegangs token in voor de implementatie. Kopieer de gegenereerde code en meld u aan bij de [aanmeldings pagina](https://microsoft.com/devicelogin) van het apparaat met uw Azure-referenties.

   Het installatie programma valideert en start de implementatie, wat ongeveer 20 minuten kan duren.

   Nadat de implementatie is voltooid, ontvangt u de volgende uitvoer koppelingen:

    - **DATAHUB URL**: de Swagger-koppeling voor het proberen van Azure FarmBeats-api's.
    - **URL voor Accelerator**: de gebruikers interface voor het verkennen van de Azure FarmBeats Smart-Farm Accelerator.
    - **Bestand**van de implementatie-logbestand: het logboek bestand dat tijdens de implementatie wordt gemaakt. U kunt dit zo nodig voor het oplossen van problemen gebruiken.

Raadpleeg [problemen oplossen](troubleshoot-project-farmbeats.md)als u problemen ondervindt.


## <a name="validate-the-deployment"></a>De implementatie valideren

### <a name="datahub"></a>Datahub

Nadat de Datahub-installatie is voltooid, ontvangt u de URL voor toegang tot Azure FarmBeats-Api's via de Swagger-interface in de indeling https://\<yourdatahub-website-name >. azurewebsites. net/Swagger.

1. Als u zich wilt aanmelden via Swagger, kopieert en plakt u de URL in uw browser.
1. Meld u aan met uw Azure Portal referenties.
1. Beschrijving Probeer het volgende ' Sanity-test ': 

     - Meld u aan bij de Swagger-Portal met behulp van de Datahub-koppeling, die u hebt ontvangen als uitvoer voor een geslaagde implementatie.
     - Uitgebreide typen Get API: Selecteer ' Probeer het uit '/Execute '
     - U ontvangt de server respons code 200 en geen uitzonde ring, zoals "403 niet-geautoriseerde gebruiker".

### <a name="accelerator"></a>Snelle

Nadat de installatie van de Accelerator is voltooid, ontvangt u de URL voor toegang tot de Azure FarmBeats-gebruikers interface in de indeling https://\<Accelerator-website-name >. azurewebsites. net.

1. Als u zich wilt aanmelden vanuit de Accelerator, kopieert en plakt u de URL in uw browser.
1. Meld u aan met uw Azure Portal referenties.

## <a name="upgrade"></a>Upgraden

De instructies voor de upgrade zijn vergelijkbaar met die voor de installatie van de eerste keer. Ga als volgt te werk:

1. Meld u aan bij de Azure Portal en selecteer vervolgens het gewenste abonnement en de Azure-Tenant.
1. Open Cloud Shell in de bovenste navigatie van het Azure Portal.

   ![Project FarmBeats](./media/prepare-for-deployment/navigation-bar-1.png)

1. Selecteer in de vervolg keuzelijst **omgeving** aan de linkerkant de optie **bash**.
1. Breng zo nodig wijzigingen aan in het bestand *input. json* en upload het vervolgens naar Cloud shell. U kunt bijvoorbeeld uw e-mail adres bijwerken voor de melding die u wilt ontvangen.
1. Typ of plak de volgende twee opdrachten in Cloud Shell. Zorg ervoor dat u het pad voor de *invoer. json* -bestand wijzigt en selecteer vervolgens de sleutel ENTER.

    ```bash
    wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```

   Het installatie programma vraagt u automatisch om de vereiste invoer tijdens runtime.

1. Voer een toegangs token in voor de implementatie. Kopieer de gegenereerde code en meld u aan bij de [aanmeldings pagina](https://microsoft.com/devicelogin) van het apparaat met uw Azure-referenties.
1. Voer uw Sentinel-wacht woord in.

   Het installatie programma valideert nu en begint met het maken van de resources, wat ongeveer 20 minuten kan duren.

   Nadat de implementatie is voltooid, ontvangt u de volgende uitvoer koppelingen:
    - **DATAHUB URL**: de Swagger-koppeling voor het proberen van Azure FarmBeats-api's.
    - **URL voor Accelerator**: de gebruikers interface voor het verkennen van de Azure FarmBeats Smart-Farm Accelerator.
    - **Bestand**van de implementatie-logbestand: het logboek bestand dat tijdens de implementatie wordt gemaakt. U kunt dit zo nodig voor het oplossen van problemen gebruiken.

> [!NOTE]
> Noteer de voor gaande waarden voor toekomstig gebruik.


## <a name="uninstall"></a>Verwijderen

Momenteel wordt de automatische verwijdering van Azure FarmBeats met het installatie programma niet ondersteund. Als u Datahub of Accelerator wilt verwijderen, verwijdert u in de Azure Portal de resource groep waarin deze onderdelen zijn geïnstalleerd of verwijdert u de resources hand matig.

Als u bijvoorbeeld Datahub en Accelerator in twee verschillende resource groepen hebt geïmplementeerd, kunt u deze resource groepen als volgt verwijderen:

1. Meld u aan bij Azure Portal.
1. Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant waar u Azure FarmBeats wilt implementeren.

   > [!NOTE]
   > Datahub is vereist voor een goede werking van de versnelling. Het is niet raadzaam om Datahub te verwijderen zonder de Accelerator te verwijderen.

1. Selecteer **resource groepen**en voer de naam in van de Datahub of Accelerator-resource groep die u wilt verwijderen.
1. Selecteer de naam van de resourcegroep. Typ de naam opnieuw om deze te bevestigen en selecteer vervolgens **verwijderen** om de resource groep en alle onderliggende resources te verwijderen.
   U kunt ook elke resource hand matig verwijderen, een methode die niet wordt aangeraden.
1. Als u Datahub wilt verwijderen of verwijderen, gaat u naar de resource groep rechtstreeks op Azure en verwijdert u de resource groep.

## <a name="next-steps"></a>Volgende stappen

U hebt geleerd hoe u Azure FarmBeats implementeert. Vervolgens leert u hoe u [FarmBeats-Farms maakt](manage-farms.md#create-farms).
