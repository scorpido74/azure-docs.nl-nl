---
title: Hybride verbindingen
description: Meer informatie over het maken en gebruiken van hybride verbindingen in Azure App Service om toegang te krijgen tot bronnen in verschillende netwerken.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: seodec18, fasttrack-edit
ms.openlocfilehash: ec842530f3cae26b869a649617f279d204b98fcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80047781"
---
# <a name="azure-app-service-hybrid-connections"></a>Hybride verbindingen van Azure App Service

Hybride verbindingen is zowel een service in Azure als een functie in Azure App Service. Als een service, het heeft toepassingen en mogelijkheden dan die worden gebruikt in App Service. Zie Hybride verbindingen voor [Azure Relay][HCService]voor meer informatie over hybride verbindingen en het gebruik ervan buiten de App-service.

Binnen App Service kunnen hybride verbindingen worden gebruikt om toegang te krijgen tot toepassingsbronnen in andere netwerken. Het biedt toegang van uw app tot een eindpunt van een toepassing. Het maakt geen alternatieve mogelijkheid om toegang te krijgen tot uw toepassing. Zoals gebruikt in App Service, elke hybride verbinding correleert met een enkele TCP-host en poort combinatie. Dit betekent dat het hybrid connection-eindpunt op elk besturingssysteem en elke toepassing kan worden ingeschakeld, mits u toegang hebt tot een TCP-luisterpoort. De functie Hybride verbindingen weet niet of geeft om wat het toepassingsprotocol is of waar u toegang toe hebt. Het is gewoon het verstrekken van toegang tot het netwerk.  


## <a name="how-it-works"></a>Hoe werkt het? ##
De functie Hybride verbindingen bestaat uit twee uitgaande oproepen naar Azure Service Bus Relay. Er is een verbinding vanuit een bibliotheek op de host waar uw app wordt uitgevoerd in App Service. Er is ook een verbinding van de Hybrid Connection Manager (HCM) naar Service Bus Relay. De HCM is een relayservice die u implementeert binnen het netwerk dat de bron host die u probeert te openen. 

Via de twee samengevoegde verbindingen heeft uw app een TCP-tunnel naar een vaste host:port-combinatie aan de andere kant van de HCM. De verbinding maakt gebruik van TLS 1.2 voor sas-sleutels (Security and Shared Access Signature) voor verificatie en autorisatie.    

![Diagram van hybride verbinding op hoog niveau][1]

Wanneer uw app een DNS-verzoek invoert dat overeenkomt met een geconfigureerd eindpunt voor hybride verbinding, wordt het uitgaande TCP-verkeer omgeleid via de hybride verbinding.  

> [!NOTE]
> Dit betekent dat u moet proberen om altijd een DNS-naam te gebruiken voor uw hybride verbinding. Sommige clientsoftware doet geen DNS-lookup als het eindpunt in plaats daarvan een IP-adres gebruikt. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Voordelen voor hybride verbinding van App Service ###

De mogelijkheid voor hybride verbindingen biedt een aantal voordelen, waaronder:

- Apps hebben veilig toegang tot on-premises systemen en services.
- De functie vereist geen internettoegankelijk eindpunt.
- Het is snel en eenvoudig op te zetten. 
- Elke hybride verbinding komt overeen met één host:poortcombinatie, handig voor de beveiliging.
- Het vereist normaal gesproken geen firewall gaten. De verbindingen zijn allemaal uitgaand via standaard webpoorten.
- Omdat de functie netwerkniveau is, is deze agnostisch voor de taal die door uw app wordt gebruikt en de technologie die door het eindpunt wordt gebruikt.
- Het kan worden gebruikt om toegang te bieden in meerdere netwerken vanuit één app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Dingen die u niet doen met hybride verbindingen ###

Dingen die u niet doen met hybride verbindingen zijn:

- Zet een station op.
- Gebruik UDP.
- Toegang tot TCP-gebaseerde services die gebruikmaken van dynamische poorten, zoals FTP Passive Mode of Extended Passive Mode.
- Ondersteuning voor LDAP, omdat udp nodig kan hebben.
- Ondersteuning voor Active Directory, omdat u geen lid worden van een App-servicemedewerker.

### <a name="prerequisites"></a>Vereisten ###
 - Windows App-service is vereist. Het is alleen beschikbaar in Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hybride verbindingen toevoegen en maken in uw app ##

Als u een hybride verbinding wilt maken, gaat u naar de [Azure-portal][portal] en selecteert u uw app. Selecteer **Netwerkinstellingen** > **Configureren van uw eindpunten voor hybride verbinding**. Hier ziet u de hybride verbindingen die zijn geconfigureerd voor uw app.  

![Schermafbeelding van de lijst Hybride verbinding][2]

Als u een nieuwe hybride verbinding wilt toevoegen, selecteert u **[+] Hybride verbinding toevoegen**.  U ziet een lijst met de hybride verbindingen die u al hebt gemaakt. Als u een of meer van deze apps aan uw app wilt toevoegen, selecteert u de gewenste apps en selecteert u **Geselecteerde hybride verbinding toevoegen**.  

![Schermafbeelding van hybride verbindingsportal][3]

Als u een nieuwe hybride verbinding wilt maken, selecteert u **Nieuwe hybride verbinding maken**. Geef de volgende op: 

- Naam hybride verbinding.
- Hostname van eindpunt.
- Eindpuntpoort.
- Service Bus naamruimte die u wilt gebruiken.

![Schermafbeelding van het dialoogvenster Nieuwe hybride verbinding maken][4]

Elke hybride verbinding is gekoppeld aan een naamruimte van een ServiceBus en elke naamruimte van servicebus bevindt zich in een Azure-gebied. Het is belangrijk om te proberen een servicebusnaamruimte te gebruiken in dezelfde regio als uw app, om latentie van het netwerk te voorkomen.

Als u uw hybride verbinding uit uw app wilt verwijderen, klikt u er met de rechtermuisknop op en selecteert u **Verbreken**.  

Wanneer een hybride verbinding aan uw app wordt toegevoegd, u details op uw app zien door deze eenvoudig te selecteren. 

![Schermafbeelding van details van hybride verbindingen][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Een hybride verbinding maken in de Azure Relay-portal ###

Naast de portalervaring vanuit uw app, u hybride verbindingen maken vanuit de Azure Relay-portal. Als u een hybride verbinding wilt gebruiken door App Service, moet deze:

* Toestemming van de klant vereisen.
* Een metagegevensitem met de naam Endpoint hebben, dat een host:poortcombinatie als waarde bevat.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybride verbindingen en app-serviceplannen ##

Hybride verbindingen voor app-service zijn alleen beschikbaar in Basic-, Standard-, Premium- en Geïsoleerde prijs-SKU's. Er zijn grenzen verbonden aan het prijsplan.  

| Prijsplan | Aantal hybride verbindingen dat bruikbaar is in het plan |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Geïsoleerd | 200 |

De gebruikersinterface van het App-serviceplan laat zien hoeveel hybride verbindingen worden gebruikt en door welke apps.  

![Schermafbeelding van eigenschappen van app-service-abonnement][6]

Selecteer de hybride verbinding om details te bekijken. U alle informatie die u in de app-weergave hebt gezien, zien. U ook zien hoeveel andere apps in hetzelfde abonnement die hybride verbinding gebruiken.

Er is een limiet aan het aantal hybrid connection-eindpunten dat kan worden gebruikt in een App Service-abonnement. Elke hybride verbinding die wordt gebruikt, kan echter worden gebruikt voor een willekeurig aantal apps in dat abonnement. Een enkele hybride verbinding die wordt gebruikt in vijf afzonderlijke apps in een App Service-abonnement, telt bijvoorbeeld als één hybride verbinding.

### <a name="pricing"></a>Prijzen ###

Naast het feit dat er een SKU-vereiste voor appservice-abonnement is, zijn er extra kosten verbonden aan het gebruik van hybride verbindingen. Er zijn kosten verbonden aan elke luisteraar die wordt gebruikt door een hybride verbinding. De listener is de Hybrid Connection Manager. Als u vijf hybride verbindingen had die door twee Hybride Verbindingsmanagers worden gesteund, zouden dat 10 luisteraars zijn. Zie [Service Bus-prijzen voor][sbpricing]meer informatie.

## <a name="hybrid-connection-manager"></a>Hybride verbindingsbeheer ##

De functie Hybride verbindingen vereist een relayagent in het netwerk die uw eindpunt voor hybride verbinding host. Die relaisagent wordt de Hybrid Connection Manager (HCM) genoemd. Als u HCM wilt downloaden, selecteert u **Netwerkinstellingen** > **voor hybride verbinding**configureren in uw app in de [Azure-portal.][portal]  

Deze tool wordt uitgevoerd op Windows Server 2012 en hoger. De HCM draait als een service en verbindt uitgaande met Azure Relay op poort 443.  

Na het installeren van HCM u HybridConnectionManagerUi.exe uitvoeren om de gebruikersinterface voor het hulpprogramma te gebruiken. Dit bestand bevindt zich in de installatiemap Hybrid Connection Manager. In Windows 10 u ook gewoon zoeken naar *de gebruikersinterface van Hybrid Connection Manager* in uw zoekvak.  

![Schermafbeelding van Hybride Verbindingsbeheer][7]

Wanneer u de HCM-gebruikersinterface start, ziet u eerst een tabel met alle hybride verbindingen die zijn geconfigureerd met dit exemplaar van de HCM. Als u wijzigingen wilt aanbrengen, moet u zich eerst verifiëren met Azure. 

Ga als een mogelijk doen er met een of meer hybride verbindingen over:

1. Start de HCM UI.
2. Selecteer **Een andere hybride verbinding configureren**.
![Schermafbeelding van Nieuwe hybride verbindingen configureren][8]

1. Meld u aan met uw Azure-account om uw hybride verbindingen beschikbaar te krijgen met uw abonnementen. De HCM blijft uw Azure-account niet verder gebruiken. 
1. Kies een abonnement.
1. Selecteer de hybride verbindingen die u wilt dat de HCM doorgeeft.
![Schermafbeelding van hybride verbindingen][9]

1. Selecteer **Opslaan**.

U nu de hybride verbindingen zien die u hebt toegevoegd. U ook de geconfigureerde hybride verbinding selecteren om details te zien.

![Schermafbeelding van hybride verbindingsgegevens][10]

Om de hybride verbindingen waarmee het is geconfigureerd te ondersteunen, vereist HCM:

- TCP-toegang tot Azure via poort 443.
- TCP-toegang tot het eindpunt hybride verbinding.
- De mogelijkheid om DNS-look-ups uit te brengen op de endpointhost en de naamruimte servicebus.

> [!NOTE]
> Azure Relay is afhankelijk van Web Sockets voor connectiviteit. Deze mogelijkheid is alleen beschikbaar op Windows Server 2012 of hoger. Hierdoor wordt HCM niet ondersteund op iets eerder dan Windows Server 2012.
>

### <a name="redundancy"></a>Redundantie ###

Elke HCM kan meerdere hybride verbindingen ondersteunen. Ook kan een bepaalde Hybrid Connection worden ondersteund door meerdere HCMs. Het standaardgedrag is om verkeer over de geconfigureerde HCM's te routeren voor een bepaald eindpunt. Als u een hoge beschikbaarheid wilt op uw hybride verbindingen vanuit uw netwerk, voert u meerdere HCM's uit op afzonderlijke machines. Het algoritme voor belastingverdeling dat door de relayservice wordt gebruikt om verkeer naar de HCM's te distribueren, is een willekeurige toewijzing. 

### <a name="manually-add-a-hybrid-connection"></a>Handmatig een hybride verbinding toevoegen ###

Als u iemand buiten uw abonnement in staat wilt stellen een HCM-exemplaar voor een bepaalde hybride verbinding te hosten, deelt u de tekenreeks gatewayverbinding voor de hybride verbinding met hen. U de tekenreeks voor de gatewayverbinding zien in de eigenschappen hybride verbinding in de [Azure-portal.][portal] Als u die tekenreeks wilt gebruiken, selecteert u **Handmatig invoeren** in de HCM en plakt u in de tekenreeks gatewayverbinding.

![Handmatig een hybride verbinding toevoegen][11]

### <a name="upgrade"></a>Upgraden ###

Er zijn periodieke updates voor de Hybrid Connection Manager om problemen op te lossen of verbeteringen aan te bieden. Wanneer upgrades worden uitgebracht, verschijnt er een pop-up in de HCM-gebruikersinterface. Als u de upgrade toepast, worden de wijzigingen toegepast en wordt de HCM opnieuw opgestart. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Een hybride verbinding programmatisch toevoegen aan uw app ##

De onderstaande API's kunnen direct worden gebruikt om de hybride verbindingen die zijn verbonden met uw apps te beheren. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Het JSON-object dat is gekoppeld aan een hybride verbinding ziet eruit als volgt:

    {
      "name": "[hybrid connection name]",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "[location]",
      "properties": {
        "serviceBusNamespace": "[namespace name]",
        "relayName": "[hybrid connection name]",
        "relayArmUri": "/subscriptions/[subscription id]/resourceGroups/[resource group name]/providers/Microsoft.Relay/namespaces/[namespace name]/hybridconnections/[hybrid connection name]",
        "hostName": "[endpoint host name]",
        "port": [port],
        "sendKeyName": "defaultSender",
        "sendKeyValue": "[send key]"
      }
    }

Een manier om deze informatie te gebruiken is met de armclient, die u krijgen van het [ARMClient][armclient] GitHub-project. Hier vindt u een voorbeeld van het koppelen van een reeds bestaande hybride verbinding aan uw app. Maak een JSON-bestand volgens het bovenstaande schema, zoals:

    {
      "name": "relay-demo-hc",
      "type": "Microsoft.Relay/Namespaces/HybridConnections",
      "location": "North Central US",
      "properties": {
        "serviceBusNamespace": "demo-relay",
        "relayName": "relay-demo-hc",
        "relayArmUri": "/subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myrelay-rg/providers/Microsoft.Relay/namespaces/demo-relay/hybridconnections/relay-demo-hc",
        "hostName": "my-wkstn.home",
        "port": 1433,
        "sendKeyName": "defaultSender",
        "sendKeyValue": "Th9is3is8a82lot93of3774stu887ff122235="
      }
    }

Als u deze API wilt gebruiken, hebt u de verzendsleutel en relay resource-ID nodig. Als u uw gegevens hebt opgeslagen met de bestandsnaam hctest.json, geeft u deze opdracht uit om uw hybride verbinding aan uw app toe te voegen: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Uw hybride verbindingen beveiligen ##

Een bestaande hybride verbinding kan worden toegevoegd aan andere Web Apps van App Service door elke gebruiker die voldoende machtigingen heeft voor het onderliggende Azure Service Bus Relay. Dit betekent dat als u moet voorkomen dat anderen diezelfde hybride verbinding opnieuw gebruiken (bijvoorbeeld wanneer de doelbron een service is die geen aanvullende beveiligingsmaatregelen heeft om ongeautoriseerde toegang te voorkomen), moet u de toegang tot de Azure vergrendelen Service Bus Relay.

Iedereen `Reader` met toegang tot het relay kan de hybride verbinding _zien_ wanneer deze wordt toegevoegd aan zijn web-app in de Azure Portal, maar ze kunnen deze niet _toevoegen_ omdat ze niet over de machtigingen beschikken om de verbindingstekenreeks op te halen die wordt gebruikt om de relayverbinding tot stand te brengen. Om de hybride verbinding met succes toe `listKeys` te`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`voegen, moeten ze de toestemming hebben ( ). De `Contributor` rol of een andere rol die deze machtiging op de Relay bevat, stelt gebruikers in staat om de hybride verbinding te gebruiken en toe te voegen aan hun eigen web-apps.

## <a name="troubleshooting"></a>Problemen oplossen ##

De status van "Verbonden" betekent dat ten minste één HCM is geconfigureerd met die hybride verbinding en azure kan bereiken. Als de status voor uw hybride verbinding niet **verbonden**staat, is uw hybride verbinding niet geconfigureerd op een HCM die toegang heeft tot Azure.

De belangrijkste reden dat clients geen verbinding kunnen maken met hun eindpunt is omdat het eindpunt is opgegeven met behulp van een IP-adres in plaats van een DNS-naam. Als uw app het gewenste eindpunt niet kan bereiken en u een IP-adres hebt gebruikt, schakelt u over naar het gebruik van een DNS-naam die geldig is op de host waar de HCM wordt uitgevoerd. Controleer ook of de DNS-naam correct wordt opgelost op de host waar de HCM wordt uitgevoerd. Controleer of er verbinding is van de host waar de HCM wordt uitgevoerd naar het eindpunt hybride verbinding.  

In App Service kan het gereedschap opdrachtregel voor **tcpping** worden aangeroepen via de Console Advanced Tools (Kudu). Deze tool kan u vertellen of u toegang hebt tot een TCP-eindpunt, maar het vertelt u niet of u toegang hebt tot een eindpunt voor hybride verbinding. Wanneer u het gereedschap in de console gebruikt tegen een eindpunt voor hybride verbinding, bevestigt u alleen dat het een host:poortcombinatie gebruikt.  

Als u een opdrachtregelclient voor uw eindpunt hebt, u de verbinding testen vanaf de app-console. U bijvoorbeeld de toegang tot webservereindpunten testen met behulp van krul.

## <a name="biztalk-hybrid-connections"></a>Hybride verbindingen biztalk ##

De vroege vorm van deze functie heette BizTalk Hybrid Connections. Deze mogelijkheid ging end of life op 31 mei 2018 en stopte de activiteiten. BizTalk hybride verbindingen zijn verwijderd uit alle apps en zijn niet toegankelijk via de portal of API. Als u deze oudere verbindingen nog steeds hebt geconfigureerd in Hybride Verbindingsbeheer, ziet u een status van Stopgezet en geeft u onderaan een instructie End of Life weer.

![BizTalk hybride verbindingen in de HCM][12]


<!--Image references-->
[1]: ./media/app-service-hybrid-connections/hybridconn-connectiondiagram.png
[2]: ./media/app-service-hybrid-connections/hybridconn-portal.png
[3]: ./media/app-service-hybrid-connections/hybridconn-addhc.png
[4]: ./media/app-service-hybrid-connections/hybridconn-createhc.png
[5]: ./media/app-service-hybrid-connections/hybridconn-properties.png
[6]: ./media/app-service-hybrid-connections/hybridconn-aspproperties.png
[7]: ./media/app-service-hybrid-connections/hybridconn-hcm.png
[8]: ./media/app-service-hybrid-connections/hybridconn-hcmadd.png
[9]: ./media/app-service-hybrid-connections/hybridconn-hcmadded.png
[10]: ./media/app-service-hybrid-connections/hybridconn-hcmdetails.png
[11]: ./media/app-service-hybrid-connections/hybridconn-manual.png
[12]: ./media/app-service-hybrid-connections/hybridconn-bt.png

<!--Links-->
[HCService]: https://docs.microsoft.com/azure/service-bus-relay/relay-hybrid-connections-protocol/
[portal]: https://portal.azure.com/
[oldhc]: https://docs.microsoft.com/azure/biztalk-services/integration-hybrid-connection-overview/
[sbpricing]: https://azure.microsoft.com/pricing/details/service-bus/
[armclient]: https://github.com/projectkudu/ARMClient/
