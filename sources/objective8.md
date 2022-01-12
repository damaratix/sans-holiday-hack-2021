(obj8)=
8\. Kerberoasting
=======================
**Difficulty:** 🎄🎄🎄🎄🎄 <br>

This objective consists of:
1. **Prechallenge:** this gives us hints to complete the actual challenge. We have to play with some [**Fail2Ban rules**](prech8)
    * Location: **Santa's Office**
    * Reference <span style="color:red">**ELf**</span>: **Eve Snowshoes**
2. **Challange:** this is the actual challenge, [**Kerberoasting on an Open Fire**](ch8)
    * Location: **Santa's Office**
    * Reference <span style="color:red">**ELf**</span>: **Eve Snowshoes**

+++
<br>

There is a great [**talk**](https://www.youtube.com/watch?v=Fwv2-uV6e5I) by Andy Smith about `Fail2Ban`

<br>

If you manage to block the malicious IPs in the HoHo..No terminal you will receive these hints
```{hint}
1. [CeWL](https://github.com/digininja/CeWL) can generate some great wordlists from website, but it will ignore digits in terms by default.
2. [OneRuleToRuleThemAll.rule](https://github.com/NotSoSecure/password_cracking_rules) is great for mangling when a password dictionary isn't enough.
3. There will be some `10.X.X.X` networks in your routing tables that may be interesting. Also, consider adding **-PS22,445** to your `nmap` scans to "fix" default probing for unprivileged scans.
4. Check out [Chris Davis' talk](https://www.youtube.com/watch?v=iMh8FTzepU4) and [scripts](https://github.com/chrisjd20/hhc21_powershell_snippets) on Kerberoasting and Active Directory permissions abuse.
5. Administrators often store credentials in scripts. These can be coopted by an attacker for other purposes!
6. Learn about [Kerberoasting](https://gist.github.com/TarlogicSecurity/2f221924fef8c14a1d8e29f3cb5c5c4a) to leverage domain credentials to get usernames and crackable hashes for service accounts.
7. Investigating Active Directory errors is harder without [Bloodhound](https://github.com/BloodHoundAD/BloodHound), but there are [native](https://social.technet.microsoft.com/Forums/en-US/df3bfd33-c070-4a9c-be98-c4da6e591a0a/forum-faq-using-powershell-to-assign-permissions-on-active-directory-objects?forum=winserverpowershell) [methods](https://www.specterops.io/assets/resources/an_ace_up_the_sleeve.pdf).
```

<br>
<br>

![footer1](images/footer1_large.png)


