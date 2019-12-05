<img src="website-speed.png" width="200px" height="200px" alt="Logo" align="right">

# Website Speed 

Beside Security, **speed** is one of the most important technical factors of a successul website!

Check how fast your website is:
* <a href="https://developers.google.com/speed/pagespeed/insights/" target="_blank">PageSpeed Insights</a>
* <a href="https://gtmetrix.com/" target="_blank">GTmetrix</a>
* <a href="https://www.webpagetest.org/" target="_blank">WebPageTest</a>


## Static Content - Compressed
Even it takes longer to update your website and it is a more complex deploy process, generate as much static compressed content as possible.

### Compression
* <a href="https://github.com/google/brotli">Brotli</a> is a compression algorithm developed by Google and serves best for text compression. <a href="https://caniuse.com/#feat=brotli" target="_blank">Browser support</a> is already very good!
If the browser supports Brotli, it will send a "br" the in accept-encoding request header.

* <a href="https://github.com/google/zopfli">Zopfli</a> is a compression algorithm for DEFLATE, gzip and zlib formats, developed by Google under the Apache License and it achieves much higher compression than other DEFLATE/zlib implementations (at the expense of speed)


```sh
for i in $(find . -type f \( -name "*.js" -o -name "*.css" -o -name "*.html" -o -name "*.webmanifest" \) ); do
        brotli -f ${i}
        zopfli --i10 ${i}
done
```

Apache config (tested Apache/2.4.x on Ubuntu)
```conf
Header append Vary Accept-Encoding

RewriteEngine on

RewriteCond %{HTTP:Accept-Encoding} br
RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.br -s
RewriteRule ^(.*)$ %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.br [E=no-gzip,L]

RewriteCond %{HTTP:Accept-Encoding} gzip
RewriteCond %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.gz -s
RewriteRule ^(.*)$ %{DOCUMENT_ROOT}/%{REQUEST_FILENAME}.gz [E=no-gzip,L]


```






## Serve WebP Images




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




 ToDo: image: fast loading image  for
>
>
>
> cwebp -m 6 -pass 10 -mt -q 80
> https://medium.com/@vinhlh/how-i-apply-webp-for-optimizing-images-9b11068db349
>
>
>
>




Content Negotiation.
https://www.igvita.com/2013/05/01/deploying-webp-via-accept-content-negotiation/
https://medium.com/@vinhlh/how-i-apply-webp-for-optimizing-images-9b11068db349


https://en.wikipedia.org/wiki/WebP
WebP is an image format employing both lossy and lossless compression. It is currently developed by Google, based on technology acquired with the purchase of On2 Technologies.
