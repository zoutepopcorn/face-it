# face-it

# 1 Homepage 
###### code execution on homepage user: https://m.facebook.com/zuck
###### using MOBILE site: https://m.facebook.com/*

### 1.1 :heavy_check_mark: facebook id
```js
const profileJSON = JSON.parse(document.querySelector("#pages_follow_action_id").getAttribute("data-store"))
const profileId = profileJSON["hq-profile-logging"]["profile_id"]
```

### 1.2 :heavy_check_mark: elements from posts

```js
const posts = []
const articles = document.querySelectorAll("article")
for(const article of articles) {
    const domObject = article.querySelector("abbr")
    const date =  domObject.innerText
    const linkMobile = domObject.parentNode.href                          // this is the mobile link
    const link = linkMobile.replace("://m.", "://www.")                   // www.facebook.com/story.php?...
    posts.push({date: date, link: link, linkMobile: linkMobile})
}

console.log(`found posts: ${posts.length}`);
console.log(posts)
```
### 1.3 :balloon: * save in localStorage
```js
localStorage.setItem("scrape", profileId);
localStorage.setItem("scrapeData", JSON.stringify(posts));
```

### 1.4 :heavy_check_mark: homepage - automate 
```js
const MAX = 3;
// Scrape all comments
const sleep = (time) => {
    return new Promise((resolve) => setTimeout(resolve, time));
}

const scrapeAll = async () => {
    // Set max for testing
    let max = 0;
    for(const post of posts) {
        if(max++ >= MAX) {
            break;
        }
        await sleep(3000);
        window.open(post.link)
    }
};
```


### 
```js
// Scroll 10.000 px to the bottom
scrollBy(0,10000)
// Get height of document
document.body.clientHeight
```
---

# 2 Post
###### linked from homepage, goto: https://www.facebook.com/story.php?story_fbid=10107516989200181&id=4
###### using DESKTOP site: https://www.facebook.com/*

### 2.1 :point_right: get id's
```js
// 2.1
'use strict';
const comments = document.querySelectorAll("[aria-label^='Comment']");
const commentsMap = new Map();
for (const comment of comments) {
    const a = comment.querySelector("a");
    const id = a.getAttribute("data-hovercard").replace("/ajax/hovercard/user.php?id=", "");
    const nameId = a.getAttribute("href").replace("/", "");
    const img = a.querySelector("img").getAttribute("src");
    // TODO: to chrono?
    const date = comment.querySelector("abbr").getAttribute("data-tooltip-content");  // data-utime
    let item = {id: id, nameId: nameId, img: img, date: date };
    if (commentsMap.has(id)) {
        console.log(`has comment id ${id}`);
        const newItem = {count: commentsMap.get(id).count + 1, items: [...commentsMap.get(id).items, item]};
        console.log(newItem);
        commentsMap.set(id, newItem);
    } else {
        commentsMap.set(id, {count: 0, items: [item]});
    }
}
console.log(commentsMap);

const sortBy = (sortMap, name) => {
    'use strict';
    return [...sortMap.values()].sort((a, b)=> {
        return b[name] - a[name]
    });
};
console.log(sortBy(commentsMap, "count"));
```

```js
'use strict'
const userMap = new Map();
userMap.set("1", {id: 100});
userMap.set("2", {id: 300});
userMap.set("3", {id: 111});
const sortBy = (sortMap, name) => {
  return [...sortMap.values()].sort((a, b)=> {
    return a[name] - b[name]
  });
};
const sorted = sortBy(userMap, "id");
console.log(sorted);
```


### 2.2 :point_right: get selectors
```js
const footer = document.querySelector("#u_0_16 > div > div._3w53 > div._3tz_._7794")
const viewMore = footer.quesrySelector("a").href
// input "50 of 51,021"
const commentsCountArr = footer.querySelector("._3bu3").innerHTML.replace(",", "").split(" of ")  
// Now: commentsCountArr[0] 
// Total: commentsCountArr[1]

const moreReplies = document.querySelectorAll("li > div._2h2j")
console.log(moreReplies.innerText)

```

# 3 automate (general)
### 3.1 :heavy_check_mark: scroll example
```js
// Scroll 10.000 px to the bottom
scrollBy(0,10000)
// Get height of document
document.body.clientHeight
```

### 3.2 :heavy_check_mark: interval scroller
```js
const scrollInterval = (timeInterval, retry, cb) => {
    let tmpHeight = 0;
    const myInterval = setInterval(() => {
        if (retry++ > 3) {
            clearInterval(this);
        }
        const change = document.body.clientHeight - tmpHeight;
        tmpHeight = document.body.clientHeight;
        if (change > 0) {
            cb(change, (retry * timeInterval));
            scrollBy(0, 10000);
        }
        retry = 0;
    }, timeInterval);
    return myInterval;
};

const onBodyChange = (change, timeout) => {
    console.log(`document.body.clientHeight, changed: ${change}, after: ${timeout}`);
}

const createdInterval = scrollInterval(500, 3, onBodyChange);

// stop the scroller on some event
setTimeout(() => {
    clearInterval(createdInterval);
}, 10000); 
 
```



# :point_right: Stuff
### DOM selector
```js
// ON: https://www.facebook.com/story.php?story_fbid=10107516989200181&id=4
const domPost = {
        content: 'contentArea',
        comment: "[aria-label^='Comment']"
        hover: "[data-hovercard^='/ajax/hovercard/user.php']",
        post: "a[data-sigil='feed-ufi-trigger']",
        reply: '[data-testid="UFI2Comment/reply-link"]',
        more: '[data-testid="UFI2CommentsPagerRenderer/pager_depth_0"]'
    }
```




// ON: https://m.facebook.com/4
```js
#pages_follow_action_id
```

# TODO: 

### :point_right: Blocking images / videos with a webextension to speedup
### :point_right: Collecting.