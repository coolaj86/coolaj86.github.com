---
layout: article
uuid: A477EC82-46AC-4374-BCC8-49B3D02522A7
title: "Dear Tyler, I'm sorry I added 6,547 books to your booklist"
name: dear-tyler--i-m-sorry-i-added-6-547-books-to-your-booklist
created_at: 2011-09-02
updated_at: 2011-09-02
categories: off-topic
---

Question
---

How do you delete a 5mb list of items when each time you delete an item the server response is the 5mb - 1k list?

Answer
---

    $.ajax({
        url: "http://lug.mtu.edu/ubuntu-releases//natty/ubuntu-11.04-desktop-i386.iso"
      , timeout: 10 * 1000
      , complete: function () {
          console.log(arguments);
        }
    });

The Full Story
---

I'm working on Blyph.com with my friend Brian.
My friend Jameson has also helped us a bit.


I created an account for myself to be able to get into the BYU Book Exchange so that
I could get a list of all books used by BYU and, inadvertently, e-mail addresses 
(which isn't currently forbidden by the user agreement, btw).


Anyway, I was logged into his account to test that I could see a book that 
I listed under my account and whether or not the message submit form worked for me.

It failed in that it returned my ajax request with a "could not send message" error.
It succeeded in that it gave me the e-mail address.

What I didn't realize was that each time I requested a book it added it to a list of
books wanted and that the other users were notified in a midnight batch job.

When I finally got around to running my script I did it in the wrong browser.
I was on Jameson's account rather than my own.

The next day he asks me: "Do you have any idea why I'm getting so many texts and calls
about wanting to buy books on Japenese turture and Elementary Education? Or why I'm 
listed as wanting 6,547 books on the book exchange?"

Ha ha

We found out rather quickly that the browser's caching mechanism is very self-defeating
when you're trying to do ajax requests with 5mb of data each. They just weren't being garbage
collected fast enough and even Chrome was crashing over and over
(I should probably submit a bug).

But thank goodness for `abort()`! We were able to delete all 6,547 items in about 15 minutes
rather than spending 1 minute per 20. Phew!! We got lucky on that one.
