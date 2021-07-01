---
layout: post
title: 'PHP Coding: Reverse Geocoding for taxonomy terms' 
permalink: /blog/php-coding-reverse-geocoding-for-taxonomy-terms
published: true
date: 2021-06-28
author: davidjguru
categories: [PHP Coding]
sitemap: true
youtubeId: k84G4ODpBsE
---

| ![Picture from Unsplash, by @saddy143]({{ site.baseurl }}/images/davidjguru_php_coding_reverse_geocoding_loading_taxonomy_terms.png) |
|:--:|
| *Picture from Unsplash, user [Nafis Al Sadnan, @saddy143](https://unsplash.com/@saddy143)* |  

Have you ever had to perform geocoding? I mean, the exercise of getting some kind of data from stuff like a specific naming in order to get values like longitude and latitude. It had been a long time since I had used the know as "Geographic Information Systems" (GIS), when I was a young (and naive) Java developer...
<!--more-->

The fact is that I recently had to perform some tasks related to Geocoding: I needed to get some values from latitude and longitude values, the so-called "Reverse Geocoding". My goal was fill out a Drupal taxonomy and populating a related field in a specific content type. 
I have decided to compile my case notes here in a sequential way, but the experience was previously published separately as a sucession of Gitlab Snippets and Gist from Github that you can follow from here.  

**TL;DR** -> This is a post about how to execute Reverse Geocoding for populating a taxonomy term field in Drupal 8, 9.  

  
* [Drupal 8,9 - Reverse Geocoding using external Service from PHP](https://gist.github.com/davidjguru/0ba7b135ae2d9738278c5dff2a311e09)  
* [Drupal 8,9 - Getting Taxonomy Terms from different levels programmatically ](https://gist.github.com/davidjguru/3b9d36bf3e00dd338d751b7bfa2c41eb)  
* [Drupal 8,9 - Populating taxonomy with hierarchical structure from external geocoding ](https://gitlab.com/-/snippets/2137785)  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**Table of Contents.**

[1- What am I looking for?](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Reverse Geocoding for PHP](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Options for Reverse Geocoding](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Loading taxonomy terms](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[5- Read More]()  
[6- :wq!](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
<!-- /TOC -->
------------------------------------------------------------------------------------------------


## 1- What am I looking for?

Well, I'm inside a scenario where I'm creating new nodes of a specific content type, then I'm populating its fields programmatically and finally saving the whole new node. I'm doing all the process by coding, out of the Drupal GUI and from a custom code made by me. This is the big picture.  

At a higher zoom level, within all its related fields, I have a Entity Reference field, specfically for a Hierarchical Taxonomy Term, I mean, a field with values to pre-charged taxonomy terms from a populated vocabulary which contains terms in three levels:  

My vocabulary is storing places from Peru (Perú, Piruw) in South America. This is a set of more than 2000 terms divided in a tree of hierarchy with three level. In Peru there are 24 departments, 25 regions, 196 provinces and 1838 districts, so It looks pretty extensive, something like:  

![Reverse Geocoding: vocabulary view]({{ site.baseurl }}/images/davidjguru_php_coding_reverse_geocoding_loading_taxonomy_terms_1.png)  

I receive geocoded files with some values and I take long, lat and I start to consult to wich point they belong, in order to obtain their things like its department, province and district. Then I'll look for a match with my pre-populated taxonomy and I'll save the three references to taxonomy terms maintaining hierarchy. Ok? Let's recap.  

Steps:  
----------
1- Processing long, lat values.  
2- Getting Department, Province and District from the long, lat values.  
3- Looking for matching in my vocabulary.  
4- Setting the matched values in the taxonomy hierarchy for each new node. 
5- Saving the new populated node.  


## 2- Reverse Geocoding for PHP  

Since we have to perform Geocoding tasks, let's first remember some basic concepts and what kind of main resources we have available.  

### What is "Reverse Geocoding"  

First, let us initially review the concept of Reverse Geocoding:  
> "Reverse geocoding is the process of converting a location as described by geographic coordinates (latitude, longitude) to a human-readable address or place name. It is the opposite of forward geocoding."  

Source: [Reverse Geocoding in Wikipedia](https://en.wikipedia.org/wiki/Reverse_geocoding)  

### Installing geocoder for PHP  
[Geocoder PHP](https://geocoder-php.org/) is the most famous library for Geocoding operations written in PHP language.  
Geocoder PHP provides: “an abstraction layer for geocoding manipulations”. You can get the resources from [its repository in Github](https://github.com/geocoder-php/Geocoder) and as a [Packagist set of packages](https://packagist.org/providers/geocoder-php/provider-implementation). The main library is divided into three basic concepts in order to work with Geocoding: an HttpAdapter for doing requests, several geocoding Providers and various Dumpers to get a formatting output. You will need two basic resources: the main library and a provider. I'm gonna install it in my Drupal local deploy:  

```bash 
$ ddev composer require willdurand/geocoder
$ ddev composer require geocoder-php/locationiq-provider 
```


Or whithout ddev if you're out of this tooling:  

```bash
$ composer require willdurand/geocoder
$ composer require geocoder-php/locationiq-provider 
```

Now I have the classes available through the class loader. I can use the resources in my installation. Geocoder only requires just a pair of keyresources:

- An external provider
- A HTTP Client for connections.

I have the provider clasess and I have the HTTP Client too, Guzzle library, present and available in Drupal. So what I need now is get data from an external service. Let's see some ideas about it.   

### External Services  

As you can see in former sections I'm using the classical https://geocoder-php.org library. It requires the main library and the external providers classes in order to connect to others external APIs.  
In this case I'm using LocationIQ to get the addresses:  https://locationiq.com in free mode. I've created a new user in this platform and I'm getting a token API for queries.  

Some Limitations for the free mode of LocationIQ:  

- 1 Access Token
- 5,000 request/day
- 2 request/second

But well, only for some tests this may be useful.  
### Making External Reverse Geocoding Queries 

Now I have the Geocoding library, the classes for the provider and data for connections to external provider, I can do some tests for Reverse Geocoding. Now for instance, I wanna build a address like an unique string builded from external received data:  

```php
<?php

[...]
use Geocoder\Query\ReverseQuery;
use Http\Adapter\Guzzle6\Client;
use Geocoder\Provider\LocationIQ\LocationIQ;
use Geocoder\StatefulGeocoder; 
[...]

// You can use other options for the HTTP Client. 
// $httpClient = \Drupal::httpClient();
$httpClient = new Client();
$provider = new LocationIQ($httpClient, "MY-APIKEY-FROM-LOCATIONIQ");
$geocoder = new StatefulGeocoder($provider, 'en');

// Launch the external query.
$result = $geocoder->reverseQuery(ReverseQuery::fromCoordinates($latitude_data, $longitude_data));

// Mount the returned address like a unique string. 
$address = $result->first()->getStreetName() . ' ' . $result->first()->getStreetNumber() . ', ' .
            $result->first()->getLocality() . ' ' . $result->first()->getPostalCode() . ', ' .
            $result->first()->getCountry()->getName();

```
And then I can load the address variable in a Drupal field. But this is not my case, remember I need to populate taxonomy terms fields in a Drupal node, so beyond this example I need to test more resouces.  

## 3- Resources 

By searching these resources we can find libraries, services and APIs for consumption that have as few limitations as possible (rate limit, requests per second/day, etc), and finally achieve a more efficient implementation.  
### External Public Resources 

There are some public resources opened to connections, URLs ready to connections, just like this [cartociudad.es/geocoder/](http://www.cartociudad.es/geocoder/) with its own [Geocoding / Reverse Geocoding services](http://www.cartociudad.es/geocoder/api/geocoder/reverseGeocode?lon=-0.562854&lat=39.918735 ) (click and see the results in browser).  

The operations are free and simple, just an open URL and query parameters, but maybe the service is not available or stable enough for you. Let's see other options.  
 
### What about Drupal 

In Drupal you can use the [Drupal Geocoder Module](https://www.drupal.org/project/geocoder), a contrib module that acting like a wrapper for geocoder library and resolves by itself the installation of the main library, as you can see in: [geocoder/composer.json](https://git.drupalcode.org/project/geocoder/-/blob/8.x-3.x/composer.json).  

You have to follow the instructions from its README file: [geocoder/README.md](https://git.drupalcode.org/project/geocoder/-/blob/8.x-3.x/README.md).  

I certainly haven't had much luck using this module. Some strange bug limits me to use it for quick situations. There are some weird issues installing providers, see the [Issue 3153678](https://www.drupal.org/project/geocoder/issues/3153678).  


### Nominatim  

Nominatim is an API used to search OSM data by name and address in order to doing Geocoding and Reverse Geocoding. [OSM is "Open Street Map"](https://www.openstreetmap.org), a collaborative project creating a big, free and editable map of the world. 
[Maria Arias de Reyna](https://www.linkedin.com/in/delawen/), [@delawen](https://twitter.com/delawen) and [Juan Luis Rodríguez](https://www.linkedin.com/in/juanluisrp/), [@juanluisrp](https://twitter.com/juanluisrp) guided me in this way. They are the greatest [GIS](https://en.wikipedia.org/wiki/Geographic_information_system) experts I ever know and they were very helpful here, givin' me some ideas and links to resources. Kudos. Specifically, @delawen told me about Nominatim, a heavy-weight solution for Geocoding Operations and I began to play with it. What is 'Nominatim'? First Bus Stop:  

> "Nominatim is the geocoding software that powers the official OSM site www.openstreetmap.org. It serves 30 million queries per day on a single server."  

Source: [https://nominatim.org](https://nominatim.org/)  

Ok, I see I can do my queries using Nominatim, but what else?  

- [There is a Nominatim version based in Docker](https://github.com/mediagis/nominatim-docker).  
- [You can select Region for your Docker deploy](https://download.geofabrik.de/).  
- [And select Sub-Region](https://download.geofabrik.de/south-america.html).  
- [Country](https://download.geofabrik.de/south-america/peru.html).  
- [Or Continent](https://download.geofabrik.de/europe/).  
- [You can download the source code from its main repository](https://github.com/osm-search/Nominatim).  
- [And get an overview about how it works](https://nominatim.org/release-docs/develop/api/Overview/).


Nominatim offers some Endpoints for specific queries:  

```bash
/search - search OSM objects by name or type
/reverse - search OSM object by their location
/lookup - look up address details for OSM objects by their ID
/status - query the status of the server
/deletable - list objects that have been deleted in OSM but are held back in Nominatim in case the deletion was accidental
/polygons - list of broken polygons detected by Nominatim
/details - show internal details for an object (for debugging only)
```
And you can use Nominatim from a local Docker deploy or from live in external server:  

```bash
https://nominatim.openstreetmap.org/reverse?lat=<value>&lon=<value>&<params>
https://nominatim.openstreetmap.org/reverse?format=json&lat=-12.046374&lon=-77.042793
https://nominatim.openstreetmap.org/reverse?format=json&lat=-9.949480903131423&lon=-78.22368622836807
```
What will return a Reverse Geocoding Query from Nominatim? Take a look:  
```json
{
  "place_id": 159358569,
  "licence": "Data © OpenStreetMap contributors, ODbL 1.0. https://osm.org/copyright",
  "osm_type": "way",
  "osm_id": 286831995,
  "lat": "-9.949531163248126",
  "lon": "-78.22368982770104",
  "display_name": "Culebras, Province of Huarmey, Ancash, Peru",
  "address": {
    "village": "Culebras",
    "region": "Province of Huarmey",
    "state": "Ancash",
    "country": "Peru",
    "country_code": "pe"
  },
  "boundingbox": [
    "-9.9496929",
    "-9.9483904",
    "-78.225981",
    "-78.2215248"
  ]
}
```

Ok, This seems to be all I need!...  
Nominatim is my great solution! well, let's get to work.  


## 4- Loading taxonomy terms 

Well, I feel that throughout the previous sections we have already laid some firm foundations. Well, I feel that throughout the previous sections we have already laid some firm foundations. Now I want to recover my initial goal: to load taxonomy terms. For this I will use Nominatim, but as the local container addresses are not queryable from this article, I will change the URLs to external queries so you can try.  

I need to complete the next field:  

![Taxonomy Terms field based in geodata]({{ site.baseurl }}/images/davidjguru_php_coding_reverse_geocoding_loading_taxonomy_terms_2.png)  

### Getting data for Country
In Peru there are 24 departments, 25 regions, 196 provinces and 1838 districts. I need to save Department, Province and Districts for every item.  In the former example I will catch the data from the Nominatim reverse endpoint, where Department="Ancash" , Province="Huarmey"  District="Culebras", so based on the data received from Nominatim that you can see in a former example, I need to adapt this:  

- Department=state  
- Province=region  
- District=village  

### Mounting the new container  

Ok, now I'm gonna launch the Nominatim container using my local Docker Installation and the selected URLs with data from my required country (Peru):  

```bash
$ docker run -it --rm \
  -e PBF_URL=https://download.geofabrik.de/south-america/peru-latest.osm.pbf \
  -e REPLICATION_URL=https://download.geofabrik.de/south-america/peru-updates/ \
  -p 8080:8080 \
  --name nominatim \
  mediagis/nominatim:3.7
```

This new container will expose the API address at:  
```bash
http://localhost:8080/search.php?q=KEYWORD
```
### Resolving queries  

Now I can build some queries to my new Nominatim Server from my PHP code (in a Drupal custom module, like a service or from a Controller, you know):  

```php
<?php

[...]
use GuzzleHttp\Client as GuzzleClient;
use Http\Adapter\Guzzle6\Client as GuzzleAdapter;
use GuzzleHttp\Psr7\Request as GuzzleRequest;
[...]
```

```php
[...]
 // We are getting the values for geofield.
 // 'lat' => $latitude_data, 'lng' => $longitude_data.

$config = ['timeout' => 10];
$httpNominatimClient = new GuzzleClient($config);
$adapter = new GuzzleAdapter($httpNominatimClient);
$request = new GuzzleRequest('GET', 'https://nominatim.openstreetmap.org/reverse?format=json&lat=' . $latitude_data . '&lon=' . $longitude_data);
$response = $adapter->sendRequest($request);
$geo_nominatim = json_decode($response->getBody(), true);
[...]
```
And I can extract the required values following the returned format in the json response, putting some control in the case of the value for "District" (sometimes is a City, sometimes is a Village):  

```php 
// First get the initial strings. 
$initial_department = $geo_nominatim['address']['state'];
$initial_province = $geo_nominatim['address']['region'];
if(empty($geo_nominatim['address']['village'])){
  $initial_district = $geo_nominatim['address']['city'];
} else {
  $initial_district = $geo_nominatim['address']['village'];
}
```

### My next problem: the 'Ñ' character and the diacritics 

The first thing I notice is that Nominatim is returning me some naming without accents or diacritics in some cases, in pure English. Let's see an example about this. I'm looking for data (lat, lon) for "Huánuco" (Perú) and Nominatim is returning me something like this: 

```json
{
  "city":"Chinchao",
  "region":"Province of Huánuco",
  "state":"Huánuco",
  "postcode":"062",
  "country":"Peru",
  "country_code":"pe"
}
```

As you can see, "Huánuco" is comming with diacritics but the country name "Peru" is returned without accent. Can this cause me problems in the future? So now having observed this issue, I now have to check that there are no misinterpretations, normalizing all the strings on both sides of the matching.  

The 'Ñ' character is the sign for a very common sound that you can build using 'gn' in French or Italian, and using 'ny' in Catalan language. But at some point an old transcriber monk decided to use this symbol, and here we are... 

So, I normalize some values but maintaining the 'ñ' character in names, just in ['San Vicente de Cañete'](https://en.wikipedia.org/wiki/San_Vicente_de_Ca%C3%B1ete).  

How Can Normalize string but maintaining special characters like 'ñ'? well, I was thinking about changes the character, normalize and then return the 'ñ' character to the strings, so I can try something like this:  

```php
// Changes the 'ñ' character.
$first_step_replace_1 = str_replace('ñ', '\001', $initial_department);
$first_step_replace_2 = str_replace('ñ', '\001', $initial_province);
$first_step_replace_3 = str_replace('ñ', '\001', $initial_district);

// Normalizes all the strings.
$normalized_department = preg_replace('/[\x{0300}-\x{036f}]/u', "", Normalizer::normalize($first_step_replace_1 , Normalizer::FORM_D));
$normalized_province = preg_replace('/[\x{0300}-\x{036f}]/u', "", Normalizer::normalize($first_step_replace_2 , Normalizer::FORM_D));
$normalized_district = preg_replace('/[\x{0300}-\x{036f}]/u', "", Normalizer::normalize($first_step_replace_3 , Normalizer::FORM_D));

// Returns the 'ñ' character to the strings.
$normalized_department = str_replace('\001', 'ñ', $normalized_department);
$normalized_province = str_replace('\001', 'ñ', $normalized_province);
$normalized_district = str_replace('\001', 'ñ', $normalized_district);
``` 
Now I can make sure that everything is normalized, flattened without diacritics and maintaining the 'ñ' character.  

### Loading the whole taxonomy  

The next step for me is to make sure that I can execute a matching between the received values and the existing ones from my vocabulary. In addition, I must take into account that the same name can be repeated throughout the tree structure: A province and a district may share a name, and it may even be the case that the same name is shared at all three levels of the hierarchy of terms, for instance, see the next example where "Arequipa" is a Department, a Province and a District:  


![Same term for all the structure]({{ site.baseurl }}/images/davidjguru_php_coding_reverse_geocoding_loading_taxonomy_terms_3.png)  

So I have to do some thinking to discern how to know that a specific name is part of the first, second or third level within the taxonomy. For the moment to start with, I load the whole taxonomy, all its terms, using [the loadTree() method](https://api.drupal.org/api/drupal/core%21modules%21taxonomy%21src%21TermStorage.php/function/TermStorage%3A%3AloadTree/8.2.x). Although it is a heavy process to load all the complete entities (TRUE parameter), later I can change this for a "light" load of only name and numeric tid.  

```php
[...]
// Load the whole taxonomy tree using values.
$manager = \Drupal::entityTypeManager()->getStorage('taxonomy_term');
$taxonomy_tree = $manager->loadTree(
        'location', // The taxonomy term vocabulary machine name.
        0,               // The "tid" of parent using "0" to get all.
        3,               // Get all available level.
        TRUE             // Get full load of taxonomy term entity.
        );
[...]
```
Ok, now I'm in a loop going through the values taken from Nominatim, point by point. I need to find the term matching from traversing the taxonomy values and know in which position (by level) I should load the geolocated point. To do this I'm planning to ask each term if it has children or if it has parents, since (I think): 

- If it has no parents and it has children, it will be first level. 
- If it has parents and has children, it will be second level. 
- If it has parents and has no children, it will be third level. 

But as first step:  

```php
[...]
// Prepares the future array of taxonomy terms to load.
$array_of_tids = [];
[...]
```

Now I'm gonna loop over all the taxonomy terms in a Normalized way:  

```php
[...]
foreach ($taxonomy_tree as $term) {
  $first_step_naming = str_replace('ñ', '\001', $term->getName());
  $second_step_naming = preg_replace('/[\x{0300}-\x{036f}]/u', "", Normalizer::normalize( $first_step_naming, Normalizer::FORM_D));
  $normalized_naming = str_replace('\001', 'ñ', $second_step_naming);
[...]
}
```

I can play with terms using conditions and testing parents and childrens. I can use [the loadChildren() method](https://api.drupal.org/api/drupal/core%21modules%21taxonomy%21src%21TermStorage.php/function/TermStorage%3A%3AloadChildren/8.2.x) and [the loadParents() method](https://api.drupal.org/api/drupal/core%21modules%21taxonomy%21src%21TermStorage.php/function/TermStorage%3A%3AloadParents/8.2.x) passing by its tid for every term.  

```php
// If has not parent and has children is a first level term. 
if((empty($manager->loadParents($term->id()))) && (!empty($manager->loadChildren($term->id()))) && (($normalized_naming === $normalized_department))) {
  $array_of_tids[0] = $term->id();
}

// If has parent and children then is a second level term.
if((!empty($manager->loadParents($term->id()))) && (!empty($manager->loadChildren($term->id()))) && (($normalized_naming === $normalized_province))) {
  $array_of_tids[1] = $term->id();
}

// If has parent and has not children then is a third level term.
if((!empty($manager->loadParents($term->id()))) && (empty($manager->loadChildren($term->id()))) && (($normalized_naming === $normalized_district))) {
  $array_of_tids[2] = $term->id();
  $parent = reset($manager->loadParents($term->id()));
  $array_of_tids[1] = $parent->id();
}
```

Also I can save the terms in hierarchy using only the final term from the third level, completing the tree from bottom to top, each time I identify a third-level term:   

```php
// If has parent and has not children then is a third level term.
if((!empty($manager->loadParents($term->id()))) && (empty($manager->loadChildren($term->id()))) && (($normalized_naming === $normalized_district))) {

  // Third level Term.
  $array_of_tids[2] = $term->id();

  // Second level Term.
  $array_of_tids[1] = $parent->id();
  $parent = reset($manager->loadParents($term->id()));
  
  // First level Term.
  $grandparent = reset($manager->loadParents($parent->id());
  $array_of_tids[0] = $grandparent->id();
}
```
My goal is to finally have an array composed of three numeric values that will be the tids of the terms found. This array can be saved in the Entity Reference field for the taxonomy and finally when the node is saved, these taxonomy terms will be charged in every node.  

```php
$new_node->field_taxonomy_location = $array_of_tids;
$new_node->save();
```

Done!  


## 5- Read More 

- [Getting started with OpenStreetMap Nominatim API, by Adrián Espejo](https://medium.com/@adri.espejo/getting-started-with-openstreetmap-nominatim-api-e0da5a95fc8a).  
- [Open Search Nominatim API](https://developer.mapquest.com/documentation/open/nominatim-search/).  

## 6- :wq!

### Recommended song: Addio Lugano bella  

**Giorgio Gaber, Enzo Jannacci, Lino Toffolo, Otello Profazio and Silverio Pisu.**  

{% include youtubePlayer.html id=page.youtubeId %}

