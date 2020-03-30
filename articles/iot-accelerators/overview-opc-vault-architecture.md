---
title: OPC Vault-architectuur - Azure | Microsoft Documenten
description: OPC Vault-certificeringsbeheerservicearchitectuur
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 1e08968034134e2b9ab3b8064387d18663d5c866
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "71200155"
---
# <a name="opc-vault-architecture"></a>OPC Vault-architectuur

Dit artikel geeft een overzicht over de OPC Vault-microservice en de OPC Vault IoT Edge-module.

OPC UA-toepassingen gebruiken toepassingsinstantiecertificaten om beveiliging op toepassingsniveau te bieden. Een beveiligde verbinding wordt tot stand gebracht door gebruik te maken van asymmetrische cryptografie, waarvoor de toepassingscertificaten het publieke en private sleutelpaar bieden. De certificaten kunnen zelf worden ondertekend of ondertekend door een Certificeringsinstantie (CA).

Een OPC UA-toepassing heeft een lijst met vertrouwde certificaten die de toepassingen vertegenwoordigt die worden vertrouwd. Deze certificaten kunnen zelf worden ondertekend of ondertekend door een CA, of kunnen zelf een Root-CA of een Sub-CA zijn. Als een vertrouwd certificaat deel uitmaakt van een grotere certificaatketen, vertrouwt de toepassing alle certificaten die worden vastgeketend tot het certificaat in de vertrouwenslijst. Dit geldt zolang de volledige certificaatketen kan worden gevalideerd.

Het grote verschil tussen het vertrouwen in zelfondertekende certificaten en het vertrouwen op een CA-certificaat is de installatie-inspanning die nodig is om het implementeren en behouden van vertrouwen. Er is ook extra inspanning om een bedrijfsspecifieke CA te hosten. 

Als u vertrouwensregels voor zelfondertekende certificaten voor meerdere servers wilt distribueren met één clienttoepassing, moet u alle servertoepassingscertificaten installeren op de lijst met vertrouwensgegevens voor clienttoepassingen. Bovendien moet u het clienttoepassingscertificaat installeren op alle vertrouwenslijsten voor servertoepassingen. Deze administratieve inspanning is nogal een last, en zelfs toeneemt wanneer u certificaat levensduur te overwegen en certificaten te vernieuwen.

Het gebruik van een bedrijfsspecifieke CA kan het beheer van vertrouwen met meerdere servers en clients sterk vereenvoudigen. In dit geval genereert de beheerder eenmaal een CA-ondertekend toepassingsinstantiecertificaat voor elke gebruikte client en server. Bovendien is het CA-certificaat geïnstalleerd in elke lijst met toepassingsvertrouwensrelaties, op alle servers en clients. Met deze aanpak hoeven alleen verlopen certificaten te worden vernieuwd en vervangen voor de betreffende toepassingen.

Azure Industrial IoT OPC UA-certificaatbeheerservice helpt u bij het beheren van een bedrijfsspecifieke CA voor OPC UA-toepassingen. Deze service is gebaseerd op de OPC Vault microservice. OPC Vault biedt een microservice om een bedrijfsspecifieke CA te hosten in een beveiligde cloud. Deze oplossing wordt ondersteund door services die zijn beveiligd door Azure Active Directory (Azure AD), Azure Key Vault met Hardware Security Modules (HSMs), Azure Cosmos DB en optioneel IoT Hub als toepassingsarchief.

De OPC Vault-microservice is ontworpen om op rollen gebaseerde werkstroom te ondersteunen, waarbij beveiligingsbeheerders en fiatteurs met ondertekeningsrechten in Azure Key Vault aanvragen goedkeuren of afwijzen.

Voor compatibiliteit met bestaande OPC UA-oplossingen omvatten de services ondersteuning voor een opc Vault-microservice-backed edge-module. Hiermee wordt de **OPC UA Global Discovery Server en Certificate Management-interface** geïmplementeerd om certificaten en vertrouwenslijsten te distribueren volgens deel 12 van de specificatie. 


## <a name="architecture"></a>Architectuur

De architectuur is gebaseerd op de OPC Vault-microservice, met een OPC Vault IoT Edge-module voor het fabrieksnetwerk en een websample UX om de workflow te beheren:

![Diagram van OPC Vault-architectuur](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>OPC Vault-microservice

De OPC Vault-microservice bestaat uit de volgende interfaces om de workflow te implementeren om een bedrijfsspecifieke CA voor OPC UA-toepassingen te distribueren en te beheren.

### <a name="application"></a>Toepassing 
- Een OPC UA-toepassing kan een server of een client zijn, of beide. OPC Vault dient in dit geval als een applicatie registratie autoriteit. 
- Naast de basisbewerkingen voor het registreren, bijwerken en uitschrijven van toepassingen, zijn er ook interfaces om toepassingen met zoekexpressies te vinden en te bevragen. 
- De certificaataanvragen moeten verwijzen naar een geldige aanvraag om een aanvraag te verwerken en om een ondertekend certificaat uit te geven met alle OPC UA-specifieke extensies. 
- De toepassingsservice wordt ondersteund door een database in Azure Cosmos DB.

### <a name="certificate-group"></a>Certificaatgroep
- Een certificaatgroep is een entiteit die een basis-CA- of sub-CA-certificaat opslaat, inclusief de privésleutel om certificaten te ondertekenen. 
- De RSA-sleutellengte, de SHA-2-hashlengte en de levensduur zijn configureerbaar voor zowel emittent-CA- als ondertekende toepassingscertificaten. 
- U slaat de CA-certificaten op in Azure Key Vault, ondersteund met FIPS 140-2 Level 2 HSM. De privésleutel verlaat nooit de beveiligde opslag, omdat ondertekening wordt uitgevoerd door een Key Vault-bewerking die is beveiligd door Azure AD. 
- U de CA-certificaten na verloop van tijd verlengen en ze in veilige opslag laten blijven vanwege de sleutelkluisgeschiedenis. 
- De intrekkingslijst voor elk CA-certificaat wordt ook als geheim opgeslagen in Key Vault. Wanneer een aanvraag niet is geregistreerd, wordt het aanvraagcertificaat ook inde certificaatintrekkingslijst (CRL) ingetrokken door een beheerder.
- U afzonderlijke certificaten en gebatched certificaten intrekken.

### <a name="certificate-request"></a>Certificaataanvraag
Een certificaataanvraag implementeert de werkstroom om een nieuw sleutelpaar of een ondertekend certificaat te genereren met behulp van een Csr (Certificate Signing Request) voor een OPC UA-toepassing. 
- De aanvraag wordt opgeslagen in een database met bijbehorende informatie, zoals het onderwerp of een CSR, en een verwijzing naar de OPC UA-toepassing. 
- De bedrijfslogica in de service valideert de aanvraag op basis van de informatie die is opgeslagen in de toepassingsdatabase. De toepassing Uri in de database moet bijvoorbeeld overeenkomen met de toepassing Uri in de CSR.
- Een beveiligingsbeheerder met ondertekeningsrechten (dat wil zeggen de rol Goedkeurende goedkeuring) keurt het verzoek goed of wijst deze af. Als de aanvraag wordt goedgekeurd, wordt een nieuw sleutelpaar of ondertekend certificaat (of beide) gegenereerd. De nieuwe privésleutel wordt veilig opgeslagen in Key Vault en het nieuwe ondertekende openbare certificaat wordt opgeslagen in de database met certificaataanvragen.
- De aanvrager kan de status van het verzoek peilen totdat deze is goedgekeurd of ingetrokken. Als de aanvraag is goedgekeurd, kunnen de privésleutel en het certificaat worden gedownload en geïnstalleerd in het certificaatarchief van de OPC UA-toepassing.
- De aanvrager kan nu het verzoek accepteren om onnodige informatie uit de aanvraagdatabase te verwijderen. 

Gedurende de levensduur van een ondertekend certificaat kan een toepassing worden verwijderd of kan een sleutel worden aangetast. In een dergelijk geval kan een CA-manager:
- Een toepassing verwijderen, die ook alle in behandeling zijnde en goedgekeurde certificaataanvragen van de app verwijdert. 
- Verwijder slechts één certificaataanvraag, als alleen een sleutel wordt vernieuwd of gecompromitteerd.

Nu worden gecompromitteerde goedgekeurde en geaccepteerde certificaataanvragen gemarkeerd als verwijderd.

Een manager kan de Issuer CA CRL regelmatig vernieuwen. Tijdens de verlengingworden alle verwijderde certificaataanvragen ingetrokken en worden de certificaatserienummers toegevoegd aan de crl-intrekkingslijst. Ingetrokken certificaataanvragen worden gemarkeerd als ingetrokken. In dringende gebeurtenissen kunnen ook aanvragen voor één certificaat worden ingetrokken.

Ten slotte zijn de bijgewerkte CRL's beschikbaar voor distributie naar de deelnemende OPC UA-clients en -servers.

## <a name="opc-vault-iot-edge-module"></a>OPC Vault IoT Edge-module
Als u een global discovery server in een fabrieksnetwerk wilt ondersteunen, u de OPC Vault-module op de rand implementeren. Voer het uit als een lokale .NET Core-toepassing of start het in een Docker-container. Houd er rekening mee dat vanwege een gebrek aan Auth2-verificatieondersteuning in de huidige OPC UA .NET Standard-stack de functionaliteit van de OPC Vault-randmodule beperkt is tot een Reader-rol. Een gebruiker kan niet worden nagebootst van de randmodule tot de microservice met behulp van de OPC UA GDS-standaardinterface.

## <a name="next-steps"></a>Volgende stappen

Nu u meer te weten bent gekomen over de OPC Vault-architectuur, u het:

> [!div class="nextstepaction"]
> [OPC Vault bouwen en implementeren](howto-opc-vault-deploy.md)
