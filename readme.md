# face-md

# 1 Homepage 
https://m.facebook.com/zuck

### 1.1 facebook id
```js
const profileJSON = JSON.parse(document.querySelector("#pages_follow_action_id").getAttribute("data-store"))
const profileId = profileJSON["hq-profile-logging"]["profile_id"]
```

### 1.2 elements from posts

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
### 1.3 * save in localStorage
```js
localStorage.setItem("scrape", profileId);
localStorage.setItem("scrapeData", JSON.stringify(posts));
```

### 1.4 homepage - automate :heavy_check_mark:
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
https://www.facebook.com/story.php?story_fbid=10107516989200181&id=4

### 2.0.0
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
### 3.1
```js
// Scroll 10.000 px to the bottom
scrollBy(0,10000)
// Get height of document
document.body.clientHeight
```

### 3.2
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


# TODO: 

### X Blocking images / videos with a webextension to speedup
### X Collecting.