---
title: Apache Kafka REST-proxy-Azure HDInsight
description: Meer informatie over het uitvoeren van Apache Kafka-bewerkingen met behulp van een Kafka REST-proxy op Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: a64d03ebe7c8bbb4cfa9c7bd63a678892250373d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75482866"
---
# <a name="interact-with-apache-kafka-clusters-in-azure-hdinsight-using-a-rest-proxy"></a>Interactie met Apache Kafka clusters in azure HDInsight met behulp van een REST-proxy

Met de Kafka REST-proxy kunt u met uw Kafka-cluster communiceren via een REST API over HTTP. Dit betekent dat uw Kafka-clients zich buiten het virtuele netwerk kunnen bevinden. Daarnaast kunnen clients eenvoudige HTTP-aanroepen voor het verzenden en ontvangen van berichten naar het Kafka-cluster maken, in plaats van te vertrouwen op Kafka-bibliotheken.  

## <a name="background"></a>Achtergrond

### <a name="architecture"></a>Architectuur

Zonder REST-proxy moeten Kafka-clients zich in hetzelfde VNet bevinden als het Kafka-cluster of een gekoppeld VNet. Met de REST-proxy kunt u overal verbinding maken met gegevens producenten of gebruikers die zich elders bevinden. Als u de REST-proxy implementeert, maakt u een nieuw openbaar eind punt voor uw cluster, dat u kunt vinden in de Portal instellingen.

Zie [Apache Kafka rest proxy API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy)voor een volledige specificatie van bewerkingen die worden ondersteund door de API.

### <a name="security"></a>Beveiliging

De toegang tot de Kafka REST-proxy wordt beheerd met Azure Active Directory-beveiligings groepen. Zie [toegang tot apps en bronnen beheren met Azure Active Directory groepen](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-manage-groups)voor meer informatie.

Bij het maken van het Kafka-cluster waarvoor de REST-proxy is ingeschakeld, geeft u de AAD-beveiligings groep op die toegang moet hebben tot het REST-eind punt. De Kafka-clients (toepassingen) die toegang nodig hebben tot de REST proxy moeten door de groeps eigenaar bij deze groep worden geregistreerd. De eigenaar van de groep kan dit doen via de portal of via Power shell.

Voordat er aanvragen worden gedaan voor het eind punt van de REST-proxy, moet de client toepassing een OAuth-Token ophalen om het lidmaatschap van de juiste beveiligings groep te verifiëren. Zie [toegang tot Azure Active Directory webtoepassingen toestaan met de OAuth 2,0 code subsidie flow](../../active-directory/develop/v1-protocols-oauth-code.md)voor meer informatie over de werking van OAuth-tokens. Voor een voor beeld van het ophalen van een OAuth-token in Python, raadpleegt u voor [beeld van client toepassing](#client-application-sample)

Zodra de client toepassing het OAuth-token heeft, moet dit token door gegeven worden in de HTTP-aanvraag voor de REST-proxy.

## <a name="prerequisites"></a>Vereisten

1. U registreert een toepassing met Azure AD. De client toepassingen die u schrijft om te communiceren met de Kafka REST-proxy, gebruiken de ID en het geheim van deze toepassing om te verifiëren bij Azure.
1. Maak een Azure AD-beveiligings groep en voeg de toepassing die u hebt geregistreerd bij Azure AD toe aan de beveiligings groep. Deze beveiligings groep wordt gebruikt om te bepalen welke toepassingen mogen communiceren met de REST-proxy. Zie [een basis groep maken en leden toevoegen met Azure Active Directory](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)voor meer informatie over het maken van Azure ad-groepen.

## <a name="create-a-kafka-cluster-with-rest-proxy-enabled"></a>Een Kafka-cluster maken waarop REST-proxy is ingeschakeld

1. Schakel tijdens de werk stroom voor het maken van het Kafka-cluster, op het tabblad Beveiliging en netwerken, de optie ' Kafka REST proxy inschakelen ' in.

     ![Kafka REST proxy inschakelen en beveiligings groep selecteren](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-enable.png)

1. Klik op **beveiligings groep selecteren**. Selecteer in de lijst met beveiligings groepen de beveiligings groep waartoe u toegang wilt hebben tot de REST-proxy. U kunt het zoekvak gebruiken om de juiste beveiligings groep te vinden. Klik onderaan op de knop **selecteren** .

     ![Kafka REST proxy inschakelen en beveiligings groep selecteren](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-select-security-group.png)

1. Voltooi de resterende stappen voor het maken van uw cluster zoals beschreven in [Apache Kafka cluster maken in azure HDInsight met behulp van Azure Portal](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started).

1. Zodra het cluster is gemaakt, gaat u naar de eigenschappen van het cluster om de Kafka REST proxy-URL op te nemen.

     ![REST proxy-URL weer geven](./media/apache-kafka-rest-proxy/apache-kafka-rest-proxy-view-proxy-url.png)

## <a name="client-application-sample"></a>Voor beeld van client toepassing

U kunt de python-code hieronder gebruiken om te communiceren met de REST-proxy op uw Kafka-cluster. Deze code doet het volgende:

1. Haalt een OAuth-token uit Azure AD op
1. Hiermee maakt u het onderwerp dat is opgegeven
1. Berichten verzenden naar dat onderwerp
1. Gebruikt berichten uit dat onderwerp

Zie [python AuthenticationContext-klasse](https://docs.microsoft.com/python/api/adal/adal.authentication_context.authenticationcontext?view=azure-python)voor meer informatie over het ophalen van OAuth-tokens in python. U ziet mogelijk een vertraging terwijl onderwerpen die niet worden gemaakt of verwijderd via de Kafka REST-proxy, daar worden weer gegeven. Deze vertraging is te wijten aan het vernieuwen van de cache.

```python
#Required python packages
#pip3 install adal
#pip install msrestazure

import adal
from msrestazure.azure_active_directory import AdalAuthentication
from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
import requests

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

#getting token
login_endpoint = AZURE_PUBLIC_CLOUD.endpoints.active_directory
resource = "https://hib.azurehdinsight.net"
context = adal.AuthenticationContext(login_endpoint + '/' + tenant_id)

token = context.acquire_token_with_client_credentials(
    resource,
    client_id,
    client_secret)

accessToken = 'Bearer ' + token['accessToken']

print(accessToken)

# relative url
getstatus = "/v1/metadata/topics"
request_url = kafkarest_endpoint + getstatus

# sending get request and saving the response as response object
response = requests.get(request_url, headers={'Authorization': accessToken})
print(response.content)
```

Als u het code voorbeeld wilt gebruiken, volgt u deze stappen:

1. Sla de voorbeeld code op een computer waarop python is geïnstalleerd.
1. Installeer de vereiste python-afhankelijkheden door `pip3 install adal` en `pip install msrestazure`uit te voeren.
1. Wijzig de code en werk de volgende eigenschappen voor uw omgeving bij:
    1.  *Tenant-id* : de Azure-Tenant waar uw abonnement zich bevindt.
    1.  *Client-id* : de id van de toepassing die u hebt geregistreerd in de beveiligings groep.
    1.  *Client geheim* : het geheim voor de toepassing dat u in de beveiligings groep hebt geregistreerd
    1.  *Kafkarest_endpoint* : Haal deze waarde op via het tabblad ' Eigenschappen ' in het cluster overzicht, zoals beschreven in de [sectie implementatie](#create-a-kafka-cluster-with-rest-proxy-enabled). Deze moet de volgende indeling hebben: `https://<clustername>-kafkarest.azurehdinsight.net`
3. Voer vanaf de opdracht regel het python-bestand uit door `python <filename.py>` uit te voeren

## <a name="next-steps"></a>Volgende stappen

* [Kafka REST proxy API-naslag documenten](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)
* [Zelf studie: de Apache Kafka producer-en Consumer-Api's gebruiken](apache-kafka-producer-consumer-api.md)