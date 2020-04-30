---
title: Apache Kafka REST-proxy-Azure HDInsight
description: Leer hoe u Apache Kafka bewerkingen uitvoert met behulp van een Kafka REST-proxy op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 265e15713f8159e370ef22a197ffe931200a88f7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758992"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interactie met Apache Kafka clusters in azure HDInsight met behulp van een REST-proxy

Met Kafka REST proxy kunt u met uw Kafka-cluster communiceren via een REST API over HTTP. Deze actie houdt in dat uw Kafka-clients zich buiten uw virtuele netwerk kunnen bevinden. Clients kunnen eenvoudige HTTP-aanroepen naar het Kafka-cluster maken, in plaats van te vertrouwen op Kafka-bibliotheken. In dit artikel wordt uitgelegd hoe u een Kafka-cluster maakt waarop een REST-proxy is ingeschakeld. Bevat ook een voorbeeld code die laat zien hoe u aanroepen naar REST proxy.

## <a name="rest-api-reference"></a>Naslaginformatie over REST-API

Voor bewerkingen die worden ondersteund door de Kafka-REST API, Zie [HDInsight Kafka rest proxy API Reference](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy).

## <a name="background"></a>Achtergrond

![Kafka REST-proxy ontwerp](./media/rest-proxy/rest-proxy-architecture.png)

Zie [Apache Kafka rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)voor een volledige specificatie van bewerkingen die worden ondersteund door de API.

### <a name="rest-proxy-endpoint"></a>REST-proxy-eind punt

Het maken van een HDInsight Kafka-cluster met REST proxy maakt een nieuw openbaar eind punt voor uw cluster, dat u in de **Eigenschappen** van uw HDInsight-cluster kunt vinden op de Azure Portal.

### <a name="security"></a>Beveiliging

De toegang tot de Kafka REST-proxy wordt beheerd met Azure Active Directory-beveiligings groepen. Wanneer u het Kafka-cluster maakt, geeft u de Azure AD-beveiligings groep op met REST endpoint Access. Kafka-clients die toegang nodig hebben tot de REST proxy moeten door de groeps eigenaar bij deze groep worden geregistreerd. De groeps eigenaar kan zich registreren via de portal of via Power shell.

Voor REST proxy-eindpunt aanvragen moet client toepassingen een OAuth-Token ophalen. Het token wordt gebruikt om het lidmaatschap van een beveiligings groep te controleren. Hieronder vindt u een voor beeld van een [client toepassing](#client-application-sample) waarin wordt uitgelegd hoe u een OAuth-token ophaalt. De client toepassing geeft het OAuth-token in de HTTP-aanvraag door aan de REST-proxy.

> [!NOTE]  
> Zie [toegang tot apps en bronnen beheren met Azure Active Directory groepen](../../active-directory/fundamentals/active-directory-manage-groups.md)voor meer informatie over Aad-beveiligings groepen. Zie [toegang tot Azure Active Directory webtoepassingen toestaan met de OAuth 2,0 code subsidie flow](../../active-directory/develop/v1-protocols-oauth-code.md)voor meer informatie over de werking van OAuth-tokens.

## <a name="prerequisites"></a>Vereisten

1. U registreert een toepassing met Azure AD. De client toepassingen die u schrijft om te communiceren met de Kafka REST-proxy, gebruiken de ID en het geheim van deze toepassing om te verifiëren bij Azure.

1. Maak een Azure AD-beveiligings groep. Voeg de toepassing die u hebt geregistreerd bij Azure AD aan de beveiligings groep toe als **lid** van de groep. Deze beveiligings groep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [een basis groep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van Azure ad-groepen.

    Controleer of de groep van het type **Security**is.
    ![Beveiligings groep](./media/rest-proxy/rest-proxy-group.png)

    Controleer of de toepassing lid is van de groep.
    ![Lidmaatschap controleren](./media/rest-proxy/rest-proxy-membergroup.png)

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Een Kafka-cluster maken waarop REST-proxy is ingeschakeld

1. Schakel tijdens de werk stroom voor het maken van het Kafka-cluster op het tabblad **beveiliging en netwerk** de optie **Kafka rest proxy inschakelen** in.

     ![Kafka REST proxy inschakelen en beveiligings groep selecteren](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest.png)

1. Klik op **beveiligings groep selecteren**. Selecteer in de lijst met beveiligings groepen de beveiligings groep waartoe u toegang wilt hebben tot de REST-proxy. U kunt het zoekvak gebruiken om de juiste beveiligings groep te vinden. Klik onderaan op de knop **selecteren** .

     ![Kafka REST proxy inschakelen en beveiligings groep selecteren](./media/rest-proxy/azure-portal-cluster-security-networking-kafka-rest2.png)

1. Voltooi de resterende stappen voor het maken van uw cluster zoals beschreven in [Apache Kafka cluster maken in azure HDInsight met behulp van Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Zodra het cluster is gemaakt, gaat u naar de eigenschappen van het cluster om de Kafka REST proxy-URL op te nemen.

     ![REST proxy-URL weer geven](./media/rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Voor beeld van client toepassing

U kunt de python-code hieronder gebruiken om te communiceren met de REST-proxy op uw Kafka-cluster. Als u het code voorbeeld wilt gebruiken, volgt u deze stappen:

1. Sla de voorbeeld code op een computer waarop python is geïnstalleerd.
1. Installeer de vereiste python-afhankelijkheden `pip3 install msal`door uit te voeren.
1. Wijzig het gedeelte code om **deze eigenschappen te configureren** en de volgende eigenschappen voor uw omgeving bij te werken:

    |Eigenschap |Beschrijving |
    |---|---|
    |Tenant-id|De Azure-Tenant waar uw abonnement zich bevindt.|
    |Client-id|De ID voor de toepassing die u hebt geregistreerd in de beveiligings groep.|
    |Clientgeheim|Het geheim voor de toepassing die u hebt geregistreerd in de beveiligings groep.|
    |Kafkarest_endpoint|Haal deze waarde op via het tabblad **Eigenschappen** in het cluster overzicht, zoals beschreven in de [sectie implementatie](#create-a-kafka-cluster-with-rest-proxy-enabled). Deze moet de volgende indeling hebben:`https://<clustername>-kafkarest.azurehdinsight.net`|

1. Voer vanaf de opdracht regel het python-bestand uit door het uit te voeren`sudo python3 <filename.py>`

Deze code voert de volgende actie uit:

1. Haalt een OAuth-token op uit Azure AD.
1. Laat zien hoe u een aanvraag voor Kafka REST-proxy kunt indienen.

Zie [python AuthenticationContext-klasse](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)voor meer informatie over het ophalen van OAuth-tokens in python. U ziet mogelijk een vertraging terwijl `topics` deze niet worden gemaakt of verwijderd via de Kafka rest-proxy. Deze vertraging wordt veroorzaakt door het vernieuwen van de cache.

```python
#Required python packages
#pip3 install msal

import msal

#--------------------------Configure these properties-------------------------------#
# Tenant ID for your Azure Subscription
tenant_id = 'ABCDEFGH-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Application Id
client_id = 'XYZABCDE-1234-1234-1234-ABCDEFGHIJKL'
# Your Client Credentials
client_secret = 'password'
# kafka rest proxy -endpoint
kafkarest_endpoint = "https://<clustername>-kafkarest.azurehdinsight.net"
#--------------------------Configure these properties-------------------------------#

# Scope
scope = 'https://hib.azurehdinsight.net/.default'
#Authority
authority = 'https://login.microsoftonline.com/' + tenant_id

# Create a preferably long-lived app instance which maintains a token cache.
app = msal.ConfidentialClientApplication(
    client_id , client_secret, authority,
    #cache - For details on how look at this example: https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py
    )

# The pattern to acquire a token looks like this.
result = None

result = app.acquire_token_for_client(scopes=[scope])

print(result)
accessToken = result['access_token']

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Hieronder vindt u een voor beeld van het ophalen van een token van Azure voor REST-proxy met behulp van een krul opdracht. **U hebt de opgegeven tijdens `scope=https://hib.azurehdinsight.net/.default` het ophalen van een token nodig.**

```cmd
curl -X POST -H "Content-Type: application/x-www-form-urlencoded" -d 'client_id=<clientid>&client_secret=<clientsecret>&grant_type=client_credentials&scope=https://hib.azurehdinsight.net/.default' 'https://login.microsoftonline.com/<tenantid>/oauth2/v2.0/token'
```

## <a name="next-steps"></a>Volgende stappen

* [Kafka REST proxy API-naslag documenten](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
