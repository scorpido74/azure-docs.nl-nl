---
title: Problemen met Azure FarmBeats oplossen
description: In dit artikel wordt beschreven hoe u Azure FarmBeats oplossen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a429a1e454e73a1a9d544e308e5b2d60052d91a9
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349777"
---
# <a name="troubleshoot"></a>Problemen oplossen

In dit artikel vindt u oplossingen voor veelvoorkomende Azure FarmBeats-problemen.

Voor extra hulp kunt farmbeatssupport@microsoft.comu contact met ons opnemen via . Zorg ervoor dat u het bestand **deployer.log** in uw e-mail opneemt.

Ga als volgt te werk om het bestand **deployer.log** te downloaden:

1. Meld u aan bij **azure portal** en selecteer uw abonnement en Azure AD-tenant.
2. Open Cloud Shell via de bovenste navigatiebalk van de Azure Portal.
3. Selecteer **Bash** als de voorkeurscloudshell-ervaring.
4. Selecteer het gemarkeerde pictogram en selecteer vervolgens in de vervolgkeuzelijst **Download**.

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. Voer in het volgende deelvenster het pad naar het **bestand deployer.log** in. Voer bijvoorbeeld **farmbeats-deployer.log in**.

## <a name="sensor-telemetry"></a>Sensortelemetrie

### <a name="cant-view-telemetry-data"></a>Telemetriegegevens kunnen niet worden bekeken

**Symptoom:** Apparaten of sensoren worden geïmplementeerd en u hebt FarmBeats gekoppeld aan uw apparaatpartner, maar u geen telemetriegegevens op FarmBeats krijgen of bekijken.

**Corrigerende maatregelen**:

1. Ga naar uw FarmBeats Datahub-brongroep.   
2. Selecteer de **gebeurtenishub** (DatafeedEventHubNamespace) en controleer vervolgens op het aantal binnenkomende berichten.
3. Voer een van de volgende bewerkingen uit:   
   - Als er *geen binnenkomende berichten*zijn, neemt u contact op met uw apparaatpartner.  
   - Als er *binnenkomende berichten* farmbeatssupport@microsoft.comzijn, neemt u contact op met . Voeg uw Datahub- en Accelerator-logboeken toe en vastgelegde telemetrie.

Als u wilt weten hoe u logboeken downloaden, gaat u naar de sectie [Logboeken handmatig verzamelen.](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Kan telemetriegegevens niet bekijken na het innemen van historische/streaminggegevens van uw sensoren

**Symptoom:** Apparaten of sensoren worden geïmplementeerd en u hebt de apparaten/sensoren op FarmBeats gemaakt en telemetrie ingenomen naar de EventHub, maar u geen telemetriegegevens op FarmBeats krijgen of bekijken.

**Corrigerende maatregelen**:

1. Zorg ervoor dat u de partnerregistratie correct hebt gedaan - u dit controleren door naar uw datahub-branie te gaan, naar /Partner API te navigeren, een get te doen en te controleren of de partner is geregistreerd. Zo niet, volg [dan de stappen hier](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) om partner toe te voegen.
2. Controleer of u de juiste telemetrieberichtindeling hebt gebruikt:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>De verbindingstekenreeks Azure Event Hubs niet hebben

**Corrigerende maatregelen**:

1. Ga in Datahub Swagger naar de Partner API.
2. Selecteer **Try** > **it out uitvoeren.** > **Execute**
3. Let op de partner-ID van de sensorpartner waarin u geïnteresseerd bent.
4. Ga terug naar de Partner-API en selecteer **>op-\<en>. **
5. Geef de partner-id op vanaf stap 3 en selecteer **Uitvoeren**.

   De API-respons moet de verbindingstekenreeks Gebeurtenishubs hebben.

### <a name="device-appears-offline"></a>Apparaat wordt offline weergegeven

**Symptomen:** Apparaten zijn geïnstalleerd en u hebt FarmBeats gekoppeld aan uw apparaatpartner. De apparaten zijn online en verzenden telemetriegegevens, maar ze worden offline weergegeven.

**Corrigerende actie:** het rapportageinterval is niet geconfigureerd voor dit apparaat. Neem contact op met de fabrikant van het apparaat om het rapportage-interval in te stellen. 

### <a name="error-deleting-a-device"></a>Fout bij het verwijderen van een apparaat

Terwijl u een apparaat loost, u een van de volgende veelvoorkomende foutscenario's tegenkomen:  

**Bericht**: "Apparaat wordt verwezen in sensoren: Er zijn een of meer sensoren gekoppeld aan het apparaat. Verwijder de sensoren en verwijder het apparaat."  

**Betekenis**: Het apparaat is gekoppeld aan meerdere sensoren die in de farm worden geïmplementeerd.   

**Corrigerende maatregelen**:  

1. Verwijder de sensoren die aan het apparaat zijn gekoppeld via Accelerator.  
2. Als u de sensoren aan een ander apparaat wilt koppelen, vraagt u uw apparaatpartner hetzelfde te doen.  
3. Verwijder het apparaat `DELETE API` met behulp van een aanroep en stel de krachtparameter in als *true*.  

**Bericht:**"Apparaat wordt verwezen in apparaten als ParentDeviceId: Er zijn een of meer apparaten die zijn gekoppeld aan dit apparaat als onderliggende apparaten. Verwijder ze en verwijder dit apparaat."  

**Betekenis:** uw apparaat heeft andere apparaten die eraan zijn gekoppeld.  

**Corrigerende maatregelen**

1. Verwijder de apparaten die aan dit specifieke apparaat zijn gekoppeld.  
2. Verwijder het specifieke apparaat.  

    > [!NOTE]
    > U een apparaat niet verwijderen als er sensoren aan zijn gekoppeld. Zie de sectie **Sensor verwijderen** in [Sensorgegevens ophalen van sensorpartners](get-sensor-data-from-sensor-partner.md)voor meer informatie over het verwijderen van bijbehorende sensoren.

    > Partners hebben geen toegang tot het verwijderen van een apparaat of sensor. Alleen beheerders hebben toegang tot hetzelfde te doen.


## <a name="issues-with-jobs"></a>Problemen met taken

### <a name="farmbeats-internal-error"></a>Interne farmbeats-fout

**Bericht:**"Interne fout FarmBeats, zie handleiding voor probleemoplossing voor meer details".

**Corrigerende maatregelen:** dit probleem kan het gevolg zijn van een tijdelijke fout in de gegevenspijplijn. Maak de taak opnieuw. Als de fout blijft bestaan, voegt u het foutbericht toe FarmBeatsSupport@microsoft.comin een bericht op het FarmBeats-forum of neemt u contact op met .

## <a name="accelerator-troubleshooting"></a>Acceleratorprobleemoplossing

### <a name="access-control"></a>Toegangsbeheer

**Probleem:** U ontvangt een fout terwijl u een roltoewijzing toevoegt.

**Bericht:**'Geen overeenkomende gebruikers gevonden'.

**Corrigerende actie:** controleer de e-id waarvoor u een roltoewijzing probeert toe te voegen. De e-id moet exact overeenkomen met de id, die voor die gebruiker is geregistreerd in de Active Directory. Als de fout blijft bestaan, voegt u het foutbericht toe FarmBeatsSupport@microsoft.comin een bericht op het FarmBeats-forum of neemt u contact op met .

### <a name="unable-to-log-in-to-accelerator"></a>Kan niet inloggen op Accelerator

**Bericht:**"Fout: u bent niet bevoegd om de service te bellen. Neem contact op met de beheerder voor autorisatie."

**Corrigerende actie:** Vraag de beheerder om u toestemming te geven voor toegang tot de FarmBeats-implementatie. Dit kan door een post van de RoleAssignment API's te doen of via het toegangsbesturingselement in het deelvenster **Instellingen** in Accelerator.  

Als u al toegang hebt gekregen en met deze fout wordt geconfronteerd, probeert u het opnieuw door de pagina te vernieuwen. Als de fout blijft bestaan, voegt u het foutbericht toe FarmBeatsSupport@microsoft.comin een bericht op het FarmBeats-forum of neemt u contact op met .

![Project FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problemen  

**Probleem:** U hebt een acceleratorfout van onbepaalde oorzaak ontvangen.

**Bericht**: "Fout: er is een onbekende fout opgetreden."

**Corrigerende actie**: Deze fout treedt op als u de pagina te lang inactief laat. Vernieuw de pagina.  

Als de fout blijft bestaan, voegt u het foutbericht toe FarmBeatsSupport@microsoft.comin een bericht op het FarmBeats-forum of neemt u contact op met .

**Probleem:** FarmBeats Accelerator toont niet de nieuwste versie, zelfs niet nadat u FarmBeatsDeployment hebt geüpgraded.

**Corrigerende maatregelen:** Deze fout treedt op als gevolg van persistentie van de servicemedewerker in de browser. Ga als volgt te werk:

1. Sluit alle browsertabbladen met Accelerator open en sluit het browservenster.
2. Start een nieuw exemplaar van de browser en herlaad de Accelerator URI. Deze actie laadt de nieuwe versie van Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemen met beeldmateriaal

### <a name="wrong-username-or-password"></a>Verkeerde gebruikersnaam of wachtwoord

**Bericht voor het mislukken:**'Volledige verificatie is vereist om toegang te krijgen tot deze bron'.

**Corrigerende maatregelen**:

Voer een van de volgende handelingen uit:

- Voer het installatieprogramma opnieuw uit voor het upgraden van Datahub met de juiste gebruikersnaam en wachtwoord.
- Voer de mislukte taak opnieuw uit of voer een satellietindextaak uit voor een datumbereik van 5 tot 7 dagen en controleer vervolgens of de taak succesvol is.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel-hub: verkeerde URL of site niet toegankelijk 

**Job failure bericht:**"Oeps, er ging iets mis. De pagina die u probeerde te openen, is (tijdelijk) niet beschikbaar." 

**Corrigerende maatregelen**:
1. Open [Sentinel](https://scihub.copernicus.eu/dhus/) in uw browser om te zien of de website toegankelijk is. 
2. Als de website niet toegankelijk is, controleert u of een firewall, bedrijfsnetwerk of andere blokkeringssoftware de toegang tot de website verhindert en neemt u de nodige stappen om de Sentinel-URL toe te staan. 
3. Voer de mislukte taak opnieuw uit of voer een satellietindextaak uit voor een datumbereik van 5 tot 7 dagen en controleer vervolgens of de taak succesvol is.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-server: omlaag voor onderhoud

**Job failure bericht**: "De Copernicus Open Access Hub zal binnenkort terug! Sorry voor het ongemak, we voeren wat onderhoud op dit moment. We zijn binnenkort weer online!" 

**Corrigerende maatregelen**:

Dit probleem kan optreden als er onderhoudswerkzaamheden worden uitgevoerd op de Sentinel-server.

1. Als een taak of pijplijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in. 

   Ga voor informatie over geplande of ongeplande Sentinel-onderhoudsactiviteiten naar de [Copernicus Open Access Hub-nieuwssite.](https://scihub.copernicus.eu/news/)  

2. Voer de mislukte taak opnieuw uit of voer een satellietindextaak uit voor een datumbereik van 5 tot 7 dagen en controleer vervolgens of de taak succesvol is.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: Maximaal aantal verbindingen bereikt

**Bericht voor het mislukken:**"Maximaal aantal twee\<gelijktijdige stromen bereikt door de gebruikersnaam> van de gebruiker."

**Betekenis:** Als een taak mislukt omdat het maximum aantal verbindingen is bereikt, wordt hetzelfde Sentinel-account gebruikt in een andere software-implementatie.

**Corrigerende maatregelen**: Probeer een van de volgende opties:

* Maak een nieuw Sentinel-account aan en voer het installatieprogramma opnieuw uit om Datahub te upgraden met een nieuwe Sentinel-gebruikersnaam en -wachtwoord.  
* Voer de mislukte taak opnieuw uit of voer een satellietindextaak uit voor een datumbereik van 5 tot 7 dagen en controleer vervolgens of de taak succesvol is.

### <a name="sentinel-server-refused-connection"></a>Sentinel-server: geweigerde verbinding 

**Bericht voor het mislukken**: http://172.30.175.69:8983/solr/dhus"Server weigerde verbinding op: ." 

**Corrigerende maatregelen:** Dit probleem kan optreden als er onderhoudswerkzaamheden worden uitgevoerd op de Sentinel-server. 
1. Als een taak of pijplijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in. 

   Ga voor informatie over geplande of ongeplande Sentinel-onderhoudsactiviteiten naar de [Copernicus Open Access Hub-nieuwssite.](https://scihub.copernicus.eu/news/)  

2. Voer de mislukte taak opnieuw uit of voer een satellietindextaak uit voor een datumbereik van 5 tot 7 dagen en controleer vervolgens of de taak succesvol is.

### <a name="soil-moisture-map-has-white-areas"></a>Bodemvocht kaart heeft witte gebieden 

**Probleem**: De bodemvochtkaart is gegenereerd, maar de kaart heeft meestal witte gebieden.

**Corrigerende maatregelen**: Dit probleem kan optreden als de satellietindexen die zijn gegenereerd voor de tijd waarvoor de kaart is aangevraagd NDVI-waarden hebben die minder dan 0,3 zijn. Ga voor meer informatie naar [de Technische Gids van Sentinel.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)
1. Voer de taak opnieuw uit voor een ander datumbereik en controleer of de NDVI-waarden in de satellietindexen meer dan 0,3 zijn

## <a name="collect-logs-manually"></a>Logboeken handmatig verzamelen

[Azure Storage Explorer installeren en implementeren]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Azure Data Factory-taaklogboeken verzamelen in Datahub

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de brongroep FarmBeats Datahub.

    > [!NOTE]
    > Selecteer de Gegevenshub-brongroep die u hebt opgegeven tijdens de installatie van FarmBeats.

3. Zoek in het dashboard **van resourcegroep** naar het *opslagaccount van\* datahublogs.* Zoek bijvoorbeeld naar **datahublogsmvxmq**.  
4. Selecteer **in** de kolom Naam het opslagaccount om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het deelvenster **datahubblogs\* ** de optie **Openen in Explorer** om de toepassing Azure Storage **Explorer** te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens taaklogboeken**.
7. Selecteer **Downloaden**in het venster **Taaklogboeken** .
8. Download de logboeken naar een lokale map op uw machine.
9. E-mail het gedownloade farmbeatssupport@microsoft.com.zip-bestand naar .

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Azure Data Factory-taaklogboeken verzamelen in Accelerator

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de resourcegroep FarmBeats Accelerator.

    > [!NOTE]
    > Selecteer de resourcegroep Accelerator die u hebt opgegeven tijdens de installatie farmbeats.

3. Zoek in het dashboard **Resourcegroep** naar het *\* opslagaccount.* Zoek bijvoorbeeld naar **storagedop4k.\***
4. Selecteer het opslagaccount in de kolom **Naam** om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het **opslagvenster\* ** **Openen in Explorer** om de Azure Storage Explorer-toepassing te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens taaklogboeken**.
7. Selecteer **Downloaden**in het venster **Taaklogboeken** .
8. Download de logboeken naar een lokale map op uw machine.
9. E-mail het gedownloade farmbeatssupport@microsoft.com.zip-bestand naar .


### <a name="collect-datahub-app-service-logs"></a>Gegevenshub-app-servicelogboeken verzamelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de brongroep FarmBeats Datahub.

    > [!NOTE]
    > Selecteer de Gegevenshub-brongroep die u hebt opgegeven tijdens de installatie van FarmBeats.

3. Zoek in de brongroep naar het *\* datahublogs-opslagaccount.* Zoek bijvoorbeeld naar **datahublogsmvxmq\***.
4. Selecteer het opslagaccount in de kolom **Naam** om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het deelvenster **datahubblogs\* ** de optie **Openen in Explorer** om de Azure Storage Explorer-toepassing te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens appinsights-logs**.
7. Selecteer **Downloaden**in het deelvenster **appinsights-logs** .
8. Download de logboeken naar een lokale map op uw machine.
9. E-mail het gedownloade farmbeatssupport@microsoft.com.zip-bestand naar .

### <a name="collect-accelerator-app-service-logs"></a>Servicelogboeken van accelerator-app verzamelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de resourcegroep FarmBeats Accelerator.

    > [!NOTE]
    > Selecteer de resourcegroep FarmBeats Accelerator die is geleverd tijdens de installatie farmbeats.

3. Zoek in de brongroep naar het *\* opslagaccount.* Zoek bijvoorbeeld naar **storagedop4k.\***
4. Selecteer het opslagaccount in de kolom **Naam** om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het **opslagvenster\* ** **Openen in Explorer** om de Azure Storage Explorer-toepassing te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens appinsights-logs**.
7. Selecteer **Downloaden**in het deelvenster **appinsights-logs** .
8. Download de logboeken naar een lokale map op uw machine.
9. E-mail de farmbeatssupport@microsoft.comgedownloade map naar .

## <a name="known-issues"></a>Bekende problemen

## <a name="batch-related-issues"></a>Batchgerelateerde problemen

**Foutbericht:**'Het regionale accountquotum van Batchaccounts voor het opgegeven abonnement is bereikt'.

**Corrigerende maatregelen**: Verhoog het quotum of verwijder de ongebruikte batchaccounts en voer de implementatie opnieuw uit.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Azure Active Directory (Azure AD)-gerelateerde problemen

**Foutbericht:**"Kan de vereiste instellingen niet bijwerken naar Azure AD App d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Onvoldoende bevoegdheden om de bewerking te voltooien. Controleer of de bovenstaande instellingen goed zijn geconfigureerd voor de Azure AD-app."

**Betekenis:** de configuratie van de Azure AD-app-app is niet goed voltooid.  

**Corrigerende maatregelen:** Vraag de IT-beheerder (de persoon met leestoegang voor tenant) om ons [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) te gebruiken voor het maken van de Azure AD-appregistratie. Dit script zorgt automatisch voor de configuratiestappen.

**Foutbericht**: 'Kan geen nieuwe\<toepassingsnaam\>van Active Directory Application ' maken in deze tenant: een ander object met dezelfde waarde voor URI's van eigenschap-id bestaat al."

**Betekenis**: Er bestaat al een Azure AD-app-registratie met dezelfde naam.

**Corrigerende maatregelen:** verwijder de bestaande Azure AD-app-registratie of gebruik deze opnieuw voor installatie. Als u de bestaande Azure AD-app-registratie opnieuw gebruikt, geeft u de toepassings-id en het clientgeheim door aan het installatieprogramma en implementeert u opnieuw.

## <a name="issues-with-the-inputjson-file"></a>Problemen met het bestand input.json

**Fout:** er is een foutleesinvoer van het *bestand input van input.json.*

**Corrigerende maatregelen**: Dit probleem ontstaat meestal als gevolg van een fout bij het opgeven van het juiste filepad of de naam *van input.json* aan het installatieprogramma. Breng de juiste correcties aan en probeer de implementatie opnieuw.

**Fout:** er is een fout bij het ontleden van waarden in het bestand *input.json.*

**Corrigerende maatregelen**: Dit probleem ontstaat meestal als gevolg van onjuiste waarden in het *bestand input.json.* Breng de juiste correcties aan en probeer de implementatie opnieuw.

## <a name="high-cpu-usage"></a>Hoog CPU-gebruik

**Fout:** U ontvangt een e-mailwaarschuwing die verwijst naar een **waarschuwing voor een hoog CPU-gebruik.** 

**Corrigerende maatregelen**: 
1. Ga naar uw FarmBeats Datahub-brongroep.
2. Selecteer de **app-service**.  
3. Ga naar de prijspagina voor [app-service opschalen](https://azure.microsoft.com/pricing/details/app-service/windows/)en selecteer vervolgens een geschikte prijscategorie.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds te maken hebt met FarmBeats-problemen, neemt u contact op met ons [ondersteuningsforum.](https://aka.ms/farmbeatssupport)
