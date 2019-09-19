---
title: 'Quick Start: end-to-end SSL-versleuteling configureren met Azure-toepassing gateway-Azure Portal | Microsoft Docs'
description: Meer informatie over het gebruik van de Azure Portal om een Azure-toepassing gateway te maken met end-to-end SSL-versleuteling.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/30/2019
ms.author: absha
ms.custom: mvc
ms.openlocfilehash: a37b313bd808ee0441d84ac92050b087eba7ac9d
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097190"
---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-the-portal"></a>End-to-end SSL configureren met behulp van Application Gateway met de portal

In dit artikel wordt beschreven hoe u met behulp van de Azure Portal end-to-end SSL-versleuteling configureert met een Application Gateway v1-SKU.  

> [!NOTE]
> Voor de SKU van Application Gateway v2 zijn vertrouwde basis certificaten vereist om end-to-end-configuratie in te scha kelen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

Als u end-to-end SSL wilt configureren met een Application Gateway, is er een certificaat vereist voor de gateway en zijn de certificaten vereist voor de back-endservers. Het gateway certificaat wordt gebruikt voor het afleiden van een symmetrische sleutel volgens SSL-protocol specificatie. De symmetrische sleutel wordt vervolgens gebruikt voor het versleutelen en ontsleutelen van het verkeer dat naar de gateway wordt verzonden. Voor end-to-end SSL-versleuteling moeten de juiste back-endservers zijn toegestaan in de toepassings gateway. Hiervoor uploadt u het open bare certificaat van de back-endservers, ook wel bekend als verificatie certificaten (v1) of vertrouwde basis certificaten (v2), naar de Application Gateway. Door het certificaat toe te voegen, zorgt u ervoor dat de Application Gateway alleen communiceert met bekende back-end-exemplaren. Hiermee wordt de end-to-end-communicatie verder beveiligd.

Zie voor meer informatie [SSL-beëindiging en end-to-end SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview).

## <a name="create-a-new-application-gateway-with-end-to-end-ssl"></a>Een nieuwe toepassings gateway maken met end-to-end SSL

Als u een nieuwe toepassings gateway met end-to-end SSL-versleuteling wilt maken, moet u eerst SSL-beëindiging inschakelen tijdens het maken van een nieuwe toepassings gateway. Hiermee schakelt u SSL-versleuteling in voor de communicatie tussen de client en de toepassings gateway. Vervolgens moet u certificaten voor back-endservers white list in de HTTP-instellingen om SSL-versleuteling in te scha kelen voor de communicatie tussen de toepassings gateway en de back-endservers, waarbij End-to-end SSL-versleuteling wordt uitgevoerd.

### <a name="enable-ssl-termination-while-creating-a-new-application-gateway"></a>SSL-beëindiging inschakelen tijdens het maken van een nieuwe toepassings gateway

Raadpleeg dit artikel voor meer informatie over het [inschakelen van SSL-beëindiging tijdens het maken van een nieuwe toepassings gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="add-authenticationroot-certificate-of-back-end-servers"></a>Authenticatie/basis certificaat van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **http-instellingen** in het menu links. Azure heeft automatisch een standaard HTTP-instelling, **appGatewayBackendHttpSettings**, gemaakt wanneer u de toepassings gateway hebt gemaakt. 

3. Selecteer **appGatewayBackendHttpSettings**.

4. Onder **protocol**selecteert u **https**. Er wordt een deel venster voor **back-end-verificatie certificaten of vertrouwde basis certificaten** weer gegeven. 

5. Kies **Nieuw maken**.

6. Voer een geschikte **naam**in.

7. Selecteer het certificaat bestand met het vak **CER-certificaat uploaden** .

   Voor de standaard-en WAF (v1)-toepassings gateways moet u de open bare sleutel van uw back-end-server certificaat uploaden in. CER-indeling.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2-en WAF_v2-toepassings gateways moet u het **basis certificaat** van het back-end-server certificaat uploaden in. CER-indeling. Als het back-end-certificaat is uitgegeven door een bekende certificerings instantie, kunt u het selectie vakje goed bekende CA-certificaat gebruiken inschakelen en hoeft u geen certificaat te uploaden.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

   ![rootcert](./media/end-to-end-ssl-portal/trustedrootcert.png)

8. Selecteer **Opslaan**.

## <a name="enable-end-to-end-ssl-for-existing-application-gateway"></a>End-to-end SSL inschakelen voor bestaande toepassings gateway

Als u een bestaande toepassings gateway met end-to-end SSL-versleuteling wilt configureren, moet u eerst SSL-beëindiging inschakelen in de listener. Hiermee schakelt u SSL-versleuteling in voor de communicatie tussen de client en de toepassings gateway. Vervolgens moet u certificaten voor back-endservers white list in de HTTP-instellingen om SSL-versleuteling in te scha kelen voor de communicatie tussen de toepassings gateway en de back-endservers, waarbij End-to-end SSL-versleuteling wordt uitgevoerd.

U moet een listener met het HTTPS-protocol en-certificaat gebruiken om SSL-beëindiging in te scha kelen. U kunt ervoor kiezen om een bestaande listener te gebruiken met het HTTPS-protocol en-certificaat, of om een nieuwe listener te maken. Als u de eerste optie kiest, kunt u de onderstaande stappen negeren om **SSL-beëindiging in te scha kelen in de bestaande toepassings gateway** en direct te verplaatsen naar de sectie **verificatie/vertrouwde basis certificaten toevoegen voor back-endservers** . Als u dit laatste hebt gekozen, gebruikt u deze stappen.

### <a name="enable-ssl-termination-in-existing-application-gateway"></a>SSL-beëindiging inschakelen in bestaande toepassings gateway

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **listeners** in het menu links.

3. U kunt kiezen tussen een **Basic** -en een **multi-site** -listener conform uw vereiste.

4. Onder **protocol**selecteert u **https**. Er wordt een deel venster voor het **certificaat** weer gegeven.

5. Upload het PFX-certificaat dat u wilt gebruiken voor de SSL-beëindiging tussen de client en toepassings gateway.

   > [!NOTE]
   > Voor test doeleinden kunt u een zelfondertekend certificaat gebruiken. maar niet op de hoogte van productie werkbelastingen omdat ze moeilijker te beheren zijn en niet volledig zijn beveiligd. Meer informatie over het [maken van een zelfondertekend certificaat](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal#create-a-self-signed-certificate).

6. Voeg volgens uw vereiste andere vereiste instellingen voor de **listener** toe.

7. Selecteer **OK** om op te slaan.

### <a name="add-authenticationtrusted-root-certificates-of-back-end-servers"></a>Verificatie/vertrouwde basis certificaten van back-endservers toevoegen

1. Selecteer **Alle resources** en vervolgens **myAppGateway**.

2. Selecteer **http-instellingen** in het menu links. U kunt certificaten white list in een bestaande back-end-HTTP-instelling of een nieuwe HTTP-instelling maken. In de onderstaande stap wordt het certificaat white list voor de standaard HTTP-instelling **appGatewayBackendHttpSettings**.

3. Selecteer **appGatewayBackendHttpSettings**.

4. Onder **protocol**selecteert u **https**. Er wordt een deel venster voor **back-end-verificatie certificaten of vertrouwde basis certificaten** weer gegeven. 

5. Kies **Nieuw maken**.

6. Voer een geschikte **naam**in.

7. Selecteer het certificaat bestand met het vak **CER-certificaat uploaden** .

   Voor de standaard-en WAF (v1)-toepassings gateways moet u de open bare sleutel van uw back-end-server certificaat uploaden in. CER-indeling.

   ![addcert](./media/end-to-end-ssl-portal/addcert.png)

   Voor Standard_v2-en WAF_v2-toepassings gateways moet u het **basis certificaat** van het back-end-server certificaat uploaden in. CER-indeling. Als het back-end-certificaat is uitgegeven door een bekende certificerings instantie, kunt u het selectie vakje goed bekende CA-certificaat gebruiken inschakelen en hoeft u geen certificaat te uploaden.

   ![addtrustedrootcert](./media/end-to-end-ssl-portal/trustedrootcert-portal.png)

8. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Webverkeer met een toepassingsgateway beheren met behulp van Azure CLI](./tutorial-manage-web-traffic-cli.md)
