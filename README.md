![](spa/procesimg/rijksflix.png)

# My Web App - Rijksmuseum

I chose for the Rijksmuseum assignment. The user story for this assignment is:
> As an art lover, I want to be able to search and view art from the Rijksmuseum at home, so that I can still enjoy art during a lockdown. [Rijksmuseum - RijksData API](https://data.rijksmuseum.nl/object-metadata/)

The plan I have is to create something like Netflix, but for art. Hence the name [Rijksflix](https://juliandecloe.github.io/Rijksflix/spa/)

## Modules

Let me show you my files structure and the logics behind it:

- spa
- -- img
- -- modules
- --- getData.js _Used for fetching the API._
- --- renderData.js _Used for working with the API._
- --- router.js _Everything that can be separated from the code that has to do with routing._
- --- states.js _Used for working out the error and loading state._
- -- procesimg
- -- index.html
- -- script.js
- -- style.css

## Activity Diagram

![](spa/procesimg/activitydiagram.png)


## Insights

### How to fetch the detailed artObject API
```
for(let i = 0; i < collection.artObjects.length; i++) {
	fetch('https://www.rijksmuseum.nl/api/nl/collection/' + collection.artObjects[i].objectNumber +'?key=C21U7KQu&imgonly=true')
	.then(function(response) {
		return response.json();
	})
	.then(function(detailed) {
		console.log(detailed)
		listWrap.insertAdjacentHTML('beforeend', 
			`<li>
				<h3>${detailed.artObject.principalOrFirstMaker}</h3>
				<img src="${detailed.artObject.webImage.url}" alt="${detailed.artObject.title}">
			</li>`
		);	
	})
}
```

```
let page = 1;
const rijksAPI = 'https://www.rijksmuseum.nl/api/nl/collection?key=C21U7KQu&ps=10&imgonly=true&p=';

function getData() {
    fetch(rijksAPI + page)
    .then(function(response) {
        return response.json();
    })
    .then(function(collection) {
        console.log(collection)
        listAssign(collection);
    })
    .then(function() {
        listWrap.insertAdjacentHTML('beforeend', 
            `<li class="loading lastOne"></li>`
        );	
    })
}

```

### Check if element is in screen to load more objects

This is to check on horizontal scroll if the 4th last element is in screen to load 10 new objects.

```
function rectChecker() {
    if(!exploreSec.classList.contains('hide')) {
        let listLast = $$('.explore li');
        const rect = listLast[listLast.length - 4].getBoundingClientRect();
        if(rect.right < 600) {
            page = page + 1;
            clearInterval(listChecker);
            getData();
        }
    }
}
```

### Skeleton loader

First I made a simple css animation to make the loading elements lighter and darker for infinity:

```
.loading figure {
	width: 100%;
	height: 100%;
	background: lightgray;
	animation: loading 2s infinite ease-in;
}
```

When the API gets fetched, the loader should be removed. Hence why I remove the loading `<li>` elements in the renderData module.

```
export function starterResults(collection) {
    removeLoader();  <-------------------------------------------------------
    for(let i = 0; i < collection.artObjects.length; i++) {
        exploreWrap.insertAdjacentHTML('beforeend', 
            `<li>
                <h3>${collection.artObjects[i].title}</h3>
                <p>${collection.artObjects[i].principalOrFirstMaker}</p>
                <img src="${collection.artObjects[i].webImage.url.slice(0, -3)+"=s1000"}" alt="${collection.artObjects[i].title}">
            </li>`
        );
        exploreWrap.classList.remove('antiscroll');
    }
    listChecker = setInterval(rectChecker, 50);
}

export function removeLoader() {
    const loadingList = $$('.loading');
    for(let i = 0; i < loadingList.length; i++) {
        loadingList[i].remove();
    }
}
```

For loading new elements in the horizontal scroll I do the same thing. I just make sure to first add a loader before the user can see it.


### Search hash input

I wanted people to be able to search something on the website using hashes. That is why I don't just fetch the API using the search input. If someone searches something using the searchbar, the input will change into a hash and the API gets fetched with the hash. This way people can use the searchbar or a hash to find something.

```
export function resultsRouter() {
    routerDirect.id = searchInput.value;
    window.location.hash = routerDirect.id;
}

export function getsearchData() {
    if(searchPage == 1) {
        removeliafterLoad()
    }
    searchLoader()
    fetch(searchAPI + window.location.hash.slice(1) + '&p=' + searchPage)
    .then(function(response) {
        return response.json();
    })
    .then(function(collection) {
        console.log(collection)
        searchResults(collection);
    })
}
```

### Search hash

There is also a search hash. So if you type #search after the url of the page, it focusses on the searchbar. If you unfocus on the searchbar your hash will be removed. It was tricky to do this part, because a lot of parts of the codes had a great impact on eachother. Because of this I had to change a lot of my code to not mess up the 'search hash input' part. But keeping it short, this is what I came up with:

```
searchInput.addEventListener('input', function() {
	if(searchInput.value == "") {
		searchSec.classList.add('hide');
		exploreSec.classList.remove('hide');
		window.location.hash = '#search';
	}
});

searchInput.addEventListener('focusin', function() {
	window.location.hash = '#search';
});
```

### How did I use routing?

So I saw a lot of people in my class struggeling with routie(). Then I saw I could also just use custom hashes using `window.location.hash`. I like to code using practically nothing. `window.location.hash` gave me the opportunity to work with conditional statements like I'm used to (still took a lot of time to get everything like I wanted it to).


## NOT FINISHED! What now?

So I only had time to work out the explore part and the search part. I feel like these are the most important ones. So for now, that is fine. I do want to finish this project. I think it has a great potential to finish this project. So I definitely want to work on making categories and an object "page". I didn't get the chance to really turn it into the Rijksflix I wanted. It isn't even categorized. It is just one general explore page.

So what are my plans? Adding the "flix" to the "Rijks".

