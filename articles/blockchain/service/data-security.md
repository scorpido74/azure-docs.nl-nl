---
title: Azure Blockchain-servicebeveiliging
description: Azure Blockchain Service-concepten voor toegang tot gegevens en beveiliging
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 06bf4e0fa4037b07505a4f816fc7af56c14576d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75982233"
---
# <a name="azure-blockchain-service-security"></a>Azure Blockchain-servicebeveiliging

Azure Blockchain Service maakt gebruik van verschillende Azure-mogelijkheden om uw gegevens veilig en beschikbaar te houden. Gegevens worden beveiligd met isolatie, versleuteling en verificatie.

## <a name="isolation"></a>Isolatie

Azure Blockchain Service-resources worden geïsoleerd in een privé virtueel netwerk. Elke transactie en validatieknooppunt is een virtuele machine (VM). VM's in één virtueel netwerk kunnen niet rechtstreeks communiceren met VM's in een ander virtueel netwerk. Isolatie zorgt ervoor dat de communicatie privé blijft binnen het virtuele netwerk. Zie isolatie in de Azure [Public Cloud voor](../../security/fundamentals/isolation-choices.md#networking-isolation)meer informatie over de isolatie van het virtuele Azure-netwerk .

![VNET-diagram](./media/data-security/vnet.png)

## <a name="encryption"></a>Versleuteling

Gebruikersgegevens worden opgeslagen in Azure-opslag. Gebruikersgegevens worden versleuteld in beweging en in rust voor veiligheid en vertrouwelijkheid. Zie voor meer informatie: [Azure Storage security guide](../../storage/blobs/security-recommendations.md).

## <a name="authentication"></a>Authentication

Transacties kunnen via een RPC-eindpunt naar blockchain-knooppunten worden verzonden. Clients communiceren met een transactieknooppunt met behulp van een reverse proxy-server die gebruikersverificatie verwerkt en gegevens versleutelt via SSL.

![Verificatiediagram](./media/data-security/authentication.png)

Er zijn drie verificatiemodi voor RPC-toegang.

### <a name="basic-authentication"></a>Basisverificatie

Basisverificatie maakt gebruik van een HTTP-verificatiekop met de gebruikersnaam en het wachtwoord. Gebruikersnaam is de naam van het blockchain-knooppunt. Wachtwoord wordt ingesteld tijdens het inrichten van een lid of knooppunt. Het wachtwoord kan worden gewijzigd met behulp van de Azure-portal of CLI.

### <a name="access-keys"></a>Toegangssleutels

Toegangssleutels gebruiken een willekeurig gegenereerde tekenreeks die is opgenomen in de URL van het eindpunt. Met twee toegangstoetsen u sleutelrotatie inschakelen. Sleutels kunnen worden geregenereerd vanuit de Azure-portal en CLI.

### <a name="azure-active-directory"></a>Azure Active Directory

Azure Active Directory (Azure AD) maakt gebruik van een verificatiemechanisme op basis van claimen waarbij de gebruiker door Azure AD wordt geverifieerd met Azure AD-gebruikersreferenties. Azure AD biedt cloudgebaseerd identiteitsbeheer en stelt klanten in staat om één identiteit te gebruiken voor een hele onderneming en toegang te krijgen tot toepassingen in de cloud. Azure Blockchain Service integreert met Azure AD, -federatie, single sign-on en multi-factor authenticatie. U gebruikers, groepen en toepassingsrollen in uw organisatie toewijzen voor toegang tot blockchain-leden en knooppunts.

De Azure AD-clientproxy is beschikbaar op [GitHub.](https://github.com/Microsoft/azure-blockchain-connector/releases) De clientproxy leidt de gebruiker naar de aanmeldingspagina van Azure AD en verkrijgt een token aan toonder bij succesvolle verificatie. Vervolgens verbindt de gebruiker een Ethereum client applicatie zoals Geth of Truffle met het eindpunt van de client proxy. Ten slotte, wanneer een transactie wordt ingediend, injecteert de clientproxy het token aan toonder in de http-header en valideert de omgekeerde proxy het token met behulp van het OAuth-protocol.

## <a name="keys-and-ethereum-accounts"></a>Sleutels en Ethereum-accounts

Bij het inrichten van een Azure Blockchain Service-lid wordt een Ethereum-account en een openbaar en privésleutelpaar gegenereerd. De privésleutel wordt gebruikt om transacties naar de blockchain te verzenden. Het Ethereum-account is de laatste 20 bytes van de hash van de openbare sleutel. De Ethereum account wordt ook wel een portemonnee.

Het private en public key pair wordt opgeslagen als keyfile in JSON-formaat. De privésleutel wordt versleuteld met behulp van het wachtwoord dat is ingevoerd wanneer de blockchain-grootboekservice wordt gemaakt.

Privésleutels worden gebruikt om transacties digitaal te ondertekenen. In private blockchains vertegenwoordigt een slim contract dat is ondertekend door een privésleutel de identiteit van de ondertekenaar. Als u de geldigheid van de handtekening wilt verifiëren, kan de ontvanger de openbare sleutel van de ondertekenaar vergelijken met het adres dat is berekend op basis van de handtekening.

Constellatietoetsen worden gebruikt om een quorumknooppunt op unieke wijze te identificeren. Constellation-sleutels worden gegenereerd op het moment van knooppuntinrichting en worden opgegeven in de privateFor-parameter van een privétransactie in Quorum.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Active Directory-toegang configureren voor Azure Blockchain-service](configure-aad.md).
