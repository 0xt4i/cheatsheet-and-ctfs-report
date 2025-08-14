

Log in account and check function change email

![[Cross Site Request Forgery.png]]

So next step is i want to see if i can forge this request and then log in as another user as `carlos` and submit that forged request  and see if the web application accepts it, then that mean the web application is not verifying the user that are submitting the request because i'm creating the request as `wiener` and submitting it as `carlos`, but the application is still accepting it and still applying our changes. Therefore, if i am able to get `carlos` to change their email to an email that i control such as my email, i can go ahead and use the password recovery functionality that is available on most websites and recover their password, and then i'll be able to gain full control over their account.

So before we create forged requests, let's first clone this form and replicate its functionality in a simple HTML file. Access inspect on website, the html code that is responsible for rendering this page.

![[Cross Site Request Forgery-1.png]]

I can see the element that is relevant to the code that i'm selecting is being highlighted on the website.

![[Cross Site Request Forgery-2.png]]

Then copy element

![[Cross Site Request Forgery-3.png]]

Then i use paste to empty text file and give full location for `action` 
```html
<form class="login-form" name="change-email-form" action="https://0a0b00b7042ae85f80d3035d00640083.web-security-academy.net//my-account/change-email" method="POST`">
    <label>Email</label>
    <input required="" type="email" name="email" value="">
    <input required="" type="hidden" name="csrf" value="VdaGaRhjA2WRKLX5ypsRBxM0Q64cSGHK">
    <button class="button" type="submit"> Update email </button>
</form>
```

So click to file `csrf.html` to see website

![[Cross Site Request Forgery-4.png]]

Now, i try to discover a client side request, forgegy, vulnerability but to test if this request will be accepted by the web application i need to log out from this user, log in as the other user, and then submit the form and see if the web application is going to accept this request that we created as user number one, if it does,  then the target is vulnerable for a client side request forgery

Now i will log out `wiener` account and log in `carlos`

![[Cross Site Request Forgery-5.png]]

I will update email in right screen if left screen also change email that is bug.