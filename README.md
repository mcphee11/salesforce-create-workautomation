# salesforce-create-workautomation

This is an example how to create a Genesys Work Automation Task from salesforce. In this repo I will be using Flow Builder to create a Genesys Cloud Task when a Salesforce `case` is created. You would also use this method to 'Update' the Task based on case changes.

This requires you to have Admin/Developer experience with salesforce as well as Genesys Cloud. In the `cti` example this also requires you to have the [Genesys Cloud CTI Adaptor](https://appexchange.salesforce.com/appxListingDetail?listingId=a0N30000000pvMdEAI) installed already to allow for the use of the `purecloud.SDK` in the code. If required I will build a different example in a newer folder for the `cx cloud` Adaptor in future... If this is required please raise an issue on this repo and let me know. Or you can build it yourself and do a PR into this repo.

## Step 1 - OAuth (CTI)

Ensure you have a `client credentials` OAuth already setup with the permissions required to create & edit WorkAutomation Tasks. If you have not done this before [here](https://help.mypurecloud.com/articles/create-an-oauth-client/) is a link to the document on this.

![](/docs/images/oauth-client.png?raw=true)

You will then need to apply these client credentials into the CTI Adaptor [Configure authentication](https://help.mypurecloud.com/articles/configure-authentication/)

![](/docs/images/authentication.png?raw=true)

## Step 2 - Deploy Code (CTI)

Open up the Developer center and create a new `Apex Class` call it:

    GCworkautomation

Now copy the code from the `cti/GCworkautomain.apxc` file. I was going to paste it here but this way you will get the most up to date version as I GIT commit more updates.

#### Ensure you save the file.

## Step 3 - Build Flow

### Flow Builder

- Create a new flow from scratch with `Record-Triggered Flow`.

![](/docs/images/flowType.png?raw=true)

Then set your conditions, in my case its when a case is created and the `status=Escalated`. You can use your own conditions of course.

![](/docs/images/caseTrigger.png?raw=true)

- Add an `Action` block and search for `Create WorkItem`

Fill in the Fields with your information such as `WorkbinId` & `typeId`. If you are not sure how to get these you can use the [API Explorer](https://developer.genesys.cloud/devapps/api-explorer) in the `Task Management` section.

![](/docs/images/flowAction.png?raw=true)

Ensure that you just type in the value directly do NOT wrap it in either `"` or `'` otherwise they will be added twice.

### Process Builder (NOT NEEDED IF YOU DID IT VIA FLOW)

If your using `Process Builder` Then create a new Process based on a Case being created. In this example i set it only on a case creation as well as if the `status=Escalated` but you can use your own conditions based on your needs of course.

In the `IMMEDIATE ACTIONS` section select `Call Apex` then the Class you want is:

    Create WorkItem

Fill in the Fields with your information such as `WorkbinId` & `typeId`. If you are not sure how to get these you can use the [API Explorer](https://developer.genesys.cloud/devapps/api-explorer) in the `Task Management` section.

![](/docs/images/processBuilder.png?raw=true)

For the `customFields` you need to ensure you follow this format:

```
'key1=value1,key2=value2,key3=value3'
```

This uses the `Formula` Type which gives you the ability to then include variables and other items while giving you the ability to build a dynamic payload that will get converted into JSON inside this code.

## Step 4 - Test

Now your ready to create a new case with the conditions you set and you should see it will get created as a Task in Genesys Cloud. If you have any issues look in the developer console under the "FutureHandler" Operation that gets created when the code runs.
