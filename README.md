# ![cashcow](https://cloud.githubusercontent.com/assets/640611/16666936/be6cfe96-4481-11e6-9962-44e7bbe6a537.png)


A cached value fetcher that...

for cached values:
- goes off to get resource from cache
- shares the resource with everyone who requested it
- prepares for next trip

for non cached values:
- goes off to get value from source
- hydrates the cache
- shares the resource with everyone who requested it
- prepares for next trip

why?
- pools consumers to avoid doing multiple trips for the same resource at any point in time
- reduces memory burden by only ever holding one instance of resource in memory
- satisfies consumers early if already fetching

cashcow is not prescriptive about what cache your using, simply provide it with these three methods:

- getCache(key) a function that gets value from cache and returns a promise for that value
- setCache(key, value) a function that persists a value to the cache
- fetch(key) a function that fetches the value for real and returns a promise for that value

## Usage:
```js
var cashcow = require('cashcow')
var cowFetch = cashcow(getCache, setCache, fetch)

cowFetch('mything') // gets for real
cowFetch('mything') // combined with previous fetch
cowFetch('mything').then(function (thing) {
  // thing has been fetched for real and cache has been hydrated
  cowFetch('mything') // gets from cache
  cowFetch('mything') // combined with previous get from cache
  cowFetch('mything').then(function (thing) {
    cowFetch('mything') // gets from cache
  })
})

function getCache (key) {
  return myCustomCache.getFromMars(key)
}

function setCache (key) {
  return myCustomCache.set(key)
}

function fetch (key) {
  return getForRealz(key)
}
```
