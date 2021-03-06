# Rekt

Rekt is a python module for generically and dynamically generating
rest clients at run-time. Rekt tries to be as flexible as possible by
being a light and convenient wrapper around the venerable requests
module. rekt also supports asynchronous calls by utilizing
concurrent.futures.

**Rekt Requires:**

* Python 3.4+
* Requests
* PyYAML

## Why Use Rekt

The impetus for rekt is that the rest pattern for generating clients
is fairly standard. The one off libraries for various services have
their inconsistencies and they often lack Python3 support.

* It is a light weight code generation wrapper around requests. So at
  its core it supports what requests support. The exceptions thrown
  for bad requests will be the same as the exceptions from requests.

* Asynchronous when needed. All calls are generated with a synchronous
  and asynchronous call handler. Client objects contain a configurable
  concurrent.futures.ThreadPoolExecutor.

* Thread Safe. Some service specific rest libraries store context
  within client instance attributes in a way that does not allow for
  concurrent calls for mysterious reasons. State should be bound to
  the call, rekt was designed from the beginning to allow for
  concurrent calls.

* Generic. Rekt generates the code for clients at runtime from yaml
  configurations. Only the request parameter specifications need to be
  specified before hand. Rekt utilizes a dict subclass called
  ***DynamicObject*** as both the base Response class and as the
  object hook when deserializing the json responses in order to
  capture all of the parameters of the response as dot accessible
  object. For those that prefer it, since ***DynamicObject*** is also
  a dict subclass all of the normal dictionary methods are also
  available.


**Example Using Rekt:**

```
  >>> from rekt import utils
  >>> from rekt import load_service

  >>> conf = utils.load_builtin_config('googleplaces')
  >>> googleplaces = load_service(conf)

  >>> client = googleplaces.Client()
  >>> response = client.get_places(key=YOUR_API_KEY, location='47.6097,-122.3331', radius=1000)
  >>> print(response.keys())
  dict_keys(['html_attributions', 'results', 'next_page_token', 'status'])
  >>> my_place = response.results[0] # Note dot access, like normal objects
  >>> same_places = response['results'] # using keys works too!
  >>> print(response.__class__.__name__)
  GetPlacesResponse

  >>> import concurrent.futures
  >>> f = client.async_get_details(key=YOUR_API_KEY, placeid=my_place.place_id)
  <Future at 0x7f6880a52978 state=running>
  >>> f = next(concurrent.futures.as_completed([f]))
  >>> print(f.result().keys())
  dict_keys(['result', 'html_attributions', 'status'])
```

**Get Rekt!**