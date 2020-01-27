---
title: Azure FarmBeats installeren
description: In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement
author: usha-rathnavel
ms.topic: article
ms.date: 12/11/2019
ms.author: usrathna
ms.openlocfilehash: d1a6bdfb38431e18eb305b223ce8ee2467804052
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482450"
---
# <a name="install-azure-farmbeats"></a>Azure FarmBeats installeren

In dit artikel wordt beschreven hoe u Azure FarmBeats installeert in uw Azure-abonnement.

Azure FarmBeats is een Business-to-Business-aanbieding die beschikbaar is in azure Marketplace. Hiermee kunnen aggregatie van agrarische gegevens sets worden verzameld over providers en kan er inzicht hebben in het genereren van actie bare inzichten. Azure FarmBeats doet dit door u in staat te stellen om modellen van kunst matige intelligentie (AI) of machine learning (ML) te bouwen op basis van gegevens sets met zekering. De twee belangrijkste onderdelen van Azure FarmBeats zijn:

- **Datahub**: een API-laag die aggregatie, normalisatie en contextualization van verschillende agrarische gegevens sets in verschillende providers mogelijk maakt.

- **Accelerator**: een voor beeld-webtoepassing die is gebouwd op Datahub. Het gaat hier om de ontwikkeling en visualisatie van uw model. De Accelerator maakt gebruik van Azure FarmBeats-Api's om de visualisatie van opgenomen sensor gegevens te demonstreren als grafieken en visualisatie van model uitvoer als kaarten.

## <a name="before-you-start"></a>Voordat u begint
### <a name="components-installed"></a>Geïnstalleerde onderdelen

Wanneer u Azure FarmBeats installeert, worden de volgende resources ingericht in uw Azure-abonnement:

| Geïnstalleerde Azure-resources  | Azure FarmBeats-onderdeel  |
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
| Azure Maps-account       |     Snelle    |
| Event hub-naam ruimte    |     Datahub      |
| Logische app      |  Datahub       |
| Opslagaccount      |     Datahub & Accelerator      |
| Time Series Insights     |    Datahub    |

### <a name="costs-incurred"></a>Gemaakte kosten

De kosten van Azure FarmBeats zijn een samen vatting van de kosten van de onderliggende Azure-Services. Prijs informatie voor Azure-Services kan worden berekend met behulp van de [prijs calculator](https://azure.microsoft.com/pricing/calculator). De werkelijke kosten van de totale installatie variëren op basis van het gebruik. De constante status kosten voor de twee onderdelen zijn:

* Datahub-minder dan $10 per dag
* Accelerator-minder dan $2 per dag

### <a name="regions-supported"></a>Regio's worden ondersteund

Momenteel wordt Azure FarmBeats ondersteund in open bare Cloud omgevingen in de volgende regio's:
* Australië - oost
* US - centraal
* VS - oost
* VS - oost 2
* US - west
* US - west 2
* Europa - noord
* Europa - west
* Azië - zuidoost

### <a name="time-taken"></a>Gebruikte tijd

De volledige installatie van Azure FarmBeats, met inbegrip van de voor bereiding en installatie, duurt minder dan een uur.

## <a name="prerequisites"></a>Vereisten    

Voordat u begint met de daad werkelijke installatie van Azure FarmBeats, moet u de volgende stappen uitvoeren:

### <a name="create-sentinel-account"></a>Verklikker account maken
Met de Azure FarmBeats-installatie kunt u gratis satelliet afbeelding van het Euro pees ruimte [-](https://scihub.copernicus.eu/) Instituut voor uw farm ontvangen. Als u deze configuratie wilt configureren, hebt u een Sentinel-account nodig.

Volg de stappen voor het maken van een gratis account met Sentinel:

1. Ga naar de officiële [registratie](https://scihub.copernicus.eu/dhus/#/self-registration) pagina.
2. Geef de vereiste gegevens op (voor naam, achternaam, gebruikers naam, wacht woord en e-mail-ID) en vul het formulier in.
3. Er wordt een verificatie koppeling verzonden naar de ID van het geregistreerde e-mail bericht. Selecteer de koppeling die in het e-mail bericht wordt gegeven en voltooi de verificatie.

Het registratie proces is voltooid zodra u de verificatie hebt voltooid. Noteer uw **Sentinel-gebruikers naam** en **verklikker wachtwoord**.

### <a name="decide-subscription-and-region"></a>Abonnement en regio kiezen

U hebt de Azure-abonnements-ID en de regio waar u Azure FarmBeats wilt installeren. Kies een van de regio's die worden vermeld in de sectie regio's die worden [ondersteund](#regions-supported) .

Noteer de ID van het **Azure-abonnement** en de **Azure-regio**.

### <a name="verify-permissions"></a>Machtigingen verifiëren

U moet controleren of u voldoende bevoegdheden en machtigingen hebt in de Azure-Tenant die u wilt installeren van Azure FarmBeats.

U kunt uw toegangs machtigingen in de Azure Portal controleren door de instructies te volgen op [op rollen gebaseerd toegangs beheer](https://docs.microsoft.com/azure/role-based-access-control/check-access)

Voor het installeren van Azure FarmBeats hebt u de volgende machtigingen nodig:
- Tenant-Lees toegang
- Abonnement: Inzender of eigenaar
- Resource groep-eigenaar.

Bovendien vereist Azure FarmBeats Azure Active Directory toepassings registraties. Er zijn twee manieren om de vereiste Azure AD-installatie te volt ooien:

Voor **Beeld 1**: u hebt **Schrijf** machtigingen in de Azure-Tenant waarop u installeert. Dit betekent dat u over de benodigde machtigingen beschikt om de registratie van AAD-apps dynamisch te maken tijdens de installatie.

U kunt direct door gaan naar de sectie [de inschrijving voor Marketplace volt ooien](#complete-azure-marketplace-sign-up) .


Voor **Beeld 2**: u hebt geen **Schrijf** machtigingen in de Azure-Tenant. Dit is gebruikelijk bij het installeren van Azure FarmBeats in het Azure-abonnement van uw bedrijf en uw **Schrijf** toegang is beperkt tot alleen de resource groep die u hebt.
Vraag uw IT-beheerder in dit geval de onderstaande stappen uit om de registratie van de Azure AD-app in de Azure Portal automatisch te genereren en te volt ooien.

1. Down load en pak het pakket voor de [Aad-app-Generator](https://aka.ms/FarmBeatsAADScript) naar uw lokale machine.
2. Meld u aan bij de Azure Portal en selecteer uw abonnement en de Azure AD-Tenant.
3. Start Cloud Shell vanuit de werk balk boven aan het Azure Portal.

    ![Project FarmBeats](./media/install-azure-farmbeats/navigation-bar-1.png)

4. Kies Power shell als de voorkeurs shell-ervaring. De eerste keer dat gebruikers wordt gevraagd om een abonnement te selecteren en een opslag account te maken. Voltooi de installatie volgens de instructies.
5. Upload het script (uit stap 1) naar Cloud Shell en noteer de locatie van het geüploade bestand.

    > [!NOTE]
    > Standaard wordt het bestand geüpload naar uw basismap.

6. Ga naar de basismap met behulp van de opdracht ' cd ' en voer het volgende script uit:

      ```azurepowershell-interactive
            ./create_aad_script.ps1
      ```
7. Voer de naam van de **Datahub-website** en de naam van de **Accelerator website** in. Noteer de uitvoer van het script en deel het met de persoon die Azure FarmBeats installeert.

Zodra de IT-beheerder u de vereiste details heeft, noteert u de **Aad-client-id, het Aad-client geheim, de naam van de Datahub-website & de naam**van de website van de Accelerator.

   > [!NOTE]
   > Als u de volgende instructies van case 2 wilt, vergeet dan niet om de AAD-client-ID & het AAD-client geheim toe te voegen als afzonderlijke para meters in het [parameter bestand](#prepare-parameters-file)

U hebt nu alle informatie die nodig is om door te gaan naar de volgende sectie.

### <a name="complete-azure-marketplace-sign-up"></a>Azure Marketplace-aanmelding volt ooien

U moet de registratie van de Azure FarmBeats-aanbieding in azure Marketplace volt ooien voordat u het installatie proces van de Cloud-shell kunt starten. Volg de onderstaande stappen om de registratie te volt ooien:

1.  Meld u aan bij Azure Portal. Selecteer uw account in de rechter bovenhoek en schakel over naar de Azure AD-Tenant waar u Azure FarmBeats wilt installeren.

2.  Ga naar Azure Marketplace in de portal en zoek naar **Azure FarmBeats** in Marketplace

3.  Er wordt een nieuw venster met een overzicht van Azure FarmBeats weer gegeven. Selecteer de **maken**.

4.  Een nieuw venster wordt weergegeven. Voltooi het registratie proces door het juiste abonnement, de resource groep en de locatie te kiezen waarop u Azure FarmBeats wilt installeren.

5.  Zodra de ingevoerde gegevens zijn gevalideerd, selecteert u **OK**. De pagina Gebruiksvoorwaarden wordt weer gegeven. Bekijk de voor waarden en selecteer **maken** om het registratie proces te volt ooien.

Met deze stap wordt het registratie proces in azure Marketplace voltooid. U bent nu klaar om de voor bereiding van het parameter bestand te starten.

### <a name="prepare-parameters-file"></a>Parameter bestand voorbereiden
De laatste stap in de vereisten fase is het maken van een JSON-bestand dat als invoer wordt gebruikt tijdens de installatie van Cloud Shell. De para meters in het JSON-bestand moeten worden vervangen door de juiste waarden.

Hieronder vindt u een voor beeld van een JSON-bestand. Down load het voor beeld en werk de vereiste gegevens bij.

```json
{  
    "sku":"both",
    "subscriptionId":"da9xxxec-dxxf-4xxc-xxx21-xxx3ee7xxxxx",
    "datahubResourceGroup":"dummy-test-dh1",
    "location":"westus2",
    "datahubWebsiteName":"dummy-test-dh1",
    "acceleratorResourceGroup":"dummy-test-acc1",
    "acceleratorWebsiteName":"dummy-test-acc1",
    "sentinelUsername":"dummy-dev",
    "notificationEmailAddress":"dummy@yourorg.com",
    "updateIfExists":true
}
```

U kunt de onderstaande parameter tabel raadplegen voor meer informatie over elk van de para meters.

| Parameter | Waarde|
|--- | ---|
|sku  | Biedt de gebruiker een keuze om zowel Datahub als Accelerator of alleen de Datahub te installeren. Als u alleen Datahub wilt installeren, gebruikt u ' Datahub '. Als u Datahub en Accelerator wilt installeren, gebruikt u ' beide '|
|subscriptionId | Hiermee geeft u het Azure-abonnement op voor het installeren van Azure FarmBeats|
|datahubResourceGroup| Hiermee geeft u de naam van de resource groep voor uw Datahub-resources op. Voer hier de naam in van de resource groep die u hebt gemaakt in azure Marketplace|
|location |De locatie/Azure-regio waar u Azure FarmBeats wilt installeren|
|datahubWebsiteName  | Het unieke URL-voor voegsel voor uw Datahub-webtoepassing |
|acceleratorResourceGroup  | Hiermee geeft u de naam van de resource groep voor uw resources voor Accelerators op|
|acceleratorWebsiteName |Het unieke URL-voor voegsel voor uw webtoepassing voor Accelerators|
|sentinelUsername | De gebruikers naam voor het ophalen van de afbeelding van de verklikker satelliet|
|notificationEmailAddress  | Het e-mail adres voor het ontvangen van meldingen voor waarschuwingen die u in uw Datahub configureert|
|updateIfExists| Beschrijving Een para meter die alleen in het parameter bestand moet worden meegenomen als u een bestaand exemplaar van Azure FarmBeats wilt bijwerken. Voor een upgrade moeten andere details, zoals de namen en locaties van de resource groep, hetzelfde zijn|
|aadAppClientId | Beschrijving Een para meter die alleen in het parameter bestand moet worden opgenomen als u een vooraf gemaakte AAD-app gebruikt. Raadpleeg Case 2 in het gedeelte [machtigingen controleren](#verify-permissions) voor meer informatie |
|aadAppClientSecret  | Beschrijving Beschrijving Een para meter die alleen in het parameter bestand moet worden opgenomen als u een vooraf gemaakte AAD-app gebruikt. Raadpleeg Case 2 in het gedeelte [machtigingen controleren](#verify-permissions) voor meer informatie|

Maak op basis van de bovenstaande tabel en het JSON-voorbeeld bestand het JSON-bestand voor de para meters en sla het op uw lokale computer op.

## <a name="install"></a>Installatie

De werkelijke installatie van Azure FarmBeats-resources vindt plaats in Cloud Shell opdracht regel interface op basis van een browser met behulp van de bash-omgeving. Volg de onderstaande instructies voor het installeren van Azure FarmBeats:

1. Meld u aan bij Azure Portal. Selecteer het Azure-abonnement en de Tenant waarvoor u Azure FarmBeats wilt installeren.
2. Start **Cloud shell** vanuit de werk balk in de rechter bovenhoek van de Azure Portal.
3. Kies bash als de voorkeurs shell-ervaring. Selecteer de knop **uploaden** (gemarkeerd in de onderstaande afbeelding) en upload het JSON-bestand voor bereide para meters.

      ![Project FarmBeats](./media/install-azure-farmbeats/bash-2-1.png)

4. **Kopieer** de onderstaande opdracht en **Vervang de \<gebruikers naam >** door de juiste waarde zodat de opdracht naar het juiste pad van het geüploade bestand wijst.

    ```bash
          wget -O farmbeats-installer.sh https://aka.ms/AzureFarmbeatsInstallerScript && bash farmbeats-installer.sh /home/<username>/input.json
    ```
5. Voer de gewijzigde opdracht uit om het installatie proces te starten. U wordt gevraagd het volgende te doen:
 - Ga akkoord met de licentie voorwaarden voor **Azure FarmBeats** . Voer ' Y ' in om door te gaan naar de volgende stap als u akkoord gaat met de Gebruiksvoorwaarden. Voer ' N ' in om de installatie te beëindigen als u niet akkoord gaat met de gebruiks voorwaarden.

 - Vervolgens wordt u gevraagd om een toegangs token voor de installatie op te geven. Kopieer de gegenereerde code en meld u aan bij de [aanmeldings pagina](https://microsoft.com/devicelogin) van het apparaat met uw **Azure-referenties**.

 - Zodra het aanmelden is voltooid, wordt gevraagd om het wacht woord voor uw Sentinel-account. Voer het **wacht woord**voor het Sentinel-account in.

6. Het parameter bestand wordt gevalideerd en de installatie van de Azure-resources wordt gestart. Het duurt ongeveer **25 minuten** om de installatie te volt ooien.    
> [!NOTE]
> Inactieve Cloud Shell sessies verlopen na **20 minuten**. Houd de Cloud Shell sessie actief terwijl het installatie programma de Azure-resources implementeert. Als er een time-out optreedt voor de sessie, moet u het installatie proces opnieuw starten.

Zodra de installatie is voltooid, ontvangt u de volgende uitvoer koppelingen:
* **DATAHUB URL**: de Swagger-koppeling voor toegang tot de Datahub-api's.
* **URL voor Accelerator**: de webtoepassing voor het verkennen van de Azure FarmBeats-Accelerator.
* **Installer-logboek bestand**: het logboek bestand met details van de installatie. Dit logboek bestand kan worden gebruikt voor het oplossen van problemen met de installatie, indien nodig.

U kunt de voltooiing van uw Azure FarmBeats-installatie controleren door de volgende controles uit te voeren:

**Datahub**
1. Meld u aan bij de beschik bare Accelerator-URL (in de notatie **https://\<yourdatahub-website-name >. azurewebsites. net/Swagger**) met uw Azure-referenties.
2. U zou de verschillende FarmBeats-API-objecten moeten kunnen zien en REST-bewerkingen op de Api's moeten uitvoeren.

**Snelle**
1. Meld u aan bij de beschik bare Accelerator-URL (in de notatie **https://\<youraccelerator-website-name >. azurewebsites. net/Swagger**) met uw Azure-referenties.
2. U zou de gebruikers interface van de Accelerator moeten kunnen zien met een optie voor het maken van Farms in uw browser.

De mogelijkheid om de bovenstaande bewerkingen uit te voeren, wijst op een geslaagde installatie van Azure FarmBeats.

## <a name="upgrade"></a>Upgraden
Als u in de open bare preview-versie een bestaande installatie van Azure FarmBeats wilt bijwerken, moet u de installatie opdracht opnieuw uitvoeren in Cloud Shell opnieuw, met een extra para meter '**updateIfExists**' in het bestand para meters ingesteld op '**True**'. Raadpleeg de laatste regel van het onderstaande JSON-voor beeld voor de update-para meter.

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
De opdracht werkt de bestaande installatie van Azure FarmBeats bij naar de meest recente versie en biedt u de uitvoer koppelingen.

## <a name="uninstall"></a>Verwijderen

Voer de volgende stappen uit om Azure FarmBeats Datahub of Accelerator te verwijderen:

1.  Meld u aan bij de Azure Portal en **Verwijder de resource groepen** waarin deze onderdelen zijn geïnstalleerd.

2.  Ga naar Azure Active Directory & **de Azure AD-toepassing te verwijderen** die aan de Azure FarmBeats-installatie is gekoppeld. Hiermee wordt de installatie van Azure FarmBeats verwijderd uit uw Azure-abonnement.

## <a name="next-steps"></a>Volgende stappen
U hebt geleerd hoe u Azure FarmBeats kunt installeren in uw Azure-abonnement. Nu leert u hoe u [gebruikers kunt toevoegen](manage-users-in-azure-farmbeats.md#manage-users) aan uw Azure FarmBeats-exemplaar.
