# Component API Strategy
Discussions about maintainable component API architectures and style guides. 

# Priorities and Considerations
## Consumption UX 

### By access level
Internal/External

### By role
* Designers
* Marketing pros
* Devs on your product
* Devs on other teams within your product ecosystem

## Performance
* Prioritizing the first 14k of critical path, 1 sec of perceived load
* API call dependencies (i.e. if your platform relies on another API)

## Progressive enhancement
* semantic markup structure
* still works without JS
* offline - network is a layer of enhancement
* graded UX of components
* Mobile-first, responsive: device-agnostic
* Accessibility

## Ease of Maintainability, Versioning, Distribution
* Package managers (i.e. NPM)
* Component API (i.e. Rizzo)
* GitHub
* Do we keep compiled build files in the repo, in a separate repo, or out of SCM altogether?
* Which build tool do we use? NPM, Gulp, Grunt?
* How does a user update to a newer version?
* What happens when there is a conflict?
* Avoiding dependency hell, like this nonsense.
* Asset organization
* Readable, predictable, speakable URLs

## Separating content from UI
* Maybe the most difficult of all. At the very least, make the contribution model as simple as a pull request.
* CDN for static assets
    - Logos (use case: pointing someone to a vanity URL "example.com/assets/logo.svg", pointed from a cached CDN version)
    - Sprites? 

## Login state
Logged-in/non-authorized: which components need to change?

=====
## Alternative API structures
### On-demand assembly
i.e. Google Fonts: `<link href='http://fonts.googleapis.com/css?family=Roboto:400,300,700' rel='stylesheet' type='text/css'>`  
Not a great example: `<link href='http://style.myapi.com/v1/ui/?components=buttons:login,cancel,submit' rel='stylesheet' type='text/css'>`

### Custom Objects
The [Google Font JS solution](https://github.com/typekit/webfontloader#google) involves a JSON object that allows you to do character subsetting. 

**Example**
```javascript
WebFontConfig = {
  google: {
    families: ['Droid Sans', 'Droid Serif'],
    text: 'abcdedfghijklmopqrstuvwxyz!'
  }
};
```

### Faceted API URLs
Typekit's API uses a faceted API URL that returns a JSON/P object about the date requested by the call. 
The format follows the following convention: `https://typekit.com/docs/api/v1/:format/families/:family/:variation` 

## Example
`GET https://typekit.com/api/v1/json/families/droid-sans/n4`
returns
```json
{
  "variation": {
    "id": "gkmg:n4",
    "name": "Droid Sans Regular",
    "family": {
      "id": "gkmg",
      "link": "/api/v1/json/families/gkmg",
      "name": "Droid Sans"
    },
    "font_style": "normal",
    "font_variant": "normal",
    "font_weight": "400",
    "foundry": {
      "name": "Google Android",
      "slug": "google-android",
    },
    "libraries": [
      {
        "id": "trial",
        "link": "/api/v1/json/libraries/trial",
        "name": "Trial Library"
      },
      {
        "id": "personal",
        "link": "/api/v1/json/libraries/personal",
        "name": "Personal Library"
      }
      {
        "id": "full",
        "link": "/api/v1/json/libraries/full",
        "name": "Full Library"
      }
    ],
    "postscript_name": "DroidSans"
  }
}
```

What if we could do the same for components?
`GET https://style.myapi.com/v1/ui/components/links/default`
returns
```json
{
  "variations": {
    "id": "myapi:v1/links",
    "name": "Link Styles"
    },
    "sets": [
      {
        "id": "basic",
        "link": "/api/v1/ui/sets/basic",
        "name": "Basic Styles Only"
      },
      {
        "id": "third-party",
        "link": "/api/v1/ui/sets/third-party",
        "name": "Third Party"
      }
      {
        "id": "Full",
        "link": "/api/v1/ui/sets/full",
        "name": "Full UI component set and styles"
      }
    ]
  }
}
```

## Single point URLs returning component metadata
**Example**  
`GET http://style.myapi.com/v1/ui/?components=buttons`
Returns:
```json
{
    "assets": {
        "css": "http: //style.myapi.com/v1/ui/buttons/buttons-default.css",
        "js": "http: //style.myapi.com/v1/ui/buttons/buttons-default.css",
        "icons": "http: //style.myapi.com/v1/ui/buttons/buttons-default.svg"
    }
}
```
*there's probably a smarter way to avoid redundancy in the URLs. Maybe something like [<base>](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/base)
which could be implemented server side in a template using `link.assets.css` or something.

More to come. Just wanted to get the discussion going.
