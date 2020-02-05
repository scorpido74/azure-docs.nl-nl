---
title: Azure IoT Central-toepassingen maken en beheren vanuit de CSP-Portal | Microsoft Docs
description: Als CSP kunt u een Azure IoT Central-toepassing maken namens uw klant.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 40c5f612b5b1571bb3d39f452d64a7005701f7c1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023800"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Een Azure IoT Central-toepassing maken en beheren vanuit de CSP-Portal

Het programma voor de Microsoft Cloud Solution Provider (CSP) is een micro soft-programma voor wederverkopers. Het is de bedoeling om onze kanaal partners te voorzien van een programma met één stop om alle micro soft-commerciële online services door te verkopen. Meer informatie over het [programma Cloud Solution Provider](https://partner.microsoft.com/cloud-solution-provider).

Als CSP kunt u Microsoft Azure IoT Central-toepassingen maken en beheren namens uw klanten via het [micro soft partner centrum](https://partnercenter.microsoft.com/partner/home). Wanneer Azure IoT Central-toepassingen worden gemaakt namens klanten door Csp's, net als bij andere door de CSP beheerde Azure-Services, beheren Csp's facturering voor klanten. Kosten voor Azure IoT Central worden weer gegeven in uw factuur totaal in het micro soft partner centrum.

Meld u aan bij uw account in de micro soft-Partner Portal en selecteer een klant voor wie u een Azure IoT Central-toepassing wilt maken. Navigeer naar Service beheer voor de klant in het linkernavigatievenster.

![Micro soft Partner Center, klant weergave](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central wordt vermeld als een service die beschikbaar is voor beheer. Selecteer de koppeling Azure IoT Central op de pagina om nieuwe toepassingen te maken of bestaande toepassingen voor deze klant te beheren.

![Azure IoT Central beschikbaar voor beheer](media/howto-create-and-manage-applications-csp/image2.png)

U bent op de pagina Azure IoT Central Application Manager. Azure IoT Central houdt u de context van het micro soft partner centrum en u hebt deze gearriveerd om die bepaalde klant te beheren. U ziet dit bevestigd in de koptekst van de pagina Toepassings beheer. Hier kunt u navigeren naar een bestaande toepassing die u eerder hebt gemaakt voor deze klant om een nieuwe toepassing voor de klant te beheren of maken.

![Manager voor Csp's maken](media/howto-create-and-manage-applications-csp/image3.png)

Als u een Azure IoT Central-toepassing wilt maken, selecteert u **samen stellen** in het menu links. Kies een van de branche sjablonen of kies **verouderde toepassing** om een volledig nieuwe toepassing te maken. Hiermee wordt de pagina voor het maken van de toepassing geladen. U moet alle velden op deze pagina volt ooien en vervolgens **maken**kiezen. U vindt meer informatie over elk van de onderstaande velden.

![Toepassings pagina voor Csp's maken](media/howto-create-and-manage-applications-csp/image4.png)

![Toepassings pagina voor Csp's maken](media/howto-create-and-manage-applications-csp/image4-1.png)

![Een toepassings pagina maken voor Csp's facturerings gegevens](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Prijs plan

U kunt alleen toepassingen maken die gebruikmaken van een standaard prijs plan als CSP. Als u Azure IoT Central wilt presen teren aan uw klant, kunt u een toepassing maken die gebruikmaakt van het gratis prijs plan afzonderlijk. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/).

U kunt alleen toepassingen maken die gebruikmaken van een standaard prijs plan als CSP. Als u Azure IoT Central wilt presen teren aan uw klant, kunt u een toepassing maken die gebruikmaakt van het gratis prijs plan afzonderlijk. Meer informatie over de gratis en standaard prijzen abonnementen op de [pagina met prijzen voor Azure IOT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>De naam van de toepassing

De naam van uw toepassing wordt weer gegeven op de pagina **toepassings beheer** en binnen elke Azure IOT Central-toepassing. U kunt een wille keurige naam kiezen voor uw Azure IoT Central-toepassing. Kies een naam die voor u en anderen in uw organisatie zinvol is.

## <a name="application-url"></a>Toepassings-URL

De toepassings-URL is de koppeling naar uw toepassing. U kunt een blad wijzer in uw browser opslaan of met anderen delen.

Wanneer u de naam voor uw toepassing opgeeft, wordt de URL van de toepassing automatisch gegenereerd. Als u wilt, kunt u een andere URL voor uw toepassing kiezen. Elke URL van Azure IoT Central moet uniek zijn binnen Azure IoT Central. Er wordt een fout bericht weer gegeven als de URL die u kiest al is gemaakt.

## <a name="directory"></a>Directory

Omdat Azure IoT Central context heeft dat u de klant die u hebt geselecteerd in de micro soft-Partner Portal hebt beheerd, ziet u alleen de Azure Active Directory Tenant voor die klant in het veld Directory. 

Een Azure Active Directory-Tenant bevat gebruikers identiteiten, referenties en andere informatie over de organisatie. Meerdere Azure-abonnementen kunnen worden gekoppeld aan één Azure Active Directory Tenant.

Zie [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)voor meer informatie.

## <a name="azure-subscription"></a>Azure-abonnement

Met een Azure-abonnement kunt u exemplaren van Azure-Services maken. Azure IoT Central detecteert automatisch alle Azure-abonnementen van de klant naar wie u toegang hebt en geeft deze weer in een vervolg keuzelijst op de pagina **toepassing maken** . Kies een Azure-abonnement om een nieuwe Azure IoT Central-toepassing te maken.

Als u geen Azure-abonnement hebt, kunt u er een maken in het micro soft partner centrum. Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Toepassing maken**. Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.

Zie [Azure-abonnementen](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)voor meer informatie.

## <a name="region"></a>Regio

Kies de regio of [geografische](https://azure.microsoft.com/global-infrastructure/geographies/) locatie waar u de Azure IOT Central-toepassing wilt maken. Normaal gesp roken moet u de regio kiezen die het meest geschikt is voor uw apparaten om optimaal te kunnen profiteren van de prestaties.

Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/regions/) en [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/)voor meer informatie.

U kunt de regio's bekijken waarin Azure IoT Central beschikbaar is op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=iot-central) .

> [!Note]
> Als u eenmaal een regio hebt gekozen, kunt u de toepassing later niet naar een andere regio verplaatsen.

## <a name="application-template"></a>Toepassingsjabloon

Kies de toepassings sjabloon die u wilt gebruiken voor uw toepassing.


## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Azure IoT Central-toepassing maakt als CSP, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
