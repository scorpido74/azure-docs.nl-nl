---
title: Azure Multi-Factor Authentication-gegevensresidentie
description: Ontdek welke persoonlijke en organisatorische gegevens Azure Multi-Factor Authentication opslaat over u en uw gebruikers en welke gegevens binnen het land van herkomst blijven.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309799"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Gegevensresidentie en klantgegevens voor Azure Multi-Factor Authentication

Klantgegevens worden door Azure AD opgeslagen op een geografische locatie op basis van het adres dat uw organisatie biedt wanneer u zich abonneert op een Microsoft Online-service zoals Office 365 en Azure. Voor informatie over waar uw klantgegevens worden opgeslagen, u het gedeelte [Waar bevinden?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Cloudgebaseerde Azure Multi-Factor Authentication en Azure Multi-Factor Authentication Server verwerken en slaan bepaalde hoeveelheden persoonlijke gegevens en organisatiegegevens op. In dit artikel wordt beschreven wat en waar gegevens worden opgeslagen.

De volgende multi-factor authenticatie activiteiten zijn momenteel afkomstig uit Amerikaanse datacenters, behalve waar vermeld:

* Tweestapsverificatie met telefoongesprekken of sms'jes is meestal afkomstig uit Amerikaanse datacenters en wordt doorgestuurd door wereldwijde providers.
    * Gebruikersverificatieverzoeken voor algemene doeleinden uit andere regio's, zoals Europa of Australië, worden momenteel verwerkt door datacenters in die regio. Andere gebeurtenissen, zoals selfservicewachtwoordresets, Azure B2C-gebeurtenissen of hybride scenario's met NPS-extensie of AD FS-adapter, worden momenteel allemaal verwerkt door Amerikaanse datacenters.
* Pushmeldingen met de Microsoft Authenticator-app zijn afkomstig uit Amerikaanse datacenters. Bovendien kunnen apparaatleveranciersspecifieke services ook vanuit verschillende regio's in het spel komen.
* OATH-codes worden momenteel meestal gevalideerd in de VS.
    * Nogmaals, gebruikersverificatiegebeurtenissen voor algemene doeleinden die afkomstig zijn uit andere regio's, zoals Europa of Australië, worden verwerkt door datacenters in die regio. Extra evenementen worden momenteel verwerkt door Amerikaanse datacenters.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Persoonlijke gegevens die zijn opgeslagen door Azure Multi-Factor Authentication

Persoonlijke gegevens zijn informatie op gebruikersniveau die aan een specifieke persoon is gekoppeld. De volgende gegevensarchieven bevatten persoonlijke gegevens:

* Geblokkeerde gebruikers
* Gebruikers omzeild
* Aanvragen voor tokenwijziging van Microsoft Authenticator-apparaat
* Activiteitsrapporten voor meervoudige verificatie
* Microsoft Authenticator-activeringen

Deze informatie wordt 90 dagen bewaard.

Azure Multi-Factor Authentication registreert geen persoonlijke gegevens zoals gebruikersnaam, telefoonnummer of IP-adres, maar er is een *UserObjectId* die multi-factor authenticatie pogingen aan gebruikers identificeert. Loggegevens worden 30 dagen bewaard.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Voor Azure public clouds, met uitzondering van Azure B2C-verificatie, NPS-extensie en Windows Server 2016 of 2019 AD FS-adapter, worden de volgende persoonlijke gegevens opgeslagen:

| Gebeurtenistype                           | Gegevensarchieftype |
|--------------------------------------|-----------------|
| OATH-token                           | In logboeken voor verificatie met meerdere factoren     |
| One-way SMS                          | In logboeken voor verificatie met meerdere factoren     |
| Spraakoproep                           | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld |
| Microsoft Authenticator-melding | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld<br />Aanvragen wijzigen wanneer microsoft authenticator-apparaattoken wordt gewijzigd |

> [!NOTE]
> Het gegevensarchief Multi-Factor Authentication wordt in de Verenigde Staten opgeslagen voor alle clouds, ongeacht de regio die de verificatieaanvraag verwerkt. Microsoft Azure Germany, Microsoft Azure Beheerd door 21Vianet en Microsoft Government Cloud hebben hun eigen onafhankelijke gegevensopslag gescheiden van public cloud regio data stores, maar deze gegevens worden altijd opgeslagen in de Verenigde Staten.

Voor Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure, 21Vianet, Azure B2C-authenticatie, NPS-extensie en Windows Server 2016 of 2019 AD FS-adapter worden de volgende persoonlijke gegevens opgeslagen:

| Gebeurtenistype                           | Gegevensarchieftype |
|--------------------------------------|-----------------|
| OATH-token                           | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie |
| One-way SMS                          | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie |
| Spraakoproep                           | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld |
| Microsoft Authenticator-melding | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld<br />Aanvragen wijzigen wanneer microsoft authenticator-apparaattoken wordt gewijzigd |

### <a name="multi-factor-authentication-server"></a>Multi-Factor Authentication-server

Als u Azure Multi-Factor Authentication Server implementeert en uitvoert, worden de volgende persoonlijke gegevens opgeslagen:

> [!IMPORTANT]
> Vanaf 1 juli 2019 biedt Microsoft geen Multi-Factor Authentication Server meer aan voor nieuwe implementaties. Nieuwe klanten die multi-factor authenticatie van hun gebruikers willen vereisen, moeten azure multi-factor authenticatie in de cloud gebruiken. Bestaande klanten die multi-factor authenticatieserver vóór 1 juli hebben geactiveerd, kunnen de nieuwste versie, toekomstige updates downloaden en activeringsreferenties genereren zoals gewoonlijk.

| Gebeurtenistype                           | Gegevensarchieftype |
|--------------------------------------|-----------------|
| OATH-token                           | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie |
| One-way SMS                          | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie |
| Spraakoproep                           | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld |
| Microsoft Authenticator-melding | In logboeken voor verificatie met meerdere factoren<br />Gegevensarchief voor activiteitsrapport voor multi-factorverificatie<br />Geblokkeerde gebruikers als fraude wordt gemeld<br />Aanvragen wijzigen wanneer microsoft authenticator-apparaattoken wordt gewijzigd |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Organisatiegegevens die zijn opgeslagen door Azure Multi-Factor Authentication

Organisatiegegevens zijn informatie op tenantniveau die de configuratie of de instelling van de omgeving kan blootstellen. Tenantinstellingen van de volgende Azure portal Multi-Factor Authentication-pagina's kunnen organisatorische gegevens opslaan, zoals vergrendelingsdrempels of beller-ID-gegevens voor binnenkomende telefoonverificatieverzoeken:

* Accountuitsluiting
* Fraudewaarschuwing
* Meldingen
* Instellingen voor bellen

En voor Azure Multi-Factor Authentication Server kunnen de volgende Azure-portalpagina's organisatiegegevens bevatten:

* Serverinstellingen
* Eenmalige bypass
* Caching-regels
* Status van verificatieserver met meerdere factoren

## <a name="log-data-location"></a>Locatie van logboekgegevens

Waar logboekgegevens worden opgeslagen, is afhankelijk van in welke regio ze worden verwerkt. De meeste regio's hebben native Azure Multi-Factor Authentication-mogelijkheden, dus logboekgegevens worden opgeslagen in dezelfde regio die de aanvraag voor meervoudige verificatie verwerkt. In regio's zonder native Azure Multi-Factor Authentication-ondersteuning worden ze onderhouden door de regio's van de Verenigde Staten of Europa en worden logboekgegevens opgeslagen in dezelfde regio die de aanvraag voor multifactorverificatie verwerkt.

Sommige kernverificatielogboekgegevens worden alleen opgeslagen in de Verenigde Staten. Microsoft Azure Germany en Microsoft Azure Beheerd door 21Vianet worden altijd opgeslagen in hun respectievelijke cloud. Microsoft Government Cloud log gegevens worden altijd opgeslagen in de Verenigde Staten.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Multi-Factor Authentication User Data Collection](howto-mfa-reporting-datacollection.md)voor meer informatie over welke gebruikersgegevens worden verzameld door azure multi-factor authentication in de cloud en Azure Multi-Factor Authentication Server.
