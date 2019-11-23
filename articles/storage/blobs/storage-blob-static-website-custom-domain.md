---
title: 'Tutorial: Enable custom domain & SSL for a static website in Azure'
description: Leer hoe u een aangepast domein configureert voor het hosten van statische websites.
author: normesta
ms.service: storage
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 893ac53dc9f0b6b162c5ec22e478cd15706e50fb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74327499"
---
# <a name="tutorial-enable-custom-domain--ssl-for-a-static-website-in-azure"></a>Tutorial: Enable custom domain & SSL for a static website in Azure

Deze zelfstudie is deel twee van een serie. Hier leert u om een aangepast domein met SSL voor uw statische website in te schakelen. 

De zelfstudie laat zien hoe u met [Azure CDN](../../cdn/cdn-overview.md) het eindpunt van het aangepaste domein kunt configureren voor uw statische website. Met Azure CDN kunt u aangepaste SSL-certificaten inrichten, een aangepast domein gebruiken en aangepaste herschrijvingsregels configureren, en dat alles op hetzelfde moment. Het configureren van Azure CDN resulteert in extra kosten, maar biedt consistente lage latenties naar uw website vanaf elke locatie ter wereld. Azure CDN biedt ook SSL-versleuteling met uw eigen certificaat. Zie [Azure CDN-prijzen](https://azure.microsoft.com/pricing/details/cdn/) voor meer informatie over prijzen voor Azure CDN.

In deel twee van de serie leert u het volgende:

> [!div class="checklist"]
> * Een CDN-eindpunt op het eindpunt van een statische website maken
> * Aangepast domein en SSL inschakelen

## <a name="prerequisites"></a>Vereisten

Before you begin this tutorial, complete part one, [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md). 

## <a name="sign-in-to-the-azure-portal"></a>Aanmelden bij Azure Portal

Meld u aan bij de [Azure-portal](https://portal.azure.com/) om aan de slag te gaan.

## <a name="create-a-cdn-endpoint-on-the-static-website-endpoint"></a>Een CDN-eindpunt op het eindpunt van een statische website maken

1. Locate your storage account in the Azure portal and display the account overview.
1. Selecteer **Azure CDN** onder het menu **Blob-Service** om Azure CDN te configureren.
1. In the **CDN profile** section, specify a new or existing CDN profile. Zie voor meer informatie [Snelstartgids: Een Azure CDN-profiel en een eindpunt maken](../../cdn/cdn-create-new-endpoint.md).
1. Specify a pricing tier for the CDN endpoint. This tutorial uses the **Standard Akamai** pricing tier, because it propagates quickly, typically within a few minutes. Other pricing tiers may take longer to propagate, but may also offer other advantages. For more information, see [Compare Azure CDN product features](../../cdn/cdn-features.md).
1. In the **CDN endpoint name** field, specify a name for your CDN endpoint. The CDN endpoint must be unique across Azure.
1. Specify your the static website endpoint in the **Origin hostname** field. To find your static website endpoint, navigate to the **Static website** settings for your storage account. Copy the primary endpoint and paste it into the CDN configuration, removing the protocol identifier (*e.g.* , HTTPS).

    The following image shows an example endpoint configuration:

    ![Screenshot showing sample CDN endpoint configuration](media/storage-blob-static-website-custom-domain/add-cdn-endpoint.png)

1. Create the CDN endpoint and wait for it to propagate.
1. To verify that the CDN endpoint is configured correctly, click on the endpoint to navigate to its settings. From the CDN overview for your storage account, locate the endpoint hostname, and navigate to the endpoint, as shown in the following image. The format of your CDN endpoint will be similar to `https://staticwebsitesamples.azureedge.net`.

    ![Screenshot showing overview of CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-endpoint.png)

    Once the CDN endpoint propagation is complete, navigating to the CDN endpoint displays the contents of the index.html file that you previously uploaded to your static website.

1. To review the origin settings for your CDN endpoint, navigate to **Origin** under the **Settings** section for your CDN endpoint. You will see that the **Origin type** field is set to *Custom Origin* and that the **Origin hostname** field displays your static website endpoint.

    ![Screenshot showing Origin settings for CDN endpoint](media/storage-blob-static-website-custom-domain/verify-cdn-origin.png)

## <a name="enable-custom-domain-and-ssl"></a>Aangepast domein en SSL inschakelen

1. Maak een CNAME-record bij uw domeinnaamprovider als u een aangepast domein naar het CDN-eindpunt wilt omleiden. De CNAME-record voor het *www*subdomein moeten vergelijkbaar zijn met het volgende:

    ![CNAME-record voor het www-subdomein opgeven](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. In the Azure portal, display the settings for your CDN endpoint. Navigate to **Custom domains** under **Settings** to configure the custom domain and the SSL certificate.
1. Selecteer **Aangepast domein toevoegen**, voer uw domeinnaam in en klik op **Toevoegen**.
1. Select the new custom domain mapping to provision an SSL certificate.
1. Set **Custom Domain HTTPS** to **ON**, then click **Save**. It may take several hours to configure your custom domain. The portal displays progress as shown in the following image.

    ![Screenshot showing progress of custom domain configuration](media/storage-blob-static-website-custom-domain/configure-custom-domain-https.png)

1. Test the mapping of your static website to your custom domain by accessing the URL for your custom domain.

For more information about enabling HTTPS for custom domains, see [Tutorial: Configure HTTPS on an Azure CDN custom domain](../../cdn/cdn-custom-ssl.md).

## <a name="next-steps"></a>Volgende stappen

In deel twee van deze zelfstudie hebt u geleerd hoe u een aangepast domein kunt configureren met SSL in Azure CDN voor uw statische website.

For more information about configuring and using Azure CDN, see [What is Azure CDN?](../../cdn/cdn-overview.md).