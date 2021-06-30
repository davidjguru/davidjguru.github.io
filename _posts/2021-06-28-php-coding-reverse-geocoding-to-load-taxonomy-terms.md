---
layout: post
title: 'PHP Coding: Reverse Geocoding loading taxonomy terms' 
permalink: /blog/php-coding-reverse-geocoding-loading-taxonomy-terms
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

  
* [Drupal 8, 9 - Reverse Geocoding using external Service from PHP](https://gist.github.com/davidjguru/0ba7b135ae2d9738278c5dff2a311e09)  
* [Drupal 8,9 - Getting Taxonomy Terms from different levels programmatically ](https://gist.github.com/davidjguru/3b9d36bf3e00dd338d751b7bfa2c41eb)  
* [Drupal 8,9 - Populating taxonomy with hierarchical structure from external geocoding ](https://gitlab.com/-/snippets/2137785)  

---------------------------------------------------------------------------------------
<!-- /TOC -->
**Table of Contents.**

[1- What am I looking for?](https://davidjguru.github.io/blog/drupal-fast-tips-using-links-in-drupal-8)  
[2- Reverse Geocoding for PHP](https://davidjguru.github.io/blog/drupal-fast-tips-prefilling-fields-in-forms)  
[3- Options for Reverse Geocoding](https://davidjguru.github.io/blog/drupal-fast-tips-the-magic-of-attached)  
[4- Loading taxonomy terms](https://davidjguru.github.io/blog/drupal-fast-tips-placing-a-block-by-code)  
[5- :wq!](https://davidjguru.github.io/blog/drupal-fast-tips-from-array-to-html)  
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

### Getting data for Peru
In Peru there are 24 departments, 25 regions, 196 provinces and 1838 districts. I need to save Department, Province and Districts for every item.  In the former example I will catch the data from the Nominatim reverse endpoint, where Department="Ancash" , Province="Huarmey"  District="Culebras", adapting this:  

- Department=state  
- Province=region  
- District=village  

### Mounting the new container  

```bash
$ docker run -it --rm \
  -e PBF_URL=https://download.geofabrik.de/south-america/peru-latest.osm.pbf \
  -e REPLICATION_URL=https://download.geofabrik.de/south-america/peru-updates/ \
  -p 8080:8080 \
  --name nominatim \
  mediagis/nominatim:3.7
```

### Resolving queries  

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
### Loading the whole taxonomy  

```php
[...]
// Load the whole taxonomy tree using values.
$manager = \Drupal::entityTypeManager()->getStorage('taxonomy_term');
$taxonomy_tree = $manager->loadTree(
        'departamentos', // The taxonomy term vocabulary machine name.
        0,               // The "tid" of parent using "0" to get all.
        3,               // Get all available level.
        TRUE             // Get full load of taxonomy term entity.
        );
[...]
```


## 5- :wq!

### Recommended song: Addio Lugano bella  

**Giorgio Gaber, Enzo Jannacci, Lino Toffolo, Otello Profazio and Silverio Pisu.**  

{% include youtubePlayer.html id=page.youtubeId %}

