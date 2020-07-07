---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 66d3397fae24ee2546dae4eb5d7c9d188f9ede99
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78944115"
---
> [!NOTE]
> U kunt Azure DNS gebruiken om een aangepaste DNS-naam te configureren voor Azure App Service. Zie [Use Azure DNS to provide custom domain settings for an Azure service](../articles/dns/dns-custom-domain.md#app-service-web-apps) (Azure DNS gebruiken om aangepaste domeininstellingen te verstrekken voor een Azure-service) voor meer informatie.
>
>

Meld u aan bij de website van uw domeinprovider.

Ga naar de pagina voor het beheren van DNS-records. Elke domeinprovider heeft zijn eigen interface voor het beheren van DNS-records. Raadpleeg daarom de documentatie van de provider. Doorgaans heeft het sitegedeelte waar u moet zijn, een naam als **Domain Name**, **DNS** of **Name Server Management**. 

Vaak kunt u de pagina met DNS-records vinden door uw accountgegevens te bekijken en te zoeken naar een link als **My domains** (of iets vergelijkbaars). Ga naar de betreffende pagina en kijk of u daar een link ziet zoals **Zone file**, **DNS Records** of **Advanced configuration**.

In de schermafbeelding hieronder wordt een voorbeeld van een pagina met DNS-records weergegeven:

![Voorbeeld van een pagina met DNS-records](./media/app-service-web-access-dns-records-no-h/example-record-ui.png)

In het getoonde voorbeeld selecteert u **Add** om een record te maken. Sommige providers hebben afzonderlijke links voor verschillende typen records. Raadpleeg ook hiervoor de documentatie van de provider.

> [!NOTE]
> Bij bepaalde providers, zoals GoDaddy, worden wijzigingen in DNS-records pas van kracht wanneer u op een afzonderlijke link **Save Changes** klikt. 
