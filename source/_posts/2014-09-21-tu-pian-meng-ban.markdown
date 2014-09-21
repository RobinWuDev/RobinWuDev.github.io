---
layout: post
title: "图片蒙版"
date: 2014-09-21 14:05:25 +0800
comments: true
categories: iOS
---
我第一次听到蒙版这个概念是通过ps了解了，通过蒙版，可以让某一张图片只显示我们想要让他显示的形状。
在iOS开发中，我们也需要对图片进行各种各样的处理，例如在IM中图片的显示，并不是四四方方的显示出来的，而是显示出一个类似起泡的样子，当然这是因为QQ,微信是这样做的，导致后面的人要做IM的图片也要这样子。    
<!--more-->
我现在做的一个产品也是需要弄出这样的效果，所以我就研究了下，但是显然这东西并没有我想象的那么简单。首先我知道有三种方法可以实现这样的一种效果。

1. 通过在图片上面再盖一层图片用来遮盖，但是这种方法有一定的局限性。
2. 通过图片蒙板生成一个图片，这个方法可行，但是我遇到了另外一个问题。
3. 通过图层蒙板，使用这个方法需要准备一些东西。

下面我讲分别说明这三种的使用方法

### 1. 图片遮盖
![](/blogFiles/images/2014/09/maskDemo1.png)

在原来的图片上面再改一层图片，该图片要显示的部分为透明，不显示的区域颜色要和背景色的颜色保持一直。

![](/blogFiles/images/2014/09/maskDemo1_1.png)  
  
这个方法很常用，但是在IM中却不能使用，因为IM的背景图片是可替换的，如果换成一个不纯色的背景图片，那么这个方法就无法使用了。    

demo的下载地址:[MaskDemo1](/blogFiles/download/20140921_1.zip)

### 2. 图片蒙板

通过**CoreGraphics**提供的图片处理函数，我们可以生成一张蒙版处理后的图片。

	- (CGImageRef)rbCopyImageAndAddAlphaChannel:(CGImageRef)sourceImage {
	    CGImageRef retVal = NULL;
	    
	    size_t width = CGImageGetWidth(sourceImage);
	    size_t height = CGImageGetHeight(sourceImage);
	    
	    CGColorSpaceRef colorSpace = CGColorSpaceCreateDeviceRGB();
	    
	    CGContextRef offscreenContext = CGBitmapContextCreate(NULL, width, height,
	                                                          8, 0, colorSpace, kCGImageAlphaPremultipliedFirst);
	    
	    if (offscreenContext != NULL)
	    {
	        CGContextDrawImage(offscreenContext, CGRectMake(0, 0, width, height), sourceImage);
	        retVal = CGBitmapContextCreateImage(offscreenContext);
	        CGContextRelease(offscreenContext);
	    }
	    
	    CGColorSpaceRelease(colorSpace);
	    
	    return retVal;
	}
	
	- (UIImage*)rbMaskWithImage:(UIImage *)maskImage{
	    //创建蒙版图片
	    CGImageRef maskRef = maskImage.CGImage;
	    CGImageRef mask = CGImageMaskCreate(CGImageGetWidth(maskRef),
	                                        CGImageGetHeight(maskRef),
	                                        CGImageGetBitsPerComponent(maskRef),
	                                        CGImageGetBitsPerPixel(maskRef),
	                                        CGImageGetBytesPerRow(maskRef),
	                                        CGImageGetDataProvider(maskRef), NULL, false);
	    
	    
	    CGImageRef sourceImage = [self CGImage];
	    CGImageRef imageWithAlpha = sourceImage;
	    
	    //处理原图片，原图片需要有透明通道，如果没有需要处理(例如，gif,jpg等等)
	    //这里有一定的性能消耗
	    if (CGImageGetAlphaInfo(sourceImage) == kCGImageAlphaNone){
	        imageWithAlpha = [self rbCopyImageAndAddAlphaChannel:sourceImage];
	    }
	    
	    //创建蒙版处理后的图片
	    CGImageRef masked = CGImageCreateWithMask(imageWithAlpha, mask);
	    CGImageRelease(mask);
	    
	    //release imageWithAlpha if it was created by CopyImageAndAddAlphaChannel
	    if (sourceImage != imageWithAlpha)
	    {
	        CGImageRelease(imageWithAlpha);
	    }
	    
	    UIImage* retImage = [UIImage imageWithCGImage:masked];
	    CGImageRelease(masked);
	    
	    return retImage;
	}

通过上面的方法，我们可以生成一张图片，具体使用如下

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
		// Do any additional setup after loading the view, typically from a nib.
	    UIImage *image = [UIImage imageNamed:@"demo"];
	    UIImage *maskImage = [UIImage imageNamed:@"mask"];
	    UIImage *maskedImage = [image rbMaskWithImage:maskImage];
	    self.demoImageView.image = maskedImage;
	}

出来的效果:

![](/blogFiles/images/2014/09/maskDemo2_1.png)

有人可能会疑问，明显变形了，因为用这个方法生成，蒙版图片必须要和原图片一样大小，而我使用的是一张小图片，有设置了拉伸区域，但是并没有生效。

demo的下载地址:[MaskDemo2](/blogFiles/download/20140921_2.zip)

### 3. 使用图层蒙版

设置view.layer.mask为一张蒙版图片，那么图层的内容也会变成相应的形状，例如:

	- (void)viewDidLoad
	{
	    [super viewDidLoad];
		// Do any additional setup after loading the view, typically from a nib.
	    UIImage *image = [UIImage imageNamed:@"mask"];
	    
	    CALayer *layer = [CALayer layer];
	    layer.contents = (id)[image CGImage];
	    layer.frame = CGRectMake(0, 0, self.demoImageView.frame.size.width, self.demoImageView.frame.size.height);
	    
	    self.demoImageView.layer.mask = layer;
	    self.demoImageView.layer.masksToBounds = YES;
	}
出来的效果:

![](/blogFiles/images/2014/09/maskDemo3_1.png)

为什么跟第二种方法出来的效果是一样，因为mask图片没有没拉伸，并且用这种方法，mask图片中要显示的区域为黑色!

demo的下载地址:[MaskDemo3](/blogFiles/download/20140921_3.zip)

那这样还是无法完美的解决显示效果的问题。

github上我发现了有个项目可以实现图片拉伸，[地址](https://github.com/molon/MLIgnoreTransparentButton)，可以参考下!

参考:[UIImage 和 CALayer 的遮罩](http://blog.sina.com.cn/s/blog_611f76750101er4z.html)
