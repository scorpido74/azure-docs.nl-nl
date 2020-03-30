---
title: End-to-end TLS-versleuteling configureren met behulp van de portal
titleSuffix: Azure Application Gateway
description: Meer informatie over het gebruik van de Azure-portal om een toepassingsgateway met end-to-end TLS-versleuteling te maken.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: 6f86f32e64bbbe79ea5a403d04f7d6c29ee6b980
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133013"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-the-portal"></a>End-to-end TLS configureren met behulp van Application Gateway met de portal

In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om TLS-versleuteling (End-to-End Transport Layer Security), voorheen bekend als Ssl-versleuteling (Secure Sockets Layer) te configureren, via Azure Application Gateway v1 SKU.

> [!NOTE]
> Application Gateway v2 SKU vereist vertrouwde rootcertificaten voor het inschakelen van end-to-end configuratie.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Als u end-to-end TLS wilt configureren met een toepassingsgateway, hebt u een certificaat voor de gateway nodig. Certificaten zijn ook vereist voor de back-endservers. Het gatewaycertificaat wordt gebruikt om een symmetrische sleutel af te leiden in overeenstemming met de TLS-protocolspecificatie. De symmetrische sleutel wordt vervolgens gebruikt om het verkeer dat naar de gateway wordt verzonden te versleutelen en te decoderen. 

Voor end-to-end TLS-versleuteling moeten de back-endservers van rechts worden toegestaan in de toepassingsgateway. Upload het openbare certificaat van de back-endservers, ook wel authenticatiecertificaten (v1) of Trusted Root Certificates (v2) genoemd, naar de toepassingsgateway om deze toegang toe te staan. Het toevoegen van het certificaat zorgt ervoor dat de toepassingsgateway alleen communiceert met bekende back-end-exemplaren. Deze configuratie zorgt verder voor end-to-end communicatie.

Zie [Overzicht van TLS-beëindiging en end-to-end TLS met Application Gateway](https://docs.microsoft.com/azure/application-gateway/ssl-overview)voor meer informatie.

## <a name="create-a-new-application-gateway-with-end-to-end-tls"></a>Een nieuwe toepassingsgateway maken met end-to-end TLS

Als u een nieuwe toepassingsgateway wilt maken met end-to-end TLS-versleuteling, moet u eerst TLS-beëindiging inschakelen terwijl u een nieuwe toepassingsgateway maakt. Met deze actie kan TLS-versleuteling worden toegepast voor communicatie tussen de client en de toepassingsgateway. Vervolgens moet u de certificaten voor de back-endservers in de HTTP-instellingen op de lijst Veilige geadresseerden plaatsen. Met deze configuratie kan TLS-versleuteling worden toegepast voor communicatie tussen de toepassingsgateway en de back-endservers. Dat bereikt end-to-end TLS-encryptie.

### <a name="enable-tls-termination-while-creating-a-new-application-gateway"></a>TLS-beëindiging inschakelen tijdens het maken van een nieuwe toepassingsgateway

Zie TLS-beëindiging inschakelen voor meer informatie [terwijl u een nieuwe toepassingsgateway maakt.](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

### <a name="add-authenticationroot-certificates-of-back-end-servers"></a>Verificatie-/hoofdcertificaten van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **HTTP-instellingen** in het menu aan de linkerkant. Azure heeft automatisch een standaard HTTP-instelling gemaakt, **appGatewayBackendHttpSettings**, wanneer u de toepassingsgateway hebt gemaakt. 

3. Selecteer **appGatewayBackendHttpSettings**.

4. Selecteer onder **Protocol** **HTTPS**. Er verschijnt een deelvenster voor **backendverificatiecertificaten of vertrouwde rootcertificaten.**

5. Selecteer **Nieuw maken**.

6. Voer in het veld **Naam** een geschikte naam in.

7. Selecteer het certificaatbestand in het vak **CER-certificaat uploaden.**

   Voor standaard- en WAF-toepassingsgateways (v1) moet u de openbare sleutel van uw back-endservercertificaat uploaden in .cer-indeling.

   ![Certificaat toevoegen](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2- en WAF_v2 toepassingsgateways moet u het hoofdcertificaat van het back-endservercertificaat uploaden in de .cer-indeling. Als het back-endcertificaat is uitgegeven door een bekende certificaatautoriteit (CA), u het selectievakje **Bekende CA-certificaat gebruiken** inschakelen en hoeft u geen certificaat te uploaden.

   ![Vertrouwd basiscertificaat toevoegen](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![Basiscertificaat](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecteer **Opslaan**.

## <a name="enable-end-to-end-tls-for-an-existing-application-gateway"></a>End-to-end TLS inschakelen voor een bestaande toepassingsgateway

Als u een bestaande toepassingsgateway wilt configureren met end-to-end TLS-versleuteling, moet u eerst TLS-beëindiging inschakelen in de listener. Met deze actie kan TLS-versleuteling worden toegepast voor communicatie tussen de client en de toepassingsgateway. Plaats deze certificaten voor back-endservers vervolgens in de HTTP-instellingen in de lijst Veilige geadresseerden. Met deze configuratie kan TLS-versleuteling worden toegepast voor communicatie tussen de toepassingsgateway en de back-endservers. Dat bereikt end-to-end TLS-encryptie.

U moet een listener gebruiken met het HTTPS-protocol en een certificaat voor het inschakelen van TLS-beëindiging. U een bestaande listener gebruiken die aan deze voorwaarden voldoet of een nieuwe listener maken. Als u de vorige optie kiest, u de volgende sectie 'TLS-beëindiging inschakelen in een bestaande toepassingsgateway' negeren en rechtstreeks naar de sectie 'Verificatie/vertrouwde rootcertificaten voor backendservers toevoegen' gaan.

Als u de laatste optie kiest, past u de stappen in de volgende procedure toe.
### <a name="enable-tls-termination-in-an-existing-application-gateway"></a>TLS-beëindiging inschakelen in een bestaande toepassingsgateway

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **Listeners** in het menu aan de linkerkant.

3. Selecteer **Basis-** of **Multi-site** listener, afhankelijk van uw vereisten.

4. Selecteer onder **Protocol** **HTTPS**. Er verschijnt een deelvenster voor **certificaat.**

5. Upload het PFX-certificaat dat u wilt gebruiken voor TLS-beëindiging tussen de client en de toepassingsgateway.

   > [!NOTE]
   > Voor testdoeleinden u een zelfondertekend certificaat gebruiken. Dit wordt echter niet aangeraden voor productieworkloads, omdat ze moeilijker te beheren zijn en niet volledig veilig zijn. Zie [Een zelfondertekend certificaat maken voor](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate)meer informatie.

6. Voeg andere vereiste instellingen voor de **listener**toe, afhankelijk van uw vereisten.

7. Selecteer **OK** om op te slaan.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Verificatie/vertrouwde hoofdcertificaten van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **HTTP-instellingen** in het menu aan de linkerkant. U certificaten in een bestaande back-end HTTP-instelling in de lijst Veilige geadresseerden plaatsen of een nieuwe HTTP-instelling maken. (In de volgende stap wordt het certificaat voor de standaard HTTP-instelling, **appGatewayBackendHttpSettings**, toegevoegd aan de lijst Veilige geadresseerden.)

3. Selecteer **appGatewayBackendHttpSettings**.

4. Selecteer onder **Protocol** **HTTPS**. Er verschijnt een deelvenster voor **backendverificatiecertificaten of vertrouwde rootcertificaten.** 

5. Selecteer **Nieuw maken**.

6. Voer in het veld **Naam** een geschikte naam in.

7. Selecteer het certificaatbestand in het vak **CER-certificaat uploaden.**

   Voor standaard- en WAF-toepassingsgateways (v1) moet u de openbare sleutel van uw back-endservercertificaat uploaden in .cer-indeling.

   ![Certificaat toevoegen](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2- en WAF_v2 toepassingsgateways moet u het hoofdcertificaat van het back-endservercertificaat uploaden in de .cer-indeling. Als het back-endcertificaat is uitgegeven door een bekende CA, u het selectievakje **Bekende CA-certificaat** gebruiken inschakelen en hoeft u geen certificaat te uploaden.

   ![Vertrouwd basiscertificaat toevoegen](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
