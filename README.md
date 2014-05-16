
# Lanyon, a markdown web server

Lanyon is a simple web server, which reads directories of markdown files and
converts them to HTML and serves. An intuitive and easy way to create a website. 

Lanyon was derived from my static site generator,
[Hastie](https://github.com/mkaz/hastie). I grew tired of the multiple steps for
creating, generating and publishing. Plus, using a static site generator has a
duplicate source tree and generated tree, with some assets in each. Lanyon
simplifies this with one set of directories with content and assets.

---

## Getting Started

#### Install

Binaries are available in the `binaries` folder for Mac OS X and Linux (amd64).
You can also build a binary from the available Go source code. 

The only dependency for building is the `github.com/russross/blackfriday`
library for markdown parsing. So to build yourself, assuming you have [Golang
installed](http://golang.org/doc/install):

```bash
$ git clone https://github.com/mkaz/lanyon
$ cd lanyon/src
$ go get github.com/russross/blackfriday
$ go build -o lanyon 
```

Once you have the binary built or downloaded, copy it to a directory within your
path, `~/bin/` or `/usr/local/bin/` are two great spots.


#### Run Site

A quick way to see what's going on is to check out the example site. You can run
the example site by running the binary from that directory. Lanyon looks for the
config file `lanyon.json` in the directory it was started in. The config file
tells it where to look for templates and what directory to serve content from.

```bash
$ cd lanyon/example
$ lanyon
>>> Lanyon listening on http://localhost:9999
```

You can now go to that URL and see the demo site running. All markdown files,
files with the extension `.md` are reference on the web server with .html
extension.

For example a call to `/giraffe.html` will first check to see if the html file
exists, if so it will serve the file. Otherwise it will check if `giraffe.md`
exists and convert that to HTML.

---

## Configuration

The configuration file is in JSON format, here is an example with all parameters
set:

```json
{
    "PortNum": 9999,
    "TemplateDir": "templates",
    "PublicDir": "public",
    "RedirectDomain": ["www.mkaz.com", "www.mkaz.com"]
}
```

**PortNum** - [required] the port number for the web server to listen to, if you
set to port 80 it will require being started by root user who has permission

**TemplateDir** - [required] the directory which holds the template files,
accepts a full path, or relative to lanyon start directory. Directories can
include the trailing slash.

**PublicDir** - [required] the directory which holds the main site's markdown
files. This is your web server root, all assets are also included in here.
Accepts a full path, or relative to lanyon start directory.

**RedirectDomain** - [optional] A tuple of domains which if specified, checks to
see if the requested domain matches the first, if the domain does not match,
issues a redirect to the second domain.

It would be common to include the same domain in both, to make sure all requests
go to the proper domain. In my production config, I use this parameter to redirect
all requests through the CDN, unless it is an origin request, so it looks like
["origin.mkaz.com", "www.mkaz.com"]


---

## Templates & Customizing

By far the easiest way to customize is to modify the style.css file to fit your
needs. The example templates produce a common blog markup, based off the open
source [WordPress Underscores theme](https://github.com/automattic/_s). 

For deeper customization, Lanyon uses Go Templates which is a relatively simply
templating language providing basic variable substitution and minimal logic. You
can modify the templates to fit your needs. See the full documentation at
http://golang.org/pkg/text/template/

Get started customizing by looking at the example templates in the repository. 
Examples exist for post and category templates. You can add your own template by
naming the file "custom.html" where "custom" is the name of your template.

You would refer to this template in the front matter of your page

        ---
        title: My Page Title
        date: 2005-02-01
        layout: custom
        ---

        My page content


Variables available to the template:

    .Title - title of page
    .Content - main body content of page
    .Category - directory file resides
    .Layout - template used for layout
    .Params - a map of key-value params


Lanyon will create a parameter out of any front matter variables it does not
recognize. This becomes a flexible way to control templates and customize
specific pages. Here's an example, using a parameter to include jQuery.

In the page you want to include jQuery, include the following front matter

    ---
    title: My jQuery Page
    jquery: yes
    ---

    This is a page that will include jQuery

And then within your template, you can use an if-else clause to check for the
jQuery Parameter and include.

    {{ if .Params.jquery }}
        <script src="/a/jquery.min.js"></script>
    {{ end }}

Note: Only the templates can use the templating language, variables and logic
are not available within the individual markdown pages.

---

## Error Page

When a page is not found, Lanyon looks for a file called `404.md` to serve as
the error page. If this page does not exist it will serve a plain error message.

---

## Less Support

Lanyon supports automatic compilation of Less files into CSS. This requires
`lessc` to be installed and then any files created with `.less` extension within
the public directory will automatically be compiled to css. You should access
them as `.css`. 

For example, the file `public/a/style.less` would be accessed through the web
server as `http://localhost:9999/a/style.css`

You can install `lessc` using the node.js package manager, npm. If you already
have node installed:

```bash
$ npm install -g less
```

See [lesscss.org](http://lesscss.org/) for more information.


---

## Performance

I think Lanyon will perform quite well on most servers. However, I recommend
setting up a reverse-proxy caching server such as
[Varnish](https://www.varnish-cache.org/) or [nginx](http://nginx.org/) in
front.  Additionally, you can use a CDN service such as [Amazon
CloudFront](http://aws.amazon.com/cloudfront/) to further improve serving
performance and offload load.

I use Lanyon to serve my personal site [mkaz.com](http://mkaz.com) without a
proxy cache, but with Amazon CloudFront as a CDN.

---

## About

Lanyon is licensed under MIT Open Source license, see LICENSE file for details.

I welcome any comments, suggestions, contributions or just well wishes. Using
github is probably easiest but if you want, you can email me at marcus@mkaz.com
or reach me on Twitter at [@mkaz](https://twitter.com/mkaz).

