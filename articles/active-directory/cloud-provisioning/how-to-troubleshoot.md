---
title: Problemen met het inrichten van Cloud Azure AD Connect
description: In dit document wordt beschreven hoe u problemen kunt oplossen die zich kunnen voordoen met de Cloud inrichtings agent.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: 4100886e0a24fa961b9085bd507ae3f4ebfdd6eb
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794235"
---
# <a name="cloud-provisioning-troubleshooting"></a>Problemen met het inrichten van Clouds oplossen

Cloud inrichting raakt veel verschillende dingen en heeft veel verschillende afhankelijkheden.  Natuurlijk kan dit leiden tot verschillende problemen.  Dit document is ontworpen om u aan de slag te gaan met het oplossen van deze problemen.  In dit document vindt u informatie over de typische gebieden waar u zich moet concentreren, het verzamelen van aanvullende informatie en verschillende technieken die kunnen worden gebruikt om problemen op te lossen.  


## <a name="common-troubleshooting-areas"></a>Algemene probleemoplossings gebieden

|Naam|Beschrijving|
|-----|-----|
|[Agent problemen](#agent-issues)|Controleer of de agent juist is geïnstalleerd en communiceert met Azure AD.|
|[Problemen met object synchronisatie](#object-synchronization-issues)|Inrichtings Logboeken gebruiken om problemen met object synchronisatie op te lossen.|
|[Quarantaine problemen inrichten](#provisioning-quarantined-issues)|Meer informatie over het inrichten van een quarantaine probleem en hoe u deze kunt oplossen.|


## <a name="agent-issues"></a>Agent problemen
Enkele van de eerste dingen die u met de agent wilt verifiëren, zijn:


-  is dit geïnstalleerd?
-  wordt de agent lokaal uitgevoerd?
-  bevindt de agent zich in de portal?
-  is de agent gemarkeerd als in orde?  

Deze items kunnen worden geverifieerd in de Azure Portal en op de lokale server waarop de-agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Verificatie van Azure Portal-agent

Als u wilt controleren of de agent wordt gedetecteerd door Azure, voert u de volgende stappen uit:

1. Meld u aan bij Azure Portal.
2. Selecteer aan de linkerkant **Azure Active Directory**, klik op **Azure AD Connect** en selecteer in het midden de optie **inrichting beheren (preview)** .
3.  Klik in het scherm **Azure AD inrichten (preview)** op **Alle agents controleren**.
 ![Azure AD-inrichtings](media/how-to-install/install7.png)</br>
 
4. Op het **scherm on-premises Provisioning agents** ziet u de agents die u hebt geïnstalleerd.  Controleer of de agent in kwestie is en is gemarkeerd als **in orde**.
 ![inrichten agents](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>De poort controleren

Als u wilt controleren of het Azure luistert op poort 443 en dat uw agent ermee kan communiceren, kunt u het volgende hulp programma gebruiken:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Met deze test wordt gecontroleerd of uw agents kunnen communiceren met Azure via poort 443.  Open een browser en navigeer naar de bovenstaande URL van de server waarop de agent is geïnstalleerd.
 ![Services](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Op de lokale server

Voer de volgende stappen uit om te controleren of de agent wordt uitgevoerd:

1.  Op de server waarop de agent is geïnstalleerd, opent u **Services** door ernaar te navigeren of door naar start/uitvoeren/services. msc te gaan.
2.  Zorg er bij **Services**voor dat **Microsoft Azure AD connect agent updater** en **Microsoft Azure AD Connect inrichtings agent** zijn, en of de status wordt **uitgevoerd**.
 ![Services](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-issues"></a>Veelvoorkomende problemen met de agent installatie

Hier volgen enkele veelvoorkomende problemen met de agent installatie en wat de typische oplossing is.

#### <a name="agent-failed-to-start"></a>De agent is niet gestart

Als er een fout bericht wordt weer gegeven waarin staat dat:

**De service Microsfoft Azure AD Connect inrichtings agent is niet gestart.  Controleer of u voldoende rechten hebt om de systeem services te starten.** 

Dit wordt meestal veroorzaakt door een groeps beleid waardoor er geen machtigingen kunnen worden toegepast op de lokale NT-Service "aanmeldings account" die is gemaakt door het installatie programma (NT SERVICE\AADConnectProvisioningAgent) deze machtigingen zijn vereist voor het starten van de service.

Gebruik de volgende stappen om dit probleem op te lossen:

1.  Meld u aan bij de server met een beheerders account
2.  Open **Services** door ernaar te navigeren of door naar start/uitvoeren/services. msc te gaan.
3.  Dubbel klik onder **Services** op **Microsoft Azure AD verbinding inrichten agent**
4. Wijzig op het tabblad het aanmeldings account in een domein beheerder en start de service opnieuw. 

 ![Services](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Er is een time-out van de agent of het certificaat is ongeldig

Mogelijk worden de volgende fouten weer geven als u probeert de agent te registreren.

 ![Services](media/how-to-troubleshoot/troubleshoot4.png)

Dit wordt meestal veroorzaakt doordat de agent geen verbinding kan maken met de hybride identiteits service en de HTTP-proxy moet configureren.  Als u dit wilt oplossen, configureert u een uitgaande proxy. 

De inrichtings agent ondersteunt het gebruik van een uitgaande proxy. U kunt deze configureren door het configuratie bestand van de agent te bewerken **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Voeg de volgende regels toe aan het einde van het bestand net vóór de afsluitende `</configuration>` label.
Vervang de variabelen [proxy-server] en [proxy-port] door de naam en poort waarden van de proxy server.

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

Mogelijk wordt de volgende fout weer geven bij het installeren van de Cloud-inrichtings agent.

Dit wordt meestal veroorzaakt door de agent waardoor de Power shell-registratie scripts niet kunnen worden uitgevoerd vanwege lokaal Power shell-uitvoerings beleid.

Als u dit wilt oplossen, wijzigt u het Power shell-uitvoerings beleid op de-server. U moet een computer-en gebruikers beleid hebben als ' ongedefinieerd ' of ' RemoteSigned '. Als het ' onbeperkt ' is, wordt deze fout weer geven.  Zie [Power shell Execution policies](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)(Engelstalig) voor meer informatie. 

### <a name="log-files"></a>Logboekbestanden

De agent verzendt standaard zeer minimale fout berichten en stack tracerings gegevens. U vindt deze traceer Logboeken in de map: **C:\PROGRAMDATA\MICROSOFT\AZURE AD Connect Provisioning Agent\Trace**

Gebruik de volgende stappen om aanvullende informatie te verzamelen voor het oplossen van problemen met betrekking tot agents.

1. De service **Microsoft Azure AD verbinding inrichten agent** stoppen
2. Maak een kopie van het oorspronkelijke configuratie bestand: **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**
3. Vervang de bestaande < systeem. Diagnostische gegevens > sectie met het volgende en alle tracerings berichten gaan naar het bestand **ProvAgentTrace. log**

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
4. De service **Microsoft Azure AD verbinding inrichten agent** starten
5. U kunt de volgende opdracht gebruiken om het bestand af te sluiten en problemen op te lossen: 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-issues"></a>Problemen met object synchronisatie

De volgende sectie bevat informatie over het oplossen van object synchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken

In de Azure Portal kunnen inrichtings logboeken worden gebruikt om problemen met object synchronisatie op te sporen en op te lossen.  Selecteer **Logboeken**om de logboeken weer te geven.
 ![inrichtings logboeken](media/how-to-troubleshoot/log1.png)

De inrichtings logboeken bieden een schat aan informatie over de status van de objecten die worden gesynchroniseerd tussen uw on-premises AD-omgeving en Azure.

 ![Inrichtingslogboeken](media/how-to-troubleshoot/log2.png)

U kunt de vervolg keuzelijsten boven aan de pagina gebruiken om de weer gave te filteren op nul in op specifieke problemen, datums, enzovoort.  Als u dubbelklikt op een afzonderlijke gebeurtenis, krijgt u meer gedetailleerde informatie.

 ![Inrichtingslogboeken](media/how-to-troubleshoot/log3.png)

Deze informatie bevat gedetailleerde stappen en waar het synchronisatie probleem plaatsvindt.  Zo kunt u de exacte plaats van het probleem op nul zetten en lokaliseren.


## <a name="provisioning-quarantined-issues"></a>In quarantaine geplaatste problemen inrichten

Met Cloud Provisioning wordt de status van uw configuratie gecontroleerd en worden de beschadigde objecten in de status ' quarantaine ' geplaatst. Als de meeste of alle aanroepen voor het doel systeem consistent mislukken vanwege een fout, bijvoorbeeld ongeldige beheerders referenties, wordt de inrichtings taak gemarkeerd als in quarantaine.

 ![Voorschriften](media/how-to-troubleshoot/quarantine1.png)

Als u op de status klikt, kunt u aanvullende informatie over de quarantaine bekijken.  U kunt ook de fout code en het bericht ophalen.

 ![Voorschriften](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolving-a-quarantine"></a>Een quarantaine omzetten

- Gebruik de Azure Portal om de inrichtings taak opnieuw te starten. Selecteer **inrichting opnieuw starten**op de configuratie pagina van de agent.

  ![Voorschriften](media/how-to-troubleshoot/quarantine3.png)

- Gebruik Microsoft Graph om [de inrichtings taak opnieuw te starten](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). U hebt volledige controle over wat u opnieuw opstart. U kunt ervoor kiezen om de borg te wissen (om de borg teller voor de quarantaine status opnieuw te starten), de quarantaine te wissen (de toepassing uit quarantaine te verwijderen) of door water merken te wissen. Gebruik de volgende aanvraag:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichten?](what-is-provisioning.md)
- [Wat is Azure AD Connect Cloud inrichting?](what-is-cloud-provisioning.md)



