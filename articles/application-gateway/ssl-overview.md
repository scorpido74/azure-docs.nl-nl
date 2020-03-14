---
title: End-to-end SSL inschakelen op Azure-toepassing gateway
description: Dit artikel bevat een overzicht van de Application Gateway end-to-end SSL-ondersteuning.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 64b90afd598b96604fc9c3ddc4bc10586e714363
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79279102"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Overzicht van SSL-beëindiging en end-to-end SSL met Application Gateway

Secure Sockets Layer (SSL) is de standaard beveiligings technologie voor het tot stand brengen van een versleutelde koppeling tussen een webserver en een browser. Deze koppeling zorgt ervoor dat alle gegevens die tussen de webserver en de browsers worden door gegeven, privé en versleuteld blijven. Application Gateway ondersteunt zowel SSL-beëindiging op de gateway als end-to-end SSL-versleuteling.

## <a name="ssl-termination"></a>SSL-beëindiging

Application Gateway ondersteunt SSL-beëindiging op de gateway, waarna het verkeer normaal gesproken onversleuteld naar de back-endservers wordt doorgeleid. Er zijn een aantal voor delen van het uitvoeren van SSL-beëindiging op de Application Gateway:

- **Verbeterde prestaties** : de grootste prestaties bij het uitvoeren van SSL-ontsleuteling is de initiële handshake. Om de prestaties te verbeteren, voert de server die de ontsleuteling uitvoert SSL-sessie-Id's uit en beheert TLS-sessie tickets. Als dit gebeurt in de toepassings gateway, kunnen alle aanvragen van dezelfde client de in de cache opgeslagen waarden gebruiken. Als de server op de back-endservers wordt uitgevoerd, gaat de client telkens wanneer de aanvragen van de client naar een andere servers worden geverifieerd. Het gebruik van TLS-tickets kan helpen om dit probleem te verhelpen, maar ze worden niet door alle clients ondersteund en kunnen lastig zijn om te configureren en te beheren.
- **Verbeterd gebruik van de back-endservers** – SSL/TLS-verwerking is zeer CPU-intensief, en wordt veel meer intensief naarmate de sleutel grootten toenemen. Door dit werk van de back-endservers te verwijderen, kunnen ze zich richten op wat ze het meest efficiënt zijn om inhoud te leveren.
- **Intelligente route ring** : door het verkeer te ontsleutelen, heeft de Application Gateway toegang tot de aanvraag inhoud, zoals HEADERS, URI, enzovoort, en kan deze gegevens gebruiken om aanvragen te routeren.
- **Certificaat beheer** : certificaten hoeven alleen te worden aangeschaft en geïnstalleerd op de toepassings gateway en niet op alle back-endservers. Dit bespaart tijd en geld.

Als u SSL-beëindiging wilt configureren, moet u een SSL-certificaat toevoegen aan de listener om de toepassings gateway in te scha kelen voor het afleiden van een symmetrische sleutel volgens SSL-protocol specificatie. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het SSL-certificaat moet de PFX-indeling (Personal Information Exchange) hebben. Met deze bestands indeling kunt u de persoonlijke sleutel exporteren die is vereist voor de toepassings gateway om de versleuteling en ontsleuteling van verkeer uit te voeren.

> [!NOTE] 
>
> Application Gateway biedt geen mogelijkheid voor het maken van een nieuw certificaat of het verzenden van een certificaat aanvraag naar een certificerings instantie.

Voor een goede werking van de SSL-verbinding moet u controleren of het SSL-certificaat voldoet aan de volgende voor waarden:

- Of de huidige datum en tijd binnen het datum bereik geldig van en geldig tot zijn voor het certificaat.
- De algemene naam (CN) van het certificaat komt overeen met de host-header in de aanvraag. Als de client bijvoorbeeld een aanvraag indient bij `https://www.contoso.com/`, moet de algemene naam `www.contoso.com` zijn.

### <a name="certificates-supported-for-ssl-termination"></a>Certificaten die worden ondersteund voor SSL-beëindiging

Application Gateway ondersteunt de volgende typen certificaten:

- CA-certificaat (certificerings instantie): een CA-certificaat is een digitaal certificaat dat is uitgegeven door een certificerings instantie (CA)
- EV-certificaat (Extended validatie): een EV-certificaat is een standaard certificaat richtlijn voor certificaten. Hiermee wordt de browser-Locator-balk groen en wordt de bedrijfs naam ook gepubliceerd.
- Joker certificaat: dit certificaat ondersteunt een wille keurig aantal subdomeinen op basis van *. site.com, waarbij uw subdomein de * vervangen. Het biedt echter geen ondersteuning voor site.com, dus als de gebruikers toegang hebben tot uw website zonder de toonaangevende ' www ' te hoeven typen, heeft het Joker certificaat hier geen betrekking op.
- Zelfondertekende certificaten: client browsers vertrouwen deze certificaten niet en de gebruiker wordt gewaarschuwd dat het certificaat van de virtuele service geen deel uitmaakt van een vertrouwens keten. Zelfondertekende certificaten zijn geschikt voor testen of omgevingen waarin beheerders de clients beheren en de beveiligings waarschuwingen van de browser veilig kunnen passeren. Werk belastingen voor productie moeten nooit zelfondertekende certificaten gebruiken.

Zie voor meer informatie [SSL-beëindiging configureren met Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Grootte van het certificaat
Controleer de sectie [limieten van Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) om de maximale grootte van SSL-certificaten te bepalen die wordt ondersteund.

## <a name="end-to-end-ssl-encryption"></a>End-to-end SSL-versleuteling

Sommige klanten willen mogelijk niet-versleutelde communicatie met de back-endservers. Dit kan komen door de beveiligings- of nalevingsvereisten, of misschien kan de toepassing alleen worden gebruikt via een beveiligde verbinding. Voor zulke toepassingen ondersteunt Application Gateway end-to-end SSL-versleuteling.

Met end-to-end SSL kunt u veilig gevoelige gegevens versleuteld verzenden naar de back-endserver, terwijl u gebruik blijft maken van de voordelen van de Layer 7-taakverdelingsfuncties die Application Gateway te bieden heeft. Voorbeelden van deze functies zijn: sessieaffiniteit op basis van cookies, routering op basis van een URL, ondersteuning voor routering op basis van sites en de mogelijkheid om X-Forwarded-\*-headers in te voegen.

Wanneer Application Gateway is geconfigureerd met de communicatiemodus voor end-to-end SSL, worden door Application Gateway de SSL-sessies op de gateway beëindigd en wordt het gebruikersverkeer ontsleuteld. Vervolgens worden de geconfigureerde regels toegepast voor het selecteren van het juiste exemplaar van de back-endgroep waarnaar het verkeer moet worden doorgeleid. Application Gateway initieert vervolgens een nieuwe SSL-verbinding met de back-endserver en versleutelt de gegevens opnieuw met het openbare-sleutelcertificaat van de back-endserver, voordat de aanvraag naar de back-endserver wordt verstuurd. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker. End-to-end SSL wordt ingeschakeld door de protocol instelling in de [back-end-HTTP-instelling](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) in te stellen op https, die vervolgens wordt toegepast op een back-end-groep.

Het SSL-beleid is van toepassing op zowel frontend-als back-end-verkeer. Op de front-end Application Gateway fungeren als de server en wordt het beleid afgedwongen. Op de backend fungeert Application Gateway als de client en verzendt de protocol/cipher-informatie als voor keur tijdens de SSL-handshake.

Application Gateway communiceert alleen met de back-end-exemplaren die hun certificaat hebben white list met de toepassings gateway of waarvan de certificaten zijn ondertekend door bekende CA-instanties waarbij de certificaat-CN overeenkomt met de hostnaam in de HTTP back-end-instellingen. Dit zijn onder andere de vertrouwde Azure-Services, zoals Azure-app service-Web-apps en Azure API Management.

Als de certificaten van de leden in de back-end-groep niet zijn ondertekend met bekende CA-instanties, moet elke instantie in de back-end-groep met end-to-end SSL ingeschakeld zijn geconfigureerd met een certificaat om beveiligde communicatie toe te staan. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Hiermee wordt de end-to-end-communicatie verder beveiligd.

> [!NOTE] 
>
> De installatie van het verificatie certificaat is niet vereist voor vertrouwde Azure-Services, zoals Azure-app Services Web apps en Azure API Management.

> [!NOTE] 
>
> Het certificaat dat is toegevoegd aan de **back-end-HTTP-instelling** voor authenticatie van de back-end-servers, kan hetzelfde zijn als het certificaat dat aan de **listener** is toegevoegd voor SSL-beëindiging op Application Gateway of op een andere manier voor verbeterde beveiliging.

![end-to-end SSL-scenario][1]

In dit voorbeeld worden-aanvragen via TLS1.2 doorgestuurd naar de back-endservers in Pool1 met behulp van end-to-end SSL.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>End-to-end SSL en het opnemen van certificaten in een lijst van toegestane certificaten

Application Gateway communiceert alleen met bekende back-endexemplaren waarvan het certificaat is opgenomen in een lijst van toegestane certificaten voor de Application Gateway. Als u het opnemen van certificaten in een lijst van toegestane certificaten wilt inschakelen, moet u de openbare sleutel van de back-endservercertificaten uploaden naar de Application Gateway (niet het basiscertificaat). Alleen verbindingen met bekende en goedgekeurde back-ends zijn vervolgens toegestaan. De resterende back-ends hebben een gatewayfout als resultaat. Zelfondertekende certificaten zijn uitsluitend bedoeld voor testdoeleinden en het wordt afgeraden om deze in een productieomgeving te gebruiken. Dergelijke certificaten moeten worden white list met de Application Gateway, zoals beschreven in de voor gaande stappen voordat ze kunnen worden gebruikt.

> [!NOTE]
> De installatie van het verificatie certificaat is niet vereist voor vertrouwde Azure-Services, zoals Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>End-to-end-SSL met de v2-SKU

Verificatie certificaten zijn afgeschaft en vervangen door vertrouwde basis certificaten in de Application Gateway v2-SKU. Ze werken op dezelfde manier als verificatie certificaten met enkele belang rijke verschillen:

- Voor certificaten die zijn ondertekend door bekende CA-instanties waarvan de naam overeenkomt met de hostnaam in de HTTP-back-end-instellingen, is geen extra stap vereist voor end-to-end-SSL om te werken. 

   Als de back-end-certificaten bijvoorbeeld zijn uitgegeven door een bekende CA en een CN van contoso.com hebben en het veld host van de back-end-http-instelling ook is ingesteld op contoso.com, zijn er geen aanvullende stappen vereist. U kunt het http-instellings Protocol van de back-end instellen op HTTPS en zowel voor de status test als voor het gegevenspad is SSL ingeschakeld. Als u Azure App Service of andere Azure-webservices als uw back-end gebruikt, worden deze impliciet vertrouwd en zijn er geen verdere stappen vereist voor end-to-end SSL.
   
> [!NOTE] 
>
> Een SSL-certificaat kan alleen worden vertrouwd als het certificaat van de back-endserver is uitgegeven door een CA die is opgenomen in het vertrouwde archief van de Application Gateway. als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie, wordt de Application Gateway vervolgens gecontroleerd om te controleren of het certificaat van de verlenende CA is uitgegeven door een vertrouwde certificerings instantie, enzovoort, totdat een vertrouwde CERTIFICERINGs instantie wordt gevonden (op welk moment een vertrouwde, beveiligde verbinding wordt ingesteld) of geen vertrouwde certificerings instantie kan worden gevonden (op welk punt bevindt het Application Gateway de back-end te markeren slecht). Daarom wordt aanbevolen het back-endserver certificaat zowel de root-als de intermidiate-certificerings instantie te bevatten.

- Als het certificaat zelfondertekend is of door onbekende tussen personen is ondertekend, moet u een vertrouwd basis certificaat instellen om end-to-end SSL in v2 SKU in te scha kelen. Application Gateway communiceert alleen met back-ends waarvan het basis certificaat van het server certificaat overeenkomt met een van de lijst met vertrouwde basis certificaten in de back-end-http-instelling die aan de groep is gekoppeld.

> [!NOTE] 
>
> Het zelfondertekende certificaat moet deel uitmaken van een certificaat keten. Eén zelfondertekend certificaat zonder keten wordt niet ondersteund in v2 SKU.

- Naast het overeenkomende basis certificaat wordt Application Gateway ook gevalideerd als de host-instelling die is opgegeven in de back-end-http-instelling overeenkomt met die van de algemene naam (CN) die wordt gepresenteerd door het SSL-certificaat van de back-endserver. Wanneer u probeert een SSL-verbinding met de back-end tot stand te brengen, stelt Application Gateway de uitbrei ding Servernaamindicatie (SNI) in op de host die is opgegeven in de back-end-http-instelling.
- Als de optie **hostnaam uit het back-mailadres kiezen** wordt gekozen in plaats van het veld host in de http-instelling van de back-end, wordt de SNI-header altijd ingesteld op de FQDN-naam van de back-endserver en de CN op het back-endserver SSL-certificaat moet overeenkomen met de FQDN. Back-end-groeps leden met IP-adressen worden niet ondersteund in dit scenario.
- Het basis certificaat is een base64-gecodeerd basis certificaat van de back-end-server certificaten.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt leren over end-to-end SSL, gaat u naar end- [to-end SSL configureren met behulp van Application Gateway met Power shell](application-gateway-end-to-end-ssl-powershell.md) om een toepassings gateway te maken met end-to-end SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
