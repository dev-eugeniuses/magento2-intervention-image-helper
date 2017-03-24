# Intervention Image helper for Magento 2
A useful template helper for applying transformations to images in Magento 2 using the [Intervention Image](http://image.intervention.io) library.

## Installation
`composer require "dakzilla/intervention-image-helper:~1.0"`

`php bin/magento setup:upgrade`

## Features
+ The image helper can be called from any template, without the need to create a custom block
+ All of the transformation methods can be chained
+ By using an @var [DocComment](https://phpdoc.org/docs/latest/references/phpdoc/tags/var.html) as shown in the example below, your IDE will show autocompletion and documentation hints for each method
+ The images are cached automatically on creation, and are loaded from cache on every subsequent call
+ Ability to set JPEG quality directly in template
+ The module works great with Apptrian image optimizer for Magento 2 or any other image optimization module

## Usage
### Transforming an image from a template
Call the image helper from any (.phtml) template using this code:
```
<?php
/** @var \Dakzilla\Intervention\Helper\Image $imageHelper */
$imageHelper = $this->getImageHelper();
?>
```
You can now call the `make` method to load an image URL...
```
$image = $imageHelper->make('http://mysite.com/media/wysiwyg/home/home-main.jpg')

# Will resolve to /path/to/your/site/pub/media/wysiwyg/home/home-main.jpg
```
...or a relative image path
```
$image = $imageHelper->make('test/Pineapple.jpg')

# Will resolve to /path/to/your/site/pub/media/test/Pineapple.jpg
```
Then, you can chain the desired transformation methods, and finally call the `get` method to get the http link to the cached image:
```
$imageUrl = $image->flip()
    ->invert()
    ->resize(350, null, function ($constraint) {
        $constraint->aspectRatio();
    })
    ->get();
    
# Will return http://mysite.com/media/cache/dakzilla_intervention/<cache key>/myimage.jpg
```
Or, you can do all of these at once with a short, chained syntax:
```
<img src="<?php echo $imageHelper
    ->make('test/Pineapple.jpg')
    ->flip()
    ->invert()
    ->resize(350, null, function ($constraint) {
        $constraint->aspectRatio();
    })
    ->get(); ?>">
```

The above example will create a horizontally-flipped, color-inverted and automatically resized version of the original picture that retains the correct ratio. Of course, a more common usage would be to simply crop and/or resize a picture without applying cosmetic filters.

### Enabling image compression and setting the JPEG quality
Once set as shown below, image compression and quality will be applied to every image that is generated with this module. This means that if you are generating multiple images on a single page, they will all share the compression settings.

#### Defaults
+ Image compression: Off
+ JPEG quality: 85%

From the template, call these methods on the image helper to enable JPEG compression and set the quality (1-100, lowest to highest):
```
<?php
/** @var \Dakzilla\Intervention\Helper\Image $imageHelper */
$imageHelper->setCompressImages()->setQuality(75);
?>
```

If you're okay with the default quality level, you only need to call the `setCompressImages` method to enable compression:
```
<?php
/** @var \Dakzilla\Intervention\Helper\Image $imageHelper */
$imageHelper->setCompressImages();
?>
```

## Clearing the image cache
Delete the image cache directory. By default, this is `<magento root>/pub/media/cache/dakzilla_intervention`
To avoid potential permission issues, do not re-create this folder after deleting it. Let Magento re-create it automatically. 

## Available transformation methods
The helper provides IDE-compatible method hints for every available transformation method. For the most part, these methods are self-explanatory. For further information on these methods, please refer to the [Intervention Image documentation](http://image.intervention.io/).
 
 + blur
 + brightness
 + circle
 + colorize
 + contrast
 + crop
 + ellipse
 + fill
 + flip
 + fit
 + gamma
 + greyscale
 + heighten
 + insert (ex: for applying a watermark)
 + interlace
 + invert
 + limitColors
 + line
 + mask
 + opacity
 + orientate (for camera pictures with EXIF data)
 + pixel
 + pixelate
 + polygon
 + rectangle
 + resize
 + resizeCanvas
 + rotate
 + sharpen
 + text
 + trim
 + widen

## Issues
+ This module does not include tests
+ This module does not provide a way to choose the image manipulation library (defaults to GD)
+ Some features from the Intervention Image library (like canvas or output streaming) are not available (but may be implemented later)
+ This module throws exceptions instead of failing gracefully

## Compatibility
This module has been tested with Magento 2.1.5. As Magento 2 is still evolving rapidly, there is no guarantee that it will work with every version. However, as this package is unassuming in what it achieves and respects Magento 2 best coding practices, I see no reason why it should cause issues in your installation.

## Requirements
+ PHP 7.0 and above
+ Magento 2.1.0 and above
+ GD or Imagick library
+ Fileinfo Extension

## To-do
+ Provide a way to clear image cache from admin and command line
+ Allow the module to fail gracefully in case of a file error
+ Tests
+ Provide more control to end-user, like choosing the image manipulation library
+ Enable JPEG quality control in admin
+ Allow cached images to expire
+ Automatically clean up expired image caches via cron
+ A way to create image templates (pre-made styles) to provide a shorter, cleaner template syntax
+ Adding Etags in HTTP header for better cache invalidation

## Thanks
This simple package is piggybacking on the incredible work of [Oliver Vogel](https://github.com/olivervogel) with his [Intervention Image](https://github.com/Intervention/image) and [Image Cache](https://github.com/Intervention/imagecache) packages.

This module was also inspired by the awesome work of [Stämpfli AG](https://github.com/staempfli) and their [Magento 2 Image Resizer](https://github.com/staempfli/magento2-module-image-resizer) module.

## License
This module is licensed under the MIT License

Copyright 2017 Simon Dakin

Made with ♥ in Montreal