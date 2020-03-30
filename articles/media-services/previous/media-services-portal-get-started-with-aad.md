---
title: Aan de slag met Azure AD-verificatie met de Azure-portal| Microsoft Documenten
description: Meer informatie over het gebruik van de Azure-portal om toegang te krijgen tot Azure Active Directory (Azure AD)-verificatie om de Azure Media Services API te gebruiken.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: ee04fa7120f5510d703d72e662036f4fe952cd66
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78330665"
---
# <a name="get-started-with-azure-ad-authentication-by-using-the-azure-portal"></a>Aan de slag met verificatie bij Azure Active Directory met de Azure-portal

> [!NOTE]
> Er worden geen nieuwe functies of functionaliteit meer aan Media Services v2. toegevoegd. <br/>Bekijk de nieuwste versie, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Zie ook [migratierichtlijnen van v2 naar v3](../latest/migrate-from-v2-to-v3.md)

Meer informatie over het gebruik van de Azure-portal om toegang te krijgen tot Azure Active Directory (Azure AD)-verificatie om toegang te krijgen tot de Azure Media Services API.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account. Als u geen account hebt, begint u met een [gratis proefversie van Azure.](https://azure.microsoft.com/pricing/free-trial/) 
- Een Media Services-account. Zie [Een Azure Media Services-account maken met de Azure-portal](media-services-portal-create-account.md)voor meer informatie.

Wanneer u Azure AD-verificatie gebruikt met Azure Media Services, hebt u twee verificatieopties:

- **Serviceprincipal-verificatie**. Een service verifiëren. Toepassingen die deze verificatiemethode vaak gebruiken, zijn apps die daemonservices, services op het middensegment of geplande taken uitvoeren: web-apps, functie-apps, logische apps, API's of een microservice.
- **Gebruikersverificatie**. Verifieer een persoon die de app gebruikt om te communiceren met Media Services-bronnen. De interactieve toepassing moet eerst vragen aan de gebruiker om referenties. Een voorbeeld is een beheerconsole-app die door geautoriseerde gebruikers wordt gebruikt om coderingstaken of live streaming te controleren. 

## <a name="access-the-media-services-api"></a>Toegang kunt krijgen tot de Media Services API

Op deze pagina u de verificatiemethode selecteren die u wilt gebruiken om verbinding te maken met de API. De pagina bevat ook de waarden die u nodig hebt om verbinding te maken met de API.

1. Selecteer in de [Azure-portal](https://portal.azure.com/)uw Media Services-account.
2. Selecteer hoe u verbinding maken met de Media Services API.
3. Selecteer **onder Verbinding maken met de API voor Media Services**de API-versie van Media Services waarmee u verbinding wilt maken.

## <a name="service-principal-authentication--recommended"></a>Serviceprincipal-verificatie (aanbevolen)

Verifieert een service met behulp van een Azure Active Directory-app (Azure AD) en geheim. Dit wordt aanbevolen voor alle middle-tier services die naar de Media Services API worden aangeroepen. Voorbeelden zijn Web Apps, Functions, Logic Apps, API's en microservices. Dit is de aanbevolen verificatiemethode.

### <a name="manage-your-azure-ad-app-and-secret"></a>Uw Azure AD-app en geheim beheren

Met de sectie **Uw AAD-app en geheim beheren** u een nieuwe Azure AD-app selecteren of maken en een geheim genereren. Om veiligheidsredenen kan het geheim niet worden weergegeven nadat het mes is gesloten. De toepassing gebruikt de toepassings-ID en het geheim voor verificatie om een geldig token voor mediaservices te verkrijgen.

Zorg ervoor dat u over voldoende machtigingen beschikt om een toepassing te registreren bij uw Azure AD-tenant en om de toepassing toe te wijzen aan een rol in uw Azure-abonnement. Zie [Vereiste machtigingen voor](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)meer informatie.

### <a name="connect-to-media-services-api"></a>Verbinding maken met De API van Media Services

De **Connect to Media Services API** biedt u waarden die u gebruikt om uw serviceprincipal-toepassing met elkaar te verbinden. U tekstwaarden opmaken of de JSON- of XML-blokken kopiëren.

## <a name="user-authentication"></a>Gebruikersverificatie

Deze optie kan worden gebruikt om een werknemer of lid van een Azure Active Directory te verifiëren die een app gebruikt om te communiceren met Media Services-bronnen. De interactieve toepassing moet eerst de gebruiker om de referenties van de gebruiker vragen. Deze verificatiemethode mag alleen worden gebruikt voor beheertoepassingen.

### <a name="connect-to-media-services-api"></a>Verbinding maken met De API van Media Services

Kopieer uw referenties om uw gebruikerstoepassing te verbinden vanuit de **api-sectie Verbinding maken met Media Services.** U tekstwaarden opmaken of de JSON- of XML-blokken kopiëren.

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met [het uploaden van bestanden naar je account.](media-services-portal-upload-files.md)
