#  Lab 1
## 在 AKS 上部署 Java 應用程式（包含 Azure App Configuration）

- 目標： 讓學生熟悉如何使用 AKS 部署容器化的 Java 應用程式，並使用 Azure App Configuration 管理應用程式設定。
- 步驟：
  - 建立 AKS 叢集。
  - 建立 Azure App Configuration 資源。
  - 使用 Docker 將 Java 應用程式容器化，並使其能夠從 Azure App Configuration 讀取設定。
  - 建立 Kubernetes 部署和服務。
  - 將應用程式部署到 AKS 叢集。
  - 驗證應用程式是否正常運作，並測試設定變更是否能即時生效。
- 學習重點： AKS 的基本概念、Docker 容器化、Kubernetes 部署與服務、Azure App Configuration 的使用。

## 在您的 AKS 叢集中創建一個 Kubernetes Secret 來存儲連接字串：

```Bash
  kubectl create secret generic app-config-secret --from-literal=connection-string="<您的連接字串>"
```
## 部署到 AKS:
- 使用 ```bash kubectl apply -f deployment.yaml ```部署應用程式。
- 使用 ```bash kubectl apply -f service.yaml ```創建服務。

## 驗證應用程式:
- 使用 ```bash kubectl get service java-app-service --watch``` 監控 Load Balancer 的外部 IP 地址。
- 一旦獲得外部 IP 地址，請在瀏覽器中或使用 curl 訪問 <外部 IP 地址>/hello。您應該看到 "Hello from App Configuration!"。


## 測試設定變更:
在 Azure Portal 的 App Configuration 資源中，修改 my.greeting 的值，例如改為 "Greeting updated!".
在幾秒鐘內（通常不需要重新部署），刷新瀏覽器或再次執行 curl 命令。您應該看到新的問候語 "Greeting updated!"，這表明配置已即時更新。
