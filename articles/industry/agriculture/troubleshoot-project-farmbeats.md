---
title: Problemen met Azure FarmBeats oplossen
description: In dit artikel wordt beschreven hoe u problemen met Azure FarmBeats oplost.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 0a4fb337adfb2f4e6b8edb86ac620103e929c3a8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74842132"
---
# <a name="troubleshoot-azure-farmbeats"></a>Problemen met Azure FarmBeats oplossen

Dit artikel bevat oplossingen voor veelvoorkomende problemen met Azure FarmBeats.

Neem contact met ons op farmbeatssupport@microsoft.comvoor meer hulp. Zorg ervoor dat u het bestand *deployer. log* in uw e-mail bericht opneemt.

Ga als volgt te werk om het bestand *deployer. log* te downloaden:

1. Selecteer het gemarkeerde pictogram en selecteer vervolgens in de vervolg keuzelijst **downloaden**.

    ![Project FarmBeats](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

1. Geef in het volgende deel venster het pad op naar het bestand *deployer. log* . Voer bijvoorbeeld **farmbeats-deployer. log**in.

## <a name="sensor-telemetry"></a>Sensor-telemetrie

### <a name="cant-view-telemetry-data"></a>Kan geen telemetriegegevens weer geven

**Symptoom**: er zijn apparaten of Sens oren geïmplementeerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat, maar u kunt geen telemetriegegevens ophalen of weer geven op FarmBeats.

**Corrigerende actie**: 

1. Ga naar de resource groep FarmBeats Datahub.   
1. Selecteer de **Event hub** (DatafeedEventHubNamespace) en controleer vervolgens op het aantal inkomende berichten.
1. Voer een van de volgende bewerkingen uit:   
   * Als er *geen inkomende berichten*zijn, neemt u contact op met de partner van uw apparaat.  
   * Als er *inkomende berichten*zijn, neemt u contact op met farmbeatssupport@microsoft.com. Koppel uw Datahub-en Accelerator logboeken en vastgelegde telemetrie.

Als u wilt weten hoe u Logboeken kunt downloaden, gaat u naar de sectie [Logboeken hand matig verzamelen](#collect-logs-manually) .  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>De Azure-Event Hubs niet hebben connection string

**Corrigerende actie**: 

1. Ga in Datahub Swagger naar de partner-API.
1. Selecteer ** > ** **uitproberen** > **uitvoeren**.
1. Noteer de partner-ID van de sensor partner die u wilt.
1. Ga terug naar de partner-API en selecteer **Get/\<ID >** .
1. Geef de partner-ID op uit stap 3 en selecteer vervolgens **uitvoeren**.
   
   De API-reactie moet de Event Hubs connection string hebben.

### <a name="device-appears-offline"></a>Apparaat wordt offline weer gegeven

**Symptomen**: er zijn apparaten geïnstalleerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat. De apparaten zijn online en verzenden telemetriegegevens, maar ze worden offline weer gegeven.

**Corrigerende actie**: het rapportage-interval is niet geconfigureerd voor dit apparaat. Neem contact op met de fabrikant van uw apparaat om het rapportage-interval in te stellen. 

### <a name="error-deleting-a-device"></a>Fout bij het verwijderen van een apparaat

Tijdens het verwijderen van een apparaat kan een van de volgende veelvoorkomende fout scenario's optreden:  

**Bericht**: er wordt naar het apparaat verwezen in Sens oren: er zijn een of meer Sens oren gekoppeld aan het apparaat. Verwijder de Sens oren en verwijder vervolgens het apparaat.  

Dat **wil zeggen**: het apparaat is gekoppeld aan meerdere Sens oren die in de farm zijn geïmplementeerd.   

**Corrigerende actie**:  

1. Verwijder de Sens oren die zijn gekoppeld aan het apparaat via Accelerator.  
1. Als u de Sens oren wilt koppelen aan een ander apparaat, vraagt u de partner van het apparaat hetzelfde te doen.  
1. Verwijder het apparaat met behulp van een `DELETE API` aanroep en stel de para meter Force in op *True*.  

**Bericht**: in apparaten wordt naar het apparaat verwezen als ParentDeviceId: er zijn een of meer apparaten die zijn gekoppeld aan dit apparaat als onderliggende apparaten. Verwijder ze en verwijder dit apparaat. "  

Dat **wil zeggen**: er zijn andere apparaten aan het apparaat gekoppeld.  

**Corrigerende actie**

1. De apparaten verwijderen die zijn gekoppeld aan dit specifieke apparaat.  
1. Het specifieke apparaat verwijderen.  

    > [!NOTE]
    > U kunt een apparaat niet verwijderen als er Sens oren aan zijn gekoppeld. Meer informatie over het verwijderen van gekoppelde Sens oren vindt u in de sectie sensor verwijderen in [sensor gegevens ophalen van sensor partners](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problemen met taken

### <a name="farmbeats-internal-error"></a>Interne FarmBeats-fout

**Bericht**: ' FarmBeats interne fout ' Zie probleemoplossings gids voor meer informatie. '

**Corrigerende actie**: dit probleem kan het gevolg zijn van een tijdelijke fout in de gegevens pijplijn. Maak de taak opnieuw. Als de fout zich blijft voordoen, voegt u het fout bericht toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Problemen met accelerators oplossen

### <a name="access-control"></a>Toegangsbeheer

**Probleem**: er wordt een fout bericht weer gegeven tijdens het toevoegen van een roltoewijzing.

**Bericht**: er zijn geen overeenkomende gebruikers gevonden.

**Corrigerende actie**: Controleer de e-mail-id waarvoor u een roltoewijzing wilt toevoegen. De e-mail-ID moet exact overeenkomen met de geregistreerde ID voor die gebruiker in de Active Directory. Als de fout zich blijft voordoen, voegt u het fout bericht toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Kan niet aanmelden bij de Accelerator

**Bericht**: ' fout: u bent niet gemachtigd om de service aan te roepen. Neem contact op met de beheerder voor autorisatie. "

**Corrigerende actie**: vraag de beheerder om u te autoriseren voor toegang tot de FarmBeats-implementatie. U kunt dit doen door een bericht te plaatsen van de RoleAssignment-Api's of via de Access Control in het deel venster **instellingen** in Accelerator.  

Als u al toegang hebt gekregen en deze fout ondervindt, kunt u het opnieuw proberen door de pagina te vernieuwen. Als de fout zich blijft voordoen, voegt u het fout bericht toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

![Project FarmBeats](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemen met de Accelerator  

**Probleem**: u hebt een verwerkings fout van een onbepaalde oorzaak ontvangen.

**Bericht**: ' fout: er is een onbekende fout opgetreden. '

**Corrigerende actie**: deze fout treedt op als u de pagina te lang niet-actief blijft. Vernieuw de pagina.  

Als de fout zich blijft voordoen, voegt u het fout bericht toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

**Probleem**: FarmBeats Accelerator geeft niet de nieuwste versie weer, zelfs nadat u FarmBeatsDeployment hebt bijgewerkt.

**Corrigerende actie**: deze fout treedt op door de service medewerker-persistentie in de browser. Ga als volgt te werk:
1. Sluit alle browser tabbladen met Accelerator open en sluit het browser venster. 
1. Start een nieuw exemplaar van de browser en laad de Accelerator-URI opnieuw. Met deze actie wordt de nieuwe versie van Accelerator geladen.

## <a name="sentinel-imagery-related-issues"></a>Sentinel: problemen met betrekking tot installatie kopieën

### <a name="wrong-username-or-password"></a>Onjuiste gebruikers naam of wacht woord

**Taak fout bericht**: volledige verificatie is vereist voor toegang tot deze resource.

**Corrigerende actie**: 

Voer een van de volgende handelingen uit:
* Voer het installatie programma voor het bijwerken van Datahub met de juiste gebruikers naam en wacht woord opnieuw uit.
* Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Sentinel hub: onjuiste URL of site niet toegankelijk 

**Taak fout bericht**: er is iets fout gegaan. De pagina die u probeert te openen, is (tijdelijk) niet beschikbaar. " 

**Corrigerende actie**:
1. Open [Sentinel](https://scihub.copernicus.eu/dhus/) in uw browser om te zien of de website toegankelijk is. 
1. Als de website niet toegankelijk is, controleert u of een firewall, bedrijfs netwerk of andere blokkerende software de toegang tot de website verhindert en voert u de benodigde stappen uit om de verklikker-URL toe te staan. 
1. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.  

### <a name="sentinel-server-down-for-maintenance"></a>Verklikker server: omlaag voor onderhoud

**Taak fout bericht**: de Open Access-hub Copernicus wordt binnenkort weer gegeven. Onze excuses voor het ongemak. we voeren nu een aantal onderhoud uit. We zijn binnenkort weer online. " 

**Corrigerende actie**:

Dit probleem kan zich voordoen als er onderhouds activiteiten op de Sentinel-server worden uitgevoerd.

1. Als een taak of pijp lijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in te dienen. 

   Voor informatie over geplande of niet-geplande verklikker onderhouds activiteiten gaat u naar de [nieuws site Copernicus Open Access hub](https://scihub.copernicus.eu/news/) .  
1. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinel: maximum aantal verbindingen bereikt

**Taak fout bericht**: het maximum aantal gelijktijdige stromen dat door de gebruiker is bereikt\<gebruikers naam >.

Dat **wil zeggen**: als een taak mislukt omdat het maximum aantal verbindingen is bereikt, wordt hetzelfde verklikker account gebruikt in een andere software-implementatie.

**Corrigerende actie**: Voer een van de volgende handelingen uit:
* Maak een nieuw verklikker account en voer het installatie programma opnieuw uit om Datahub bij te werken met een nieuwe Sentinel-gebruikers naam en-wacht woord.  
* Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.

### <a name="sentinel-server-refused-connection"></a>Verklikker server: verbinding geweigerd 

**Taak fout bericht**: de server heeft de verbinding geweigerd bij: http://172.30.175.69:8983/solr/dhus. 

**Corrigerende actie**: dit probleem kan zich voordoen als er onderhouds activiteiten worden uitgevoerd op de Sentinel-server. 
1. Als een taak of pijp lijn mislukt omdat er onderhoud wordt uitgevoerd, dient u de taak na enige tijd opnieuw in te dienen. 

   Voor informatie over geplande of niet-geplande verklikker onderhouds activiteiten gaat u naar de [nieuws site Copernicus Open Access hub](https://scihub.copernicus.eu/news/) .  
1. Voer de mislukte taak opnieuw uit of voer een satelliet-indexen taak uit voor een datum bereik van 5 tot 7 dagen en controleer vervolgens of de taak is geslaagd.

## <a name="collect-logs-manually"></a>Logboeken hand matig verzamelen

[Installeer en implementeer Azure Storage Explorer]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Azure Data Factory-taak logboeken verzamelen in Datahub
1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek in **het zoekvak naar de resource** groep FarmBeats Datahub.

    > [!NOTE]
    > Selecteer de Datahub-resource groep die u tijdens de implementatie van FarmBeats hebt opgegeven.

1. Zoek in het dash board van de **resource groep** naar het *datahublogs-\** -opslag account. Zoek bijvoorbeeld naar **datahublogsmvxmq**.  

1. Selecteer in de kolom **naam** het opslag account om het dash board van het **opslag account** weer te geven.
1. Selecteer in het deel venster **datahubblogs\*** **Open in Verkenner** om de **Open Azure Storage Explorer** -toepassing weer te geven.
1. Selecteer **BLOB-containers**in het linkerdeel venster en selecteer vervolgens **taak-logboeken**.
1. In het deel venster **taak logboeken** selecteert u **downloaden**.
1. Down load de logboeken naar een lokale map op uw computer.
1. Het gedownloade zip-bestand per e-mail verzenden naar farmbeatssupport@microsoft.com.

    ![Project FarmBeats](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Azure Data Factory-taak Logboeken in de Accelerator verzamelen 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek in **het zoekvak naar de resource** groep FarmBeats Accelerator.

    > [!NOTE]
    > Selecteer de resource groep voor accelerators die u hebt opgegeven tijdens de implementatie van FarmBeats.

1. Zoek in het dash board van de **resource groep** naar het opslag account *opslag\** . Zoek bijvoorbeeld naar **storagedop4k\*** .
1. Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
1. Selecteer in het deel venster **opslag\*** de optie **openen in verkenner** om de Azure Storage Explorer toepassing te openen.
1. Selecteer **BLOB-containers**in het linkerdeel venster en selecteer vervolgens **taak-logboeken**.
1. In het deel venster **taak logboeken** selecteert u **downloaden**.
1. Down load de logboeken naar een lokale map op uw computer.
1. Het gedownloade zip-bestand per e-mail verzenden naar farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Datahub app service-logboeken verzamelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek in **het zoekvak naar de resource** groep FarmBeats Datahub.

    > [!NOTE]
    > Selecteer de Datahub-resource groep die u tijdens de implementatie van FarmBeats hebt opgegeven.

1. Zoek in de resource groep naar het datahublogs-opslag account van *\** . Zoek bijvoorbeeld naar **fordatahublogsmvxmq\*** .
1. Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
1. Selecteer in het deel venster **datahubblogs\*** **Open in verkenner** om de Azure Storage Explorer toepassing te openen.
1. Selecteer **BLOB-containers**in het linkerdeel venster en selecteer vervolgens **appinsights-logs**.
1. In het deel venster **appinsights-logs** selecteert u **downloaden**.
1. Down load de logboeken naar een lokale map op uw computer.
1. Het gedownloade zip-bestand per e-mail verzenden naar farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>App service-logboeken voor Accelerators verzamelen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Zoek in **het zoekvak naar de resource** groep FarmBeats Accelerator.

    > [!NOTE]
    > Selecteer de resource groep FarmBeats Accelerator die tijdens de implementatie van FarmBeats is verschaft.

1. Zoek in de resource groep naar het opslag account *opslag\** . Zoek bijvoorbeeld naar **storagedop4k\*** .
1. Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
1. Selecteer in het deel venster **opslag\*** de optie **openen in verkenner** om de Azure Storage Explorer toepassing te openen.
1. Selecteer **BLOB-containers**in het linkerdeel venster en selecteer vervolgens **appinsights-logs**.
1. In het deel venster **appinsights-logs** selecteert u **downloaden**.
1. Down load de logboeken naar een lokale map op uw computer.
1. De gedownloade map per e-mail verzenden naar farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Bekende problemen

## <a name="batch-related-issues"></a>Batch-gerelateerde problemen

**Fout bericht**: de regionale account quota van de batch-accounts voor het opgegeven abonnement is bereikt.

**Corrigerende actie**: Verhoog het quotum of verwijder de niet-gebruikte batch-accounts en voer de implementatie opnieuw uit.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemen met betrekking tot Azure Active Directory (Azure AD)

**Fout bericht**: de vereiste instellingen kunnen niet worden bijgewerkt naar Azure AD-App d41axx40-XX21-4fbd-8xxf-97Xxx9e2xxc0: onvoldoende bevoegdheden om de bewerking te volt ooien. Zorg ervoor dat de bovenstaande instellingen juist zijn geconfigureerd voor de Azure AD-app. "

**Betekenis**: de configuratie van de Azure AD-App-registratie is niet correct voltooid.  

**Corrigerende actie**: vraag de IT-beheerder (de persoon met lees toegang voor de Tenant) om het [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) te gebruiken voor het maken van de registratie van de Azure AD-app. Dit script zorgt er automatisch voor dat de configuratie stappen ook worden uitgevoerd.

**Fout bericht**: kan geen nieuwe Active Directory toepassing maken\<toepassings naam\>in deze Tenant: er bestaat al een ander object met dezelfde waarde voor de eigenschaps-id-uri's.

Dat **wil zeggen**: er bestaat al een Azure AD-App-registratie met dezelfde naam.

**Corrigerende actie**: Verwijder de bestaande Azure AD-App-registratie of hergebruik deze voor installatie. Als u de bestaande Azure AD-App-registratie opnieuw wilt gebruiken, geeft u de toepassings-ID en het client geheim door aan het installatie programma en implementeert u het opnieuw.

## <a name="issues-with-the-inputjson-file"></a>Problemen met het bestand input. json

**Fout**: er is een fout opgetreden tijdens het lezen van de invoer van het bestand *input. json* .

**Corrigerende actie**: dit probleem doet zich meestal voor vanwege een fout in het opgeven van de juiste *invoer.* het pad naar het JSON-bestand of de naam van het installatie programma. Voer de juiste correcties uit en probeer de implementatie opnieuw uit te voeren.

**Fout**: er is een fout opgetreden bij het parseren van waarden in het bestand *input. json* .

**Corrigerende actie**: dit probleem doet zich voornamelijk voor als gevolg van onjuiste waarden in het bestand *input. json* . Voer de juiste correcties uit en probeer de implementatie opnieuw uit te voeren.

## <a name="high-cpu-usage"></a>Hoog CPU-gebruik

**Fout**: er wordt een e-mail waarschuwing weer geven die verwijst naar een *hoge waarschuwing voor CPU-gebruik*. 

**Corrigerende actie**: 
1. Ga naar de resource groep FarmBeats Datahub.
1. Selecteer app service.  
1. Ga naar de pagina met [prijs](https://azure.microsoft.com/pricing/details/app-service/windows/)opschalen app service en selecteer een geschikte prijs categorie.

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds FarmBeats problemen ondervindt, neemt u contact op met ons [ondersteunings forum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
