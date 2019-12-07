<img src="website-speed-1280x1280.png" width="150" height="150" alt="Logo" align="right">


## Table of Contents
* [How fast is your website?](#how-fast-is-your-website)
* [Static Pre-compressed Content](#static-pre-compressed-content)
* [WebP Images](#webp-images)
* [Minification](#minification)

<br/>

<br/>

## How fast is your website?
First things first - check the current status of your website! There are a lot of online tools for that, below are my favorits: 
* <a href="https://developers.google.com/speed/pagespeed/insights/" target="_blank">PageSpeed Insights</a>
* <a href="https://gtmetrix.com/" target="_blank">GTmetrix</a>
* <a href="https://www.webpagetest.org/" target="_blank">WebPageTest</a>

<br/>


## Static Pre-compressed Content
Even the deploy process is more complex and the update of the website takes longer, you need to generate as much static pre-compressed content as possible to avoid compressing on every request!
Textfiles like HTML, CSS, Javascript, JSON, XML and Webmanifest are good candidates for such static compression.

#### Compression
* <a href="https://github.com/google/brotli">Brotli</a> is a compression algorithm developed by Google and it's <a href="https://caniuse.com/#feat=brotli" target="_blank">Browser support</a> is already very good!
If the browser supports Brotli, it will send a "br" in the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Encoding" target="_blank">accept-encoding</a> <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers" target="_blank">request header</a>.

* <a href="https://github.com/google/zopfli">Zopfli</a> is a compression algorithm for DEFLATE, gzip and zlib formats, developed by Google under the Apache License and it achieves much higher compression than other DEFLATE/zlib implementations (at the expense of speed).

*Comparison of compression methods (examples from [wien52.at](https://wien52.at/) 12/2019)

| File | Compression | Size [bytes] |
|:---|:---:|---:|
| index.html    |     none    |        68616 |
| index.html.br |    brotli   |        12691 |
| index.html.gz |    zopfli   |        14293 |
| index.css     |     none    |        22703 |
| index.css.br  |    brotli   |         4278 |
| index.css.gz  |    zopfli   |         4626 |
| index.js      |     none    |       128443 |
| index.js.br   |    brotli   |        30541 |
| index.js.gz   |    zopfli   |        33231 |

<br>

*Shellscript to generate .gzip and .br versions of files:*
```bash
cd [to-the-www-directory]
for i in $(find . -type f \( -name "*.js" -o -name "*.css" -o -name "*.html" -o -name "*.webmanifest" \) ); do
        brotli -f ${i}
        zopfli --i50 ${i}
done
```
<br>

*Configure Apache to serve pre-compressed files if they exisit (tested on Apache/2.4.x, Ubuntu):*
```apache
# Encoding for Brotli files
AddEncoding br .br

# Set gzip encoding instead of setting as a Content Type
RemoveType .gz
AddEncoding x-gzip .gz

<FilesMatch "\.(css|html|js)\.br$">
	# Prevent mime module to set brazilian language header (because the file ends with .br)
	RemoveLanguage .br

	# Set encoding type
	Header set Content-Encoding br

	# Force proxies to cache brotli & non-brotli files deperate
	Header append Vary Accept-Encoding

</FilesMatch>

<FilesMatch "\.(css|html|js)\.gz$">
	# Set encoding type
	Header set Content-Encoding gzip

	# Force proxies to cache gzip & non-gzip files deperate
	Header append Vary Accept-Encoding
</FilesMatch>

# Mapping foo.suffix.gz or foo.suffix.br => Type
AddType "text/html" .html.br .htm.br .html.gz .htm.gz
AddType "text/css" .css.br css.gz
AddType "text/javascript" .js.br .js.gz
AddType "application/webmanifest+json" .webmanifest.br .webmanifest.gz

RewriteEngine on

RewriteCond %{HTTP:Accept-Encoding} br
RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.br -s
RewriteRule ^(.*)$ %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.br [E=no-gzip,L]

RewriteCond %{HTTP:Accept-Encoding} gzip
RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.gz -s
RewriteRule ^(.*)$ %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.gz [E=no-gzip,L]
```

You can have mod_brotli activated, it will compress the content that is not pre-compressed.


<br/>


## WebP Images
Serve WebP Images - coming soon...

<br/>


## Minification
coming soon...


<!--
Make the Web Faster: 
Serve WebP Images to Speed Up Your Website

WebP is an image format for lossy and lossless compression, currently developed by Google.

*** example cwebp aufruf
*** Statistik w52 Vergleich aller xxxxx JPEGs mit same Quality Index zu webp

Usage:
1) as HTML5 picture element
<picture>
  <source srcset="logo.webp" type="image/webp">
  <img src="logo.png" alt="logo">
</picture>
2) transparent, via Apache mod_rewrite
*** example von w52



WebP wikipedia documentation: https://en.wikipedia.org/wiki/WebP
WebP documentation from Google: https://developers.google.com/speed/webp
WebP CLI tool documentation from Google: https://developers.google.com/speed/webp/docs/cwebp
WebP browser support: https://caniuse.com/#search=webp



cwebp -m 6 -pass 10 -mt -q 80
https://medium.com/@vinhlh/how-i-apply-webp-for-optimizing-images-9b11068db349



Content Negotiation.
https://www.igvita.com/2013/05/01/deploying-webp-via-accept-content-negotiation/
https://medium.com/@vinhlh/how-i-apply-webp-for-optimizing-images-9b11068db349


https://en.wikipedia.org/wiki/WebP
WebP is an image format employing both lossy and lossless compression. It is currently developed by Google, based on technology acquired with the purchase of On2 Technologies.

-->

