---
title: Azure AD Connect cloud provisioning troubleshooting Azure AD Connect cloud provisioning troubleshooting Azure AD Connect cloud provisioning troubleshooting Azure AD
description: In dit artikel wordt beschreven hoe u problemen oplost die zich kunnen voordoen bij de cloudprovisioning-agent.
author: billmath
ms.author: billmath
manager: daveba
ms.date: 12/02/2019
ms.topic: article
ms.prod: windows-server-threshold
ms.technology: identity-adfs
ms.openlocfilehash: e41be4b76245f2567015eb0ede317830120ee61a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75549482"
---
# <a name="cloud-provisioning-troubleshooting"></a>Problemen met cloudprovisioning oplossen

Cloudprovisioning raakt veel verschillende dingen en heeft veel verschillende afhankelijkheden. Deze brede reikwijdte kan tot verschillende problemen leiden. In dit artikel u deze problemen oplossen. Het introduceert de typische gebieden voor u om zich te concentreren op, hoe u extra informatie te verzamelen, en de verschillende technieken die u gebruiken om problemen op te sporen.


## <a name="common-troubleshooting-areas"></a>Veelvoorkomende probleemoplossingsgebieden

|Name|Beschrijving|
|-----|-----|
|[Agent problemen](#agent-problems)|Controleer of de agent correct is geïnstalleerd en of deze communiceert met Azure Active Directory (Azure AD).|
|[Synchronisatieproblemen met objecten](#object-synchronization-problems)|Gebruik inrichten van logboeken om problemen met objectsynchronisatie op te lossen.|
|[In quarantaine geplaatste problemen](#provisioning-quarantined-problems)|Begrijp quarantaineproblemen en hoe u deze oplossen.|


## <a name="agent-problems"></a>Agent problemen
Enkele van de eerste dingen die u wilt verifiëren met de agent zijn:

-  Is het geïnstalleerd?
-  Is de agent lokaal actief?
-  Zit de agent in het portaal?
-  Is de agent gemarkeerd als gezond?

Deze items kunnen worden geverifieerd in de Azure-portal en op de lokale server waarop de agent wordt uitgevoerd.

### <a name="azure-portal-agent-verification"></a>Azure-portalagentverificatie

Voer de volgende stappen uit om te controleren of de agent wordt gezien door Azure en in orde is.

1. Meld u aan bij Azure Portal.
1. Selecteer aan de linkerkant **Azure Active Directory** > **Azure AD Connect**. Selecteer in het midden **De optie Inrichting beheren (voorbeeld)**.
1. Selecteer alle **agents**controleren in het scherm **Azure AD Provisioning (preview).**

   ![Alle agenten bekijken](media/how-to-install/install7.png)</br>
 
1. Op het scherm **On-premises inrichtingsagenten** ziet u de agents die u hebt geïnstalleerd. Controleer of de agent in kwestie er is en *gemarkeerd is als Gezond*.

   ![Scherm van on-premises inrichtingsagenten](media/how-to-install/install8.png)</br>

### <a name="verify-the-port"></a>De poort verifiëren

Als u wilt controleren of Azure luistert op poort 443 en dat uw agent ermee kan communiceren, gebruikt u het volgende hulpprogramma:

https://aadap-portcheck.connectorporttest.msappproxy.net/ 

Deze test controleert of uw agents kunnen communiceren met Azure via poort 443. Open een browser en ga naar de vorige URL vanaf de server waar de agent is geïnstalleerd.

![Verificatie van de bereikbaarheid van de poort](media/how-to-install/verify2.png)

### <a name="on-the-local-server"></a>Op de lokale server

Voer deze stappen uit om te controleren of de agent wordt uitgevoerd.

1. Open **Services** op de server met de installatie van de agent door ernaar te navigeren of door naar **Run** > **Services.msc****Run** > te starten.
1. Controleer **onder Services**of Microsoft Azure AD Connect Agent **Updater** en Microsoft Azure AD Connect **Provisioning Agent** aanwezig zijn en dat hun status wordt *uitgevoerd.*

   ![Servicesscherm](media/how-to-troubleshoot/troubleshoot1.png)

### <a name="common-agent-installation-problems"></a>Problemen met de installatie van veelvoorkomende agents

In de volgende secties worden enkele veelvoorkomende problemen met de installatie van agenten en typische oplossingen beschreven.

#### <a name="agent-failed-to-start"></a>Agent kan niet starten

Mogelijk ontvangt u een foutbericht waarin staat:

**Service 'Microsoft Azure AD Connect Provisioning Agent' kan niet worden gestart. Controleer of u over voldoende bevoegdheden beschikt om de systeemservices te starten.** 

Dit probleem wordt meestal veroorzaakt door een groepsbeleid dat voorkomt dat machtigingen worden toegepast op het lokale aanmeldingsaccount van NT-service dat is gemaakt door de installatieprogramma (NT SERVICE\AADConnectProvisioningAgent). Deze machtigingen zijn vereist om de service te starten.

Voer de volgende stappen uit om dit probleem op te lossen.

1. Meld u aan bij de server met een beheerdersaccount.
1. Open **Services** door ernaar te navigeren of door naar **Run** > **Services.msc****Run** > te gaan.
1. Dubbelklik onder **Services**op **Microsoft Azure AD Connect Provisioning Agent**.
1. Wijzig **dit** **account** op het tabblad Aanmelden in een domeinbeheerder. Start vervolgens de service opnieuw. 

   ![Tabblad Aanmelden](media/how-to-troubleshoot/troubleshoot3.png)

#### <a name="agent-times-out-or-certificate-is-invalid"></a>Agent-uit- of certificaat is ongeldig

Mogelijk krijgt u het volgende foutbericht wanneer u de agent probeert te registreren.

![Foutbericht time-out](media/how-to-troubleshoot/troubleshoot4.png)

Dit probleem wordt meestal veroorzaakt doordat de agent geen verbinding kan maken met de Hybride Identiteitsservice en vereist dat u een HTTP-proxy configureert. Als u dit probleem wilt oplossen, configureert u een uitgaande proxy. 

De inrichtingsagent ondersteunt het gebruik van een uitgaande proxy. U het configureren door het agentconfig-bestand *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*te bewerken. Voeg de volgende regels toe, tegen het einde `</configuration>` van het bestand vlak voor de afsluitende tag.
Vervang de `[proxy-server]` variabelen `[proxy-port]` en door de naam en poortwaarden van uw proxyserver.

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

#### <a name="agent-registration-fails-with-security-error"></a>Agentregistratie mislukt met beveiligingsfout

U krijgt mogelijk een foutmelding wanneer u de cloudprovisioning-agent installeert.

Dit probleem wordt meestal veroorzaakt doordat de agent de PowerShell-registratiescripts niet kan uitvoeren vanwege lokaal PowerShell-uitvoeringsbeleid.

Als u dit probleem wilt oplossen, wijzigt u het uitvoeringsbeleid van PowerShell op de server. U moet het beleid voor machines en gebruikers hebben ingesteld als *Niet gedefinieerd* of *Extern ondertekend.* Als ze zijn ingesteld als *Onbeperkt,* ziet u deze fout. Zie [PowerShell-uitvoeringsbeleid](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6)voor meer informatie . 

### <a name="log-files"></a>Logboekbestanden

Standaard zendt de agent minimale foutberichten uit en bevat de traceringsinformatie. U deze traceringslogboeken vinden in de map *C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace*.

Voer de volgende stappen uit om aanvullende details te verzamelen voor het oplossen van problemen met agentproblemen.

1. Stop de service **Microsoft Azure AD Connect Provisioning Agent**.
1. Maak een kopie van het oorspronkelijke config-bestand: *C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config*.
1. Vervang de `<system.diagnostics>` bestaande sectie door het volgende en alle traceerberichten gaan naar het bestand *ProvAgentTrace.log*.

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
1. Start de service **Microsoft Azure AD Connect Provisioning Agent**.
1. Gebruik de volgende opdracht om de problemen met het bestand en de foutopsporing te volgen. 
    ```
    Get-Content “C:/ProgramData/Microsoft/Azure AD Connect Provisioning Agent/Trace/ProvAgentTrace.log” -Wait
    ```
## <a name="object-synchronization-problems"></a>Synchronisatieproblemen met objecten

In de volgende sectie vindt u informatie over het oplossen van problemen met objectsynchronisatie.

### <a name="provisioning-logs"></a>Inrichtingslogboeken

In de Azure-portal kunnen inloglogboeken worden gebruikt om problemen met objectsynchronisatie op te sporen en op te lossen. Als u de logboeken wilt weergeven, selecteert u **Logboeken**.

![Knop Logboeken](media/how-to-troubleshoot/log1.png)

Provisioning logs bieden een schat aan informatie over de status van de objecten die worden gesynchroniseerd tussen uw on-premises Active Directory-omgeving en Azure.

![Scherm Logboeken inrichten](media/how-to-troubleshoot/log2.png)

U de vervolgkeuzevakken boven aan de pagina gebruiken om de weergave te filteren op nul in specifieke problemen, zoals datums. Dubbelklik op een afzonderlijke gebeurtenis om aanvullende informatie te bekijken.

![Vervolgkeuzelijstgegevens voor het inrichten van logboeken](media/how-to-troubleshoot/log3.png)

Deze informatie bevat gedetailleerde stappen en waar het synchronisatieprobleem zich voordoet. Op deze manier u de exacte plek van het probleem lokaliseren.


## <a name="provisioning-quarantined-problems"></a>In quarantaine geplaatste problemen

Cloudprovisioning bewaakt de status van uw configuratie en plaatst ongezonde objecten in een quarantainestatus. Als de meeste of alle oproepen tegen het doelsysteem consistent mislukken als gevolg van een fout, bijvoorbeeld ongeldige beheerdersreferenties, wordt de inrichtingstaak gemarkeerd als in quarantaine.

![Quarantinestatus](media/how-to-troubleshoot/quarantine1.png)

Als u de status selecteert, ziet u aanvullende informatie over de quarantaine. U ook de foutcode en het bericht verkrijgen.

![Informatie over quarantainestatus](media/how-to-troubleshoot/quarantine2.png)

### <a name="resolve-a-quarantine"></a>Een quarantaine oplossen

- Gebruik de Azure-portal om de inrichtingstaak opnieuw te starten. Selecteer op de configuratiepagina van de agent de optie **Inrichting opnieuw starten**.

  ![Inrichting opnieuw opstarten](media/how-to-troubleshoot/quarantine3.png)

- Gebruik Microsoft Graph om [de inrichtingstaak opnieuw te starten.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) Je hebt volledige controle over wat je opnieuw start. U ervoor kiezen om duidelijk:
  - Borgs, om het borgteller te herstarten dat naar quarantainestatus wordt opgebouwd.
  - Quarantaine, om de toepassing uit quarantaine te verwijderen.
  - Watermerken. 
  
  Gebruik de volgende aanvraag:
 
  `POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart`

## <a name="next-steps"></a>Volgende stappen 

- [Wat is inrichting?](what-is-provisioning.md)
- [Wat is Azure AD Connect-cloudinrichting?](what-is-cloud-provisioning.md)



