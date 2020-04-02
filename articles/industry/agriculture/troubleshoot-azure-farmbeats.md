---
title: Problemen met Azure FarmBeats oplossen
description: In dit artikel wordt beschreven hoe u Azure FarmBeats oplossen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 504dfb721c7b87d5497c73851d0694601b253d5c
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529697"
---
# <a name="troubleshoot"></a>Problemen oplossen

In dit artikel vindt u oplossingen voor veelvoorkomende Azure FarmBeats-problemen. Neem voor extra hulp contact op farmbeatssupport@microsoft.commet ons [ondersteuningsforum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) of mail ons op .

## <a name="install-issues"></a>Problemen met installeren

  > [!NOTE]
  > Als u de installatie opnieuw start vanwege een fout, moet u de brongroep verwijderen of alle bronnen uit de resourcegroep verwijderen voordat u de installatie opnieuw activeert.

### <a name="invalid-sentinel-credentials"></a>Ongeldige Sentinel-referenties

De Sentinel-referenties die tijdens de installatie worden verstrekt, zijn onjuist. Start de installatie opnieuw met de juiste referenties.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>Het regionale accountquotum van batchaccounts voor het opgegeven abonnement is bereikt

Verhoog het quotum of verwijder de ongebruikte batchaccounts en start de installatie opnieuw.

### <a name="invalid-resource-group-location"></a>Ongeldige locatie resourcegroep

Controleer of de resourcegroep zich op dezelfde locatie bevindt als de regio die tijdens de installatie is opgegeven.

### <a name="other-install-issues"></a>Andere installatieproblemen

Neem contact met ons op met de volgende gegevens:

- Uw abonnements-id
- Naam resourcegroep
- Voeg het logboekbestand voor de implementatiefout toe met de onderstaande stappen:

    1. Navigeer naar de resourcegroep in de Azure-portal.
    2. Selecteer Implementaties onder sectie Instellingen aan de linkerkant.
    3. Voor elke implementatie die 'Mislukt' weergeeft, klik je door naar details en download je de implementatiegegevens. Voeg dit bestand toe aan de e-mail.

## <a name="sensor-telemetry"></a>Sensortelemetrie

### <a name="cant-view-telemetry-data"></a>Telemetriegegevens kunnen niet worden bekeken

**Symptoom:** Apparaten of sensoren worden geïmplementeerd en u hebt FarmBeats gekoppeld aan uw apparaatpartner, maar u geen telemetriegegevens op FarmBeats krijgen of bekijken.

**Corrigerende maatregelen**:

1. Ga naar uw FarmBeats Datahub-brongroep.
2. Selecteer de **gebeurtenishub** (DatafeedEventHubNamespace) en controleer vervolgens op het aantal binnenkomende berichten.
3. Voer een van de volgende bewerkingen uit:
   - Als er *geen binnenkomende berichten*zijn, neemt u contact op met uw apparaatpartner.  
   - Als er *binnenkomende berichten*zijn, neem dan contact met ons op met uw Datahub- en Accelerator-logboeken en vastgelegde telemetrie.

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

> [!NOTE]
> De partner-ID van de sensorpartner waarin u geïnteresseerd bent.

3. Ga terug naar de Partner-API en selecteer **>op-\<en>. **
4. Geef de partner-id op vanaf stap 3 en selecteer **Uitvoeren**.

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

**Corrigerende maatregelen:** dit probleem kan het gevolg zijn van een tijdelijke fout in de gegevenspijplijn. Maak de taak opnieuw. Als de fout blijft bestaan, neemt u contact met ons op met de foutmelding / logboeken.

## <a name="accelerator-troubleshooting"></a>Acceleratorprobleemoplossing

### <a name="access-control"></a>Toegangsbeheer

**Probleem:** U ontvangt een fout terwijl u een roltoewijzing toevoegt.

**Bericht:**'Geen overeenkomende gebruikers gevonden'.

**Corrigerende actie:** controleer de e-id waarvoor u een roltoewijzing probeert toe te voegen. De e-id moet exact overeenkomen met de id, die voor die gebruiker is geregistreerd in de Active Directory. Als de fout blijft bestaan, neemt u contact met ons op met de foutmelding / logboeken.

### <a name="unable-to-log-in-to-accelerator"></a>Kan niet inloggen op Accelerator

**Bericht:**"Fout: u bent niet bevoegd om de service te bellen. Neem contact op met de beheerder voor autorisatie."

**Corrigerende actie:** Vraag de beheerder om u toestemming te geven voor toegang tot de FarmBeats-implementatie. Dit kan door een post van de RoleAssignment API's te doen of via het toegangsbesturingselement in het deelvenster **Instellingen** in Accelerator.  

Als u al toegang hebt gekregen en met deze fout wordt geconfronteerd, probeert u het opnieuw door de pagina te vernieuwen. Als de fout blijft bestaan, neemt u contact met ons op met de foutmelding / logboeken.

![Project FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Accelerator problemen  

**Probleem:** U hebt een acceleratorfout van onbepaalde oorzaak ontvangen.

**Bericht**: "Fout: er is een onbekende fout opgetreden."

**Corrigerende actie**: Deze fout treedt op als u de pagina te lang inactief laat. Vernieuw de pagina.  

Als de fout blijft bestaan, neemt u contact met ons op met de foutmelding / logboeken.

**Probleem:** FarmBeats Accelerator toont niet de nieuwste versie, zelfs niet nadat u FarmBeatsDeployment hebt geüpgraded.

**Corrigerende maatregelen:** Deze fout treedt op als gevolg van persistentie van de servicemedewerker in de browser. Ga als volgt te werk:

1. Sluit alle browsertabbladen met Accelerator open en sluit het browservenster.
2. Start een nieuw exemplaar van de browser en herlaad de Accelerator URI. Deze actie laadt de nieuwe versie van Accelerator.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: Problemen met beeldmateriaal

### <a name="wrong-username-or-password"></a>Verkeerde gebruikersnaam of wachtwoord

**Bericht voor het mislukken:**'Volledige verificatie is vereist om toegang te krijgen tot deze bron'.

**Corrigerende maatregelen**:

Voer een van de volgende handelingen uit:

- Update FarmBeats met de juiste gebruikersnaam / wachtwoord met behulp van de onderstaande stappen en probeer de taak opnieuw.

    *Sentinel-gebruikersnaam bijwerken*
    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    2. Zoek **in het** vak Zoeken naar de brongroep FarmBeats Datahub.
    3. Selecteer Opslagaccountopslag***** -> containers -> batch-prep-bestanden -> to_vm -> config.ini
    4. Klik op Bewerken
    5. De gebruikersnaam bijwerken in de sectie sentinel_account

    *Sentinel-wachtwoord bijwerken*
    1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
    2. Zoek **in het** vak Zoeken naar de brongroep FarmBeats Datahub.
    3. Selecteer keyvault-*****
    4. Selecteer Toegangsbeleid onder Instellingen
    5. Klik op 'Toegangsbeleid toevoegen'
    6. Gebruik 'Geheim beheer' voor configureren vanuit sjabloon en voeg uzelf toe aan Principal
    7. Klik op Toevoegen en klik vervolgens op Opslaan op de pagina Toegangsbeleid
    8. Klik op Geheimen onder Instellingen
    9. Klik op Sentinel-wachtwoord
    10. Een nieuwe versie van de waarde maken en inschakelen

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

**Betekenis:** Als een taak mislukt omdat het maximum aantal verbindingen is bereikt, wordt hetzelfde Sentinel-account in meerdere taken gebruikt.

**Corrigerende maatregelen**: Probeer een van de volgende opties:

* Wacht tot de andere taken zijn voltooid voordat de mislukte taak opnieuw wordt uitgevoerd.
* Maak een nieuw Sentinel-account aan en werk de Sentinel-gebruikersnaam en -wachtwoord bij in FarmBeats.

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

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Azure Data Factory-taaklogboeken of App-servicelogboeken verzamelen in Datahub

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de brongroep FarmBeats Datahub.
3. Zoek in het dashboard **van resourcegroep** naar het *opslagaccount van\* datahublogs.* Bijvoorbeeld, *datahublogsmvxmq*.  
4. Selecteer **in** de kolom Naam het opslagaccount om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het deelvenster **datahubblogs\* ** de optie **Openen in Explorer** om de toepassing Azure Storage **Explorer** te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens taaklogboeken** voor Azure Data Factory-logboeken of **appinsights-logs** voor App Service-logboeken.
7. Selecteer De logboeken **downloaden** en downloaden naar een lokale map op uw machine.

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Azure Data Factory-taaklogboeken of App-servicelogboeken voor Accelerator verzamelen

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Zoek **in het** vak Zoeken naar de resourcegroep FarmBeats Accelerator.
3. Zoek in het dashboard **Resourcegroep** naar het *\* opslagaccount.* Bijvoorbeeld *storagedop4k\**.
4. Selecteer het opslagaccount in de kolom **Naam** om het dashboard **van het opslagaccount** weer te geven.
5. Selecteer in het **opslagvenster\* ** **Openen in Explorer** om de Azure Storage Explorer-toepassing te openen.
6. Selecteer **blobcontainers**in het linkerdeelvenster en selecteer **vervolgens taaklogboeken** voor Azure Data Factory-logboeken of **appinsights-logs** voor App Service-logboeken.
7. Selecteer De logboeken **downloaden** en downloaden naar een lokale map op uw machine.

## <a name="high-cpu-usage"></a>Hoog CPU-gebruik

**Fout:** U ontvangt een e-mailwaarschuwing die verwijst naar een **waarschuwing voor een hoog CPU-gebruik.**

**Corrigerende maatregelen**:

1. Ga naar uw FarmBeats Datahub-brongroep.
2. Selecteer de **app-service**.  
3. Ga naar de prijspagina voor [app-service opschalen](https://azure.microsoft.com/pricing/details/app-service/windows/)en selecteer vervolgens een geschikte prijscategorie.
