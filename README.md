
# Anypoint Template: Salesforce to Salesforce User Broadcast

+ [License Agreement](#licenseagreement)
+ [Use Case](#usecase)
+ [Considerations](#considerations)
	* [Salesforce Considerations](#salesforceconsiderations)
+ [Run it!](#runit)
	* [Running on premise](#runonopremise)
	* [Running on Studio](#runonstudio)
	* [Running on Mule ESB stand alone](#runonmuleesbstandalone)
	* [Running on CloudHub](#runoncloudhub)
	* [Deploying your Anypoint Template on CloudHub](#deployingyouranypointtemplateoncloudhub)
	* [Properties to be configured (With examples)](#propertiestobeconfigured)
+ [API Calls](#apicalls)
+ [Customize It!](#customizeit)
	* [config.xml](#configxml)
	* [businessLogic.xml](#businesslogicxml)
	* [endpoints.xml](#endpointsxml)
	* [errorHandling.xml](#errorhandlingxml)


# License Agreement <a name="licenseagreement"/>
Note that using this template is subject to the conditions of this [License Agreement](AnypointTemplateLicense.pdf).
Please review the terms of the license before downloading and using this template. In short, you are allowed to use the template for free with Mule ESB Enterprise Edition, CloudHub, or as a trial in Anypoint Studio.

# Use Case <a name="usecase"/>
As a Salesforce admin I want to migrate Users from one Salesforce organization to another one.

This Template should serve as a foundation for setting an online sync of Users from one Salesforce instance to another. Everytime there is new User or a change in an already existing one, the integration will poll for changes in Salesforce source instance and it will be responsible for updating the User on the target org.

What about Passwords? When the User is updated in the target instance, the password is not changed and therefore there is nothing to concern about in this case. Password set in case of User creation is not being covered by this template considering that many different approaches can be selected.

Requirements have been set not only to be used as examples, but also to establish a starting point to adapt your integration to your requirements.

As implemented, this Template leverages the [Batch Module](http://www.mulesoft.org/documentation/display/current/Batch+Processing).
The batch job is divided in *Process* and *On Complete* stages.
The integration is triggered by a scheduler defined in the flow that is going to trigger the application, querying newest Salesforce updates/creations matching a filter criteria and executing the batch job.
During the *Process* stage, each Salesforce User will be filtered depending on, if it has an existing matching user in the Salesforce Org B.
The last step of the *Process* stage will group the users and create/update them in Salesforce Org B.
Finally during the *On Complete* stage the Template will log output statistics data into the console.

# Considerations <a name="considerations"/>

To make this Anypoint Template run, there are certain preconditions that must be considered. All of them deal with the preparations in both source and destination systems, that must be made in order for all to run smoothly. **Failing to do so could lead to unexpected behavior of the template.**

1. **Users cannot be deleted in Salesforce:** For now, the only thing to do regarding users removal is disabling/deactivating them, but this won't make the username available for new user.
2. **Each user needs to be associated to a Profile:** Salesforce's profiles are what define the permissions the user will have for manipulating data and other users. Each Salesforce account has its own profiles. In this kick you will find a processor labeled *Prepare for Upsert* where we map your Profile Ids from the source account to the ones in the target account. Note that for the integration test to run properly, you should change the constant *DEFAULT_PROFILE_ID* in *BusinessLogicTestIT* to one that's valid in your source test organization.
3. **Working with sandboxes for the same account**: Although each sandbox should be a completely different environment, Usernames cannot be repeated in different sandboxes, i.e. if you have a user with username *bob.dylan* in *sandbox A*, you will not be able to create another user with username *bob.dylan* in *sandbox B*. If you are indeed working with Sandboxes for the same Salesforce account you will need to map the source username to a different one in the target sandbox, for this purpose, please refer to the processor labeled *Prepare for Upsert*.



## Salesforce Considerations <a name="salesforceconsiderations"/>

There may be a few things that you need to know regarding Salesforce, in order for this template to work.

In order to have this template working as expected, you should be aware of your own Salesforce field configuration.

### FAQ

 - Where can I check that the field configuration for my Salesforce instance is the right one?

    [Salesforce: Checking Field Accessibility for a Particular Field][1]

- Can I modify the Field Access Settings? How?

    [Salesforce: Modifying Field Access Settings][2]


[1]: https://help.salesforce.com/HTViewHelpDoc?id=checking_field_accessibility_for_a_particular_field.htm&language=en_US
[2]: https://help.salesforce.com/HTViewHelpDoc?id=modifying_field_access_settings.htm&language=en_US

### As source of data

If the user configured in the template for the source system does not have at least *read only* permissions for the fields that are fetched, then a *InvalidFieldFault* API fault will show up.

```
java.lang.RuntimeException: [InvalidFieldFault [ApiQueryFault [ApiFault  exceptionCode='INVALID_FIELD'
exceptionMessage='
Account.Phone, Account.Rating, Account.RecordTypeId, Account.ShippingCity
^
ERROR at Row:1:Column:486
No such column 'RecordTypeId' on entity 'Account'. If you are attempting to use a custom field, be sure to append the '__c' after the custom field name. Please reference your WSDL or the describe call for the appropriate names.'
]
row='1'
column='486'
]
]
```

### As destination of data

There are no particular considerations for this Anypoint Template regarding Salesforce as data destination.









# Run it! <a name="runit"/>
Simple steps to get Salesforce to Salesforce User Broadcast running.


## Running on premise <a name="runonopremise"/>
In this section we detail the way you should run your Anypoint Template on your computer.


### Where to Download Mule Studio and Mule ESB
First thing to know if you are a newcomer to Mule is where to get the tools.

+ You can download Mule Studio from this [Location](http://www.mulesoft.com/platform/mule-studio)
+ You can download Mule ESB from this [Location](http://www.mulesoft.com/platform/soa/mule-esb-open-source-esb)


### Importing an Anypoint Template into Studio
Mule Studio offers several ways to import a project into the workspace, for instance: 

+ Anypoint Studio Project from File System
+ Packaged mule application (.jar)

You can find a detailed description on how to do so in this [Documentation Page](http://www.mulesoft.org/documentation/display/current/Importing+and+Exporting+in+Studio).


### Running on Studio <a name="runonstudio"/>
Once you have imported you Anypoint Template into Anypoint Studio you need to follow these steps to run it:

+ Locate the properties file `mule.dev.properties`, in src/main/resources
+ Complete all the properties required as per the examples in the section [Properties to be configured](#propertiestobeconfigured)
+ Once that is done, right click on you Anypoint Template project folder 
+ Hover you mouse over `"Run as"`
+ Click on  `"Mule Application"`


### Running on Mule ESB stand alone <a name="runonmuleesbstandalone"/>
Complete all properties in one of the property files, for example in [mule.prod.properties] (../master/src/main/resources/mule.prod.properties) and run your app with the corresponding environment variable to use it. To follow the example, this will be `mule.env=prod`. 
Once your app is all set and started, there is no need to do anything else. The application will poll Salesforce to know if there are any newly created or updated objects and synchronize them.

## Running on CloudHub <a name="runoncloudhub"/>
While [creating your application on CloudHub](http://www.mulesoft.org/documentation/display/current/Hello+World+on+CloudHub) (Or you can do it later as a next step), you need to go to Deployment > Advanced to set all environment variables detailed in **Properties to be configured** as well as the **mule.env**.
Once your app is all set and started, you will need to define Salesforce outbound messaging and a simple workflow rule. [This article will show you how to accomplish this](https://www.salesforce.com/us/developer/docs/api/Content/sforce_api_om_outboundmessaging_setting_up.htm)
The most important setting here is the `Endpoint URL` which needs to point to your application running on CloudbHub, eg. `http://yourapp.cloudhub.io:80`. Additionally, try to add just few fields to the `Fields to Send` to keep it simple for begin.
Once this all is done every time when you will make a change on Account in source Salesforce org. This account will be sent as a SOAP message to the Http endpoint of running application in Cloudhub.

### Deploying your Anypoint Template on CloudHub <a name="deployingyouranypointtemplateoncloudhub"/>
Mule Studio provides you with really easy way to deploy your Template directly to CloudHub, for the specific steps to do so please check this [link](http://www.mulesoft.org/documentation/display/current/Deploying+Mule+Applications#DeployingMuleApplications-DeploytoCloudHub)


## Properties to be configured (With examples) <a name="propertiestobeconfigured"/>
In order to use this Mule Anypoint Template you need to configure properties (Credentials, configurations, etc.) either in properties file or in CloudHub as Environment Variables. Detail list with examples:
### Application configuration
**Scheduler configuration**
+ scheduler.frequency `60000`
+ scheduler.startDelay `0`

**Batch Aggregator configuration**
+ page.size `100`

**Watermarking default last query timestamp e.g. 2017-12-13T03:00:59Z**
+ watermark.defaultExpression `2017-12-13T03:00:59Z`

**Salesforce Connector configuration for company A**
+ sfdc.a.username `bob.dylan@orga`
+ sfdc.a.password `DylanPassword123`
+ sfdc.a.securityToken `avsfwCUl7apQs56Xq2AKi3X`

**Salesforce Connector configuration for company B**
+ sfdc.b.username `joan.baez@orgb`
+ sfdc.b.password `JoanBaez456`
+ sfdc.b.securityToken `ces56arl7apQs56XTddf34X`
+ sfdc.b.profile.id `00f10000003JzFxAAX`

# API Calls <a name="apicalls"/>
Salesforce imposes limits on the number of API Calls that can be made. Therefore calculating this amount may be an important factor to consider. User Broadcast Template calls to the API can be calculated using the formula:

***1 + X + X / ${page.size}***

Being ***X*** the number of Users to be synchronized on each run. 

The division by ***${page.size}*** is because by default, for each Upsert API Call, Users are gathered in groups of a number defined by the ${page.size} property. Also consider that this calls are executed repeatedly every polling cycle.	

For instance if 10 records are fetched from origin instance, then 12 api calls will be made (1 + 10 + 1).


# Customize It!<a name="customizeit"/>
This brief guide intends to give a high level idea of how this Anypoint Template is built and how you can change it according to your needs.
As mule applications are based on XML files, this page will be organized by describing all the XML that conform the Anypoint Template.
Of course more files will be found such as Test Classes and [Mule Application Files](http://www.mulesoft.org/documentation/display/current/Application+Format), but to keep it simple we will focus on the XMLs.

Here is a list of the main XML files you'll find in this application:

* [config.xml](#configxml)
* [endpoints.xml](#endpointsxml)
* [businessLogic.xml](#businesslogicxml)
* [errorHandling.xml](#errorhandlingxml)


## config.xml<a name="configxml"/>
Configuration for Connectors and [Configuration Properties](http://www.mulesoft.org/documentation/display/current/Configuring+Properties) are set in this file. **Even you can change the configuration here, all parameters that can be modified here are in properties file, and this is the recommended place to do it so.** Of course if you want to do core changes to the logic you will probably need to modify this file.

In the visual editor they can be found on the *Global Element* tab.


## businessLogic.xml<a name="businesslogicxml"/>
Functional aspect of the Template is implemented on this XML, directed by a batch job that will be responsible for creations/updates. The several message processors constitute high level actions that fully implement the logic of this Template:

1. During the *Process* stage, each Salesforce User will be filtered depending on, if it has an existing matching user in the Salesforce Org B.
2. The last step of the *Process* stage will group the users and create/update them in Salesforce Org B.
3. Finally during the *On Complete* stage the Template will log output statistics data into the console.



## endpoints.xml<a name="endpointsxml"/>
This file is conformed by two flows.

The first one we'll call it **scheduler** flow. This one contains the Scheduler endpoint that will periodically trigger **watermarking** flow and then executing the batch job process.

The second one we'll call it **watermarking** flow. This one contains watermarking logic that will be querying Salesforce for updated/created Users that meet the defined criteria in the query since the last polling. The last invocation timestamp is stored by using Objectstore Component and updated after each Salesforce query.



## errorHandling.xml<a name="errorhandlingxml"/>
This is the right place to handle how your integration will react depending on the different exceptions. 
This file holds a [Error Handling](http://www.mulesoft.org/documentation/display/current/Error+Handling) that is referenced by the main flow in the business logic.



