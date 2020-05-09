---
title: Azure-app configuratie integreren met Kubernetes-implementatie met behulp van helm
description: Meer informatie over het gebruik van dynamische configuraties in Kubernetes-implementatie met helm.
services: azure-app-configuration
author: shenmuxiaosen
manager: zhenlan
ms.service: azure-app-configuration
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: shuawan
ms.openlocfilehash: 2aebccdf18aaba345beb344a8b6fc3b37754a4a1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/05/2020
ms.locfileid: "82793615"
---
# <a name="integrate-with-kubernetes-deployment-using-helm"></a>Integreren met Kubernetes-implementatie met behulp van helm

Helm biedt een manier om toepassingen te definiëren, te installeren en te upgraden die in Kubernetes worden uitgevoerd. Een helm-grafiek bevat de informatie die nodig is om een exemplaar van een Kubernetes-toepassing te maken. De configuratie wordt buiten het diagram zelf opgeslagen, in een bestand met de naam *Values. yaml*. 

Tijdens het release proces voegt helm de grafiek samen met de juiste configuratie om de toepassing uit te voeren. Variabelen die zijn gedefinieerd in *Values. yaml* kunnen bijvoorbeeld worden verwezen als omgevings variabelen in de actieve containers. Helm biedt ook ondersteuning voor het maken van Kubernetes-geheimen die kunnen worden gekoppeld als gegevens volumes of als omgevings variabelen worden weer gegeven.

U kunt de waarden die zijn opgeslagen in *Values. yaml* overschrijven door extra op YAML gebaseerde configuratie bestanden op de opdracht regel op te geven bij het uitvoeren van helm. Azure-app-configuratie ondersteunt het exporteren van configuratie waarden naar YAML-bestanden. Door deze export functionaliteit in uw implementatie te integreren, kunnen uw Kubernetes-toepassingen configuratie waarden gebruiken die zijn opgeslagen in de app-configuratie.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Gebruik waarden uit app-configuratie bij het implementeren van een toepassing naar Kubernetes met behulp van helm.
> * Een Kubernetes-geheim maken op basis van een Key Vault verwijzing in de app-configuratie.

In deze zelf studie wordt ervan uitgegaan dat u basis informatie krijgt over het beheren van Kubernetes met helm. Meer informatie over het installeren van toepassingen met helm in de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/kubernetes-helm).

## <a name="prerequisites"></a>Vereisten

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
- [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) installeren (versie 2.4.0 of hoger)
- [Helm](https://helm.sh/docs/intro/install/) installeren (versie 2.14.0 of hoger)
- Een Kubernetes-cluster.

## <a name="create-an-app-configuration-store"></a>Een app-configuratie archief maken

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Selecteer **Configuration Explorer** > **maken** om de volgende sleutel-waardeparen toe te voegen:

    | Sleutel | Waarde |
    |---|---|
    | Settings. Color | Wit |
    | instellingen. bericht | Gegevens van Azure App Configuration |

    Laat het **Label** en het **inhouds type** nu leeg.

## <a name="add-a-key-vault-reference-to-app-configuration"></a>Een Key Vault verwijzing toevoegen aan de app-configuratie
1. Meld u aan bij de [Azure Portal](https://portal.azure.com) en voeg een geheim toe aan [Key Vault](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#add-a-secret-to-key-vault) met de naam **wacht woord** en de waarde **myPassword**. 
2. Selecteer het exemplaar van het app-configuratie archief dat u in de vorige sectie hebt gemaakt.

3. Selecteer **Configuration Explorer**.

4. Selecteer **+** > **verwijzing naar sleutel kluis**maken en geef vervolgens de volgende waarden op:
    - **Sleutel**: Selecteer **geheimen. wacht woord**.
    - **Label**: laat deze waarde leeg.
    - **Abonnement**, **resource groep**en **sleutel kluis**: Voer de waarden in die overeenkomen met die in de sleutel kluis die u in de vorige stap hebt gemaakt.
    - **Geheim**: Selecteer het geheime **wacht woord** dat u in de vorige sectie hebt gemaakt.

## <a name="create-helm-chart"></a>Helm-grafiek maken ##
Maak eerst een voor beeld van een helm-grafiek met de volgende opdracht
```console
helm create mychart
```

Helm maakt een nieuwe map met de naam myChart met de structuur die hieronder wordt weer gegeven. 

> [!TIP]
> Volg deze [grafieken gids](https://helm.sh/docs/chart_template_guide/getting_started/) voor meer informatie.

```
mychart
|-- Chart.yaml
|-- charts
|-- templates
|   |-- NOTES.txt
|   |-- _helpers.tpl
|   |-- deployment.yaml
|   |-- ingress.yaml
|   `-- service.yaml
`-- values.yaml
```

Werk vervolgens de sectie **spec: Temp late: spec: containers** van het bestand *Deployment. yaml* . Met het volgende code fragment worden twee omgevings variabelen toegevoegd aan de container. U kunt hun waarden dynamisch instellen tijdens de implementatie.

```yaml
env:
- name: Color
    value: {{ .Values.settings.color }}
- name: Message
    value: {{ .Values.settings.message }}
``` 

Het volledige *implementatie. yaml* -bestand na de update moet er als volgt uitzien.

```yaml
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ include "mychart.fullname" . }}
  labels:
    app.kubernetes.io/name: {{ include "mychart.name" . }}
    helm.sh/chart: {{ include "mychart.chart" . }}
    app.kubernetes.io/instance: {{ .Release.Name }}
    app.kubernetes.io/managed-by: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app.kubernetes.io/name: {{ include "mychart.name" . }}
      app.kubernetes.io/instance: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mychart.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          env:
            - name: Color
              value: {{ .Values.settings.color }}
            - name: Message
              value: {{ .Values.settings.message }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
```

Als u gevoelige gegevens wilt opslaan als Kubernetes geheimen, voegt u een *geheimen. yaml* -bestand toe onder de map Sjablonen.

> [!TIP]
> Meer informatie over het gebruik van [Kubernetes-geheimen](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets).

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysecret
type: Opaque
data:
  password: {{ .Values.secrets.password }}
```

Werk tot slot het bestand *Values. yaml* bij met de volgende inhoud om optioneel de standaard waarden te bieden van de configuratie-instellingen en-geheimen waarnaar wordt verwezen in de *implementatie. yaml* -en *geheimen. yaml* -bestanden. De werkelijke waarden worden overschreven door de configuratie die wordt opgehaald uit de app-configuratie.

```yaml
# settings will be overwritten by App Configuration
settings:
    color: red
    message: myMessage
```

## <a name="pass-configuration-from-app-configuration-in-helm-install"></a>Configuratie door geven van app-configuratie in helm-installatie ##
Down load eerst de configuratie van de app-configuratie naar een *myConfig. yaml* -bestand. Gebruik een sleutel filter om de sleutels die beginnen met instellingen te downloaden **.**.. Als het sleutel filter in uw geval niet voldoende is om sleutels van Key Vault verwijzingen uit te sluiten, kunt u het argument **----overs Laan** gebruiken om ze uit te sluiten. 

> [!TIP]
> Meer informatie over de [export opdracht](https://docs.microsoft.com/cli/azure/appconfig/kv?view=azure-cli-latest#az-appconfig-kv-export). 

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path myConfig.yaml --key "settings.*"  --separator "." --format yaml
```

Down load geheimen vervolgens naar een bestand met de naam *mySecrets. yaml*. Het opdracht regel argument **--Resolve-met sleutel kluis** worden de Key Vault verwijzingen omgezet door de werkelijke waarden op te halen in Key Vault. U moet deze opdracht uitvoeren met referenties die toegangs machtigingen hebben voor de bijbehorende Key Vault.

> [!WARNING]
> Wanneer dit bestand gevoelige informatie bevat, moet u het bestand zorgvuldig en opschonen wanneer het niet meer nodig is.

```azurecli-interactive
az appconfig kv export -n myAppConfiguration -d file --path mySecrets.yaml --key "secrets.*" --separator "." --resolve-keyvault --format yaml
```

Gebruik helm upgrade **-f** argument voor het door geven van de twee configuratie bestanden die u hebt gemaakt. Ze overschrijven de configuratie waarden die zijn gedefinieerd in *Values. yaml* met de waarden die zijn geëxporteerd uit de app-configuratie.

```console
helm upgrade --install -f myConfig.yaml -f mySecrets.yaml "example" ./mychart 
```

U kunt ook het argument **--set** gebruiken voor helm-upgrade om letterlijke sleutel waarden door te geven. Het gebruik van het argument **--set** is een goede manier om te voor komen dat gevoelige gegevens worden bewaard op schijf. 

```powershell
$secrets = az appconfig kv list -n myAppConfiguration --key "secrets.*" --resolve-keyvault --query "[*].{name:key, value:value}" | ConvertFrom-Json

foreach ($secret in $secrets) {
  $keyvalues += $secret.name + "=" + $secret.value + ","
}

if ($keyvalues){
  $keyvalues = $keyvalues.TrimEnd(',')
  helm upgrade --install --set $keyvalues "example" ./mychart 
}
else{
  helm upgrade --install "example" ./mychart 
}

```

Controleer of de configuraties en geheimen zijn ingesteld door toegang te krijgen tot het [Kubernetes-dash board](https://docs.microsoft.com/azure/aks/kubernetes-dashboard). U ziet dat de **kleur** en **bericht** waarden van de app-configuratie zijn ingevuld in de omgevings variabelen van de container.

![Quickstart voor het lokaal starten van een app](./media/kubernetes-dashboard-env-variables.png)

Er is ook een geheim, **wacht woord**, opgeslagen als Key Vault referentie in de app-configuratie toegevoegd aan Kubernetes geheimen. 

![Quickstart voor het lokaal starten van een app](./media/kubernetes-dashboard-secrets.png)

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u Azure-app configuratie gegevens geëxporteerd die moeten worden gebruikt in een Kubernetes-implementatie met helm. Ga verder met de voor beelden van Azure CLI voor meer informatie over het gebruik van app-configuratie.

> [!div class="nextstepaction"]
> [Azure-CLI](https://docs.microsoft.com/cli/azure/appconfig?view=azure-cli-latest)
