# Azure Resource Master Tag List

![Azure](https://img.shields.io/badge/azure-%230072C6.svg?style=for-the-badge&logo=microsoftazure&logoColor=white) :construction:

## Tags

|    Tag       |    Format     |  Values  |  Notes    |
|--------------|:--------------|----------|-----------|
|  * CreatedOn  | MM/DD/YYYY  | 01/31/1900  |  |
|  * CreatedBy  | User Name  | Name  |  |
|  * BusinessUnit | | |
|  * SupportGroup  | String  | [SNOW List](https://pediatrix.service-now.com/now/nav/ui/classic/params/target/sys_user_group_list.do%3Fsysparm_target%3Dincident.assignment_group%26sysparm_target_value%3D%26sysparm_reference_value%3D%26sysparm_nameofstack%3Dreflist%26sysparm_clear_stack%3Dtrue%26sysparm_element%3Dassignment_group%26sysparm_reference%3Dsys_user_group%26sysparm_view%3Dsys_ref_list%26sysparm_form_view%3Ddefault%26sysparm_additional_qual%3D%26sysparm_domain_restore%3Dfalse) | |
|  ApplicationName  | String  | _Working_  | Need to get a list of appropriate valuies  |
|  IAC | String | Y/N | Terraform or other means of deployment/management |
|  BusinessOwner  | User Name  | Jeff Truman |  |
|  TechnicalContact  | User Name  | Jeff Truman |  |
|  Criticality  | Range 1 through 4  | 1,2,3,4 |  |

## VM :computer:

VM Specific Tags - in addition to mater tag list

|    Tag       |    Format     |  Values  |  Notes    |
|--------------|:--------------|----------|-----------|
|  PatchingWindow  | HH:MM to HH:MM  | 03:00 AM to 07:00 AM|  |
|  PatchingDOW  | Letter(s) of Day  | MTWTFSS |  |


## AKS :whale:

List based on tags returned by general query.


|    Tag       |    Format     |  Values  |  Notes    |
|--------------|:--------------|----------|-----------|
|  aks-managed-cluster-name  |  |  _Working_  |     |
|  aks-managed-cluster-rg  |  |  _Working_  |     |
|  aks-managed-createOperationID  |  |  _Working_  |     |
|  aks-managed-creationSource  |  |  _Working_  |     |
|  aks-managed-kubeletIdentityClientID  |  |  _Working_  |     |
|  aks-managed-operationID  |  |  _Working_  |     |
|  aks-managed-orchestrator  |  |  _Working_  |     |
|  aks-managed-poolName  |  |  _Working_  |     |
|  aks-managed-resourceNameSuffix  |  |  _Working_  |     |
|  aks-managed-type  |  |  _Working_  |     |
|  ingress-for-aks-cluster-id  |  | _Working_  |   |

## Code Sampleas

### Adding base required tags to Resource (Virtual Machine):

```powershell
$tags = @{
    "CreatedOn"        = "10/14/2021"
    "Criticality"      = "2" 
    "Application"      = "Application Name"
    "BusinessOwner"    = "Full Name seperate names with /"
    "TechnicalContact" = "Full Name"
    "PatchingWindow"   = "Patching Time Window"
    "PatchingDOW"      = "Days of the week Patches can be applied like MTWTFSS"
    "Compliance"       = "Y"
    "Environment"      = "Production"
    "IAC"              = "N"
}
$res = Get-AzResource -Name servername -ResourceType Microsoft.Compute/virtualMachines
New-AzTag -ResourceId $res.id -Tag $tags
```

### Use OSDisk to estimate the CreatedOn tag for a VM

```powershell
Set-Azcontext -Subscription SubscriptionName | Out-Null
$vms = Get-AzVM
$tagName = 'CreatedOn'
ForEach ($vm in $vms) {
    $dskInfo = get-azdisk -Name $vm.StorageProfile.OsDisk.Name
    $tagValue = '{0:MM/dd/yyyy}' -f $dskInfo.TimeCreated
    $vm.Name
    If ($vm.tags.Keys -contains $tagName) {
        "`tTag Exists"
    }
    Else {
        "`tWill set CreatedOn Tag for {0} on {1}" -f $vm.Name, $tagValue
        $vm.Tags.Add($tagName, $tagValue)
        Set-AzResource -ResourceId $vm.Id -Tag $vm.tags -Force
    }
}
```

