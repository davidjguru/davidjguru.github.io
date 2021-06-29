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

Well, this month I've been doing some things I haven't practiced for some time: data migrations, intensive connections to Gitlab API v4 and even some web-scraping to get data. Along the way I've recovered some old PHP functions that I had forgotten or hadn't used for a long time. As I didn't want to forget them again, I took this notebook as a reference and wrote down a small post with some functions of our old friend PHP that can give us a little help in the day to day.  
<!--more-->

The old man PHP has over 1000 built-in functions that can be called and used directly from your code and will be independient of the platform/CMS/framework (Drupal, Symfony, Laravel...) so you can load the functions in your scripts or classes in order to execute specific tasks. I've given a little context to each one and possible uses. You probably already know them or they seem very obvious to you, but I thought it was fun to share them. I'm sure someone might find them useful.  

**Note:** This month (may 2021) I've published an article with a review about [the book '31 Days of Drupal Migrations' in The Russian Lullaby](https://www.therussianlullaby.com/blog/books-31-days-of-drupal-migrations). This could be interesting for you.  



## 1- What is "Reverse Geocoding"?

> "Reverse geocoding is the process of converting a location as described by geographic coordinates (latitude, longitude) to a human-readable address or place name. It is the opposite of forward geocoding."  

Source: [Reverse Geocoding in Wikipedia](https://en.wikipedia.org/wiki/Reverse_geocoding)  

## 2- Options for Reverse Geocoding  

### Installing geocoder for PHP  

Installing in my Drupal local deploy:  

```bash 
$ ddev composer require willdurand/geocoder
$ ddev composer require geocoder-php/locationiq-provider 
```


Or whithout ddev if you're out of this tooling:  

```bash
$ composer require willdurand/geocoder
$ composer require geocoder-php/locationiq-provider 
```

Now I have now the classes available through the class loader. I can use the resources in my installation. Geocoder only requires just a pair of keyresources:

- An external provider
- A HTTP Client for connections.

I have the provider and I have the HTTP Client too, Guzzle library, present and available in Drupal. So what I need now is an external service.  


### External Services  

I'm using the classical https://geocoder-php.org library. It requires the main library and the external providers classes in order to connect to others external APIs.
In this case I'm using LocationIQ to get the addresses:  https://locationiq.com in free mode. I'm getting a token API for queries.  

Some Limitations:  

- 1 Access Token
- 5,000 request/day
- 2 request/second

### Making External Reverse Geocoding Queries 

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


## 3- Resources 

### External Public Resources 

There are some public resources opened to connections, like:
http://www.cartociudad.es/geocoder/api/geocoder/reverseGeocode?lon=-0.562854&lat=39.918735  

### What about Drupal 

In Drupal you can use the [Drupal Geocoder Module](https://www.drupal.org/project/geocoder), a contrib module that acting like a wrapper for geocoder library and resolves by itself the installation of the main library, as you can see in: [geocoder/composer.json](https://git.drupalcode.org/project/geocoder/-/blob/8.x-3.x/composer.json).  

You have to follow the instructions from its README file: [geocoder/README.md](https://git.drupalcode.org/project/geocoder/-/blob/8.x-3.x/README.md).  

There are some weird issues installing providers, see the [Issue 3153678](https://www.drupal.org/project/geocoder/issues/3153678).  


### Nominatim 

> "Nominatim is the geocoding software that powers the official OSM site www.openstreetmap.org. It serves 30 million queries per day on a single server."

Source: [https://nominatim.org](https://nominatim.org/)  

Nominatim based in Docker:
https://github.com/mediagis/nominatim-docker
Selecting Region: 
https://download.geofabrik.de/
Selecting Sub-Region:
https://download.geofabrik.de/south-america.html
Selecting Country: 
https://download.geofabrik.de/south-america/peru.html
Selecting Continent:  
https://download.geofabrik.de/europe/
Main Repository::
https://github.com/osm-search/Nominatim
Nominatim API overview::
https://nominatim.org/release-docs/develop/api/Overview/



Endpoints for queries

```bash
/search - search OSM objects by name or type
/reverse - search OSM object by their location
/lookup - look up address details for OSM objects by their ID
/status - query the status of the server
/deletable - list objects that have been deleted in OSM but are held back in Nominatim in case the deletion was accidental
/polygons - list of broken polygons detected by Nominatim
/details - show internal details for an object (for debugging only)
```

https://nominatim.openstreetmap.org/reverse?lat=<value>&lon=<value>&<params>
https://nominatim.openstreetmap.org/reverse?format=json&lat=-12.046374&lon=-77.042793
https://nominatim.openstreetmap.org/reverse?format=json&lat=-9.949480903131423&lon=-78.22368622836807


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

## 4- Loading taxonomy terms 


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

## :wq!

### Recommended song: Addio Lugano bella  

**Giorgio Gaber, Enzo Jannacci, Lino Toffolo, Otello Profazio and Silverio Pisu.**  

{% include youtubePlayer.html id=page.youtubeId %}

