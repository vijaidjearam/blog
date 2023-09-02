---
layout: post
date: 2023-09-02 22:15:36
title: TemperMonkey Script to paste Front Matter Template
category: TemperMonkey
tags: Tempermonkey javascript
---

## The script loads the Frontmatter template to the clipboard
After creating new post just CTRL + V to paste the FrontMatter

```
// ==UserScript==
// @name        front_matter_block_github_blog
// @namespace   brahimmachkouri
// @version     0.1
// @description Paste the "Front Matter" block for Github blogs : go in the edit post and CTRL-V
// @author      Brahim Machkouri
// @match       https://github.com/*/blog/new/main/_posts
// @grant       GM_setClipboard
// ==/UserScript==

document.onload = (function() {// 2022-03-09

    'use strict';
    const date = formatDate(new Date())+ ' ' + formatHour(new Date());
    console.log("yes");
    const header = '---\n\
layout: post\n\
date: '+ date + '\n\
title: \n\
category: \n\
tags: \n\
---\n\
filename:'+ formatDate(new Date()) + '-title.md \n\
';
    GM_setClipboard (header);
    // Convenience function to execute your callback only after an element matching readySelector has been added to the page.
    // Example: runWhenReady('.search-result', augmentSearchResults);
    // Gives up after 1 minute.
    function runWhenReady(readySelector, callback) {
        var numAttempts = 0;
        console.log(readySelector)
        var tryNow = function() {
            var elem = document.querySelector(readySelector);
            console.log(elem);
            if (elem) {
                document.querySelector('input[aria-label="File name"]').value = formatDate(new Date()) + "-title.md";
            } else {
                numAttempts++;
                if (numAttempts >= 34) {
                    console.log(readySelector);
                    console.log('Giving up after 34 attempts. Could not find: ' + readySelector);
                } else {
                    setTimeout(tryNow, 250 * Math.pow(1.1, numAttempts));
                }
            }
        };
        tryNow();
    };
    const readySelector = 'input[aria-label="File name"]'
    runWhenReady(readySelector);
    //const input = document.querySelector('input[aria-label="File name"]');
    //console.log(input)
    //input.value = "-title.md";
    //document.querySelector('input[aria-label="File name"]').value = formatDate(new Date()) + "-title.md";


    function padTo2Digits(num) {
        return num.toString().padStart(2, '0');
    }

    function formatDate(date) {
        return (
            [
                date.getFullYear(),
                padTo2Digits(date.getMonth() + 1),
                padTo2Digits(date.getDate()),
            ].join('-')
        );
    }

    function formatHour(date) {
        return ([
            padTo2Digits(date.getHours()),
            padTo2Digits(date.getMinutes()),
            padTo2Digits(date.getSeconds()),
        ].join(':')
               );

    }

})();
```
