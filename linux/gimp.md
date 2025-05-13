# Masks

**white reveals, black conceals** (weiß enthüllt, schwarz verbirgt)

### black mask

* layer is concealed
* then draw with white to bring it back again

### white mask

* layer remains visible
* then draw with black to conceal it

# Tutorials



## color

### change color of multiple text layers

- Put all your text layers in layer group
- Add a layer at the top of the group, fill it with the color/pattern/image
- Set the composite mode of that layer to `Clip to back drop` either using the layer attributes dialog (as show) or directly in the right click menu.
- The group is used to limit the scope of the `Clip to backdrop`

### change color of foreground obj

1. copy selection
2. create new transparency layer
3. past selection
4. change color

### change background of obj to grey

1. copy colored img
2. change copy to greyscale
3. add white layer mask to original
4. draw black mask around foreground obj

### adjust scanned PDF

- navigate to `Colors`, `Levels...`
- Under `Input Levels` change the lower bound from `0` to something like `120`. Change the upper bound from `255` to something like `220`.