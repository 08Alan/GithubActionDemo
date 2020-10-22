# GithubActionDemo New Features Demo

## 預先環境配置：

- Github Personal Account
- Visual Studio 2019 or Visual Studio Code
- Azure CLI Client Tool

## 預先準備Azure資源

Azure Resource：
-	2 * App Service
    - Azure App Service Windows
    - Azure App Service for Container
-	1 Container Registry

![](README/2020-10-22-16-34-13.png)

## 項目說明
alanAPI是一個利用.NET Core撰寫的API接口
此項目僅有一個API路由
EX: `contoso.azurewebsites.net/WeatherForecast`

## Demo 環節

> 1. 利用Github Action 完成App Service中的部署

    - 部署一：將此API部署至App Service Windows中

    - 部署二：將此API部署至App Service for Container且利用Visual Studio推送容器

  2. Github Security

    - Dependabot：掃描代碼安全漏洞

    - Code Scanning：利用CodeQL進行CICD流程時的代碼漏洞掃描

  3. Github CodeSpaces 代碼遠程協作功能
  
  4. Github Project 達成項目工作流程控管
