* a **tint** is a mixture of a color with white, which increases lightness
* a **shade** is a mixture with black, which increases darkness

```css
.colored {
	hsl(
  	hue	/* a degree on the color wheel (from 0 to 360) - 0 (or 360) is red, 120 is green, 240 is blue */
		saturation % 	/*  saturation; 0% is a shade of gray and 100% is the full color (full saturation) */
		lightness % 	/* lightness; 0% is black, 50% is normal, and 100% is white) */
}
```

## hsl()

```css
/* Old Syntax */
hsla(26.06deg, 99%, 51%, .75)

/* New Syntax */
hsl(26.06 99% 51% / .75)
```

## oklch()

*“Chroma refers to the purity of a color. A hue with high chroma has no black, white, or gray added to it. Conversely, adding white, black, or gray reduces its chroma. It’s similar to saturation but not quite the same.*

```css
oklch( [ lightness ] [ chroma ] [ hue ] )
oklch( [ <percentage (0%-100%)> ] [ <number> (0-0.37) ] [ <degrees> (0deg-360deg) ]  )
color: oklch(70.9% 0.195 47.025);
```

## linear-gradient()

Because `<gradient>`s belong to the `<image>` data type, they can only be used where images can be used. For this reason, `linear-gradient()` won't work on `background-color` and other properties that use the <color> data type.

```css
/* Color stop: A gradient going from the bottom to top,
   starting blue, turning green at 40% of its length,
   and finishing red */
linear-gradient(0deg, blue, green 40%, red)

/* A gradient tilted 45 degrees,
   starting blue and finishing red */
linear-gradient(45deg, blue, red)

/* A gradient going from the bottom right to the top left corner,
   starting blue and finishing red */
linear-gradient(to left top, blue, red)
```

## color-mix()

### color-interpolation-method

```css
<color-interpolation-method> = 
  in [ <rectangular-color-space> | <polar-color-space> <hue-interpolation-method>? ]  

<rectangular-color-space> = 
  srgb          |
  srgb-linear   |
  display-p3    |
  a98-rgb       |
  prophoto-rgb  |
  rec2020       |
  lab           |
  oklab         |
  xyz           |
  xyz-d50       |
  xyz-d65       

<polar-color-space> = 
  hsl    |
  hwb    |
  lch    |
  oklch  

<hue-interpolation-method> = 
  [ shorter | longer | increasing | decreasing ] hue
```

```css
html {
  color-mix(<color-interpolation-method>, red 5%, green 15%)
  --color-1: #222023;
  --text1: color-mix(in srgb, var(--color-1) 10%, white); 
  /* 10% of color-1 is mixed with white */
  /* color-1 results very bright */
  --light-text1: color-mix(in srgb, var(--color-1) 10%, black);
}

.make-transparent {
  background: color-mix(
    in oklab, 
    red, 
    transparent 75%
  );
}
```

## color-mix()

`color-mix(method, color1[ p1], color2[ p2])`

```css
div {
  background-color: color-mix(in srgb, #34c9eb 0%, white);
}
```