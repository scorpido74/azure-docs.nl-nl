---
title: Zelf studie voor het configureren van Azure Active Directory B2C met whoIam
titleSuffix: Azure AD B2C
description: Meer informatie over het integreren van Azure AD B2C verificatie met whoIam voor gebruikers verificatie
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 6545c5f40004dc50904618a8ea734eb73eeee933
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817429"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Zelf studie voor het configureren van WhoIAM met Azure Active Directory B2C

In deze voorbeeld zelfstudie bieden we richt lijnen voor het configureren van [WhoIAM](https://www.whoiam.ai/brims/) -identiteits beheersysteem (BRIMS) in uw omgeving en het integreren met Azure AD B2C.

WhoIAM BRIMS is een set apps en services die in uw omgeving wordt geïmplementeerd. Het biedt spraak-, SMS-en e-mail verificatie van uw gebruikers database. BRIMS werkt in combi natie met uw bestaande oplossing voor identiteits-en toegangs beheer en is platform neutraal.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

- Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).

- [Een Azure AD B2C-Tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) die is gekoppeld aan uw Azure-abonnement.

- Een WhoIAM- [proef account](https://www.whoiam.ai/contact-us/)

## <a name="scenario-description"></a>Scenariobeschrijving

De WhoIAM-integratie bevat de volgende onderdelen:

- Azure AD B2C Tenant: de autorisatie server die verantwoordelijk is voor het controleren van de referenties van de gebruiker op basis van het aangepaste beleid dat in de Tenant is gedefinieerd. Het wordt ook wel de ID-provider genoemd.

- Een beheer portal voor het beheer van clients en hun configuraties

- Een API-service die verschillende functies via eind punten beschikbaar maakt  

- Azure Cosmos DB die wordt gebruikt als back-end voor zowel de BRIMS-beheer portal als de API-service

In het volgende architectuur diagram wordt de implementatie weer gegeven.

![scherm afbeelding voor whoiam-Architecture-diagram](media/partner-whoiam/whoiam-architecture-diagram.png)

|Stap | Beschrijving |
|:-----| :-----------|
| 1. | De gebruiker ontvangt op een aanmeldings pagina. De gebruiker start een registratie-of aanmeldings aanvraag voor een app die Azure AD B2C als id-provider gebruikt.
| 2. | Als onderdeel van de verificatie vraagt de gebruiker om het eigendom van hun e-mail adres of telefoon nummer te verifiëren of om hun stem als biometrische verificatie factor te gebruiken.  
| 3. | Azure AD B2C roept de BRIMS API-service aan die wordt door gegeven aan het e-mail adres, telefoon nummer en de spraak opname van de gebruiker
| 4. | BRIMS maakt gebruik van vooraf gedefinieerde configuraties, zoals volledig aanpas bare e-mail-en SMS-sjablonen om te communiceren met de gebruiker in hun respectieve taal die consistent is met het uiterlijk van de app.
| 5. | Wanneer de identiteits controle van een gebruiker is voltooid, retourneert BRIMS een token om Azure AD B2C het resultaat van de verificatie aan te geven. Azure AD B2C verleent de gebruiker vervolgens toegang tot de app of de verificatie poging mislukt.  

## <a name="onboard-with-whoiam"></a>Onboard met WhoIAM

1. Neem contact op met [WhoIAM](https://www.whoiam.ai/contact-us/) en maak een BRIMS-account.

2. Nadat een account is gemaakt, gebruikt u de richt lijnen voor onboarding die voor u beschikbaar zijn en configureert u de volgende Azure-Services:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) : wordt gebruikt voor beveiligde opslag van wacht woord, zoals e-mail service-wacht woorden.

    - [Azure app service](https://azure.microsoft.com/services/app-service/) : wordt gebruikt voor het hosten van de BRIMS-API en de beheer Portal Services

    - [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) : wordt gebruikt voor het verifiëren van gebruikers met beheerders rechten voor de beheer Portal

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) : wordt gebruikt voor het opslaan en ophalen van instellingen

    - [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview#:~:text=Application%20Insights%2C%20a%20feature%20of%20Azure%20Monitor%2C%20is,professionals.%20Use%20it%20to%20monitor%20your%20live%20applications.) (optioneel): wordt gebruikt om u aan te melden bij de API en de beheer Portal

3. Implementeer de BRIMS-API en de BRIMS-beheer Portal in uw Azure-omgeving.

4. Azure AD B2C aangepaste beleids voorbeelden zijn beschikbaar in uw BRIMS-onboarding-documentatie. Volg het document om uw app te configureren en gebruik het BRIMS-platform voor verificatie van de gebruikers identiteit.  

Raadpleeg de [product documentatie](https://www.whoiam.ai/brims/) voor meer informatie over de BRIMS van WhoIAM

## <a name="test-the-user-flow"></a>De gebruikersstroom testen

1. Open de Azure AD B2C Tenant en selecteer onder beleids regels het **Framework identiteits ervaring**selecteren.

2. Selecteer uw eerder gemaakte **SignUpSignIn**.

3. Selecteer **gebruikers stroom uitvoeren** en selecteer de instellingen:

   a. **Toepassing**: Selecteer de geregistreerde app (voor beeld is JWT)

   b. **Antwoord-URL**: de **omleidings-URL** selecteren

   c. Selecteer **gebruikers stroom uitvoeren**.

4. Ga door naar de registratie stroom en maak een account

5. De BRIMS-service wordt aangeroepen tijdens de stroom, nadat het gebruikers kenmerk is gemaakt. Als de stroom onvolledig is, controleert u of de gebruiker niet is opgeslagen in de map.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie:

- [Aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Aan de slag met aangepast beleid in Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
