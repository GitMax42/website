# 🆆🅴🅱🆂🅸🆃🅴 - 🆂🅿🅴🅴🅳

![image](https://github.com/ManfredGruber/website/blob/master/assets/website-speed-v2.png)
Beside [Security](https://developer.mozilla.org/en-US/docs/Web/Security), **speed** is one of the most important technical factors for successful websites! Below are some tips and techniques to speed up your website.

<br/><br/>

## Table of Contents
* [How fast is your website?](#how-fast-is-your-website)
* [Pre-built and Pre-compressed Content](#static-pre-compressed-content)
* [Serve WebP Images](#serve-webp-images)
* [Minification](#minification)


<br/><br/>


# How fast is your website?
First things first - check the current status of your website! There are a lot of online tools for that, below are my favorits: 
* <a href="https://developers.google.com/speed/pagespeed/insights/" target="_blank">PageSpeed Insights</a>
* <a href="https://gtmetrix.com/" target="_blank">GTmetrix</a>
* <a href="https://www.webpagetest.org/" target="_blank">WebPageTest</a>


<br/><br/>


# Pre-built and Pre-compressed Content
Even the deploy process is more complex and every update takes longer, you need to generate as much pre-built and pre-compressed content as possible to avoid compressing on every request. When it comes to minimising the time to first byte, nothing beats pre-built files.
<br/>Text files like HTML, CSS, Javascript, JSON, XML and Webmanifest are good candidates for compression.
<br/>The list of Static Site Generators (some for the now very popular [JAMstack](https://www.staticgen.com/) ) is very long but it does not matter how you generate your files - I user [vim](https://www.vim.org/), [zsh](https://en.wikipedia.org/wiki/Z_shell), [make](https://www.gnu.org/software/make/) and [perl](https://www.perl.org/) to be as framework independent as possible.

### Compression
* <a href="https://github.com/google/brotli">Brotli</a> is a compression algorithm developed by Google and it's <a href="https://caniuse.com/#feat=brotli" target="_blank">Browser support</a> is already very good!
If the browser supports Brotli, it will send a "br" in the <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Accept-Encoding" target="_blank">accept-encoding</a> <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers" target="_blank">request header</a>.
* <a href="https://github.com/google/zopfli">Zopfli</a> is a compression algorithm for DEFLATE, gzip and zlib formats, developed by Google under the Apache License and it achieves much higher compression than other DEFLATE/zlib implementations (at the expense of speed).

<br/>

*Comparison of compression methods (examples from [wien52.at](https://wien52.at/) 12/2019)*

| File | Compression | Size [bytes] |
|:---|:---:|---:|
| index.html    |     none    |        68620 |
| index.html.gz |     gzip    |        15157 |
| index.html.gz |    zopfli   |        14312 |
| index.html.br |    brotli   |        12697 |
| index.css     |     none    |        22703 |
| index.css.gz  |     gzip    |         4889 |
| index.css.gz  |    zopfli   |         4626 |
| index.css.br  |    brotli   |         4278 |
| index.js      |     none    |       128374 |
| index.js.gz   |     gzip    |        34259 |
| index.js.gz   |    zopfli   |        33218 |
| index.js.br   |    brotli   |        30511 |

<br/>

*Shell script to generate .gzip and .br versions of files:*
```bash
cd [to-the-www-directory]
for i in $(find . -type f \( -name "*.js" -o -name "*.css" -o -name "*.html" -o -name "*.webmanifest" \) ); do
        brotli -f ${i}
        zopfli --i50 ${i}
done
```
<br>

*Configure Apache to serve pre-compressed files if they exist (tested on Apache/2.4.x, Ubuntu):*
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

Remark: you can have mod_brotli activated, it will compress the content that is not pre-compressed.


<br/><br/>


# Serve WebP images
![image](https://upload.wikimedia.org/wikipedia/commons/thumb/0/06/WebPLogo.svg/200px-WebPLogo.svg.png) [WebP](https://en.wikipedia.org/wiki/WebP) is an image format for lossy and lossless compression, developed by Google and widely supported by browsers, as you can see from [caniuse](https://caniuse.com/#feat=webp). 
Files are typically smaller in size compared to other formats at equivalent SSIM (Structural similarity) index, so perfect to save some bytes.


### How to generate WebP images
There are a different tools/application to generate WebP images, if you like to generate it via CLI, than 
[Google developers portal](https://developers.google.com/speed/webp/docs/using)
is the best place. The example below shows a cwebp call on CLI:

```sh
cwebp -m 6 -pass 10 -mt -q 70 image.jpeg -o output.webp
#       │      │     │    └-> quality
#       │      │     └-> multithreading for speed improvements
#       │      └-> maximizing the amount of analysis pass
#       └-> slowest compression method to get the best compression
```


### There are 2 ways using WebP
#### 1. As [HTML](https://developer.mozilla.org/en-US/docs/Web/HTML/) [img](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/img) / [picture](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture) element, or within [CSS background-image](https://developer.mozilla.org/en-US/docs/Web/CSS/background-image).
The example below shows a typical html picture element:
```html5
<picture>
  <source srcset="logo.webp" type="image/webp">
  <img src="logo.png" alt="logo">
</picture>
```

2. Transparent via Apache mod_rewrite
This really smart way is based on [Content Negotiation](https://en.wikipedia.org/wiki/Content_negotiation) 
with the big advantage, that it can be used in parallel to the existing implementation. That means, you do not need to convert all your images at once, as this method is transparent for the client web-browser.
The example below shows the relevant [Apache web-server](https://httpd.apache.org/) configuration for WebP on [wien52.at](https://wien52.at):

```apache
<IfModule mod_setenvif.c>
  SetEnvIf Request_URI "\.(jpe?g|png)$" REQUEST_image
</IfModule>

<IfModule mod_rewrite.c>
  RewriteEngine On
  RewriteCond %{HTTP_ACCEPT} image/webp
  RewriteCond %{DOCUMENT_ROOT}/$1.webp -f
  RewriteRule (.+)\.(jpe?g|png)$ $1.webp [T=image/webp]
</IfModule>

<IfModule mod_headers.c>
  Header append Vary Accept env=REQUEST_image
</IfModule>

AddType image/webp .webp
```

<!--

Content Negotiation.
https://www.igvita.com/2013/05/01/deploying-webp-via-accept-content-negotiation/
https://medium.com/@vinhlh/how-i-apply-webp-for-optimizing-images-9b11068db349

-->


<br/><br/>


# Minification
coming soon...

