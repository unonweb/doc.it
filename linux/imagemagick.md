# imagemagick

# identify

describes the format and characteristics of one or more image files

```sh
identify -format "%wx%h" <img> # prints size
identify -list format # lists the available formats
```

```sh
identify -verbose <file> | grep GPS

find -name *.webp 
```

# convert

```sh
# converting formats
convert img.jpeg img.png # convert jpeg into png
convert *.jpg *.png # converts all .jpegs into .pngs
convert img.jpeg img.pdf # converts into pdf

# converting sizes
convert img.jpeg -resize 64x64 img_64x64.jpeg
convert img.jpeg -resize 50% img_half.jpeg
# by providing only the width, ImageMagic does the math for you and automatically retains the aspect ratio by resizing the output image with a proportional height:
convert img.jpg -resize 500x img_500.jpg
```

## pdf

```sh
# convert jpg to pdf
convert *.jpg -auto-orient pictures.pdf # get a single pdf containing all jpg in the current folder
```

## flip / join

```sh
convert -flip original.jpg flipped.jpg # flips vertically
convert -append org.jpeg flip.jpg joined.jpg # joins vertically
```

## reduce file size

```sh
convert input.png -quality 75 output.jpg
```

## exiftool

```sh
sudo apt install libimage-exiftool-perl
exiftool image.jpg
exiftool -author="linuxconfig" image.jpg 
```

```sh
convert -list Format # list available formats
convert -list Format | grep raw 
```

## optimize

According to one StackOverflow poster, this strategy focuses on following Google Lighthouse's guide on how to pass the "Optimize Images" Lighthouse audit in Chrome DevTools:

```sh
convert <img> \
-sampling-factor 4:2:0 \
-strip \
-quality 85 \
-interlace JPEG \
-colorspace RGB \
feldroy-512x512-pagespeed.jpg 
```