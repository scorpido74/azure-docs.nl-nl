---
title: Beperkte toegang verlenen tot gegevens met gedeelde toegangshandtekeningen (SAS)
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van sas-handtekeningen (Shared Access Signatures) om toegang tot Azure Storage-bronnen te delegeren, waaronder blobs, wachtrijen, tabellen en bestanden.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7a5967f52a187fe289c6fb1ca72af2d5fd17f010
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255234"
---
# <a name="grant-limited-access-to-azure-storage-resources-using-shared-access-signatures-sas"></a>Beperkte toegang verlenen tot Azure Storage-bronnen met behulp van gedeelde toegangshandtekeningen (SAS)

Een gedeelde toegangshandtekening (SAS) biedt veilige gedelegeerde toegang tot bronnen in uw opslagaccount zonder dat dit ten koste gaat van de beveiliging van uw gegevens. Met een SAS hebt u gedetailleerde controle over hoe een client toegang heeft tot uw gegevens. U onder andere bepalen tot welke resources de client toegang heeft, welke machtigingen ze op deze resources hebben en hoe lang de SAS geldig is.

## <a name="types-of-shared-access-signatures"></a>Typen handtekeningen voor gedeelde toegang

Azure Storage ondersteunt drie typen gedeelde toegangshandtekeningen:

- **Gebruikersdelegatie SAS.** Een Gebruikersdelegatie SAS is beveiligd met Azure Active Directory (Azure AD)-referenties en ook met de machtigingen die zijn opgegeven voor de SAS. Een SAS voor gebruikersdelegatie is alleen van toepassing op Blob-opslag.

    Zie [SAS voor gebruikersdelegatie (REST API) maken voor](/rest/api/storageservices/create-user-delegation-sas)meer informatie over de SAS voor gebruikersdelegatie.

- **Service SAS.** Een service SAS is beveiligd met de opslagaccountsleutel. Een service SAS geeft de toegang tot een bron in slechts één van de Azure Storage-services: Blob-opslag, wachtrijopslag, tabelopslag of Azure-bestanden. 

    Zie [Een service SAS (REST API) maken](/rest/api/storageservices/create-service-sas)voor meer informatie over de service SAS.

- **Account SAS.** Een account SAS is beveiligd met de opslagaccountsleutel. Een account-SAS delegeert toegang tot resources in een of meer van de opslagservices. Alle bewerkingen die beschikbaar zijn via een service of gebruikersdelegatie SAS zijn ook beschikbaar via een account SAS. Bovendien u met de SAS-account toegang tot bewerkingen delegeren die van toepassing zijn op het niveau van de service, zoals **Service-eigenschappen opmaken/instellen** en **Servicestatistieken uitvoeren.** U kunt ook toegang tot het lezen, schrijven en verwijderen van bewerkingen delegeren voor blobcontainers, tabellen, wachtrijen en bestandsshares die niet zijn toegestaan bij een service-SAS. 

    Voor meer informatie over het account SAS, [Maak een account SAS (REST API)](/rest/api/storageservices/create-account-sas).

> [!NOTE]
> Microsoft raadt u aan om Azure AD-referenties waar mogelijk te gebruiken als best practice voor beveiliging, in plaats van de accountsleutel te gebruiken, die gemakkelijker kan worden gecompromitteerd. Wanneer voor uw toepassingsontwerp handtekeningen voor gedeelde toegang vereist voor toegang tot Blob-opslag, gebruikt u Azure AD-referenties om indien mogelijk een SAS voor gebruikersdelegatie sAS te maken voor superieure beveiliging.

Een handtekening met gedeelde toegang kan een van de twee formulieren aannemen:

- **Ad hoc SAS:** Wanneer u een ad hoc SAS maakt, worden de begintijd, de vervaldatum en de machtigingen voor de SAS allemaal opgegeven in de SAS URI (of impliciet, als de begintijd wordt weggelaten). Elk type SAS kan een ad hoc SAS zijn.
- **Service SAS met opgeslagen toegangsbeleid:** Een opgeslagen toegangsbeleid wordt gedefinieerd op een resourcecontainer, wat een blobcontainer, tabel, wachtrij of bestandsshare kan zijn. Het beleid voor opgeslagen toegang kan worden gebruikt om beperkingen voor een of meer handtekeningen voor gedeelde servicete beheren. Wanneer u een service-SAS koppelt aan een opgeslagen toegangsbeleid, neemt de SAS de&mdash;&mdash;beperkingen over de begintijd, vervaldatum en machtigingen die zijn gedefinieerd voor het beleid voor opgeslagen toegang.

> [!NOTE]
> Een gebruikersdelegatie SAS of een account SAS moet een ad hoc SAS zijn. Het beleid voor opgeslagen toegang wordt niet ondersteund voor de sas of de accountSAS.

## <a name="how-a-shared-access-signature-works"></a>Hoe een handtekening met gedeelde toegang werkt

Een gedeelde toegangshandtekening is een ondertekende URI die verwijst naar een of meer opslagbronnen en een token bevat dat een speciale set queryparameters bevat. Het token geeft aan hoe de resources toegankelijk zijn voor de client. Een van de queryparameters, de handtekening, is opgebouwd uit de SAS-parameters en ondertekend met de sleutel die is gebruikt om de SAS te maken. Deze handtekening wordt door Azure Storage gebruikt om toegang tot de opslagbron te autoriseren.

### <a name="sas-signature"></a>SAS-handtekening

U een SAS op twee manieren ondertekenen:

- Met een *gebruikersdelegatiesleutel* die is gemaakt met Azure AD-referenties (Azure Directory). Een gebruikersdelegatie SAS is ondertekend met de gebruikersdelegatiesleutel.

    Als u de gebruikersdelegatiesleutel wilt krijgen en de SAS wilt maken, moet aan een Azure AD-beveiligingsprincipal een RBAC-rol (Role-based access control) worden toegewezen die de actie **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** bevat. Zie [SAS (REST API) maken](/rest/api/storageservices/create-user-delegation-sas)voor gedetailleerde informatie over RBAC-rollen met machtigingen om de gebruikersdelegatiesleutel te krijgen.

- Met de opslagaccountsleutel. Zowel een service SAS als een account SAS worden ondertekend met de opslagaccountsleutel. Als u een SAS wilt maken die is ondertekend met de accountsleutel, moet een toepassing toegang hebben tot de accountsleutel.

### <a name="sas-token"></a>SAS-token

Het SAS-token is een tekenreeks die u aan de clientzijde genereert, bijvoorbeeld met behulp van een van de Azure Storage-clientbibliotheken. Het SAS-token wordt op geen enkele manier bijgehouden door Azure Storage. U een onbeperkt aantal SAS-tokens aan de clientzijde maken. Nadat u een SAS hebt gemaakt, u deze distribueren naar clienttoepassingen waarvoor toegang nodig is tot bronnen in uw opslagaccount.

Wanneer een clienttoepassing een SAS URI aan Azure Storage biedt als onderdeel van een aanvraag, controleert de service de SAS-parameters en -handtekening om te controleren of deze geldig is voor het toestaan van de aanvraag. Als de service controleert of de handtekening geldig is, is de aanvraag geautoriseerd. Anders wordt het verzoek geweigerd met foutcode 403 (Verboden).

Hier is een voorbeeld van een service SAS URI, met de resource URI en het SAS-token:

![Onderdelen van een service SAS URI](./media/storage-sas-overview/sas-storage-uri.png)

## <a name="when-to-use-a-shared-access-signature"></a>Wanneer een handtekening voor gedeelde toegang gebruiken

Gebruik een SAS wanneer u veilige toegang wilt bieden tot bronnen in uw opslagaccount aan elke client die anders geen machtigingen voor deze bronnen heeft.

Een veelvoorkomend scenario waarbij een SAS nuttig is, is een service waarbij gebruikers hun eigen gegevens lezen en schrijven naar uw opslagaccount. In een scenario waarin een opslagaccount gebruikersgegevens opslaat, zijn er twee typische ontwerppatronen:

1. Clients uploaden en downloaden gegevens via een front-endproxyservice, waarmee verificatie wordt uitgevoerd. Deze front-end proxyservice heeft het voordeel dat validatie van bedrijfsregels mogelijk is, maar voor grote hoeveelheden gegevens of transacties met een hoog volume kan het maken van een service die kan worden geschaald om aan de vraag te voldoen, duur of moeilijk zijn.

   ![Scenariodiagram: Front-end proxyservice](./media/storage-sas-overview/sas-storage-fe-proxy-service.png)

1. Met een eenvoudige service wordt de client indien nodig geverifieerd en vervolgens een SAS gegenereerd. Zodra de clienttoepassing de SAS ontvangt, hebben ze rechtstreeks toegang tot opslagaccountbronnen met de machtigingen die door de SAS zijn gedefinieerd en voor het interval dat door de SAS is toegestaan. Dankzij de SAS hoeven alle gegevens niet via de front-endproxyservice te worden doorgestuurd.

   ![Scenariodiagram: SAS-serviceprovider](./media/storage-sas-overview/sas-storage-provider-service.png)

Veel real-world diensten kunnen gebruik maken van een hybride van deze twee benaderingen. Sommige gegevens kunnen bijvoorbeeld worden verwerkt en gevalideerd via de front-endproxy, terwijl andere gegevens worden opgeslagen en/of rechtstreeks worden gelezen via SAS.

Bovendien is een SAS vereist om toegang tot het bronobject in een kopieerbewerking in bepaalde scenario's te autoriseren:

- Wanneer u een blob kopieert naar een andere blob die zich in een ander opslagaccount bevindt, moet u een SAS gebruiken om toegang tot de bronblob te autoriseren. U optioneel een SAS gebruiken om ook toegang tot de doelblob te autoriseren.
- Wanneer u een bestand kopieert naar een ander bestand dat zich in een ander opslagaccount bevindt, moet u een SAS gebruiken om de toegang tot het bronbestand te autoriseren. U optioneel een SAS gebruiken om ook toegang tot het doelbestand te autoriseren.
- Wanneer u een blob kopieert naar een bestand of een bestand naar een blob, moet u een SAS gebruiken om toegang tot het bronobject te autoriseren, zelfs als de bron- en doelobjecten zich in hetzelfde opslagaccount bevinden.

## <a name="best-practices-when-using-sas"></a>Best practices bij gebruik van SAS

Wanneer u gedeelde toegangshandtekeningen in uw toepassingen gebruikt, moet u zich bewust zijn van twee potentiële risico's:

- Als een SAS is gelekt, kan deze worden gebruikt door iedereen die het verkrijgt, wat mogelijk uw opslagaccount kan compromitteren.
- Als een SAS die aan een clienttoepassing wordt geleverd verloopt en de toepassing geen nieuwe SAS uit uw service kan ophalen, kan de functionaliteit van de toepassing worden belemmerd.

De volgende aanbevelingen voor het gebruik van handtekeningen voor gedeelde toegang kunnen helpen deze risico's te beperken:

- **Gebruik ALTIJD HTTPS** om een SAS te maken of te distribueren. Als een SAS wordt doorgegeven via HTTP en onderschept, een aanvaller het uitvoeren van een man-in-the-middle aanval is in staat om de SAS te lezen en vervolgens gebruiken net zoals de beoogde gebruiker zou kunnen hebben, potentieel compromitterende gevoelige gegevens of waardoor voor gegevens corruptie door de kwaadwillende gebruiker.
- **Gebruik indien mogelijk een SAS voor gebruikersdelegatie.** Een gebruikersdelegatie SAS biedt superieure beveiliging voor een service SAS of een account SAS. Een SAS voor gebruikersdelegatie is beveiligd met Azure AD-referenties, zodat u uw accountsleutel niet met uw code hoeft op te slaan.
- **Heb een herroepingsplan voor een SAS.** Zorg ervoor dat u bereid bent om te reageren als een SAS is aangetast.
- **Definieer een opgeslagen toegangsbeleid voor een service-SAS.** Opgeslagen toegangsbeleid geeft u de mogelijkheid om machtigingen voor een service SAS in te trekken zonder de opslagaccountsleutels opnieuw te hoeven genereren. Stel de vervaldatum op deze zeer ver in de toekomst (of oneindig) en zorg ervoor dat het regelmatig wordt bijgewerkt om het verder te verplaatsen naar de toekomst.
- **Gebruik vervaldatums op korte termijn op een ad hoc SAS-service SAS of account SAS.** Op deze manier, zelfs als een SAS wordt aangetast, is het alleen geldig voor een korte tijd. Deze praktijk is vooral belangrijk als u niet verwijzen naar een opgeslagen toegangsbeleid. De vervaltijden op korte termijn beperken ook de hoeveelheid gegevens die naar een blob kan worden geschreven door de beschikbare tijd om er naar te uploaden te beperken.
- **Laat klanten de SAS automatisch vernieuwen indien nodig.** Klanten moeten de SAS ruim voor het verstrijken vernieuwen, zodat er tijd is voor nieuwe pogingen als de service die de SAS levert niet beschikbaar is. Als het de bedoeling is dat uw SAS wordt gebruikt voor een klein aantal onmiddellijke, kortstondige bewerkingen die naar verwachting binnen de vervaldatum zullen worden voltooid, kan dit overbodig zijn omdat de SAS naar verwachting niet zal worden verlengd. Echter, als u een klant hebt die routinematig verzoeken via SAS doet, dan komt de mogelijkheid van expiratie in het spel. De belangrijkste overweging is om de noodzaak voor de SAS van korte duur (zoals eerder vermeld) in evenwicht te brengen met de noodzaak om ervoor te zorgen dat de klant vroeg om verlenging vroeg genoeg (om verstoring te voorkomen als gevolg van de SAS afloopt voorafgaand aan succesvolle verlenging).
- **Wees voorzichtig met SAS begintijd.** Als u de begintijd voor een SAS instelt op **nu,** dan als gevolg van klokscheefheid (verschillen in huidige tijd volgens verschillende machines), kunnen storingen de eerste paar minuten met tussenpozen worden waargenomen. Stel in het verleden de starttijd in op ten minste 15 minuten. Of stel het helemaal niet in, waardoor het in alle gevallen onmiddellijk geldig is. Hetzelfde geldt over het algemeen voor de vervaldatum en vergeet niet dat u tot 15 minuten klok scheeftrekken in beide richtingen op elk verzoek. Voor clients die vóór 2012-02-12 een REST-versie gebruiken, is de maximale duur voor een SAS die niet verwijst naar een opgeslagen toegangsbeleid 1 uur en geen beleid dat een langere termijn opgeeft dan dat.
- **Wees voorzichtig met de SAS datetime-indeling.** Als u de begintijd en/of het verstrijken van een SAS instelt, moet u voor sommige hulpprogramma's (bijvoorbeeld voor het opdrachtregelhulpprogramma AzCopy) de datumtijdindeling '+%Y-%m-%dT%H:%M:%SZ' zijn, met name met inbegrip van de seconden om het SAS-token te laten werken.  
- **Wees specifiek met de bron die moet worden geopend.** Een best practice voor beveiliging is om een gebruiker de minimaal vereiste bevoegdheden te bieden. Als een gebruiker alleen leestoegang tot één entiteit nodig heeft, geeft u hem of haar leestoegang tot die ene entiteit en hebt hij geen toegang tot alle entiteiten. Dit helpt ook de schade te beperken als een SAS wordt aangetast omdat de SAS minder stroom in de handen van een aanvaller heeft.
- **Begrijp dat uw account in rekening wordt gebracht voor elk gebruik, ook via een SAS.** Als u schrijftoegang tot een blob geeft, kan een gebruiker ervoor kiezen om een blob van 200 GB te uploaden. Als je ze ook leestoegang hebt gegeven, kunnen ze ervoor kiezen om het 10 keer te downloaden, waardoor je 2 TB aan uitweekosten maakt. Nogmaals, bieden beperkte machtigingen om te helpen de potentiële acties van kwaadwillende gebruikers te beperken. Gebruik kortstondige SAS om deze dreiging te verminderen (maar houd rekening met klok scheeftrekken op de eindtijd).
- **Gegevens valideren die zijn geschreven met behulp van een SAS.** Wanneer een clienttoepassing gegevens naar uw opslagaccount schrijft, moet u er rekening mee houden dat er problemen kunnen zijn met die gegevens. Als uw toepassing vereist dat gegevens worden gevalideerd of geautoriseerd voordat deze klaar zijn voor gebruik, moet u deze validatie uitvoeren nadat de gegevens zijn geschreven en voordat deze door uw toepassing worden gebruikt. Deze praktijk beschermt ook tegen corrupte of schadelijke gegevens die naar uw account worden geschreven, hetzij door een gebruiker die de SAS naar behoren heeft overgenomen, hetzij door een gebruiker die misbruik maakt van een gelekte SAS.
- **Weet wanneer u geen SAS moet gebruiken.** Soms wegen de risico's die verbonden zijn aan een bepaalde bewerking ten opzichte van uw opslagaccount op tegen de voordelen van het gebruik van een SAS. Maak voor dergelijke bewerkingen een middle-tier service die naar uw opslagaccount wordt geschreven na het uitvoeren van bedrijfsregelvalidatie, verificatie en controle. Soms is het soms eenvoudiger om toegang op andere manieren te beheren. Als u bijvoorbeeld alle blobs in een container openbaar leesbaar wilt maken, u de container openbaar maken in plaats van een SAS aan elke client te verstrekken voor toegang.
- **Gebruik Azure Monitor- en Azure Storage-logboeken om uw toepassing te controleren.** U Azure Monitor- en storageanalytics-logboekregistratie gebruiken om een piek in autorisatiefouten te observeren als gevolg van een storing in uw SAS-serviceprovider of de onbedoelde verwijdering van een opgeslagen toegangsbeleid. Zie [Azure Storage-statistieken in Azure Monitor](storage-metrics-in-azure-monitor.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) en Azure Storage [Analytics-logboekregistratie](storage-analytics-logging.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)voor meer informatie.

## <a name="get-started-with-sas"></a>Aan de slag met SAS

Zie de volgende artikelen voor elk SAS-type om aan de slag te gaan met handtekeningen voor gedeelde toegang.

### <a name="user-delegation-sas"></a>Gebruikersdelegatie SAS

- [Een SAS voor gebruikersdelegatie maken voor een container of blob met PowerShell](../blobs/storage-blob-user-delegation-sas-create-powershell.md)
- [Een SAS voor gebruikersdelegatie maken voor een container of blob met de Azure CLI](../blobs/storage-blob-user-delegation-sas-create-cli.md)
- [Een gebruikersdelegatie SAS maken voor een container of blob met .NET](../blobs/storage-blob-user-delegation-sas-create-dotnet.md)

### <a name="service-sas"></a>Service SAS

- [Een service-SAS maken voor een container of blob met .NET](../blobs/storage-blob-service-sas-create-dotnet.md)

### <a name="account-sas"></a>Account SAS

- [Een account SAS maken met .NET](storage-account-sas-create-dotnet.md)

## <a name="next-steps"></a>Volgende stappen

- [Toegang delegeren met een gedeelde toegangshandtekening (REST API)](/rest/api/storageservices/delegate-access-with-shared-access-signature)
- [Een SAS voor gebruikersdelegatie (REST API) maken](/rest/api/storageservices/create-user-delegation-sas)
- [Een service-SAS (REST API) maken](/rest/api/storageservices/create-service-sas)
- [Een account SAS (REST API) maken](/rest/api/storageservices/create-account-sas)
