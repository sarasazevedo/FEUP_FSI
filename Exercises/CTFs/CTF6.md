# CTF 6

First, we analysed the main page of the website. We noticed that there was an ID for the request that is generated periodically and an HTML input form.

We tried writing something in the input to see what the website would do and to figure out our next step.

![Alt text](/Exercises/images/CTF6-1.png)

We concluded that we should be able to inject malicious scripts in the input to obtain the flag. However, we needed more information at the time. So, we started to explore the page that was redirected by the "page" link.

We noticed that we have access to the admin page, where it evaluates the justification and decides if it gives the flag or not.

![Alt text](/Exercises/images/CTF6-2.png)

We inspected the HTML code of the website, and we noticed that the buttons were disabled. We also noticed that it redirects to the API that deals with the request, using the generated ID.

So, we just needed to inject a script that generates the form, but will be enabled and also simulate the click.

This is our malicious script:

```html
<form method="POST" action="http://ctf-fsi.fe.up.pt:5005/request/<id-of-the-request>/approve" role="form">     
    <div class="submit">         
        <input type="submit" id="giveflag" value="Give the flag">    
    </div> 
</form>  

<script type="text/javascript">     
    document.querySelector('#giveflag').click(); 
</script>
```

We injected this code, and put the ID of the request given in the main page and clicked on the submit button:

![Alt text](/Exercises/images/CTF6-4.png)

We've also had to disable the javascript extensions in order to complete the attack:

![Alt text](/Exercises/images/CTF6-3.png)

Now, we just needed to refresh the page to obtain the flag:

![Alt text](/Exercises/images/CTF6-5.png)

![Alt text](/images/CTF6-6.png)
