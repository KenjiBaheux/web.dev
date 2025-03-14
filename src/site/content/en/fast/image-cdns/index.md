---
layout: post
title: Use image CDNs to optimize images
authors:
  - katiehempenius
  - jlwagner
  - tunetheweb
description: |
  Image CDNs are excellent at optimizing images.
  Switching to an image CDN can yield a 40–80% savings in image bytes.
date: 2019-08-14
updated: 2023-03-08
codelabs:
  - install-thumbor
tags:
  - performance
---

## Why use an image CDN?

Image content delivery networks (CDNs) are excellent at optimizing images. Switching to an image CDN can yield a [40–80% savings](https://www.youtube.com/watch?v=YJGCZCaIZkQ&t=1010s) in image file size. In theory, it's possible to achieve the same results using only build scripts, but it's rare in practice.

## What's an image CDN?

Image CDNs specialize in the transformation, optimization, and delivery of images. You can also think of them as APIs for accessing and manipulating the images used on your site. For images loaded from an image CDN, an image URL indicates not only which image to load, but also parameters like size, format, and quality. This makes it easy to create variations of an image for different use cases.

<figure>
  {% Img src="image/admin/OIF2VcXp8P6O7tQvw53B.jpg", alt="Shows the request/response flow between the image CDN and the client. Parameters like size and format are used to request variations of the same image.", width="800", height="408" %}
  <figcaption>
    Examples of transformations image CDNs can perform based on parameters in image URLs.
  </figcaption>
</figure>

Image CDNs are different from build-time image optimization scripts in that they create new versions of images as they're needed. As a result, CDNs are generally better suited to creating images that are heavily customized for each individual client than build scripts are.

## How image CDNs use URLs to indicate optimization options

Image URLs used by image CDNs convey important information about an image and the transformations and optimizations that should be applied to it. URL formats will vary depending on image CDN, but at a high-level, they all have similar features. Let's walk through some of the most common features.

<figure>
  {% Img src="image/admin/GA4udXeYUEjHSY4N0Qew.jpg", alt="Image URLs typically consist of the following components: origin, image, security key, and transformations.", width="800", height="127" %}
</figure>

### Origin

An image CDN can live on your own domain or the domain of your image CDN. Third-party image CDNs typically offer the option of using a custom domain for a fee. Using your own domain makes it easier to switch image CDNs at a later date because no URL changes will be needed.

The example above uses the image CDN's domain ("example-cdn.com") with a personalized subdomain, rather than a custom domain.

### Image

Image CDNs can usually be configured to automatically retrieve images from their existing locations when they're needed. This capability is often achieved by including the complete URL of the _existing image_ within the URL for the image generated by the image CDN. For example, you might see a URL that looks like this: `https://my-site.example-cdn.com/https://flowers.com/daisy.jpg/quality=auto`. This URL would retrieve and optimize the image that exists at `https://flowers.com/daisy.jpg`.

{% Aside %}
As the above example shows, the file exension requested (`.jpg` is this case) may not be the same as the image file format returned (WebP in this example). The `content-type` HTTP Header will inform the browser which format the URL is in so it can process it appropriately. However, this may cause some confusion if the file is saved to disk and used by another program that expects the file format to match the file extension.
{% endAside %}

Another widely supported way of uploading images to an image CDN is to send them via an HTTP POST request to the image CDN's API.

### Security key

A security key prevents other people from creating new versions of your images. If this feature is enabled, each new version of an image requires a unique security key. If someone tries to change the parameters of the image URL but doesn't provide a valid security key, they won't be able to create a new version. Your image CDN will take care of the details of generating and tracking security keys for you.

### Transformations

Image CDNs offer tens, and in some cases hundreds, of different image transformations. These transformations are specified via the URL string, and there are no restrictions on using multiple transformations at the same time. In the context of web performance, the most important image transformations are size, pixel density, format, and compression. These transformations are the reason why switching to an image CDN typically results in a significant reduction in image size.

There tends to be an objectively best setting for performance transformations, so some image CDNs support an "auto" mode for these transformations. For example, instead of specifying that images be transformed to the WebP format, you could allow the CDN to automatically select and serve the optimal format. Signals that an image CDN can use to determine the best way to transform an image include:

* [Client hints](https://developer.chrome.com/blog/automating-resource-selection-with-client-hints/) (for example, viewport width, DPR, and image width)
* The [`Save-Data`](https://developer.mozilla.org/docs/Web/HTTP/Headers/Save-Data) header
* The [User-Agent](https://developer.mozilla.org/docs/Web/HTTP/Headers/User-Agent) request header
* The [Network Information API](https://developer.mozilla.org/docs/Web/API/Network_Information_API)

For example, the image CDN might serve AVIF to a Chrome browser, WebP to an Edge browser, and JPEG to a very old browser. Auto settings are popular because they allow you to take advantage of image CDNs' significant expertise in optimizing images without the need for code changes to adopt new technologies once they're supported by the image CDN.

## Types of Image CDNs

Image CDNs can be broken down into two categories: self-managed and third-party-managed.

### Self-managed image CDNs

Self-managed CDNs can be a good choice for sites with engineering staff who are comfortable maintaining their own infrastructure.

- [Thumbor](https://github.com/thumbor/thumbor) is the most popular self-managed image CDN. While it is open-source and free to use, it generally has fewer features than most commercial CDNs, and its documentation is somewhat limited. [Wikipedia](https://wikitech.wikimedia.org/wiki/Thumbor), [Square](https://medium.com/square-corner-blog/dynamic-images-with-thumbor-a430a1cfcd87), and [99designs](https://99designs.com/tech-blog/blog/2013/07/01/thumbnailing-with-thumbor/) are three sites that use Thumbor. See the [How to install the Thumbor image CDN](/install-thumbor) post for instructions on setting it up.
- [Imaginary](https://github.com/h2non/imaginary)
- [Imagor](https://github.com/cshum/imagor)

### Third-party image CDNs

Third-party image CDNs provide image CDNs as a service. Just as cloud providers provide servers and other infrastructure for a fee; image CDNs provide image optimization and delivery for a fee. Because third-party image CDNs maintain the underlying technology, getting started is fairly simple and can usually be accomplished in 10-15 minutes, although a complete migration for a large site might take far longer. Third-party image CDNs are typically priced based on usage tiers, with most image CDNs providing either a free tier or a free trial to give you an opportunity to try out their product.

## Choosing an image CDN

There are many good options for image CDNs. Some will have more features than others, but all of them will probably help you save bytes on your images and therefore load your pages faster. Besides feature sets, other factors to consider when choosing an image CDN are cost, support, documentation, and ease of setup or migration.

Trying them out yourself before making a decision can also be helpful. Below you can find codelabs with instructions on how to quickly get started with several image CDNs.

## Effects on Largest Contentful Paint (LCP)

Images are a vital part of the user experience on many websites, and thus factor into how well sites do when it comes to [Largest Contentful Paint](/lcp/). Here are a few things to keep in mind if you decide to use an image CDN:

1. Images served from CDNs may come from a cross-origin server, which involves extra connection setup time. When possible, try to use an image CDN that proxies through the primary origin so that you're not adding extra origins for the browser to connect to. This has the same effect as self-hosting images on the primary origin.
2. Consider using a [`fetchpriority` attribute value of `"high"`](/priority-hints/#summary) on the LCP image element so that the browser can begin loading that image as soon as possible.
3. If an image is not immediately discoverable in the initial HTML, consider using a [`rel=preload`](https://developer.chrome.com/docs/lighthouse/performance/uses-rel-preload/) hint for your LCP candidate image so that the browser can load that image ahead of time.
4. If proxying through your origin is not possible, and the exact image to be loaded will not be known until later during page load, [you should set up a connection to the cross-origin image CDN as early as possible](/preconnect-and-dns-prefetch/) to shorten the resource loading phase of what could be your page's LCP candidate image.

Image CDNs are indispensable tools that eliminate the toil of manually optimizing images, and should be considered. As always, though, there are trade-offs to consider, and keeping an eye on your website's LCP candidate images and adding hints as appropriate can mitigate any added latency to those key requests.
