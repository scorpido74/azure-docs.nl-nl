---
title: Nieuwe functies voor Azure Key Vault | Microsoft Docs
description: Recente updates voor Azure Key Vault
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 07/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 78d0f483bb18213fa7d6718f15dd77733a10049c
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069353"
---
# <a name="whats-new-for-azure-key-vault"></a>Nieuwe functies voor Azure Key Vault

> [!WARNING]
> **Juli 2020**: Er zijn twee updates in Key Vault die implementaties van de service kunnen beïnvloeden: [Voorlopig verwijderen standaard ingeschakeld in Key Vault](#soft-delete-on-by-default) en [Wijzigingen in Azure TLS-certificaat](#azure-tls-certificate-changes). Kijk hieronder voor de details.

Dit zijn de nieuwe functies in Azure Key Vault. Nieuwe functies en verbeteringen worden ook aangekondigd in het [Key Vault-kanaal voor Azure-updates](https://azure.microsoft.com/updates/?category=security&query=Key%20vault).

## <a name="soft-delete-on-by-default"></a>Voorlopig verwijderen standaard ingeschakeld

Vanaf eind 2020 is de functie **Voorlopig verwijderen standaard ingeschakeld voor alle sleutelkluizen**, zowel nieuwe als bestaande. Raadpleeg het artikel [Voorlopig verwijderen wordt ingeschakeld in alle sleutelkluizen](soft-delete-change.md) voor de volledige details over deze wijziging die fouten kan veroorzaken, en voor de stappen voor het vinden en vooraf bijwerken van betrokken sleutelkluizen. 

## <a name="azure-tls-certificate-changes"></a>Wijzigingen in Azure TLS-certificaat  

Microsoft werkt Azure-services bij om TLS-certificaten te gebruiken van een andere set basis-CA's (certificeringsinstanties). Deze wijziging wordt doorgevoerd omdat de huidige CA-certificaten niet voldoen aan een van de forumbasislijnvereisten voor CA's/browsers.

### <a name="when-will-this-change-happen"></a>Wanneer vindt deze wijziging plaats?

- Deze transitie is op 7 juli 2020 begonnen in Azure AD-services (Active Directory).
- Alle nieuw gemaakte Azure TLS/SSL-eindpunten bevatten bijgewerkte certificaten die zijn gekoppeld aan de nieuwe basis-CA's. 
- Bestaande Azure-eindpunten worden vanaf 13 augustus 2020 gefaseerd overgezet. Dit wordt op 26 oktober 2020 voltooid.
- [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub) en [DPS](/azure/iot-dps/) blijven in de Baltimore CyberTrust Root CA, maar de tussenliggende CA's veranderen. [Klik hier voor meer informatie](https://techcommunity.microsoft.com/t5/internet-of-things/azure-iot-tls-changes-are-coming-and-why-you-should-care/ba-p/1658456).

> [!IMPORTANT]
> Klanten moeten hun toepassing(en) mogelijk bijwerken na deze wijziging om verbindingsfouten te voorkomen wanneer ze proberen verbinding te maken met Azure-services. 

### <a name="what-is-changing"></a>Wat wordt er gewijzigd?

Op dit moment zijn de meeste TLS-certificaten die worden gebruikt voor Azure-services, gekoppeld aan de volgende basis-CA:

| Algemene naam van de CA | Vingerafdruk (SHA1) |
|--|--|
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |

TLS-certificaten die worden gebruikt voor Azure-services, worden gekoppeld aan de volgende basis-CA's:

| Algemene naam van de CA | Vingerafdruk (SHA1) |
|--|--|
| [DigiCert Global Root G2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt) | df3c24f9bfd666761b268073fe06d1cc8d4f82a4 |
| [DigiCert Global Root CA](https://cacerts.digicert.com/DigiCertGlobalRootCA.crt) | a8985d3a65e5e5c4b2d7d66d40c6dd2fb19c5436 |
| [Baltimore CyberTrust Root](https://cacerts.digicert.com/BaltimoreCyberTrustRoot.crt) | d4de20d05e66fc53fe1a50882c78db2852cae474 |
| [D-TRUST Root Class 3 CA 2 2009](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt) | 58e8abb0361533fb80f79b1b6d29d3ff8d5f00f0 |
| [Microsoft RSA Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20RSA%20Root%20Certificate%20Authority%202017.crt) | 73a5e64a3bff8316ff0edccc618a906e4eae4d74 | 
| [Microsoft EV ECC Root Certificate Authority 2017](https://www.microsoft.com/pkiops/certs/Microsoft%20EV%20ECC%20Root%20Certificate%20Authority%202017.crt) | 6b1937abfd64e1e40daf2262a27857c015d6228d |

### <a name="when-can-i-retire-the-old-intermediate-thumbprint"></a>Wanneer kan ik de oude tussenliggende vingerafdruk buiten gebruik stellen?

De huidige CA-certificaten worden *niet* ingetrokken tot 15 februari 2021. Na deze datum kunt u de oude vingerafdrukken uit uw code verwijderen.

Als deze datum wordt gewijzigd, ontvangt u een melding over de nieuwe intrekkingsdatum.

### <a name="will-this-affect-me"></a>Heeft dit invloed op mij?

We verwachten dat **de meeste Azure-klanten niet** worden beïnvloed.  Uw toepassing kan echter worden beïnvloed als expliciet een lijst met geaccepteerde CA's is opgegeven voor de toepassing. Dit wordt certificaatkoppeling genoemd.

Hier zijn enkele manieren om vast te stellen of uw toepassing wordt beïnvloed:

- Bekijk uw broncode voor de vingerafdruk, de algemene naam, en andere certificaateigenschappen van de Microsoft IT TLS-CA's die u [hier](https://www.microsoft.com/pki/mscorp/cps/default.htm) kunt vinden. Als er een overeenkomst is, heeft dit gevolgen voor de toepassing. Als u dit probleem wilt oplossen, werkt u de broncode bij met de nieuwe CA's. Het is een aanbevolen procedure om ervoor te zorgen dat CA's op korte termijn kunnen worden toegevoegd of bewerkt. Industriële regelgeving vereist dat CA -certificaten binnen 7 dagen worden vervangen, en daarom moeten klanten die afhankelijk zijn van koppeling, snel reageren.

- Als u een toepassing hebt die integreert met Azure-API's of andere Azure-services, en u niet zeker weet of de toepassing gebruikmaakt van certificaatkoppeling, neemt u contact op met de leverancier.

- Voor verschillende besturingssystemen en taalruntimes die communiceren met Azure-services, zijn mogelijk extra stappen vereist om de certificaatketen juist te bouwen met deze nieuwe basis-CA's. 
    - **Linux**: Voor veel distributies moet u de hierboven vermelde CA's toevoegen aan /etc/ssl/certs. Raadpleeg de documentatie van de distributie voor specifieke instructies.
    - **Java**: Controleer of het Java-sleutelarchief de CA's bevat die hierboven worden vermeld.
    - **Windows uitgevoerd in omgevingen zonder verbinding**: Voor systemen die worden uitgevoerd in omgevingen zonder verbinding, moeten de bovenstaande basis-CA's worden toegevoegd aan het archief Vertrouwde basiscertificeringsinstanties, en de tussenliggende CA's aan het archief Tussenliggende certificeringsinstanties.
    - **Android**: Raadpleeg de documentatie voor uw apparaat en versie van Android.
    - **Andere hardwareapparaten, met name IoT**: Neem contact op met de fabrikant van het apparaat. 

- Als u een omgeving hebt waarin firewallregels zijn ingesteld om alleen uitgaande oproepen toe te staan naar specifieke CRL-downloadlocaties (certificaatintrekkingslijst) en/of OCSP-verificatielocaties (Online Certificate Status Protocol). U hebt de volgende CRL- en OCSP-URL's nodig:

    - http://crl3&#46;digicert&#46;com
    - http://crl4&#46;digicert&#46;com
    - http://ocsp&#46;digicert&#46;com
    - http://www&#46;d-trust&#46;net
    - http://root-c3-ca2-2009&#46;ocsp&#46;d-trust&#46;net
    - http://crl&#46;microsoft&#46;com
    - http://oneocsp&#46;microsoft&#46;com
    - http://ocsp&#46;msocsp&#46;com

## <a name="june-2020"></a>Juni 2020

Azure Monitor voor Key Vault is nu beschikbaar als preview-versie.  Azure Monitor biedt uitgebreide bewaking van uw sleutelkluizen door een uniforme weergave te bieden van uw aanvragen, prestaties, fouten en latentie in Key Vault. Zie [Azure Monitor voor Azure Key Vault (preview)](../../azure-monitor/insights/key-vault-insights-overview.md) voor meer informatie.

## <a name="may-2020"></a>Mei 2020

Key Vault BYOK (Bring Your Own Key) is nu algemeen beschikbaar. Raadpleeg de [Azure Key Vault BYOK-specificatie](../keys/byok-specification.md) en leer hoe u [Met HSM beveiligde sleutels importeert in Key Vault (BYOK)](../keys/hsm-protected-keys-byok.md).

## <a name="march-2020"></a>Maart 2020

Privé-eind punten zijn nu beschikbaar als preview-versie. Met Azure Private Link hebt u via een privé-eindpunt in uw virtuele netwerk toegang tot Azure Key Vault en in Azure gehoste services van klanten of partners.  Meer informatie over [Sleutelkluis integreren met Azure Private Link](private-link-service.md)

## <a name="2019"></a>2019

- Release van de Azure Key Vault-SDK's van de volgende generatie. Raadpleeg de quickstarts over Azure Key Vault-geheimen voor [Python](../secrets/quick-create-python.md), [.NET](../secrets/quick-create-net.md), [Java](../secrets/quick-create-java.md) en [Node.js](../secrets/quick-create-node.md) voor gebruiksvoorbeelden
- Nieuwe Azure-beleidsregels voor het beheren van sleutelkluiscertificaten. Zie de [Ingebouwde Azure Policy-definities voor Key Vault](../policy-samples.md).
- Azure Key Vault-extensie van virtuele machine is nu algemeen beschikbaar.  Zie [Key Vault-extensie van virtuele machine voor Linux](../../virtual-machines/extensions/key-vault-linux.md) en [Key Vault-extensie van virtuele machine voor Windows](../../virtual-machines/extensions/key-vault-windows.md).
- Gebeurtenisgestuurd beheer van geheimen voor Azure Key Vault is nu beschikbaar in Azure Event Grid. Bekijk [het Event Grid-schema voor gebeurtenissen in Azure Key Vault](../../event-grid/event-schema-key-vault.md] en [Key Vault-meldingen ontvangen en beantwoorden met Azure Event Grid](event-grid-tutorial.md) voor meer informatie.

## <a name="2018"></a>2018

Nieuwe functies en integraties die dit jaar zijn uitgebracht:

- Integratie met Azure Functions. Zie [De rotatie van een geheim automatiseren](../secrets/tutorial-rotation.md) voor een voorbeeldscenario waarin [Azure Functions](../../azure-functions/index.yml) wordt gebruikt voor sleutelkluisbewerkingen. 
- [Integratie met Azure Databricks](/azure/databricks/scenarios/store-secrets-azure-key-vault). Hiermee biedt Azure Databricks nu ondersteuning voor twee geheimbereiktypen: Ondersteund in Azure Key Vault en Databricks. Raadpleeg [Een geheimbereik met ondersteuning in Azure Key Vault maken](/azure/databricks/security/secrets/secret-scopes#--create-an-azure-key-vault-backed-secret-scope) voor meer informatie
- [Service-eindpunten voor virtuele netwerken voor Azure Key Vault](overview-vnet-service-endpoints.md).

## <a name="2016"></a>2016

Nieuwe functies die dit jaar zijn uitgebracht:

- Beheerde opslagaccountsleutels. Met de functie Opslagaccountsleutels is eenvoudigere integratie met Azure Storage toegevoegd. Bekijk het overzichtsonderwerp [Overzicht van Beheerde opslagaccountsleutels](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-storage-keys) voor meer informatie.
- Voorlopig verwijderen. Met de functie Voorlopig verwijderen is gegevensbescherming van uw sleutelkluizen en sleutelkluisobjecten verbeterd. Bekijk het overzichtsonderwerp [Overzicht van Voorlopig verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) voor meer informatie.

## <a name="2015"></a>2015

Nieuwe functies die dit jaar zijn uitgebracht:
- Certificaatbeheer. Als functie toegevoegd aan de GA-versie 2015-06-01, op 26 september 2016.

Algemene beschikbaarheid (versie 2015-06-01) werd aangekondigd op 24 juni 2015. In deze release zijn de volgende wijzigingen aangebracht: 
- Een sleutel verwijderen - veld 'gebruiken' is verwijderd.
- Informatie ophalen over een sleutel - veld 'gebruiken' is verwijderd.
- Een sleutel importeren in een kluis - veld 'gebruiken' is verwijderd.
- Een sleutel herstellen - veld 'gebruiken' is verwijderd.     
- RSA_OAEP is gewijzigd in RSA-OAEP voor RSA-algoritmen. Zie [Over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).    
 
De tweede preview-versie (versie 2015-02-01-preview) werd aangekondigd op 20 april 2015. Zie de blogpost [REST API-update](https://docs.microsoft.com/archive/blogs/kv/rest-api-update) voor meer informatie. De volgende taken zijn bijgewerkt:
 
- De sleutels in een kluis vermelden - ondersteuning voor paginering toegevoegd aan bewerking.
- De versies van een sleutel vermelden - bewerking toegevoegd voor het vermelden van de versies van een sleutel.  
- Geheimen in een kluis vermelden - ondersteuning voor paginering toegevoegd.
- Versies van een geheim vermelden - bewerking toegevoegd voor het vermelden van de versies van een geheim.  
- Alle bewerkingen - gemaakt/bijgewerkte tijdstempels toegevoegd aan kenmerken.  
- Een geheim maken - inhoudstype toegevoegd aan geheimen.
- Een sleutel maken - tags toegevoegd als optionele informatie.
- Een geheim maken - tags toegevoegd als optionele informatie.
- Een sleutel bijwerken - tags toegevoegd als optionele informatie.
- Een geheim bijwerken - tags toegevoegd als optionele informatie.
- De maximale grootte voor geheimen is gewijzigd van 10 K in 25 kB. Zie [Over sleutels, geheimen en certificaten](about-keys-secrets-certificates.md).    

## <a name="2014"></a>2014
 
De eerste preview-versie (versie 2014-12-08-preview) werd aangekondigd op 8 januari 2015.  

## <a name="next-steps"></a>Volgende stappen

Als u aanvullende vragen hebt, kunt u contact met ons opnemen via [ondersteuning](https://azure.microsoft.com/support/options/).  
