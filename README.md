# Active Directory: Group Policy Hardening
This repo demonstrates the implementation of password policy, account lockout, and software restriction policies.

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

