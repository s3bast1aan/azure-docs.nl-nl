---
title: Service-principal voor Azure Kubernetes-cluster
description: Een service-principal voor Azure Active Directory voor een Kubernetes-cluster in AKS
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: get-started-article
ms.date: 04/19/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 4ad0fc3fdb7d5b7c14f13fd6c279915974558dc9
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578793"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Service-principals met AKS (Azure Kubernetes Service)

Voor een AKS-cluster is een [service-principal voor Azure Active Directory][aad-service-principal] vereist voor gebruik met Azure-API's. De service-principal is nodig voor het dynamisch maken en beheren van resources zoals [Azure Load Balancer][azure-load-balancer-overview].

In dit artikel worden verschillende opties getoond om een service-principal in te stellen voor een Kubernetes-cluster.

## <a name="before-you-begin"></a>Voordat u begint


Als u een service-principal voor Azure AD wilt maken, moet u beschikken over machtigingen voor het registreren van een toepassing bij de Azure AD-tenant. U moet ook machtigingen hebben om de toepassing aan een rol toe te wijzen in uw abonnement. Als u niet beschikt over de benodigde machtigingen, moet u mogelijk de Azure AD- of abonnementsbeheerder vragen om de benodigde machtigingen toe te wijzen, of vooraf een service-principal maken voor het Kubernetes-cluster.

Ook moet de Azure CLI-versie 2.0.27 of later zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="create-sp-with-aks-cluster"></a>Service-principal met AKS-cluster maken

Wanneer u een AKS-cluster maakt met behulp van de opdracht `az aks create`, kunt u ervoor kiezen om automatisch een service-principal te genereren.

In het volgende voorbeeld wordt een AKS-cluster gemaakt en vervolgens ook een service-principal, omdat er geen bestaande service-principal is opgegeven. Uw account moet beschikken over de juiste rechten voor het maken van een service-principal om deze bewerking te kunnen voltooien.

```azurecli-interactive
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="use-an-existing-sp"></a>Een bestaande service-principal gebruiken

Er kan een bestaande service-principal voor Azure AD worden gebruikt of vooraf gemaakt voor gebruik met een AKS-cluster. Dit is handig bij het implementeren van een cluster in Azure Portal, waar u de gegevens voor de service-principal moet opgeven. Wanneer u een bestaande service-principal gebruikt, moet het clientgeheim worden geconfigureerd als een wachtwoord.

## <a name="pre-create-a-new-sp"></a>Vooraf een nieuwe service-principal maken

Gebruik de opdracht [az ad sp create-for-rbac][az-ad-sp-create] om een service-principal te maken met de Azure CLI.

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op het volgende. Noteer `appId` en `password`. Deze waarden worden gebruikt bij het maken van een AKS-cluster.

```json
{
  "appId": "7248f250-0000-0000-0000-dbdeb8400d85",
  "displayName": "azure-cli-2017-10-15-02-20-15",
  "name": "http://azure-cli-2017-10-15-02-20-15",
  "password": "77851d2c-0000-0000-0000-cb3ebc97975a",
  "tenant": "72f988bf-0000-0000-0000-2d7cd011db47"
}
```

## <a name="use-an-existing-sp"></a>Een bestaande service-principal gebruiken

Wanneer u een vooraf gemaakte service-principal gebruikt, geeft u `appId` en `password` op als argumentwaarden voor de opdracht `az aks create`.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --service-principal <appId> --client-secret <password>
```

Als u een AKS-cluster implementeert met behulp van Azure Portal, typt u de waarde `appId` in het veld **Client-id service-principal** en de waarde `password` in het veld **Clientgeheim service-principal** in het configuratieformulier voor het AKS-cluster.

![Afbeelding van browsen naar Azure Vote](media/container-service-kubernetes-service-principal/sp-portal.png)

## <a name="additional-considerations"></a>Aanvullende overwegingen

Houd rekening met het volgende als u werkt met AKS en service-principals voor Azure AD.

* De service-principal voor Kubernetes is een onderdeel van de configuratie van het cluster. Gebruik echter niet de id voor het implementeren van het cluster.
* Elke service-principal is gekoppeld aan een Azure AD-toepassing. De service-principal voor een Kubernetes-cluster kan zijn gekoppeld aan elke geldige Azure AD-toepassingsnaam (bijvoorbeeld `https://www.contoso.org/example`). De URL van de toepassing hoeft geen echt eindpunt te zijn.
* Zorg ervoor dat u bij het opgeven van de **client-id** van de service-principal gebruikmaakt van de waarde van de `appId`.
* Op de hoofd- en knooppunt-VM's in het Kubernetes-cluster worden de referenties voor de service-principal opgeslagen in het bestand `/etc/kubernetes/azure.json`.
* Als u de opdracht `az aks create` gebruikt om de service-principal automatisch te genereren, worden de referenties voor de service-principal naar het bestand `~/.azure/aksServicePrincipal.json` geschreven op de computer die wordt gebruikt om de opdracht uit te voeren.
* Wanneer u een AKS-cluster verwijdert dat is gemaakt door `az aks create`, wordt de service-principal die automatisch is gemaakt, niet verwijderd. Als u de service-principal wilt verwijderen, haalt u eerst het id voor de service-principal op met [az ad app list][az-ad-app-list]. In het volgende voorbeeld wordt eerst een query uitgevoerd voor het cluster met de naam *myAKSCluster* en wordt vervolgens het app-id verwijderd met [az ad app delete][az-ad-app-delete]. Vervang deze namen door uw eigen waarden:

    ```azurecli-interactive
    az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
    az ad app delete --id <appId>
    ```

## <a name="next-steps"></a>Volgende stappen

Zie de documentatie over Azure AD-toepassingen voor meer informatie over service-principals voor Azure Active Directory.

> [!div class="nextstepaction"]
> [Toepassings- en service-principalobjecten][service-principal]

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
