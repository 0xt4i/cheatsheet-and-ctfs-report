## Cookie  Manipulation
LAB: [Lab: User role controlled by request parameter | Web Security Academy](https://portswigger.net/web-security/access-control/lab-user-role-controlled-by-request-parameter)

## Accessing Private User Data
[Lab: User ID controlled by request parameter with password disclosure | Web Security Academy](https://portswigger.net/web-security/access-control/lab-user-id-controlled-by-request-parameter-with-password-disclosure)

## Insecure Direct Object Reference
[Lab: Insecure direct object references | Web Security Academy](https://portswigger.net/web-security/access-control/lab-insecure-direct-object-references)

It happen when the application directly accesses user data without verification based on user input.

### Recon
Because don't have account so cann't access my acount. I turned to test live chat.

![[3.1.png]]

Then use and click view transcript, it will download file txt 

![[3.2.png]]

Notice why it is not 1, but 2.

So use Burp Suite for intercepting and modifying HTTP requests and responses.

when click view transcript, create messange with path `/download-transcript/4`

![[3.3.png]]

So i want to download file`1.txt`

![[3.4.png]]

![[3.5.png]]

Now, can see password.

#  Privilege Escalation with Burp Repeater
[Lab: User role can be modified in user profile | Web Security Academy](https://portswigger.net/web-security/access-control/lab-user-role-can-be-modified-in-user-profile)

Use Burp Repeater 
![[3.6.png]]

![[3.7.png]]

When click to send and you will be able to see the response that this request triggered right at the bottom. Therefore you can modify this request.

Now we send a post request to path `/my-account/change-email` and value new email `tai3@gmail.com` 

- Get a response of `300` which is a redirection response
- The Apikey is `zQsvxoabm34cBLR5KFxmyfgoQ96W7CTq` but this is our api key so this is not really a information leak.
- Also see the roleid `1` so this is probably my permissions on this website or my role on this website.

Now when I send a request to change the email, I also send a request to change the role ID at the same time.

# Debugging flows with http trace & Gaining Admin Access
[Lab: Authentication bypass via information disclosure | Web Security Academy](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-authentication-bypass)

This is show the http trace and see how useful it is in understanding how the target application works and how it can help us with exploiting it and discovering bugs within it.

When access page and my experence that the bug is path admin page so if go to admin page  can see 

![[3.8.png]]

An information air message saying the interface is only available to local user, Even if you didn't get an information error message like this and even ask you for a username and a password that should trigger your curiosity or your bug hunter

how is it knowing that i'm not the admin and it's not letting me see anying to try to understand how this page works. We're going to turn on our interceptor.

Use burp suite for see simple get request to the path that we are requesting which is the admin 

![[3.9.png]]

We will look at the body parameters in here and there is nothing as well. So there's nothing that is being send that would allow do have application to know that i'm not admin and to know that it shouldn't even show me login boxes in such scenarios.

I'd usually forward the packet to see if there is more data being sent and sure enough there is more data being sent but again if you analyze this data you will not see anything useful

In that see the next thing i would do is try to send the same request but instead of get so let me just send it now. Instead of geting here i would type trace

![[3.10.png]]

The trace method is really useful in such scenarios because it help us debug or understand how the target web application works.
Because when we send a trace to the target web server the target web server will response by simply sending, us back the request that we sent to it. This is very usefull because it lets us understand whether the data that we sent to the server got modified at some stage 

we're sending a trace request to the admin and download text file 

![[Broken Access Control  Vulnerabilities.png]]
I will notice here at the bottom we actually have and extra header call `X-Custom-IP-Authorization` 

The original request we sent contained all the necessary data, but somehow, along the way, this particular header was modified and added to the target web server. The IP you see here is actually my IP. Upon reviewing this and adopting a hacker or bug hunter mindset, you can see that at some point, this header was added, along with your own, which caused the admin page to not load. Therefore, it’s likely that the admin page is not loading for you either. Because the web server knows that you're not admin.

So you should either try to put the admin or you should simply replace this with a reference to the local ip, 127.0.0.1 is one way of referencing the local host.
What we're trying to do is we're trying to send this header and saying that we are a local user on the target computer.

Copy `X-Custom-IP-Authorization: 127.0.0.1` and use the match and replace feature

![[3.11.png]]

![[3.12.png]]

![[3.13.png]]

Simultaneously turn off intercept


![[3.14.png]]

And finally, reload page admin and can see page.

![[3.15.png]]

# Directory / Path Traversal
[Lab: File path traversal, traversal sequences blocked with absolute path bypass | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-absolute-path-bypass)

When install a web server on a server or a computer, you basically define a certain path where the website files will be stores that path is usually `/var/www/` or `/var/www/html`
So the main idea is you would define a path within your file system, it contains the files of the website and when users access to website they get served files that are included in that directory.
So this directory would be called a web root
> directory this can actually be anywhere it doesn't have to be in this path but this is one of the default paths where the web root.

Directory or path traversal vulnerabilies allow us to break out of this path and load files from other locations on the server or maybe even load files within this server but you're not allowed to see so maybe load source code or sensitive files that not directly visible through the url bar through basically just putting the website location.

![[3.16.png]]

Should test every single functionality. We going  to go ahead and load a product and i as can see i get a product image to test everything whatever parameters you have in the url.

Turn on intercept of Burp Suite 
![[3.17.png]]

Continue forward and we have else get and focus this can modify the data `56.jpg` 

![[3.18.png]]

I will notice that you have a request that is being sent to an endpoint called `image`

Use repeater of Burp Suite

![[3.19.png]]

 So i try to load the file on the target system but that is outside my web root, somewhere from home example `/etc/passwd`. because should access file exists on Linux, also `/home` require username.
![[3.19-1.png]]

Show response

![[3.20.png]]

# By passing  Absolute Path  Restriction
[Lab: File path traversal, simple case | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-simple)

![[3.20-1.png]]

Similar to the above, maybe vulnerabilities in `/image?filename=63.jpg` 

Redirect repeater in burp suite

![[Bug-Bounty Hunting & Web Security Testing/attachments/3.png]]

Modify `63.jpg` become `/etc/passwd` and send this request then receive

![[3-1.png]]

So I can see an error saying no such file because i will probably just get a 400.
Maybe tree path of system display
```text
/
|-- /var -- /www -- /html /images
|-- /home
|-- /etc
```

So if I want to move `/etc/passwd`, I must go to the root directory `/`, which is the main directory.

I try `../etc/passwd` or `../../../etc/passwd` 

![[3-2.png]]

![[3-3.png]]

Well, i can see content file `/etc/passwd` 

![[3-4.png]]

# Bypassing Hard-coded Extensions
[Lab: File path traversal, validation of file extension with null byte bypass | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass)

This another example where in a real life scenario i might think the target is not vulnerable because i try the exploit that we showed earlier, they're not going to work because the target is using some kind of filtering, it is a little bit more restrictive. But i still going to manage to load paths beyond our limits and exploit the vulnerability.

The lab is similar to the one before and check path `../../../../etc/passwd` with request for read content `/etc/passwd`.

![[3-5.png]]

So at this stage i might think that the target is not vulnerable and move on but there is still so much that you should try.
I thinking maybe there's some filtering, maybe some restriction in there that is preventing us from loading this file 
From original request that was being sent, can see it's asking for an image that ends with a`.jpg`. The target web application is expecting whatever i load in here to be an image, not a file. 
So the problem is if i do `.jpg` in `/etc/passwd.jpg`, there is no such file that is called `passwd.jpg`, therefore that's not good to me. 
But pass filter if that was the case `null byte` like `%00` because in program language as `C`,`C++` It will terminate as soon as it encounters a null byte and ignore the rest.

![[3-6.png]]

# Bypassing Filtering
[Lab: File path traversal, traversal sequences stripped non-recursively | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-sequences-stripped-non-recursively)
The other small trick that will allow to discover them. Maybe make me thinking the target is not vulnerable if come across one.

Use `....//....//....//....//etc/passwd` because some security fillter only block the exact pattern `../`, but not block `....//`. Besides, `....//` can still be normalized into `../..` or similar `../../` reconnected for bypass filtering.

![[3-7.png]]

# Bypassing Hard-coded Paths
[Lab: File path traversal, validation of start of path | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-validate-start-of-path)

Another example of a directory or a path traversal as can see there are vert simple vulnerabilities and as i said. So your chance of discovering them are very high.

![[3-8.png]]
Similar to the above examples, but with a slightly different path `/var/www/images/5.jpg`. `/var` is default directory that you have on linux systems, so right now we're actually passing a full time to the web application, this is very strong indication that the target might be vulnerable.
I will simply go back three directories because have `images`, `www`, `var` to the root directory. The path have structure like `/var/www/images/../../../etc/passwd`. Notice, not modifying any of `/var/www/images`.

![[3-9.png]]


# Bypass Advanced Filtering
[Lab: File path traversal, traversal sequences stripped with superfluous URL-decode | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)

This example is other payload for request to bypass filtering.
When web application load and therefore we have strong a indication that the target might be vulnerable to a path reversal or a directory traversal vulnerability.
If you’ve tried the above methods and still get 'no such file', then it’s still not sufficient.
So i am going to url encode the special characters this is the forward slash `/` 
![[3-10.png]]

Choose `URL-encode all characters` 

![[3-12.png]]

The characters `/` become `%2f` 
![[3-15.png]]

The reason why i am writing it `/` to `%2f` because i am assuming that the target web application has some kind of a firewall or filter that is checking if the value sent to it contains a forward slash `/` and if it does it's denying our request or maybe it's removing it therefore i am trying to pass a `/` to do application differently so that it bypasses the filter but still gets executed as a forward slash. So i will use path as `..%2f..%2f..%2f..%2fetc%2fpasswd`


![[3-14.png]]

I sent request and still doesn't work so at this stage i am going to be like well definitely this is just not vulnerable and i am going move on but i should try harder because the targets firewall might be smart and maybe it's actually checking if the input is being url encoded.

So basily the firewall might be taking your input and basically doing transform it back to original form to our`/` and then it's checking does the input contain a `/` and it does denying me from accessing  the file because it know that i am trying  to do something malicious on the target.

So i am going to do url encode our input after forward slash encoded, it mean encode `/` to %2f, then encode `%2f` become `%25%32%66` 

![[Broken Access Control  Vulnerabilities-3.png]]

# Bypassing Extreme Filtering
[Lab: File path traversal, traversal sequences stripped with superfluous URL-decode | Web Security Academy](https://portswigger.net/web-security/file-path-traversal/lab-superfluous-url-decode)

Now provide a cheat sheet that contains a large number of payloads that you could use again to bypass and discover these vulnerabilities of bugs and that might actually make you think might actually make you think that pen testing is very boring because if you really are serious about discovering bugs and discovering vulnerabilities. You're actually going to have to do a lot of trial and error, send stuff like this over and over again in order to discover bugs in your target. But luckily there are tools out there that can make this process more enjoyable so that you can focus on the front part which is discovering or spotting the weaknesses and then letting these tools automatically send all of these request for us.

Use the `directory-traversal-cheatsheet` file to automate path modifications in requests, making manual alterations more difficult. Navigation bar Intruder

![[Broken Access Control  Vulnerabilities-6.png]]

I am relying on our experience in analyzing the endpoint and its input to spot a weak request, weak input, or potentially vulnerable input and once we're at this stage we could automate the task of sending all in file `directory-traversal-cheatsheet` . The intruder allow not manually define which parts of the request want test.

Tell Burp in the positions location what to test in this request that is `70.jpg` is vulnerable input, so hightlight it and add sign

![[Broken Access Control  Vulnerabilities-7.png]]

Copy the contents of `directory-traversal-cheatsheet` and paste them into the Payload Configuration field.

![[Broken Access Control  Vulnerabilities-8.png]]

So i have 180 values that need to be tested.

Click start attack to send all payload.

![[Broken Access Control  Vulnerabilities-9.png]]

And I get the results quickly.

![[Broken Access Control  Vulnerabilities-10.png]]


