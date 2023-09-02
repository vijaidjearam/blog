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

(function() {// 2022-03-09

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
    //Unable to set the value to the filename, javascript error unable to set value to a null value.
    //const input = document.querySelector('input[aria-label="File name"]').value;
    //input.value = "-title.md";
    //document.querySelector('input[aria-label="File name"]').value = formatDate(new Date()) + "-title.md";
    GM_setClipboard (header);

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
