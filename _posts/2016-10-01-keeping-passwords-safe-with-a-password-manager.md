---
title:  "Keeping Passwords Safe / Why and How I Use Password Managers"
date:   2016-10-01 14:43:26 +0100
categories: [Security]
tags: [password, security]
---

I had several chats recently about how and why accounts get stolen. I realized that reasons for hiding passwords from others and the ways of doing it are not at all obvious. That is why I decided to create a short post where I advocate the use of password managers, describe their functions and talk about how I believe they should be used in the correct and secure way.

So before we take a look at what password managers are, let's talk about passwords themselves a bit so that we understand why it is so helpful and cool to use a password manager.

## Strong password

Passwords are **secret** strings of characters that you send along with your username to a service provider (Google, Facebook, Uber, Dropbox, your local library's information system, in general, all **social networks**, **internet portals**, **forums** or **cloud services**) in order to register and log in to your account.

What you aim for when creating a password is to make it **as hard to guess as possible**. Internet services and tools often provide a password strength checker on the registration form.

To make your password secure enough, they encourage you to:

1. include at least one uppercase and one lowercase letter
2. include at least one number
3. include a special character
4. make the password long enough

What does it mean "long enough"? In this article - [Thycotic - Calculating Password Complexity](https://thycotic.force.com/support/s/article/Calculating-Password-Complexity), there is a calculation made that says a password like "%ZBGbv]8" is breakable in 4 hours. For a password with the length of 10 characters, it is 3 years. Note that the example password meets the first three requirements for a strong password. Since the amount of time grows exponentially with every additional character, it is obvious that **the key attribute** for a very **strong password** is its **length**!

With weak passwords, and an irresponsible use of them, you are prone to several types of attacks. Let me present some of them to you:

### Brute Force Attack

**Automated password guessing** is called a Brute Force attack. For simplicity, let's assume a hacker already knows his victim's username. He can write a simple script to generate strings and send different username/password combinations to the login form.

Each attempt to log in with one username and password combination is an HTTP request that travels over the internet to a destination server where it's validated and compared against data in the database. Then a response is generated and sent back, which tells the hacker whether he succeeded.

This **takes a lot of time**, but still, with a short or common password you get your ID stolen very quickly, especially if your password contains words from **password dictionaries** (word lists of the most commonly used passwords) that are used by more sophisticated scripts to guess your password quicker.

So always remember:

> **DO** use a password that’s strong enough to prevent others from guessing it!

Now, different web services treat passwords with different amount of care. **There are still those that store your password in plain text**. Other services use advanced techniques like [hashing](https://en.wikipedia.org/wiki/Cryptographic_hash_function) or [salting](https://en.wikipedia.org/wiki/Salt_(cryptography)) that make it more difficult for a hacker to find your password using brute force even if he manages to steal the user database.

Nevertheless, hashing and salting don't make it impossible to read the user database once it's stolen. A **Brute Force attack** can be performed to find passwords in plain text. At the same time, you may even never find out that someone has stolen your data unless the service provider has a suspicion of getting hacked or the stolen database gets posted to dark-web forums (which you can check on sites like [haveibeenpwned.com](https://haveibeenpwned.com/) or [leakedsource.com](https://www.leakedsource.com/)).

To avoid single points of failures at securing your privacy:

> **DO NOT** use one password to log in to multiple accounts!

### Phishing

Phishing equals **tricking people** into providing sensitive information to an untrusted entity. Persuading you to share your account credentials out loud during a drinking game belongs to phishing skills too. On the internet, things go very similarly. An attacker manages to navigate you to a trustworthy / pretty / familiar-looking website that **asks you to log in or register**. After you "log in" you are, in most cases, redirected to the real website which makes you think you provided invalid credentials.

So how do you know you're not being screwed over?

The first thing you should always do when filling a login form is to make sure you're on a safe page, for example by **checking the URL** of the site you're on for any strange parts or if it’s different from what you expect it to be. In fact, your credentials should always be connected to one domain name only. You should also **avoid inserting passwords on sites that don't use HTTPS**.

To complete the little summary of statements of wisdom, always follow this advice:

> **DO NOT** insert passwords into untrusted web forms!

## Managing passwords

In previous chapters, we confirmed we should use strong passwords and that we should use as many passwords for as many accounts we have. This means we have to somehow manage our existing usernames and passwords. Here are some of the approaches people take:

**Remembering passwords** to all accounts is what most people do. **To remember many different passwords is a challenging task for your brain cells**. One of the ways is coming up with a strong password (a long sentence) and append one more word to it that’s different for every service you register to. If you come up with a sustainable system and are good enough at remembering things, this may be the way. But in general, it is still difficult to do this, especially when you have multiple accounts on one service provider.

**Some people write passwords down**. In the better case scenario, they look for their passwords in a paper notebook stored on the shelf above their monitor. The worse idea connected to this approach is storing passwords in an unencrypted text file on your computer or even cloud storage (probably the worst way ever).

The best thing you can do is **use a password manager** as long as you do it right:

## Password Managers

Password managers are programs that encrypt and store your passwords. To decrypt them back and use them, you must insert the password to your passwords. Usually, they also contain a password generator so you don't have to come up with your own passwords. There's a variety of other features password managers provide like two-factor authentication or protection with a key file.

There is a nice list of password managers here: [List of password managers - Wikipedia, the free encyclopedia](https://en.wikipedia.org/wiki/List_of_password_managers).

### Can I trust a password manager?

#### A) What is going on under the hood?

Letting some third party software play around with my password and other sensitive data makes me feel really uncomfortable. That is the reason, in this particular case, why I prefer using open source software. Once the code is open to the broad audience, it is likely to have been checked by many people for malicious behavior or security bugs. If you don't trust the majority (which is amazing! Keep it so...), you can always check the code and compile it by yourself - or just ask a geeky friend to do it for you (and buy him/her a beer or two of course). This narrows the list to about a half.

> **DO** use an open source password manager!

#### B) Storing passwords on the cloud?

Another thing that I feel like people should be careful about is sending the password database (the encrypted file your password manager keeps the passwords in) to the cloud. This feature seems desirable - what if I accidentally drop my computer while climbing Mount Everest and loose all my data including the passwords? What if I want to access my passwords from my smartphone? Some passwords managers give you the possibility to use their cloud to store your encrypted passwords and access them from anywhere. But it means you give them full trust. What if a newly hired database admin chooses the path of the dark side, targets you as a victim, and runs a brute force script to decrypt your passwords file?

> **DO NOT** enable the manager's "store online" feature

> **DO** your own backups of the password database file!

... and [look here](https://www.pcworld.com/article/2936621/the-lastpass-security-breach-what-you-need-to-know-do-and-watch-out-for.html) how a popular online password manager failed to stay secure.

#### C) Browser integration?

Browser extensions (plugins) are basically HTML/javascript applications that may expose you to the dangers of many different hacker attacks. The same thing as with password managers themselves applies to browser extensions - implicitly, do not trust them, even though it may seem convenient to click an icon next to the address bar, log in and be able to manage your own credentials, for example from the browser on your friend's computer (question of logging in to accounts on devices you don't own is perhaps a topic for another post).

My advice:

> **DO NOT** rely on browser integration or browser-only password managers.

### How should I proceed then?

After all the do's and don’t's about how to use password managers, here is a scenario you can follow to meet enough security and accessibility requirements:

#### 1. Get a cross-platform password manager

Since you may want to access your account from anywhere, you should get a password manager that is compatible with Windows, Mac OS X, Linux, Android, iOS and all other platforms you may work on.

#### 2. Create your passwords database file

The first step that a password manager will do for you is create a password database file. Make sure you **know where it is saved** and how to **protect it with a very strong password**.

Come up with a long sentence and follow the rules of a strong password above. This is the **first of two passwords you will ever need to remember** (...in this scenario. I actually feel better remembering my super secure password to my primary email account too :) ). You will use it to access the password database file.

You can also have the password manager generate a key file for you that you can save to a USB flash drive. Don't forget to back up the flash drive too.

#### 3. Update your accounts with new passwords

Log in to every account and go through the "change password" procedure. Use the password that your new password manager generated for you for this particular account. Don't forget to click "save" in your password manager regularly (ctrl+s or cmd+s should do).

#### 4. Make your password file accessible

As said before, I wouldn't be providing my password file to the password manager's creator. They know exactly the encryption algorithm, and they know that the data that is sent to them are actually the password files.

Find a reliable and cross-platform compatible cloud storage provider that provides client-side encryption ([CloudWars.net - Top 10 Secure Dropbox Alternatives of 2016](https://www.cloudwards.net/top-10-secure-dropbox-alternatives/)) and set up your file sync clients on your devices. This is actually the second username/password combination you will need to know by heart to never lose your passwords. The great bonus is that with the client-side encryption, your passwords get double encrypted.

#### 5. Enjoy the safety!

From now on, you can authenticate with super secure passwords. Do always remember to take security seriously and apply other security layers like **two-factor authentication**. Always use **verified internet browsers** and don't forget to **upgrade to latest version regularly**. Make sure nobody is watching when you type your password, and when browsing the Internet, only insert your credentials to forms on websites that you trust and that support HTTPS, **verify validity of the SSL certificates on these sites** by checking the green lock symbol in the address bar and **never tell your passwords or answers to security questions to anyone**...
