
```css
/* stretch an image to fit the background of an element */
div {
 background-image: url('background.jpg');
 background-size: cover;
 background-repeat: no-repeat;
}
```

# background-clip

sets whether an element's background extends underneath its border box, padding box, or content box.

If the element has no background-image or background-color, this property will only have a visual effect when the border has transparent regions or partially opaque regions (due to border-style or border-image); otherwise, the border masks the difference.

```css
div {
	background-clip: border-box;
	/* The background extends to the outside edge of the border 
	(but underneath the border in z-ordering) */
	background-clip: padding-box;
	/* The background extends to the outside edge of the padding. 
	No background is drawn beneath the border. */
	background-clip: content-box; 
	/* The background is painted within (clipped to) the content box */
	background-clip: text;
	/* experimental; the background is painted within (clipped to) the foreground text */
}
```

# background-size

Sets the size of the element's background image. The image can be left to its natural size, stretched, or constrained to fit the available space.  
Spaces not covered by a background image are filled with the `background-color`.  

```css
div {
	background-size: cover;
	/* Scales the image as large as possible to fill the container, 
	stretching the image if necessary
  	If the proportions of the image differ from the element, 
    it is *cropped either vertically or horizontally* so that no empty space remains. */
	background-size: contain;
	/* Scales the image as large as possible within its container 
	without cropping or stretching the image
  	If the container is larger than the image, this will result in image tiling, 
	unless 'background-repeat: no-repeat' is set */
	background-size: 50%;  
  	/* Stretches the image in the corresponding dimension to the specified percentage
	of the 'background positioning area'
	The background positioning area is determined by the value of 
	'background-origin' (by default, the padding box). 
  	However, if 'background-attachment: fixed', 
	the positioning area is instead the entire viewport. */
```

# background-repeat

Sets how background images are repeated. A background image can be repeated along the horizontal and vertical axes, or not repeated at all.

/* Keyword values */
* `background-repeat: repeat-x:`
* `background-repeat: repeat-y:`
* `background-repeat: repeat;`
* `background-repeat: space;`
* `background-repeat: round;`
* `background-repeat: no-repeat;`

/* Two-value syntax: horizontal | vertical */
* `background-repeat: repeat space;`
* `background-repeat: repeat repeat;`
* `background-repeat: round space;`
* `background-repeat: no-repeat round;`

# background-position

* relative to the position layer set by `background-origin`

If no background-position has been specified, the image is placed at the *default top-left position* of the element (0,0), which is located within the top-left corner of the element, just inside the border at the outside edge of the padding box.

```css
div {
  /* 1-value syntax */
  /* If only one value is specified for background-position, the second value is assumed to be center. */
  background-position: center;
  /* => 50% */
  /* => 'center center' */
  background-position: top | left | bottom | right; /* specifies an edge against which to place the item. The other dimension is then set to 50%, so the item is placed in the middle of the edge specified */
  background-position: 20%; /* the X coordinate relative to the left edge, with the Y coordinate set to 50% */
  
  /* 2-value syntax */
  /* left-offset top-offset */ 
  background-position: 100px 200px; /* 100 pixels from the left and 200 pixels from the top of the element */
  
  /* 4-value syntax */
  /* The first and third values are keyword value defining X and Y. 
  The second and fourth values are offsets for the preceding X and Y keyword values */
  background-position: top 20% left 30%;
}
div {
  background-position-x: left; /* Aligns the left edge of the background image with the left edge of the background position layer. */
  background-position-x: center; /* Aligns the center of the background image with the center of the background position layer. */
  background-position-x: right; /* Aligns the right edge of the background image with the right edge of the background position layer. */
}
```

# Using a percentage value

In this case the position refers to both the element itself and the corresponding point on the background-image. For example, a background-image with background-position values of 50% 50% will place the point of the image that’s located at 50% of the image’s width and 50% of the image’s height at a corresponding position within the element that contains the image. In the above case, this causes the image to be perfectly centered. This is an important point to grasp—using background-position isn’t the same as placing an element with absolute position using percentages where the top-left corner of the element is placed at the position specified.