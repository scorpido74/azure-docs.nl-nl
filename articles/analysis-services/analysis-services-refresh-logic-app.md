---
title: Vernieuwen met Logic Apps voor Azure Analysis Services-modellen | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u asynchrone vernieuwing voor Azure Analysis Services codeert met Azure Logic Apps.
author: chrislound
ms.service: analysis-services
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: chlound
ms.openlocfilehash: 78bc629598c0635b7760285d0507b7a85a4ab551
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127017"
---
# <a name="refresh-with-logic-apps"></a>Vernieuwen met Logic Apps

Met Logic Apps en REST-aanroepen u geautomatiseerde bewerkingen voor het vernieuwen van gegevens uitvoeren op uw tabelmodellen voor Azure Analysis, inclusief synchronisatie van alleen-lezen replica's voor het uitschalen van query's.

Zie [Asynchrone vernieuwing met de REST API](analysis-services-async-refresh.md)voor meer informatie over het gebruik van REST API's met Azure Analysis Services.

## <a name="authentication"></a>Authentication

Alle aanroepen moeten worden geverifieerd met een geldig Azure Active Directory -token (OAuth 2).  De voorbeelden in dit artikel gebruiken een Service Principal (SPN) om te verifiëren voor Azure Analysis Services. Zie [Een serviceprincipal maken met Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie.

## <a name="design-the-logic-app"></a>De logische app ontwerpen

> [!IMPORTANT]
> In de volgende voorbeelden wordt ervan uitgegaan dat de firewall van Azure Analysis Services is uitgeschakeld. Als de firewall is ingeschakeld, moet het openbare IP-adres van de initiator van de aanvraag op de witte lijst staan in de firewall van Azure Analysis Services. Zie [Limieten en configuratiegegevens voor Azure Logic Apps voor](../logic-apps/logic-apps-limits-and-config.md#configuration)meer informatie over IP-bereiken van Azure Logic Apps per regio.

### <a name="prerequisites"></a>Vereisten

#### <a name="create-a-service-principal-spn"></a>Een serviceprincipal (SPN) maken

Zie [Een serviceprincipal maken met Azure-portal](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over het maken van een serviceprincipal.

#### <a name="configure-permissions-in-azure-analysis-services"></a>Machtigingen configureren in Azure Analysis Services
 
De serviceprincipal die u maakt, moet serverbeheerdersmachtigingen op de server hebben. Zie Een [serviceprincipal toevoegen aan de serverbeheerderrol](analysis-services-addservprinc-admins.md)voor meer informatie.

### <a name="configure-the-logic-app"></a>De logische app configureren

In dit voorbeeld is de Logic App ontworpen om te activeren wanneer een HTTP-aanvraag wordt ontvangen. Hierdoor kan het gebruik van een orchestration-tool, zoals Azure Data Factory, de vernieuwing van het Azure Analysis Services-model activeren.

Zodra u een Logic-app hebt gemaakt:

1. Kies in de ontwerper van logic-app de eerste actie **als Wanneer een HTTP-aanvraag wordt ontvangen**.

   ![Http-ontvangen activiteit toevoegen](./media/analysis-services-async-refresh-logic-app/1.png)

Deze stap wordt gevuld met de HTTP-POST-URL zodra de Logische App is opgeslagen.

2. Voeg een nieuwe stap toe en zoek naar **HTTP**.  

   ![HTTP-activiteit toevoegen](./media/analysis-services-async-refresh-logic-app/9.png)

   ![HTTP-activiteit toevoegen](./media/analysis-services-async-refresh-logic-app/10.png)

3. Selecteer **HTTP** om deze actie toe te voegen.

   ![HTTP-activiteit toevoegen](./media/analysis-services-async-refresh-logic-app/2.png)

Configureer de HTTP-activiteit als volgt:

|Eigenschap  |Waarde  |
|---------|---------|
|**Methode**     |POST         |
|**Uri**     | https://*uw serverregio*/servers/*aas-servernaam*/modellen/*uw databasenaam*/ververst <br /> <br /> Bijvoorbeeld: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes|
|**Headers**     |   Inhoudstype, toepassing/json <br /> <br />  ![Headers](./media/analysis-services-async-refresh-logic-app/6.png)    |
|**Hoofdtekst**     |   Zie [Asynchrone vernieuwing met de REST API - POST /refreshes](analysis-services-async-refresh.md#post-refreshes)voor meer informatie over het vormen van de aanvraagbody. |
|**Verificatie**     |Active Directory OAuth         |
|**Tenant**     |Uw Azure Active Directory TenantId invullen         |
|**Doelgroep**     |https://*.asazure.windows.net         |
|**Client-id**     |Voer de clientid van de servicenaam clientID in         |
|**Referentietype**     |Geheim         |
|**Geheim**     |Voer uw Service Principal Name Secret in         |

Voorbeeld:

![Voltooide HTTP-activiteit](./media/analysis-services-async-refresh-logic-app/7.png)

Test nu de Logic App.  Klik in de ontwerper van de Logische app op **Uitvoeren**.

![De logische app testen](./media/analysis-services-async-refresh-logic-app/8.png)

## <a name="consume-the-logic-app-with-azure-data-factory"></a>De Logic-app gebruiken met Azure Data Factory

Zodra de Logic App is opgeslagen, controleert u de **activiteit Wanneer een HTTP-aanvraag is ontvangen** en kopieert u de **HTTP-POST-URL** die nu wordt gegenereerd.  Dit is de URL die door Azure Data Factory kan worden gebruikt om de asynchrone aanroep te voeren om de Logic App te activeren.

Hier volgt een voorbeeld van Azure Data Factory Web Activity dat deze actie doet.

![Webactiviteit gegevensfabriek](./media/analysis-services-async-refresh-logic-app/11.png)

## <a name="use-a-self-contained-logic-app"></a>Een zelfstandige Logic-app gebruiken

Als u niet van plan bent een orchestration-tool zoals Data Factory te gebruiken om de modelvernieuwing te activeren, u instellen dat de logische app de vernieuwing activeert op basis van een schema.

Verwijder in het bovenstaande voorbeeld de eerste activiteit en vervang deze door een **activiteit Planning.**

![Activiteit plannen](./media/analysis-services-async-refresh-logic-app/12.png)

![Activiteit plannen](./media/analysis-services-async-refresh-logic-app/13.png)

In dit voorbeeld wordt **Herhaling**gebruikt.

Zodra de activiteit is toegevoegd, configureert u de interval en frequentie en voegt u een nieuwe parameter toe en kiest **U Op deze uren**.

![Activiteit plannen](./media/analysis-services-async-refresh-logic-app/16.png)

Selecteer de gewenste uren.

![Activiteit plannen](./media/analysis-services-async-refresh-logic-app/15.png)

Sla de Logische App op.

## <a name="next-steps"></a>Volgende stappen

[Monsters](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
