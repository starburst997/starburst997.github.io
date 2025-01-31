---
layout: post
title:  "LinkedIn verification with ClearMe Hell"
date:   2025-01-30 18:00:00 -0400
categories: post
published: false
---

<center class="images">
  <a href="/assets/posts/2025-01-30-linkedin-verification/linkedin.png" target="_blank"><img src="/assets/posts/2025-01-30-linkedin-verification/linkedin.png" alt="Crappy shit" height="200"/></a>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
  <a href="/assets/posts/2025-01-30-linkedin-verification/clearme.png" target="_blank"><img src="/assets/posts/2025-01-30-linkedin-verification/clearme.png" alt="Even crappier shit" height="200"/></a>
</center>

I was updating my [LinkedIn](https://www.linkedin.com/in/jeandenisboivin) recently and the verification thingy banner was bugging me, so I caved in and tried to verify to no avail.

<center class="images borders">
  <a href="/assets/posts/2025-01-30-linkedin-verification/fail.png" target="_blank"><img src="/assets/posts/2025-01-30-linkedin-verification/fail.png" alt="We weren't able to verify your identity" height="200"/></a>
</center>

No matter what I tried, browsers, apps, etc. Right after taking the selfie, the page will greet me with **"We weren't able to verify your identity"**.

I then remembered that I tried ages ago and was stuck on this error with no recourse. Some quick google search shows that I am not alone and plenty of people have the same issues. *What a joke* that those companies that employs hundreds (thousands?) of developers cannot get some simple verification process to works for years...

I hate those scenarios because I just cannot give up on stupidity like this and have to find a way to get past their incompetence.

<br/>

## Investigating the error

Turns out that for some reason my account was stuck with an error.

(**442** on */api/enroll/v1/assurance/steps*)

```
"UserCannotBeAssured: User Cannot Be Assured"
```

Whatever that means...

<br/>

## Solution

<center class="images borders">
  <a href="/assets/posts/2025-01-30-linkedin-verification/form.png" target="_blank"><img src="/assets/posts/2025-01-30-linkedin-verification/form.png" alt="Right to deletion" height="200"/></a>
</center>

The privacy laws, with all their headaches, at least comes useful in this instance as we can request the account to be deleted and starts from fresh!

While visiting the [Privacy Policy](https://www.clearme.com/privacy-policy) page, you can get access to a page where you can fill a form to request all of your data to be deleted (look for *"4. Your privacy rights with CLEAR"*).

Visit the [Right to Deletion](https://my.clearme.com/CommunityPrivacyRequest) page and fill the forms. You'll receive an email you need to confirm and a SMS code.

After a few hours, you should get another email telling you that the data have been deleted.

<br/>

## Trying again

Lo and behod, trying the verification process again finally allowed me to get past the selfie!

*(btw the selfie portion is horrendus, their auto-detect feature snap your picture at the worst moment and you need to be extra careful and quick)*

However, the cheers were shortlived as no matter what I tried, my driving license (back) wouldn't get recognized. I then made the mistake of trying with my canadian passport and was met with the **same dreadful error** with no way to get around but to request my account to be deleted again...

Second time, I was very careful, made sure to use good lighting, only use my driver license, used a device with better camera and carefully wait for the auto-detect to works.

<br/>

## Conclusion

If you get stuck in a loop with an error in any kind of service because of their incompetence (and with terrible customer support), try to request that they delete all of your data so you get a chance to try again fresh and hopefully succeed by having all the stars aligned this time.

Knowing that Microsoft owns LinkedIn and that their [verification process for code signing](/post/2025/01/29/code-signing.html) was such a breeze compared to ClearMe, I hope they ditch them and use [that system instead](https://www.au10tix.com/).

<br/>