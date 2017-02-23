# from github to git-ssb: a guide to hacking together on the distributed web

you came to the right place! git-ssb is a great fit for just this. this guide
tries to act as a transition document for github (or vanilla git) users into the
world of git-ssb. it will walk you through:

1. understanding what "ssb" is
2. installing scuttlebot, the ssb peer server, and joining the network
3. installing the `git-ssb` command and the `git-ssb-web` web interface
4. a walkthrough of using git-ssb to do common github workflows (creating repos,
   making pull requests, merging pull requests, issues, etc)


## enter ssb

what is ssb?

ssb is [secure scuttlebutt](https://github.com/ssbc/secure-scuttlebutt) the
protocol that powers the *peer-to-peer log store*,
[scuttlebot](http://scuttlebot.io). there is a great deal of information to be
found by reading these sites, if you're so inclined.

if you're familiar with bitcoin or blockchains, ssb is kind of like having your
own personal blockchain: you can append messages of any kind, with any data
you'd like. and, unlike bitcoin, there's no money involved: it's just a data
structure on your local machine.

your personal log can only be appended to, and is cryptographically secure: each
message references the hash of the message that came before it. the whole thing
is then also signed by your public key, making it both tamper-proof, and
appendable only to the private key holder (you).

the final piece of ssb that makes it all work is the gossip protocol: when you
run scuttlebot, you become a peer in a network of other scuttlebot users. you
can choose to 'follow' other people's personal logs. as a result, whenever you
see that user (or any user that follows that user too) online, you can retrieve
their latest log entries from them. all without any central server or central
authentication.

[git-ssb](http://gitmx.com/%25n92DiQh7ietE%2BR%2BX%2FI403LQoyf2DtR3WQfCkDKlheQU%3D.sha256)
builds on top of this: things like commits, branches, issues, and pull requests
are modeled as log entries on each participant's personal log, while the gossip
protocol runs in the background and propagates new content to everyone involved
in the git repository.


## installing scuttlebot

first you'll need to install [node and npm](https://nodejs.org).

once you've done that, follow [these
instructions](https://ssbc.github.io/patchwork/) to install Patchwork, a nice
front-end that also packages a Scuttlebot server. this involves getting an
"invite" to the scuttlebot network. this isn't a strict requirement, but an
invite means you'll have other peers willing to share your log around the
network, which is necessary for git-ssb to be useful. stop by #ssb on Freenode
via IRC and ask for an invite -- there are many folks who are happy to provide
them.


## installing git-ssb

[git-ssb](http://gitmx.com/%25n92DiQh7ietE%2BR%2BX%2FI403LQoyf2DtR3WQfCkDKlheQU%3D.sha256)
is primarily the work of
[cel](http://gitmx.com/%40f%2F6sQ6d2CMxRUhLpspgGIulDxDCwYD7DzFzPNr7u5AU%3D.ed25519),
and comes in the form of a command-line program not unlike `git` or github's
[hub](https://github.com/github/hub).

let's use `npm` to install the `git-ssb` command:

```
$ npm install --global git-ssb
```

you can run `git-ssb` (or `git ssb`) from the command line now and get a feel
for the sorts of things it can do


## git-ssb web

if you've already joined the ssb network successfully, you can take a look at
what other hackers on ssb are working on! run `git-ssb web` and point your web
browser at the URL it outputs.

![git-ssb web screenshot](http://gitmx.com/%25q5d5Du%2B9WkaSdjc8aJPZm%2BjMrqgo0tmfR%2BRcX5ZZ6H4%3D.sha256/raw/b3fa523aaaf02c61131da1d45a8f1b1174e4ef5e/static/screenshot-user-activity.png)

you'll see that it's not dissimilar from github:
[git-ssb-web](http://gitmx.com/%25q5d5Du%2B9WkaSdjc8aJPZm%2BjMrqgo0tmfR%2BRcX5ZZ6H4%3D.sha256)
lets you see commit activity, view repositories, browse commit history, see
issues and pull requests. all without a centralized authority.

there are at least a few public git-ssb web servers run by the community, for
browsing git-ssb content without running your own scuttlebot:
- http://gitmx.com
- http://ssb.celehner.com
- http://git.mixmix.io


## creating an ssb git remote

git-ssb makes scuttlebot act like a git remote. navigate to an existing git
repository of yours and run

```
$ git-ssb create ssb
```

this will create a new git repo on ssb. the command will output something that
looks like

```
Created repo: ssb://%DEvlJYD+zuudMyNWFBjiiNvZ8DbOyBYhCkE5EVtWSV0=.sha256
(my-little-repo)
```

the string `%DEvlJYD+zuudMyNWFBjiiNvZ8DbOyBYhCkE5EVtWSV0=.sha256` is a hash that
uniquely identifies your git-ssb repository. to see this in action, you can
navigate to `/tmp` and run

```
$ cd /tmp

$ git clone ssb://%DEvlJYD+zuudMyNWFBjiiNvZ8DbOyBYhCkE5EVtWSV0=.sha256 my-repo
```

and you'll see that `/tmp/my-repo` now exists. git-ssb talked to your local
scuttlebot server and pulled down the entire git repository.

in fact, you can also view your handiwork by running `git-ssb web` and opening
your browser: you'll see your brand new repo near the top of the activity list
and can browse it like you might've on github.


## collaboration: forks and pull requests

if you're reading this article, then you've already found one ssb git
repository: this one! let's create a fork, make some changes, and send a pull
request:

### clone

```
$ cd /tmp

$ git clone ssb://%RPKzL382v2fAia5HuDNHD5kkFdlP7bGvXQApSXqOBwc=.sha256 git-ssb-intro

$ cd git-ssb-intro
```

### fork

let's look at the help for `git-ssb fork`:

```
Usage: git ssb fork [<upstream>] <remote_name>

  Create a new git-ssb repo as a fork of another repo
  and add it as a git remote

Arguments:
  upstream      id, url, or git remote name of the repo to fork.
                default: 'origin' or 'ssb'
  remote_name   Name for the new remote
```

since we just did a fresh git clone, the remote to the original repo is
`origin`; you can type `git remote -v` and have a look. since `origin` (or
`ssb`) are the default for forks, you can omit this option.

`remote_name` is whatever you'd like to call the remote of your fork. I usually
call mine `noffle-ssb`, to indicate that it's a fork remote and that it's my
personal one:

```
$ git-ssb fork noffle-ssb
Created repo: ssb://%DKWLuN05qtwjdwTG7zCG4aU12muBK2pkpYgaBbeVkTM=.sha256 (git-ssb-intro)
Added remote: noffle-ssb
```

great! now we can make some changes and commit them. I forgot to add a license
to this document, so we can add that, commit, and push:

```
$ cat >> README.md

# license

ISC
^D

$ git commit -am 'adds license'
[master 3a7c5f7] adds license
 1 file changed, 4 insertions(+)

$ git push noffle-ssb
Counting objects: 3, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 2.55 KiB | 0 bytes/s, done.
Total 3 (delta 0), reused 0 (delta 0)
Decoding objects: 100% (3/3)
Pushing blobs...pwMeF/zIvuwFc4/T3VBwpHr+r6H+dHvWKFyIxCs1+08=.sha256
Published %i0Fcr3zRdf50Tin1UdEmYU38eKWthmkO3XgpfMYU1AU=.sha256ha256
To ssb://%DKWLuN05qtwjdwTG7zCG4aU12muBK2pkpYgaBbeVkTM=.sha256
   6a701c2..3a7c5f7  master -> master
```

we can see our fork on the web interface, too! navigate to
`http://localhost:7718` and you'll see your repo fork.

let's finally create a pull request with our changes:

```
$ git-ssb pull-request
```

this will launch your text editor and let you type in a message. it's good
practice to make the first line of your message the subject, and leave two
newlines before writing your message. something like

```
test pull request

please merge me
```

sure enough, you can now navigate to the `from-github-to-git-ssb` repo on ssb
and see your pull request:
http://localhost:7718/%25RPKzL382v2fAia5HuDNHD5kkFdlP7bGvXQApSXqOBwc%3D.sha256/pulls

if you were the owner of the git repo, you could merge this pull request from
the command line by pulling down the fork remote and merging:

```
git fetch ssb://%RPKzL382v2fAia5HuDNHD5kkFdlP7bGvXQApSXqOBwc=.sha256 noffle-ssb
git merge noffle-ssb
git push ssb
```

## go forth and collaborate

well done, and welcome to the scuttleverse and the world of distributed social
coding! share git-ssb with your friends, and consider managing an ssb remote of
your github work, to encourage further growth of the distributed web.

thanks for reading! please share feedback or questions as issues on this repo,
or, if you find any errors in the text, send a pull request.

