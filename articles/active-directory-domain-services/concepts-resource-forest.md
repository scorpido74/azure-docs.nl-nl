---
title: Resourceforestconcepten voor Azure AD-domeinservices | Microsoft Documenten
description: Lees wat een bronforest is in Azure Active Directory Domain Services en hoe deze uw organisatie ten goede komen in een hybride omgeving met beperkte gebruikersverificatieopties of beveiligingsproblemen.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: e0e5dde246dbcd5e5cb2e4ae923872a59a539d87
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476403"
---
# <a name="resource-forest-concepts-and-features-for-azure-active-directory-domain-services"></a>Concepten en functies voor resourceforest voor Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) biedt een aanmeldingservaring voor verouderde, on-premises, bedrijfstoepassingen. Gebruikers, groepen en wachtwoordhashes van on-premises en cloudgebruikers worden gesynchroniseerd met het door Azure AD DS beheerde domein. Deze gesynchroniseerde wachtwoordhashes zijn wat gebruikers één set referenties geeft die ze kunnen gebruiken voor de on-premises AD DS, Office 365 en Azure Active Directory.

Hoewel ze veilig zijn en extra beveiligingsvoordelen bieden, kunnen sommige organisaties deze gebruikerswachtwoorden niet synchroniseren met Azure AD of Azure AD DS. Gebruikers in een organisatie weten mogelijk hun wachtwoord niet omdat ze alleen smartcardverificatie gebruiken. Deze beperkingen voorkomen dat sommige organisaties Azure AD DS gebruiken om on-premises klassieke toepassingen naar Azure te tillen en te verplaatsen.

Als u aan deze behoeften en beperkingen wilt voldoen, u een door Azure AD DS beheerd domein maken dat een bronforest gebruikt. In dit conceptuele artikel wordt uitgelegd wat forests zijn en hoe ze op andere bronnen vertrouwen om een veilige verificatiemethode te bieden. Azure AD DS-bronforests zijn momenteel in preview.

> [!IMPORTANT]
> Azure AD DS-bronforests ondersteunen momenteel geen Azure HDInsight of Azure Files. De standaard Azure AD DS-gebruikersforests ondersteunen beide aanvullende services.

## <a name="what-are-forests"></a>Wat zijn bossen?

Een *forest* is een logische constructie die wordt gebruikt door AD DS (Active Directory Domain Services) om een of meer *domeinen*te groeperen. De domeinen slaan vervolgens objecten op voor gebruikers of groepen en bieden verificatieservices.

In Azure AD DS bevat het forest slechts één domein. On-premises AD DS-forests bevatten vaak veel domeinen. In grote organisaties, vooral na fusies en overnames, u eindigen met meerdere on-premises forests die vervolgens meerdere domeinen bevatten.

Standaard wordt een door Azure AD DS beheerd domein gemaakt als *gebruikersforest.* Dit type forest synchroniseert alle objecten uit Azure AD, inclusief gebruikersaccounts die zijn gemaakt in een on-premises AD DS-omgeving. Gebruikersaccounts kunnen rechtstreeks verifiëren tegen het door Azure AD DS beheerde domein, bijvoorbeeld om u aan te melden bij een vm die is verbonden aan een domein. Een gebruikersforest werkt wanneer de wachtwoordhashes kunnen worden gesynchroniseerd en gebruikers geen exclusieve aanmeldingsmethoden zoals smartcardverificatie gebruiken.

In een Azure AD *DS-bronforest* verifiëren gebruikers zich via een eenrichtingsforestvertrouwensrelatie van hun on-premises AD DS. *trust* Met deze benadering worden de gebruikersobjecten en wachtwoordhashes niet gesynchroniseerd met Azure AD DS. De gebruikersobjecten en -referenties bestaan alleen in de on-premises AD DS. Met deze aanpak kunnen ondernemingen resources en toepassingsplatforms in Azure hosten die afhankelijk zijn van klassieke verificatie, zoals LDAPS, Kerberos of NTLM, maar eventuele verificatieproblemen of -problemen worden verwijderd. Azure AD DS-bronforests zijn momenteel in preview.

Resourceforests bieden ook de mogelijkheid om uw toepassingen één component tegelijk op te tillen en te verschuiven. Veel verouderde on-premises toepassingen zijn met meerdere lagen, vaak met behulp van een webserver of front-end en veel databasegerelateerde componenten. Deze lagen maken het moeilijk om de hele toepassing in één stap naar de cloud te tillen en te verschuiven. Met resourceforests u uw toepassing gefaseerd naar de cloud tillen, waardoor u uw toepassing gemakkelijker naar Azure verplaatsen.

## <a name="what-are-trusts"></a>Wat zijn vertrouwen?

Organisaties die meer dan één domein hebben, hebben vaak gebruikers nodig om toegang te krijgen tot gedeelde bronnen in een ander domein. Toegang tot deze gedeelde bronnen vereist dat gebruikers in het ene domein zich verifiëren naar een ander domein. Als u deze verificatie- en autorisatiemogelijkheden wilt bieden tussen clients en servers in verschillende domeinen, moet er een *vertrouwensrelatie* zijn tussen de twee domeinen.

Bij domeinvertrouwensrelaties vertrouwen de verificatiemechanismen voor elk domein op de verificaties die afkomstig zijn van het andere domein. Vertrouwensrelaties bieden gecontroleerde toegang tot gedeelde bronnen in een resourcedomein (het *vertrouwende* domein) door te controleren of binnenkomende verificatieaanvragen afkomstig zijn van een vertrouwde autoriteit (het *vertrouwde* domein). Vertrouwensrelaties fungeren als bruggen die alleen gevalideerde verificatieverzoeken toestaan om tussen domeinen te reizen.

Hoe een vertrouwensrelatie verificatieaanvragen doorgeeft, is afhankelijk van de manier waarop deze is geconfigureerd. Vertrouwensrelaties kunnen op een van de volgende manieren worden geconfigureerd:

* **One-way** - biedt toegang vanuit het vertrouwde domein tot bronnen in het vertrouwende domein.
* **Tweerichtings** - biedt toegang vanuit elk domein tot bronnen in het andere domein.

Vertrouwensrelaties worden ook geconfigureerd om aanvullende vertrouwensrelaties op een van de volgende manieren te verwerken:

* **Niet-transitief** - De vertrouwensrelatie bestaat alleen tussen de twee domeinen van de vertrouwenspartner.
* **Transitief** - Vertrouwen strekt zich automatisch uit tot alle andere domeinen die een van de partners vertrouwt.

In sommige gevallen worden vertrouwensrelaties automatisch vastgesteld wanneer domeinen worden gemaakt. Andere keren moet u een type vertrouwensrelatie kiezen en expliciet de juiste relaties tot stand brengen. De specifieke typen vertrouwensrelaties die worden gebruikt en de structuur van die vertrouwensrelaties zijn afhankelijk van de manier waarop de Directory-service van Active Directory is georganiseerd en of verschillende versies van Windows naast elkaar bestaan op het netwerk.

## <a name="trusts-between-two-forests"></a>Vertrouwen tussen twee bossen

U domeinvertrouwensrelaties binnen één forest uitbreiden naar een ander forest door handmatig een forestvertrouwensrelatie in één of twee richtingen te maken. Een forestvertrouwensrelatie is een transitieve vertrouwensrelatie die alleen bestaat tussen een foresthoofddomein en een tweede foresthoofddomein.

* Met een eenrichtingsforestvertrouwensrelatie kunnen alle gebruikers in één forest alle domeinen in het andere forest vertrouwen.
* Een tweerichtingsforestvertrouwensrelatie vormt een transitieve vertrouwensrelatie tussen elk domein in beide forests.

De transitiviteit van bostrusts is beperkt tot de twee bospartners. Het forestvertrouwen strekt zich niet uit tot extra forests die door een van de partners worden vertrouwd.

![Diagram van forestvertrouwensrelatie van Azure AD DS naar on-premises AD DS](./media/concepts-resource-forest/resource-forest-trust-relationship.png)

U verschillende domein- en forestvertrouwensconfiguraties maken, afhankelijk van de Active Directory-structuur van de organisatie. Azure AD DS ondersteunt alleen een eenrichtingsforestvertrouwensrelatie. In deze configuratie kunnen resources in Azure AD DS alle domeinen in een on-premises forest vertrouwen.

## <a name="supporting-technology-for-trusts"></a>Ondersteunende technologie voor trusts

Vertrouwensrelaties gebruiken verschillende services en functies, zoals DNS om domeincontrollers te vinden in partneringforests. Vertrouwensrelaties zijn ook afhankelijk van NTLM- en Kerberos-verificatieprotocollen en van windows-gebaseerde autorisatie- en toegangscontrolemechanismen om een beveiligde communicatie-infrastructuur te bieden in Active Directory-domeinen en -forests. De volgende services en functies ondersteunen succesvolle vertrouwensrelaties.

### <a name="dns"></a>DNS

AD DS heeft DNS nodig voor locatie en naamgeving van de domeincontroller (DC). De volgende ondersteuning van DNS is beschikbaar voor AD DS om succesvol te werken:

* Een naamomzettingsservice waarmee netwerkhosts en -services DC's kunnen vinden.
* Een naamgevingsstructuur waarmee een onderneming haar organisatiestructuur kan weergeven in de namen van de directoryservicedomeinen.

Een DNS-domeinnaamruimte wordt meestal geïmplementeerd die de AD DS-domeinnaamruimte weerspiegelt. Als er een bestaande DNS-naamruimte is vóór de AD DS-implementatie, wordt de DNS-naamruimte meestal verdeeld voor Active Directory en wordt een DNS-subdomein en -delegatie voor de Forestroot van Active Directory gemaakt. Voor elk Active Directory-onderliggend domein worden vervolgens extra DNS-domeinnamen toegevoegd.

DNS wordt ook gebruikt om de locatie van Active Directory DC's te ondersteunen. De DNS-zones worden gevuld met DNS-bronrecords waarmee netwerkhosts en -services Active Directory DC's kunnen vinden.

### <a name="applications-and-net-logon"></a>Toepassingen en netaanmelding

Beide toepassingen en de Net Logon-service zijn onderdelen van het Windows distributed security channel-model. Toepassingen die zijn geïntegreerd met Windows Server en Active Directory gebruiken verificatieprotocollen om te communiceren met de Net Logon-service, zodat een beveiligd pad kan worden vastgesteld waarover verificatie kan plaatsvinden.

### <a name="authentication-protocols"></a>Verificatieprotocollen

Active Directory DC's verifiëren gebruikers en toepassingen met behulp van een van de volgende protocollen:

* **Kerberos-verificatieprotocol voor versie 5**
    * Het Kerberos-versie 5-protocol is het standaardverificatieprotocol dat wordt gebruikt door on-premises computers met Windows en ondersteunende besturingssystemen van derden. Dit protocol is opgegeven in RFC 1510 en is volledig geïntegreerd met Active Directory, servermessage block (SMB), HTTP en remote procedure call (RPC), evenals de client- en servertoepassingen die deze protocollen gebruiken.
    * Wanneer het Kerberos-protocol wordt gebruikt, hoeft de server geen contact op te nemen met de DC. In plaats daarvan krijgt de client een ticket voor een server door er een aan te vragen bij een DC in het serveraccountdomein. De server valideert vervolgens het ticket zonder enige andere autoriteit te raadplegen.
    * Als een computer die betrokken is bij een transactie het Kerberos versie 5-protocol niet ondersteunt, wordt het NTLM-protocol gebruikt.

* **NTLM-verificatieprotocol**
    * Het NTLM-protocol is een klassiek netwerkverificatieprotocol dat wordt gebruikt door oudere besturingssystemen. Om compatibiliteitsredenen wordt het door Active Directory-domeinen gebruikt om netwerkverificatieaanvragen te verwerken die afkomstig zijn van toepassingen die zijn ontworpen voor eerdere Windows-clients en -servers en besturingssystemen van derden.
    * Wanneer het NTLM-protocol wordt gebruikt tussen een client en een server, moet de server contact opnemen met een domeinverificatieservice op een DC om de clientreferenties te verifiëren. De server verifieert de client door de clientreferenties door te sturen naar een DC in het domein van het clientaccount.
    * Wanneer twee Active Directory-domeinen of -forests zijn verbonden door een vertrouwensrelatie, kunnen verificatieverzoeken die met behulp van deze protocollen worden ingediend, worden doorgestuurd om toegang te bieden tot bronnen in beide forests.

## <a name="authorization-and-access-control"></a>Autorisatie- en toegangscontrole

Autorisatie- en vertrouwenstechnologieën werken samen om een beveiligde communicatie-infrastructuur te bieden in Active Directory-domeinen of -forests. Autorisatie bepaalt welk toegangsniveau een gebruiker heeft om resources in een domein. Vertrouwensrelaties vergemakkelijken cross-domain autorisatie van gebruikers door het verstrekken van een pad voor het verifiëren van gebruikers in andere domeinen, zodat hun verzoeken om gedeelde bronnen in die domeinen kunnen worden geautoriseerd.

Wanneer een verificatieverzoek in een vertrouwensdomein wordt gevalideerd door het vertrouwde domein, wordt deze doorgegeven aan de doelbron. De doelbron bepaalt vervolgens of de specifieke aanvraag van de gebruiker, service of computer in het vertrouwde domein moet worden geautoriseerd op basis van de configuratie van het toegangscontrolebeheer.

Vertrouwensrelaties bieden dit mechanisme om verificatieaanvragen te valideren die worden doorgegeven aan een vertrouwend domein. Toegangsbeheermechanismen op de resourcecomputer bepalen het uiteindelijke toegangsniveau dat wordt verleend aan de aanvrager in het vertrouwde domein.

## <a name="next-steps"></a>Volgende stappen

Zie [Hoe werken forestvertrouwensrelaties in Azure AD DS voor][concepts-trust] meer informatie over vertrouwensrelaties?

Zie Een beheerd [Azure AD DS-beheerd domein maken en configureren][tutorial-create-advanced]om aan de slag te gaan met het maken van een door Azure AD DS beheerd domein met een resourceforest. U vervolgens [een uitgaande forestvertrouwensrelatie maken naar een on-premises domein (voorbeeld).][create-forest-trust]

<!-- LINKS - INTERNAL -->
[concepts-trust]: concepts-forest-trust.md
[tutorial-create-advanced]: tutorial-create-instance-advanced.md
[create-forest-trust]: tutorial-create-forest-trust.md
