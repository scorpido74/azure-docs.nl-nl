---
title: End-to-end TLS inschakelen op Azure-toepassing gateway
description: Dit artikel bevat een overzicht van de Application Gateway end-to-end TLS-ondersteuning.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311852"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway

Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), is de standaard beveiligings technologie voor het tot stand brengen van een versleutelde koppeling tussen een webserver en een browser. Deze koppeling zorgt ervoor dat alle gegevens die tussen de webserver en de browsers worden door gegeven, privé en versleuteld blijven. Application Gateway ondersteunt zowel TLS-beëindiging bij de gateway als end-to-end TLS-versleuteling.

## <a name="tls-termination"></a>TLS-beëindiging

Application Gateway ondersteunt TLS-beëindiging op de gateway, waarna verkeer doorgaans niet-versleuteld naar de back-endservers stromen. Er zijn een aantal voor delen van het uitvoeren van TLS-beëindiging op de Application Gateway:

- **Verbeterde prestaties** : de grootste prestaties bij het uitvoeren van TLS-ontsleuteling is de initiële handshake. Om de prestaties te verbeteren, voert de server die de ontsleuteling uitvoert TLS-sessie-Id's uit en beheert TLS-sessie tickets. Als dit gebeurt in de toepassings gateway, kunnen alle aanvragen van dezelfde client de in de cache opgeslagen waarden gebruiken. Als de server op de back-endservers wordt uitgevoerd, wordt de client telkens wanneer de aanvragen van de client naar een andere servers gaan, opnieuw geverifieerd. Het gebruik van TLS-tickets kan helpen om dit probleem te verhelpen, maar ze worden niet door alle clients ondersteund en kunnen lastig zijn om te configureren en te beheren.
- **Verbeterd gebruik van de back-endservers** – SSL/TLS-verwerking is zeer CPU-intensief, en wordt veel meer intensief naarmate de sleutel grootten toenemen. Door dit werk van de back-endservers te verwijderen, kunnen ze zich richten op wat ze het meest efficiënt zijn om inhoud te leveren.
- **Intelligente route ring** : door het verkeer te ontsleutelen, heeft de Application Gateway toegang tot de aanvraag inhoud, zoals HEADERS, URI, enzovoort, en kan deze gegevens gebruiken om aanvragen te routeren.
- **Certificaat beheer** : certificaten hoeven alleen te worden aangeschaft en geïnstalleerd op de toepassings gateway en niet op alle back-endservers. Dit bespaart tijd en geld.

Als u TLS-beëindiging wilt configureren, moet er een TLS/SSL-certificaat aan de listener worden toegevoegd om de toepassings gateway in te scha kelen voor het afleiden van een symmetrische sleutel conform TLS/SSL-protocol specificatie. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Het TLS/SSL-certificaat moet de PFX-indeling (Personal Information Exchange) hebben. Met deze bestands indeling kunt u de persoonlijke sleutel exporteren die is vereist voor de toepassings gateway om de versleuteling en ontsleuteling van verkeer uit te voeren.

> [!NOTE] 
>
> Application Gateway biedt geen mogelijkheid voor het maken van een nieuw certificaat of het verzenden van een certificaat aanvraag naar een certificerings instantie.

Als u de TLS-verbinding wilt gebruiken, moet u ervoor zorgen dat het TLS/SSL-certificaat voldoet aan de volgende voor waarden:

- Of de huidige datum en tijd binnen het datum bereik geldig van en geldig tot zijn voor het certificaat.
- De algemene naam (CN) van het certificaat komt overeen met de host-header in de aanvraag. Als de client bijvoorbeeld een aanvraag indient bij `https://www.contoso.com/`, moet de algemene naam `www.contoso.com` zijn.

### <a name="certificates-supported-for-tls-termination"></a>Certificaten die worden ondersteund voor het beëindigen van TLS

Application Gateway ondersteunt de volgende typen certificaten:

- CA-certificaat (certificerings instantie): een CA-certificaat is een digitaal certificaat dat is uitgegeven door een certificerings instantie (CA)
- EV-certificaat (Extended validatie): een EV-certificaat is een certificaat dat voldoet aan de richt lijnen voor de normen van het gestandaardiseerde certificaat. Hiermee wordt de browser Locator-balk groen en wordt de bedrijfs naam ook gepubliceerd.
- Joker certificaat: dit certificaat ondersteunt een wille keurig aantal subdomeinen op basis van *. site.com, waarbij uw subdomein de * vervangen. Het biedt echter geen ondersteuning voor site.com, dus als de gebruikers toegang hebben tot uw website zonder de toonaangevende ' www ' te hoeven typen, heeft het Joker certificaat hier geen betrekking op.
- Zelfondertekende certificaten: client browsers vertrouwen deze certificaten niet en de gebruiker wordt gewaarschuwd dat het certificaat van de virtuele service geen deel uitmaakt van een vertrouwens keten. Zelfondertekende certificaten zijn geschikt voor testen of omgevingen waarin beheerders de clients beheren en de beveiligings waarschuwingen van de browser veilig kunnen passeren. Werk belastingen voor productie moeten nooit zelfondertekende certificaten gebruiken.

Zie [Configure TLS Termination with Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)(Engelstalig) voor meer informatie.

### <a name="size-of-the-certificate"></a>Grootte van het certificaat
Controleer de sectie [limieten van Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) om de maximale grootte van TLS/SSL-certificaten te weten die wordt ondersteund.

## <a name="end-to-end-tls-encryption"></a>End-to-end TLS-versleuteling

Sommige klanten willen mogelijk niet-versleutelde communicatie met de back-endservers. Dit kan komen door de beveiligings- of nalevingsvereisten, of misschien kan de toepassing alleen worden gebruikt via een beveiligde verbinding. Voor dergelijke toepassingen ondersteunt Application Gateway end-to-end TLS-versleuteling.

End-to-end TLS biedt u de mogelijkheid om veilig gevoelige gegevens naar de back-end te versleutelen, terwijl u nog steeds profiteert van de voor delen van functies voor taak verdeling van laag 7 die door Application Gateway worden geboden. Voorbeelden van deze functies zijn: sessieaffiniteit op basis van cookies, routering op basis van een URL, ondersteuning voor routering op basis van sites en de mogelijkheid om X-Forwarded-\*-headers in te voegen.

Wanneer de toepassings gateway is geconfigureerd met een end-to-end TLS-communicatie modus, worden de TLS-sessies op de gateway beëindigd en wordt het gebruikers verkeer ontsleuteld. Vervolgens worden de geconfigureerde regels toegepast voor het selecteren van het juiste exemplaar van de back-endgroep waarnaar het verkeer moet worden doorgeleid. Application Gateway initieert vervolgens een nieuwe TLS-verbinding met de back-endserver en versleutelt de gegevens opnieuw met het open bare-sleutel certificaat van de back-endserver voordat de aanvraag naar de back-end wordt verzonden. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker. End-to-end TLS wordt ingeschakeld door de protocol instelling in [back-end http](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) in te stellen op https, die vervolgens wordt toegepast op een back-end-groep.

Het TLS-beleid is van toepassing op zowel frontend-als back-end-verkeer. Op de front-end Application Gateway fungeren als de server en wordt het beleid afgedwongen. Op de backend fungeert Application Gateway als de client en verzendt de protocol/cipher-informatie als voor keur tijdens de TLS-handshake.

Application Gateway communiceert alleen met de back-end-exemplaren die hun certificaat hebben white list met de toepassings gateway of waarvan de certificaten zijn ondertekend door bekende CA-instanties waarbij de certificaat-CN overeenkomt met de hostnaam in de HTTP-back-end-instellingen. Dit zijn onder andere de vertrouwde Azure-Services, zoals Azure-app service-Web-apps en Azure API Management.

Als de certificaten van de leden in de back-end-groep niet zijn ondertekend met bekende CA-instanties, moet elk exemplaar in de back-end-groep met end-to-end TLS ingeschakeld worden geconfigureerd met een certificaat om beveiligde communicatie toe te staan. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Hiermee wordt de end-to-end-communicatie verder beveiligd.

> [!NOTE] 
>
> De installatie van het verificatie certificaat is niet vereist voor vertrouwde Azure-Services, zoals Azure-app Services Web apps en Azure API Management.

> [!NOTE] 
>
> Het certificaat dat is toegevoegd aan de **back-end-HTTP-instelling** voor authenticatie van de back-end-servers, kan hetzelfde zijn als het certificaat dat is toegevoegd aan de **listener** voor TLS-beëindiging op Application Gateway of op een andere manier voor verbeterde beveiliging.

![end-to-end TLS-scenario][1]

In dit voor beeld worden aanvragen die gebruikmaken van TLS 1.2 gerouteerd naar back-endservers in Pool1 met end-to-end TLS.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>End-to-end-TLS en white list van certificaten

Application Gateway communiceert alleen met bekende back-endexemplaren waarvan het certificaat is opgenomen in een lijst van toegestane certificaten voor de Application Gateway. Als u het opnemen van certificaten in een lijst van toegestane certificaten wilt inschakelen, moet u de openbare sleutel van de back-endservercertificaten uploaden naar de Application Gateway (niet het basiscertificaat). Alleen verbindingen met bekende en goedgekeurde back-ends zijn vervolgens toegestaan. De resterende back-ends hebben een gatewayfout als resultaat. Zelfondertekende certificaten zijn uitsluitend bedoeld voor testdoeleinden en het wordt afgeraden om deze in een productieomgeving te gebruiken. Dergelijke certificaten moeten worden white list met de Application Gateway, zoals beschreven in de voor gaande stappen voordat ze kunnen worden gebruikt.

> [!NOTE]
> De installatie van het verificatie certificaat is niet vereist voor vertrouwde Azure-Services, zoals Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End-to-end TLS met de v2-SKU

Verificatie certificaten zijn afgeschaft en vervangen door vertrouwde basis certificaten in de Application Gateway v2-SKU. Ze werken op dezelfde manier als verificatie certificaten met enkele belang rijke verschillen:

- Voor certificaten die zijn ondertekend door bekende CA-instanties waarvan de naam overeenkomt met de hostnaam in de HTTP-back-end-instellingen, is geen extra stap vereist voor end-to-end-TLS. 

   Als de back-end-certificaten bijvoorbeeld zijn uitgegeven door een bekende CA en een CN van contoso.com hebben en het veld host van de back-end-http-instelling ook is ingesteld op contoso.com, zijn er geen aanvullende stappen vereist. U kunt het http-instellings Protocol van de back-end instellen op HTTPS en zowel voor de status test als voor het gegevenspad is TLS ingeschakeld. Als u Azure App Service of andere Azure-webservices als uw back-end gebruikt, worden deze impliciet vertrouwd en zijn er geen verdere stappen vereist voor end-to-end-TLS.
   
> [!NOTE] 
>
> Om een TLS/SSL-certificaat te kunnen vertrouwen, moet dat certificaat van de back-endserver zijn uitgegeven door een CA die is opgenomen in het vertrouwde archief van de Application Gateway. als het certificaat niet is uitgegeven door een vertrouwde certificerings instantie, controleert de Application Gateway vervolgens of het certificaat van de verlenende CA is uitgegeven door een vertrouwde certificerings instantie, enzovoort, totdat een vertrouwde CERTIFICERINGs instantie is gevonden (op welk moment een vertrouwde, beveiligde verbinding tot stand wordt gebracht) of geen vertrouwde certificerings instantie kan worden gevonden (waarbij de Application Gateway de back-end beschadigd maakt). Daarom wordt aanbevolen dat het back-endserver certificaat zowel de basis-Ca's als de tussenliggende certificerings instantie bevat.

- Als het certificaat zelfondertekend is of door onbekende tussen personen is ondertekend, moet er een vertrouwd basis certificaat worden gedefinieerd om end-to-end TLS in v2 SKU in te scha kelen. Application Gateway communiceert alleen met back-ends waarvan het basis certificaat van het server certificaat overeenkomt met een van de lijst met vertrouwde basis certificaten in de back-end-http-instelling die aan de groep is gekoppeld.

> [!NOTE] 
>
> Het zelfondertekende certificaat moet deel uitmaken van een certificaat keten. Eén zelfondertekend certificaat zonder keten wordt niet ondersteund in v2 SKU.

- Naast het overeenkomende basis certificaat wordt Application Gateway ook gevalideerd als de host-instelling die is opgegeven in de back-end-http-instelling overeenkomt met die van de algemene naam (CN) die wordt gepresenteerd door het TLS/SSL-certificaat van de back-endserver. Wanneer u probeert een TLS-verbinding met de back-end tot stand te brengen, stelt Application Gateway de uitbrei ding Servernaamindicatie (SNI) in op de host die is opgegeven in de back-end-http-instelling.
- Als de optie **hostnaam uit het back-mailadres kiezen** wordt gekozen in plaats van het veld host in de http-instelling van de back-end, wordt de SNI-header altijd ingesteld op de FQDN-naam van de back-end en de CN op het TLS/SSL-server certificaat moet overeenkomen met de FQDN. Back-end-groeps leden met IP-adressen worden niet ondersteund in dit scenario.
- Het basis certificaat is een base64-gecodeerd basis certificaat van de back-end-server certificaten.

## <a name="next-steps"></a>Volgende stappen

Nadat u hebt leren over end-to-end TLS, gaat u naar [end-to-end TLS configureren met behulp van Application Gateway met Power shell](application-gateway-end-to-end-ssl-powershell.md) om een toepassings gateway te maken met end-to-end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
