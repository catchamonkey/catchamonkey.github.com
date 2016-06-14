---
layout: post
title: Imagemagick sfThumbnailPlugin fix for scaling under symfony 1.0
date: 16th March 2010
---

I've been using the sfThumbnail Plugin in symfony 1.0 for image resizing when users upload.  
When I asked the plugin to resize an image with scaling there was a problem and the image came out skewed and distorted.  
After a little research I found the following fix.

Once you have installed the plugin, edit the sfImageMagickAdapter.class.php in the plugin dir.

Change lines 223-237:

From
```php
<?php
$width  = $this->sourceWidth;
$height = $this->sourceHeight;
$x = $y = 0;
switch (@$this->options['method']) {
  case "shave_all":
    if ($width > $height)
    {
      $x = ceil(($width - $height) / 2 );
      $width = $height;
    }
    elseif ($height > $width)
    {
      $y = ceil(($height - $width) / 2);
      $height = $width;
    }
```


To
```php
<?php
$width  = $this->sourceWidth;
$height = $this->sourceHeight;
$mWidth = $this->maxWidth;
$mHeight = $this->maxHeight;
$x = $y = 0;
switch (@$this->options['method']) {
  case "shave_all":
    if ($width > $height)
    {
      $x = ceil(($width - ($height*$mWidth)/$mHeight) / 2 );
      $width = $height;
    }
    elseif ($height > $width)
    {
      $y = ceil(($height - $width*($mHeight/$mWidth)) / 2);
      $height = $width;
    }
```

It's a straight forward edit so you should be able to see why this simple change has the desired effect, and your images will now behave nicely when scaling.
