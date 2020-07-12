---
title: Back-end-services beveiligen met verificatie op basis van client certificaten
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van back-end-services met verificatie op basis van client certificaten in azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/08/2020
ms.author: apimpm
ms.openlocfilehash: 8e02a47cd6ae6e4883b5113b07d4049cd723232d
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86250189"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Back-endservices beveiligen met behulp van verificatie via clientcertificaten in Azure API Management

Met API Management kunt u de toegang tot de back-end-service van een API beveiligen met behulp van client certificaten. In deze hand leiding wordt uitgelegd hoe u certificaten beheert in het Azure API Management service-exemplaar in de Azure Portal. Ook wordt uitgelegd hoe u een API configureert om een certificaat te gebruiken voor toegang tot een back-end-service.

Voor informatie over het beheren van certificaten met behulp van de API Management REST API, zie de <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">entiteit Azure API Management rest API-certificaat</a>.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze hand leiding wordt beschreven hoe u uw API Management service-exemplaar configureert voor het gebruik van verificatie van client certificaten voor toegang tot de back-end-service voor een API. Voordat u de stappen in dit artikel volgt, moet uw back-end-service zijn geconfigureerd voor verificatie van client certificaten ([voor het configureren van certificaat verificatie in de Azure app service raadpleegt u dit artikel][to configure certificate authentication in Azure WebSites refer to this article]). U hebt toegang tot het certificaat en het wacht woord voor het uploaden naar de API Management-service nodig.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Een certificaat uploaden

> [!NOTE]
> U kunt in plaats van een geüpload certificaat een certificaat gebruiken dat is opgeslagen in de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) -service, zoals wordt weer gegeven in dit [voor beeld](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml).

![Client certificaten toevoegen](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Volg de onderstaande stappen om een nieuw client certificaat te uploaden. Als u nog geen API Management service-exemplaar hebt gemaakt, raadpleegt u de zelf studie [een API Management service-exemplaar maken][Create an API Management service instance].

1. Navigeer naar uw Azure API Management service-exemplaar in de Azure Portal.
2. Selecteer **certificaten** in het menu.
3. Klik op de knop **+ Toevoegen**.
    ![Client certificaten toevoegen](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Blader naar het certificaat en geef de ID en het wacht woord op.
5. Klik op **Maken**.

> [!NOTE]
> Het certificaat moet de indeling **. pfx** hebben. Zelfondertekende certificaten zijn toegestaan.

Zodra het certificaat is geüpload, wordt het weer gegeven in de **certificaten**.  Als u veel certificaten hebt, noteert u de vinger afdruk van het gewenste certificaat om [een API te configureren voor het gebruik van een client certificaat voor gateway verificatie][Configure an API to use a client certificate for gateway authentication].

> [!NOTE]
> Als u validatie van certificaat keten wilt uitschakelen wanneer u bijvoorbeeld een zelfondertekend certificaat gebruikt, volgt u de stappen die worden beschreven in dit Veelgestelde vragen [item](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end).

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Een client certificaat verwijderen

Als u een certificaat wilt verwijderen, klikt u op context menu **...** en selecteert u **verwijderen** naast het certificaat.

![Client certificaten verwijderen](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Als het certificaat wordt gebruikt door een API, wordt er een waarschuwings scherm weer gegeven. Als u het certificaat wilt verwijderen, moet u eerst het certificaat verwijderen uit Api's die zijn geconfigureerd om het te gebruiken.

![Fout bij verwijderen van client certificaten](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Een API configureren voor het gebruik van een client certificaat voor gateway verificatie

1. Klik op **api's** in het menu **API Management** aan de linkerkant en navigeer naar de API.
    ![Client certificaten inschakelen](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Klik op het tabblad **ontwerp** op een potlood pictogram van de **back-end** -sectie.
3. Wijzig de **Gateway referenties** in het **client certificaat** en selecteer uw certificaat in de vervolg keuzelijst.
    ![Client certificaten inschakelen](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klik op **Opslaan**.

> [!WARNING]
> Deze wijziging is onmiddellijk van kracht en aanroepen naar bewerkingen van die API gebruiken het certificaat om te verifiëren op de back-endserver.


> [!TIP]
> Wanneer een certificaat wordt opgegeven voor gateway verificatie voor de back-end-service van een API, wordt het een onderdeel van het beleid voor die API en kan het worden weer gegeven in de beleids editor.

## <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Als u zelfondertekende certificaten gebruikt, moet u validatie van de certificaat keten uitschakelen om API Management te communiceren met het back-end-systeem. Anders wordt er een fout code van 500 geretourneerd. Als u dit wilt configureren, kunt u de [`New-AzApiManagementBackend`](/powershell/module/az.apimanagement/new-azapimanagementbackend) (voor nieuwe back-end) of [`Set-AzApiManagementBackend`](/powershell/module/az.apimanagement/set-azapimanagementbackend) (voor bestaande back-end) Power shell-cmdlets gebruiken en de `-SkipCertificateChainValidation` para meter instellen op `True` .

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: ./mock-api-responses.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: ./api-management-policies.md
[Caching policies]: ./api-management-policies.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: ./api-management-caching-policies.md
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
