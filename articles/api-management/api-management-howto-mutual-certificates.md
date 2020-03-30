---
title: Beveiligde back-endservices met verificatie van clientcertificaat
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van back-endservices met verificatie van clientcertificaatin Azure API Management.
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
ms.openlocfilehash: b0ddf6dda99ee666e3052b5a70e51c7e4208a374
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80347105"
---
# <a name="how-to-secure-back-end-services-using-client-certificate-authentication-in-azure-api-management"></a>Back-endservices beveiligen met behulp van verificatie via clientcertificaten in Azure API Management

API-beheer stelt u in staat om toegang te krijgen tot de back-endservice van een API met behulp van clientcertificaten. In deze handleiding ziet u hoe u certificaten beheert in de azure API Management-serviceinstantie in de Azure-portal. Het legt ook uit hoe u een API configureert om een certificaat te gebruiken om toegang te krijgen tot een back-endservice.

Zie Azure API Management REST API Certificate Entity voor informatie over het beheren van certificaten met de API-API api voor API <a href="https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity">Beheer.</a>

## <a name="prerequisites"></a><a name="prerequisites"> </a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

In deze handleiding ziet u hoe u uw API Management-serviceinstantie configureert om clientcertificaatverificatie te gebruiken om toegang te krijgen tot de back-endservice voor een API. Voordat u de stappen in dit artikel volgt, moet u uw back-endservice hebben geconfigureerd voor verificatie van clientcertificaat[(om certificaatverificatie in de Azure App Service te configureren, verwijzen naar dit artikel][to configure certificate authentication in Azure WebSites refer to this article]). U hebt toegang nodig tot het certificaat en het wachtwoord voor het uploaden naar de API Management-service.

## <a name="upload-a-certificate"></a><a name="step1"> </a>Een certificaat uploaden

> [!NOTE]
> In plaats van een geüpload certificaat u een certificaat gebruiken dat is opgeslagen in de [Azure Key Vault-service,](https://azure.microsoft.com/services/key-vault/) zoals in dit [voorbeeld](https://github.com/galiniliev/api-management-policy-snippets/blob/galin/AkvCert/examples/Look%20up%20Key%20Vault%20certificate%20using%20Managed%20Service%20Identity%20and%20call%20backend.policy.xml)wordt weergegeven.

![Clientcertificaten toevoegen](media/api-management-howto-mutual-certificates/apim-client-cert-new.png)

Volg de onderstaande stappen om een nieuw clientcertificaat te uploaden. Als u nog geen API Management-serviceinstantie hebt gemaakt, raadpleegt u de zelfstudie [Een API Management-serviceinstantie maken.][Create an API Management service instance]

1. Navigeer naar de instantie azure API Management-service in de Azure-portal.
2. Selecteer **Certificaten** in het menu.
3. Klik op de knop **+ Toevoegen**.
    ![Clientcertificaten toevoegen](media/api-management-howto-mutual-certificates/apim-client-cert-add.png)
4. Blader naar het certificaat, geef de id en het wachtwoord op.
5. Klik **op Maken**.

> [!NOTE]
> Het certificaat moet in **.pfx-formaat** zijn. Zelfondertekende certificaten zijn toegestaan.

Zodra het certificaat is geüpload, wordt het weergegeven in de **certificaten**.  Als u veel certificaten hebt, noteer dan de duimafdruk van het gewenste certificaat om [een API te configureren om een clientcertificaat te gebruiken voor gatewayverificatie.][Configure an API to use a client certificate for gateway authentication]

> [!NOTE]
> Volg de stappen die in dit [faq-item](api-management-faq.md#can-i-use-a-self-signed-tlsssl-certificate-for-a-back-end)zijn beschreven om validatie van de certificaatketen uit te schakelen bij het gebruik van bijvoorbeeld een zelfondertekend certificaat.

## <a name="delete-a-client-certificate"></a><a name="step1a"> </a>Een clientcertificaat verwijderen

Als u een certificaat wilt verwijderen, klikt u op contextmenu **...** en selecteert u **Verwijderen** naast het certificaat.

![Clientcertificaten verwijderen](media/api-management-howto-mutual-certificates/apim-client-cert-delete-new.png)

Als het certificaat door een API wordt gebruikt, wordt een waarschuwingsscherm weergegeven. Als u het certificaat wilt verwijderen, moet u het certificaat eerst verwijderen uit api's die zijn geconfigureerd om het te gebruiken.

![Clientcertificaten verwijderen als mislukt](media/api-management-howto-mutual-certificates/apim-client-cert-delete-failure.png)

## <a name="configure-an-api-to-use-a-client-certificate-for-gateway-authentication"></a><a name="step2"> </a>Een API configureren om een clientcertificaat te gebruiken voor gatewayverificatie

1. Klik op **API's** in het menu **API-beheer** aan de linkerkant en navigeer naar de API.
    ![Clientcertificaten inschakelen](media/api-management-howto-mutual-certificates/apim-client-cert-enable.png)

2. Klik op het tabblad **Ontwerpen** op een potloodpictogram van de sectie **Backend.**
3. Wijzig de **gatewayreferenties** in **clientcert** en selecteer uw certificaat in de vervolgkeuzelijst.
    ![Clientcertificaten inschakelen](media/api-management-howto-mutual-certificates/apim-client-cert-enable-select.png)

4. Klik op **Opslaan**.

> [!WARNING]
> Deze wijziging is onmiddellijk van kracht en oproepen naar bewerkingen van die API gebruiken het certificaat om te verifiëren op de back-endserver.


> [!TIP]
> Wanneer een certificaat is opgegeven voor gatewayverificatie voor de back-endservice van een API, wordt het onderdeel van het beleid voor die API en kan het worden weergegeven in de beleidseditor.

## <a name="self-signed-certificates"></a>Zelfondertekende certificaten

Als u zelfondertekende certificaten gebruikt, moet u de validatie van de certificaatketen uitschakelen om API-beheer te laten communiceren met het backend-systeem. Anders zal het een 500 foutcode retourneren. Om dit te configureren, [`New-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/new-azapimanagementbackend) u de [`Set-AzApiManagementBackend`](https://docs.microsoft.com/powershell/module/az.apimanagement/set-azapimanagementbackend) PowerShell-cmdlets (voor nieuwe back-end) of (voor bestaande back-end) powershell gebruiken en de `-SkipCertificateChainValidation` parameter instellen op `True`.

```powershell
$context = New-AzApiManagementContext -resourcegroup 'ContosoResourceGroup' -servicename 'ContosoAPIMService'
New-AzApiManagementBackend -Context  $context -Url 'https://contoso.com/myapi' -Protocol http -SkipCertificateChainValidation $true
```

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[Azure API Management REST API Certificate entity]: https://msdn.microsoft.com/library/azure/dn783483.aspx
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[to configure certificate authentication in Azure WebSites refer to this article]: ../app-service/app-service-web-configure-tls-mutual-auth.md

[Prerequisites]: #prerequisites
[Upload a client certificate]: #step1
[Delete a client certificate]: #step1a
[Configure an API to use a client certificate for gateway authentication]: #step2
[Test the configuration by calling an operation in the Developer Portal]: #step3
[Next steps]: #next-steps
