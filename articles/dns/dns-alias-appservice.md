---
title: Host load-balanced Azure web apps at the zone apex
description: Use an Azure DNS alias record to host load-balanced web apps at the zone apex
services: dns
author: asudbring
ms.service: dns
ms.topic: article
ms.date: 08/10/2019
ms.author: allensu
ms.openlocfilehash: a673a74f8f6f919e7ebb7fc3b065ee0742ab3a10
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74212374"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Host load-balanced Azure web apps at the zone apex

The DNS protocol prevents the assignment of anything other than an A or AAAA record at the zone apex. An example zone apex is contoso.com. This restriction presents a problem for application owners who have load-balanced applications behind Traffic Manager. It isn't possible to point at the Traffic Manager profile from the zone apex record. As a result, application owners must use a workaround. A redirect at the application layer must redirect from the zone apex to another domain. An example is a redirect from contoso.com to www\.contoso.com. This arrangement presents a single point of failure for the redirect function.

With alias records, this problem no longer exists. Now application owners can point their zone apex record to a Traffic Manager profile that has external endpoints. Application owners can point to the same Traffic Manager profile that's used for any other domain within their DNS zone.

For example, contoso.com and www\.contoso.com can point to the same Traffic Manager profile. This is the case as long as the Traffic Manager profile has only external endpoints configured.

In this article, you learn how to create an alias record for your domain apex, and configure your Traffic Manager profile end points for your web apps.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet een beschikbare domeinnaam hebben die u in Azure DNS kunt hosten om te testen. U moet het volledige beheer over dit domein hebben. Volledig beheer betekent ook de mogelijkheid om naamserverrecords (NS) voor het domein in te stellen.

Zie voor instructies voor het hosten van uw domein in Azure DNS [Zelfstudie: Uw domein in Azure DNS hosten](dns-delegate-domain-azure-dns.md).

Het voorbeelddomein dat wordt gebruikt voor deze zelfstudie is contoso.com, maar u moet uw eigen domeinnaam gebruiken.

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Create a resource group to hold all the resources used in this article.

## <a name="create-app-service-plans"></a>Create App Service plans

Create two Web App Service plans in your resource group using the following table for configuration information. For more information about creating an App Service plan, see [Manage an App Service plan in Azure](../app-service/app-service-plan-manage.md).


|Naam  |Besturingssysteem  |Locatie  |Prijsniveau  |
|---------|---------|---------|---------|
|ASP-01     |Windows|VS - oost|Dev/Test D1-Shared|
|ASP-02     |Windows|VS - centraal|Dev/Test D1-Shared|

## <a name="create-app-services"></a>Create App Services

Create two web apps, one in each App Service plan.

1. On upper left corner of the Azure portal page, select **Create a resource**.
2. Type **Web app** in the search bar and press Enter.
3. Select **Web App**.
4. Selecteer **Maken**.
5. Accept the defaults, and use the following table to configure the two web apps:

   |Naam<br>(must be unique within .azurewebsites.net)|Resourcegroep |Runtime stack|Regio|App Service Plan/Location
   |---------|---------|-|-|-------|
   |App-01|Use existing<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - oost|ASP-01(D1)|
   |App-02|Use existing<br>Uw resourcegroep selecteren|.NET Core 2.2|VS - centraal|ASP-02(D1)|

### <a name="gather-some-details"></a>Gather some details

Now you need to note the IP address and host name for the web apps.

1. Open your resource group and select your first web app (**App-01** in this example).
2. In the left column, select **Properties**.
3. Note the address under **URL**, and under **Outbound IP Addresses** note the first IP address in the list. You'll use this information later when you configure your Traffic Manager end points.
4. Repeat for **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Een Traffic Manager-profiel maken

Create a Traffic Manager profile in your resource group. Use the defaults and type a unique name within the trafficmanager.net namespace.

For information about creating a Traffic Manager profile, see [Quickstart: Create a Traffic Manager profile for a highly available web application](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Eindpunten maken

Now you can create the endpoints for the two web apps.

1. Open your resource group and select your Traffic Manager profile.
2. In the left column, select **Endpoints**.
3. Selecteer **Toevoegen**.
4. Use the following table to configure the endpoints:

   |Type  |Naam  |Kiezen  |Locatie  |Custom Header settings|
   |---------|---------|---------|---------|---------|
   |External endpoint     |End-01|IP address you recorded for App-01|VS - oost|host:\<the URL you recorded for App-01\><br>Example: **host:app-01.azurewebsites.net**|
   |External endpoint     |End-02|IP address you recorded for App-02|VS - centraal|host:\<the URL you recorded for App-02\><br>Example: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Create DNS zone

You can either use an existing DNS zone for testing, or you can create a new zone. To create and delegate a new DNS zone in Azure, see [Tutorial: Host your domain in Azure DNS](dns-delegate-domain-azure-dns.md).

## <a name="add-a-txt-record-for-custom-domain-validation"></a>Add a TXT record for custom domain validation

When you add a custom hostname to your web apps, it will look for a specific TXT record to validate your domain.

1. Open your resource group and select the DNS zone.
2. Selecteer **Recordset**.
3. Add the record set using the following table. For the value, use the actual web app URL that you previously recorded:

   |Naam  |Type  |Waarde|
   |---------|---------|-|
   |@     |TXT|App-01.azurewebsites.net|


## <a name="add-a-custom-domain"></a>Een aangepast domein toevoegen

Add a custom domain for both web apps.

1. Open your resource group and select your first web app.
2. In the left column, select **Custom domains**.
3. Under **Custom Domains**, select **Add custom domain**.
4. Under **Custom domain**, type your custom domain name. For example, contoso.com.
5. Selecteer **Valideren**.

   Your domain should pass validation and show green check marks next to **Hostname availability** and **Domain ownership**.
5. Selecteer **Aangepast domein toevoegen**.
6. To see the new hostname under **Hostnames assigned to site**, refresh your browser. The refresh on the page doesn't always show changes right away.
7. Repeat this procedure for your second web app.

## <a name="add-the-alias-record-set"></a>Add the alias record set

Now add an alias record for the zone apex.

1. Open your resource group and select the DNS zone.
2. Selecteer **Recordset**.
3. Add the record set using the following table:

   |Naam  |Type  |Alias record set  |Alias type  |Azure resource|
   |---------|---------|---------|---------|-----|
   |@     |A|Ja|Azure resource|Traffic Manager - your profile|


## <a name="test-your-web-apps"></a>Test your web apps

Now you can test to make sure you can reach your web app and that it's being load balanced.

1. Open a web browser and browse to your domain. For example, contoso.com. You should see the default web app page.
2. Stop your first web app.
3. Close your web browser, and wait a few minutes.
4. Start your web browser and browse to your domain. You should still see the default web app page.
5. Stop your second web app.
6. Close your web browser, and wait a few minutes.
7. Start your web browser and browse to your domain. You should see Error 403, indicating that the web app is stopped.
8. Start your second web app.
9. Close your web browser, and wait a few minutes.
10. Start your web browser and browse to your domain. You should see the default web app page again.

## <a name="next-steps"></a>Volgende stappen

To learn more about alias records, see the following articles:

- [Tutorial: Configure an alias record to refer to an Azure public IP address](tutorial-alias-pip.md)
- [Tutorial: Configure an alias record to support apex domain names with Traffic Manager](tutorial-alias-tm.md)
- [Veelgestelde vragen over DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

To learn how to migrate an active DNS name, see [Migrate an active DNS name to Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
