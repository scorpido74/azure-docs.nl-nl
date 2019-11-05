---
title: Microsoft Azure gegevens versleuteling-at-rest | Microsoft Docs
description: Dit artikel bevat een overzicht van Microsoft Azure gegevens versleuteling in rust, de algemene mogelijkheden en algemene overwegingen.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2019
ms.author: barclayn
ms.openlocfilehash: 0fddabc7973fbea147de9f3b6e6da5556407030c
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498453"
---
# <a name="azure-data-encryption-at-rest"></a>Azure-gegevens versleuteling-at-rest

Microsoft Azure bevat hulpprogram ma's voor het beveiligen van gegevens op basis van de beveiligings-en nalevings behoeften van uw bedrijf. Dit document is gericht op:

- Hoe gegevens worden beveiligd in de rest van Microsoft Azure
- Bespreekt de verschillende onderdelen die deel uitmaken van de implementatie van gegevens beveiliging,
- Beoordelingen van de voor-en nadelen van de verschillende beveiligings benaderingen voor sleutel beheer.

Versleuteling op rest is een gemeen schappelijke beveiligings vereiste. In azure kunnen organisaties gegevens in rust versleutelen zonder het risico of de kosten van een aangepaste oplossing voor sleutel beheer. Organisaties hebben de mogelijkheid om de versleuteling van Azure volledig te beheren. Daarnaast hebben organisaties verschillende opties om versleuteling of versleutelings sleutels nauw keurig te beheren.

## <a name="what-is-encryption-at-rest"></a>Wat is versleuteling in rust?

Versleuteling bij rest is de code ring (versleuteling) van gegevens wanneer deze persistent wordt gemaakt. De versleuteling bij rest-ontwerpen in azure gebruikt symmetrische versleuteling om grote hoeveel heden gegevens snel te versleutelen en te ontsleutelen op basis van een eenvoudig conceptueel model:

- Een symmetrische versleutelings sleutel wordt gebruikt voor het versleutelen van gegevens die naar opslag worden geschreven.
- Dezelfde versleutelings sleutel wordt gebruikt om die gegevens te ontsleutelen wanneer deze gereed is voor gebruik in het geheugen.
- Gegevens kunnen gepartitioneerd zijn en verschillende sleutels kunnen voor elke partitie worden gebruikt.
- Sleutels moeten worden opgeslagen op een veilige locatie met toegangs beheer op basis van identiteit en controle beleid. Gegevens versleutelings sleutels worden vaak versleuteld met een sleutel versleutelings sleutel in Azure Key Vault om de toegang verder te beperken.

In de praktijk moeten sleutel beheer-en controle scenario's, evenals schaal-en beschikbaarheids garanties, extra constructs vereisen. Microsoft Azure versleuteling bij rest-concepten en-onderdelen worden hieronder beschreven.

## <a name="the-purpose-of-encryption-at-rest"></a>Het doel van versleuteling bij rust

Versleuteling bij rest biedt gegevens beveiliging voor opgeslagen gegevens (op rest). Aanvallen tegen gegevens op rest zijn onder andere pogingen om fysieke toegang te verkrijgen tot de hardware waarop de gegevens worden opgeslagen en vervolgens de Inge sloten gegevens in gevaar te brengen. In een dergelijke aanval is het mogelijk dat de harde schijf van een server tijdens het onderhoud niet kan worden verwerkt, waardoor een aanvaller de harde schijf kan verwijderen. Later zou de aanvaller de harde schijf in een computer onder hun controle kunnen plaatsen om toegang te krijgen tot de gegevens.

Versleuteling op rest is ontworpen om te voor komen dat een aanvaller toegang krijgt tot de niet-versleutelde gegevens door ervoor te zorgen dat de gegevens worden versleuteld wanneer ze op schijf zijn. Als een aanvaller een harde schijf verkrijgt met versleutelde gegevens, maar niet de versleutelings sleutels, moet de aanvaller de versleuteling verslaan om de gegevens te lezen. Deze aanval is veel ingewik kelder en maakt gebruik van resources dan toegang tot niet-versleutelde gegevens op een harde schijf. Daarom wordt versleuteling in rust sterk aanbevolen en is een hoge prioriteit vereist voor veel organisaties.

Versleuteling in rust kan ook worden vereist door de nood zaak van een organisatie voor data governance en nalevings inspanningen. Industriële en wettelijke voor schriften zoals HIPAA, PCI en FedRAMP, leggen specifieke veiligheids maatregelen vast over de vereisten voor gegevens bescherming en-versleuteling. Versleuteling op rest is een verplichte maat regel die vereist is om te voldoen aan sommige van deze voor Schriften.

Naast het voldoen aan de nalevings vereisten en de regelgeving, biedt versleuteling op rest een ingrijpende beveiliging. Microsoft Azure biedt een compatibel platform voor services, toepassingen en gegevens. Het biedt ook uitgebreide functionaliteit en fysieke beveiliging, gegevens toegangs beheer en controle. Het is echter belang rijk om extra "overlappende" beveiligings maatregelen te bieden voor het geval een van de andere veiligheids maatregelen mislukt en versleuteling in rust biedt een dergelijke beveiligings maatregel

Micro soft hecht veel belang aan het versleutelen van de rest-opties in Cloud Services en geeft klanten controle over de versleutelings sleutels en logboeken van het sleutel gebruik. Daarnaast werkt micro soft standaard bij het versleutelen van alle klant gegevens op rest.

## <a name="azure-encryption-at-rest-components"></a>Azure-versleuteling bij rest-onderdelen

Zoals eerder beschreven, is het doel van het versleutelen van versleuteling bij rest de gegevens die persistent zijn op schijf versleuteld met een geheime versleutelings sleutel. U kunt het beste een beveiligde sleutel maken, opslag, Toegangs beheer en beheer van de versleutelings sleutels opgeven. Hoewel de details kunnen verschillen, kan de Azure-Services-versleuteling bij rest-implementaties worden beschreven in termen die in het volgende diagram worden geïllustreerd.

![Onderdelen](./media/encryption-atrest/azure-security-encryption-atrest-fig1.png)

### <a name="azure-key-vault"></a>Azure Key Vault

De opslag locatie van de versleutelings sleutels en toegangs beheer voor die sleutels is centraal voor een versleuteling in het rest-model. De sleutels moeten zeer goed worden beveiligd, maar kunnen worden beheerd door opgegeven gebruikers en beschikbaar zijn voor specifieke services. Voor Azure-Services is Azure Key Vault de aanbevolen oplossing voor sleutel opslag en biedt een gemeen schappelijke beheer ervaring tussen services. Sleutels worden opgeslagen en beheerd in sleutel kluizen, en toegang tot een sleutel kluis kan worden gegeven aan gebruikers of services. Azure Key Vault ondersteunt het maken van sleutels of het importeren van klant sleutels voor gebruik in scenario's met door de klant beheerde versleutelings sleutel.

### <a name="azure-active-directory"></a>Azure Active Directory

Machtigingen voor het gebruik van sleutels die zijn opgeslagen in Azure Key Vault, om deze te beheren of te openen voor versleuteling bij rest versleuteling en ontsleuteling, kunnen worden verleend aan Azure Active Directory-accounts.

### <a name="key-hierarchy"></a>Sleutel hiërarchie

Er wordt meer dan één versleutelings sleutel gebruikt in een versleuteling bij rest-implementatie. Het opslaan van een versleutelings sleutel in Azure Key Vault zorgt voor veilige toegang tot de sleutel en Centraal beheer van sleutels. De lokale toegang tot de versleutelings sleutels van de service is echter efficiënter voor bulk versleuteling en ontsleuteling dan met Key Vault voor elke gegevens bewerking, waardoor een sterkere versleuteling en betere prestaties mogelijk zijn. Het beperken van het gebruik van één versleutelings sleutel vermindert het risico dat de sleutel wordt aangetast en de kosten voor het opnieuw versleutelen wanneer een sleutel moet worden vervangen. Azure-versleuteling bij rest-modellen gebruiken een belang rijke hiërarchie die bestaat uit de volgende soorten sleutels om deze behoeften aan te pakken:

- **Gegevens versleutelings sleutel (dek)** : een SYMMETRISCHe AES256-sleutel die wordt gebruikt om een partitie of gegevens blok te versleutelen.  Eén resource kan veel partities en veel gegevens versleutelings sleutels hebben. Het versleutelen van elk gegevens blok met een andere sleutel maakt crypto-analyse aanvallen lastiger. Toegang tot DEKs is vereist voor de resource provider of het toepassings exemplaar dat een specifiek blok versleutelt en ontsleutelt. Wanneer een DEK wordt vervangen door een nieuwe sleutel, moeten alleen de gegevens in het bijbehorende blok opnieuw worden versleuteld met de nieuwe sleutel.
- Sleutel versleutelings **sleutel (KEK)** : een versleutelings sleutel die wordt gebruikt om de gegevens versleutelings sleutels te versleutelen. Door gebruik te maken van een versleutelings sleutel die Key Vault nooit verlaat, kunnen de gegevens versleutelings sleutels zelf worden versleuteld en beheerd. De entiteit die toegang heeft tot de KEK kan afwijken van de entiteit waarvoor de DEK is vereist. Een entiteit kan toegang tot de DEK Broker om de toegang van elke DEK te beperken tot een specifieke partitie. Omdat de KEK is vereist voor het ontsleutelen van de DEKs, is de KEK een specifiek punt waarmee DEKs effectief kan worden verwijderd door het verwijderen van de KEK.

De gegevens versleutelings sleutels, versleuteld met de sleutel versleutelings sleutels, worden afzonderlijk opgeslagen en alleen een entiteit met toegang tot de sleutel coderings sleutel kan deze versleutelings sleutels ontsleutelen. Verschillende modellen van sleutel opslag worden ondersteund. Verderop in de volgende sectie wordt elk model uitvoerig besproken.

## <a name="data-encryption-models"></a>Gegevens versleutelings modellen

Het is belang rijk dat u begrijpt hoe de verschillende versleutelings modellen en hun voor-en nadelen essentieel zijn om te zien hoe de verschillende resource providers in azure versleuteling in rust implementeren. Deze definities worden gedeeld door alle resource providers in azure om een gemeen schappelijke taal en taxonomie te garanderen.

Er zijn drie scenario's voor versleuteling aan server zijde:

- Versleuteling aan de server zijde met door service beheerde sleutels
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - Micro soft beheert de sleutels
  - Volledige Cloud functionaliteit

- Versleuteling aan de server zijde met door de klant beheerde sleutels in Azure Key Vault
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - De besturings elementen van de klant worden via Azure Key Vault
  - Volledige Cloud functionaliteit

- Versleuteling aan de server zijde met door de klant beheerde sleutels op door de klant bestuurde hardware
  - Azure-resource providers voeren de bewerkingen voor versleuteling en ontsleuteling uit
  - Klant besturings elementen sleutels voor door de klant beheerde hardware
  - Volledige Cloud functionaliteit

Voor versleuteling aan de client zijde moet u rekening houden met het volgende:

- De gedecodeerde gegevens kunnen niet worden weer gegeven in Azure-Services
- Klanten beheren en bewaren sleutels on-premises (of in andere beveiligde winkels). Sleutels zijn niet beschikbaar voor Azure-Services
- Gereduceerde Cloud functionaliteit

De ondersteunde versleutelings modellen in Azure worden onderverdeeld in twee hoofd groepen: ' client versleuteling ' en ' versleuteling aan de server zijde ' zoals eerder beschreven. Onafhankelijk van de versleutelings methode voor het gebruikte rest model, raadt Azure-Services altijd het gebruik van een beveiligd Trans Port, zoals TLS of HTTPS, aan. Versleuteling in Trans Port moet daarom worden verholpen door het transport protocol en mag niet een belang rijke factor zijn bij het bepalen van de versleuteling in het rest model dat moet worden gebruikt.

### <a name="client-encryption-model"></a>Client versleutelings model

Het client versleutelings model verwijst naar de versleuteling die buiten de resource provider of Azure wordt uitgevoerd door de service of de toepassing die u aanroept. De versleuteling kan worden uitgevoerd door de service toepassing in azure of door een toepassing die wordt uitgevoerd in het Data Center van de klant. Bij het gebruik van dit versleutelings model ontvangt de Azure-resource provider een versleutelde blob van gegevens zonder de mogelijkheid om de gegevens op enigerlei wijze te ontsleutelen of toegang te geven tot de versleutelings sleutels. In dit model wordt het sleutel beheer uitgevoerd door de aanroepende service/toepassing en is deze ondoorzichtig voor de Azure-service.

![Client](./media/encryption-atrest/azure-security-encryption-atrest-fig2.png)

### <a name="server-side-encryption-model"></a>Versleutelings model aan server zijde

Versleutelings modellen aan de server zijde verwijzen naar versleuteling die wordt uitgevoerd door de Azure-service. In dat model voert de resource provider de bewerkingen versleutelen en ontsleutelen uit. Azure Storage kunnen bijvoorbeeld gegevens ontvangen in bewerkingen met tekst zonder opmaak en de versleuteling en ontsleuteling intern uitvoeren. De resource provider kan gebruikmaken van versleutelings sleutels die door micro soft of door de klant worden beheerd, afhankelijk van de ingestelde configuratie.

![Server](./media/encryption-atrest/azure-security-encryption-atrest-fig3.png)

### <a name="server-side-encryption-key-management-models"></a>Beheer modellen voor versleutelings sleutels aan server zijde

Elk van de versleuteling aan de server zijde bij rest modellen impliceert de onderscheidende kenmerken van sleutel beheer. Dit omvat waar en hoe versleutelings sleutels worden gemaakt en opgeslagen, evenals de toegangs modellen en de procedures voor sleutel rotatie.

#### <a name="server-side-encryption-using-service-managed-keys"></a>Versleuteling aan de server zijde met door service beheerde sleutels

Voor veel klanten is de essentiële vereiste om ervoor te zorgen dat de gegevens worden versleuteld wanneer deze in rust zijn. Versleuteling aan de server zijde met door service beheerde sleutels maakt dit model mogelijk door klanten toe te staan om de specifieke resource (opslag account, SQL-data base, enzovoort) te markeren voor versleuteling en alle aspecten van het sleutel beheer, zoals sleutel uitgifte, rotatie en back-up naar micro soft, te laten staan . De meeste Azure-Services die ondersteuning bieden voor versleuteling in rust, bieden doorgaans ondersteuning voor dit model van het beheer van de versleutelings sleutels naar Azure. De Azure-resource provider maakt de sleutels, plaatst deze in beveiligde opslag en haalt ze op wanneer dat nodig is. Dit betekent dat de service volledige toegang heeft tot de sleutels en dat de service volledige controle heeft over het beheer van de referentie levenscyclus.

![bijgehouden](./media/encryption-atrest/azure-security-encryption-atrest-fig4.png)

Versleuteling aan de server zijde met door service beheerde sleutels maakt daarom snel de nood zaak om versleuteling op rest te hebben met lage overhead voor de klant. Als er een klant beschikbaar is, wordt doorgaans de Azure Portal voor het doel abonnement en de resource provider geopend en wordt een vak gecontroleerd waarin wordt aangegeven dat de gegevens moeten worden versleuteld. In sommige bron beheer server versleuteling met door service beheerde sleutels is standaard ingeschakeld.

Versleuteling aan de server zijde met door micro soft beheerde sleutels betekent dat de service volledige toegang heeft tot de sleutels op te slaan en te beheren. Hoewel sommige klanten de sleutels willen beheren omdat ze ervan overtuigd zijn dat ze betere beveiliging krijgen, moeten de kosten en risico's die zijn gekoppeld aan een oplossing voor aangepaste sleutel opslag worden overwogen bij het evalueren van dit model. In veel gevallen kan een organisatie bepalen dat resource beperkingen of Risico's van een on-premises oplossing groter zijn dan het risico van Cloud beheer van de versleuteling bij rust sleutels.  Dit model is echter mogelijk niet voldoende voor organisaties die vereisten hebben om het maken of de levens cyclus van de versleutelings sleutels te controleren of om verschillende mede werkers te laten beheren van de versleutelings sleutels van een service dan het beheer van de service (dat wil zeggen, schei ding van sleutel beheer van het algehele beheer model voor de service).

##### <a name="key-access"></a>Sleutel toegang

Wanneer versleuteling aan de server zijde met door service beheerde sleutels wordt gebruikt, worden de sleutel maken, opslag en toegang tot de service allemaal beheerd door de service. Normaal gesp roken worden de gegevens versleutelings sleutels in een archief opgeslagen die dicht bij de gegevens staan en snel beschikbaar en toegankelijk zijn wanneer de sleutel versleutelings sleutels worden opgeslagen in een veilige interne opslag.

**Voordelen**

- Eenvoudige installatie
- Micro soft beheert de belangrijkste draaiing, back-up en redundantie
- De klant heeft niet de kosten die zijn gekoppeld aan de implementatie of het risico van een aangepast sleutel beheer schema.

**Nadelen**

- Geen klant controle over de versleutelings sleutels (sleutel specificatie, levens cyclus, intrekken, enz.)
- Het is niet mogelijk om sleutel beheer te scheiden van het algehele beheer model voor de service

#### <a name="server-side-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Versleuteling aan de server zijde met door de klant beheerde sleutels in Azure Key Vault

Voor scenario's waarbij de vereiste is om de gegevens in rust te versleutelen en de versleutelings sleutels te beheren, kunnen klanten versleuteling aan de server zijde gebruiken met door de klant beheerde sleutels in Key Vault. Sommige services kunnen alleen de sleutel voor de sleutel van de basis sleutel opslaan in Azure Key Vault en de versleutelde gegevens versleutelings sleutel opslaan op een interne locatie dichter bij de gegevens. In dat geval kunnen klanten hun eigen sleutels naar Key Vault (Bring Your Own Key BYOK) brengen, of nieuwe codes genereren en ze gebruiken om de gewenste resources te versleutelen. Terwijl de resource provider de bewerkingen voor versleuteling en ontsleuteling uitvoert, wordt de geconfigureerde sleutel versleutelings sleutel gebruikt als basis sleutel voor alle versleutelings bewerkingen.

Verlies van sleutel versleutelings sleutels betekent gegevens verlies. Daarom mogen sleutels niet worden verwijderd. Er moet een back-up van sleutels worden gemaakt wanneer u deze maakt of roteert. [Zacht verwijderen](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) moet zijn ingeschakeld op een kluis die sleutel versleutelings sleutels opslaat. In plaats van een sleutel te verwijderen, stelt u ingeschakeld in op ONWAAR of stelt u de verval datum in.

##### <a name="key-access"></a>Sleutel toegang

Het versleutelings model aan de server zijde met door de klant beheerde sleutels in Azure Key Vault omvat de service die toegang heeft tot de sleutels voor versleutelen en ontsleutelen, indien nodig. Versleuteling bij rest sleutels wordt via een toegangscontrole beleid toegankelijk gemaakt voor een service. Dit beleid verleent de service identiteit toegang om de sleutel te ontvangen. Een Azure-service die namens een gekoppeld abonnement wordt uitgevoerd, kan worden geconfigureerd met een identiteit in dat abonnement. De service kan Azure Active Directory verificatie uitvoeren en een verificatie token ontvangen dat zichzelf identificeert als die service namens het abonnement. Dit token kan vervolgens worden weer gegeven aan Key Vault om een sleutel te verkrijgen waaraan toegang is verleend.

Voor bewerkingen die gebruikmaken van versleutelings sleutels, kan aan een service-identiteit toegang worden verleend voor een van de volgende bewerkingen: ontsleutelen, versleutelen, sleutel uitpakken, wrapKey, verifiëren, ondertekenen, ophalen, weer geven, bijwerken, maken, importeren, verwijderen, back-up en herstellen.

Voor het verkrijgen van een sleutel voor gebruik bij het versleutelen of ontsleutelen van gegevens in rust de service-identiteit die door het service-exemplaar van de resource manager moet worden uitgevoerd sleutel uitpakken (om de sleutel voor ontsleuteling op te halen) en WrapKey (om een sleutel in te voegen in de sleutel kluis bij het maken van een nieuwe sleutel).

>[!NOTE]
>Zie de pagina uw sleutel kluis beveiligen in de [Azure Key Vault documentatie](../../key-vault/key-vault-secure-your-key-vault.md)voor meer informatie over Key Vault autorisatie.

**Voordelen**

- Volledige controle over de gebruikte sleutels: coderings sleutels worden beheerd in de Key Vault van de klant onder het besturings element van de klant.
- Mogelijkheid om meerdere services te versleutelen op één Master
- Kan sleutel beheer scheiden van het algehele beheer model voor de service
- Kan de locatie van de service en de sleutel in verschillende regio's bepalen

**Nadelen**

- De klant heeft volledige verantwoordelijkheid voor belangrijkste toegangs beheer
- De klant heeft volledige verantwoordelijkheid voor het beheer van de levens cyclus van sleutels
- Extra installatie & configuratie overhead

#### <a name="server-side-encryption-using-customer-managed-keys-in-customer-controlled-hardware"></a>Versleuteling aan de server zijde met door de klant beheerde sleutels in door de klant bestuurde hardware

Met sommige Azure-Services wordt de host van uw eigen sleutel (HYOK) Key Management model ingeschakeld. Deze beheer modus is handig in scenario's waarin de gegevens in rust moeten worden versleuteld en de sleutels in een eigen opslag plaats buiten de controle van micro soft moeten worden beheerd. In dit model moet de service de sleutel ophalen van een externe site. De prestaties en de beschik baarheid van de garantie worden beïnvloed en de configuratie is ingewik kelder. Omdat de service echter toegang heeft tot de DEK tijdens de versleutelings-en ontsleutelings bewerkingen, zijn de algemene beveiligings garanties van dit model vergelijkbaar met wanneer de sleutels door de klant worden beheerd in Azure Key Vault.  Als gevolg hiervan is dit model niet geschikt voor de meeste organisaties, tenzij ze specifieke vereisten voor sleutel beheer hebben. Als gevolg van deze beperkingen bieden de meeste Azure-Services geen ondersteuning voor versleuteling aan de server zijde met behulp van door de server beheerde sleutels in door de klant bewaakte hardware.

##### <a name="key-access"></a>Sleutel toegang

Wanneer versleuteling aan de server zijde met door service beheerde sleutels in door de klant bewaakte hardware wordt gebruikt, worden de sleutels op een door de klant geconfigureerd systeem beheerd. Azure-Services die dit model ondersteunen, bieden een manier om een beveiligde verbinding tot stand te brengen met een door de klant geleverd sleutel archief.

**Voordelen**

- Volledige controle over de gebruikte basis sleutel: coderings sleutels worden beheerd door een door de klant verschafte winkel
- Mogelijkheid om meerdere services te versleutelen op één Master
- Kan sleutel beheer scheiden van het algehele beheer model voor de service
- Kan de locatie van de service en de sleutel in verschillende regio's bepalen

**Nadelen**

- Volledige verantwoordelijkheid voor sleutel opslag, beveiliging, prestaties en beschik baarheid
- Volledige verantwoordelijkheid voor belangrijkste toegangs beheer
- Volledige verantwoordelijkheid voor het beheer van de levens cyclus van sleutels
- Aanzienlijke kosten voor installatie, configuratie en onderhouds werkzaamheden
- Toename van de beschik baarheid van het netwerk tussen het Data Center van de klant en Azure-data centers.

## <a name="encryption-at-rest-in-microsoft-cloud-services"></a>Versleuteling in rust in micro soft-Cloud Services

Microsoft Cloud Services worden in alle drie Cloud modellen gebruikt: IaaS, PaaS, SaaS. Hieronder ziet u voor beelden van hoe ze in elk model passen:

- Software Services, aangeduid als software als een server of SaaS, die de toepassing heeft die door de Cloud wordt verschaft, zoals Office 365.
- Platform services die klanten gebruikmaken van de cloud in hun toepassingen, met behulp van de Cloud voor zaken als opslag, analyses en service bus-functionaliteit.
- Infrastructuur services of Infrastructure as a Service (IaaS) waarin de klant besturings systemen en toepassingen implementeert die worden gehost in de Cloud en mogelijk gebruikmaken van andere Cloud Services.

### <a name="encryption-at-rest-for-saas-customers"></a>Versleuteling op rest voor SaaS-klanten

SaaS-klanten (Software as a Service) hebben doorgaans versleuteling op rest ingeschakeld of beschikbaar in elke service. Office 365 biedt verschillende opties voor klanten om versleuteling op rest te verifiëren of in te scha kelen. Zie [versleuteling in office 365](https://docs.microsoft.com/office365/securitycompliance/encryption)voor meer informatie over Office 365-Services.

### <a name="encryption-at-rest-for-paas-customers"></a>Versleuteling bij rest voor PaaS-klanten

De gegevens van platform as a Service (PaaS) bevinden zich doorgaans in een opslag service, zoals Blob Storage, maar kunnen ook worden opgeslagen in de cache van de toepassings Execution omgeving, zoals een virtuele machine. Bekijk de onderstaande tabel voor de opslag-en toepassings platformen die u gebruikt om de versleutelings opties voor de beschik bare rest te bekijken.

### <a name="encryption-at-rest-for-iaas-customers"></a>Versleuteling bij rest voor IaaS-klanten

IaaS-klanten (Infrastructure as a Service) kunnen een groot aantal services en toepassingen gebruiken. IaaS Services kunnen versleuteling in de rest inschakelen in hun door Azure gehoste virtuele machines en Vhd's met behulp van Azure Disk Encryption.

#### <a name="encrypted-storage"></a>Versleutelde opslag

Net als PaaS kunnen IaaS-oplossingen gebruikmaken van andere Azure-Services die gegevens versleuteld bewaren op rest. In dergelijke gevallen kunt u de versleuteling inschakelen op rest-ondersteuning, zoals wordt geboden door elke verbruikte Azure-service. In de onderstaande tabel worden de belangrijkste opslag-, service-en toepassings platforms en het versleutelings model op rest-ondersteuning opgesomd. 

#### <a name="encrypted-compute"></a>Versleutelde compute

Alle Managed Disks, moment opnamen en installatie kopieën worden versleuteld met behulp van Storage Service Encryption met een door een service beheerde sleutel. Een meer complete versleuteling bij rest-oplossing zorgt ervoor dat de gegevens niet in een niet-versleutelde vorm worden bewaard. Tijdens het verwerken van de gegevens op een virtuele machine kunnen gegevens worden bewaard in het Windows-pagina bestand of het Linux-wissel bestand, een crash dump of een toepassings logboek. Om ervoor te zorgen dat deze gegevens in rust worden versleuteld, kunnen IaaS-toepassingen gebruikmaken van Azure Disk Encryption op een virtuele machine met Azure IaaS (Windows of Linux) en virtuele schijf.

#### <a name="custom-encryption-at-rest"></a>Aangepaste versleuteling bij rest

Het wordt aanbevolen om waar mogelijk IaaS toepassingen Azure Disk Encryption en versleuteling te gebruiken bij rest opties die worden verschaft door alle verbruikte Azure-Services. In sommige gevallen, zoals onregelmatige versleutelings vereisten of opslag op basis van Azure, moet een ontwikkelaar van een IaaS-toepassing mogelijk versleuteling op rest zelf implementeren. Ontwikkel aars van IaaS-oplossingen kunnen beter integreren met Azure-beheer en klant verwachtingen door gebruik te maken van bepaalde Azure-onderdelen. Met name moeten ontwikkel aars de Azure Key Vault-service gebruiken voor het leveren van beveiligde sleutel opslag en hun klanten een consistente sleutel beheer opties bieden met die van de meeste services van het Azure-platform. Daarnaast moeten aangepaste oplossingen door Azure beheerde service-identiteiten worden gebruikt om service accounts toegang te geven tot versleutelings sleutels. Voor informatie over ontwikkel aars over Azure Key Vault en beheerde service-identiteiten raadpleegt u hun respectieve Sdk's.

## <a name="azure-resource-providers-encryption-model-support"></a>Ondersteuning voor het versleutelings model van Azure-resource providers

Microsoft Azure Services bieden ondersteuning voor een of meer van de versleuteling bij rest-modellen. Voor sommige services zijn echter een of meer van de versleutelings modellen niet van toepassing. Voor services die door de klant beheerde sleutel scenario's ondersteunen, kunnen ze slechts een subset van de sleutel typen ondersteunen die Azure Key Vault ondersteunt voor sleutel versleutelings sleutels. Daarnaast kunnen services de ondersteuning voor deze scenario's en sleutel typen in verschillende schema's vrijgeven. In deze sectie wordt de versleuteling op rest-ondersteuning beschreven op het moment van schrijven voor elk van de belangrijkste Azure-Services voor gegevens opslag.

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

Elke klant die gebruikmaakt van Azure Infrastructure as a Service (IaaS)-functies, kan versleutelen op rest voor hun IaaS Vm's en schijven via Azure Disk Encryption. Zie de [Azure Disk Encryption-documentatie](../azure-security-disk-encryption-overview.md)voor meer informatie over Azure Disk Encryption.

#### <a name="azure-storage"></a>Azure-opslag

Alle Azure Storage services (Blob-opslag, wachtrij opslag, tabel opslag en Azure Files) ondersteunen server versleuteling in rust. Sommige services ondersteunen daarnaast door de klant beheerde sleutels en versleuteling aan de client zijde. 

- Aan de server zijde: alle Azure Storage services maken server zijde versleuteling mogelijk door gebruik te maken van door de service beheerde sleutels, die transparant zijn voor de toepassing. Zie [Azure Storage-service versleuteling voor Data-at-rest](../../storage/common/storage-service-encryption.md)voor meer informatie. Azure Blob-opslag en Azure Files ondersteunen ook RSA 2048-bits door de klant beheerde sleutels in Azure Key Vault. Zie [Storage service Encryption het gebruik van door de klant beheerde sleutels in azure Key Vault](../../storage/common/storage-encryption-keys-portal.md)voor meer informatie.
- Client zijde: Azure-blobs,-tabellen en-wacht rijen ondersteunen versleuteling aan de client zijde. Wanneer u versleuteling aan de client zijde gebruikt, versleutelt klanten de gegevens en uploadt de gegevens als een versleutelde blob. Sleutel beheer wordt uitgevoerd door de klant. Zie [versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md)voor meer informatie.

#### <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database biedt momenteel ondersteuning voor versleuteling in rust voor micro soft-beheerde service-en versleutelings scenario's aan de client zijde.

Ondersteuning voor Server versleuteling wordt momenteel geboden via de SQL-functie met de naam Transparent Data Encryption. Zodra een Azure SQL Database klant wordt ingeschakeld, wordt er automatisch een TDE-sleutel gemaakt en beheerd. Versleuteling in rust kan worden ingeschakeld op Data Base-en server niveau. Vanaf 2017 juni is [transparent Data Encryption (TDE)](https://msdn.microsoft.com/library/bb934049.aspx) standaard ingeschakeld voor nieuw gemaakte data bases. Azure SQL Database ondersteunt door de klant beheerde sleutels van RSA 2048-bits in Azure Key Vault. Zie [transparent Data Encryption met Bring your own Key ondersteuning voor Azure SQL database en Data Warehouse](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-byok-azure-sql?view=azuresqldb-current)voor meer informatie.

Versleuteling aan client zijde van Azure SQL Database gegevens wordt ondersteund via de [Always encrypted](https://msdn.microsoft.com/library/mt163865.aspx) -functie. Always Encrypted gebruikt een sleutel die door de client is gemaakt en opgeslagen. Klanten kunnen de hoofd sleutel opslaan in een Windows-certificaat archief, Azure Key Vault of een lokale hardware Security module. Met behulp van SQL Server Management Studio kiezen SQL-gebruikers welke sleutel ze willen gebruiken om de kolom te versleutelen.

#### <a name="encryption-model-and-key-management-table"></a>Versleutelings model en sleutel beheer tabel

|                                  |                    | **Versleutelings model en sleutel beheer** |                    |
|----------------------------------|--------------------|-----------------------------------------|--------------------|
|                                  | **Server-side met door service beheerde sleutel**     | **Server-zijde met door de klant beheerde sleutel**             | **Client-side met door client beheerde sleutel**      |
| **AI en Machine Learning**      |                    |                    |                    |
| Azure Search                     | Ja                | Preview            | -                  |
| Azure Machine Learning   | Ja                | -                  | -                  |
| Azure Machine Learning Studio    | Ja                | Preview, RSA 2048-bits | -               |
| Power BI                         | Ja                | Preview, RSA 2048-bits | -                  |
| **Analytische gegevens**                    |                    |                    |                    |
| Azure Stream Analytics           | Ja                | -                  | -                  |
| Event Hubs                       | Ja                | Preview, alle RSA-lengten. | -                  |
| Azure Analysis Services          | Ja                | -                  | -                  |
| Azure Data Catalog               | Ja                | -                  | -                  |
| Apache Kafka in azure HDInsight  | Ja                | Alle RSA-lengten.   | -                  |
| Azure Data Explorer              | Ja                | -                  | -                  |
| Azure Data Factory               | Ja                | Ja                | -                  |
| Azure Data Lake Store            | Ja                | Ja, RSA 2048-bits  | -                  |
| **Containers**                   |                    |                    |                    |
| Azure Kubernetes Service         | Ja                | -                  | -                  |
| Container Registry               | Ja                | -                  | -                  |
| **Compute**                      |                    |                    |                    |
| Virtuele machines                 | Ja                | Ja, RSA 2048-bits  | -                  |
| Schaalset voor virtuele machines        | Ja                | Ja, RSA 2048-bits  | -                  |
| SAP HANA                         | Ja                | Ja, RSA 2048-bits  | -                  |
| **Databases**                    |                    |                    |                    |
| SQL Server op Virtual Machines   | Ja                | Ja, RSA 2048-bits  | Ja                |
| Azure SQL Database               | Ja                | Ja, RSA 2048-bits  | Ja                |
| Azure SQL Database voor MariaDB   | Ja                | -                  | -                  |
| Azure SQL Database voor MySQL     | Ja                | -                  | -                  |
| Azure SQL Database voor PostgreSQL | Ja                | -                  | -                  |
| Azure SQL Data Warehouse         | Ja                | Ja, RSA 2048-bits  | Ja                |
| SQL Server Stretch Database      | Ja                | Ja, RSA 2048-bits  | Ja                |
| Table Storage                    | Ja                | -                  | Ja                |
| Azure Cosmos DB                  | Ja                | -                  | -                  |
| **DevOps**                       |                    |                    |                    |
| Azure DevOps                     | Ja                | -                  | Ja                |
| Azure Repos                      | Ja                | -                  | Ja                |
| **Identity**                     |                    |                    |                    |
| Azure Active Directory           | Ja                | -                  | -                  |
| Azure Active Directory Domain Services | Ja          | Ja, RSA 2048-bits  | -                  |
| **Opneming**                  |                    |                    |                    |
| Service Bus                      | Ja                | -                  | Ja                |
| Event Grid                       | Ja                | -                  | -                  |
| API Management                   | Ja                | -                  | -                  |
| **IoT-Services**                 |                    |                    |                    |
| IoT Hub                          | Ja                | -                  | Ja                |
| **Beheer en governance**    |                    |                    |                    |
| Azure Site Recovery              | Ja                | Ja, RSA 2048-bits  | Ja                |
| **Tussenliggend**                        |                    |                    |                    |
| Media Services                   | Ja                | -                  | Ja                |
| **Opslag**                      |                    |                    |                    |
| Blob Storage                     | Ja                | Ja, RSA 2048-bits  | Ja                |
| Disk Storage                     | Ja                | -                  | -                  |
| Beheerde schijfopslag             | Ja                | -                  | -                  |
| File Storage                     | Ja                | Ja, RSA 2048-bits  | -                  |
| Queue Storage                    | Ja                | -                  | Ja                |
| Avere vFXT                       | Ja                | -                  | -                  |
| Azure NetApp Files               | Ja                | -                  | -                  |
| Archiefopslag                  | Ja                | Ja, RSA 2048-bits  | -                  |
| StorSimple                       | Ja                | Ja, RSA 2048-bits  | Ja                |
| Azure Backup                     | Ja                | -                  | Ja                |
| Data Box                         | Ja                | -                  | Ja                |

## <a name="conclusion"></a>Conclusie

De beveiliging van klant gegevens die zijn opgeslagen in Azure-Services is van cruciaal belang voor micro soft. Alle door Azure gehoste services zijn doorgevoerd om versleuteling te bieden met behulp van rest-opties. Foundational Services, zoals Azure Storage, Azure SQL Database en Key Analytics en intelligence-services, bieden al versleuteling op rest opties. Sommige van deze services ondersteunen door de klant beheerde sleutels en versleuteling aan de client zijde, evenals door de service beheerde sleutels en versleuteling. Microsoft Azure Services zijn breed uitgebreid met de beschik baarheid van de rest en er zijn nieuwe opties gepland voor preview en algemene Beschik baarheid in de komende maanden.
