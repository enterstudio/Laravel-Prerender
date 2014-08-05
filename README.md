Larevel Prerender [![Build Status](https://travis-ci.org/JeroenNoten/Laravel-Prerender.svg?branch=master)](https://travis-ci.org/JeroenNoten/Laravel-Prerender)
=========================== 

Google, Facebook, Twitter, Yahoo, and Bing are constantly trying to view your website... but they don't execute javascript. That's why Prerender is built. Prerender is perfect for AngularJS SEO, BackboneJS SEO, EmberJS SEO, and any other javascript framework.

This middleware intercepts requests to your Laravel website or application from crawlers, and then makes a call to the (external) Prerender Service to get the static HTML instead of the javascript for that page.

Prerender adheres to google's `_escaped_fragment_` proposal, which we recommend you use. It's easy:
- Just add `<meta name="fragment" content="!">` to the `<head>` of all of your pages
- If you use hash urls (#), change them to the hash-bang (#!), but you can also use HTML5's push-state
- That's it! Perfect SEO on javascript pages.

## Installation

Require this package in your composer.json and run composer update (or run `composer require nutsweb/laravel-prerender:dev-master` directly):

    "nutsweb/laravel-prerender": "dev-master"

After updating composer, add the ServiceProvider to the providers array in app/config/app.php.

    'Nutsweb\LaravelPrerender\LaravelPrerenderServiceProvider',

Publish the configuration file.

    $ php artisan config:publish nutsweb/laravel-prerender

If you want to make use of the prerender.io service, fill in your token in the configuration file.
    
    // config.php
    'prerender_token' => 'YOUR-TOKEN',
    
If you are using a self-hosted service, change the server address in the configuration file.

    // config.php
    'prerender_url' => 'http://example.com',

By default, the service is disabled for `local` environments. If you want, you can change this in `config/packages/nutsweb/laravel-prerender/local/config.php`.

## How it works
1. The middleware checks to make sure we should show a prerendered page
	1. The middleware checks if the request is from a crawler (`_escaped_fragment_` or agent string)
	2. The middleware checks to make sure we aren't requesting a resource (js, css, etc...)
	3. (optional) The middleware checks to make sure the url is in the whitelist
	4. (optional) The middleware checks to make sure the url isn't in the blacklist
2. The middleware makes a `GET` request to the [prerender service](https://github.com/prerender/prerender) (phantomjs server) for the page's prerendered HTML
3. Return that HTML to the crawler

# Customization

### Whitelist

Whitelist paths or patterns. You can use asterix syntax, or regular expressions (without start and end markers).
If a whitelist is supplied, only url's containing a whitelist path will be prerendered.
An empty array means that all URIs will pass this filter.
Note that this is the full request URI, so including starting slash and query parameter string.

```php
// config.php:
'whitelist' => [
    '/frontend/*' // only prerender pages starting with '/frontend/'
],
```

### Blacklist

Blacklist paths to exclude. You can use asterix syntax, or regular expressions (without start and end markers).
If a blacklist is supplied, all url's will be prerendered except ones containing a blacklist path.
By default, a set of asset extentions are included (this is actually only necessary when you dynamically provide assets via routes).
Note that this is the full request URI, so including starting slash and query parameter string.

```js
// config.php:
'blacklist' => [
    '/api/*' // do not prerender pages starting with '/api/*'
],
```

## Contributing

I love any contributions! Feel free to create issues or pull requests.

## License

The MIT License (MIT)

Copyright (c) 2013 Todd Hooper &lt;todd@prerender.io&gt;

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
