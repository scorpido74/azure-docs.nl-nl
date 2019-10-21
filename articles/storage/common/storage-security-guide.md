---
title: Beveiligings handleiding Azure Storage | Microsoft Docs
description: Meer informatie over de vele methoden voor het beveiligen van Azure Storage, waaronder maar niet beperkt tot RBAC, Storage Service Encryption, versleuteling aan client zijde, SMB 3,0 en Azure Disk Encryption.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/21/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 72e695762f2e45309787e6f62fa97aae4c959f34
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/18/2019
ms.locfileid: "72598095"
---
# <a name="azure-storage-security-guide"></a>Azure Storage-beveiligings handleiding

Azure Storage biedt een uitgebreide reeks beveiligings mogelijkheden die ontwikkel aars in staat stellen om veilige toepassingen te bouwen:

- Alle gegevens (inclusief meta gegevens) die naar Azure Storage zijn geschreven, worden automatisch versleuteld met behulp van [Storage service Encryption (SSE)](storage-service-encryption.md). Zie voor meer informatie [standaard versleuteling aankondigen voor Azure-blobs, bestanden, tabellen en Queue Storage](https://azure.microsoft.com/blog/announcing-default-encryption-for-azure-blobs-files-table-and-queue-storage/).
- Azure Active Directory (Azure AD) en op rollen gebaseerde Access Control (RBAC) worden als volgt ondersteund voor Azure Storage voor resource management-bewerkingen en-gegevens bewerkingen:   
    - U kunt RBAC-rollen toewijzen aan het opslag account voor beveiligings-principals en Azure AD gebruiken voor het autoriseren van resource beheer bewerkingen, zoals sleutel beheer.
    - Azure AD-integratie wordt ondersteund voor Blob-en wachtrij gegevens bewerkingen. U kunt RBAC-rollen toewijzen aan een abonnement, resource groep, opslag account of een afzonderlijke container of wachtrij voor een beveiligings-principal of een beheerde identiteit voor Azure-resources. Zie [toegang tot Azure Storage verifiëren met behulp van Azure Active Directory](storage-auth-aad.md)voor meer informatie.   
- Gegevens kunnen worden beveiligd in transit tussen een toepassing en Azure met behulp van versleuteling aan de [client zijde](../storage-client-side-encryption.md), https of SMB 3,0.  
- Besturings systeem-en gegevens schijven die door Azure virtual machines worden gebruikt, kunnen worden versleuteld met [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).
- Gedelegeerde toegang tot de gegevens objecten in Azure Storage kan worden verleend met behulp van een hand tekening voor gedeelde toegang. Zie [beperkte toegang verlenen tot Azure storage-resources met behulp van Shared Access signatures (SAS)](storage-sas-overview.md)voor meer informatie.

Dit artikel bevat een overzicht van elk van deze beveiligings functies die kunnen worden gebruikt met Azure Storage. Koppelingen worden verstrekt aan artikelen die informatie geven over elke functie, zodat u gemakkelijk verder kunt onderzoeken op elk onderwerp.

Hier volgen de onderwerpen die in dit artikel worden behandeld:

* [Beveiliging van het beheer vlak](#management-plane-security) : uw opslag account beveiligen

  Het beheer vlak bestaat uit de resources die worden gebruikt voor het beheren van uw opslag account. In deze sectie wordt het Azure Resource Manager-implementatie model beschreven en wordt uitgelegd hoe u op rollen gebaseerde Access Control (RBAC) kunt gebruiken om de toegang tot uw opslag accounts te beheren. Het adres bevat ook informatie over het beheren van de sleutels van uw opslag account en hoe u deze opnieuw genereert.
* [Beveiliging van gegevens vlak](#data-plane-security) : de toegang tot uw gegevens beveiligen

  In deze sectie kijken we naar het toestaan van toegang tot de daad werkelijke gegevens objecten in uw opslag account, zoals blobs, bestanden, wacht rijen en tabellen, met behulp van hand tekeningen voor gedeelde toegang en opgeslagen toegangs beleid. We behandelen zowel SAS op service niveau als SAS op account niveau. U ziet ook hoe u de toegang tot een specifiek IP-adres (of een bereik van IP-adressen) kunt beperken, hoe u het protocol dat wordt gebruikt voor HTTPS kunt beperken en hoe u een Shared Access Signature intrekt zonder te wachten totdat het verloopt.
* [Versleuteling 'in transit'](#encryption-in-transit)

  In deze sectie wordt beschreven hoe u gegevens kunt beveiligen wanneer u deze overbrengt naar of uit Azure Storage. Er wordt gecommuniceerd over het aanbevolen gebruik van HTTPS en de versleuteling die wordt gebruikt door SMB 3,0 voor Azure-bestands shares. We gaan ook kijken naar client-side Encryption, waarmee u de gegevens kunt versleutelen voordat ze worden overgebracht naar de opslag in een client toepassing, en om de gegevens te ontsleutelen nadat deze buiten de opslag zijn overgedragen.
* [Versleuteling 'at rest'](#encryption-at-rest)

  We spreken over Storage Service Encryption (SSE). dit wordt nu automatisch ingeschakeld voor nieuwe en bestaande opslag accounts. We gaan ook kijken hoe u Azure Disk Encryption kunt gebruiken en de belangrijkste verschillen en gevallen van schijf versleuteling versus SSE versus versleuteling aan de client zijde te verkennen. We kijken kort naar FIPS-compatibiliteit voor computers van de Amerikaanse overheid.
* [Opslaganalyse](#storage-analytics) gebruiken om de toegang van Azure Storage te controleren

  In deze sectie wordt beschreven hoe u informatie kunt vinden in de logboeken van de opslag analyse voor een aanvraag. We nemen een kijkje in de werkelijke opslag analyse-logboek gegevens en zien hoe u kunt onderscheiden of een aanvraag wordt gedaan met de sleutel van het opslag account, met een gedeelde toegangs handtekening of anoniem, en of deze is geslaagd of mislukt.
* [Clients op basis van een browser inschakelen met CORS](#cross-origin-resource-sharing-cors)

  In deze sectie wordt uitgelegd hoe u het delen van cross-Origin-resources (CORS) toestaat. We praten over de toegang tussen domeinen en hoe u deze kunt afhandelen met de CORS-mogelijkheden die zijn ingebouwd in Azure Storage.

## <a name="management-plane-security"></a>Beveiliging van het beheer vlak
Het beheer vlak bestaat uit bewerkingen die van invloed zijn op het opslag account zelf. U kunt bijvoorbeeld een opslag account maken of verwijderen, een lijst met opslag accounts in een abonnement ophalen, de sleutel van het opslag account ophalen of de sleutel van het opslag account opnieuw genereren.

Wanneer u een nieuw opslag account maakt, selecteert u een implementatie model van klassiek of Resource Manager. Het klassieke model voor het maken van resources in azure staat alleen de toegang tot het abonnement toe, en is op zijn beurt het opslag account.

Deze hand leiding is gericht op het Resource Manager-model dat de aanbevolen manier is voor het maken van opslag accounts. Met de Resource Manager-opslag accounts kunt u, in plaats van toegang tot het hele abonnement te geven, de toegang op een meer beperkt niveau beheren op het beheer vlak met behulp van op rollen gebaseerde Access Control (RBAC).

### <a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Uw opslag account beveiligen met Access Control op basis van rollen (RBAC)
Laten we weten wat RBAC is en hoe u het kunt gebruiken. Elk Azure-abonnement heeft een Azure Active Directory. Gebruikers, groepen en toepassingen van die Directory kunnen toegang krijgen om resources te beheren in het Azure-abonnement dat gebruikmaakt van het Resource Manager-implementatie model. Dit type beveiliging wordt aangeduid als op rollen gebaseerd Access Control (RBAC). Als u deze toegang wilt beheren, kunt u de [Azure Portal](https://portal.azure.com/), de [Azure cli-hulpprogram ma's](../../cli-install-nodejs.md), [Power shell](/powershell/azureps-cmdlets-docs)of de rest-api's van de [Azure Storage Resource provider](https://msdn.microsoft.com/library/azure/mt163683.aspx)gebruiken.

Met het Resource Manager-model plaatst u het opslag account in een resource groep en beheert u de toegang tot het beheer vlak van dat specifieke opslag account met behulp van Azure Active Directory. U kunt bijvoorbeeld specifieke gebruikers de mogelijkheid geven om toegang te krijgen tot de sleutels van het opslag account, terwijl andere gebruikers informatie over het opslag account kunnen bekijken, maar geen toegang hebben tot de sleutel van het opslag account.

#### <a name="granting-access"></a>Toegang verlenen
Toegang wordt verleend door de juiste RBAC-rol toe te wijzen aan gebruikers, groepen en toepassingen, aan het juiste bereik. Als u toegang wilt verlenen aan het hele abonnement, wijst u een rol toe op het abonnements niveau. U kunt toegang verlenen aan alle resources in een resource groep door machtigingen te verlenen aan de resource groep zelf. U kunt ook specifieke rollen toewijzen aan specifieke resources, zoals opslag accounts.

Hier volgen de belangrijkste punten die u moet weten over het gebruik van RBAC voor toegang tot de beheer bewerkingen van een Azure Storage account:

* Wanneer u toegang toewijst, wijst u in principe een rol toe aan het account dat u toegang wilt geven. U kunt de toegang beheren tot de bewerkingen die worden gebruikt voor het beheer van dat opslag account, maar niet voor de gegevens objecten in het account. U kunt bijvoorbeeld machtigingen verlenen om de eigenschappen van het opslag account (zoals redundantie) op te halen, maar niet naar een container of gegevens binnen een container in Blob Storage.
* Als iemand toestemming heeft om toegang te krijgen tot de gegevens objecten in het opslag account, kunt u hen toestemming geven om de sleutel van het opslag account te lezen. deze gebruiker kan vervolgens deze sleutels gebruiken voor toegang tot de blobs, wacht rijen, tabellen en bestanden.
* Rollen kunnen worden toegewezen aan een specifiek gebruikers account, een groep gebruikers of aan een specifieke toepassing.
* Elke rol heeft een lijst met acties en niet de acties. De rol Inzender voor virtuele machines heeft bijvoorbeeld de actie ' Listkeys ophalen ', waarmee de sleutels van het opslag account kunnen worden gelezen. De Inzender heeft ' not Actions ', zoals het bijwerken van de toegang voor gebruikers in de Active Directory.
* Rollen voor opslag zijn onder andere (maar zijn niet beperkt tot) de volgende rollen:

  * Eigenaar: ze kunnen alles beheren, met inbegrip van toegang.
  * Inzender: ze kunnen alles doen wat de eigenaar kan doen, met uitzonde ring van toegang toewijzen. Iemand met deze rol kan de sleutels voor het opslag account weer geven en opnieuw genereren. Met de sleutels voor het opslag account hebben ze toegang tot de gegevens objecten.
  * Lezer: ze kunnen informatie weer geven over het opslag account, met uitzonde ring van geheimen. Als u bijvoorbeeld een rol met lezers machtigingen voor het opslag account aan iemand toewijst, kunnen ze de eigenschappen van het opslag account weer geven, maar kunnen ze geen wijzigingen aanbrengen in de eigenschappen of de sleutels van het opslag account weer geven.
  * Inzender voor opslag accounts: ze kunnen het opslag account beheren: ze kunnen de resource groepen en-resources van het abonnement lezen en implementaties van de abonnements resource groep maken en beheren. Ze hebben ook toegang tot de sleutel van het opslag account, die op zijn beurt de toegang tot het gegevens vlak kan krijgen.
  * Beheerder van gebruikers toegang: ze kunnen gebruikers toegang tot het opslag account beheren. Ze kunnen bijvoorbeeld lezers toegang verlenen tot een specifieke gebruiker.
  * Inzender voor virtuele machines: ze kunnen virtuele machines beheren, maar niet het opslag account waarmee ze zijn verbonden. Met deze rol kan de sleutel van het opslag account worden weer gegeven. Dit betekent dat de gebruiker aan wie u deze rol toewijst, het gegevens vlak kan bijwerken.

    Om een gebruiker een virtuele machine te laten maken, moeten ze het bijbehorende VHD-bestand in een opslag account kunnen maken. Hiervoor moeten ze de sleutel van het opslag account kunnen ophalen en door geven aan de API die de VM maakt. Daarom moeten ze deze machtiging hebben, zodat ze de sleutels van het opslag account kunnen weer geven.
* De mogelijkheid om aangepaste rollen te definiëren is een functie waarmee u een reeks acties kunt samen stellen uit een lijst met beschik bare acties die kunnen worden uitgevoerd op Azure-resources.
* U moet de gebruiker in uw Azure Active Directory instellen voordat u hieraan een rol kunt toewijzen.
* U kunt een rapport maken met wie u hebt toegewezen/ingetrokken wat voor soort toegang tot/waar en op welk bereik u Power shell of de Azure CLI wilt gebruiken.

#### <a name="resources"></a>Bronnen
* [Toegangsbeheer op basis van rollen in Azure Active Directory](../../role-based-access-control/role-assignments-portal.md)

  In dit artikel wordt het toegangsbeheer op basis van rollen in Azure Active Directory uitgelegd.
* [RBAC: ingebouwde rollen](../../role-based-access-control/built-in-roles.md)

  In dit artikel vindt u meer informatie over de ingebouwde functies die beschikbaar zijn in RBAC.
* [Resource Manager-implementatie en klassieke implementatie begrijpen](../../azure-resource-manager/resource-manager-deployment-model.md)

  In dit artikel worden de implementaties van Resource Manager en de klassieke implementatie modellen beschreven en worden de voor delen van het gebruik van Resource Manager en resource groepen uitgelegd. In dit artikel wordt uitgelegd hoe de Azure compute-, netwerk-en opslag providers onder het Resource Manager-model werken.
* [Toegangsbeheer op basis van rollen beheren met de REST-API](../../role-based-access-control/role-assignments-rest.md)

  Dit artikel beschrijft hoe u de REST-API gebruikt om RBAC te beheren.
* [Naslaginformatie over de REST-API voor de Azure Storage-resourceprovider](https://msdn.microsoft.com/library/azure/mt163683.aspx)

  In deze API-verwijzing worden de Api's beschreven die u kunt gebruiken om uw opslag account programmatisch te beheren.

* [Toegangsbeheer op basis van rollen voor Microsoft Azure vanuit Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

  Dit is een koppeling naar een video op Channel 9 van de vergadering over MS Ignite in 2015. In deze sessie wordt ingegaan op de opties voor toegangsbeheer en rapportage in Azure en worden aanbevolen procedures behandeld voor de beveiliging van de toegang tot Azure-abonnementen met Azure Active Directory.

### <a name="managing-your-storage-account-keys"></a>De sleutels van uw opslag account beheren
Sleutels voor opslag accounts zijn 512-bits teken reeksen die door Azure worden gemaakt en die samen met de naam van het opslag account kunnen worden gebruikt voor toegang tot de gegevens objecten die zijn opgeslagen in het opslag account, bijvoorbeeld blobs, entiteiten in een tabel, wachtrij berichten en bestanden op een Azure-bestands share. De toegang tot de sleutel van het opslag account beheren beheert de toegang tot het gegevens vlak voor dat opslag account.

Elk opslag account heeft twee sleutels waarnaar wordt verwezen als "sleutel 1" en "sleutel 2" in de [Azure Portal](https://portal.azure.com/) en in de Power shell-cmdlets. Deze kunnen hand matig worden gegenereerd met een van de volgende methoden, waaronder, maar niet beperkt tot het gebruik van de [Azure Portal](https://portal.azure.com/), Power shell, de Azure CLI of programmatisch met behulp van de client bibliotheek voor .net-opslag of de Azure Storage Services-rest API.

Er zijn een aantal redenen om de sleutels van uw opslag account opnieuw te genereren.

* U kunt deze mogelijk op regel matige basis opnieuw genereren om veiligheids redenen.
* U kunt de sleutels van uw opslag account opnieuw genereren als iemand in een toepassing wordt beheerd naar Hack en de sleutel ophaalt die is opgeslagen in een configuratie bestand, zodat deze volledige toegang geeft tot uw opslag account.
* Een andere case voor het opnieuw genereren van sleutels is als uw team gebruikmaakt van een Storage Explorer toepassing die de sleutel van het opslag account behoudt en een van de team leden verlaat. De toepassing blijft werken, waardoor ze toegang hebben tot uw opslag account nadat ze zijn verdwenen. Dit is in feite de belangrijkste reden voor het maken van gedeelde toegangs handtekeningen op account niveau: u kunt een SAS op account niveau gebruiken in plaats van de toegangs sleutels in een configuratie bestand op te slaan.

#### <a name="key-regeneration-plan"></a>Sleutel regeneratie plan
U wilt niet alleen de sleutel die u gebruikt zonder een planning opnieuw genereren. Als u dat doet, kunt u alle toegang tot dat opslag account afmelden, wat kan leiden tot grote onderbrekingen. Daarom zijn er twee sleutels. Genereer één sleutel per keer.

Voordat u de sleutels opnieuw genereert, moet u een lijst hebben van alle toepassingen die afhankelijk zijn van het opslag account, evenals andere services die u gebruikt in Azure. Als u bijvoorbeeld Azure Media Services gebruikt die afhankelijk zijn van uw opslag account, moet u de toegangs sleutels opnieuw synchroniseren met uw media service nadat u de sleutel opnieuw hebt gegenereerd. Als u toepassingen gebruikt, zoals een opslag Verkenner, moet u ook de nieuwe sleutels voor deze toepassingen opgeven. Als u virtuele machines hebt waarvan de VHD-bestanden worden opgeslagen in het opslag account, worden deze niet beïnvloed door het opnieuw genereren van de sleutel van het opslag account.

U kunt uw sleutels opnieuw genereren in de Azure Portal. Wanneer de sleutels opnieuw worden gegenereerd, kan het tot tien minuten duren voordat deze worden gesynchroniseerd tussen opslag Services.

Wanneer u klaar bent, ziet u hier het algemene proces waarin wordt uitgelegd hoe u uw sleutel moet wijzigen. In dit geval is de veronderstelling dat u momenteel sleutel 1 gebruikt en gaat u alles wijzigen voor het gebruik van sleutel 2.

1. Genereer sleutel 2 opnieuw om er zeker van te zijn dat het veilig is. U kunt dit doen in de Azure Portal.
2. In alle toepassingen waar de opslag sleutel is opgeslagen, wijzigt u de opslag sleutel zodat de nieuwe waarde van sleutel 2 wordt gebruikt. De toepassing testen en publiceren.
3. Genereer de sleutel 1 opnieuw nadat alle toepassingen en services actief zijn en correct zijn uitgevoerd. Dit zorgt ervoor dat iedereen met wie u de nieuwe sleutel niet uitdrukkelijk hebt opgegeven, geen toegang meer heeft tot het opslag account.

Als u momenteel sleutel 2 gebruikt, kunt u hetzelfde proces gebruiken, maar de sleutel namen vervolgens terugdraaien.

U kunt migreren over een paar dagen, waarbij u elke toepassing wijzigt om de nieuwe sleutel te gebruiken en deze te publiceren. Nadat alle zijn voltooid, moet u teruggaan en de oude sleutel opnieuw genereren, zodat deze niet meer werkt.

Een andere mogelijkheid is om de sleutel van het opslag account in een [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) als geheim te plaatsen en de sleutel van de toepassing op te halen. Wanneer u de sleutel opnieuw genereert en de Azure Key Vault bijwerkt, hoeven de toepassingen niet opnieuw te worden geïmplementeerd omdat deze automatisch de nieuwe sleutel van de Azure Key Vault ophalen. Houd er rekening mee dat u de toepassing de sleutel kunt laten lezen telkens wanneer u deze nodig hebt, of u kunt deze in de cache opslaan en als deze niet kan worden gebruikt, haalt u de sleutel opnieuw op uit het Azure Key Vault.

Met Azure Key Vault wordt ook een ander beveiligings niveau voor uw opslag sleutels toegevoegd. Als u deze methode gebruikt, hebt u nooit de opslag sleutel vastgelegd in een configuratie bestand. Dit betekent dat de toegang tot de sleutels zonder specifieke machtigingen wordt verwijderd.

Een ander voor deel van het gebruik van Azure Key Vault is dat u ook de toegang tot uw sleutels kunt controleren met behulp van Azure Active Directory. Dit betekent dat u toegang kunt verlenen tot de toepassingen die de sleutels moeten ophalen van Azure Key Vault en dat andere toepassingen geen toegang hebben tot de sleutels zonder dat ze hiervoor toestemming geven.

> [!NOTE]
> Micro soft raadt u aan om op hetzelfde moment slechts één van de sleutels in al uw toepassingen te gebruiken. Als u Key 1 op sommige locaties en sleutel 2 in andere gebruikt, kunt u de sleutels niet draaien zonder dat de toepassing de toegang verliest.

#### <a name="resources"></a>Bronnen

* [Instellingen van opslagaccounts beheren in de Azure-portal](storage-account-manage.md)
* [Naslaginformatie over de REST-API voor de Azure Storage-resourceprovider](https://msdn.microsoft.com/library/mt163683.aspx)

## <a name="data-plane-security"></a>Beveiliging van gegevens vlak
Beveiliging van het gegevens vlak verwijst naar de methoden die worden gebruikt voor het beveiligen van de gegevens objecten die zijn opgeslagen in Azure Storage: de blobs, wacht rijen, tabellen en bestanden. We hebben methoden gezien voor het versleutelen van de gegevens en beveiliging tijdens de door Voer van de gegevens, maar hoe gaat u verder met het beheren van de toegang tot de objecten?

Er zijn drie opties voor het autoriseren van toegang tot gegevens objecten in Azure Storage, waaronder:

- Azure AD gebruiken om toegang te verlenen aan containers en wacht rijen. Azure AD biedt voor delen ten opzichte van andere methoden voor verificatie, waaronder het verwijderen van geheimen in uw code. Zie [toegang tot Azure Storage verifiëren met behulp van Azure Active Directory](storage-auth-aad.md)voor meer informatie. 
- De sleutels van uw opslag account gebruiken om toegang te verlenen via een gedeelde sleutel. Voor autorisatie via een gedeelde sleutel moet u de sleutels van uw opslag account opslaan in uw toepassing. Daarom raadt micro soft aan om in plaats daarvan Azure AD te gebruiken.
- Shared Access Signatures gebruiken om beheerde machtigingen te verlenen aan specifieke gegevens objecten voor een specifieke tijds duur.

Daarnaast kunt u voor Blob Storage open bare toegang tot uw blobs toestaan door het toegangs niveau in te stellen voor de container die de blobs dienovereenkomstig bevat. Als u de toegang voor een container instelt op BLOB of container, wordt open bare Lees toegang voor de blobs in die container toegestaan. Dit betekent dat iedereen met een URL die verwijst naar een BLOB in die container deze kan openen in een browser zonder gebruik te maken van een Shared Access Signature of de sleutels voor het opslag account.

Naast het beperken van de toegang via autorisatie, kunt u ook [firewalls en virtuele netwerken](storage-network-security.md) gebruiken om de toegang tot het opslag account te beperken op basis van netwerk regels.  Met deze aanpak kunt u de toegang weigeren voor openbaar Internet verkeer en alleen toegang verlenen aan specifieke Azure Virtual Networks of open bare IP-adresbereiken voor Internet.

### <a name="storage-account-keys"></a>Opslagaccountsleutels
Sleutels voor opslag accounts zijn 512-bits teken reeksen die zijn gemaakt door Azure en die samen met de naam van het opslag account kunnen worden gebruikt voor toegang tot de gegevens objecten die zijn opgeslagen in het opslag account.

U kunt bijvoorbeeld blobs lezen, schrijven naar wacht rijen, tabellen maken en bestanden wijzigen. Veel van deze acties kunnen worden uitgevoerd via de Azure Portal, of een van de vele Storage Explorer toepassingen gebruiken. U kunt ook code schrijven om de REST API of een van de opslag-client bibliotheken te gebruiken om deze bewerkingen uit te voeren.

Zoals besproken in de sectie over de [beveiliging van het beheer vlak](#management-plane-security), kan toegang tot de opslag sleutels voor een klassiek opslag account worden verleend door volledige toegang te geven tot het Azure-abonnement. Toegang tot de opslag sleutels voor een opslag account met behulp van het Azure Resource Manager model kan worden beheerd via op rollen gebaseerde Access Control (RBAC).

### <a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Toegang tot objecten in uw account delegeren met behulp van hand tekeningen voor gedeelde toegang en opgeslagen toegangs beleid
Een Shared Access Signature is een teken reeks met een beveiligings token dat kan worden gekoppeld aan een URI, waarmee u de toegang tot opslag objecten kunt delegeren en beperkingen kunt opgeven, zoals de machtigingen en het datum-en tijd bereik van de toegang.

U kunt toegang verlenen tot blobs, containers, wachtrij berichten, bestanden en tabellen. Met tabellen kunt u daad werkelijk toegangs rechten verlenen voor een bereik van entiteiten in de tabel door de partitie en het bereik van de rij op te geven waartoe u wilt dat de gebruiker toegang heeft. Als u bijvoorbeeld gegevens hebt opgeslagen met een partitie sleutel van een geografische status, kunt u iemand toegang geven tot alleen de gegevens voor Californië.

In een ander voor beeld kunt u een webtoepassing een SAS-token geven waarmee gegevens naar een wachtrij kunnen worden geschreven, en waarmee een rol van werk rollen een SAS-token kan geven om berichten uit de wachtrij op te halen en te verwerken. Of u kunt een klant een SAS-token geven waarmee ze afbeeldingen kunnen uploaden naar een container in Blob Storage en een webtoepassing machtigen om deze afbeeldingen te lezen. In beide gevallen is er sprake van een schei ding van de bezorgdheid: elke toepassing kan alleen de toegang krijgen die ze nodig hebben om hun taak uit te voeren. Dit is mogelijk via het gebruik van hand tekeningen voor gedeelde toegang.

#### <a name="why-you-want-to-use-shared-access-signatures"></a>Waarom u gedeelde toegangs handtekeningen wilt gebruiken
Waarom zou u een SAS wilt gebruiken in plaats van de sleutel van uw opslag account, wat zo veel eenvoudiger is? Het geven van de sleutel van uw opslag account is hetzelfde als het delen van de sleutels van uw opslag-Konink rijk. Hiermee wordt volledige toegang verleend. Iemand kan uw sleutels gebruiken en hun hele muziek bibliotheek uploaden naar uw opslag account. Ze kunnen uw bestanden ook vervangen door virussen geïnfecteerde versies of uw gegevens stelen. Het is niet nodig om onbeperkte toegang te geven tot uw opslag account.

Met Shared Access signatures kunt u een client alleen de machtigingen geven die vereist zijn voor een beperkte periode. Als iemand bijvoorbeeld een BLOB uploadt naar uw account, kunt u hen schrijf toegang geven voor genoeg tijd om de BLOB te uploaden (afhankelijk van de grootte van de blob, natuurlijk). En als u van gedachten verandert, kunt u die toegang intrekken.

Daarnaast kunt u opgeven dat aanvragen die worden gedaan met behulp van een SAS, beperkt zijn tot een bepaald IP-adres of IP-adres bereik dat extern is voor Azure. U kunt ook vereisen dat aanvragen worden gedaan met behulp van een specifiek protocol (HTTPS of HTTP/HTTPS). Dit betekent dat als u alleen HTTPS-verkeer wilt toestaan, u het vereiste protocol alleen op HTTPS kunt instellen en het HTTP-verkeer wordt geblokkeerd.

#### <a name="definition-of-a-shared-access-signature"></a>Definitie van een Shared Access Signature
Een Shared Access Signature is een reeks query parameters die zijn toegevoegd aan de URL die op de resource is gericht

Dit geeft informatie over de toegang die is toegestaan en de tijds duur waarvoor de toegang is toegestaan. Hier volgt een voor beeld. deze URI biedt vijf minuten Lees toegang tot een blob. Houd er rekening mee dat SAS-query parameters een URL-code ring moeten hebben, zoals% 3A voor dubbele punt (:) of %20 voor een spatie.

```
http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
?sv=2015-04-05 (storage service version)
&st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
&se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
&sr=b (resource is a blob)
&sp=r (read access)
&sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
&spr=https (only allow HTTPS requests)
&sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)
```

#### <a name="how-the-shared-access-signature-is-authorized-by-the-azure-storage-service"></a>Hoe de Shared Access Signature wordt geautoriseerd door de Azure Storage-service
Wanneer de opslag service de aanvraag ontvangt, worden de para meters voor invoer query's gebruikt en wordt er een hand tekening gemaakt met dezelfde methode als het aanroepende programma. Vervolgens worden de twee hand tekeningen vergeleken. Als ze overeenkomen, kan de opslag service de versie van de opslag service controleren om ervoor te zorgen dat deze geldig is. Controleer of de huidige datum en tijd binnen het opgegeven venster vallen, Controleer of de toegang is aangevraagd met de aangevraagde aanvraag, enzovoort.

Als bijvoorbeeld met onze URL hierboven wordt verwezen naar een bestand in plaats van een blob, zou deze aanvraag mislukken omdat het Shared Access Signature is voor een blob. Als de REST-opdracht die wordt aangeroepen om een BLOB bij te werken, mislukt dit omdat de Shared Access Signature opgeeft dat alleen lees toegang is toegestaan.

#### <a name="types-of-shared-access-signatures"></a>Typen Shared Access signatures
* Een SAS op service niveau kan worden gebruikt om toegang te krijgen tot specifieke bronnen in een opslag account. Enkele voor beelden hiervan zijn het ophalen van een lijst met blobs in een container, het downloaden van een blob, het bijwerken van een entiteit in een tabel, het toevoegen van berichten aan een wachtrij of het uploaden van een bestand naar een bestands share.
* Een SAS op account niveau kan worden gebruikt om toegang te krijgen tot alles waar een SAS op service niveau kan worden gebruikt. Daarnaast kan het opties bieden voor bronnen die niet zijn toegestaan met een SAS op service niveau, zoals de mogelijkheid om containers, tabellen, wacht rijen en bestands shares te maken. U kunt ook toegang tot meerdere services tegelijk opgeven. U kunt bijvoorbeeld iemand toegang geven tot blobs en bestanden in uw opslag account.

#### <a name="creating-a-sas-uri"></a>Een SAS-URI maken
1. U kunt op aanvraag een URI maken, waarbij alle query parameters elke keer worden gedefinieerd.

   Deze aanpak is flexibel, maar als u een logische set para meters hebt die vergelijkbaar zijn, is het gebruik van een opgeslagen toegangs beleid een beter idee.
2. U kunt een opgeslagen toegangs beleid maken voor een volledige container, een bestands share, een tabel of een wachtrij. Vervolgens kunt u deze gebruiken als basis voor de SAS-Uri's die u maakt. Machtigingen op basis van opgeslagen toegangs beleid kunnen eenvoudig worden ingetrokken. U kunt Maxi maal vijf beleids regels definiëren voor elke container, wachtrij, tabel of bestands share.

   Als u bijvoorbeeld veel mensen de blobs in een specifieke container zou lezen, kunt u een opgeslagen toegangs beleid maken met de tekst ' Lees toegang geven ' en alle andere instellingen die elke keer hetzelfde zullen zijn. Vervolgens kunt u een SAS-URI maken met behulp van de instellingen van het beleid voor opgeslagen toegang en de verval datum/-tijd opgeven. Het voor deel hiervan is dat u niet alle query parameters hoeft op te geven.

#### <a name="revocation"></a>Intrekkings
Stel dat uw SAS heeft geknoeid of dat u deze wilt wijzigen vanwege de vereisten voor de beveiligings-of regelgeving van het bedrijf. Hoe trekt u de toegang tot een resource in met behulp van die SAS? Dit is afhankelijk van hoe u de SAS-URI hebt gemaakt.

Als u ad hoc-Uri's gebruikt, hebt u drie opties. U kunt SAS-tokens met een kort verval beleid uitgeven en wachten tot de SA'S verlopen. U kunt de naam van de resource wijzigen of verwijderen (ervan uitgaande dat het token aan een enkel object is toegewezen). U kunt de sleutel van het opslag account wijzigen. Deze laatste optie kan aanzienlijke gevolgen hebben, afhankelijk van het aantal services dat het opslag account gebruikt en waarschijnlijk niet iets dat u wilt doen zonder enige planning.

Als u een SAS gebruikt die is afgeleid van een opgeslagen toegangs beleid, kunt u de toegang verwijderen door het opgeslagen toegangs beleid in te trekken. u kunt dit gewoon wijzigen zodat het al is verlopen of u kunt het helemaal verwijderen. Dit wordt onmiddellijk van kracht en alle SA'S die zijn gemaakt met behulp van het beleid voor opgeslagen toegang, worden ongeldig. Het bijwerken of verwijderen van het beleid voor opgeslagen toegang kan van invloed zijn op de toegang tot deze specifieke container, bestands share, tabel of wachtrij via SAS, maar als de clients zijn geschreven zodat ze een nieuwe SAS aanvragen wanneer de oude ongeldig wordt, werkt dit prima.

Omdat het gebruik van een SAS die is afgeleid van een opgeslagen toegangs beleid, u de mogelijkheid biedt om die SA'S onmiddellijk in te trekken, is het de aanbevolen best practice om altijd opgeslagen toegangs beleid te gebruiken wanneer dat mogelijk is.

#### <a name="resources"></a>Bronnen
Raadpleeg de volgende artikelen voor meer informatie over het gebruik van hand tekeningen voor gedeelde toegang en opgeslagen toegangs beleid:

* Dit zijn de referentie artikelen.

  * [Service-SA'S](https://msdn.microsoft.com/library/dn140256.aspx)

    In dit artikel vindt u voor beelden van het gebruik van een SAS op service niveau met blobs, wachtrij berichten, tabel bereiken en bestanden.
  * [Een service-SAS samen stellen](https://msdn.microsoft.com/library/dn140255.aspx)
  * [Een account maken van SAS](https://msdn.microsoft.com/library/mt584140.aspx)

* Verificatie

  * [Verificatie voor de Azure Storage services](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* Zelf studie aan de slag met gedeelde toegang

  * [Zelf studie SAS aan de slag](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

## <a name="encryption-in-transit"></a>Versleuteling in transit
### <a name="transport-level-encryption--using-https"></a>Versleuteling op transport niveau: HTTPS gebruiken
Een andere stap die u moet uitvoeren om ervoor te zorgen dat uw Azure Storage gegevens worden beveiligd door de gegevens te versleutelen tussen de client en Azure Storage. De eerste aanbeveling is altijd het [https](https://en.wikipedia.org/wiki/HTTPS) -protocol te gebruiken. Dit zorgt voor beveiligde communicatie via het open bare Internet.

Als u een beveiligd communicatie kanaal wilt, moet u altijd HTTPS gebruiken bij het aanroepen van de REST-Api's of het openen van objecten in de opslag. Ook **gedeelde toegangs handtekeningen**, die kunnen worden gebruikt voor het delegeren van toegang tot Azure Storage-objecten, bevatten een optie om op te geven dat alleen het HTTPS-protocol kan worden gebruikt bij het gebruik van hand tekeningen voor gedeelde toegang, zodat iedereen koppelingen met SAS-tokens kan verzenden maakt gebruik van het juiste protocol.

U kunt het gebruik van HTTPS afdwingen bij het aanroepen van de REST-Api's om toegang te krijgen tot objecten in opslag accounts door [beveiligde overdracht](../storage-require-secure-transfer.md) in te scha kelen die vereist is voor het opslag account. Verbindingen die gebruikmaken van HTTP, worden geweigerd als deze functie is ingeschakeld.

### <a name="using-encryption-during-transit-with-azure-file-shares"></a>Versleuteling gebruiken tijdens de overdracht met Azure-bestands shares
[Azure files](../files/storage-files-introduction.md) ondersteunt VERSLEUTELING via SMB 3,0 en met https bij gebruik van de bestands rest API. Bij het koppelen buiten de Azure-regio is de Azure-bestands share aanwezig in, zoals on-premises of in een andere Azure-regio, SMB 3,0 met versleuteling altijd vereist. SMB 2,1 biedt geen ondersteuning voor versleuteling, daarom zijn standaard verbindingen alleen toegestaan binnen dezelfde regio in azure, maar SMB 3,0 met versleuteling kan worden afgedwongen door een [veilige overdracht te vereisen](../storage-require-secure-transfer.md) voor het opslag account.

SMB 3,0 met versleuteling is beschikbaar in [alle ondersteunde Windows-en Windows Server-besturings systemen](../files/storage-how-to-use-files-windows.md) , met uitzonde ring van Windows 7 en windows server 2008 R2, die alleen ondersteuning bieden voor SMB 2,1. SMB 3,0 wordt ook ondersteund op [macOS](../files/storage-how-to-use-files-mac.md) en distributies van [Linux](../files/storage-how-to-use-files-linux.md) met Linux kernel 4,11 en hoger. De ondersteuning voor versleuteling voor SMB 3,0 is ook backported aan oudere versies van de Linux-kernel door diverse Linux-distributies. Raadpleeg de [vereisten van SMB-clients](../files/storage-how-to-use-files-linux.md#smb-client-reqs)voor meer informatie.

### <a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>De beveiliging van gegevens die u naar de opslag stuurt, beveiligen met behulp van versleuteling aan de client zijde
Een andere optie waarmee u ervoor kunt zorgen dat uw gegevens veilig zijn terwijl ze worden overgedragen tussen een client toepassing en opslag is versleuteling aan de client zijde. De gegevens worden versleuteld voordat ze naar Azure Storage worden overgebracht. Bij het ophalen van de gegevens uit Azure Storage worden de gegevens ontsleuteld nadat deze zijn ontvangen aan de client zijde. Hoewel de gegevens worden versleuteld over het hele netwerk, raden we u aan om ook HTTPS te gebruiken, aangezien er controles voor de integriteit van gegevens zijn ingebouwd, waardoor netwerk fouten die van invloed zijn op de integriteit van de gegevens worden verholpen.

Versleuteling aan de client zijde is ook een methode voor het versleutelen van uw gegevens in rust tijd, omdat de gegevens worden opgeslagen in de versleutelde vorm. Verderop in dit onderwerp vindt u meer informatie over [versleuteling in rust](#encryption-at-rest).

## <a name="encryption-at-rest"></a>Versleuteling bij rest
Er zijn drie Azure-functies die versleuteling bieden bij rest. Azure Disk Encryption wordt gebruikt voor het versleutelen van het besturings systeem en de gegevens schijven in IaaS Virtual Machines. Versleuteling aan client zijde en SSE worden beide gebruikt voor het versleutelen van gegevens in Azure Storage. 

Hoewel u versleuteling aan de client zijde kunt gebruiken voor het versleutelen van de gegevens die onderweg zijn (die ook in de versleutelde vorm worden opgeslagen in de opslag), kunt u de voor keur geven aan HTTPS tijdens de overdracht en een zekere manier hebben om de gegevens automatisch te versleutelen wanneer deze worden opgeslagen. Er zijn twee manieren om dit te doen: Azure Disk Encryption en SSE. Er wordt een code ring gebruikt voor het rechtstreeks versleutelen van gegevens op besturings systeem en gegevens schijven die worden gebruikt door Vm's, en de andere wordt gebruikt voor het versleutelen van gegevens die zijn geschreven naar Azure Blob Storage.

### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE is ingeschakeld voor alle opslag accounts en kan niet worden uitgeschakeld. Met SSE worden uw gegevens automatisch versleuteld wanneer deze naar Azure Storage worden geschreven. Wanneer u gegevens uit Azure Storage leest, wordt deze door Azure Storage ontsleuteld voordat ze worden geretourneerd. Met SSE kunt u uw gegevens beveiligen zonder dat u code hoeft te wijzigen of code aan toepassingen hoeft toe te voegen.

U kunt door micro soft beheerde sleutels of uw eigen aangepaste sleutels gebruiken. Micro soft genereert beheerde sleutels en verwerkt hun beveiligde opslag en de normale rotatie, zoals gedefinieerd door intern micro soft-beleid. Zie [Storage service Encryption het gebruik van door de klant beheerde sleutels in azure Key Vault](storage-service-encryption-customer-managed-keys.md)voor meer informatie over het gebruik van aangepaste sleutels.

SSE versleutelt automatisch gegevens in alle prestatielagen (Standaard en Premium), alle implementatiemodellen (Azure Resource Manager en het klassieke model) en alle services van Azure Storage (Blob, Queue, Table en File). 

### <a name="client-side-encryption"></a>Versleuteling aan client zijde
We hebben versleuteling aan de client zijde vermeld bij het bespreken van de versleuteling van de gegevens die onderweg zijn. Met deze functie kunt u uw gegevens in een client toepassing programmatisch versleutelen voordat u deze verzendt over de draden die moeten worden geschreven naar Azure Storage en kunt u uw gegevens programmatisch ontsleutelen nadat u deze hebt opgehaald van Azure Storage.

Dit voorziet in het door voeren van versleuteling, maar biedt ook de mogelijkheid om op rest te versleutelen. Hoewel de gegevens tijdens de overdracht worden versleuteld, raden we u aan HTTPS te gebruiken om te profiteren van de ingebouwde gegevens integriteits controles die de netwerk fouten helpen beperken die van invloed zijn op de integriteit van de gegevens.

Een voor beeld van waar u dit kunt doen, is als u een webtoepassing hebt waarmee blobs worden opgeslagen en blobs worden opgehaald, en u de toepassing en gegevens zo veilig mogelijk wilt maken. In dat geval gebruikt u versleuteling aan de client zijde. Het verkeer tussen de client en de Azure Blob-service bevat de versleutelde resource en niemand kan de gegevens in de overdracht interpreteren en opnieuw samen brengen in uw persoonlijke blobs.

Versleuteling aan client zijde is ingebouwd in de Java-en de .NET Storage-client Bibliotheken, die op hun beurt gebruikmaken van de Azure Key Vault Api's, zodat u deze eenvoudig kunt implementeren. Het proces voor het versleutelen en ontsleutelen van de gegevens maakt gebruik van de envelop techniek en slaat meta gegevens op die worden gebruikt door de versleuteling in elk opslag object. Voor blobs wordt deze bijvoorbeeld opgeslagen in de BLOB-meta gegevens, maar voor wacht rijen, wordt deze toegevoegd aan elk wachtrij bericht.

Voor de versleuteling zelf kunt u uw eigen versleutelings sleutels genereren en beheren. U kunt ook sleutels gebruiken die worden gegenereerd door de Azure Storage-client bibliotheek, of u kunt de sleutels laten genereren door de Azure Key Vault. U kunt uw versleutelings sleutels opslaan in uw on-premises sleutel opslag of u kunt deze opslaan in een Azure Key Vault. Met Azure Key Vault kunt u toegang verlenen tot de geheimen in Azure Key Vault voor specifieke gebruikers die Azure Active Directory gebruiken. Dit betekent dat niet alleen iedereen de Azure Key Vault kan lezen en de sleutels kan ophalen die u gebruikt voor versleuteling aan de client zijde.

#### <a name="resources"></a>Bronnen
* [Blobs in Microsoft Azure Storage versleutelen en ontsleutelen met behulp van Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)

  In dit artikel wordt uitgelegd hoe u versleuteling aan de client zijde kunt gebruiken met Azure Key Vault, met inbegrip van hoe u de KEK maakt en opslaat in de kluis met behulp van Power shell.
* [Versleuteling aan client zijde en Azure Key Vault voor Microsoft Azure Storage](../storage-client-side-encryption.md)

  Dit artikel bevat een uitleg over versleuteling aan de client zijde en biedt voor beelden van het gebruik van de Storage-client bibliotheek voor het versleutelen en ontsleutelen van resources van de vier opslag Services. Ook wordt een besprekingen over Azure Key Vault.

### <a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Schijven die worden gebruikt door uw virtuele machines versleutelen met behulp van Azure Disk Encryption
Met Azure Disk Encryption kunt u de stations van het besturings systeem en de gegevens schijven die worden gebruikt door een virtuele machine van IaaS versleutelen. Voor Windows worden de stations versleuteld met de standaard BitLocker-versleutelings technologie. Voor Linux worden de schijven versleuteld met de DM-cryptografie technologie. Dit is geïntegreerd met Azure Key Vault zodat u de schijf versleutelings sleutels kunt beheren en beheren.

De oplossing ondersteunt de volgende scenario's voor IaaS Vm's wanneer deze zijn ingeschakeld in Microsoft Azure:

* Integratie met Azure Key Vault
* Virtuele machines uit de Standard-laag: [A, D, DS, G, GS, enzovoort serie IaaS vm's](https://azure.microsoft.com/pricing/details/virtual-machines/)
* Versleuteling inschakelen op Windows-en Linux IaaS-Vm's
* Versleuteling uitschakelen voor besturings systeem en gegevens stations voor Windows IaaS-Vm's
* Versleuteling uitschakelen op gegevens stations voor Linux IaaS Vm's
* Versleuteling inschakelen voor IaaS-Vm's waarop Windows client-besturings systeem wordt uitgevoerd
* Versleuteling inschakelen voor volumes met koppel paden
* Versleuteling inschakelen voor Linux-Vm's die zijn geconfigureerd met schijf striping (RAID) met behulp van mdadm
* Versleuteling inschakelen op virtuele Linux-machines met behulp van LVM voor gegevens schijven
* Versleuteling inschakelen op Windows-Vm's die zijn geconfigureerd met behulp van opslag ruimten
* Alle open bare Azure-regio's worden ondersteund

De oplossing biedt geen ondersteuning voor de volgende scenario's, functies en technologie in de release:

* IaaS-Vm's voor de Basic-laag
* Versleuteling uitschakelen op een OS-station voor Linux IaaS Vm's
* IaaS-Vm's die worden gemaakt met behulp van de klassieke methode voor het maken van VM'S
* Integratie met uw on-premises sleutel beheer service
* Azure Files (gedeeld bestands systeem), NFS (Network File System), dynamische volumes en Windows-Vm's die zijn geconfigureerd met RAID-systemen op basis van software


> [!NOTE]
> Versleuteling van Linux-besturingssysteem schijf wordt momenteel ondersteund in de volgende Linux-distributies: RHEL 7,2, CentOS 7,2 n en Ubuntu 16,04.
>
>

Deze functie zorgt ervoor dat alle gegevens op de schijven van de virtuele machine in de rest van Azure Storage zijn versleuteld.

#### <a name="resources"></a>Bronnen
* [Azure Disk Encryption voor virtuele machines met Windows en Linux IaaS](../../security/fundamentals/encryption-overview.md)

### <a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Vergelijking van Azure Disk Encryption, SSE en versleuteling aan de client zijde

#### <a name="iaas-vms-and-their-vhd-files"></a>IaaS Vm's en hun VHD-bestanden

Voor gegevens schijven die worden gebruikt door IaaS Vm's, wordt Azure Disk Encryption aanbevolen. Als u een virtuele machine met onbeheerde schijven maakt met behulp van een installatie kopie van Azure Marketplace, voert Azure een beschik bare [kopie](https://en.wikipedia.org/wiki/Object_copying) van de installatie kopie naar uw opslag account in azure Storage en is deze niet versleuteld, zelfs als u SSE hebt ingeschakeld. Nadat de virtuele machine is gemaakt en de installatie kopie is bijgewerkt, begint SSE met het versleutelen van de gegevens. Daarom kunt u het beste Azure Disk Encryption gebruiken op Vm's met niet-beheerde schijven die zijn gemaakt op basis van installatie kopieën in de Azure Marketplace als u deze volledig versleuteld wilt maken. Als u een virtuele machine met Managed Disks maakt, versleutelt SSE alle gegevens standaard met behulp van door het platform beheerde sleutels. 

Als u van on-premises een vooraf versleutelde virtuele machine in azure brengt, kunt u de versleutelings sleutels naar Azure Key Vault uploaden en de versleuteling voor die virtuele machine die u on-premises gebruikt, blijven gebruiken. Azure Disk Encryption is ingeschakeld om dit scenario af te handelen.

Als u een niet-versleutelde VHD van on-premises hebt, kunt u deze uploaden naar de galerie als een aangepaste installatie kopie en hiervan een virtuele machine inrichten. Als u dit doet met behulp van de Resource Manager-sjablonen, kunt u deze vragen om Azure Disk Encryption in te scha kelen wanneer de virtuele machine wordt opgestart.

Wanneer u een gegevens schijf toevoegt en koppelt aan de virtuele machine, kunt u Azure Disk Encryption op die gegevens schijf inschakelen. De gegevens schijf wordt lokaal eerst versleuteld en vervolgens wordt de laag van het klassieke implementatie model een langzame schrijf bewerking voor de opslag, zodat de opslag inhoud wordt versleuteld.

#### <a name="client-side-encryption"></a>Clientversleuteling
Versleuteling aan de client zijde is de veiligste methode voor het versleutelen van uw gegevens, omdat deze gegevens versleutelt vóór de door voer.  Hiervoor moet u echter wel code toevoegen aan uw toepassingen met behulp van opslag, wat u mogelijk niet wilt. In dergelijke gevallen kunt u HTTPS gebruiken om uw gegevens in transit te beveiligen. Zodra de gegevens Azure Storage bereikt, wordt deze door SSE versleuteld.

Met versleuteling aan de client zijde kunt u tabel entiteiten, wachtrij berichten en blobs versleutelen. 

Versleuteling aan de client zijde wordt volledig door de toepassing beheerd. Dit is de veiligste benadering, maar u moet wel programmatisch wijzigingen aanbrengen in uw toepassing en de belangrijkste beheer processen plaatsen. U gebruikt dit wanneer u de extra beveiliging tijdens de overdracht wilt gebruiken en u wilt dat de opgeslagen gegevens worden versleuteld.

Versleuteling aan de client zijde is meer belasting op de client en u moet hiervoor rekening met uw schaalbaarheids plannen, met name als u een grote hoeveelheid gegevens versleutelt en overdraagt.

#### <a name="storage-service-encryption-sse"></a>Storage Service Encryption (SSE)

SSE wordt beheerd door Azure Storage. SSE biedt geen beveiliging van de gegevens die onderweg zijn, maar versleutelt de gegevens die worden geschreven naar Azure Storage. SSE heeft geen invloed op de prestaties van Azure Storage.

U kunt elk soort gegevens van het opslag account versleutelen met behulp van SSE (blok-blobs, toevoeg-blobs, pagina-blobs, tabel gegevens, wachtrij gegevens en bestanden).

Als u een archief of bibliotheek van VHD-bestanden hebt die u gebruikt als basis voor het maken van nieuwe virtuele machines, kunt u een nieuw opslag account maken en vervolgens de VHD-bestanden uploaden naar dat account. Deze VHD-bestanden worden versleuteld door Azure Storage.

Als u Azure Disk Encryption hebt ingeschakeld voor de schijven in een VM, worden nieuw geschreven gegevens versleuteld door SSE en door Azure Disk Encryption.

## <a name="storage-analytics"></a>Storage Analytics
### <a name="using-storage-analytics-to-monitor-authorization-type"></a>Het verificatie type bewaken met behulp van Opslaganalyse
Voor elk opslag account kunt u Azure Opslaganalyse inschakelen voor het uitvoeren van logboek registratie en metrische gegevens voor opslag. Dit is een uitstekend hulp programma voor het controleren van de prestatie gegevens van een opslag account of het oplossen van problemen met een opslag account, omdat u prestatie problemen ondervindt.

Een deel van de gegevens die u in de logboeken van de opslag analyse kunt zien, is de verificatie methode die door iemand wordt gebruikt bij de toegang tot opslag. Met Blob Storage kunt u bijvoorbeeld zien of ze een Shared Access Signature of de sleutels voor het opslag account hebben gebruikt of dat de BLOB openbaar was.

Dit kan handig zijn als u de toegang tot opslag nauw keurig kunt beveiligen. In Blob Storage kunt u bijvoorbeeld alle containers instellen op privé en het gebruik van een SAS-service in uw toepassingen implementeren. Vervolgens kunt u de logboeken regel matig controleren om te zien of de blobs toegankelijk zijn via de sleutels voor het opslag account, die kunnen wijzen op een schending van de beveiliging, of als de blobs openbaar zijn, maar ze niet moeten worden gebruikt.

#### <a name="what-do-the-logs-look-like"></a>Hoe zien de logboeken eruitzien?
Nadat u de metrische gegevens van het opslag account en de logboek registratie via de Azure Portal hebt ingeschakeld, worden analytics data snel verzameld. De logboek registratie en metrische gegevens voor elke service zijn gescheiden. de logboek registratie wordt alleen geschreven wanneer er sprake is van activiteit in dat opslag account, terwijl de metrische gegevens elke minuut, elk uur of elke dag worden geregistreerd, afhankelijk van de wijze waarop u deze configureert.

De logboeken worden opgeslagen in blok-blobs in een container met de naam $logs in het opslag account. Deze container wordt automatisch gemaakt wanneer Opslaganalyse is ingeschakeld. Als deze container eenmaal is gemaakt, kunt u deze niet meer verwijderen.

In de $logs container bevindt zich een map voor elke service en zijn er submappen voor het jaar/maand/dag/uur. Onder uur worden de logboeken genummerd. De mapstructuur ziet er als volgt uit:

![Weer gave van logboek bestanden](./media/storage-security-guide/image1.png)

Elke aanvraag voor Azure Storage wordt geregistreerd. Hier volgt een moment opname van een logboek bestand, waarin de eerste velden worden weer gegeven.

![Moment opname van een logboek bestand](./media/storage-security-guide/image2.png)

U kunt zien dat u de logboeken kunt gebruiken om een wille keurig soort aanroepen naar een opslag account bij te houden.

#### <a name="what-are-all-of-those-fields-for"></a>Wat zijn al deze velden voor?
In de onderstaande bronnen vindt u een artikel met een lijst met veel velden in de logboeken en waarvoor ze worden gebruikt. Hier volgt de lijst met velden in de aangegeven volg orde:

![Moment opname van velden in een logboek bestand](./media/storage-security-guide/image3.png)

We zijn geïnteresseerd in de vermeldingen voor GetBlob en hoe ze zijn geautoriseerd. Daarom moeten we zoeken naar vermeldingen met het Operation-type ' Get-BLOB ' en de aanvraag status (vierde </sup> kolom) en het autorisatie type (achtste </sup> kolom) controleren.

In de eerste paar rijen in de bovenstaande vermelding is de aanvraag status "geslaagd" en het autorisatie type "Authenticated". Dit betekent dat de aanvraag is geautoriseerd met de sleutel van het opslag account.

#### <a name="how-is-access-to-my-blobs-being-authorized"></a>Hoe wordt toegang tot mijn blobs geautoriseerd?
Er zijn drie gevallen waarin we geïnteresseerd zijn.

1. De blob is openbaar en wordt geopend met behulp van een URL zonder Shared Access Signature. In dit geval is de aanvraag status ' AnonymousSuccess ' en is het autorisatie type ' anoniem '.

   1.0; 2015-11-17T02:01:29.0488963 Z; GetBlob **AnonymousSuccess**; 200; 124; 37; **anoniem**;; mijn opslag...
2. De blob is persoonlijk en is gebruikt met een Shared Access Signature. In dit geval is de aanvraag status "SASSuccess" en is het autorisatie type "SAS".

   1.0; 2015-11-16T18:30:05.6556115 Z; GetBlob **SASSuccess**; 200; 416; 64; **SAS**;; mijn opslag...
3. De blob is persoonlijk en de opslag sleutel is gebruikt om deze te openen. In dit geval is de aanvraag status "**geslaagd**" en is het autorisatie type "**Authenticated**".

   1.0; 2015-11-16T18:32:24.3174537 Z; GetBlob **Geslaagd**; 206; 59; 22; **geverifieerd**; mijn opslag...

U kunt de micro soft Message Analyzer gebruiken om deze logboeken te bekijken en te analyseren. Het bevat Zoek-en filter mogelijkheden. U kunt bijvoorbeeld zoeken naar instanties van GetBlob om te zien of het gebruik overeenkomt met wat u verwacht, dat wil zeggen, om ervoor te zorgen dat iemand niet op de juiste wijze toegang krijgt tot uw opslag account.

#### <a name="resources"></a>Bronnen
* [Storage Analytics](../storage-analytics.md)

  Dit artikel bevat een overzicht van opslag analyse en hoe u deze kunt inschakelen.
* [Opslaganalyse-logboek indeling](https://msdn.microsoft.com/library/azure/hh343259.aspx)

  In dit artikel ziet u de Opslaganalyse-logboek indeling en de beschik bare velden, inclusief verificatie-type, waarmee het type verificatie wordt aangegeven dat voor de aanvraag wordt gebruikt.
* [Een opslag account in de Azure Portal bewaken](../storage-monitor-storage-account.md)

  In dit artikel wordt beschreven hoe u de bewaking van metrische gegevens en logboek registratie voor een opslag account kunt configureren.
* [End-to-end-probleem oplossing met behulp van Azure Storage metrische gegevens en logboek registratie, AzCopy en Message Analyzer](../storage-e2e-troubleshooting.md)

  In dit artikel vindt u informatie over het oplossen van problemen met behulp van de Opslaganalyse en wordt uitgelegd hoe u micro soft Message Analyzer gebruikt.
* [Micro soft Message Analyzer-bedienings handleiding](https://technet.microsoft.com/library/jj649776.aspx)

  Dit artikel is de referentie voor de micro soft Message Analyzer en bevat koppelingen naar een zelf studie, Quick Start en functie samenvatting.

## <a name="cross-origin-resource-sharing-cors"></a>CORS (Cross-Origin Resource Sharing)
### <a name="cross-domain-access-of-resources"></a>Toegang tot meerdere domeinen van bronnen
Wanneer een webbrowser die in het ene domein wordt uitgevoerd, een HTTP-aanvraag voor een bron van een ander domein maakt, wordt dit een cross-Origin HTTP-aanvraag genoemd. Een HTML-pagina die wordt aangeboden vanuit contoso.com, maakt bijvoorbeeld een aanvraag voor een JPEG die wordt gehost op fabrikam.blob.core.windows.net. Uit veiligheids overwegingen beperken browsers de cross-Origin HTTP-aanvragen die worden geïnitieerd vanuit scripts, zoals Java script. Dit betekent dat als sommige Java script-code op een webpagina op contoso.com, JPEG op fabrikam.blob.core.windows.net verzoekt, de browser de aanvraag niet toestaat.

Wat doet dit met Azure Storage? Als u statische assets zoals JSON-of XML-gegevens bestanden opslaat in Blob Storage met behulp van een opslag account met de naam Fabrikam, wordt het domein voor de assets fabrikam.blob.core.windows.net en kan de contoso.com-webtoepassing geen toegang krijgen tot deze met behulp van Java script omdat de domeinen verschillend zijn. Dit geldt ook als u probeert een van de Azure Storage-services, zoals Table Storage, aan te roepen die de JSON-gegevens retour neren die moeten worden verwerkt door de Java script-client.

#### <a name="possible-solutions"></a>Mogelijke oplossingen
Een manier om dit op te lossen is het toewijzen van een aangepast domein, zoals ' storage.contoso.com ', aan fabrikam.blob.core.windows.net. Het probleem is dat u het aangepaste domein alleen aan één opslag account kunt toewijzen. Wat gebeurt er als de assets worden opgeslagen in meerdere opslag accounts?

Een andere manier om dit op te lossen is dat de webtoepassing fungeert als een proxy voor de opslag aanroepen. Dit betekent dat als u een bestand uploadt naar Blob Storage, de webtoepassing het lokaal schrijft en het vervolgens naar Blob Storage kopieert, of dat alles in het geheugen leest en het vervolgens naar Blob Storage schrijft. U kunt ook een speciale webtoepassing (zoals een web-API) schrijven die de bestanden lokaal uploadt en naar Blob Storage schrijft. In beide gevallen hebt u de mogelijkheid om die functie te gebruiken bij het bepalen van de schaalbaarheids behoeften.

#### <a name="how-can-cors-help"></a>Hoe kan CORS Help?
Met Azure Storage kunt u CORS inschakelen: cross-Origin resource delen. Voor elk opslag account kunt u domeinen opgeven die toegang hebben tot de bronnen in dat opslag account. In ons geval zoals hierboven beschreven, kunnen we CORS inschakelen op het fabrikam.blob.core.windows.net-opslag account en zo configureren dat toegang tot contoso.com wordt toegestaan. Vervolgens kan de webtoepassing contoso.com rechtstreeks toegang krijgen tot de resources in fabrikam.blob.core.windows.net.

Houd er rekening mee dat CORS toegang biedt, maar dat er geen verificatie wordt geboden, wat vereist is voor alle niet-open bare toegang tot opslag resources. Dit betekent dat u alleen toegang kunt krijgen tot blobs als deze openbaar zijn of dat u een Shared Access Signature hebt die u de juiste machtigingen geeft. Tabellen, wacht rijen en bestanden hebben geen open bare toegang en vereisen een SAS.

CORS is standaard uitgeschakeld voor alle services. U kunt CORS inschakelen met behulp van de REST API of de Storage-client bibliotheek om een van de methoden voor het instellen van het service beleid aan te roepen. Wanneer u dat doet, neemt u een CORS-regel op, die in XML is. Hier volgt een voor beeld van een CORS-regel die is ingesteld met behulp van de bewerking service-eigenschappen instellen voor de BLOB-service voor een opslag account. U kunt deze bewerking uitvoeren met de Storage-client bibliotheek of de REST-Api's voor Azure Storage.

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Wat betekent elke rij:

* **AllowedOrigins** Zo weet u welke niet-overeenkomende domeinen gegevens van de opslag service kunnen aanvragen en ontvangen. Dit betekent dat zowel contoso.com als fabrikam.com gegevens kunnen aanvragen van Blob Storage voor een specifiek opslag account. U kunt dit ook instellen op een Joker teken (\*) zodat alle domeinen toegang hebben tot aanvragen.
* **AllowedMethods** Dit is de lijst met methoden (HTTP-aanvraag bewerkingen) die kunnen worden gebruikt bij het maken van de aanvraag. In dit voor beeld zijn alleen PUT en GET toegestaan. U kunt dit instellen op een Joker teken (\*), zodat alle methoden kunnen worden gebruikt.
* **AllowedHeaders** Dit zijn de aanvraag headers die in het bron domein kunnen worden opgegeven bij het maken van de aanvraag. In dit voor beeld zijn alle meta gegevens headers die beginnen met x-MS-meta gegevens, x-MS-meta doel en x-MS-meta-ABC toegestaan. Het Joker teken (\*) geeft aan dat elke header die begint met het opgegeven voor voegsel is toegestaan.
* **ExposedHeaders** Hiermee wordt aangegeven welke antwoord headers door de browser moeten worden weer gegeven aan de aanvraag verlener. In dit voor beeld wordt elke koptekst die begint met ' x-MS-meta-' weer gegeven.
* **MaxAgeInSeconds** Dit is de maximale hoeveelheid tijd die een browser de aanvraag voor Preflight-opties in de cache opslaat. (Zie het eerste artikel hieronder voor meer informatie over de Preflight-aanvraag.)

#### <a name="resources"></a>Bronnen
Bekijk deze bronnen voor meer informatie over CORS en hoe u deze inschakelt.

* [CORS-ondersteuning (cross-Origin Resource Sharing) voor de Azure Storage services op Azure.com](../storage-cors-support.md)

  Dit artikel bevat een overzicht van CORS en het instellen van de regels voor de verschillende opslag Services.
* [CORS-ondersteuning (cross-Origin Resource Sharing) voor de Azure Storage services op MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

  Dit is de referentie documentatie voor CORS-ondersteuning voor de Azure Storage services. Dit heeft koppelingen naar artikelen die van toepassing zijn op elke opslag service en toont een voor beeld en legt elk element in het CORS-bestand voor.
* [Microsoft Azure Storage: CORS](https://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

  Dit is een koppeling naar het eerste blog artikel waarin CORS wordt aangekondigd en hoe u deze kunt gebruiken.

## <a name="frequently-asked-questions-about-azure-storage-security"></a>Veelgestelde vragen over Azure Storage beveiliging
1. **Hoe kan ik de integriteit verifiëren van de blobs die ik naar of van Azure Storage overdraagt als ik het HTTPS-protocol niet kan gebruiken?**

   Als u om een of andere reden HTTP in plaats van HTTPS moet gebruiken en u met blok-blobs werkt, kunt u MD5-controle gebruiken om te helpen bij het controleren van de integriteit van de overdracht van blobs. Dit helpt bij de beveiliging van netwerk/Trans Port-laag fouten, maar niet noodzakelijkerwijs met tussenliggende aanvallen.

   Als u HTTPS kunt gebruiken, dat beveiliging op transport niveau biedt, is het gebruik van MD5-controle overbodig en onnodig.

   Bekijk het [overzicht van Azure Blob MD5](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx)voor meer informatie.
2. **Wat is de FIPS-naleving voor de Amerikaanse overheid?**

   De Verenigde Staten-Federal Information Processing Standard (FIPS) definieert cryptografische algoritmen die zijn goedgekeurd voor gebruik door Amerikaanse federale overheid computer systemen voor de beveiliging van gevoelige gegevens. Het inschakelen van de FIPS-modus op een Windows-Server of-bureau blad vertelt u het besturings systeem dat alleen FIPS-gevalideerde cryptografische algoritmen moeten worden gebruikt. Als een toepassing niet-compatibele algoritmen gebruikt, wordt de toepassing onderbroken. With.NET Framework versie 4.5.2 of hoger, de toepassing schakelt automatisch de algoritmen voor crypto grafie in voor het gebruik van FIPS-compatibele algoritmen wanneer de computer zich in de FIPS-modus bevindt.

   Micro soft laat elke klant weten of de FIPS-modus moet worden ingeschakeld. We geloven dat er geen dwingende reden is voor klanten die geen wettelijke voor Schriften ondergaan om standaard de FIPS-modus in te scha kelen.

### <a name="resources"></a>Bronnen
* [Waarom we ' FIPS-modus ' niet meer aanraden](https://blogs.technet.microsoft.com/secguide/2014/04/07/why-were-not-recommending-fips-mode-anymore/)

  In dit blog artikel vindt u een overzicht van FIPS en wordt uitgelegd waarom de FIPS-modus niet standaard is ingeschakeld.
* [Validatie van FIPS 140](https://technet.microsoft.com/library/cc750357.aspx)

  Dit artikel bevat informatie over hoe micro soft-producten en cryptografische modules voldoen aan de FIPS-norm voor de Amerikaanse federale overheid.
* ["Systeem cryptografie: gebruik FIPS-compatibele algoritmen voor versleuteling, hashing en ondertekening van beveiligings instellingen in Windows XP en in latere versies van Windows](https://support.microsoft.com/kb/811833)

  In dit artikel vindt u informatie over het gebruik van FIPS-modus in oudere Windows-computers.
