---
title: Knooppunten voor Azure Blockchain-servicetransacties configureren
description: Azure Blockchain Service-transactieknooppunten configureren
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: 8fa18496d0c1aa59beb55569e731967d5ebea427
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876926"
---
# <a name="configure-azure-blockchain-service-transaction-nodes"></a>Knooppunten voor Azure Blockchain-servicetransacties configureren

Transactieknooppunten worden gebruikt om blockchain-transacties naar Azure Blockchain Service te verzenden via een openbaar eindpunt. Het standaard transactieknooppunt bevat de privésleutel van de Ethereum-account die op de blockchain is geregistreerd en kan als zodanig niet worden verwijderd.

Ga als instellen voor de standaardgegevens van het transactieknooppunt:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Navigeer naar uw Azure Blockchain Service-lid. Selecteer **Transactieknooppunten**.

    ![Standaardtransactieknooppunt selecteren](./media/configure-transaction-nodes/nodes.png)

    Overzichtsdetails omvatten openbare eindpuntadressen en openbare sleutel.

## <a name="create-transaction-node"></a>Transactieknooppunt maken

U maximaal negen extra transactieknooppunten toevoegen aan uw blockchain-lid, voor een totaal van 10 transactieknooppunten. Door transactieknooppunten toe te voegen, u de schaalbaarheid vergroten of de belasting distribueren. U bijvoorbeeld een eindpunt voor transactieknooppunt hebben voor verschillende clienttoepassingen.

Een transactieknooppunt toevoegen:

1. Navigeer in de Azure-portal naar uw Azure Blockchain Service-lid en selecteer **Transactieknooppunten > Toevoegen**.
1. Voer de instellingen voor het nieuwe transactieknooppunt in.

    ![Transactieknooppunt toevoegen](./media/configure-transaction-nodes/add-node.png)

    | Instelling | Beschrijving |
    |---------|-------------|
    | Name | Naam van transactieknooppunt. De naam wordt gebruikt om het DNS-adres voor het eindpunt van het transactieknooppunt te maken. Bijvoorbeeld `newnode-myblockchainmember.blockchain.azure.com`. De naam van het knooppunt kan niet worden gewijzigd nadat deze is gemaakt. |
    | Wachtwoord | Stel een sterk wachtwoord in. Gebruik het wachtwoord om toegang te krijgen tot het eindpunt van het transactieknooppunt met basisverificatie.

1. Selecteer **Maken**.

    Het inrichten van een nieuw transactieknooppunt duurt ongeveer 10 minuten. Extra transactieknooppunten brengen kosten met zich mee. Zie [Azure-prijzen](https://aka.ms/ABSPricing)voor meer informatie over de kosten.

## <a name="endpoints"></a>Eindpunten

Transactieknooppunten hebben een unieke DNS-naam en openbare eindpunten.

Ga als lid van het eindpunt van een transactieknooppunt als voorbeeld naar de bestek:

1. Navigeer in de Azure-portal naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteer **Overzicht**.

    ![Eindpunten](./media/configure-transaction-nodes/endpoints.png)

Eindpunten voor transactieknooppunt zijn veilig en vereisen verificatie. U verbinding maken met een transactieeindpunt met Azure AD-verificatie, HTTPS-basisverificatie en een toegangssleutel via HTTPS of Websocket via TLS gebruiken.

### <a name="azure-active-directory-access-control"></a>Azure Active Directory-toegangsbeheer

Azure Blockchain Service-transactieknooppunteindpunten ondersteunen Azure Active Directory (Azure AD)-verificatie. U Azure AD-gebruiker, groep en serviceprincipal toegang verlenen tot uw eindpunt.

Ga als het gaat om Azure AD-toegangsbeheer toe te staan aan uw eindpunt:

1. Navigeer in de Azure Portal naar uw Azure Blockchain Service-lid en selecteer **Transactieknooppunten > Toegangsbeheer (IAM) > Voeg > Functietoewijzing toevoegen toe.**
1. Maak een nieuwe roltoewijzing voor een gebruiker, groep of serviceprincipal (toepassingsrollen).

    ![IAM-rol toevoegen](./media/configure-transaction-nodes/add-role.png)

    | Instelling | Actie |
    |---------|-------------|
    | Rol | Selecteer **Eigenaar,** **bijdrager**of **lezer**.
    | Toegang toewijzen aan | Selecteer **Azure AD-gebruiker, groep of serviceprincipal**.
    | Selecteer | Zoek naar de gebruiker, groep of serviceprincipal die u wilt toevoegen.

1. Selecteer **Opslaan** om de roltoewijzing toe te voegen.

Zie [Toegang tot Azure-bronnen beheren met RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie over Azure AD-toegangsbeheer.

Zie Verbinding maken met uw knooppunt [met AAD-verificatie](configure-aad.md)voor meer informatie over het maken van verbinding met Azure AD-verificatie.

### <a name="basic-authentication"></a>Basisverificatie

Voor HTTPS-basisverificatie worden gebruikersnaam- en wachtwoordreferenties doorgegeven in de HTTPS-header van het verzoek naar het eindpunt.

U de eindpuntdetails van een transactieknooppunt weergeven in de Azure-portal. Navigeer naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteer **Basisverificatie** in instellingen.

![Basisverificatie](./media/configure-transaction-nodes/basic.png)

De gebruikersnaam is de naam van uw knooppunt en kan niet worden gewijzigd.

Als u de \<URL\> wilt gebruiken, vervangt u het wachtwoord door het wachtwoord dat is ingesteld wanneer het knooppunt is ingericht. U het wachtwoord bijwerken door **wachtwoord opnieuw instellen te selecteren.**

### <a name="access-keys"></a>Toegangssleutels

Voor verificatie van toegangssleutels is de toegangssleutel opgenomen in de URL van het eindpunt. Wanneer het transactieknooppunt is ingericht, worden twee toegangssleutels gegenereerd. Beide toegangssleutels kunnen worden gebruikt voor verificatie. Met twee toetsen u toetsen wijzigen en roteren.

U de toegangssleutelgegevens van een transactieknooppunt bekijken en eindpuntadressen kopiëren die de toegangssleutels bevatten. Navigeer naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteer **Toegangssleutels** in instellingen.

### <a name="firewall-rules"></a>Firewall-regels

Met firewallregels u de IP-adressen beperken die kunnen proberen te verifiëren tot uw transactieknooppunt.  Als er geen firewallregels zijn geconfigureerd voor uw transactieknooppunt, kan deze door geen enkele partij worden geopend.  

Als u de firewallregels van een transactieknooppunt wilt weergeven, navigeert u naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteert u **Firewallregels** in instellingen.

U firewallregels toevoegen door een regelnaam, start-IP-adres en een beëindigend IP-adres in het **firewallregelsraster** in te voeren.

![Firewall-regels](./media/configure-transaction-nodes/firewall-rules.png)

Inschakelen:

* **Eén IP-adres:** Configureer hetzelfde IP-adres voor de begin- en eindIP-adressen.
* **IP-adresbereik:** Configureer het ip-adresbereik voor begin en einde. Een bereik vanaf 10.221.34.0 en eindigend op 10.221.34.255 zou bijvoorbeeld het volledige 10.221.34.xxx subnet mogelijk maken.
* **Sta alle IP-adressen toe:** Configureer het beginIP-adres op 0,0.0.0 en het uiteindelijke IP-adres naar 255.255.255.255.

## <a name="connection-strings"></a>Verbindingsreeksen

Syntaxis van verbindingstekenreeksen voor uw transactieknooppunt is bedoeld voor basisverificatie of met toegangssleutels. Verbindingstekenreeksen, inclusief toegangssleutels via HTTPS en WebSockets, worden geleverd.

U de verbindingstekenreeksen van een transactieknooppunt bekijken en eindpuntadressen kopiëren. Navigeer naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteer **Verbindingstekenreeksen** in instellingen.

![Verbindingsreeksen](./media/configure-transaction-nodes/connection-strings.png)

## <a name="sample-code"></a>Voorbeeldcode

Er wordt voorbeeldcode meegeleverd om snel verbinding te maken met uw transactieknooppunt via Web3, Nethereum, Web3js en Truffle.

U de voorbeeldverbindingscode van een transactieknooppunt bekijken en kopiëren om te gebruiken met populaire hulpprogramma's voor ontwikkelaars. Ga naar een van uw transactieknooppunten voor Azure Blockchain Service-leden en selecteer **Voorbeeldcode** in instellingen.

Kies het tabblad Web3, Nethereum, Truffle of Web3j om het codevoorbeeld weer te geven dat u wilt gebruiken.

![Voorbeeldcode](./media/configure-transaction-nodes/sample-code.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Transactieknooppunten configureren met Azure CLI](manage-cli.md)
