## ICS4U-2-E-Gravity

In order to create a gravitational attraction between two objects we need to:

1) Determine the distance between the object
2) Use the equation of gravitation to calculate the acceleration that will be applied on each object from the action of gravity

Once we have determined the acceleration we can use it to update the velocity and position of the objects.

### Calculating the distance between objects

Assume that you have two objects labelled $\square_1$ and $\square_2$. Each object will be placed at position in $x$ and $y$ dimensions and have associated velocity and accelerations in each dimension ($v_x, v_y, a_x, a_y$).

The displacement vector between each object can be calculated in each dimension by: $\Delta x = x_2-x_1$ and $\Delta y = y_2-y_1$.

<img src="/Users/BLawrence/Desktop/ICS4U-2-E-Gravity.assets/ICS4U-2-E-Gravity.svg" alt="ICS4U-2-E-Gravity" style="max-width:4in" />

The distance between the objects $d$, in two dimensions can be calculated using pythagorean theorum and knowing the displacement in the x and y dimensions.

$d^2 = \Delta x^2 + \Delta y^2$

$d = \sqrt{\Delta x^2 + \Delta y^2}$

$d = \sqrt{(x_2-x_1)^2 + (y_2-y_1)^2}$

### Calculating the accelleration due to gravity

Newton's law of gravity states that:

$F_g = G\frac{m_1 m_2}{d^2}$

Where F is the magnitude of the force between the objects 1 and 2, G is a constant, $m_\square$ is the mass of each object and $d$ is the distance between the objects.

<img src="/Users/BLawrence/Desktop/ICS4U-2-E-Gravity.assets/ICS4U-2-E-Gravity-gravity.svg" alt="ICS4U-2-E-Gravity" style="max-width:4in" />

By Newton's second law:

$F = ma$

Therefore, the accelleration applied to object 1 by object 2 can be expressed as:

$m_1 a_1 = F_g = G\frac{m_1 m_2}{d^2}$
$a_1 = \frac{F_g}{m_1} = G\frac{m_1 m_2}{m_1 d^2}$ 
$a_1 = G\frac{m_2}{d^2}$

This will be the accelleration applied to the object 1 by object 2. However we need to decompose this acceleration into its x and y components to calculate positions and velocities on the HTML canvas.

We can find the $x$ and $y$ components of the overall force vector $F_{g1}$ by similar triangles with the displacement vectors.

<img src="/Users/BLawrence/Desktop/ICS4U-2-E-Gravity.assets/ICS4U-2-E-Gravity-similar.svg" alt="ICS4U-2-E-Gravity" style="max-width:4in" />

Therefore by the geometry above:

$\frac{F_{gx1}}{F_{g1}} = \frac{\overrightarrow{x_{12}}}{d}$

And as accelleration induced by the gravitational interaction is proportional, and in the same direction as the applied gravitational force:

$\frac{a_{x1}}{a_1} = \frac{\overrightarrow{x_{12}}}{d}$

And therefore, rearranging and combining with above:
$a_{x1} = a_1 \frac{\overrightarrow{x_{12}}}{d}$
$a_{x1} = G\frac{m_2}{d^2} \frac{\overrightarrow{x_{12}}}{d}$
$a_{x1} = G\frac{m_2 \overrightarrow{x_{12}}}{d^3}$
$a_{x1} = G\frac{m_2 (x_2 - x_1)}{d^3}$


All of the above is of course equivalent in the y direction.

## In code

Using the above interactions and equations, the attraction of one object on another can be expressed as the following method (which adds accelleration for the interaction, given an input of the other object). ```this``` refers to object 1 above, and ```otherParticle``` refers object 2 above. ```G``` is a universal constant defined for the interaction.

```javascript
attract(otherParticle) {
    let x_dist = otherParticle.x - this.x;
    let y_dist = otherParticle.y - this.y;
    let dist = Math.pow(x_dist * x_dist + y_dist * y_dist, 0.5);

    this.x_a += G * (x_dist) * otherParticle.mass / (dist * dist * dist);
    this.y_a += G * (y_dist) * otherParticle.mass / (dist * dist * dist);
  }
```
**note: ** as this method adds acceleration, the acceleration of all particles should be set to zero after they are moved and before calculating the accelleration above for the next timestep.

We can save some calculations (especially that slow ```Math.pow()``` call) by updating both particles at the same time  and then only calling the attract method once for each pair of objects. 

Note the ```-1``` factor on the second particle acceleration to account for the inverted direction of the x and y displacement vectors from $\overrightarrow{x_{12}}$ to $\overrightarrow{x_{21}}$.

```javascript
attract(otherParticle) {
    let x_dist = otherParticle.x - this.x;
    let y_dist = otherParticle.y - this.y;
    let dist = Math.pow(x_dist * x_dist + y_dist * y_dist, 0.5);
    let x_factor = G * (x_dist) / (dist * dist * dist);
    let y_factor = G * (y_dist) / (dist * dist * dist);

    this.x_a += x_factor * otherParticle.mass;
    this.y_a += y_factor * otherParticle.mass;

    otherParticle.x_a += -1 * x_factor * this.mass;
    otherParticle.y_a += -1 * y_factor * this.mass;

  }
```

