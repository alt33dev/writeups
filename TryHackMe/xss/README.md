# Cross Site Scripting - A Walkthrough
This room is available on [TryHackMe.com](https://tryhackme.com/room/xss)

![Cross-Site Scripting](images/title.png)
## TASK 1
Just read it and hit the button!



## TASK 2
Deploy the box, read the text, hit the button. Done.
It says, `You do not need to be connected to our network to deploy and access this.`
I have no idea why it says that or what it means. You most definitely do have to be connected to the network to access the machine, so just ignore this.



## TASK 3 -- STORED XSS
### Question 1
No answer needed, but you might want to create an account on this XSS website we just deployed. I used `user` for the username and `pass` for the password. Yeah, I know, that's some real out-of-the-box thinking.

### Question 2
Make sure you have your account created, and you are logged in. Click the `Stored XSS` link at the top of the page.
Don't overcomplicate this. The question is just asking if you could possibly insert any HTML tags in the input box. You don't have to be clever and actually add some HTML to the DOM using some complicated code or anything fancy. Just enter some HTML in the input and click the button. For example, you could just enter a paragraph tag: `<p>HEY, it's HTML!!</p>` and be done with it. Just about any HTML tag should do the trick here. You should get a flag that shows up in a green box.

### Question 3
Pretty simple here, too. We just need to enter something as a comment that will make a popup box appear on the page with the content of our cookie when the page loads. JavaScript to the rescue!! It is cross-site *scripting*, after all.
So, let's just try doing `<script>alert(document.cookie)</script>`
We should see the contents of our cookie in an alert box, then as soon as we close that, another alert should pop up with our flag. YAY!

### Question 4
Still simple, but has a couple steps. First, we need to look at the HTML source and see where that text we are supposed to change is, and figure out how to change it using some JavaScript.
OK, so we found it. Conveniently, it is surrounded by a span tag with an id attribute. That makes it super easy to change with our script. We can use the getElementById method of the document and the innerHTML property of that element to change the contents of that span.
```
<script>document.getElementById('thm-title').innerHTML="I am a hacker"</script>
```
When we submit this, we are going to see the popups from the previous question because this is *stored* XSS. The things we do will show up on every subsequent load of the page. If we have done this one correctly, you should see your answer in red text next to the number 3 instructions on the XSS playground page.

### Question 5
OK, now we are getting somewhere. Remember when we just displayed the contents of our cookie in an alert? That was pretty useless because it's *OUR* cookie. What if we want some other user's cookie so we could be seen as them on the system? We can do that!!
In a real-world situation, we would set up a web server that would capture the requests that the comment we are about to put on the site is going to cause every user's browser to make. In this room, however, there is an easier method. Every request sent to /log/some_value_here will enter `some_value_here` to the logs which can be viewed on the /logs page. Super!!
We need to be a bit careful, here. If we do what is suggested and modify the location, then every time we go to the page from now on it will try to forward us to that location. It's would also be a bit too obvious to the other users that something strange is going on.
Let's just do an img tag instead.
```
<img src="javascript:'/log/' + document.cookie" />
```
This will steal the user's cookie and send it to the logs page.

### Question 6
Now that we have Jack's cookie, we can become Jack!  We just copy it and replace our cookie with Jack's cookie value, refresh the page, and enter a new comment. If we did everything correctly, we will see a new comment from Jack and our new answer flag will show up on the page, too!!



## TASK 4 -- REFLECTED XSS
I would have liked to have seen this task as number 3, and move the slightly more complicated Stored XSS to this task, but it is what it is.
To start, click the Reflected XSS link at the top of the XSS Playground page.
This time, we are interacting with a search feature. Let's see what it does. Enter some text in the search box -- I used `blahblahblah`. When we hit the Search button we see the text `You searched for: blahblahblah` Behavior like this is a good clue that the page might be vulnerable to reflected XSS.
The tasks here are simple, so let's get to it!

### Question 1
This is very similar to question 3 in the previous task. We just need to make an alert appear on the page. This should do the trick:
```
<script>alert("Hello")</script>
```
You should see your popup that says Hello, and then another with your answer flag. 
This looks almost the same as the previous task, but there's an important difference. This time, our script isn't being stored anywhere, so the next time we visit this page it is *NOT* going to run our script. That is the main difference between Stored XSS and Reflected XSS.

### Question 2
Same deal here, just displaying different stuff. Let's do this!!
It's easy to overthink this, especially if you read the question the way I did. I assumed that I was supposed to get the *client* IP address, but that is not the case here. I'm not sure what value there would be to using javascript to get the IP of the server, but we can do it if that's what the task requires. Here we go.
```
<script>alert(window.location.hostname)</script>
```
Technically, this returns the hostname of the server. It just happens in this case that there is no host *name* in this URL, so it returns the IP address as the hostname.



## TASK 5 -- 
