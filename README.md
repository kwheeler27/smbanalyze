# smbanalyze

## Usage

Load the package:

    from smbanalyze import *

Loading a single image:

```python
image = Image.fromFile('test1.img')
background = Image.fromFile('background.img', background=True)

image_bg = Image.fromFile('test1.img', background='background.img')

# is true!
image - background == image_bg

# Properties
image.frames
image.width
image.height
image.times

# display frames -- starts with 0
image_bg.show(0)
image_bg.show(10)
```

Get out the donor/acceptor counts:

```python
ROIs = Image.ROI.fromFile('roi1.txt')
image_bg.addROI(*ROIs)

# or bottom,left and top,right and origin
donorROI = Image.ROI( (5,5), (20,25), origin='relative', name='donor' )
# left, right, bottom, top
acceptorROI = Image.ROI.fromCorners( 50, 60, 90, 100, origin='absolute', name='acceptor' )
image_bg.addROI(donorROI,acceptorROI)

donor,acceptor = image_bg.donor,image_bg.acceptor
```

Origin reflects whether the pixels are numbered with respect to the CCD origin ('absolute') or the image origin ('relative'). Absolute ROIs are robust to changes in the subimage coordinates.

To calculate fret:

```python
fret = FRET.calculate(image_bg) # optional: beta= , gamma=

# Access as named fields
fret.time, fret.donor, fret.acceptor, fret.fret

# or as a tuple (order matters!)
T,donor,acceptor,f = fret

# and save to a file
FRET.toFile('saveTo.fret', fret)
```

`fret` is a special "named tuple" from the collections package in the python library with more flexible usage, as shown above. Don't be confused; it's just a tuple in which each position also has a name which you can see with `fret._fields`.

Plot the data:

```python
FRET.plot(fret, title='test1')

# Rerunning this command overwrites the current figure
# but if you want a new figure
figure()
FRET.plot(fret)

# and if you have pulling data
pull = FileIO.loadstr('test1.str')
FRET.plot(fret, pull=pull)

# and if you want to see an FEC
FRET.plot(pull, FEC=True)
# this works too, though they don't align
FRET.plot(fret, pull, FEC=True)
```