# Azure Policy

## Before you Begin

You will need at least 2 VMs already provisioned in order to successfully complete this lab.  If you already have the IIS VMs provisioned from the previous lab, you may continue.  If you deleted them and have no other VMs provisioned, please provision two new VMs using the basic standard settings and then progress to Exercise 1. 


## Lab Concepts

Azure Policy helps you manage and prevent IT issues with policy definitions that enforce rules and effects for your resources. When you use Azure Policy, resources stay compliant with your corporate standards and service level agreements.  This ensures proper governance and standards are maintained through the resources' lifecycles.  As a best practice, Azure policies should be in place prior to migrating your first workloads as part of creating the landing zone.  This ensure proper compliance as you migrate resources into Azure, rather than having to go back and remediate, or potentially rebuild, the resources post-migration.  See [Landing zone considerations](https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/considerations/) under the Microsoft Cloud Adoption Framework for more information and pre-migration best practices.  

In this lab we are going to create an Azure policy that will report when the tag  `department` is not set to `lab`.  With your practice, you may want to have a similar policy that checks for the existance of a tag, like environment or customer, regarless of value or with a specific value.  This will help you as the MSP to manage your customers' resources and ensure they can meet regulatory or business requirements (Azure policy does a lot more than just tags!).

After completing this lab, you should understand how to:
1. Use Azure policies to enforce or audit against specific resource settings
2. Create and configure new policy initiative definitions
3. Assign policies and check for compliance



## Exercise 1 - Create a policy initiative definition

A definition is the building block for Azure policies.  Definitions define what the policy is validating and which actions should be taken if the condition is or is not met.  You can create your own policy definition using JSON, or choose from many of the pre-existing policy definitions already available.  Let's use the existing policy definitions to prevent new resources from being deployed outside of an US region.  You will create an initiative definition to do this, which will not only use a predefined policy definition, but will also fill in the parameters that you want to customize and enfore.

1. Launch the Azure Policy service in the Azure portal by clicking **All services**, then searching for and selecting **Policy**. Click on the star and **Policy** will now appear on the left pane.  Select **Policy**.
2. Select **Definitions** under Authoring in the left side of the Azure Policy page.
3. Select **+ Initiative Definition** at the top of the page to open the Initiative definition page.
4. Choose your subscription as the 'Definition Location'.  This implies where the policy initiative definition is saved; policy scope is limted to this parent and children of the parent.
5. Enter **Region Enforcement** as the Name and create a brief Description of the policy, such as 'Prevent resource creation outside of US Regions.'
6. For Category, choose *Create new*, and enter **MyCustomPolicies**.
7. Under **AVAILABLE DEFINITIONS** search for *location* and then select **Allowed Locations**, then **+Add**.
8. Under **Allowed Locations** select the US regions from the drop-down list box.  Choose `Central US, East US, East US 2, North Central US, South Central US, West Central US, West US and West US 2`
9. Click **Save**.



## Exercise 2 - Create a policy definition

The first policy initiative definition you created was a deny policy.  Meaning resource creation will be denied if it violates the policy.  In this exercise you will create a new custom audit policy.  Audit policies do not prevent the creation of resouces that are out of compliance with the policy, but instead provides a report showing which resouces do not meet compliance.  From the report, you can manually corrent the resource to bring it back into compliance, or use automation scripts and/or Azure policy to bring it back into compliance automatically.  You will define your new custom policy using JSON.

1. Launch the Azure Policy service in the Azure portal by clicking **All services**, then searching for and selecting **Policy**. Click on the star and **Policy** will now appear on the left pane.  Select **Policy**.
1. Select **Definitions** under Authoring in the left side of the Azure Policy page.
2. Select **+ Policy Definition** at the top of the page to open the policy definition page.
3. Choose your subscription as the 'Definition Location'.  This implies where the policy definition is saved; policy scope is limted to this parent and children of the parent.
4. Enter **Audit for Tag Existence** as the Name and create a brief Description of the policy, such as 'Audit which resources do not have the proper tags.'
5. For Category, choose *Use existing* and select **Tags**.
6. Under **POLICY RULE** copy the JSON text below and paste it into the policy rule box - overwriting what was there

```
{
  "mode": "Indexed",
  "policyRule": {
    "if": {
      "not": {
        "field": "tags",
        "containsKey": "[parameters('ctagName')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "ctagName": {
      "type": "String",
      "metadata": {
        "displayName": "Tag Name",
        "description": "The name of the tag to look for, such as department, cost center, customer"
      }
    }
  }
}
```
8. Click **Save**.


## Exercise 3 - Create an initiative policy definition using a custom policy
Now that you have a custom policy created, you can use it as the basis for your new initiative policy definition.

1. Launch the Azure Policy service in the Azure portal by clicking **All services**, then searching for and selecting **Policy**. Click on the star and **Policy** will now appear on the left pane.  Select **Policy**.
2. Select **Definitions** under Authoring in the left side of the Azure Policy page.
3. Select **+ Initiative Definition** at the top of the page to open the Initiative definition page.
4. Choose your subscription as the 'Definition Location'.  This implies where the policy initiative definition is saved; policy scope is limted to this parent and children of the parent.
5. Enter **Audit for Customer Tag** as the Name and create a brief Description of the policy, such as 'Check resources to ensure a customer tag has been applied.'
6. For Category, choose *USe Existing* and select **MyCustomPolicies**.
7. Under **AVAILABLE DEFINITIONS** search for *tag* and then select the custom policy you created in Exercise 2 **Audit for Tag Existence**, then **+Add**.
8. Under **Tag Name** set the value to `Customer`
9. Click **Save**.

##Exercise 4 - Add a Tag to Your VM

In order to test the audit policy, you need to add a tag to one of your VMs

1. Within the Azure Portal, find one of your VMs you previously created and click on it to go to the resource
2. Click on **Tags**
3. Enter `Customer` as the Tag Name
4. Enter any value you wish as the Tag Value
5. Click **Save**


##Exercise 5 - Assign the Policy Initiatives
In order for your policies to go into effect, you must assign them a scope and activate them.

1. Launch the Azure Policy service in the Azure portal by clicking **All services**, then searching for and selecting **Policy**. Click on the star and **Policy** will now appear on the left pane.  Select **Policy**.
2. Select **Assignments** under **Authoring** on the left side of the Azure Policy page. 
3. Select **Assign Initiative** from the top of the **Policy - Assignments** page.
4. On the **Assign Initiative** page, select **Region Enforcement** as the *Initiative Definition*, using the ellipsis to open the list of available definitions.
5. Search for it if you don't see **Region Enforcement** available.
6. Enter a brief description of the policy assignment.
7. Click **Review + create** and then **Create**.
8. Repeat steps 1-7, using the **Customer Tag Assigned** initiative.

## Exercise 6 - Check Deny Policy
Let's check to see if the deny policy works correctly by trying to create a new VM in a region outside of the US.

1. Within the Azure Portal, create a new VM
2. Create a new Windows Server 2016 VM and select a region outside of the US
3. Eenter in all other mandatory information.
4. Click **Review + create**
5. Were you able to create the VM or did you receive an error?
6. If your policy was created and applied correctly, you should receive a **validation failed** error.
7. Click on the error to view the details.
8. In the details pain, you should see that the **Initiative: Region Enforcement** policy prevented the creation of the VM.
9. Click **Raw Error**
10. Observe the JSON deployment details.  You should see that the `expressionValue:` for `expression:location` is a non-US region, and the `evaluatedExpressions` includes only US region, listed under the `targetValue` section.


## Exercise 7 - Check initial compliance

Now let's check to see if our second policy initiative successfully identifies VMs missing a **customer** tag.  Please note that it may take Azure Policy up to `10-30 minutes` to evaluate and report results.  NOTE: For future use, you can manually force an on-demand evaluation scan by using a REST API command [docuemented here.](https://docs.microsoft.com/en-us/azure/governance/policy/how-to/get-compliance-data#on-demand-evaluation-scan)

1. Select **Compliance** in the left side of the Azure Policy page.
2. Locate the **Customer Tag Assigned** initiative. If it's still in a Compliance state of `Not started` or `Non-compliant`, wait a few more minutes, refresh and check again.
3. Click on the initiative to get full details on the progress of the assignment.
4. Do you have any VMs that are compliant?  
5. How about non-compliant VMs?  Can you identify those?



## Exercise 8 - Modify Non-compliant resources
Once evaluations are returned, you will notice that most of your resources are non-compliant.  Complete these steps to make one VM resource compliant and review the results.

1. Select **Compliance** in the left side of the Azure Policy page 
2. Click on **Customer Tag Assigned** and then on **Audit for Tag Existence** to see individual details by resource
2. Under resource compliance, click on one of the VMs and then **view resource**.
3. Click **Tags** to enter a new tag
4. Set `Tag Name` to **Customer**.
5. Set `Tag Value` to any value you choose.
6. Click **Save**
7. On the left-hand side of the VM menu click **Policies** 
8. Notice the VM is still not compliant as the policy has not run a new evaluation since the change. 
9. The VM should be compliant upon next policy evaluation, which occurs once every 24 hours for already created resource.  New resources are validated against enfore policies at the time of their creation.



<br></br>
[Back to Table of Contents](./index.md#4-azure-migrate)