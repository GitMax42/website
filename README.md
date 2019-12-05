<div style="height:200px; display: block; background: red;">
  <img src="website-speed.png" width="200px" height="200px" alt="Logo" align="right">

Beside Security, **speed** is one of the most important factors of a successul website!

Below are some techniques and tips to optimize your website to make it faster. 
</div>



# Check how fast your website is
* [PageSpeed Insights](https://developers.google.com/speed/pagespeed/insights/)
* [GTmetrix](https://gtmetrix.com/)
* [WebPageTest](https://www.webpagetest.org/)


# Static Content
# Serve WebP Images




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



https://github.com/ManfredGruber



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
