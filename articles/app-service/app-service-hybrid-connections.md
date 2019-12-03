---
title: Hybride verbindingen
description: Meer informatie over het maken en gebruiken van hybride verbindingen in Azure App Service om toegang te krijgen tot bronnen in verschillende netwerken.
author: ccompy
ms.assetid: 66774bde-13f5-45d0-9a70-4e9536a4f619
ms.topic: article
ms.date: 06/06/2019
ms.author: ccompy
ms.custom: fasttrack-edit
ms.openlocfilehash: ffc5ee32541cfbbda2ae54fd229c1436f133d730
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671513"
---
# <a name="azure-app-service-hybrid-connections"></a>Azure App Service Hybride verbindingen #

Hybride verbindingen is zowel een service in azure als een functie in Azure App Service. Als service heeft het gebruik en de mogelijkheden die groter zijn dan de functies die worden gebruikt in App Service. Zie [Azure Relay hybride verbindingen][HCService]voor meer informatie over hybride verbindingen en het gebruik ervan buiten app service.

In App Service kan Hybride verbindingen worden gebruikt voor toegang tot toepassings bronnen in andere netwerken. Het biedt toegang vanuit uw app tot een eind punt van de toepassing. Er is geen alternatieve mogelijkheid om toegang te krijgen tot uw toepassing. De hybride verbinding wordt gebruikt in App Service en is afgestemd op één combi natie van TCP-host en poort. Dit betekent dat het hybride verbindings eindpunt zich op elk besturings systeem en elke toepassing kan bevinden, mits u een TCP-Luister poort opent. Met de functie Hybride verbindingen weet u niet of u weet wat het toepassings protocol is of wat u opent. Het biedt simpelweg netwerk toegang.  


## <a name="how-it-works"></a>Het werkt als volgt ##
De functie Hybride verbindingen bestaat uit twee uitgaande oproepen voor Azure Service Bus door sturen. Er is een verbinding van een bibliotheek op de host waarop uw app wordt uitgevoerd in App Service. Er is ook een verbinding tussen Hybrid Connection Manager (HCM) en Service Bus Relay. De HCM is een Relay-service die u implementeert in het netwerk dat als host fungeert voor de bron waartoe u toegang wilt krijgen. 

Via de twee gekoppelde verbindingen heeft uw app een TCP-tunnel naar een vaste host: poort combinatie aan de andere kant van de HCM. De verbinding maakt gebruik van TLS 1,2 voor beveiliging en SAS-sleutels (Shared Access Signature) voor verificatie en autorisatie.    

![Diagram van de stroom op hoog niveau van hybride verbinding][1]

Als uw app een DNS-aanvraag maakt die overeenkomt met een geconfigureerd hybride verbindings eindpunt, wordt het uitgaande TCP-verkeer omgeleid via de hybride verbinding.  

> [!NOTE]
> Dit betekent dat u altijd een DNS-naam moet gebruiken voor uw hybride verbinding. Sommige client software voert geen DNS-zoek opdracht uit als het eind punt in plaats daarvan een IP-adres gebruikt. 
>

### <a name="app-service-hybrid-connection-benefits"></a>Voor delen van hybride verbinding App Service ###

Er zijn een aantal voor delen ten opzichte van de Hybride verbindingen mogelijkheid, waaronder:

- Apps hebben veilig toegang tot on-premises systemen en services.
- Voor de functie is geen eind punt via internet toegankelijk.
- Het is snel en eenvoudig in te stellen. 
- Elke hybride verbinding komt overeen met één host: poort combinatie, handig voor beveiliging.
- Normaal gesp roken is geen firewall-gaten vereist. De verbindingen zijn alle uitgaande via standaard webpoorten.
- Omdat de functie netwerk niveau is, wordt deze neutraal in de taal die wordt gebruikt door uw app en de technologie die door het eind punt wordt gebruikt.
- Het kan worden gebruikt om toegang te bieden in meerdere netwerken vanuit één app. 

### <a name="things-you-cannot-do-with-hybrid-connections"></a>Wat u niet kunt doen met Hybride verbindingen ###

Wat u niet kunt doen met Hybride verbindingen zijn onder andere:

- Koppel een station.
- Gebruik UDP.
- Toegang tot TCP-services die gebruikmaken van dynamische poorten, zoals de passieve FTP-modus of de uitgebreide passieve modus.
- Ondersteuning voor LDAP, omdat hiervoor UDP vereist is.
- Ondersteuning Active Directory, omdat u geen domein kunt toevoegen aan een App Service-werk nemer.

### <a name="prerequisites"></a>Vereisten ###
 - Windows app service is vereist. Het is alleen beschikbaar in Windows.  

## <a name="add-and-create-hybrid-connections-in-your-app"></a>Hybride verbindingen toevoegen en maken in uw app ##

Als u een hybride verbinding wilt maken, gaat u naar de [Azure Portal][portal] en selecteert u uw app. Selecteer **netwerk** > **uw hybride verbindings eindpunten te configureren**. Hier ziet u de Hybride verbindingen die zijn geconfigureerd voor uw app.  

![Scherm opname van de lijst met hybride verbindingen][2]

Selecteer **[+] hybride verbinding toevoegen**om een nieuwe hybride verbinding toe te voegen.  U ziet een lijst met de Hybride verbindingen die u al hebt gemaakt. Als u een of meer ervan aan uw app wilt toevoegen, selecteert u de gewenste items en selecteert u **geselecteerde hybride verbinding toevoegen**.  

![Scherm opname van de portal voor hybride verbindingen][3]

Als u een nieuwe hybride verbinding wilt maken, selecteert u **nieuwe hybride verbinding maken**. Geef het volgende op: 

- Naam van de hybride verbinding.
- Hostnaam van eind punt.
- Eindpunt poort.
- Service Bus naam ruimte die u wilt gebruiken.

![Scherm afbeelding van het dialoog venster nieuwe hybride verbinding maken][4]

Elke hybride verbinding is gekoppeld aan een Service Bus naam ruimte en elke Service Bus naam ruimte bevindt zich in een Azure-regio. Het is belang rijk om een Service Bus naam ruimte in dezelfde regio als uw app te gebruiken om te voor komen dat de netwerk latentie wordt geduurd.

Als u uw hybride verbinding uit uw app wilt verwijderen, klikt u er met de rechter muisknop op en selecteert u **verbinding verbreken**.  

Wanneer een hybride verbinding wordt toegevoegd aan uw app, kunt u de details ervan weer geven door deze te selecteren. 

![Scherm opname van de details van hybride verbindingen][5]

### <a name="create-a-hybrid-connection-in-the-azure-relay-portal"></a>Een hybride verbinding maken in de Azure Relay Portal ###

Naast de portal-ervaring vanuit uw app, kunt u in de Azure Relay Portal Hybride verbindingen maken. Voor een hybride verbinding die wordt gebruikt door App Service moet het volgende:

* Client autorisatie vereist.
* Een meta gegevens item met een naam eind punt hebben dat een host: poort combinatie als waarde bevat.

## <a name="hybrid-connections-and-app-service-plans"></a>Hybride verbindingen-en App Service-plannen ##

App Service Hybride verbindingen zijn alleen beschikbaar in Sku's Basic, Standard, Premium en alleenstaande prijzen. Er zijn limieten gebonden aan het prijs plan.  

| Prijs plan | Aantal Hybride verbindingen bruikbaar in het plan |
|----|----|
| Basic | 5 |
| Standard | 25 |
| Premium | 200 |
| Isolated | 200 |

In de gebruikers interface van het App Service plan wordt weer gegeven hoeveel Hybride verbindingen er worden gebruikt en door welke apps.  

![Scherm afbeelding van de eigenschappen van het App Service plan][6]

Selecteer de hybride verbinding om de details te bekijken. U kunt alle informatie zien die u hebt gezien in de app-weer gave. U kunt ook zien hoeveel andere apps in hetzelfde abonnement gebruikmaken van die hybride verbinding.

Er geldt een limiet voor het aantal hybride verbindings eindpunten dat kan worden gebruikt in een App Service plan. Elke hybride verbinding die wordt gebruikt, kan echter worden gebruikt in elk wille keurig aantal apps in dat plan. Een enkele hybride verbinding die wordt gebruikt in vijf afzonderlijke apps in een App Service abonnement is bijvoorbeeld een hybride verbinding.

### <a name="pricing"></a>Prijzen ###

Naast de vereiste van een App Service plan SKU, zijn er extra kosten verbonden aan het gebruik van Hybride verbindingen. Er worden kosten in rekening gebracht voor elke listener die wordt gebruikt door een hybride verbinding. De listener is de Hybrid Connection Manager. Als er vijf Hybride verbindingen worden ondersteund door twee hybride verbindings beheer, zou dat 10 listeners zijn. Zie [Service Bus prijzen][sbpricing]voor meer informatie.

## <a name="hybrid-connection-manager"></a>Hybrid Connection Manager ##

De functie Hybride verbindingen vereist een relay-agent in het netwerk dat als host fungeert voor uw hybride verbindings eindpunt. Deze relay-agent wordt de Hybrid Connection Manager (HCM) genoemd. Als u HCM wilt downloaden, selecteert u in de [Azure Portal][portal] **netwerk** > **uw hybride verbindings-eind punten configureren**in uw app.  

Dit hulp programma kan worden uitgevoerd op Windows Server 2012 en hoger. De HCM wordt uitgevoerd als een service en maakt verbinding met het uitgaande verkeer naar Azure Relay op poort 443.  

Nadat u HCM hebt geïnstalleerd, kunt u HybridConnectionManagerUi. exe uitvoeren om de gebruikers interface voor het hulp programma te gebruiken. Dit bestand bevindt zich in de installatiemap van Hybrid Connection Manager. In Windows 10 kunt u ook alleen zoeken naar *Hybrid Connection Manager gebruikers interface* in het zoekvak.  

![Scherm opname van Hybrid Connection Manager][7]

Wanneer u de HCM-gebruikers interface start, is het eerste wat u ziet een tabel met een lijst met alle Hybride verbindingen die zijn geconfigureerd met dit exemplaar van de HCM. Als u wijzigingen wilt aanbrengen, moet u eerst verifiëren met Azure. 

Een of meer Hybride verbindingen toevoegen aan uw HCM:

1. Start de HCM-gebruikers interface.
2. Selecteer **een andere hybride verbinding configureren**.
![scherm opname van nieuwe Hybride verbindingen configureren][8]

1. Meld u aan met uw Azure-account om uw Hybride verbindingen beschikbaar te stellen voor uw abonnementen. De HCM blijft uw Azure-account verder gebruiken dan dat. 
1. Kies een abonnement.
1. Selecteer het Hybride verbindingen dat u door de HCM wilt laten door sturen.
Scherm opname van Hybride verbindingen ![][9]

1. Selecteer **Opslaan**.

U kunt nu de Hybride verbindingen zien die u hebt toegevoegd. U kunt ook de geconfigureerde hybride verbinding selecteren om de details te bekijken.

![Scherm afbeelding van de details van de hybride verbinding][10]

Ter ondersteuning van de Hybride verbindingen deze is geconfigureerd met, vereist HCM:

- TCP-toegang tot Azure via poort 443.
- TCP-toegang tot het hybride verbindings eindpunt.
- De mogelijkheid om DNS-Zoek-ups uit te voeren op de eindpunt-host en de Service Bus naam ruimte.

> [!NOTE]
> Azure Relay is afhankelijk van websockets voor connectiviteit. Deze functie is alleen beschikbaar in Windows Server 2012 of hoger. Als gevolg hiervan wordt HCM niet ondersteund op een eerdere versie dan Windows Server 2012.
>

### <a name="redundancy"></a>Redundantie ###

Elke HCM kan meerdere Hybride verbindingen ondersteunen. Daarnaast kan elke gegeven hybride verbinding worden ondersteund door meerdere HCMs. Het standaard gedrag is het routeren van verkeer via de geconfigureerde HCMs voor een bepaald eind punt. Als u een hoge Beschik baarheid op uw Hybride verbindingen van uw netwerk wilt, voert u meerdere HCMs uit op afzonderlijke machines. De taakverdelings algoritme die door de Relay-service wordt gebruikt voor het distribueren van verkeer naar de HCMs is een wille keurige toewijzing. 

### <a name="manually-add-a-hybrid-connection"></a>Een hybride verbinding hand matig toevoegen ###

Als u wilt dat iemand buiten uw abonnement als host voor een HCM-exemplaar voor een bepaalde hybride verbinding beschikt, moet u de gateway connection string voor de hybride verbinding delen. U kunt de gateway connection string zien in de eigenschappen van de hybride verbinding in de [Azure Portal][portal]. Als u deze teken reeks wilt gebruiken, selecteert u **hand matig invoeren** in de HCM en plakt u de gateway Connection String.

![Een hybride verbinding hand matig toevoegen][11]

### <a name="upgrade"></a>Upgraden ###

De Hybrid Connection Manager bevatten periodieke updates om problemen op te lossen of verbeteringen aan te brengen. Wanneer er upgrades worden uitgebracht, wordt een pop-upvenster weer gegeven in de HCM-gebruikers interface. Wanneer de upgrade wordt toegepast, worden de wijzigingen toegepast en wordt de HCM opnieuw gestart. 

## <a name="adding-a-hybrid-connection-to-your-app-programmatically"></a>Een hybride verbinding met uw app via een programma toevoegen ##

De hieronder vermelde Api's kunnen rechtstreeks worden gebruikt voor het beheren van de Hybride verbindingen die is verbonden met uw apps. 

    /subscriptions/[subscription name]/resourceGroups/[resource group name]/providers/Microsoft.Web/sites/[app name]/hybridConnectionNamespaces/[relay namespace name]/relays/[hybrid connection name]?api-version=2016-08-01

Het JSON-object dat is gekoppeld aan een hybride verbinding ziet er als volgt uit:

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

Een manier om deze informatie te gebruiken is met de armclient, die u kunt ophalen uit het project [armclient][armclient] github. Hier volgt een voor beeld van het koppelen van een al bestaande hybride verbinding met uw app. Maak een JSON-bestand volgens het bovenstaande schema, zoals:

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

Als u deze API wilt gebruiken, hebt u de resource-ID voor verzenden en relay nodig. Als u uw gegevens hebt opgeslagen met de bestands naam hctest. json, geeft u deze opdracht om uw hybride verbinding aan uw app toe te voegen: 

    armclient login
    armclient put /subscriptions/ebcidic-asci-anna-nath-rak1111111/resourceGroups/myapp-rg/providers/Microsoft.Web/sites/myhcdemoapp/hybridConnectionNamespaces/demo-relay/relays/relay-demo-hc?api-version=2016-08-01 @hctest.json

## <a name="secure-your-hybrid-connections"></a>Uw Hybride verbindingen beveiligen ##

Een bestaande hybride verbinding kan worden toegevoegd aan andere App Service Web Apps door elke gebruiker die voldoende machtigingen heeft voor de onderliggende Azure Service Bus relay. Dit betekent dat als u moet voor komen dat andere gebruikers dezelfde hybride verbinding opnieuw gebruiken (bijvoorbeeld wanneer de doel resource een service is waarvoor geen extra beveiligings maatregelen gelden om onbevoegde toegang te voor komen), moet u de toegang tot Azure vergren delen. Service Bus Relay.

Iedereen met `Reader` toegang tot de relay kan de hybride verbinding _zien_ wanneer deze probeert toe te voegen aan hun web-app in de Azure-Portal, maar deze kan niet worden _toegevoegd_ omdat ze geen machtigingen hebben om de Connection String op te halen die wordt gebruikt om de relay-verbinding tot stand te brengen. Om de hybride verbinding toe te voegen, moeten ze beschikken over de `listKeys` machtiging (`Microsoft.Relay/namespaces/hybridConnections/authorizationRules/listKeys/action`). Met de `Contributor` rol of een andere rol die deze machtiging voor de relay bevat, kunnen gebruikers de hybride verbinding gebruiken en toevoegen aan hun eigen Web Apps.

## <a name="troubleshooting"></a>Problemen oplossen ##

De status verbonden betekent dat er ten minste één HCM is geconfigureerd met die hybride verbinding en dat Azure kan worden bereikt. Als de status voor uw hybride verbinding niet **verbonden**is, is uw hybride verbinding niet geconfigureerd op een HCM die toegang heeft tot Azure.

De primaire reden waarom clients geen verbinding kunnen maken met hun eind punt, omdat het eind punt is opgegeven met behulp van een IP-adres in plaats van een DNS-naam. Als uw app het gewenste eind punt niet kan bereiken en u een IP-adres hebt gebruikt, schakelt u over naar het gebruik van een DNS-naam die geldig is op de host waarop de HCM wordt uitgevoerd. Controleer ook of de DNS-naam correct wordt omgezet op de host waarop de HCM wordt uitgevoerd. Controleer of er een verbinding is tussen de host waarop de HCM wordt uitgevoerd voor het hybride verbindings eindpunt.  

In App Service kan het opdracht regel programma **tcpping** worden aangeroepen vanuit de console geavanceerde hulp middelen (kudu). Met dit hulp programma kunt u aangeven of u toegang hebt tot een TCP-eind punt, maar u krijgt hier geen informatie over als u toegang hebt tot een hybride verbindings eindpunt. Wanneer u het hulp programma gebruikt in de-console op basis van een hybride verbindings eindpunt, bevestigt u alleen dat het gebruikmaakt van een host: poort combinatie.  

Als u een opdracht regel-client voor uw eind punt hebt, kunt u de verbinding testen via de app-console. U kunt bijvoorbeeld de toegang tot webserver eindpunten testen met behulp van krul.

## <a name="biztalk-hybrid-connections"></a>Hybrid Connections van BizTalk ##

De vroege vorm van deze functie werd BizTalk-Hybride verbindingen genoemd. Deze mogelijkheid is geëindigd op 31 mei 2018 en gestopte bewerkingen. BizTalk Hybrid connections zijn verwijderd uit alle apps en zijn niet toegankelijk via de portal of API. Als u deze oudere verbindingen nog steeds hebt geconfigureerd in de Hybrid Connection Manager, ziet u de status van Discontinued en wordt onderaan de instructie End-of-Life weer gegeven.

![BizTalk-Hybride verbindingen in de HCM][12]


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
