```
          _______ _________ _       ______   _______  _______  _        _______  _______  _______ 
         (  ___  )\__   __/( )     (  ___ \ (  ___  )(  ____ \| \    /\(  ___  )(  ____ \(  ____ \
         | (   ) |   ) (   | |     | (   ) )| (   ) || (    \/|  \  / /| (   ) || (    \/| (    \/
         | |   | |   | |   | |     | (__/ / | (___) || |      |  (_/ / | |   | || (__    | (__    
         | |   | |   | |   | |     |  __ (  |  ___  || |      |   _ (  | |   | ||  __)   |  __)   
         | |   | |   | |   (_)     | (  \ \ | (   ) || |      |  ( \ \ | |   | || (      | (      
         | (___) |___) (___ _      | )___) )| )   ( || (____/\|  /  \ \| (___) || )      | )      
         (_______)\_______/(_)     |/ \___/ |/     \|(_______/|_/    \/(_______)|/       |/       
                                                                                                  
```

oibackoff - backoff functionality for any : fn(function(err, data) { ... });

[![Build Status](https://secure.travis-ci.org/appsattic/oibackoff.png?branch=master)](http://travis-ci.org/appsattic/oibackoff)

## Features ##

* three different backoff algorithms: exponential, fibonacci and linear
* max number of tries
* max time to wait for any retry
* scaling of the delay between tries

Your code can stay the same plus you also get extra information about intermediate errors.

## Examples ##

Original code:

```
var dns = require('dns');

// original code
dns.resolve('chilts.org', function(err, addresses) {
    if (err) {
        // do something to recover from this error
        return;
    }

    // do something with addresses
    console.log(addresses);
});

```

Using exponential backoff, with a maxium of 5 tries, with delays of 0.2, 0.4, 0.8, 1.6 and 3.2 seconds is fairly
similar and you can reuse the 'backoff' function many times:

```
var backoff = require('oibackoff').backoff({
    algorithm  : 'exponential',
    delayRatio : 0.2,
    maxTries   : 5,
});

backoff(dns.resolve, 'chilts.org', function(err, addresses, priorErrors) {
    if (err) {
        // do something to recover from this error
        return;
    }

    // do something with addresses
    console.log(addresses);
});
```

Notes:

* 'err' contains the last error encountered (if maxTries was reached without success)
* 'addresses' contrains the same as the original upon success, or null if all attempts failed
* 'priorErrors' is informational and you may ignore it or use it to help you diagnose problems

## Options ##

### maxTries ###

Default: 3

Will retry a maximum number of times. If you don't want a maxiumum, set this to 0.

### delayRatio ###

Default : 1

This is the ratio for each delay between each try (in seconds).

If you choose the exponential algorithm, then 1s delayRatio will result in delays of 1, 2, 4, 8 etc

### algorithm ###

Default : 'exponential'

Valid Values : exponential, incremental, fibonacci ;)

### maxDelay ###

No Default.

If your chosen backoff strategy reaches a point which is above this number, then each succesive retry will top-out at
'maxDelay' e.g. if you choose 'exponential', with a delayRatio of 1 and maxTries at 10, the retry delays will be 1, 2,
4, 8, 10, 10, ... (instead of 1, 2, 4, 8, 16, 32, ...).

## Example Backoff Stategies ##

```
var oibackoff = require('oibackoff');

// 0.4, 0.8, 1.6, 3.2, 6.4, ...
var backoff = oibackoff.backoff({
    algorithm  : 'exponential',
    delayRatio : 0.4,
});

// 1, 2, 3, 4, 5, ...
var backoff = oibackoff.backoff({
    algorithm  : 'incremental',
    delayRatio : 1,
});

// 0.5, 1.0, 1.5, 2.5, 4, ...
var backoff = oibackoff.backoff({
    algorithm  : 'fibonacci',
    delayRatio : 0.5,
});
```

## Author ##

Written by: [Andrew Chilton](http://chilts.org/) - [Blog](http://chilts.org/blog/) -
[Twitter](https://twitter.com/andychilton).

## License ##

The MIT License : http://opensource.org/licenses/MIT

Copyright (c) 2011-2012 AppsAttic Ltd. http://appsattic.com/

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated
documentation files (the 'Software'), to deal in the Software without restriction, including without limitation the
rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit
persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the
Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE
WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR
OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
