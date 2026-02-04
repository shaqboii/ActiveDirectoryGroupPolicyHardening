# Active Directory: Group Policy Hardening
This repo demonstrates the implementation of password policy, account lockout, software restriction policies, firewall config, and LAPS.

From server manager, open ```Tools > Group Policy Management```

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/0a3d989a-ef76-4747-8dfd-6d4dd29b57d1" />

Under default Domain Policy, hit Settings. Scroll down a little and you will see the Account Policies/Password Policies section displaying the current rules.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/ac72ab51-cf2c-4431-ade1-c55b4ada83d4" />

Right now, user passwords must be a minimum of 7 characters in length, with any 3 of 4 complexity requirements enforced. This includes lowercase, uppercase, numerics, and special characters. This, combined with a nonexistent lockout policy, is a little weak. Let's fix that.

Right-click on Default Domain Policy and hit Edit. Then navigate to ```Computer Configuration > Policies > Windows Settings > Security Settings > Account Policies > Password Policy``` From here, you can see the various password policy settings, of which you can double-click to edit. I am going to make the maximum password age to 30 days and the minimum password length to 12.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/2bddba46-01fc-4251-8273-c9b6d811c7e7" />

Now, to configure the lockout policy. I am going to make the user have 4 tries to login, and if they fail to login, they will have to wait 10 minutes to try again. Note that if you choose to make the account lockout duration to 0 minutes, that means that instead of waiting to try again, the user must contact the administrator to regain access to the login portal.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/c5ed78c0-4240-47e3-8ec4-498046601491" />

Now, let's check to see if our changes have been applied. Go back to the Group Policy Management's Settings window and refresh the page. You should see your updated account setting. 

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/1d37e2e7-77e4-4445-b130-27562d4411a4" />

Just to be sure, I will run ```gpupdate /force``` in powershell to ensure that the rules were applied.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/40d2af12-b3b3-4396-a5ed-443264063aa6" />

Let's try setting a poor password on a user to ensure that our rules are in place. From the server manager, go to ```Tools > Active Directory Users and Computers``` Right-click on a user and hit ```Reset Password...``` Recall that I made the minimum length to be 12 characters, with 3 of 4 character complexity requirements. To test this, I will use ```111222aaabbb``` as the password. This does fulfill the length requirement, but not the complexity requirement. 

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/f1a0bf86-7492-405e-a58b-93d964e034ec" />

As expected, we cannot set that as a password. Nice!

Now, I am going to enforce a software restriction that dissallows users to use the Windows Media Player. To do this, while under the Group Policy Management Editor, look for a folder also under security settings named Software Restriction Policies. Then, right-click it and add software restriction policies. It will create two new folders under it; Security Levels and Additional rules. Security Levels defines the level of access a program may have, and Additional Rules hosts the paths to the files of which we want to allow/dissallow. I am going to dissallow the Windows Media Player by adding a new path rule and feeding ```C:\Program Files (x86)\Windows Media Player\wmplayer.exe``` into the path. Under Security Level, I will select Dissallow. After applying it, I will run ```gpupdate /force``` in powershell to make sure it is updated. Now, let's head over to our PC1 to check if the rule has been enforced.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/c3381146-2e6e-4e0a-9b52-d4388bf23f19" />

On the PC1, update the group policy by running ```gpupdate /force``` in powershell. Then restart.

Navigate to the Windows Media Player executable and run it. You will come across this screen. 

<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/801b6822-38b7-4cdd-92ce-d8e59a925c21" />

Congratulations! You've implemented a software restriction!

Now, I will enable and configure a firewall. From Server Manager, go to ```Tools > Group Policy Management```. From the GPM window, locate the ```Group Policy Objects``` folder under your domain. Right-click this folder, and create a new GPO. Name it **Enable Firewall Group Policy**. 

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/28c199cb-20e1-48fe-a10d-a07b5c66dbcf" />

Let's enforce the firewall on our PC1. Right-click on the newly created GPO and hit Edit. Navigate to ```Policies > Administrative Templates > Network > Network Connections > Windows Defender Firewall > Standard Profile```. From here, double-click on ```Windows Defender Firewall: Protect all network connections``` and enable it. Do this for the Domain Profile too.

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/20cb184d-02fd-407b-81ec-243730f44a89" />

Now, let's link this firewall to our PC1. To do this, go back to the Group Policy Management window and right-click on the OU that contains PC1, which in my case, is Workstations, and select **Link an existing GPO**. 

<img width="1920" height="974" alt="image" src="https://github.com/user-attachments/assets/19732192-074b-4da2-9ea0-4c1e3bdb8267" />

Let's ensure that the firewall has been enabled for our PC1. Before we update the GP on PC1, let's intentionally disable Windows Defender Firewall on it. Once we update GP, it should be not only enabled, but strictly controlled by the admin.

<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/6dad72d0-b9c0-4cb2-96a1-e1196d6a22dd" />

Now that it is disabled, let's update GP by running ```gpupdate /force``` in powershell. 

<img width="1024" height="768" alt="image" src="https://github.com/user-attachments/assets/2f93d1ed-3895-4c0e-ba46-35af98bf3831" />

Nice! The firewall was enabled as intended, and it cannot be changed without admin oversight.
