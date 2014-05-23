---
layout: page
title: artoo.scrape
id: scrape
---

# {{ page.title }}

---

**artoo**'s main goal is to provide you with some useful scraping helpers and this is precisely what the following methods do.

It is advisable, however, to check the [quick start]({{ site.baseurl }}/quick_start) section of this documentation to find a less exhaustive but more concise presentation about the `artoo.scrape` method.

---

* [artoo.scrape](#scrape)
* [artoo.scrapeOne](#scrape-one)
* [artoo.scrapeTable](#scrape-table)

---

<h2 id="scrape">artoo.scrape</h2>
`artoo.scrape` is the heart of **artoo**'s scraping techniques. It takes a selector as its root iterator and then takes the data model you intent to extract at each step of the iteration.

For instance, you could need to iterate on a list while extracting the id and the text of each element of the list.

```js
artoo.scrape(iterator, model, [params, callback]);
```

---

### Arguments

* **iterator**  *css selector | jQuery selector* : the selection of the elements on which you want to iterate.
* **model**     *mixed* : the model of the data you want to scrape and its retrievers.
* **params**    *?object* : an object of optional parameters.
  * **limit**   *?integer* : the number of items you want to scrape if you do not want each of them.
  * **done**    *?function* : a function taking as only argument the scraped items and to be triggered when the scraping is done.
* **callback**  *?function* : same as `params.done`.


---

### Data model

Choosing a data model when using `artoo.scrape` is just a matter of deciding whether you want the function to return an array of values or rather an array of items with properties you prealably chose.

```js
// Passing a single element to the method will
// return an array of the wanted data
artoo.scrape('ul > li', 'text');
>>> ['text of first li', 'text of second li']

// Passing an object to the method will
// return an array of items
artoo.scrape('ul > li', {text: 'text', id: 'id'});
>>> [
  {text: 'text of fist li', id: 'first-li'},
  {text: 'text of second li', id: 'second-li'}
]
```

---

### Retrievers

Ok, but now that you know what kind of array you want to be returned, you need to specify how to retrieve the wanted data.

There are three ways to get what you want with `artoo.scrape`:

#### a single string

Basically, if you pass a string as your retriever, **artoo** will try to apply the given jQuery method, `text` or `html` for instance, to the current item in the iteration, else he'll try to find a relevant attribute.

```js
// We want to retrieve the html of the elements
artoo.scrape('ul > li', 'html');

// We want to retrieve the href of the elements
artoo.scrape('ul a', 'href');
```

#### an expressive object

If you need something a little more complex like subselection but want to stay concise, you can also use an expressive object.

```js
// The sel attribute in the object passed as retriever enables you
// to perform a subselection
artoo.scrape('ul > li', {
  text: {sel: 'span', method: 'text'},
  url: {sel: 'a', attr: 'href'}
});
```

#### a function

Finally, if none of the above methods work for you, you remain free to pass a function that will perform the data retrieval.

Note that a function passed to `artoo.scrape` follows jQuery paradigms: `this` would actually be a reference to the current DOM element in the iteration.

You can therefore use `$(this)` as you would in any jQuery callback.

```js
artoo.scrape('ul > li', function() {
  return +$(this).attr('data-nb') * 4;
});
```

Note also that functions passed as retrievers can take an argument which is actually **artoo**'s internal jQuery reference.You can read the reason why [here]({{ site.baseurl }}/jquery#dollar).

```js
artoo.scrape('ul > li', {
  text: function($) {
    return $(this).text();
  },
  nb: function($) {
    return +$(this).attr('data-nb');
  }
})
```

### Recursivity

If you need recursivity within the `artoo.scrape` method, rather that calling the method itself in a function retriever, you can also pass an object with the scrape property like in the example below.

```js
artoo.scrape('ul.list > li', {
  scrape: {
    sel: 'ul.sublist > li',
    data: 'text'
  }
});
>>> [['item1-1', 'item1-2'], ['item2-1', 'item2-2']]
```

---

<h2 id="scrape-one">artoo.scrapeOne</h2>

---

<h2 id="scrape-table">artoo.scrapeTable</h2>
helper on basic table iteration