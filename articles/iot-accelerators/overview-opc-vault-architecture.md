---
title: OPC-kluis architectuur-Azure | Microsoft Docs
description: OPC kluis certificaat beheer service-architectuur
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "71200155"
---
# <a name="opc-vault-architecture"></a>OPC-kluis architectuur

Dit artikel bevat een overzicht van de OPC kluis micro service en de OPC kluis IoT Edge-module.

OPC UA-toepassingen gebruiken toepassings exemplaar certificaten om beveiliging op toepassings niveau te bieden. Er wordt een beveiligde verbinding tot stand gebracht met behulp van asymmetrische crypto grafie waarvoor de toepassings certificaten het open bare en persoonlijke sleutel paar bieden. De certificaten kunnen zelfondertekend of ondertekend zijn door een certificerings instantie (CA).

Een OPC UA-toepassing heeft een lijst met vertrouwde certificaten die de vertrouw bare toepassingen vertegenwoordigt. Deze certificaten kunnen zelfondertekend of ondertekend zijn door een CA, of kunnen een basis-CA of een subca zijn. Als een vertrouwd certificaat deel uitmaakt van een grotere certificaat keten, vertrouwt de toepassing alle certificaten die zijn gekoppeld aan het certificaat in de lijst met vertrouwens relaties. Dit geldt zolang de volledige certificaat keten kan worden gevalideerd.

Het belangrijkste verschil tussen het vertrouwen van zelfondertekende certificaten en het vertrouwen van een CA-certificaat is de installatie-inspanning die nodig is voor het implementeren en onderhouden van vertrouwen. Er is ook extra inspanning voor het hosten van een bedrijfsspecifieke CA. 

Als u het vertrouwen wilt distribueren voor zelfondertekende certificaten voor meerdere servers met één client toepassing, moet u alle server toepassings certificaten installeren in de lijst met vertrouwde client toepassingen. Daarnaast moet u het client toepassings certificaat installeren op alle Server Application Trust-lijsten. Deze administratieve inspanning is een zeer last en zelfs groter wanneer u de levens duur van het certificaat moet overwegen en certificaten wilt vernieuwen.

Het gebruik van een bedrijfsspecifieke CA kan het beheer van vertrouwen met meerdere-servers en-clients aanzienlijk vereenvoudigen. In dit geval genereert de beheerder voor elke gebruikte client en server een CA-ondertekend certificaat voor het toepassings exemplaar. Daarnaast wordt het CA-certificaat geïnstalleerd in elke lijst met vertrouwde toepassingen, op alle servers en clients. Met deze aanpak moeten alleen verlopen certificaten worden vernieuwd en vervangen voor de betrokken toepassingen.

Met de Azure-service voor het beheer van de industriële IoT OPC kan u een bedrijfsspecifieke CA voor OPC UA-toepassingen beheren. Deze service is gebaseerd op de OPC-kluis micro service. OPC-kluis biedt een micro service om een bedrijfsspecifieke CA in een beveiligde Cloud te hosten. Deze oplossing wordt ondersteund door services die zijn beveiligd door Azure Active Directory (Azure AD), Azure Key Vault met Hardware Security modules (Hsm's), Azure Cosmos DB en optioneel IoT Hub als een toepassings archief.

De OPC-kluis micro service is ontworpen ter ondersteuning van op rollen gebaseerde werk stromen, waarbij beveiligings beheerders en goed keurders met ondertekenings rechten in Azure Key Vault aanvragen goed keuren of afwijzen.

Voor compatibiliteit met bestaande OPC UA-oplossingen zijn de Services voorzien van ondersteuning voor een met de module OPC kluis micro service ondersteunde rand. Hiermee implementeert u de **OPC UA Global Discovery-server en de certificaat beheer** interface voor het distribueren van certificaten en vertrouwens lijsten volgens deel 12 van de specificatie. 


## <a name="architecture"></a>Architectuur

De architectuur is gebaseerd op de OPC-kluis micro service, met een OPC kluis IoT Edge module voor het fabrieks netwerk en een webvoorbeeld UX om de werk stroom te beheren:

![Diagram van OPC-kluis architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC-kluis micro service

De OPC kluis micro service bestaat uit de volgende interfaces voor het implementeren van de werk stroom voor het distribueren en beheren van een bedrijfsspecifieke CA voor OPC UA-toepassingen.

### <a name="application"></a>Toepassing 
- Een OPC UA-toepassing kan een server of een client zijn, of beide. OPC-kluis fungeert in dit geval als een registratie-instantie voor toepassingen. 
- Naast de basis bewerkingen voor het registreren, bijwerken en ongedaan maken van de registratie van toepassingen, zijn er ook interfaces om toepassingen met zoek expressies te vinden en er query's op uit te zoeken. 
- De certificaat aanvragen moeten verwijzen naar een geldige toepassing om een aanvraag te verwerken en een ondertekend certificaat uit te geven met alle OPC UA-specifieke uitbrei dingen. 
- De toepassings service wordt ondersteund door een data base in Azure Cosmos DB.

### <a name="certificate-group"></a>Certificaat groep
- Een certificaat groep is een entiteit waarin een basis-CA of een subca-certificaat wordt opgeslagen, met inbegrip van de persoonlijke sleutel voor het ondertekenen van certificaten. 
- De lengte van de RSA-sleutel, de SHA-2-hash-lengte en de levens duur kunnen worden geconfigureerd voor zowel de verlenende CA als de ondertekende toepassings certificaten. 
- U slaat de CA-certificaten op in Azure Key Vault, die worden ondersteund met FIPS 140-2 level 2 HSM. De persoonlijke sleutel verlaat nooit de beveiligde opslag, omdat ondertekening wordt uitgevoerd door een Key Vault bewerking die wordt beveiligd door Azure AD. 
- U kunt de CA-certificaten na verloop van tijd vernieuwen en ze blijven behouden in veilige opslag vanwege Key Vault geschiedenis. 
- De intrekkings lijst voor elk CA-certificaat wordt ook opgeslagen in Key Vault als een geheim. Wanneer een toepassing niet is geregistreerd, wordt het toepassings certificaat ook ingetrokken in de certificaatintrekkingslijst (CRL) door een beheerder.
- U kunt afzonderlijke certificaten intrekken, evenals de batch certificaten.

### <a name="certificate-request"></a>Certificaat aanvraag
Een certificaat aanvraag implementeert de werk stroom voor het genereren van een nieuw sleutel paar of een ondertekend certificaat met behulp van een aanvraag voor certificaat ondertekening (CSR) voor een OPC UA-toepassing. 
- De aanvraag wordt opgeslagen in een Data Base met bijbehorende informatie, zoals het onderwerp of een CSR, en een verwijzing naar de OPC UA-toepassing. 
- De bedrijfs logica in de service valideert de aanvraag met de informatie die is opgeslagen in de toepassings database. De URI van de toepassing in de data base moet bijvoorbeeld overeenkomen met de toepassings-URI in de CSR.
- Een beveiligings beheerder met aanmeldings rechten (dat wil zeggen, de functie fiatteur) keurt de aanvraag goed of weigert deze. Als de aanvraag is goedgekeurd, wordt een nieuw sleutel paar of een ondertekend certificaat (of beide) gegenereerd. De nieuwe persoonlijke sleutel wordt beveiligd opgeslagen in Key Vault en het nieuwe ondertekende open bare certificaat wordt opgeslagen in de data base van de certificaat aanvraag.
- De aanvrager kan de aanvraag status controleren totdat deze is goedgekeurd of ingetrokken. Als de aanvraag is goedgekeurd, kunnen de persoonlijke sleutel en het certificaat worden gedownload en geïnstalleerd in het certificaat archief van de OPC UA-toepassing.
- De aanvrager kan nu de aanvraag accepteren om overbodige gegevens uit de aanvraag database te verwijderen. 

Gedurende de levens duur van een ondertekend certificaat kan een toepassing worden verwijderd of kan een sleutel worden aangetast. In een dergelijk geval kan een CA-beheerder:
- Een toepassing verwijderen, waarmee ook alle in behandeling zijnde en goedgekeurde certificaat aanvragen van de app worden verwijderd. 
- U kunt slechts één certificaat aanvraag verwijderen als alleen een sleutel wordt vernieuwd of wordt geknoeid.

Er zijn nu geïnfecteerde goedgekeurde en geaccepteerde certificaat aanvragen gemarkeerd als verwijderd.

Een manager kan de CA-CRL van de verlener regel matig vernieuwen. Op het moment van de verlenging worden alle verwijderde certificaat aanvragen ingetrokken en worden de serie nummers van het certificaat toegevoegd aan de intrekkings lijst voor CERTIFICAATINTREKKINGSLIJSTen. Ingetrokken certificaat aanvragen worden als ingetrokken gemarkeerd. In dringende gebeurtenissen kunnen ook enkele certificaat aanvragen worden ingetrokken.

Ten slotte zijn de bijgewerkte Crl's beschikbaar voor distributie naar de deelnemende OPC UA-clients en-servers.

## <a name="opc-vault-iot-edge-module"></a>OPC-kluis IoT Edge module
Ter ondersteuning van de globale detectie server van het netwerk, kunt u de OPC-kluis module op de rand implementeren. Voer deze uit als een lokale .NET core-toepassing of start deze in een docker-container. Houd er rekening mee dat vanwege een gebrek aan ondersteuning voor Auth2-verificatie in de huidige OPC UA .NET Standard-stack, de functionaliteit van de module OPC kluis Edge beperkt is tot een rol van lezer. Een gebruiker kan niet worden geïmiteerd van de module Edge naar de micro service door gebruik te maken van de standaard interface OPC UA GDS.

## <a name="next-steps"></a>Volgende stappen

Nu u over de OPC-kluis architectuur hebt geleerd, kunt u het volgende doen:

> [!div class="nextstepaction"]
> [OPC-kluis bouwen en implementeren](howto-opc-vault-deploy.md)
