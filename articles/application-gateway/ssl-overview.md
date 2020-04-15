---
title: End-to-end TLS inschakelen op Azure Application Gateway
description: Dit artikel is een overzicht van de End-to-end TLS-ondersteuning van de Toepassing gateway.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311852"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway

Transport Layer Security (TLS), voorheen bekend als Secure Sockets Layer (SSL), is de standaard beveiligingstechnologie voor het leggen van een versleutelde koppeling tussen een webserver en een browser. Deze link zorgt ervoor dat alle gegevens die tussen de webserver en browsers worden doorgegeven privé en versleuteld blijven. Application gateway ondersteunt zowel TLS-beëindiging bij de gateway als end-to-end TLS-versleuteling.

## <a name="tls-termination"></a>TLS-beëindiging

Application Gateway ondersteunt TLS-beëindiging bij de gateway, waarna verkeer meestal onversleuteld naar de backendservers stroomt. Er zijn een aantal voordelen van het doen van TLS-beëindiging bij de toepassingsgateway:

- **Verbeterde prestaties** - De grootste prestatiehit bij het doen van TLS-decryptie is de eerste handdruk. Om de prestaties te verbeteren, de server doet de decryptie caches TLS sessie-id's en beheert TLS sessie tickets. Als dit gebeurt bij de toepassingsgateway, kunnen alle aanvragen van dezelfde client de in de cache opgeslagen waarden gebruiken. Als dit gebeurt op de backendservers, moet de client telkens naar een andere server gaan. Het gebruik van TLS-tickets kan helpen dit probleem te beperken, maar ze worden niet door alle clients ondersteund en kunnen moeilijk te configureren en te beheren zijn.
- **Beter gebruik van de backend servers** - SSL / TLS verwerking is zeer CPU-intensief, en wordt steeds intensiever naarmate de belangrijkste maten toenemen. Door dit werk van de backendservers te verwijderen, kunnen ze zich concentreren op waar ze het meest efficiënt in zijn en inhoud leveren.
- **Intelligente routering** - Door het verkeer te decoderen, heeft de toepassingsgateway toegang tot de aanvraaginhoud, zoals kopteksten, URI enzovoort, en kan deze gegevens worden gebruikt om aanvragen te routeren.
- **Certificaatbeheer** : certificaten hoeven alleen te worden gekocht en geïnstalleerd op de toepassingsgateway en niet op alle backendservers. Dit bespaart zowel tijd als geld.

Om TLS-beëindiging te configureren, moet een TLS/SSL-certificaat aan de listener worden toegevoegd om de toepassingsgateway in staat te stellen een symmetrische sleutel af te leiden volgens tls/SSL-protocolspecificatie. De symmetrische sleutel wordt vervolgens gebruikt om het verkeer dat naar de gateway wordt verzonden te versleutelen en te decoderen. Het TLS/SSL-certificaat moet in pfx-formaat (Personal Information Exchange) zijn. Met deze bestandsindeling u de privésleutel exporteren die vereist is door de toepassingsgateway om de versleuteling en decryptie van het verkeer uit te voeren.

> [!NOTE] 
>
> Application gateway biedt geen mogelijkheid om een nieuw certificaat te maken of een certificaataanvraag te sturen naar een certificeringsinstantie.

Als u de TLS-verbinding wilt laten werken, moet u ervoor zorgen dat het TLS/SSL-certificaat aan de volgende voorwaarden voldoet:

- Dat de huidige datum en tijd binnen het datumbereik 'Geldig van' en 'Geldig tot' op het certificaat liggen.
- De algemene naam (CN) van het certificaat komt overeen met de host-header in de aanvraag. Als de client bijvoorbeeld een aanvraag indient bij `https://www.contoso.com/`, moet de algemene naam `www.contoso.com` zijn.

### <a name="certificates-supported-for-tls-termination"></a>Certificaten ondersteund voor TLS-beëindiging

Toepassingsgateway ondersteunt de volgende typen certificaten:

- CA -certificaat (Certificaatautoriteit): een CA-certificaat is een digitaal certificaat dat is afgegeven door een certificeringsinstantie (CA)
- EV -certificaat (Extended Validation): een EV-certificaat is een certificaat dat voldoet aan de richtlijnen voor industriestandaardcertificaten. Dit zal de browser locator bar groen en publiceren van de bedrijfsnaam ook.
- Wildcardcertificaat: dit certificaat ondersteunt een willekeurig aantal subdomeinen op basis van *.site.com, waarbij uw subdomein de *. Het ondersteunt echter geen site.com, dus in het geval dat de gebruikers toegang hebben tot uw website zonder het typen van de toonaangevende "www", zal het wildcardcertificaat dat niet dekken.
- Zelfondertekende certificaten: clientbrowsers vertrouwen deze certificaten niet en waarschuwen de gebruiker dat het certificaat van de virtuele service geen deel uitmaakt van een vertrouwensketen. Zelfondertekende certificaten zijn goed voor het testen of omgevingen waar beheerders de clients beheren en de beveiligingswaarschuwingen van de browser veilig kunnen omzeilen. Productieworkloads mogen nooit zelfondertekende certificaten gebruiken.

Zie [TLS-beëindiging configureren met toepassingsgateway voor](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)meer informatie.

### <a name="size-of-the-certificate"></a>Grootte van het certificaat
Controleer de sectie [Toepassingsgateway-limieten](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) om de maximale ondersteunde TLS/SSL-certificaatgrootte te kennen.

## <a name="end-to-end-tls-encryption"></a>END-to-end TLS-versleuteling

Sommige klanten willen mogelijk geen onversleutelde communicatie naar de backendservers. Dit kan komen door de beveiligings- of nalevingsvereisten, of misschien kan de toepassing alleen worden gebruikt via een beveiligde verbinding. Voor dergelijke toepassingen ondersteunt application gateway end-to-end TLS-versleuteling.

End-to-end TLS stelt u in staat om veilig gevoelige gegevens te verzenden naar de backend versleuteld, terwijl u nog steeds profiteren van de voordelen van Layer 7 load-balancing functies die applicatie gateway biedt. Voorbeelden van deze functies zijn: sessieaffiniteit op basis van cookies, routering op basis van een URL, ondersteuning voor routering op basis van sites en de mogelijkheid om X-Forwarded-\*-headers in te voegen.

Wanneer geconfigureerd met end-to-end TLS-communicatiemodus, beëindigt de toepassingsgateway de TLS-sessies op de gateway en decodeert het gebruikersverkeer. Vervolgens worden de geconfigureerde regels toegepast voor het selecteren van het juiste exemplaar van de back-endgroep waarnaar het verkeer moet worden doorgeleid. Application gateway initieert vervolgens een nieuwe TLS-verbinding met de backendserver en versleutelt gegevens opnieuw met behulp van het public key-certificaat van de backendserver voordat het verzoek naar de backend wordt verzonden. Reacties van de webserver ondergaan hetzelfde proces terug naar de eindgebruiker. End-to-end TLS is ingeschakeld door de protocolinstelling in [Backend HTTP-instelling](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) in te stellen op HTTPS, die vervolgens wordt toegepast op een backendpool.

Het TLS-beleid is van toepassing op zowel frontend- als backendverkeer. Aan de voorkant fungeert Application Gateway als de server en handhaaft het beleid. Op de backend fungeert Application Gateway als de client en stuurt de protocol/cipher-informatie als voorkeur tijdens de TLS-handshake.

Toepassingsgateway communiceert alleen met die backend-exemplaren die hun certificaat op de witte lijst hebben gezet met de toepassingsgateway of waarvan de certificaten zijn ondertekend door bekende CA-autoriteiten, waarbij het certificaat-CN overeenkomt met de hostnaam in de HTTP-backend-instellingen. Deze omvatten de vertrouwde Azure-services, zoals Azure App-serviceweb-apps en Azure API Management.

Als de certificaten van de leden in de backendpool niet zijn ondertekend door bekende CA-autoriteiten, moet elke instantie in de backendpool met end-to-end TLS worden geconfigureerd met een certificaat om veilige communicatie mogelijk te maken. Het toevoegen van het certificaat zorgt ervoor dat de toepassingsgateway alleen communiceert met bekende back-end-exemplaren. Dit zorgt verder voor de end-to-end communicatie.

> [!NOTE] 
>
> Het instellen van verificatiecertificaten is niet vereist voor vertrouwde Azure-services, zoals Azure App-serviceweb-apps en Azure API Management.

> [!NOTE] 
>
> Het certificaat dat is toegevoegd aan **Backend HTTP-instelling** om de backendservers te verifiëren, kan hetzelfde zijn als het certificaat dat aan de **listener** is toegevoegd voor TLS-beëindiging bij de toepassingsgateway of anders voor verbeterde beveiliging.

![end-to-end tls-scenario][1]

In dit voorbeeld worden aanvragen met TLS1.2 doorgestuurd naar backendservers in Groep1 met behulp van end-to-end TLS.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>End-to-end TLS en whitelisting van certificaten

Application Gateway communiceert alleen met bekende back-endexemplaren waarvan het certificaat is opgenomen in een lijst van toegestane certificaten voor de Application Gateway. Als u het opnemen van certificaten in een lijst van toegestane certificaten wilt inschakelen, moet u de openbare sleutel van de back-endservercertificaten uploaden naar de Application Gateway (niet het basiscertificaat). Alleen verbindingen met bekende en goedgekeurde back-ends zijn vervolgens toegestaan. De resterende back-ends hebben een gatewayfout als resultaat. Zelfondertekende certificaten zijn uitsluitend bedoeld voor testdoeleinden en het wordt afgeraden om deze in een productieomgeving te gebruiken. Dergelijke certificaten moeten op de witte lijst staan met de toepassingsgateway zoals beschreven in de voorgaande stappen voordat ze kunnen worden gebruikt.

> [!NOTE]
> Het instellen van verificatiecertificaten is niet vereist voor vertrouwde Azure-services, zoals Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End-to-end TLS met de v2 SKU

Verificatiecertificaten zijn afgeschaft en vervangen door vertrouwde basiscertificaten in de Application Gateway v2 SKU. Ze werken op dezelfde manier als verificatiecertificaten met een paar belangrijke verschillen:

- Certificaten die zijn ondertekend door bekende CA-autoriteiten waarvan de CN overeenkomt met de hostnaam in de HTTP-backend-instellingen, vereisen geen extra stap voor end-to-end TLS om te werken. 

   Als de backendcertificaten bijvoorbeeld worden uitgegeven door een bekende CA en een CN van contoso.com hebben en het hostveld van de backendhttp-instelling ook is ingesteld op contoso.com, zijn er geen extra stappen vereist. U het backend http-instellingsprotocol instellen op HTTPS en zowel de statussonde als het gegevenspad zijn TLS ingeschakeld. Als u Azure App Service of andere Azure-webservices als back-end gebruikt, worden deze impliciet ook vertrouwd en zijn er geen verdere stappen vereist voor end-to-end TLS.
   
> [!NOTE] 
>
> Om een TLS/SSL-certificaat te kunnen vertrouwen, dat certificaat van de backendserver moet zijn uitgegeven door een CA die is opgenomen in de vertrouwde winkel van de Application Gateway.Als het certificaat niet is uitgegeven door een vertrouwde CA, controleert de Application Gateway om te zien of het certificaat van de uitgevende CA is uitgegeven door een vertrouwde CA, enzovoort totdat een vertrouwde CA wordt gevonden (op welk punt een vertrouwde, beveiligde verbinding wordt gemaakt) of dat er geen vertrouwde CA kan worden gevonden (op welk punt de gateway van de toepassing de backend niet in orde wordt gemaakt). Daarom wordt aanbevolen dat het backendservercertificaat zowel de root- als de tussentijdse CA's bevat.

- Als het certificaat zelf is ondertekend of ondertekend door onbekende tussenpersonen, moet er een vertrouwd rootcertificaat worden gedefinieerd om TLS in v2 SKU in te schakelen. Application Gateway communiceert alleen met backends waarvan het rootcertificaat van het servercertificaat overeenkomt met een van de lijst met vertrouwde rootcertificaten in de backend http-instelling die aan de groep is gekoppeld.

> [!NOTE] 
>
> Het zelfondertekende certificaat moet deel uitmaken van een certificaatketen. Een enkel zelfondertekend certificaat zonder ketting wordt niet ondersteund in V2 SKU.

- Naast de overeenkomen met het rootcertificaat valideert Application Gateway ook of de hostinstelling die is opgegeven in de backend http-instelling overeenkomt met die van de algemene naam (CN) die wordt weergegeven door het TLS/SSL-certificaat van de backendserver. Wanneer u een TLS-verbinding met de backend probeert te maken, stelt Application Gateway de SNI-extensie (Server Name Indication) in op de host die is opgegeven in de backend http-instelling.
- Als **hostname van backend-adres** wordt gekozen in plaats van het veld Host in de backend http-instelling, wordt de SNI-header altijd ingesteld op de backendpool FQDN en moet de CN op de backendserver TLS/SSL-certificaat overeenkomen met de FQDN. Backend poolleden met IP's worden in dit scenario niet ondersteund.
- Het hoofdcertificaat is een base64-gecodeerd rootcertificaat van de backendservercertificaten.

## <a name="next-steps"></a>Volgende stappen

Nadat u meer te weten bent gekomen over tls van eind tot einde, gaat u naar [TLS configureren met behulp van Application Gateway met PowerShell](application-gateway-end-to-end-ssl-powershell.md) om een toepassingsgateway te maken met end-to-end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
