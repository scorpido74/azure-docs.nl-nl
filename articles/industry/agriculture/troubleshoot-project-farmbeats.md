---
title: Problemen oplossen
description: Problemen met Azure FarmBeats oplossen.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 22c304b26eddaee4444f6eb12957e2a6fedf7810
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793279"
---
# <a name="troubleshooting"></a>Problemen oplossen

In de volgende secties worden veelvoorkomende problemen met Azure FarmBeats beschreven en hoe u deze kunt oplossen.

Voor aanvullende hulp gaat u naar ons op farmbeatssupport@microsoft.com, neemt u het bestand deployer. log op deze e-mail op.

 Gebruik deze stappen om het bestand deployer. log te downloaden:

1. Het gemarkeerde pictogram en selecteer **downloaden** optie in de vervolg keuzelijst.

    ![Maten van project-Farm](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Geef in het volgende scherm het pad op naar het bestand deployer. log. Bijvoorbeeld farmbeats-deployer. log.

## <a name="sensor-telemetry"></a>Sensor-telemetrie

### <a name="telemetry-not-seen"></a>Telemetrie niet gezien

**Symptoom**: apparaten/Sens oren worden geïmplementeerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat. u kunt echter geen telemetriegegevens op FarmBeats ophalen of weer geven.

**Corrigerende actie**: Ga naar de Azure Portal en voer de volgende stappen uit:

1. Ga naar de resource groep FarmBeats data hub.   
2. Selecteer de **Event hub** (DatafeedEventHubNamespace....) en controleer het aantal inkomende berichten.   
3. Als er **geen inkomende berichten**zijn, neemt u contact op met de partner van uw apparaat.  
4. Als er **inkomende berichten**zijn, neemt u contact op met farmbeatssupport@microsoft.com met data hub-en Accelerator logboeken en vastgelegde telemetrie.

Zie de [sectie logs](#collect-logs-manually) van het document voor meer informatie over het downloaden van Logboeken.  

### <a name="dont-have-the-eventhub-connection-string"></a>Geen Eventhub-verbindings reeks

**Corrigerende actie**: Ga naar de Datahub Swagger en voer de volgende stappen uit:
1. Navigeer naar partner-API
2. Klik op GET-> probeer het uit-> uitvoeren
3. Noteer de partner-id van de sensor partner die u interesseert
4. Ga terug naar de partner-API en klik op GET/{id}
5. Geef de id op uit stap 3 en klik op uitvoeren
6. De API-reactie moet de EventHub-verbindings reeks hebben

### <a name="device-appears-offline"></a>Apparaat wordt offline weer gegeven

**Symptomen**: er zijn apparaten geïnstalleerd en u hebt FarmBeats gekoppeld aan de partner van uw apparaat. De apparaten zijn online en verzenden telemetriegegevens, maar ze worden offline weer gegeven.

**Corrigerende actie**: het rapportage-interval is niet geconfigureerd voor dit apparaat. Neem contact op met de fabrikant van uw apparaat om het rapportage-interval in te stellen. 

### <a name="error-deleting-a-resource"></a>Fout bij het verwijderen van een resource

Hieronder vindt u de veelvoorkomende fout scenario's tijdens het verwijderen van een apparaat:  

**Bericht**: er wordt naar het apparaat verwezen in Sens oren: er zijn een of meer Sens oren gekoppeld aan het apparaat. Verwijder de Sens oren en verwijder vervolgens het apparaat.  

Dat **wil zeggen**: het apparaat is gekoppeld aan meerdere Sens oren die in de farm zijn geïmplementeerd.   

**Corrigerende actie**:  

1. Verwijder de Sens oren die zijn gekoppeld aan het apparaat via de Accelerator.  
2. Als u de Sens oren aan een ander apparaat wilt koppelen, vraagt u de partner van het apparaat hetzelfde te doen.  
3. Verwijder het apparaat met een API-aanroep verwijderen de para meter Force instellen als ' True '.  

**Bericht**: in apparaten wordt naar het apparaat verwezen als ParentDeviceId: er zijn een of meer apparaten die aan dit apparaat zijn gekoppeld als onderliggende apparaten. Verwijder ze en verwijder dit apparaat.  

**Betekenis**: er zijn andere apparaten gekoppeld aan het apparaat  

**Corrigerende actie**

1. De apparaten verwijderen die zijn gekoppeld aan het specifieke apparaat  
2. Het specifieke apparaat verwijderen  

    > [!NOTE]
    > U kunt een apparaat niet verwijderen als er Sens oren aan zijn gekoppeld. Zie [Sens oren verwijderen](get-sensor-data-from-sensor-partner.md) in het hoofd stuk sensor gegevens ophalen voor meer informatie over het verwijderen van gekoppelde Sens oren.


## <a name="issues-with-jobs"></a>Problemen met taken

### <a name="farmbeats-internal-error"></a>Interne FarmBeats-fout

**Bericht**: FarmBeats interne fout, zie probleemoplossings gids voor meer informatie.

**Corrigerende maat regelen**: dit kan worden veroorzaakt door een tijdelijke fout in de gegevens pijplijn. Maak de taak opnieuw. Als de fout zich blijft voordoen, voegt u de fout toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Problemen met accelerators oplossen

### <a name="access-control"></a>Access Control

**Fout bij het toevoegen van roltoewijzing**

**Bericht**: er zijn geen overeenkomende gebruikers gevonden

**Corrigerende actie**: Controleer de e-mail-id waarvoor u een roltoewijzing probeert uit te voeren. De e-mail-ID moet een exacte overeenkomst zijn die is geregistreerd voor die gebruiker in het Active Directory. Als de fout zich blijft voordoen, voegt u de fout toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Kan niet aanmelden bij de Accelerator

**Bericht**: fout: u bent niet gemachtigd om de service aan te roepen. Neem contact op met de beheerder voor autorisatie.

**Corrigerende actie**: vraag de beheerder om u te autoriseren voor toegang tot de FarmBeats-implementatie. U kunt dit doen door een bericht te plaatsen van de RoleAssignment-Api's of via de Access Control in het deel venster instellingen van de Accelerator.  

Als u al hebt toegevoegd en deze fout hebt, probeert u het opnieuw door de pagina te vernieuwen.  Als de fout zich blijft voordoen, voegt u de fout toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com.

![Maten van project-Farm](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Accelerator-er is een onbekende fout opgetreden  

**Bericht**: fout: er is een onbekende fout opgetreden

**Corrigerende actie**: dit gebeurt als u de pagina te lang niet-actief blijft. Vernieuw de pagina.  

Als de fout blijft bestaan, voegt u de fout toe aan een bericht op het FarmBeats-forum of neemt u contact op met FarmBeatsSupport@microsoft.com

**De nieuwste versie wordt niet weer gegeven na het upgraden van de FarmBeatsDeployment met FarmBeats Accelerator**

**Corrigerende actie**: dit gebeurt door de persistentie van de service medewerker in de browser.
Sluit alle browser tabbladen die de Accelerator hebben geopend en sluit het browser venster. Start een nieuw exemplaar van de browser en laad de Accelerator-URI opnieuw. Hiermee wordt de nieuwe versie van de Accelerator geladen.

## <a name="sentinel-imagery-related-issues"></a>Problemen met betrekking tot verklikker installatie kopieën

### <a name="sentinel-wrong-username-or-password"></a>Sentinel-onjuiste gebruikers naam of wacht woord

**Fout bericht over taak**; Volledige verificatie is vereist voor toegang tot deze bron.

**Correctie actie**: Voer het installatie programma opnieuw uit voor de upgrade van data hub met de juiste gebruikers naam en wacht woord.

**Corrigerende actie**: mislukte taak opnieuw uitvoeren of een satelliet-indexen taak voor datum bereik van 5-7 dagen uitvoeren. Controleer of de taak is voltooid.

### <a name="sentinel-hub-wrong-url-or-not-accessible"></a>Onjuiste URL van Sentinel hub of niet toegankelijk 

**Fout bericht over taak**: er is iets fout gegaan. De pagina die u probeert te openen, is (tijdelijk) niet beschikbaar. 

**Corrigerende actie**:
1.  Open verklikker-URL (https://scihub.copernicus.eu/dhus/) in browser en controleer of de website toegankelijk is. 
2.  Als de website niet toegankelijk is, controleert u of er een firewall/bedrijfs netwerk etc. de website blokkeert en de benodigde stappen neemt om de bovenstaande URL toe te staan. 
3.  De taak kan niet worden uitgevoerd of een satelliet-indexen taak voor een datum bereik van 5-7 dagen opnieuw uitvoeren en controleren of de taak is voltooid.  

### <a name="sentinel-server-down-for-maintenance"></a>Sentinel-server voor onderhoud

**Taak fout bericht**: de Open Access-hub Copernicus wordt binnenkort weer gegeven. Onze excuses voor het ongemak. we voeren nu een aantal onderhoud uit. We zijn binnenkort weer online. 

**Corrigerende actie**:

1.  Dit probleem kan zich voordoen als er onderhouds activiteiten op de Sentinel-server worden uitgevoerd. 
2.  Als een taak/pijp lijn mislukt met de bovenstaande reden, moet u de taak na enige tijd opnieuw verzenden. 
3.  De gebruiker kan naar https://scihub.copernicus.eu/news/ gaan om informatie te controleren over alle geplande/ongeplande onderhouds activiteiten.  
4.  De taak kan niet worden uitgevoerd of een satelliet-indexen taak voor een datum bereik van 5-7 dagen opnieuw uitvoeren en controleren of de taak is voltooid.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Verklikker maximum aantal verbindingen bereikt

**Taak fout bericht**: maximum aantal twee gelijktijdige stromen dat door de gebruiker<username>is bereikt 

**Corrigerende actie**
1.  Als een taak met de bovenstaande reden mislukt, wordt hetzelfde Sentinel-account gebruikt in een andere implementatie/software. 
2.  De gebruiker kan een nieuw Sentinel-account maken en het installatie programma opnieuw uitvoeren om de data hub bij te werken met een nieuwe Sentinel-gebruikers naam en-wacht woord.  
3.  De taak kan niet worden uitgevoerd of een satelliet-indexen taak voor het datum bereik van 5-7 dagen opnieuw uitvoeren en controleren of de taak is voltooid.

### <a name="sentinel-server-refused-connection"></a>De Sentinel-server heeft de verbinding geweigerd 

**Taak fout bericht**:

De server heeft de verbinding geweigerd op: http://172.30.175.69:8983/solr/dhus 

**Corrigerende actie**: dit probleem kan optreden als er onderhouds activiteiten worden uitgevoerd op een Sentinel-server. 
1.  Als een taak/pijp lijn mislukt met de bovenstaande reden, moet u de taak na enige tijd opnieuw verzenden. 
2.  De gebruiker kan naar https://scihub.copernicus.eu/news/ gaan om informatie te controleren over alle geplande/ongeplande onderhouds activiteiten.  
3.  De taak kan niet worden uitgevoerd of een satelliet-indexen taak voor het datum bereik van 5-7 dagen opnieuw uitvoeren en controleren of de taak is voltooid.


## <a name="collect-logs-manually"></a>Logboeken hand matig verzamelen

[Installeren en implementeren]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) in azure Storage Explorer.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>De taak logboeken van de data hub-ADF verzamelen
1. Meld u aan bij https://portal.azure.com
2. Zoek in **het zoekvak naar de resource** groep FarmBeats data hub.

    > [!NOTE]
    > Selecteer de resource groep data hub die is opgegeven op het moment van de FarmBeats-implementatie.

Zoek in het dash board van de resource groep naar het opslag account (datahublogs....). Bijvoorbeeld datahublogsmvxmq  

1.  Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
2.  Selecteer in de pagina (datahubblogs...) de optie **openen in Verkenner** om de **Open Azure Storage Explorer** -toepassing weer te geven.
3.  Selecteer in het linkerpaneel, (datahubblogs...), **BLOB-containers**, **taak-logboeken**.
4.  Op het tabblad **taak logboeken** selecteert u **downloaden**.
5.  Selecteer de locatie voor het downloaden van de logboeken naar een lokale map op uw computer.
6.  Het gedownloade zip-bestand per e-mail verzenden naar farmbeatssupport@microsoft.com

    ![Maten van project-Farm](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Het verzamelen van Logboeken voor de ADF van snelle taken

1.  Meld u aan bij https://portal.azure.com
2.  Zoek in **het zoekvak naar de resource** groep FarmBeats Accelerator.

    > [!NOTE]
    > Selecteer de resource groep Accelerator die is opgegeven op het moment van de FarmBeats-implementatie.

3.  Zoek in het dash board van de resource groep naar opslag.... opslag account. Bijvoorbeeld storagedop4k
4.  Selecteer het opslag account in de kolom **naam** om het dash board van het opslag account weer te geven.
5.  Selecteer in de pagina (opslag....) de optie **openen in Verkenner** om open Azure Storage Explorer-toepassing weer te geven.
6.  Selecteer in het linkerpaneel, < Storage....), **BLOB-containers**, de optie **taak-logboeken**.
7.  Op het tabblad **taak logboeken** selecteert u **downloaden**.
8.  Selecteer de locatie voor het downloaden van de logboeken naar een lokale map op uw computer.
9.  Het gedownloade zip-bestand per e-mail verzenden naar farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Data hub-app service-logboeken verzamelen

1.  Meld u aan bij https://portal.azure.com
2.  Zoek in **het zoekvak naar de resource** groep FarmBeats data hub.

    > [!NOTE]
    > Selecteer de resource groep data hub die is opgegeven op het moment van de FarmBeats-implementatie.

3.  Zoek in de resource groep naar het opslag account (datahublogs....). Bijvoorbeeld datahublogsmvxmq
4.  Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
5.  Selecteer in de pagina (datahubblogs...) de optie **openen in Verkenner** om de **Open Azure Storage Explorer** -toepassing weer te geven.
6.  Selecteer in het linkerpaneel, (datahubblogs....), **BLOB-containers**, appinsights-logs.
7.  Op het tabblad appinsights-logs selecteert u **downloaden**.
8.  Selecteer het pad om de logboeken te downloaden naar een lokale map op uw computer.
9.  De gedownloade map per e-mail verzenden naar farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Informatie over het verzamelen van app service-logboeken voor Accelerators

1.  Meld u aan bij https://portal.azure.com
2.  Zoek in de **Zoek**opdracht naar FarmBeats Accelerator Resource Group.

    > [!NOTE]
    > Selecteer de resource groep Farmbeats Accelerator die wordt gegeven op het moment van de FarmBeats-implementatie.

3.  Zoek in de resource groep naar het opslag account (opslag....). Bijvoorbeeld storagedop4k
4.  Selecteer het opslag account in de kolom **naam** om het dash board van het **opslag account** weer te geven.
5.  Selecteer in de pagina (opslag....) de optie **openen in Verkenner** om **Open Azure Storage Explorer** -toepassing weer te geven.
6.  Selecteer in het linkerpaneel, (opslag....), **BLOB-containers**, appinsights-logs.
7.  Op het tabblad appinsights-logs selecteert u **downloaden**.
8.  Selecteer de locatie voor het downloaden van de logboeken naar een lokale map op uw computer.
9.  De gedownloade map per e-mail verzenden naar farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Bekende problemen

## <a name="batch-related-issues"></a>Batch-gerelateerde problemen

**Fout**: het regionale account quotum van de batch-accounts voor het opgegeven abonnement is bereikt.

**Corrigerende actie**: Verhoog het quotum of Verwijder ongebruikte batch-accounts en voer de implementatie opnieuw uit.

### <a name="azure-active-directory-related-issues"></a>Verwante problemen Azure Active Directory

**Fout**: de vereiste instellingen kunnen niet worden bijgewerkt naar Azure AD-App d41axx40-XX21-4fbd-8xxf-97Xxx9e2xxc0: onvoldoende bevoegdheden om de bewerking te volt ooien. Zorg ervoor dat de bovenstaande instellingen juist zijn geconfigureerd voor de Azure AD-app.

**Betekenis**: de configuratie van de Azure AD-App-registratie is niet juist uitgevoerd.  

**Corrigerende actie**: vraag de IT-beheerder (met lees toegang voor de Tenant) om het [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) te gebruiken voor het maken van de registratie van de Azure AD-app. Dit script neemt ook automatisch de configuratie stappen.

**Fout**: kan geen nieuwe Active Directory toepassing "dummy" maken in deze Tenant: er bestaat al een ander object met dezelfde waarde voor de eigenschaps-id-uri's

Dat **wil zeggen**dat er al een Azure AD-App-registratie met dezelfde naam bestaat.

**Corrigerende actie**: Verwijder de bestaande Azure AD-App-registratie of hergebruik opnieuw voor installatie. Als u de bestaande Azure AD opnieuw gebruikt, geeft u de toepassings-ID en het client geheim door aan het installatie programma en voert u de implementatie opnieuw uit.

## <a name="inputjson-related-issues"></a>In verband met het invoeren. json-problemen

**Fout bij** het lezen van invoer. JSON-bestand

**Corrigerende maat regelen**: dit probleem doet zich meestal voor als er een fout is opgetreden in de juiste invoer-JSON-pad of-naam naar het installatie programma. Voer de juiste correcties uit en probeer opnieuw te implementeren.

**Fout bij het parseren van JSON-invoer**

**Corrigerende actie**: dit probleem doet zich voornamelijk voor als gevolg van onjuiste waarden in het JSON-bestand van de invoer. Voer de juiste correcties uit en probeer de implementatie opnieuw uit te voeren.

## <a name="high-cpu-usage"></a>Hoog CPU-gebruik

**Fout**: er wordt een e-mail waarschuwing weer geven die verwijst naar een hoog CPU-gebruik. 

**Corrigerende actie**: 
1.  Ga naar de resource groep FarmBeats data hub.
2.  Selecteer app service.  
3.  Ga naar omhoog schalen (App Service plan) en selecteer een geschikte [prijs categorie](https://azure.microsoft.com/pricing/details/app-service/windows/)

## <a name="next-steps"></a>Volgende stappen

Als u nog steeds problemen ondervindt, kunt u contact met ons opnemen via ons [ondersteunings forum](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
