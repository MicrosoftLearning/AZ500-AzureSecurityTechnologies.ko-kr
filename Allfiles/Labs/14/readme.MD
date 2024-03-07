여기에는 랩에 구성된 리소스를 배포하는 bicep 파일이 포함됩니다.

이 템플릿을 배포하려면 다음을 수행합니다. 

```
$date = Get-Date -Format "MM-dd-yyyy"
$rand = Get-Random -Maximum 1000
$deploymentName = "AzInsiderDeployment-"+"$date"+"-"+"$rand"
New-AzResourceGroupDeployment -Name $deploymentName -ResourceGroupName AZ500LAB131415 -TemplateFile .\main.bicep -TemplateParameterFile .\azuredeploy.parameters.json -c
```
