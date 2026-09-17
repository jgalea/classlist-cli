<div align="center">

# classlist

[![License](https://img.shields.io/badge/LICENSE-MIT-5C9E31?style=for-the-badge)](LICENSE)
[![Python](https://img.shields.io/badge/PYTHON-3.9%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org)
[![Built by](https://img.shields.io/badge/BUILT%20BY-JGALEA-8A2BE2?style=for-the-badge&logo=github&logoColor=white)](https://github.com/jgalea)

**Read your school's Classlist account from your computer instead of the app: the parent directory, class groups, posts, events and messages.**

</div>

Classlist is the app a lot of schools use for parent-to-parent messaging, class lists and events. It works fine, but everything lives inside the app, which means scrolling to find the thing you half remember reading. This tool puts the same information in a terminal window, where you can search it, read a whole class group at once, or check what is coming up without opening anything.

Almost everything it does is reading. The one command that writes is `classlist post`, which asks you to confirm before anything is sent, and nothing else in the tool replies, RSVPs or changes your account.

This is an unofficial tool, not made by or connected to Classlist.

## What you need

A Mac or a Linux computer. macOS already has everything required, so there is nothing to install first. Windows is not tested.

You also need a Classlist account you already log into, with the email and password you normally use. If your school uses a different system, this will not help.

## Installing it

Open the Terminal app. On a Mac, press Command and Space together, type Terminal, and press Enter. A window with a text prompt appears. Copy each block below, paste it in, and press Enter.

First, download the tool:

```
mkdir -p ~/.local/bin
curl -fsSL https://raw.githubusercontent.com/jgalea/classlist-cli/main/classlist -o ~/.local/bin/classlist
chmod +x ~/.local/bin/classlist
```

Then let your computer find it:

```
echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

That second step is only needed once, ever.

## Signing in

```
classlist login
```

It asks for your Classlist email and password. The password is typed blind, so nothing appears as you type it, which is normal. It goes straight to Classlist and is never written to your computer.

Classlist may email you a verification code the first time. Type the code in when asked.

After that you stay signed in. If the session ever expires, the tool asks again by itself. To sign out and delete what is stored, run `classlist logout`.

## Using it

Every command starts with the word `classlist`.

```
classlist pupils
```

Your children and the classes they are in.

```
classlist contacts
classlist contacts smith
classlist contacts --class 2B
```

The parent directory: everyone, or the parents matching a name, or one class. Searching by a child's name works too, which is usually how you actually know people.

```
classlist groups
```

Every group you belong to, with how many unread posts each one has.

```
classlist posts
classlist posts --group 5551234567890123
```

The activity stream, newest first, or just one group. Group ids come from `classlist groups`.

```
classlist events
classlist events --past
```

What is coming up and whether you have replied, or what has already happened.

```
classlist messages
classlist thread 5559876543210987
```

Your conversations, then one conversation in full. Thread ids come from `classlist messages`.

```
classlist post "Anyone know when the book fair starts?" --group 5551234567890123
```

Writes a post to one of your groups. It shows you the message and which group it is going to, and waits for you to type `y` before sending. Group ids come from `classlist groups`.

```
classlist notifications
classlist announcements
classlist classes
```

Recent notifications, school announcements, and the list of classes with their ids.

`classlist posts`, `classlist notifications` and `classlist announcements` take `-n 5` to show fewer items. Any command takes `--json`, on either side of the command name, to print the raw data for a spreadsheet or another program.

## Where your information is kept

Everything lives in a folder called `.config/classlist` inside your home folder, readable only by you.

Your password is not stored. What is stored is the session token Classlist hands back when you sign in, which is what keeps you logged in.

A copy of the parent directory is saved there too, so that searching it is instant instead of downloading everyone again. It is refetched once it is more than fifteen minutes old, but the file itself stays on disk until then, and after that until the next command replaces it. It holds names, classes and children's names, the same things the app shows you, and nothing else from the response.

To remove it along with your session:

```
classlist logout
```

## When something does not work

If you see `command not found: classlist`, the second install step did not take. Close the Terminal window, open a new one, and try again.

If it says it cannot ask for your password, you are not running it in a Terminal window. Run `classlist login` directly rather than from a script.

If a command fails with a number like 401 or 403, your session has expired in a way the tool could not fix. Run `classlist login` again.

## What it cannot do

It cannot comment on someone else's post, RSVP to an event, or send a private message. Posting to a group is the only thing it writes.

Two things Classlist's own servers refuse for a parent account, so they are not offered: the full text of a school announcement, and searching the directory on the server. The announcement list still shows the subject, who sent it and when, and `classlist contacts` searches the copy on your own computer instead.

The verification-code step is built from the app's own sign-in flow, but the account it was developed against was never asked for a code, so that path has not been tested against a live prompt.

## For the technically inclined

Single Python file, no dependencies, about 400 lines. It calls the same endpoints as the Classlist web app at `https://api.classlist.com/_ah/api/`, sending the `ClasslistToken` header the app sends. There is no public or documented API; this was worked out by reading the app's own JavaScript.

`classlist raw <path>` calls any endpoint directly and prints the JSON, which is how you get at anything not wrapped in a command. Unlike the rest of the tool, `raw` can send a `--body` and so can write.

If you keep your credentials in 1Password, set `CLASSLIST_OP_ITEM` to the item reference, for example `op://Private/Classlist`, and sign-in reads the username and password from there instead of asking.

## Licence

MIT. See [LICENSE](LICENSE).
