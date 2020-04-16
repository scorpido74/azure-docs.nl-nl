---
title: Wat is Azure Sleutelkluis? | Microsoft Docs
description: Ontdek hoe Azure Key Vault cryptografische sleutels en geheimen beschermt die cloudtoepassingen en -services gebruiken.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: 14eda137d386146d96b6b9aa54e1ed57021db19d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432136"
---
# <a name="azure-key-vault-basic-concepts"></a>Basisconcepten van Azure Key Vault

Azure Key Vault is een hulpprogramma voor het veilig opslaan en openen van geheimen. Een geheim is alles waartoe u de toegang streng wilt beheren, zoals API-sleutels, wachtwoorden of certificaten. Een kluis is een logische groep geheimen.

Hier zijn andere belangrijke termen:

- **Tenant**: een tenant is de organisatie die een specifiek exemplaar van Microsoft-cloudservices in eigendom heeft en beheert. Het wordt meestal gebruikt om te verwijzen naar de set Azure- en Office 365-services voor een organisatie.

- **Kluiseigenaar**: een kluiseigenaar kan een sleutelkluis maken en heeft er volledige toegang toe en controle over. De eigenaar van de kluis kan ook controles instellen om vast te leggen wie toegang heeft tot geheimen en sleutels. Beheerders kunnen de levenscyclus van sleutels beheren. Ze kunnen een nieuwe versie van de sleutel instellen, een back-up maken en gerelateerde taken uitvoeren.

- **Kluisconsument**: een kluisconsument kan acties uitvoeren op de elementen in de sleutelkluis wanneer de eigenaar van de kluis toegang verleent aan de consument. De beschikbare acties zijn afhankelijk van de verleende machtigingen.

- **Resource**: een resource is een beheerbaar item dat beschikbaar is via Azure. Veelvoorkomende voorbeelden zijn virtuele machine, opslagaccount, web-app, database en virtueel netwerk. Er zijn er nog veel meer.

- **Resourcegroep**: een resourcegroep is een container met gerelateerde resources voor een Azure-oplossing. De resourcegroep kan alle resources voor de oplossing bevatten of enkel de resources die u als groep wilt beheren. U bepaalt hoe resources worden toegewezen aan resourcegroepen op basis van wat voor uw organisatie het meest zinvol is.

- **Serviceprincipal**: Een Azure-serviceprincipal is een beveiligingsidentiteit die door gebruikers gemaakte apps, services en automatiseringshulpprogramma's gebruiken om toegang te krijgen tot specifieke Azure-bronnen. Zie het als een "gebruikersidentiteit" (gebruikersnaam en wachtwoord of certificaat) met een specifieke rol en streng gecontroleerde machtigingen. Anders dan een algemene gebruikers-id, hoeft een service-principal slechts enkele specifieke handelingen uit te kunnen voeren. Het verbetert de beveiliging als u het alleen het minimale machtigingsniveau verleent dat het nodig heeft om zijn beheertaken uit te voeren.

- [Azure Active Directory (Azure AD)](../../active-directory/active-directory-whatis.md): Azure AD is de Active Directory-service voor een tenant. Elke adreslijst heeft een of meer domeinen. Aan een directory kunnen vele abonnementen zijn gekoppeld, maar slechts één tenant.

- **Tenant-id voor Azure AD**: een tenant-id is een unieke manier om een Azure AD-exemplaar in een Azure-abonnement te identificeren.

- **Beheerde identiteiten**: Azure Key Vault biedt een manier om referenties en andere sleutels en geheimen veilig op te slaan, maar uw code moet worden geverifieerd naar Key Vault om ze op te halen. Het gebruik van een beheerde identiteit maakt het oplossen van dit probleem eenvoudiger door Azure-services een automatisch beheerde identiteit te geven in Azure AD. U kunt deze identiteit gebruiken voor verificatie bij Key Vault bij alle services die ondersteuning bieden voor Microsoft Azure Active Directory-verificatie, zonder dat u referenties in uw code hoeft te hebben. Zie de volgende afbeelding en het [overzicht van beheerde identiteiten voor Azure-bronnen voor](../../active-directory/managed-identities-azure-resources/overview.md)meer informatie.

    ![Diagram met de werking van beheerde identiteiten voor Azure-resources](../media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Verificatie
Als u bewerkingen met Key Vault wilt uitvoeren, moet u deze eerst verifiëren. Er zijn drie manieren om te verifiëren voor Key Vault:

- [Beheerde identiteiten voor Azure-resources:](../../active-directory/managed-identities-azure-resources/overview.md)Wanneer u een app implementeert op een virtuele machine in Azure, u een identiteit toewijzen aan uw virtuele machine die toegang heeft tot Key Vault. U ook identiteiten toewijzen aan [andere Azure-bronnen.](../../active-directory/managed-identities-azure-resources/overview.md) Het voordeel van deze aanpak is dat de app of service de rotatie van het eerste geheim niet beheert. Azure roteert automatisch de identiteit. Wij raden deze aanpak aan als best practice. 
- **Serviceprincipal en certificaat:** U een serviceprincipal en een bijbehorend certificaat gebruiken dat toegang heeft tot Key Vault. We raden deze aanpak niet aan omdat de eigenaar of ontwikkelaar van de toepassing het certificaat moet roteren.
- **Service principal en geheim:** Hoewel u een serviceprincipal en een geheim gebruiken om te authenticeren naar Key Vault, raden we dit niet aan. Het is moeilijk om automatisch het bootstrap-geheim te draaien dat wordt gebruikt om te verifiëren naar Key Vault.


## <a name="key-vault-roles"></a>Rollen van Key Vault

Gebruik de volgende tabel om beter te begrijpen hoe Key Vault u kan helpen om aan de behoeften van ontwikkelaars en beveiligingsadministrators te voldoen.

| Rol | Probleemformulering | Opgelost door Azure Key Vault |
| --- | --- | --- |
| Ontwikkelaar voor een Azure-toepassing |"Ik wil een toepassing voor Azure schrijven die sleutels gebruikt voor ondertekening en versleuteling. Maar ik wil dat deze sleutels extern zijn van mijn toepassing, zodat de oplossing geschikt is voor een applicatie die geografisch verspreid is. <br/><br/>Ik wil dat deze sleutels en geheimen worden beveiligd, zonder dat ik de code zelf hoef te schrijven. Ik wil ook dat deze sleutels en geheimen gemakkelijk te gebruiken zijn vanuit mijn applicaties, met optimale prestaties." |√ De sleutels worden opgeslagen in een kluis en wanneer dit nodig is, aangeroepen via een URI.<br/><br/> √ De sleutels worden beveiligd door Azure. Hiervoor wordt gebruikgemaakt van algoritmen, sleutellengten en HSM's die voldoen aan de industriestandaard.<br/><br/> √ Sleutels worden verwerkt in HSM's die zich in dezelfde Azure-datacenters bevinden als de toepassingen. Deze methode biedt betere betrouwbaarheid en minder latentie dan wanneer de sleutels zich op een afzonderlijke locatie bevinden, zoals on-premises. |
| SaaS-ontwikkelaar (Software as a Service) |"Ik wil niet de verantwoordelijkheid of mogelijke aansprakelijkheid voor de huurder sleutels en geheimen van mijn klanten. <br/><br/>Ik wil dat klanten hun sleutels bezitten en beheren, zodat ik me kan concentreren op het doen waar ik goed in ben, namelijk het leveren van de belangrijkste softwarefuncties." |√ Klanten kunnen hun eigen sleutels in Azure importeren en beheren. Wanneer een SaaS-toepassing cryptografische bewerkingen moet uitvoeren met behulp van de sleutels van klanten, voert Key Vault deze bewerkingen uit namens de toepassing. De toepassing ziet de sleutels van de klanten niet. |
| Chief Security Officer (CSO) |"Ik wil weten of onze applicaties voldoen aan FIPS 140-2 Level 2 HSM's voor veilig sleutelbeheer. <br/><br/>Ik wil ervoor zorgen dat mijn organisatie de controle heeft over de levenscyclus van de sleutel en het sleutelgebruik kan bewaken. <br/><br/>En hoewel we meerdere Azure-services en -bronnen gebruiken, wil ik de sleutels beheren vanaf één locatie in Azure." |√ HSM's zijn FIPS 140-2 Level 2-gevalideerde modules.<br/><br/>√ Key Vault is zodanig ontworpen dat Microsoft uw sleutels niet kan zien of extraheren.<br/><br/>√ Sleutelgebruik wordt vrijwel in realtime vastgelegd.<br/><br/>√ De kluis biedt één interface, ongeacht het aantal kluizen dat u in Azure hebt, welke regio's ze ondersteunen en door welke toepassingen ze worden gebruikt. |

Iedereen met een Azure-abonnement kan sleutelkluizen maken en gebruiken. Hoewel Key Vault ontwikkelaars en beveiligingsbeheerders ten goede komt, kan het worden geïmplementeerd en beheerd door de beheerder van een organisatie die andere Azure-services beheert. Deze beheerder kan zich bijvoorbeeld aanmelden met een Azure-abonnement, een kluis maken voor de organisatie waarin sleutels moeten worden opgeslagen en vervolgens verantwoordelijk zijn voor operationele taken zoals deze:

- Een sleutel of geheim maken of importeren.
- Een sleutel of geheim intrekken of verwijderen.
- Gebruikers of toepassingen machtigingen verlenen voor toegang tot de sleutelkluis, zodat ze de sleutels en geheimen in de kluis kunnen beheren of gebruiken.
- Sleutelgebruik configureren (bijvoorbeeld ondertekenen of versleutelen).
- Sleutelgebruik bewaken.

Deze beheerder geeft ontwikkelaars vervolgens URL's om te bellen vanuit hun toepassingen. Deze beheerder geeft ook belangrijke informatie over het registreren van het gebruik aan de beveiligingsbeheerder. 

![Overzicht van hoe Azure Key Vault werkt][1]

Ontwikkelaars kunnen de sleutels ook rechtstreeks beheren door gebruik te maken van API's. Zie [Ontwikkelaarshandleiding voor Key Vault](developers-guide.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [beveiligen van uw kluis).](secure-your-key-vault.md)

<!--Image references-->
[1]: ../media/key-vault-whatis/AzureKeyVault_overview.png
Azure Sleutelkluis is beschikbaar in de meeste regio's. Zie de pagina [Prijzen van Key Vault](https://azure.microsoft.com/pricing/details/key-vault/) voor meer informatie.
