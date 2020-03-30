---
title: PaaS-toepassingen beveiligen met Azure Storage | Microsoft Documenten
description: Meer informatie over de aanbevolen procedures voor Azure Storage-beveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: tomsh
ms.openlocfilehash: 675e10101d01d831aad7652c70cbfcf320085a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70999173"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-storage"></a>Aanbevolen procedures voor het beveiligen van PaaS-web- en mobiele toepassingen met Azure Storage
In dit artikel bespreken we een verzameling aanbevolen procedures voor Azure Storage-beveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen (Platform-as-a-Service). Deze best practices zijn afgeleid van onze ervaring met Azure en de ervaringen van klanten zoals uzelf.

Azure maakt het mogelijk om opslag te implementeren en te gebruiken op manieren die on-premises niet gemakkelijk haalbaar zijn. Met Azure-opslag u met relatief weinig moeite hoge niveaus van schaalbaarheid en beschikbaarheid bereiken. Azure Storage is niet alleen de basis voor Windows en Linux Azure Virtual Machines, het kan ook grote gedistribueerde toepassingen ondersteunen.

Azure Storage biedt de volgende vier services: Blob-opslag, tabelopslag, wachtrijopslag en bestandsopslag. Zie [Inleiding tot Microsoft Azure Storage](/azure/storage/common/storage-introduction)voor meer informatie.

De [beveiligingshandleiding voor Azure Storage](/azure/storage/common/storage-security-guide) is een geweldige bron voor gedetailleerde informatie over Azure Storage en beveiliging. Deze best practices artikel adressen op een hoog niveau een aantal van de concepten gevonden in de security guide en links naar de security guide, evenals andere bronnen, voor meer informatie.

In dit artikel worden de volgende aanbevolen procedures toegepast:

- Shared Access Signatures (SAS)
- Toegangsbeheer op basis van rollen (RBAC)
- Clientzijdeversleuteling voor waardevolle gegevens
- Storage Service Encryption


## <a name="use-a-shared-access-signature-instead-of-a-storage-account-key"></a>Een handtekening voor gedeelde toegang gebruiken in plaats van een opslagaccountsleutel
Toegangscontrole is van cruciaal belang. Azure genereert twee 512-bits opslagaccountsleutels (SAK's) wanneer u een opslagaccount maakt om u te helpen de toegang tot Azure Storage te beheren. Het niveau van de belangrijkste redundantie maakt het mogelijk voor u om onderbrekingen van de service te voorkomen tijdens routinematige sleutelrotatie. 

Opslagtoegangssleutels zijn geheimen met hoge prioriteit en mogen alleen toegankelijk zijn voor degenen die verantwoordelijk zijn voor toegangsbeheer voor opslag. Als de verkeerde mensen toegang krijgen tot deze sleutels, hebben ze volledige controle over de opslag en kunnen ze bestanden vervangen, verwijderen of toevoegen aan opslag. Dit omvat malware en andere soorten inhoud die uw organisatie of uw klanten in gevaar kunnen brengen.

U hebt nog steeds een manier nodig om toegang te bieden tot objecten in opslag. Om meer gedetailleerde toegang te bieden, u profiteren van de gedeelde toegangshandtekening (SAS). De SAS maakt het mogelijk om specifieke objecten in opslag te delen voor een vooraf gedefinieerd tijdsinterval en met specifieke machtigingen. Met een handtekening voor gedeelde toegang u definiëren:

- Het interval waarop de SAS geldig is, inclusief de begintijd en de vervaldatum.
- De door de SAS verleende machtigingen. Een SAS op een blob kan bijvoorbeeld een gebruiker lees- en schrijfmachtigingen voor die blob verlenen, maar geen machtigingen verwijderen.
- Een optioneel IP-adres of een reeks IP-adressen waarvan Azure Storage de SAS accepteert. U bijvoorbeeld een reeks IP-adressen opgeven die behoren tot uw organisatie. Dit biedt een andere mate van beveiliging voor uw SAS.
- Het protocol waarover Azure Storage de SAS accepteert. U deze optionele parameter gebruiken om de toegang tot clients met HTTPS te beperken.

Met SAS u inhoud delen op de manier waarop u deze wilt delen zonder uw opslagaccountsleutels weg te geven. Het altijd gebruiken van SAS in uw toepassing is een veilige manier om uw opslagbronnen te delen zonder uw opslagaccountsleutels in gevaar te brengen.

Zie [Handtekeningen voor gedeelde toegang gebruiken](/azure/storage/common/storage-dotnet-shared-access-signature-part-1)voor meer informatie over handtekening voor gedeelde toegang. 

## <a name="use-role-based-access-control"></a>Op rollen gebaseerd toegangsbeheer gebruiken
Een andere manier om de toegang te beheren is het gebruik [van role-based access control](/azure/role-based-access-control/overview) (RBAC). Met RBAC richt u zich op het geven van de exacte machtigingen die werknemers nodig hebben, op basis van de noodzaak om te weten en de minste privilege beveiligingsprincipes. Te veel machtigingen kunnen een account blootstellen aan aanvallers. Te weinig machtigingen betekent dat werknemers hun werk niet efficiënt kunnen doen. RBAC helpt dit probleem op te lossen door fijnkorrelig toegangsbeheer voor Azure aan te bieden. Dit is noodzakelijk voor organisaties die beveiligingsbeleid willen afdwingen voor gegevenstoegang.

U ingebouwde RBAC-rollen in Azure gebruiken om bevoegdheden toe te wijzen aan gebruikers. Gebruik bijvoorbeeld Opslagaccountinzenderopdragers voor cloudoperators die opslagaccounts en de rol van de klassieke opslagaccountbijdrage moeten beheren om klassieke opslagaccounts te beheren. Voor cloudoperators die VM's moeten beheren, maar niet het virtuele netwerk- of opslagaccount waarmee ze zijn verbonden, u deze toevoegen aan de rol Virtuele machineinzender.

Organisaties die het beheer van gegevenstoegangsniet afdwingen met behulp van mogelijkheden zoals RBAC, geven mogelijk meer bevoegdheden dan nodig is voor hun gebruikers. Dit kan leiden tot data compromis door dat sommige gebruikers toegang tot gegevens die ze niet zouden moeten hebben in de eerste plaats.

Zie voor meer informatie over RBAC:

- [Toegang beheren met op rollen gebaseerd toegangsbeheer en Azure Portal](/azure/role-based-access-control/role-assignments-portal)
- [Ingebouwde rollen voor Azure-resources](/azure/role-based-access-control/built-in-roles)
- [Veiligheidsgids voor Azure Storage](/azure/storage/common/storage-security-guide) 

## <a name="use-client-side-encryption-for-high-value-data"></a>Client-side encryptie gebruiken voor waardevolle gegevens
Met versleuteling aan de clientzijde u gegevens die onderweg worden verzonden programmatisch versleutelen voordat u naar Azure Storage uploadt en gegevens programmatisch decoderen wanneer u deze ophaalt. Dit biedt versleuteling van gegevens in transit, maar het biedt ook encryptie van gegevens in rust. Client-side encryptie is de meest veilige methode voor het versleutelen van uw gegevens, maar het vereist wel dat u programmatische wijzigingen aan te brengen in uw toepassing en belangrijke beheerprocessen op zijn plaats te zetten.

Client-side encryptie stelt u ook in staat om de uitsluitende controle over uw encryptiesleutels. U uw eigen encryptiesleutels genereren en beheren. Het maakt gebruik van een enveloptechniek waarbij de Azure-opslagclientbibliotheek een inhoudsversleutelingssleutel (CEK) genereert die vervolgens wordt verpakt (versleuteld) met behulp van de sleutelversleutelingssleutel (KEK). De KEK wordt geïdentificeerd door een sleutel-id en kan een asymmetrisch sleutelpaar of een symmetrische sleutel zijn en lokaal worden beheerd of opgeslagen in [Azure Key Vault.](/azure/key-vault/key-vault-overview)

Client-side encryptie is ingebouwd in de Java en de .NET storage client bibliotheken. Raadpleeg [Client-side encryptie en Azure Key Vault voor Microsoft Azure Storage](/azure/storage/common/storage-client-side-encryption) voor informatie over het versleutelen van gegevens binnen clienttoepassingen en het genereren en beheren van uw eigen versleutelingssleutels.

## <a name="enable-storage-service-encryption-for-data-at-rest"></a>Storage Service-versleuteling inschakelen voor gegevens in rust
Wanneer [Storage Service Encryption](/azure/storage/common/storage-service-encryption) for File storage is ingeschakeld, worden de gegevens automatisch versleuteld met AES-256-versleuteling. Microsoft verwerkt alle versleuteling, decryptie en sleutelbeheer. Deze functie is beschikbaar voor LRS- en GRS-redundantietypen.

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt u kennis gemaakt met een verzameling aanbevolen procedures voor Azure Storage-beveiliging voor het beveiligen van uw PaaS-web- en mobiele toepassingen. Zie voor meer informatie over het beveiligen van uw PaaS-implementaties:

- [PaaS-implementaties beveiligen](paas-deployments.md)
- [PaaS-web- en mobiele toepassingen beveiligen met Azure App Services](paas-applications-using-app-services.md)
- [PaaS-databases beveiligen in Azure](paas-applications-using-sql.md)
