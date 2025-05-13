HTML entities
===========================

An HTML entity is a string that begins with an ampersand (`&`) and ends with a semicolon.

In CSS you need to use a Unicode escape sequence for such characters, which is based on the hexadecimal value of the code point of a character.

## monochrome

### crosses

&#10005;	\2715	Multiplication x
&times;	

&#10006;	\2716	Heavy Multiplication X
&#10010;	\271A	Heavy Greek Cross
&#10008;	\2718
&#10007;	\2717
&#215;	\00D7

&#10060; 	\274C

### marks

&#10004;	\2714
&#10003;	\2713

### boxes

&#9746;	\2612
&#9745;	\2611
&#8864;
&#8862;
&#9744;	\2610

### signs

&#9888;	\26A0	
&#9762;	\2622

### numbers

&#10102;	\2776
&#10103;	\2777

&#10112;	\2780

&#10122;	\278A



### emojis

&#9829;	

### things

&#9742;	\260E
&#9743;	\260F
&#9733;	\2605
&#9734;	\2606
&#9728;	\2600
&#9872;	\2690
&#9873;	\2691



### circle & dots

&#x2022;
&#x2026;
&#8853; 	Circled Plus
&#8854; 	Circled Minus
&#8861; 	Circled Dash
&#8857;	

&#9737;	\2609

&#8858; 	Circled Ring
&#8855; 	Circled Times
&#9675; 	White Circle
&#9679; 	Black Circle
&#9711; 	Large Circle

&#8285;	\205D
&#8286;	\205E
&#8228;	\2024
&#8229;	\2025
&#8230;	\2026

### lines & bars & dashes

&#8801;
&#9776;	\2630

&#x2016;	\2016

&#8722;	

&#8212;	\2014	

### arrows & triangles

&#10132;	

&#10148;	\27A4

&#11165;	Black Upwards Equilateral Arrowhead 

&#11181;	Black Curved Rightwards and Upwards Arrow

&#8592;	\2190
&#8595;	\2193
&#8593;	\2191
&#8594;	\2192

&#10141;	\279D

&#10142;	\279E

&#10140;	\279C

&#10132;	\2794


&#10137;	\2799

&#10139;	\279B

&#8702;
&#8701;

&#9650;	\25B2
&#9660;	

&#9652;

&#9653;

&#10144;	\27A0

&#10145;	\27A1

&#10143;	\279F

&#10162;	\27B2

&#8657;
&#8658;
&#8659;

&#8679;
&#8681;	

&#8630;
&#8631;

&#8635;
&#8634;

&#8672;
&#8673;
&#8674;
&#8675; 

&#8689;
&#8690;

&#8691;
&#8703; 

&#8883;
&#8882;
&#8710;
&#8711; 

&#8249;	\2039
&#8250;	\203A

&#10092;	\276C
&#10093;	\276D

&#10096;	\2770
&#10097;	\2771

&#10094;	\276E	
&#10095;	\276F

&#9001;	\2329
&#9002; 	\232A



## controls

stop = &#9724;

thin pause &#10073;

big pause &#10074;

play&#9658; 

circle&#9899;

  ⏩︎ fast forward
  23EA  ⏪︎ rewind, fast backwards
  23EB  ⏫︎ fast increase
  23EC  ⏬︎ fast decrease
  23ED  ⏭︎ skip to end, next
  23EE  ⏮︎ skip to start, previous
  23EF  ⏯︎ play/pause toggle
  23F1  ⏱︎ stopwatch
  23F2  ⏲︎ timer clock
  23F3  ⏳︎ hourglass
  23F4  ⏴︎ reverse, back
  23F5  ⏵︎ forward, next, play
  23F6  ⏶︎ increase
  23F7  ⏷︎ decrease
  23F8  ⏸︎ pause
  23F9  ⏹︎ stop
  23FA  ⏺︎ record  

## colored

```css
button {
  font-size: 20px;
  color: transparent;
  text-shadow: 0 0 0 blue; /* this way you can color an emoji */
}
```

&#10060;	&#10062;	&#9889;	&#11088;	&#128077;	&#128078;

## manipulate html entities

```html
<span>&#10147;</span>
```

```css
span {
  transform: rotate(180deg);
  display: inline-block;
}
```
