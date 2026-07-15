# Neighbour

Check out our new cloud service, Authentication Anywhere. Can you find other user's secrets?

## Solution

Check out our new cloud service, Authentication Anywhere -- log in from anywhere you would like! Users can enter their username and password, for a totally secure login process! You definitely wouldn't be able to find any secrets that other people have in their profile, right?

So here we are given a website which can be accessed after starting the machine ` http://<Machine-ip>`

So I first started of by visting the url after the machine became accessible. The website consisted of the following login page:

![login page](../IMAGES/Neighbour%20CTF/website%20login%20page.png)

In order to login we are also given a hint to view the page source for the guest credentials, so lets inspect the source code of login page to see if this hint can get us credentials for the guest user or any other important clue:

![guest creds](../IMAGES/Neighbour%20CTF/login%20page%20guest%20user%20creds.png)

So we can see here that the credentials for the guest profile are `guest:guest`. Also there is another hint that says that `"admin" user account is off limits!!!!!`. Maybe this will be helpful later on after we login.

So lets first login through the guest credentials we found to explore more about the given target website:

![guest dashboard](../IMAGES/Neighbour%20CTF/guest%20logged%20in.png)

So we are greeted on the guest profile dashboard with some text and a logout button. The page says that `Try not to peep your neighbours profile`. Lets examine its source code as well to see if we could make sense of this more:

![source code](../IMAGES/Neighbour%20CTF/guest%20dashboard%20source%20code.png)

Here we can see a comment stating that admin account could be vulnerable. There is nothing more to discover here so lets switch back to the guest dashboard to see if there is anything interesting there that we missed.

![guest profile](../IMAGES/Neighbour%20CTF/guest%20logged%20in.png)

If we could observe that how the url is constructed we can maybe tweak its parameter to get some different result ?

The url for the guest profile is: `<Machine-ip>/profile.php?user=guest`. 

Based on the previous two hints about admin profile we can try to replace the `user=guest` to `user=admin` to see if it could return us some different webpage:

![admin](../IMAGES/Neighbour%20CTF/admin%20dashboard.png)

So this change really worked !! So, the given website is vulnerable to `Indirect Object Reference (IDOR)` that allows a user to view information related to other users.

----