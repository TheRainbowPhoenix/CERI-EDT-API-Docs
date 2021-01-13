# Trivia
- The dosi-api is coded in `php`
- It is powered by `Luracast Restler v3.0.0rc5`
- You'll get a `Pas les droits` if you try to query it with your own account. You'll have to use a custom account I'll document about later.

# Basics
The DOSI API is located at `https://api-dosi.univ-avignon.fr` for the prod version and `https://api-dosi-test.univ-avignon.fr` for the testing one.

## The base url
There're many services under this API. The common way to access one is querying this way:
`https://api-dosi{{env}}.univ-avignon.fr/{{api}}/v{{version}}/`

Where:

| field        | type                | description |
| -----------: | :------------------ | :---------- |
| env          | String or empty     | The env prefix. Currently only `-test` and `` are used according to my searches |
| api          | String              | The service you'd like to query. See the list for more. |
| version      | int (default 1)     | The version of the API |

Among all the services, I've found thoses being used:
- `appig`
- `partage`

## Make your first request 
In order to do anything you'll need to grab a token from the service you'd like to use. It's pretty easy once you known the url:
`https://api-dosi.univ-avignon.fr/{{api}}/v{{version}}/Token/getToken`, and for the (current at the time of this writing, 2021) version of partage : 
`https://api-dosi.univ-avignon.fr/partage/v1/Token/getToken`.
You'll have to post a payload with the md5-hashed (in hex) of the password. I've write this simple PoC:
```python
import requests
from urllib import parse

url = "https://api-dosi.univ-avignon.fr/partage/v1/Token/getToken"
payload = {
    'user': 'username_here',
    'passClient': php_md5('password_here')
}
r = requests.post(url, data=payload)
print(r.text)
```
with the `php_md5` looking like this:
```python
import hashlib

def php_md5(text: str) -> str:
    result = hashlib.md5(text.encode())
    return result.hexdigest()
```

Then you should get a token, ending with `_PROD` that you'll re-use for any future request in the header at `token`, like this:
`token: "XXXXXXXXXXXXX_PROD"`

## About the structure
This API is very RPC-likely, so the routes works that way:
`https://api-dosi{{env}}.univ-avignon.fr/{{api}}/v{{version}}/{{class}}/{{method}}`
Where class will be very likely to begins with an uppercase letter, like "Calendar". 

If you do GET, simply add the elements at the end of the URL in the old-HTML way: `&key=value`. 

# Sources
- Reverse DNS scannings
- [UAPV](https://github.com/UAPV) for parts of the source code
