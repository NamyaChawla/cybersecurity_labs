# What is Authentication?
verification, you are who you claim to be

# How is it different from Authorization?
Authorization verifies whether a person is allowed to perform a particular action or not

# Why does it matter?
Broken authentication allows attackers to either bypass the login entirely or take access to another user's account, giving them same level of access, alongside access to other person's information

# Factors of authentication
**Something you know** : password, security question
**Something you have** : key, card
**Something you are** : biometric

## Types of vulnerabilities
**Username Enumeration**
App responds differently when the username is valid/invalid
- different error messages: ('Username not found' / 'Incorrect password')
- different response time (takes longer time, if username exists)
for time variation, during a brute force attack, a trick is to use a very long password, this way, the correct username will take significantly longer time to respond, and will hence be differentiated easily
- different status code
Attacker first uses most popular, or the most plausible usernames to figure out a valid one and then brute forces a password for the same
- different response length: although this comes under the different error messages category, its crucial to look for this during a brute force attack
  
**Flawed Brute Force Protection**
- IP Blocked after n failed attemps : solution is to login using your own credentials every few attemps, this resets it automatically
or
we can use **header spoofing**: X-Forwarded-For: x
alongside the list of potential credentials, keep increasing x, use Pitchforce attack for this
- Account Locked flow : Similar to above, account gets locked, login with your own credentials after a few attempts
Some websites locks, but still tell you, password is incorrect, use this to figure out a valid username
**NOTE** :  Lockout applies to username and not your IP

**FLAW 1** [Account Enumeration via lockout]
If username gets locked: It was valid, otherwise, it wasn't
**FLAW 2** [Horizontal Brute Force to bypass account lock]
Instead of many passwords on one account, use some passwords on many accounts
This way, attempt limit per account in a particular time span does not exceed
**FLAW 3** [Credential Stuffing]
- use leaked passwords from real data breaches
- Dangerous because people reuse passwords accross sites

