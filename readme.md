#  Lab 1
## 在 AKS 上部署 Java 應用程式（包含 Azure App Configuration）

### 目標： 讓學生熟悉如何使用 AKS 部署容器化的 Java 應用程式，並使用 Azure App Configuration 管理應用程式設定。
- 步驟：
  - 建立 AKS 叢集。
  - 建立 Azure App Configuration 資源。
  - 使用 Docker 將 Java 應用程式容器化，並使其能夠從 Azure App Configuration 讀取設定。
  - 建立 Kubernetes 部署和服務。
  - 將應用程式部署到 AKS 叢集。
  - 驗證應用程式是否正常運作，並測試設定變更是否能即時生效。
- 學習重點： AKS 的基本概念、Docker 容器化、Kubernetes 部署與服務、Azure App Configuration 的使用。

### 說明:

- 定義一個名為 java-app-service 的 Service，用於公開應用程式。
- selector 用於將 Service 路由到具有 app: java-app 標籤的 Pod。
- ports 定義了 Service 的端口映射：將外部的 80 端口流量轉發到 Pod 的 8080 端口。
- type: LoadBalancer 將在 Azure Kubernetes Service 中創建一個 Azure Load Balancer，以便從外部訪問應用程式。

### 部署步驟概覽:

- 建立 AKS 叢集: 您可以使用 Azure CLI 或 Azure Portal 創建一個 AKS 叢集。
- 建立 Azure App Configuration 資源: 使用 Azure CLI 或 Azure Portal 創建一個 App Configuration 資源。
- 在 App Configuration 中設定值: 在您的 App Configuration 資源中添加一個鍵值對，例如 my.greeting 和 Hello from App Configuration!.
  - 建立 Docker 鏡像:
    - 確保您已安裝 Docker。
    - 在包含 Dockerfile 和打包後的 JAR 檔案的目錄中執行 docker build -t <您的 Docker Registry>/java-app:latest .
    - 使用 docker login <您的 Docker Registry> 登錄您的 Docker 鏡像倉庫。
    - 推送鏡像到您的 Docker 鏡像倉庫：docker push <您的 Docker Registry>/java-app:latest
    - 建立 Kubernetes Secret:
      - 取得您的 Azure App Configuration 資源的連接字串。您可以在 Azure Portal 的 App Configuration 資源的 "存取金鑰" 部分找到它。
      - 在您的 AKS 叢集中創建一個 Kubernetes Secret 來存儲連接字串：
      ```Bash
      kubectl create secret generic app-config-secret --from-literal=connection-string="<您的連接字串>"
      ```
      
      將 <您的連接字串> 替換為您的實際連接字串。

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
