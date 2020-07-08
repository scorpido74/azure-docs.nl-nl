---
title: Problemen met het inrichten van Cloud Azure AD Connect
description: In dit artikel wordt beschreven hoe u problemen kunt oplossen die zich kunnen voordoen met de Cloud inrichtings agent.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: how-to
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 009e762b69d4f3512158d69ef3c67089096c9da7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85360789"
---
# <a name="cloud-provisioning-troubleshooting"></a>Problemen met het inrichten van Clouds oplossen

Cloud inrichting raakt veel verschillende dingen en heeft veel verschillende afhankelijkheden. Dit breed bereik kan verschillende problemen veroorzaken. Dit artikel helpt u bij het oplossen van deze problemen. Hierin worden de typische gebieden geïntroduceerd voor het richten op, het verzamelen van aanvullende informatie en de verschillende technieken die u kunt gebruiken om problemen op te sporen.


## <a name="common-troubleshooting-areas"></a>Algemene probleemoplossings gebieden

|Naam|Beschrijving|
|-----|-----|
|[Agent problemen](#agent-problems)|Controleer of de agent juist is geïnstalleerd en of deze communiceert met Azure Active Directory (Azure AD).|
|[Problemen met object synchronisatie](#object-synchronization-problems)|Inrichtings Logboeken gebruiken om problemen met object synchronisatie op te lossen.|
|[In quarantaine geplaatste problemen inrichten](#provisioning-quarantined-problems)|Informatie over het inrichten van quarantaine problemen en hoe u deze kunt oplossen.|


## <a name="agent-problems"></a>Agent problemen
Enkele van de eerste dingen die u met de agent wilt verifiëren, zijn:

-  Is dit geïnstalleerd?
-  Wordt de agent lokaal uitgevoerd?
-  Bevindt de agent zich in de portal?
-  Is de agent gemarkeerd als in orde?

Deze items kunnen worden geverifieerd in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent

Voer de volgende stappen uit om te controleren of de agent door Azure wordt gezien en in orde is.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory**  >  **Azure AD CONNECT**. Selecteer in het midden de optie **inrichting beheren (preview)**.
1. Selecteer **Alle agents controleren**op het scherm **Azure AD Provisioning (preview)** .

   ![Alle agents controleren](media/how-to-install/install7.png)</br>
 
1. Op het scherm **on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie is en is gemarkeerd als *in orde*.

   ![Scherm on-premises ingerichte agents](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>De poort controleren

Gebruik het volgende hulp programma om te controleren of Azure luistert op poort 443 en dat uw agent ermee kan communiceren:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Met deze test wordt gecontroleerd of uw agents kunnen communiceren met Azure via poort 443. Open een browser en ga naar de vorige URL van de server waarop de agent is geïnstalleerd.

![Verificatie van de bereik baarheid van de poort](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Op de lokale server

Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd.

1. Op de server waarop de agent is geïnstalleerd, opent u **Services** door **ernaar te navigeren of door te gaan**met het  >  **uitvoeren**van  >  **Services. msc**.
1. Zorg er bij **Services**voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en de status wordt *uitgevoerd*.

   ![Scherm Services](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Veelvoorkomende problemen met de agent installatie

In de volgende secties worden enkele veelvoorkomende problemen met de agent installatie en typische oplossingen beschreven.

#### <a name="agent-failed-to-start"></a>De agent is niet gestart

Er wordt mogelijk een fout bericht met de volgende strekking weer gegeven:

**De service Microsoft Azure AD Connect inrichtings agent kan niet worden gestart. Controleer of u voldoende rechten hebt om de systeem services te starten.** 

Dit probleem wordt meestal veroorzaakt door een groeps beleid waarmee wordt voor komen dat machtigingen kunnen worden toegepast op het aanmeldings account van de lokale NT-service die is gemaakt door het installatie programma (NT SERVICE\AADConnectProvisioningAgent). Deze machtigingen zijn vereist voor het starten van de service.

Voer de volgende stappen uit om dit probleem op te lossen.

1. Meld u met een beheerders account aan bij de server.
1. Open **Services** door te navigeren naar de service of **door te gaan**met het  >  **uitvoeren**van  >  **Services. msc**.
1. Dubbel klik onder **Services**op **Microsoft Azure AD Connect inrichtings agent**.
1. Wijzig op het tabblad **Aanmelden** het **account** in een domein beheerder. Start de service vervolgens opnieuw. 

   ![Tabblad Aanmelden](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Er is een time-out van de agent of het certificaat is ongeldig

Mogelijk wordt het volgende fout bericht weer gegeven wanneer u probeert de agent te registreren.

![Fout bericht time-out](media/how-to-troubleshoot/troubleshoot4.png)

Dit probleem wordt meestal veroorzaakt door de agent waardoor geen verbinding kan worden gemaakt met de Hybrid Identity-service, hiervoor moet u een HTTP-proxy configureren. U kunt dit probleem oplossen door een uitgaande proxy te configureren. 

De inrichtings agent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratie bestand van de agent te bewerken *C:\Program Files\Microsoft Azure AD Connect inrichting Agent\AADConnectProvisioningAgent.exe.config*. Voeg de volgende regels toe aan het einde van het bestand net vóór de eindtag `</configuration>` .
Vervang de variabelen `[proxy-server]` en `[proxy-port]` door de naam en poort waarden van de proxy server.

```xml
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

#### <a name="agent-registration-fails-with-security-error"></a>Registratie van agent mislukt vanwege beveiligings fout

Er wordt mogelijk een fout bericht weer gegeven wanneer u de Cloud inrichtings Agent installeert.

Dit probleem wordt meestal veroorzaakt door de agent waardoor de Power shell-registratie scripts niet kunnen worden uitgevoerd vanwege het lokale Power shell-uitvoerings beleid.

Om dit probleem op te lossen, wijzigt u het Power shell-uitvoerings beleid op de-server. U moet het beleid voor de computer en gebruiker instellen op niet- *gedefinieerde* of *RemoteSigned*. Als ze zijn ingesteld als *onbeperkt*, wordt deze fout weer geven. Zie [Power shell Execution policies](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)(Engelstalig) voor meer informatie. 

### <a name="log-files"></a>Logboekbestanden

De agent verzendt standaard minimale foutberichten en stack-traceringsgegevens. U vindt deze traceer Logboeken in de map *C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect Provisioning Agent\Trace*.

Volg deze stappen om aanvullende informatie te verzamelen voor het oplossen van problemen met de agent.

1. Stop de service **Microsoft Azure ad het verbinden van inrichtings agent**.
1. Maak een kopie van het oorspronkelijke configuratie bestand: *C:\Program Files\Microsoft Azure AD Connect inrichting Agent\AADConnectProvisioningAgent.exe.config*.
1. Vervang de bestaande `<system.diagnostics>` sectie door het volgende en alle tracerings berichten worden naar het bestand *ProvAgentTrace. log*.

   ```xml
     <system.diagnostics>
         <sources>
         <source name="AAD Connect Provisioning Agent">
             <listeners>
             <add name="console"/>
             <add name="etw"/>
             <add name="textWriterListener"/>
             </listeners>
         </source>
         </sources>
         <sharedListeners>
         <add name="console" type="System.Diagnostics.ConsoleTraceListener" initializeData="false"/>
         <add name="etw" type="System.Diagnostics.EventLogTraceListener" initializeData="Azure AD Connect Provisioning Agent">
             <filter type="System.Diagnostics.EventTypeFilter" initializeData="All"/>
         </add>
         <add name="textWriterListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log"/>
         </sharedListeners>
     </system.diagnostics>
    
   ```
1. Start de service **Microsoft Azure AD verbinding inrichtings agent**.
1. Gebruik de volgende opdracht om het bestand af te sluiten en problemen op te lossen. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Problemen met object synchronisatie

De volgende sectie bevat informatie over het oplossen van object synchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken

In de Azure Portal kunnen inrichtings logboeken worden gebruikt om problemen met object synchronisatie op te sporen en op te lossen. Selecteer **Logboeken**om de logboeken weer te geven.

![Knop Logboeken](media/how-to-troubleshoot/log1.png)

Inrichtings logboeken bieden een schat aan informatie over de status van de objecten die worden gesynchroniseerd tussen uw on-premises Active Directory omgeving en Azure.

![Scherm voor het inrichten van Logboeken](media/how-to-troubleshoot/log2.png)

U kunt de vervolg keuzelijsten boven aan de pagina gebruiken om de weer gave te filteren op nul in op specifieke problemen, zoals datums. Dubbel klik op een afzonderlijke gebeurtenis om aanvullende informatie weer te geven.

![Informatie over de vervolg keuzelijst voor het inrichtings logboek](media/how-to-troubleshoot/log3.png)

Deze informatie bevat gedetailleerde stappen en waar het synchronisatie probleem plaatsvindt. Op deze manier kunt u de exacte plaats van het probleem lokaliseren.


## <a name="provisioning-quarantined-problems"></a>In quarantaine geplaatste problemen inrichten

Met Cloud Provisioning wordt de status van uw configuratie bewaakt en worden beschadigde objecten in een quarantaine status geplaatst. Als de meeste of alle aanroepen voor het doel systeem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerders referenties, wordt de inrichtings taak gemarkeerd als in quarantaine.

![Quarantinestatus](media/how-to-troubleshoot/quarantine1.png)

Als u de status selecteert, ziet u aanvullende informatie over de quarantaine. U kunt ook de fout code en het bericht ophalen.

![Status informatie voor quarantaine](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Een quarantaine omzetten

- Gebruik de Azure Portal om de inrichtings taak opnieuw te starten. Selecteer **inrichting opnieuw opstarten**op de pagina configuratie van agent.

  ![Inrichting opnieuw starten](media/how-to-troubleshoot/quarantine3.png)

- Gebruik Microsoft Graph om [de inrichtings taak opnieuw te starten](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). U hebt volledige controle over wat u opnieuw opstart. U kunt het volgende wissen:
  - Borg, om de borg teller opnieuw op te starten die de quarantaine status toeneemt.
  - Quarantaine, om de toepassing uit quarantaine te verwijderen.
  - Water merken. 
  
  Gebruik de volgende aanvraag:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)



