---
title: Beheer hulpprogramma implementeren-Azure
description: Een gebruikers interface-hulp programma installeren voor het beheren van virtuele bureau blad-resources van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 06/04/2019
ms.author: helohr
ms.openlocfilehash: f07403d8d0b2c6d0dd7a6b851a87b47b0c32501a
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679489"
---
# <a name="tutorial-deploy-a-management-tool"></a>Zelfstudie: Een hulpprogramma voor beheer implementeren

Het beheer programma biedt een gebruikers interface (UI) voor het beheren van micro soft-resources voor virtuele Bureau bladen. In deze zelf studie leert u hoe u het beheer programma implementeert en er verbinding mee maakt.

>[!NOTE]
>Deze instructies gelden voor een Windows-specifieke configuratie voor virtueel bureau blad die kan worden gebruikt met de bestaande processen van uw organisatie.

## <a name="important-considerations"></a>Belang rijke overwegingen

Omdat de app toestemming nodig heeft om te communiceren met het virtuele bureau blad van Windows, ondersteunt dit hulp programma geen Business-to-Business (B2B)-scenario's. Elk Azure Active Directory (AAD)-abonnement van de Tenant heeft een eigen afzonderlijke implementatie van het beheer programma nodig.

Dit beheer programma is een voor beeld. Micro soft zal belang rijke updates voor de beveiliging en kwaliteit bieden. De [bron code is beschikbaar in github](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy). Klanten en partners worden aangemoedigd het hulp programma aan te passen aan de behoeften van hun bedrijf.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>Wat u nodig hebt om de Azure Resource Manager-sjabloon uit te voeren

Voordat u de Azure Resource Manager-sjabloon implementeert, hebt u een Azure Active Directory gebruiker nodig om de beheer GEBRUIKERSINTERFACE te implementeren. Deze gebruiker moet:

- Azure Multi-Factor Authentication (MFA) is uitgeschakeld
- Machtiging voor het maken van resources in uw Azure-abonnement
- Toestemming voor het maken van een Azure AD-toepassing. Volg deze stappen om te controleren of uw gebruiker over de [vereiste machtigingen](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions)beschikt.

Nadat u de Azure Resource Manager-sjabloon hebt geïmplementeerd, moet u de beheer GEBRUIKERSINTERFACE starten om te valideren. Deze gebruiker moet:
- Een roltoewijzing hebben om uw Windows Virtual Desktop-Tenant weer te geven of te bewerken

## <a name="run-the-azure-resource-manager-template-to-provision-the-management-ui"></a>Voer de Azure Resource Manager sjabloon uit om de gebruikers interface van het beheer in te richten

Voordat u begint, moet u ervoor zorgen dat de server-en client-apps toestemming hebben op de [Windows-pagina toestemming voor virtueel bureau blad](https://rdweb.wvd.microsoft.com) voor de Azure Active Directory (Aad) die wordt weer gegeven.

Volg deze instructies voor het implementeren van de Azure resource management-sjabloon:

1. Ga naar de [pagina github Azure RDS-sjablonen](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/wvd-management-ux/deploy).
2. Implementeer de sjabloon in Azure.
    - Als u in een bedrijfs abonnement implementeert, schuift u omlaag en selecteert u **implementeren naar Azure**. Zie de [richt lijnen voor sjabloon parameters](#guidance-for-template-parameters).
    - Als u in een Cloud Solution Provider-abonnement implementeert, volgt u deze instructies om te implementeren in Azure:
        1. Schuif omlaag en klik met de rechter muisknop op **implementeren naar Azure**, en selecteer vervolgens **koppelings locatie kopiëren**.
        2. Open een tekst editor zoals Klad blok en plak de koppeling daar.
        3. Voer direct na <https://portal.azure.com/> en vóór de hashtag (#) een apen staartje (@) in, gevolgd door de domein naam van de Tenant. Hier volgt een voor beeld van de notatie: <https://portal.azure.com/@Contoso.onmicrosoft.com#create/>.
        4. Meld u aan bij de Azure Portal als gebruiker met beheerders-en Inzender machtigingen voor het Cloud Solution Provider-abonnement.
        5. Plak de koppeling die u naar de tekst editor hebt gekopieerd naar de adres balk.

### <a name="guidance-for-template-parameters"></a>Richt lijnen voor sjabloon parameters
U kunt als volgt para meters invoeren voor het configureren van het hulp programma:

- Dit is de URL van de extern bureau blad-Broker: https: \//rdbroker. WVD. Microsoft. com/
- Dit is de bron-URL: https: \//Mrs-Prod. AAM. GBL/Mevr-RDInfra-Prod
- Gebruik uw AAD-referenties met MFA uitgeschakeld om u aan te melden bij Azure. Bekijk [wat u nodig hebt om de Azure Resource Manager-sjabloon uit te voeren](#what-you-need-to-run-the-azure-resource-manager-template).
- Gebruik een unieke naam voor de toepassing die wordt geregistreerd in uw Azure Active Directory voor het beheer programma. bijvoorbeeld Apr3UX.

## <a name="provide-consent-for-the-management-tool"></a>Toestemming geven voor het beheer programma

Nadat de GitHub-Azure Resource Manager sjabloon is voltooid, vindt u een resource groep met twee app-Services samen met één app service-plan in de Azure Portal.

Voordat u zich aanmeldt en het beheer programma gebruikt, moet u toestemming geven voor de nieuwe Azure Active Directory-toepassing die aan het beheer programma is gekoppeld. Door toestemming te geven, kunt u het beheer programma toestaan om het beheer van virtuele Windows-Bureau bladen te laten aanroepen namens de gebruiker die is aangemeld bij het hulp programma.

![Een scherm opname met de machtigingen die worden gegeven wanneer u akkoord gaat met het beheer programma voor de gebruikers interface.](media/management-ui-delegated-permissions.png)

Als u wilt bepalen welke gebruiker u kunt gebruiken om u aan te melden bij het hulp programma, gaat u naar de [pagina met gebruikers instellingen van Azure Active Directory](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) en noteert u de waarde voor **gebruikers kan toestemming geven om toegang te krijgen tot Bedrijfs gegevens**namens hen.

![Een scherm opname waarin wordt getoond of gebruikers toestemming kunnen geven voor toepassingen alleen voor hun gebruiker.](media/management-ui-user-consent-allowed.png)

- Als de waarde is ingesteld op **Ja**, kunt u zich aanmelden met een gebruikers account in de Azure Active Directory en alleen toestemming geven voor die gebruiker. Als u zich echter op een later tijdstip bij het beheer programma aanmeldt met een andere gebruiker, moet u dezelfde toestemming opnieuw uitvoeren.
- Als de waarde is ingesteld op **Nee**, moet u zich aanmelden als globale beheerder in de Azure Active Directory en toestemming geven voor de beheerder voor alle gebruikers in de Directory. Geen van de andere gebruikers wordt gevraagd om toestemming te vragen.


Nadat u hebt bepaald welke gebruiker u gaat gebruiken om toestemming te geven, volgt u deze instructies om toestemming te geven voor het hulp programma:

1. Ga naar uw Azure-resources, selecteer de Azure-app Services-resource met de naam die u in de sjabloon hebt gegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld. bijvoorbeeld <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Meld u aan met het juiste Azure Active Directory gebruikers account.
3. Als u een globale beheerder hebt geverifieerd, kunt u nu het selectie vakje inschakelen om toestemming te geven namens **uw organisatie**. Selecteer **accepteren** om toestemming te geven.
   
   ![Een scherm opname waarin de volledige toestemming pagina wordt weer gegeven die de gebruiker of beheerder ziet.](media/management-ui-consent-page.png)

Hiermee gaat u nu naar het beheer programma.

## <a name="use-the-management-tool"></a>Het beheer programma gebruiken

Nadat u toestemming hebt gegeven voor de organisatie of voor een opgegeven gebruiker, kunt u op elk gewenst moment toegang krijgen tot het beheer programma.

Volg deze instructies om het hulp programma te starten:

1. Selecteer de Azure-app Services-resource met de naam die u in de sjabloon hebt gegeven (bijvoorbeeld Apr3UX) en navigeer naar de URL die eraan is gekoppeld. bijvoorbeeld <https://rdmimgmtweb-210520190304.azurewebsites.net>.
2. Meld u aan met de referenties van uw Windows-virtueel bureau blad.
3. Wanneer u wordt gevraagd om een Tenant groep te kiezen, selecteert u **standaard Tenant groep** in de vervolg keuzelijst.

> [!NOTE]
> Als u een aangepaste Tenant groep hebt, voert u de naam hand matig in in plaats van te kiezen in de vervolg keuzelijst.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u het beheer programma implementeert en er verbinding mee maakt, leert u hoe u Azure Service Health kunt gebruiken om service problemen en status adviezen te bewaken.

> [!div class="nextstepaction"]
> [Zelf studie voor service waarschuwingen instellen](./set-up-service-alerts.md)
