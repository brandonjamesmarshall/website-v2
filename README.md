# This website will live forever, but won't be stuck in the past.

In June 2023, the most significant update to Ordinals dropped: **Recursion.**

It was long anticipated for many artists in the Bitcoin space, allowing for groundbreaking new concepts: Generative art collections from artists I've admired, like [@billyrestey](https://twitter.com/billyrestey) and [@0xFAR](https://twitter.com/0xfar). Plus, amazing tooling like [@tryGamma](https://twitter.com/tryGamma)'s Prints, which enabled numbered editions.

But when I first heard about Recursion, my mind immediately went to one thing: Websites.

IFPS files can go stale. Files on AWS go away once someone stops paying the bills. The realization that I could pay a one-time fee to have something reliably hosted on Bitcoin was a new concept worth exploring.

While writing for [@trustmachinesco](https://twitter.com/trustmachinesco) about the upcoming update that would lead to Recursion, I realized something awesome: I could be the first one to publish a working example of this before it hit mainnet.

So, using the HTML knowledge entirely self-taught from customizing my old Myspace profile, I cobbled together a single-page ["website"](https://ordinals.com/preview/111bb15037a4665a79701edb84050b802be1b16791f4a6f03a673d2d3e23cf70i0): A single HTML file featuring a PFP and a couple social media icons, with each image being pulled in using recursion.

In retrospect, it sounds so rudimentary, but as it turns out, I was actually the first person to do this! I posted about it on [Twitter](https://twitter.com/marshallmixing/status/1668383269598167040) and a bunch of people took notice. A few people even adapted it to build their own recursive websites, which was so amazing to see! A few months later, I met Casey (the creator of Ordinals) and he actually recognized me from the site I made! 

### It was proof that you don't need to be a very technical person to build cool shit on Bitcoin.

And while it was a great proof-of-concept, it had some serious limitations:

- It was a single-page design.
- It couldn't be updated.

After a few months, Ordinals got even more new features. Parent/child added support for hierarchical structures, like a family tree of sorts, to group collections of inscriptions together. Reinscription allowed you to inscribe a new file on the same satoshi. And new "recursive endpoints" were added which allowed you to do things like, for example, look at the most recently inscribed file on a specific sat.

Are you thinking what I'm thinking? **Time for V2.**

## The Structure

What if, instead of a single static HTML file, I could have multiple pages?   

And what if I could update them whenever I wanted?   

And what if they were as economical as possible to inscribe?

Thinking back to how traditional websites work, you typically have a DNS (domain name) which points to an IP address (server) containing a bunch of files, and those files can be updated at any time. So, let's just have a series of parent/child inscriptions that point to a specific sat, and use endpoints to make sure I serve the most recently inscribed file on that sat.

To form a general structure, I first inscribed a series of simple .txt files using parent/child that essentially act as a proxy for folders. The actual content of each file is then inscribed on the same sat that the placeholder .txt file corresponds with.

Here are the simple text files I inscribed:
* brandonmarshall.btc
    * assets.brandonmarshall.btc
        * style.assets.brandonmarshall.btc
    * blog.brandonmarshall.btc
        * index.blog.brandonmarshall.btc
    * footer.brandonmarshall.btc
    * home.brandonmarshall.btc
    * sidebar.brandonmarshall.btc

By setting it up this way, I can update each of these components individually without having to reinscribe the entire website.

For example, if I wanted to update my sidebar, I would simply reinscribe a new `sidebar.md` file onto the same sat that `sidebar.brandonmarshall.btc` is on. To update my css file, I reinscribe `style.css` on the same sat as `style.assets.brandonmarshall.btc`.

## The index.html file

There's a lot happening under the hood to make up the "Home" page that you saw when you first got here.

It's using recursion to pull in these components:
- The latest sidebar.md file
- The latest home.md file
- The latest footer.md file
- The latest style.css file
- A showdown.min.js library
- Several image and svg assets

The .md files are the most unique part of this. **I wanted to make it as inexpensive as possible to change components of my website.** If I just packaged this iensite site into one `index.html` file, I would have to reinscribe the **entire** file, even if only a small part changed.

So the first step was to break the site up into components that could be changed individually. I also wanted to cut down on my use of HTML tags, to save precious bytes of space, so opted to use Markdown for these components. The homepage is parsing the markdown using the `Showdown.js` library, which I also inscribed. One extra benefit is that these .md files (and individual blog posts) still render natively on standalone viewers like [ord.io](https://ord.io) or even the official [ordinals.com](https://ordinals.com) website.

I added some additional code so that any non-inscription links should open in a new tab or window, thus following the "sandbox" rules that Ordinals has by default when linking to external resources.

## The sidebar

Clicking on "internal" links in the sidebar uses Javascript to dynamically render them in the main content window.

My needs for this site are pretty basic, so I only have "Home" and "Blog" pages. But I also designed this to be as extensible and scalable as I could to support future scenarios. 

I could easily link to more PageName.md files in the sidebar, and they'll render in the main content area with no additional code needed! This should work with more filetypes as well.

## The Blog Page

Clicking on "Blog" is not actually loading a blog.md file like you may think. What it's actually doing is fetching an inscribed .json file which contains an array of inscription IDs, with each inscription ID representing a standalone "blog post" in .md format.

From there, it concatenates all the blog posts together and separates them with an HR rule before displaying them in order.

**Yeah, my website has OP_CAT.**

I thought about using only Parent/Child for blog posts, and showing a sequential list of all blog post "children" in the order they were inscribed. But I preferred the freedom of being able to set this list manually, just in case I wanted to remove or rearrange the order of blog posts in the future. Or just in case I messed up and inscribed something with a typo and needed to re-do it. Maybe I'll figure out a way to do this for V3 and introduce an "exceptions" list in case I want to hide something.

## Future features

One thing that would have been cool is to have each blog post dynamically show the timestamp or block it was published on. But unfortunately, an endpoint for this doesn't exist yet. Once that happens, future blog posts will have it!

I probably could have figured out a way to do this, but right now my website doesn't support deep linking of specific blog posts for easier sharing. For now, you'll have to scroll through the blog to find it, or refer to the individual .md files themselves.

## Final notes

I designed this website for myself, but have also listed it up on my [GitHub](https://github.com/brandonjamesmarshall/website-v2/) along with the batch files I used to give you a full idea of how it was built. Please feel free to adapt this to make your own site!

Altogether, this website takes up around 107kb of space, which costs roughly $450 to inscribe at 30 sat/vB. Mistakes can be very costly! I did a ton of testing in Regtest prior to inscribing these files for real to make sure I didn't mess anything up. 
