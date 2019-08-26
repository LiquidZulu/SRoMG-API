Square Root of Minus Garfield API v2
============
An easy way to access information from _SRoMG_. 

**GITHUB NOTES:** 
 + See verbose for pretty source or sromg-api for minified source.
 + Docs are uncompiled in verbose to save on size.


Documentation
-------------
Read the full documentation [here](https://liquidzulu.github.io/SRoMG-API/)


About
-----
v2.X.X brings about a complete rework of how the API works, previously it was a bodged together mess that didn't bother to render the html rather just did a bunch of linear searches which caused problems in that some comic pages would be formatted differently than others.

This new API is also an API wrapper and is packaged up into a node module, though the API will still function through calls to [http://garfield-comics.glitch.me/API](http://garfield-comics.glitch.me/API "SRoMG API").


What's new?
----------
1. Improved performance.
2. Promise based event-driven wrapper included.
3. Gets all the information regardless of how the page is formatted.
4. Can now get information on authors.

How to use the module
---------------------
To run the API locally rather than making requests to my site you need to install it from npm or GitHub:
`npm i sromg-api`

When you have downloaded the module you can include it in your project through `require()` as shown:

```javascript
// setting up constants
const sromg_api = require('sromg-api');
const META      = new sromg_api.META();
const FLAGS     = sromg_api.FLAGS;
const util      = require('util');
const { JSDOM } = require('jsdom');
const got       = require('got');

// PROMISE BASED EXAMPLE

Client.on("message", async msg => {

    if(!msg.content.equals("sromg")) return;

    let latest_sromg   = await got(`http://www.mezzacotta.net/garfield/`)
    const { document } = (new JSDOM(latest_sromg.body)).window;

    let NUMBER_OF_SROMG_COMICS = (new sromg_meta.Comic(document)).number

    msg.reply({attachment: await META.getComic(Math.random() * NUMBER_OF_SROMG_COMICS) });
});


// EVENT DRIVEN EXAMPLE
// event listeners, you can also listen for error events as detailed in the documentation

META.on("AUTH_INIT_SUCCESS", author => {
    // do something with author object, shown below this code snippet
})

META.on("COMIC_INIT_SUCCESS", comic => {
    // do something with comic object, shown below this code snippet
})




// init for author "Roytheshort" with no event logs, this will trigger the AUTH_INIT_SUCCESS event once complete
META.init({
    n: 464,
    FLAGS: FLAGS.META_PARSER_PARSE_AUTHOR | FLAGS.META_PARSER_NO_EVENT_LOGS
})

// init for author "Andrew Kepple"
META.init({
    n: 103,
    FLAGS: FLAGS.META_PARSER_PARSE_AUTHOR
})


// init for comic 2828 with no flags
META.init({
    n: 2828,
    FLAGS: 0
})
```

Author and Comic objects in detail
----------------------------------

**AUTHOR**
```JSON
{
    "name":   "Authors name",
    "number": <Number of author>,
    "bio":    "Authors biography if they have one",
    "comics": {
        "comics": [<Array>, <of>, <comic>, <numbers>],
        "buffer": <number of comics in the buffer>
    }
}
```

**COMIC**
```JSON
{
    "name":   "Comic title",
    "number": <Number of comic>,
    "image":  {
        "src": "url of the comic image",
        "x":   <number of pixels wide>,
        "y":   <number of pixels tall>
    },
    "author": {
        "name":   "name of author",
        "number": <number of author>
    },
    "transcription": "The provided transcription, no guarantee that this is accurate",
    "authorWrites":  "The authors notes on the comic WITH ALL HTML TAGS STILL INCLUDED, you may want to translate this to markdown applicable to your solution",
    "originalStrips": [
        {"strip": "date of strip", "href": "link to strip"},
        {"strip": "date of strip", "href": "link to strip"},
        ...
        ...
        {"strip": "date of strip", "href": "link to strip"}
    ]
}
```
