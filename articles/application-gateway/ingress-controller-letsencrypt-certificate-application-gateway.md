---
title: LetsEncrypt.org certificaten gebruiken met Application Gateway
description: In dit artikel vindt u informatie over het verkrijgen van een certificaat van LetsEncrypt.org en gebruiken op uw Application Gateway voor AKS-clusters.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 11/4/2019
ms.author: caya
ms.openlocfilehash: 92e9747865f1a0910c8bae4001cc597ae9ea3da6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73957972"
---
# <a name="use-certificates-with-letsencryptorg-on-application-gateway-for-aks-clusters"></a>Certificaten gebruiken met LetsEncrypt.org op Application Gateway voor AKS-clusters

In deze sectie configureert u uw AKS om gebruik te maken [van LetsEncrypt.org](https://letsencrypt.org/) en automatisch een TLS/SSL-certificaat voor uw domein te verkrijgen. Het certificaat wordt geïnstalleerd op Application Gateway, waarmee SSL/TLS-beëindiging voor uw AKS-cluster wordt uitgevoerd. De hier beschreven setup maakt gebruik van de [cert-manager](https://github.com/jetstack/cert-manager) Kubernetes add-on, die het maken en beheren van certificaten automatiseert.

Volg de onderstaande stappen om [cert-manager](https://docs.cert-manager.io) te installeren op uw bestaande AKS-cluster.

1. Helmdiagram

    Voer het volgende script `cert-manager` uit om de helmdiagram te installeren. Dit zal:

    - een nieuwe `cert-manager` naamruimte op uw AKS maken
    - de volgende CRD's maken: certificaat, uitdaging, clusteruitgever, emittent, order
    - cert-manager-diagram installeren (vanaf [docs.cert-manager.io)](https://docs.cert-manager.io/en/latest/getting-started/install/kubernetes.html#steps)

    ```bash
    #!/bin/bash

    # Install the CustomResourceDefinition resources separately
    kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

    # Create the namespace for cert-manager
    kubectl create namespace cert-manager

    # Label the cert-manager namespace to disable resource validation
    kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

    # Add the Jetstack Helm repository
    helm repo add jetstack https://charts.jetstack.io

    # Update your local Helm chart repository cache
    helm repo update

    # Install the cert-manager Helm chart
    helm install \
      --name cert-manager \
      --namespace cert-manager \
      --version v0.8.0 \
      jetstack/cert-manager
    ```

2. ClusterIssuer-bron

    Maak `ClusterIssuer` een resource. Het is `cert-manager` vereist door `Lets Encrypt` de certificaatautoriteit te vertegenwoordigen waar de ondertekende certificaten zullen worden verkregen.

    Met behulp van de `ClusterIssuer` niet-namespaced resource geeft cert-manager certificaten uit die kunnen worden verbruikt uit meerdere naamruimten. `Let’s Encrypt`gebruikt het ACME-protocol om te controleren of u een bepaalde domeinnaam beheert en om u een certificaat te geven. Meer details over `ClusterIssuer` het configureren van eigenschappen [hier](https://docs.cert-manager.io/en/latest/tasks/issuers/index.html). `ClusterIssuer`zal `cert-manager` instrueren om certificaten `Lets Encrypt` uit te geven met behulp van de faseringsomgeving die wordt gebruikt voor het testen (het rootcertificaat is niet aanwezig in browser-/clientvertrouwenswinkels).

    Het standaard uitdagingstype in de `http01`Onderstaande YAML is . Andere uitdagingen worden gedocumenteerd op [letsencrypt.org - Challenge Types](https://letsencrypt.org/docs/challenge-types/)

    > [!IMPORTANT] 
    > Update `<YOUR.EMAIL@ADDRESS>` in de YAML hieronder

    ```bash
    #!/bin/bash
    kubectl apply -f - <<EOF
    apiVersion: certmanager.k8s.io/v1alpha1
    kind: ClusterIssuer
    metadata:
    name: letsencrypt-staging
    spec:
    acme:
        # You must replace this email address with your own.
        # Let's Encrypt will use this to contact you about expiring
        # certificates, and issues related to your account.
        email: <YOUR.EMAIL@ADDRESS>
        # ACME server URL for Let’s Encrypt’s staging environment.
        # The staging environment will not issue trusted certificates but is
        # used to ensure that the verification process is working properly
        # before moving to production
        server: https://acme-staging-v02.api.letsencrypt.org/directory
        privateKeySecretRef:
        # Secret resource used to store the account's private key.
        name: example-issuer-account-key
        # Enable the HTTP-01 challenge provider
        # you prove ownership of a domain by ensuring that a particular
        # file is present at the domain
        http01: {}
    EOF
    ```

3. App implementeren

    Maak een Ingress-bron `guestbook` om de toepassing bloot te leggen met behulp van de Toepassingsgateway met het certificaat Met versleutelen door te maken.

    Zorg ervoor dat u Application Gateway een openbare Frontend IP-configuratie heeft met een DNS-naam (gebruik het standaarddomein `azure.com` of een `Azure DNS Zone` service inrichten en uw eigen aangepaste domein toewijzen).
    Let op de `certmanager.k8s.io/cluster-issuer: letsencrypt-staging`annotatie , die cert-manager vertelt om de gelabelde Ingress-bron te verwerken.

    > [!IMPORTANT] 
    > Update `<PLACEHOLDERS.COM>` in de YAML hieronder met uw eigen domein (of de Application Gateway een, bijvoorbeeld 'kh-aks-ingress.westeurope.cloudapp.azure.com')

    ```bash
    kubectl apply -f - <<EOF
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
    name: guestbook-letsencrypt-staging
    annotations:
        kubernetes.io/ingress.class: azure/application-gateway
        certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    spec:
    tls:
    - hosts:
        - <PLACEHOLDERS.COM>
        secretName: guestbook-secret-name
    rules:
    - host: <PLACEHOLDERS.COM>
        http:
        paths:
        - backend:
            serviceName: frontend
            servicePort: 80
    EOF
    ```

    Na een paar seconden hebt `guestbook` u toegang tot de service via de HTTPS-url van de Application Gateway met behulp van het automatisch uitgegeven **staging-certificaat.** `Lets Encrypt`
    Uw browser kan u waarschuwen voor een ongeldige cert-autoriteit. Het certificaat voor `CN=Fake LE Intermediate X1`de fasering wordt afgegeven door . Dit is een indicatie dat het systeem werkte zoals verwacht en u klaar bent voor uw productiecertificaat.

4. Productiecertificaat

    Zodra uw faseringscertificaat is ingesteld, u overschakelen naar een productie-ACME-server:
    1. Vervang de tijdelijke annotatie op uw Ingress-bron door:`certmanager.k8s.io/cluster-issuer: letsencrypt-prod`
    1. Verwijder de `ClusterIssuer` bestaande fasering die u in de vorige stap hebt gemaakt en maak een nieuwe door de ACME-server te vervangen door de hierboven genoemde ClusterIssuer YAML`https://acme-v02.api.letsencrypt.org/directory`

5. Verlopen en verlengen van certificaten

    Voordat `Lets Encrypt` het certificaat `cert-manager` verloopt, wordt het certificaat automatisch bijgewerkt in de geheime opslag van Kubernetes. Op dat moment past Application Gateway Ingress Controller het bijgewerkte geheim toe waarnaar wordt verwezen in de binnenkomende bronnen die worden gebruikt om de Application Gateway te configureren.
