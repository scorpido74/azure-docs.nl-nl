---
title: Azure IoT Central-toepassingen maken en beheren vanuit de CSP-portal | Microsoft Documenten
description: Als CSP u namens uw klant een Azure IoT Central-toepassing maken.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: how-to
manager: philmea
ms.openlocfilehash: 02481d5dcbaba15c9b17a27348207d9af64f3355
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982035"
---
# <a name="create-and-manage-an-azure-iot-central-application-from-the-csp-portal"></a>Een Azure IoT Central-toepassing maken en beheren vanuit de CSP-portal

Het CSP-programma (Microsoft Cloud Solution Provider) is een Microsoft Reseller-programma. De bedoeling is om onze kanaalpartners te voorzien van een one-stop-programma om alle Microsoft Commercial Online Services door te verkopen. Meer informatie over het [Cloud Solution Provider-programma](https://partner.microsoft.com/cloud-solution-provider).

Als CSP u Microsoft Azure IoT Central-toepassingen namens uw klanten maken en beheren via het [Microsoft Partner Center.](https://partnercenter.microsoft.com/partner/home) Wanneer Azure IoT Central-toepassingen namens klanten worden gemaakt door CSP's, net als bij andere CSP-beheerde Azure-services, beheren CSP's facturering voor klanten. Er worden kosten in rekening gebracht voor Azure IoT Central in uw totale factuur in het Microsoft Partner Center.

Meld u om te beginnen aan bij uw account op de Microsoft Partner Portal en selecteert een klant voor wie u een Azure IoT Central-toepassing wilt maken. Navigeer vanaf de linkernavigatie naar Servicebeheer voor de klant.

![Microsoft Partner Center, klantbeeld](media/howto-create-and-manage-applications-csp/image1.png)

Azure IoT Central wordt vermeld als een service die beschikbaar is voor beheer. Selecteer de Koppeling Azure IoT Central op de pagina om nieuwe toepassingen te maken of bestaande toepassingen voor deze klant te beheren.

![Azure IoT Central beschikbaar voor beheer](media/howto-create-and-manage-applications-csp/image2.png)

U komt terecht op de pagina Azure IoT Central Application Manager. Azure IoT Central houdt de context bij dat u afkomstig bent uit het Microsoft Partner Center en dat u die specifieke klant bent gaan beheren. U ziet dit erkend in de koptekst van de pagina Toepassingsbeheer. Vanaf hier u naar een bestaande toepassing navigeren die u eerder voor deze klant hebt gemaakt om een nieuwe toepassing voor de klant te beheren of te maken.

![Manager maken voor CSP's](media/howto-create-and-manage-applications-csp/image3.png)

Als u een Azure IoT Central-toepassing wilt maken, selecteert u **Bouwen** in het linkermenu. Kies een van de branchesjablonen of kies **Aangepaste app** om een toepassing helemaal opnieuw te maken. Hiermee wordt de pagina Het maken van toepassingen geladen. U moet alle velden op deze pagina invullen en vervolgens **Maken**kiezen. Meer informatie over elk van de onderstaande velden vindt u.

![Toepassingspagina maken voor CSP's](media/howto-create-and-manage-applications-csp/image4.png)

![Toepassingspagina maken voor CSP's](media/howto-create-and-manage-applications-csp/image4-1.png)

![Toepassingspagina maken voor factureringsgegevens van CSP's](media/howto-create-and-manage-applications-csp/image4-2.png)

## <a name="pricing-plan"></a>Prijsplan

U alleen toepassingen maken die een standaard prijsplan als Een CSP gebruiken. Als u Azure IoT Central aan uw klant wilt presenteren, u een toepassing maken die het gratis prijsplan afzonderlijk gebruikt. Meer informatie over de gratis en standaard prijsplannen vindt u op de [prijspagina van Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

U alleen toepassingen maken die een standaard prijsplan als Een CSP gebruiken. Als u Azure IoT Central aan uw klant wilt presenteren, u een toepassing maken die het gratis prijsplan afzonderlijk gebruikt. Meer informatie over de gratis en standaard prijsplannen vindt u op de [prijspagina van Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).

## <a name="application-name"></a>De naam van de toepassing

De naam van uw toepassing wordt weergegeven op de pagina **Toepassingsbeheer** en in elke Azure IoT Central-toepassing. U een naam kiezen voor uw Azure IoT Central-toepassing. Kies een naam die zinvol is voor u en voor anderen in uw organisatie.

## <a name="application-url"></a>Toepassings-URL

De url van de toepassing is de link naar uw toepassing. U er een bladwijzer in uw browser op slaan of delen met anderen.

Wanneer u de naam voor uw toepassing invoert, wordt de URL van uw toepassing automatisch gegenereerd. Als u dat liever hebt, u een andere URL voor uw toepassing kiezen. Elke Azure IoT Central URL moet uniek zijn binnen Azure IoT Central. U ziet een foutbericht als de gekozen URL al is overgenomen.

## <a name="directory"></a>Directory

Aangezien Azure IoT Central context heeft die u hebt gekomen om de klant te beheren die u hebt geselecteerd in de Microsoft Partner Portal, ziet u alleen de Azure Active Directory-tenant voor die klant in het veld Directory. 

Een Azure Active Directory-tenant bevat gebruikersidentiteiten, referenties en andere organisatiegegevens. Meerdere Azure-abonnementen kunnen worden gekoppeld aan één Azure Active Directory-tenant.

Zie [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/)voor meer informatie.

## <a name="azure-subscription"></a>Azure-abonnement

Met een Azure-abonnement kunt u exemplaren maken van Azure-services. Azure IoT Central vindt automatisch alle Azure-abonnementen van de klant waartoe u toegang hebt en geeft deze weer in een vervolgkeuzelijst op de pagina **Toepassing maken.** Kies een Azure-abonnement om een nieuwe Azure IoT Central-toepassing te maken.

Als u geen Azure-abonnement hebt, u er een maken in het Microsoft Partner Center. Nadat u het Azure-abonnement hebt gemaakt, gaat u terug naar de pagina **Toepassing maken**. Uw nieuwe abonnement wordt weergegeven in de vervolgkeuzelijst.**Azure-abonnement**.

Zie [Azure-abonnementen](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing)voor meer informatie.

## <a name="location"></a>Locatie

**Locatie** is de [geografie](https://azure.microsoft.com/global-infrastructure/geographies/) waar u de toepassing wilt maken. Meestal moet u de locatie kiezen die fysiek het dichtst bij uw apparaten ligt om optimale prestaties te krijgen. Momenteel u een IoT Central-toepassing maken in de regio's **Australië,** **Azië-Pacific,** **Europa,** **de Verenigde Staten,** **het Verenigd Koninkrijk**en **Japan.** Zodra u een locatie kiest, u uw toepassing niet later naar een andere locatie verplaatsen.

## <a name="application-template"></a>Toepassingsjabloon

Kies de toepassingssjabloon die u voor uw toepassing wilt gebruiken.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een Azure IoT Central-toepassing als Een CSP maken, volgt hier de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Uw toepassing beheren](howto-administer.md)
