---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.11.2
kernelspec:
  display_name: Python 3
  language: python
  name: python3
---

# Lab 1 - Waves and an Introduction to Jupyter Notebooks

+++

## Learning Goals
1. To visualize the propagation of single waves and a group of waves
2. To see the difference between the group velocity and phase velocity
3. To visualize the difference between deep and shallow water gravity waves
4. To work with Jupyter Notebooks

+++

## 1.1 A Single Wave
a) We know that a single wave can be expressed mathematically as
$$\eta(x,t)=A\cos(kx-\omega t+\phi)$$
and that $k$ and $\omega$ are related through a DISPERSION RELATION that contains the physics of the particular wave process we are considering. For example, for deep water waves
$$\omega=\sqrt{gk}$$
$\phi$ determines the initial state of the wave, that is, where the in space the troughs and crests are at $t=0$. It is called the phase shift.

Now, let's use Python to watch the 'propagation' of this wave through space and time:


```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt

# parameters that define the wave
k = 0.1 # wavenumber, 1/m
a = 1 # amplitude, m
phi = 0 # phase shift, between 0 and 2*pi

# Fixed parameters for this solution
g = 9.81 # acceleration due to gravity, m/s^2
x = np.arange(0, 101, 1) # spatial coordinate, m
t = np.arange(0, 3.1, 0.1) # time coordinate, s
# The dispersion relation
w = np.sqrt(g*k) # angular frequency, 1/second
col = plt.get_cmap('viridis', len(t)) # pre-allocate a colormap
eta = np.zeros(len(x)) # pre-allocate space

plt.rcParams['figure.figsize'] = (10,5)
# loop over time
for ii in  range(len(t)):
    eta = a*np.cos(k*x - w*t[ii] + phi)
    # Draw the the wave image at each timestep in the loop
    plt.plot(x, eta, c=col(ii))
plt.xlabel('x/m');
plt.ylabel('Amplitude/m');
plt.title('Blue is first, Yellow is last');
plt.show()
```

***To "run" this segment of code use the "Run" button above or use Ctrl-Enter (Command-Enter on Mac.)***

***Now answer these questions.***

+++

***Q1:*** How fast did the wave move? (estimate from figure) ____________________ m/s

***Q2:*** How fast SHOULD the wave have moved? (Calculate from dispersion relation) ____________________ m/s

You can use the box below to write a formula in Python code to do the calculation, which will appear below the figure

```{code-cell} ipython3
21+45
```

b) Another useful visualization method is to SAVE the wave shape at all times, and then plot the whole output at once. Here we will look at a spatial domain 10x the size we did above, and for 50x the amount of time:

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt

# Fixed parameters for this solution
x = np.arange(0, 1001, 1) # m
t = np.arange(0, 100.5, 0.5) # s

# For all the remaining parameters, use same values as above,
# still saved in memory
# Calculate the waves at different times, in a loop
eta = np.zeros((len(t), len(x))) # Pre-allocate space for waveshapes
for ii in range(len(t)):
    eta[ii,:]=a*np.cos(k*x - w*t[ii] + phi)

# Plot the results AFTER the loop
plt.rcParams['figure.figsize'] = (7, 5)
plt.imshow(eta, aspect='auto')
plt.xlabel('x/m')
plt.ylabel('t/s')
plt.ylim([100, 0])
plt.title('Example 1')
plt.show()
```

In this figure, bands of constant colour show the propagation of areas of equal height moving rightwards (i.e. to larger $x$) as time goes on ($t$ increases). Crests and troughs will have different colours.

Do you know how to make the waves move LEFTWARDS? (It requires inserting a single character into the code and re-running this section of code). Make sure you know how to do this, but afterwards return back to the original code.

***Q3:*** What character did you insert, and where? ____________________

c) Now let's try it for a different wavelength - let's choose a LONGER wavelength (i.e. a SMALLER wavenumber)

```{code-cell} ipython3
# This is a code fragment that relies on the first running
# the previous code fragment...
k = 0.05 # Half the previous wavenumber
# Re-calculate this!
w = np.sqrt(g*k)
for ii in range(len(t)):
    eta[ii,:]=a*np.cos(k*x - w*t[ii] + phi);

plt.rcParams['figure.figsize'] = (7, 5)
plt.imshow(eta, aspect='auto')
plt.xlabel('x/m');
plt.ylabel('t/s');
plt.ylim([100, 0])
plt.title('Example 2');
plt.show()
```

***Q4:*** Does the LONGER wavelength move faster... or slower? ____________________

***Q5:*** CALCULATE the speed by measuring things in the image. (hint: what is the formula for speed: ____________________ )

3.a) Speed in example 1: ____________________ m/s ______  
NB - you can use this 'code block' to do calculations in Python:

```{code-cell} ipython3

```

3.b) Speed in example 2: ____________________ m/s ______  

```{code-cell} ipython3

```

## 1.2 The Sum of Two Waves
Now we would like to consider a more complicated wave field
$$\eta(x,t)=\sum_{i=1}^{N}a_{i}\cos(k_{i}x-\omega_{i}t+\phi_{i})$$
that is made up of $N$ simple waves, and we begin with $N=2$. The code we have must be modified a little for this. Note the use of `np.random.uniform()` for the phases. By using this, the picture will change slightly each time you run the section

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt

# Our inputs
k = np.array([0.05, 0.1]) # 1/m
a = np.array([1, 1]) # m
phi = np.random.uniform(0, 2*np.pi, 2) # Phases chosen randomly between 0 and 2*pi
g = 9.81 # m/s^2
x = np.arange(0, 1001, 1) # m
t = np.arange(0, 100.5, 0.5) # s
eta = np.zeros((len(t), len(x)))

# The physics
w = np.sqrt(g*k)
for ii in range(len(t)): # loop over time
    for jj in range(len(k)): # loop for each wave
        eta[ii,:] = eta[ii,:] + a[jj]*np.cos(k[jj]*x - w[jj]*t[ii] + phi[jj])

# Draw the summary image as before
plt.rcParams['figure.figsize'] = (7, 5)
plt.imshow(eta, aspect='auto')
plt.xlabel('x/m')
plt.ylabel('t/s')
plt.ylim([100, 0])
plt.title('Example 3')
plt.show()

# Just plot the wave at the final time
plt.plot(x, eta[-1,:]);
plt.xlabel('x/m');
plt.ylabel('Amplitude/m');
plt.title('Example 3');
plt.show()
```

Look carefully at the results. You can see that the wave has a complicated look, but there is clearly an ENVELOPE (i.e. a region of tall waves) that is moving very slowly, as well as the faster individual waves, which move through the envelope.

***Q6:*** What is the speed of this envelope? ____________________ m/s ______  

```{code-cell} ipython3

```

Compare this speed with the AVERAGE of the speeds of the two different waves that make up the sum (i.e. the values you found earlier in Section 1.1).

***Q7:*** How is the average speed related to the envelope speed? (hald, twice, 3/4, something else...) ____________________

```{code-cell} ipython3

```

## 1.3 Multiple Waves
OK, now let's modify this code to sum up a larger number of waves. Again, by randomizing the phases, the result will look different each time you run this segment.

```{code-cell} ipython3
import numpy as np
import matplotlib.pyplot as plt

# Our inputs
k = np.arange(0.05, 0.15, 0.005) # 1/m
a = np.exp(-(k-0.1)**2/(0.02**2) ) # m
phi = np.random.uniform(0, 2*np.pi, len(k)) # Phases chosen randomly between 0 and 2*pi
g = 9.81 # m/s^2
x = np.arange(0, 1001, 1) # m
t = np.arange(0, 100.5, 0.5) # s
eta = np.zeros((len(t), len(x)))

# The physics
w = np.sqrt(g*k);
for ii in range(len(t)):
    for jj in range(len(k)):
        eta[ii, :] = eta[ii, :] + a[jj]*np.cos(k[jj]*x - w[jj]*t[ii] + phi[jj]);
        
plt.rcParams['figure.figsize'] = (7, 5)
plt.imshow(eta, aspect='auto')
plt.xlabel('x/m')
plt.ylabel('t/s')
plt.ylim([100, 0])
plt.title('Example 4')
plt.show()

plt.plot(x, eta[-1, :])
plt.xlabel('x/m')
plt.ylabel('Amplitude/m')
plt.title('Example 4')
plt.show()
```

Run this a number of times. Although the picture looks different each time, you will always notice that the CRESTS of individual waves are moving at the same speed as that in Example 1. However, these individual waves are sometimes larger and sometimes smaller.

On the other hand, there are GROUPS of wave crests that have larger amplitudes, and groups with smaller amplitudes. The speed of this GROUP is significantly slower. In fact, the speed of this group can be calculated by taking the DERIVATIVE of the dispersion curve. For a bunch of waves that "mostly" have wavenumber $k_{0}$, the GROUP VELOCITY is:

$$c_{group}(k_{0})=\frac{\partial\omega}{\partial k}\vert_{k=k_{0}}$$

***Q8:*** What is the formula for the group velocity of deep-water waves? ____________________

(Just type the formula like it was Python code.)

***Q9:*** For $k=0.1$, what is the EXACT CALCULATED group speed? ____________________ m/s ______  

(again, you can use the box below for calculations)

```{code-cell} ipython3

```

## 1.4 Wave Physics
Finally, how does this picture depend on wave physics? Create your own code block here (use the + button in the toolbar to add a code cell below) and copy in the code from the Example 4 fragment above. Change the titles to Example 5, and now run the calculation for SHALLOW WATER waves in a water depth of $H=10\textrm{m}$. Be careful about the dispersion relation!

```{code-cell} ipython3
# Put shallow water code here

# End of code
```

***Q10:*** What is the formula for the group velocity for shallow water waves? ____________________

```{code-cell} ipython3

```

***Q11:*** Is the group velocity for shallow water waves smaller/the same/greater than the phase velocity? ____________________

```{code-cell} ipython3

```

***TO HAND IN***

***Instructions for submitting the assignments.***
