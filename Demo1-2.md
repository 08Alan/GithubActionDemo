# Demo1-2:利用Github Action 部署至App Service for Container

利用Visual Studio將此天氣API接口利用Publish進行容器映像打包
並且將其推送至Azure Container Registry之中

右鍵點擊項目進行Publish

![](Demo1-2/2020-10-22-17-40-31.png)

發布目標選擇Container Registry選取既有的ACR進行部署

![](Demo1-2/2020-10-22-17-42-13.png)

![](Demo1-2/2020-10-22-17-43-19.png)

完成設置後開始進行發布

![](Demo1-2/2020-10-22-17-43-58.png)

下方建置日志進行 `docker build && docker push`

![](Demo1-2/2020-10-22-17-44-59.png)

回到Azure Portal 之中 確認Azure Container Registry中的存儲庫已經Push完畢

![](Demo1-2/2020-10-22-17-46-53.png)

接著開始設置Github Action pipeline流水線

進入到Actions頁籤, 新增Workflow, 選擇Dotnet Core模板

![](Demo1-2/2020-10-22-17-48-59.png)

與Demo1-1相同 在最下方加入Azure App Service for Container Deploy steps
不過此時加入需要拉取的容器映像位置

```
      - uses: azure/webapps-deploy@v2
        with:
          app-name: '${{ env.AZURE_WEBAPP_NAME }}'
          publish-profile: ${{ secrets.azureWebAppContainerPublishProfile }}
          images: 'gadacr.azurecr.io/alanapi:latest'
```

! 注意這裡的App Service for Contaier的發布配置文件有Bug 需將下載回來的Profile手動編輯將PublishURL修改

`publishUrl="yoursitename.scm.azurewebsites.net:443"`

![](Demo1-2/2020-10-22-17-53-30.png)

修改完後將此Profile加入Secrets

![](Demo1-2/2020-10-22-17-54-45.png)

此時已配置完畢Actions流水線, 完整的yml如下：

```
name: AppServiceContainer

on:
  push:
    branches:
      - master
  pull_request:
    branches:
      - '*'
# CONFIGURATION
# For help, go to https://github.com/Azure/Actions
#
# 1.1 Set up the following secrets in your repository:
#   AZURE_WEBAPP_PUBLISH_PROFILE
#
# 2. Change these variables for your configuration:
env:
  AZURE_WEBAPP_NAME: 'GAD-ContainerDemo'    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.100'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:

      # Checkout the repoff
      - uses: actions/checkout@master
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 

      - uses: azure/webapps-deploy@v2
        with:
          app-name: '${{ env.AZURE_WEBAPP_NAME }}'
          publish-profile: ${{ secrets.azureWebAppContainerPublishProfile }}
          images: 'gadacr.azurecr.io/alanapi:latest'
      
```

回到Github Action之中 進行Pipeline workflows Commit

此時CICD流程已經啟動 點回Actions頁籤 選取剛剛設置的Workflows進行查看

![](Demo1-2/2020-10-22-17-57-23.png)

CICD流水線結束後 項目發布完成

![](Demo1-2/2020-10-22-17-57-54.png)

訪問App Service 並且加入後方路由地址後 可獲得相關天氣資訊

![](Demo1-2/2020-10-22-17-58-24.png)