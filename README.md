# Hands-on Workshop: Using Salesforce DX for Lightning Development

### Prerequisites:

Online:

* Github account: https://github.com/join
* Salesforce DX trial org: https://developer.salesforce.com/promotions/orgs/dx-signup

Local Machine:

* Git (includes CLI tool and Bash): https://git-scm.com/downloads 
* Salesforce DX CLI tool: https://developer.salesforce.com/tools/sfdxcli
* Terminal for Mac, Git Bash (usually comes with git installation) for Windows.
* Text Editor (preferable VS Code): https://code.visualstudio.com/download

note: some screenshots are a little outdated.

### Step 1: Master repo in github

 Log into github, create a new repository. (Github is similar to Bitbucket repositories)
[Image: file:///-/blob/KUTAAAjvy1y/xwD_dS5M2hzKkDGYX51W2w]
### Step 2: Create new local project

Make sure sfdx CLI is updated and all necessary plugins are installed

```
sfdx update
```

On local machine, create a new sfdx project in the terminal

```
sfdx force:project:create -n dxlightning
cd dxlightning
```

[Image: file:///-/blob/KUTAAAjvy1y/Bh1IAF0qyoJqZgjYWULK9g]
### Step 3: Initialize local git repo

Initialize as a git repository and check status

```
git init
git status
```

[Image: file:///-/blob/KUTAAAjvy1y/1sFkqzYu8TGqs3LABqvoew]
### step 4: open code editor

Open in VS Code (or any other text editor)
[Image: file:///-/blob/KUTAAAjvy1y/FPVTfFyE1d_rhtCj1zDzww]
### step 5: working with CLI

Open terminal in VS Code. If not using VS code, open Terminal for Mac or Git Bash for Windows.
use SFDX cli to create a new lightning component quickContact in aura folder and open it in the editor

```
sfdx force:lightning:component:create -d force-app/main/default/aura/ -n quickContact
```

[Image: file:///-/blob/KUTAAAjvy1y/_H-gy4QUMrNM8Mm9Kt_hgg]
### step 6: Write code

Paste following code in quickContact.cmp file and save

```
<!-- component with interfaces added to give it ability to be used for Record home pages with inbuilt recordId attribute -->
<aura:component implements="flexipage:availableForRecordHome,force:hasRecordId" access="global" >

    <!-- attribute to store the contact record returned by Lightning Data Service -->
    <aura:attribute name="con" type="Contact"/>
    
    <!-- Lightning Data Service usage in EDIT mode to get the Contact record -->
    <force:recordData aura:id="DataService"
                        recordId="{!v.recordId}"
                        mode="EDIT"
                        layoutType="FULL"
                        targetFields="{!v.con}" />
    
    <!-- UI Base component using SLDS for a Container of the Contact fields -->
    <lightning:card title="Edit Contact quickly" iconName="utility:edit">
        
        <!-- Input base component for editing fields -->
        <p class="slds-p-horizontal--small">
                <lightning:input type="text" label="First Name" name="first" value="{!v.con.FirstName}" />
                <lightning:input type="text" label="Last Name" name="last" value="{!v.con.LastName}" />
                <lightning:input type="email" label="Email" name="email" value="{!v.con.Email}" />
                <lightning:input type="tel" label="Phone" name="phone" value="{!v.con.Phone}" />
        </p>
        <!-- Button base component added to the container through lightning:card "actions" attribute -->
        <aura:set attribute="actions">
            <lightning:button label="Save" onclick="{!c.handleSave}" iconName="utility:save"/>
        </aura:set>
        
    </lightning:card>

</aura:component>
```

Paste following code in quickContactController.js and save

```
({
    // function to handle the "Save" button click
    handleSave : function(component, event, helper) {
        //invoke Data Service's inbuilt saveRecord function
        component.find("DataService").saveRecord(function(result){});
    }
})
```

### step 7: Authenticate SFDX

Let's see how this works in a scratch org. Bur first, we need to authenticate our CLI with an org which has Salesforce DX Dev Hub enabled. Let's use our DX trial org for this.

```
sfdx force:auth:web:login -d -a devHub
```

[Image: file:///-/blob/KUTAAAjvy1y/sAMM5EkX6i0FZKW22gRcKw]
### step 8: New Scratch org

Create a new scratch org using SFDX CLI

```
sfdx force:org:create -f config/project-scratch-def.json -s -a devOrg
```

[Image: file:///-/blob/KUTAAAjvy1y/CD4h7RZarb09U_3rV8Xmnw]
### step 9: deploy code

Push code to the scratch org

```
sfdx force:source:push
```

[Image: file:///-/blob/KUTAAAjvy1y/2Lmwxms84N5pm7shMM0fFw]
### step 10: Open Scratch Org

Open scratch org using the CLI

```
sfdx force:org:open
```

[Image: file:///-/blob/KUTAAAjvy1y/LEyguyRGk29R8oV3ts4kXQ][Image: file:///-/blob/KUTAAAjvy1y/rFLvE3aSXkKDTgHirdwIng]
### step 11: Work in the scratch org

Add the new component to the Contact record home page as a new default tab on the right sidebar
[Image: file:///-/blob/KUTAAAjvy1y/R1NkQ14o2Ey8yepBwDLUkA][Image: file:///-/blob/KUTAAAjvy1y/yzm0USHi-EpOS7-_ew2TzQ][Image: file:///-/blob/KUTAAAjvy1y/o1_NLTz--IpNxdw3uWueGw][Image: file:///-/blob/KUTAAAjvy1y/pkOB_EEq6k1sXxKMyTOVKw][Image: file:///-/blob/KUTAAAjvy1y/i_7ZXPzPpjCvgaNwBKW1CQ][Image: file:///-/blob/KUTAAAjvy1y/drV0sI3UeKKw8NzQEnbyjw]
### step 12: Test changes

Test the component
[Image: file:///-/blob/KUTAAAjvy1y/KQDhVT2qtWRaFu9DLfMfTQ][Image: file:///-/blob/KUTAAAjvy1y/1AmVQv45YJFNlrQu7DGxuA]
### step 13: Pull changes to local source

Now, let's pull declarative changes from the scratch org into our local source

```
sfdx force:source:pull
```

[Image: file:///-/blob/KUTAAAjvy1y/fhOEZ0HflAovgHAW9MXMHw]
### step 14: Commit changes

We're ready to commit changes to git, but first let's ignore unnecessary folders by create a new .gitignore file

```
.sfdx
.vscode
.project
```

[Image: file:///-/blob/KUTAAAjvy1y/WexkEfcGys9S-Qv6ErJd0Q]Add changes to git and commit

```
git add .
git commit -m "changes done"
```

[Image: file:///-/blob/KUTAAAjvy1y/CV_NJC_iEQ27lnVWyuW2pA]
### step 15: Push commit to master Github repo

Push to github by adding github repository as a remote

```
git remote add origin https://github.com/< your github username >/DXLightning
git remote -v
```

[Image: file:///-/blob/KUTAAAjvy1y/4KWZ2YmGacl0HlMW-AyFSQ]
```
git push origin master
```

[Image: file:///-/blob/KUTAAAjvy1y/i4pMPjUteZrSx2a9UiJ4tQ]Verify in github
[Image: file:///-/blob/KUTAAAjvy1y/ixx_d9C6NbtlhyvobO4sCw]
Salesforce DX resources: https://developer.salesforce.com/platform/dx

