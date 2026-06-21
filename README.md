## Lecture 1

The main goal of this course is to build a foundational understanding of **computational vision**. However, before we can dive into the material, we need to clear something up right out of the gate: _What exactly is computational vision, and how does it differ from traditional computer vision?_

### Computer Vision

Computer vision is the field where we try to enable machines—like cameras, robots, and self-driving cars—to mimic vision and generate useful outputs based on visual cues. However, these systems aren't actually interested in understanding or replicating _human_ vision. Instead, they care strictly about the mathematical frameworks and engineering tricks that deliver the best, most accurate results for a given task.

### Computational Vision

While computer vision and computational vision share a lot of the same topics and features, **computational vision** is specifically concerned with modeling and understanding how humans _actually_ see.

In an attempt to decode human vision from a biological perspective, scientists have come up with a variety of explanations across different levels of human anatomy:

- **The Cell Level:** Some researchers argue that vision happens because specific, individual cells fire in response to certain visual features.
- **The Circuit Level:** Others look at the brain's circuitry, arguing that complex networks of interconnected neurons map directly to specific objects.
- **The Brain Region Level:** Another group might reason that distinct, specialized areas of the brain are responsible for dedicated tasks across a person's visual system.

While all of these biological perspectives are technically correct, they don't actually explain the _mechanistic_ and _algorithmic_ level of human vision—the actual rules and processes computing the data. Bridging that gap and understanding the algorithm behind the sight is exactly what we aim to do throughout this course.

### Course Material

Throughout this course, we will focus our attention on three core pillars of the human visual system. We will break these down to understand exactly how our brains process the complex sensory data coming through our eyes:

- **1. Motion Processing:** How do we perceive movement, calculate the speed and direction of objects, and navigate through a constantly changing environment?
- **2. Color Processing:** How does our visual system interpret different wavelengths of light, maintain color constancy under changing light conditions, and use color to distinguish objects?
- **3. Shape/Stereo Processing:** How do we perceive depth, construct 3D representations of the world from flat 2D retinal images, and recognize distinct geometric shapes?

### Marr's Three Levels

Now that we have mapped out the scope of our material, we need to define the framework we will use to interact with and understand it. But before we do, consider this famous quote by neuroscientist David Marr:

> "Trying to understand perception by studying only neurons is like trying to understand bird flight by studying only feathers: It just cannot be done. In order to understand bird flight, we have to understand aerodynamics; only then do the structure of feathers and different shapes of birds' wings make sense." — David Marr (1982)

To avoid the trap of looking _only_ at the "feathers" (the biology), Marr established a framework that breaks down any information-processing system into three distinct levels of analysis:

- **1. Computational Level (The "What" and "Why"):** What is the goal of the computation, why is it appropriate, and what is the underlying logic or strategy by which it can be carried out?
- **2. Representation and Algorithm Level (The "How"):** How can this computational theory be implemented? Specifically, what is the representation for the input and output, and what is the step-by-step algorithm used to transform one into the other?
- **3. Implementation Level (The Physical Machinery):** How can the representation and algorithm be physically realized? What is the actual hardware or biological substrate executing the process?

### The Classic Example: A Simple Calculator

To see how these tie together, let’s look at Marr’s original example of a basic cash register or calculator designed for addition:

- **Computational Level:** The goal is to find the sum of two numbers. The logic dictates that addition is commutative (A + B = B + A) and associative.
- **Representation & Algorithm Level:** The numbers are represented as binary strings (0s and 1s), and the system uses a binary addition algorithm (carrying the 1, etc.) to compute the total.
- **Implementation Level:** This is the concrete hardware where the program runs—in this case, silicon circuits, transistors, and logic gates.

### Our Focus in This Course

While all three levels are fascinating, **our primary concern in this course is at the first level: the Computational Level.** Because of this, we will constantly be asking core questions like:

1. What is the fundamental problem that the visual system is trying to solve?
2. Why is this specific problem so difficult?
3. What assumptions can we make about the physical world to make the problem solvable?

### Summary

To recap, we’ve defined one of the main goals of computational vision as seeking to understand _how_ humans see. Throughout this course, we will heavily focus on Marr’s **Computational Level**—investigating why visual problems are difficult and what constraints make them solvable. While computational vision is deeply intertwined with traditional computer vision, it asks fundamentally different, human-centric questions.

### Quiz

**Scenario:** Luke and Han are trying to catch a mosquito. Every time they raise their hand, the mosquito flies away.

- **Luke says:** This is because the mosquito has a specific neuron in its visual cortex that fires every time a hand appears in its visual field.
- **Han says:** The mosquito’s visual system performs a dot product between the incoming image and an internal template of a hand. If that dot product is greater than a specific threshold, the mosquito flies away.

**Question:** At which of Marr’s three levels is each explanation?

### Answer Breakdown

> - **Luke's Explanation is at the Implementation Level.**
>     
>     By pointing directly to a physical, biological structure—the specific neuron firing in the visual cortex—Luke is describing the hardware machinery executing the behavior.
>     
> - **Han's Explanation is at the Representation and Algorithm Level.**
>     
>     Han is describing the step-by-step mathematical strategy. He defines the representation (an incoming image and a template) and the exact algorithm (calculating a dot product and testing if it is greater than a threshold).
>


## Lecture 2: Motion Processing (Part 1)

### Motion Psychophysics

To kick off our deep dive into motion, we need to introduce **psychophysics**. This is the scientific field that attempts to map the relationship between physical stimuli ($\Phi$) and psychological perception ($\Psi$).

Historically, researchers have studied a wide range of physical phenomena to see how a change in the physical world affects our mental perception of that change. For instance, if you gradually turn up the volume on a speaker, people will perceive the sound getting louder. However, a strict 1:1 mapping is almost always incorrect; doubling the physical volume of the speaker does not necessarily mean we perceive it as being exactly twice as loud.

Since this course focuses specifically on computational vision rather than general perception, we need to simplify how we represent our input data. To do this, we will focus primarily on **two-dimensional motion**, leaving out complex factors like rotation and changes in depth. This allows us to mathematically represent any motion vector using just two numbers:

$$(V_x, V_y)$$

Correspondingly, our visual system is capable of perceiving both of these directional components of movement.

### Infinite Lines and the Problem of Ambiguity

When we observe the movement of finite objects in a fully visible space, tracking motion is relatively straightforward. However, as psychologist Hans Wallach discovered in 1935, **infinite lines are fundamentally ambiguous**. This means we don't necessarily perceive their true physical motion because we cannot see the boundaries or endpoints of the line.

Let's break this down using a finite line segment as an example:
![[Pasted image 20260620153230.png]]
This image demonstrates a finite line shifting. If we translate the line toward the positive x-direction, it shifts right. If we shift it down toward the negative y-direction, we get a completely different position (represented by the dashed line). We can easily tell these two movements apart because we can track the moving endpoints of the segment.

Now, consider what happens if that same line becomes **infinite** (or if we view it through a restricted aperture where the ends are hidden):

![[Pasted image 20260620153427.png]]
 Because the line is infinite, we completely lose our reference points. A physical movement directly to the right looks identical to a physical movement directly downward. Without visible endpoints, the two distinct physical actions produce the exact same visual stimulus, making infinite lines inherently ambiguous.
This visual uncertainty introduces a classic challenge in motion processing—one that our brains have to solve using specific assumptions about how the world works.!

### The Mathematics of Motion Ambiguity

To gain a precise mathematical understanding of how these infinite lines move, we can decompose any true velocity vector into two perpendicular components.

Let $v$ be the actual motion vector of the line. We can split $v$ into:

1. **Normal Velocity ($v^{\perp}$):** The component orthogonal (perpendicular) to the orientation of the line.
2. **Parallel Velocity ($v^{\parallel}$):** The component running completely parallel to the line.

Mathematically, this gives us:

$$v = v^{\perp} + v^{\parallel}$$

![[Pasted image 20260620155140.png]]
 - **Black Vector ($v$):** The true physical motion of the line.
 - **Red Vector ($v^{\perp}$):** The normal velocity component.
 - **Blue Vector ($v^{\parallel}$):** The parallel velocity component.    

Based on scientific observations, **the human visual system is completely blind to the parallel velocity ($v^{\parallel}$) of an infinite line.** Because the line lacks distinct features along its length, shifting it parallel to itself changes absolutely nothing in our visual field.

Consequently, the _perceived_ motion of an infinite line is strictly determined by its normal velocity ($v^{\perp}$). This means an infinite number of different physical movements can produce the exact same perceived motion, as long as they all share the identical normal velocity component.

### Wallach’s Formalization (1935)

Hans Wallach formalized this phenomenon through two central observations:

1. **The Indistinguishability Principle:** Let $M$ be a movie generated by an infinite line of orientation $\theta$ moving at velocity $v$. Any other physical velocity $v'$ that shares the exact same normal velocity ($v'^{\perp} = v^{\perp}$) will generate the _exact same movie $M$_.
2. **The Perceptual Claim:** When presented with a movie of an infinite line of orientation $\theta$ moving at velocity $v$, humans will always perceive the **normal velocity** ($v^{\perp}$).

**Why does the brain choose the normal velocity?** Wallach proved that the normal velocity vector is mathematically the **shortest vector** (the slowest velocity) capable of producing that specific visual data.

### The Computational View (Bringing back Marr)

Let’s connect this back to the core questions we established for Marr's first level (The Computational Level):

- **What is the problem?** Estimating the true physical motion of an object in the world.
- **Why is it difficult?** Because of the **Aperture Problem** (the inherent ambiguity of infinite or edge-constrained lines where parallel motion is hidden). The data hitting our eyes is underdetermined—one visual stimulus can mean a million different physical realities.
- **What assumptions make it solvable?** Our visual system solves this puzzle by applying an internal constraint or preference: **Humans possess a strong biological bias toward slower motions.** By assuming the world moves as slowly as possible to explain the data, the brain successfully snaps the ambiguous input into a single, definitive perception (the normal velocity).


### The Barber Pole Effect

Building on the ambiguity of infinite lines, Wallach designed a second set of experiments to observe what happens when lines move through confined windows, or apertures.

> 💡 **Interactive Check:** To get a clear visual handle on this experiment, take a quick look at the interactive [Barber Pole Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment1.html).
![[Pasted image 20260620160831.png]]

Formally, Wallach defined this second phenomenon as follows:

> _For a line at orientation $\theta$ moving through a rectangular aperture of orientation $\theta_A$ and at velocity $v$, the perceived direction of motion is biased toward $\theta_A$._

This happens because the edges of the window slice the line, creating endpoints known as **terminators**.

### Terminator Velocity

To understand why the shape of the window dictates the perceived motion, we can look at the mathematical modeling of **Terminator Velocity**.

By definition: For a line at orientation $\theta$ that intersects a static boundary line of orientation $\theta_2$, the _terminator velocity_ is the vector that tracks the physical movement of that intersection point. This velocity is constrained to move exclusively along the boundary, meaning **this velocity vector is always in the direction of $\theta_2$.**

![[Pasted image 20260620165856.png]]

### Wallach’s Third Phenomenon: Terminator Dominance

If you revisit the simulation, you will notice that the rectangular window creates two different types of boundaries: the long edges and the short edges. This brings us to Wallach’s third major observation regarding how our brain reconciles these competing signals:

> _For a line at orientation $\theta$ moving through an aperture at velocity $v$, let $v^{T_i}$ be the velocity of the individual terminators ($i$). If there is a **dominant** terminator velocity (i.e., for many points $i$, $v^{T_i} = v^*$), then the perceived velocity of the entire line will be $v^*$.

In simpler terms, because the rectangular aperture has much longer vertical edges than horizontal ones, there are significantly more terminators traveling vertically than horizontally. The visual system aggregates these signals, allows the majority ("dominant") terminator velocity to win, and tricks your brain into perceiving a vertical upward motion—exactly like the spinning stripes on a classic barber pole!

## Summary

To wrap up this portion of the lecture:

- **Psychophysics** acts as the bridge mapping physical stimuli to our psychological perception.
    
- The motion of an **infinite line** is inherently ambiguous due to the lack of visible feature points, leading humans to perceive only the **normal velocity** (the slowest physically consistent motion).
    
- When a line is bound by a window, its perceived motion depends heavily on **terminator velocity**—the movement of the intersection points along the edges of the aperture.
    

## Quiz

Look closely at the two lines below:
![[Pasted image 20260620171130.png]]

**Question:** What will be the perceived direction of motion for each of these two lines?

### Answer Breakdown

- **The Left Line (Infinite Vertical Line):** The line is perfectly vertical, which means its **parallel direction is straight down** and its **normal direction is to the right**. Since humans are blind to parallel motion along an infinite line, the downward component of the arrow completely disappears from our perception. Therefore, we perceive the line moving **strictly to the right**.
- **The Right Line (Vertical Line with Horizontal "I-Bar" Caps):** Here, the terminators are formed by the vertical line intersecting the horizontal caps. As the line moves down and to the right, these intersection points are physically constrained to slide along those horizontal boundaries. Because the terminators travel **horizontally to the right** along the cap edges, this dominant terminator velocity dictates our perception. Therefore, we perceive this line moving **strictly to the right** as well!

## Lecture 3: Combining Motion Signals

### The Constraint Line

To build on the Aperture Problem from the last lecture, let's introduce some formal mathematical terminology to describe that family of ambiguous velocities.

For a line of orientation $\theta$ moving with velocity $v$, the entire set of possible physical velocities that share the exact same normal velocity ($v^{\perp}$) is called the **Constraint Line**.

![[Pasted image 20260620202138.png]]
Admittedly, defining this as a "constraint line" doesn't change anything about what we have discussed so far. However, this geometric tool becomes incredibly powerful the moment we move past a single isolated line and start looking at scenes with **multiple** moving lines.

### Combining Lines: Wallach's Intersection of Constraints (IOC)

What happens when our visual system is confronted with two overlapping, moving gratings or lines?

> 💡 **Interactive Check:** To see this phenomenon in action, take a moment to watch the interactive [Intersection of Constraints Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment2.html).

As Wallach observed, when two different sets of lines move over one another, the human visual system doesn't see two separate movements. Instead, the two sets of lines appear to lock together and **move coherently** as a single unified object (at least under certain conditions).

![[Pasted image 20260620204417.png]]

### Interpreting Coherent Motion: Two Competing Theories

To explain how the brain computes the single, unified direction of this combined object, researchers introduced two major competing hypotheses:

#### 1. The Vector Average (VA)

This theory suggests a bottom-up, blending approach. It states that the perceived motion of the combined object is simply the mathematical **vector average** of the individual perceived motions (the normal velocities) of the two separate line sets.
- _In short:_ The brain looks at where Line A is going, looks at where Line B is going, and splits the difference down the middle.

#### 2. The Intersection of Constraints (IOC)

This theory takes a more rigid, geometric approach. It states that the perceived motion is the **exact intersection point** where the constraint lines of the individual motions cross.
- _In short:_ Because each line carries a whole "constraint line" of possible true velocities, the brain plots both constraint lines. The single point where they intersect represents the _only_ true physical velocity that satisfies both moving lines simultaneously.


---
### How to Compute the Constraint Lines and the IOC
To understand how the brain—or a computer vision algorithm—finds the Intersection of Constraints (IOC), we look at the underlying vector mathematics.

#### 1. Defining the Constraint Line Equation

Let a line have a known unit normal vector $\hat{n} = (\cos\theta, \sin\theta)$, where $\theta$ is the perpendicular angle of the line. If the line has a measured normal velocity magnitude of $c$, then its normal velocity vector is:

$$v^{\perp} = c\hat{n}$$

Because we are entirely blind to any parallel movement, the true, absolute velocity vector $v = (V_x, V_y)$ could be _any_ vector whose projection onto the normal vector equals $c$. This gives us the **Motion Constraint Equation**:

$$v \cdot \hat{n} = c$$

Expanding this into standard 2D vector components yields a linear equation for the constraint line:

$$V_x \cos\theta + V_y \sin\theta = c$$

Every point $(V_x, V_y)$ along this straight line in velocity space represents a physically valid velocity consistent with the visual data.

#### 2. Computing the Intersection (The IOC Solution)

When we have two different sets of overlapping lines (Line 1 and Line 2) moving coherently, we get a system of two linear equations with two unknowns ($V_x$ and $V_y$):

$$\begin{cases} V_x \cos\theta_1 + V_y \sin\theta_1 = c_1 \\ V_x \cos\theta_2 + V_y \sin\theta_2 = c_2 \end{cases}$$

Where:

- $\theta_1, \theta_2$ are the normal orientations of the two line configurations.
- $c_1, c_2$ are their respective measured normal velocity magnitudes.

By solving this system using standard linear algebra, we pinpoint the single coordinate $(V_x^*, V_y^*)$ where the two constraint lines cross. This intersection represents the definitive, unambiguous true velocity of the pattern that our visual system snaps onto.

---



## Motion Perception in Computer Vision

To gain a deeper understanding of human vision, let’s pivot and look at how _computers_ solve the problem of motion.

Formally, the problem is defined as follows: Given two consecutive image frames, $I_1$ and $I_2$, what is the velocity vector $v = (V_x, V_y)$ at which the scene is moving?

![[Pasted image 20260620205559.png]]

At first glance, one might argue this problem is trivial and can be solved simply by finding a displacement such that:

$$I_2(x + V_x, y + V_y) = I_1(x, y)$$

However, this equation relies on a massive underlying assumption that frequently fails in the real world: **Brightness Constancy**. It assumes that a physical point on an object does not change its pixel intensity value as it moves from one frame to the next.

To solve this mathematically, we look for the velocity that minimizes the **Sum of Squared Differences (SSD)**:

$$v^* = \arg\min_v \text{SSD}(V_x, V_y)$$

Where the SSD is defined as:

$$\text{SSD}(V_x, V_y) = \sum_{x,y} \left( I_2(x + V_x, y + V_y) - I_1(x, y) \right)^2$$

Under a perfect assumption of constant brightness, there will be a true solution where the minimum SSD is exactly zero. In real-world conditions, minimizing this equation gives us a highly accurate estimation of the true motion.

A naive, "brute-force" algorithm to solve this would simply test a massive grid of different potential velocities and choose the one that yields the lowest SSD. While computationally inefficient, it is a valid baseline approach.

💡 **The Bridge Between Fields (Assuming Constant Brightness):** It is easy to observe that **under the strict assumption of constant brightness**, the Intersection of Constraints (IOC) from human psychophysics yields an SSD of exactly zero. Therefore, under these ideal conditions, the human IOC velocity is mathematically identical to the computer's optimal $v^*$. This reveals a beautiful convergence between computer vision and computational vision, showing that both fields are optimizing for the exact same physical reality.

### The Lucas-Kanade Algorithm

Because a brute-force search is highly impractical, Bruce Lucas and Takeo Kanade introduced an algorithm in 1981 that approximates the solution efficiently.

If we assume the velocity displacements $V_x$ and $V_y$ are small, we can use a first-order **Taylor series approximation** to linearize our SSD objective function:

$$\hat{\text{SSD}}(V_x, V_y) = \sum_{x,y} \left( I_2(x,y) - I_1(x,y) + V_x I_x + V_y I_y \right)^2$$

Where $I_x$ and $I_y$ are the spatial derivatives (gradients) of the image with respect to $x$ and $y$.

Furthermore, if $I_1$ and $I_2$ are frames captured with a very small time difference between them, we can denote the temporal difference as $I_t = I_2(x,y) - I_1(x,y)$. This simplifies our approximation to:

$$\hat{\text{SSD}}(V_x, V_y) = \sum_{x,y} \left( I_t + V_x I_x + V_y I_y \right)^2$$

To find the minimum of this quadratic function, we take the derivatives with respect to $V_x$ and $V_y$ and set them to zero. This yields a neat system of linear equations in the form of $Av = b$:

$$\begin{pmatrix} \sum I_x^2 & \sum I_x I_y \\ \sum I_x I_y & \sum I_y^2 \end{pmatrix} \begin{pmatrix} V_x \\ V_y \end{pmatrix} = - \begin{pmatrix} \sum I_x I_t \\ \sum I_y I_t \end{pmatrix}$$

#### The Catch: Matrix Invertibility

This linear system yields a unique solution if and only if the matrix $A$ is invertible.

- For an **infinite line**, all the spatial gradients point in the same direction, making the matrix $A$ have a rank of 1.
- Because a rank-1 matrix is not invertible, there are an infinite number of solutions.
- If you map out this infinite family of solutions mathematically, **it traces out exactly the same constraint line** we derived in human psychophysics!
    

> 🛠️ _Self-Study Tip: Try calculating this by hand or writing a quick script for a trivial case (like no motion, or a simple single-pixel line shift). Watching the constraint line emerge directly out of image gradients is incredibly satisfying!_

### The Computational View (Bringing back Marr)

Let’s map this computer vision approach back onto Marr's framework:

- **What is the problem?** Estimating the underlying velocity field across multiple changing image frames.
- **Why is it difficult?** Because we need a way to mathematically formalize how a physical velocity $v$ transforms pixel intensities across time.
- **What assumptions make it solvable?** The **Brightness Constancy Constraint**.

At the _Algorithmic Level_, various techniques can be deployed to minimize this error function—ranging from the classic Lucas-Kanade system of linear equations to modern deep learning models (like Flownet or RAFT) that many of you may recognize from advanced machine learning courses.

> 📝 **Note:** The basic Lucas-Kanade algorithm shown here is highly foundational and assumes very small movements. In modern applications, engineers use hierarchical "coarse-to-fine" image pyramids to handle large velocities. To see how these principles are expanded to modern, real-world systems, check out this classic highly-cited text: [An Iterative Image Registration Technique with an Application to Stereo Vision (Lucas & Kanade)](https://www.ri.cmu.edu/pub_files/pub3/lucas_bruce_d_1981_1/lucas_bruce_d_1981_1.pdf).

## Summary

- An infinite line moving with velocity $v$ defines a **constraint line** in velocity space.
- When two different line orientations overlap (forming a plaid pattern), they can be perceived as moving together via a **coherent motion** vector.
- Computers estimate motion based on **brightness constancy**. The **Lucas-Kanade algorithm** solves this efficiently by local linearization and solving systems of linear equations.
- Ultimately, **computers and humans face the exact same mathematical uncertainties** (the aperture problem) when extracting motion from the physical world.

## Quiz


**Question 1:** **True or False:** For any two images generated by two infinite lines of different orientations moving with velocity $v$, the IOC velocity will always have the minimum SSD.

**Answer: FALSE.** 
**Explanation:** This statement is only true **if we assume strict brightness constancy**.
 - **If brightness constancy holds:** The physical movement doesn't change pixel intensities, meaning the true IOC velocity perfectly matches the frames and yields an $\text{SSD} = 0$ (the absolute minimum).
 - **In the real world (Without brightness constancy):** If the lighting changes, a shadow passes by, or automatic camera gain kicks in between Frame 1 and Frame 2, the pixel values change. In this case, the true physical velocity ($v_{\text{IOC}}$) will **not** have an SSD of zero, and a completely different, incorrect velocity vector might end up mathematically minimizing the SSD instead.



**Question 2: True or False:** For any two images generated by two infinite lines of different orientations moving with velocity $v$, the basic Lucas-Kanade algorithm will compute the IOC velocity.

 **Answer: FALSE** : The basic Lucas-Kanade algorithm relies on a first-order Taylor series approximation, meaning it only computes the exact solution if the physical velocity displacement $v$ is extremely small (infinitesimal). If the movement between the two frames is large, the linear approximation breaks down, and the basic algorithm will fail to find the true IOC velocity without the aid of multi-scale image pyramids.



## Lecture 4: Bayesian Decision Theory

### Introduction to the Framework

Before we continue deeper into our core visual material, we need to familiarize ourselves with a powerful mathematical framework that will help us analyze how the brain makes decisions under uncertainty. This framework is known as **Bayesian Decision Theory**.

While this field is vast enough to easily fill the scope of an entire course on its own, we are going to focus on a high-level overview of its most foundational concepts.

To understand what makes the Bayesian approach unique, let's look at a simple everyday example:

### The Thermometer Dilemma

Imagine you want to know the exact temperature outside. You open a local weather website, and it displays a digital reading of **$23.4^\circ\text{C}$**.

- **The Naive Answer:** The temperature outside is exactly $23.4^\circ\text{C}$ because that's what the sensor says.
- **The Bayesian Answer:** The actual outside temperature _depends_ on two competing factors: the reliability of that specific thermometer sensory data, and the historical climate patterns of your region for this exact time of year.
 ![[Pasted image 20260621095842.png]]
 _The website UI displaying a current reading of 23.4°C._

Now, let's look at a broader historic dataset:
![[Pasted image 20260621101056.png]]

 A historical climate graph showing that the average temperature for this specific day in April over the last fifty years is actually $20.0^\circ\text{C}$.

Suddenly, you have two different sources trying to tell you the truth:

1. **The Sensor Measurement:** $23.4^\circ\text{C}$
2. **The Historical Prior:** $20.0^\circ\text{C}$


How do we blend these two sources together? Under normal circumstances, you trust a functional thermometer more than historical averages. But what if the thermometer suddenly flashes **$50.0^\circ\text{C}$** in the middle of April? Your common sense immediately kicks in—you know $50.0^\circ\text{C}$ is historically nearly impossible for April, so you infer the thermometer is highly likely to be broken.

Bayesian decision theory provides the exact mathematical language to weigh these two forces against one another.

### Bayes' Rule: The Mathematics of Inference

To formalize this, let $Y$ be our observed measurement (the data from the world) and let $X$ be our variable of interest (the true state of the world we want to find). Bayes' Rule is written as:

$$P(X|Y) = \frac{P(X)P(Y|X)}{P(Y)}$$

To understand what this means, we break it down into its core functional components:

- **$P(X|Y)$ — The Posterior Probability:** The probability that the true state of the world is $X$, _given_ that we just observed measurement $Y$. This is what we are trying to calculate.
- **$P(Y|X)$ — The Likelihood:** The probability that the sensor would return measurement $Y$ _if the true state_ of the world actually were $X$. This represents the noise and reliability of our physical hardware.
- **$P(X)$ — The Prior Probability:** Our baseline internal belief about the world before we even look at a sensor. This is built from history, experience, and environmental constraints (e.g., "It's rarely $50^\circ\text{C}$ in April").
- **$P(Y)$ — The Normalizing Constant:** The total probability of observing measurement $Y$ across all possible states of the world.

To finish the thought: This formula means that the actual state of the world given a measurement **is not simply equivalent to the measurement itself, but is a fluid compromise between the physical evidence we observe ($The\ Likelihood$) and our foundational expectations of what makes sense ($The\ Prior$).** If our sensor is incredibly precise, the Likelihood dominates, and the Posterior shifts toward the measurement. If our sensor is highly noisy or returns an absurd anomaly, our Prior dominates, and our brain dismisses the data as an error.


### The Mathematical Case: Gaussian Distributions

Let’s look at a concrete mathematical case where both our prior knowledge and our sensory noise follow a normal (Gaussian) distribution.

Suppose our prior belief about the true state $X$ is distributed around a historical mean $\mu$ with a prior variance $\sigma^2_p$:
$$X \sim N(\mu, \sigma^2_p)$$
Next, suppose our sensor measurement $Y$ is a noisy estimate centered around the true state $X$ with a measurement variance $\sigma^2$:
$$Y|X \sim N(X, \sigma^2)$$
By multiplying the Gaussian prior and the Gaussian likelihood together according to Bayes' rule, the resulting posterior distribution $X|Y$ is also a Gaussian:
$$X|Y \sim N(\mu_{x|y}, \sigma^2_{x|y})$$
Where the new posterior mean $\mu_{x|y}$ is calculated as:
$$\mu_{x|y} = \frac{\frac{1}{\sigma^2}Y + \frac{1}{\sigma^2_p}\mu}{\frac{1}{\sigma^2} + \frac{1}{\sigma^2_p}}$$
If we rewrite this by defining **precision** as the inverse of variance ($w = \frac{1}{\sigma^2}$ and $w_p = \frac{1}{\sigma^2_p}$), the equation simplifies into a beautifully elegant form:
$$\mu_{x|y} = \frac{wY + w_p\mu}{w + w_p}$$
This demonstrates that the Bayesian estimation is fundamentally a **weighted average**. It automatically shifts more weight toward whichever distribution is more certain (i.e., has less noise / higher precision). Furthermore, this Bayesian estimator is mathematically **optimal**, meaning it is guaranteed to minimize the expected squared error.

### Tying the Math Back to the Thermometer

Let’s look at how this formula behaves when we push the reliability of our thermometer to its logical extremes:

- **The Perfect Instrument ($\sigma^2 \to 0$):**
    If the thermometer is exceptionally high-grade and has virtually zero noise, its precision approaches infinity ($w \to \infty$). In the weighting formula, the prior term drops out completely:$$\mu_{x|y} \to Y = 23.4^\circ\text{C}$$
    The brain completely trusts the measurement and ignores history.
    
- **The Broken Instrument ($\sigma^2 \to \infty$):**
    
    If the thermometer is highly unstable, cheap, or completely broken, its measurement noise approaches infinity, meaning its precision drops to zero ($w \to 0$). The measurement term completely vanishes from the equation:$$\mu_{x|y} \to \mu = 20.0^\circ\text{C}$$
    The brain completely dismisses the faulty sensor data and falls back entirely on its historical climate expectations.

Ultimately, Bayesian inference provides a mathematically rigorous way to extract a stable, accurate perception of reality even when we are forced to navigate the world using deeply noisy instruments.

### Connecting Bayesian Decision Theory to Motion Perception

Now that we have a solid grasp of the Bayesian framework, let's see how it elegantly resolves a classic conflict in human motion perception.

Recall the two competing methods we discussed for combining multiple moving lines: **Vector Average (VA)** and the **Intersection of Constraints (IOC)**. Both are mathematically sound frameworks, and each successfully predicts human perception under specific visual scenarios.

However, we run into a major dilemma when these two theories do not agree on the final direction of motion.

 ![[Pasted image 20260621104911.png]]
_A vector diagram showcasing a geometry where the Vector Average (VA) arrow points horizontally, while the Intersection of Constraints (IOC) point sits far off to the side, pointing diagonally._

In a scenario like the one above, where the two theories predict completely different directions, what does a human actually perceive?

### The Moving Rhombus Experiment

To see how the brain breaks this tie, consider the classic moving rhombus experiment:

> 🔬 **Interactive Check:** Open the [Moving Rhombus Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment3.html). For the best effect, zoom in closely on your screen so that the outer edges of the window disappear, leaving only the moving rhombus itself visible.

If your visual system behaves like most people's, you will witness a fascinating shift in perception:

- **Thick Rhombuses:** Appear to move diagonally, tracking the **IOC** solution.
- **Thin/Narrow Rhombuses:** Appear to move strictly horizontally, shifting away from the IOC and tracking the **Vector Average**.

Why does changing the mere _thickness_ of a shape alter the fundamental algorithm the brain uses to see it? One could lazily argue that the brain randomly selects different algorithms for different shapes, but this is deeply unsatisfying. It fails to answer _why_ the visual system does this, and it doesn't give us a way to simulate the underlying mechanism.

### The Bayesian Unified Motion Model

Instead of assuming the brain bounces between two separate algorithms (VA and IOC), we can use **Bayesian Decision Theory** to model this behavior using a single, unified equation.

To build this model, we establish two core, human-centric assumptions:

1. **Noisy Measurements:** Local motion signals extracted along edges are inherently ambiguous and noisy.
2. **The Slow-Motion Prior:** As Hans Wallach noted, the human visual system possesses a strong biological preference for slower velocities over faster ones.

Let's translate these assumptions into a formal probabilistic framework:

#### 1. The Likelihood (Constant Intensity Assumption)

First, we look at the standard optical flow constraint error function $C(\vec{v})$, which measures how poorly a velocity vector $\vec{v} = (v_x, v_y)$ fits the constant brightness constraint across local image gradients:
$$C(\vec{v}) = \sum_{x,y,t} w(x,y,t)(I_x v_x + I_y v_y + I_t)^2$$

Assuming the noise in our visual measurements is Gaussian, the probability of observing a particular set of image gradients given a velocity $\vec{v}$ (The Likelihood) is expressed as:
$$P(I(x,y) | \vec{v}) = \alpha e^{-\frac{C(\vec{v})}{2\pi \sigma^2_n}}$$

Where $\sigma^2_n$ represents the variance of our sensory measurement noise.

#### 2. The Slow-Motion Prior

Next, we formalize our internal bias for slow motion. We model this as a Gaussian distribution centered at zero velocity ($\vec{v} = 0$) with a variance of $\sigma^2_p$:
$$P(\vec{v}) = \alpha e^{-\frac{\|\vec{v}\|^2}{2\sigma^2_p}}$$

#### 3. The Posterior Probability

By multiplying our likelihood and our prior together according to Bayes' rule, we get our final posterior probability:
$$P(\vec{v} | I) = \alpha \left( \prod_{x,y} P(I(x,y) | \vec{v}) \right) P(\vec{v})$$

**To finish the thought:** This means that finding the velocity we actually perceive is not just a matter of minimizing image gradient errors (maximizing the Likelihood). Instead, **the brain searches for a velocity vector that balances a low image-reconstruction error with a low absolute speed.** 

---

### The Elegant Mathematical Solution

To find the most likely perceived velocity ($\vec{v}^*$), we maximize the posterior probability, which is mathematically equivalent to minimizing the negative log-posterior.

Let's bring back our gradient matrix $M$ and temporal difference vector $b$ from the Lucas-Kanade algorithm:

$$M = \begin{pmatrix} \sum I_x^2 & \sum I_x I_y \\ \sum I_x I_y & \sum I_y^2 \end{pmatrix}, \quad b = -\begin{pmatrix} \sum I_x I_t \\ \sum I_y I_t \end{pmatrix}$$

When we differentiate our Bayesian objective function (which now includes the slow-motion prior term) and set it to zero, the slow-motion prior acts as a **regularization term** added directly onto the diagonal of our gradient matrix. This gives us the final Bayesian motion estimation:

$$\vec{v} = \left( M + \frac{\sigma^2_n}{\sigma^2_p} I \right)^{-1} b$$

Where $I$ is the identity matrix, and $\frac{\sigma^2_n}{\sigma^2_p}$ is the ratio of sensory noise to prior certainty.

### How This Explains the Rhombus Illusion

This single, elegant equation completely explains why thick and thin rhombuses look different:

- **Why Thin Rhombuses look like Vector Average:**
    
    A thin, narrow rhombus has very short edges, meaning it provides very little visual contrast data. Because the signal-to-noise ratio is incredibly low, our sensory noise is very high ($\sigma^2_n \to \infty$). In our equation, as $\sigma^2_n$ blows up, the regularization term dominates, **pulling the perceived velocity heavily toward zero (slower speeds).** This strong gravitational pull toward the origin forces the vector away from the distant, fast diagonal IOC point and snaps it straight to the shorter, more conservative horizontal Vector Average path!
    
- **Why Thick Rhombuses look like IOC:**
    
    A thick rhombus provides long, prominent edges with highly reliable, strong visual data. Because the sensory data is clear, the measurement noise is tiny ($\sigma^2_n \to 0$). The regularization fraction vanishes, reducing the equation right back to the standard Lucas-Kanade/IOC solution. The brain completely trusts the visual information and perceives the true, fast diagonal motion.

Rather than running separate algorithms, the human brain uses a single, beautifully optimized Bayesian framework that constantly dials between data-driven geometry (IOC) and internal survival expectations (Slow-Motion Prior) based entirely on how clean the visual signal is!

### Conclusions

To wrap up our discussion on Bayesian Decision Theory and its applications to motion, we can summarize the core insights into a few powerful takeaways:

- **The Integration vs. Segmentation Dilemma:** Analyzing motion in the real world constantly forces our visual system to decide whether to integrate multiple local motion signals together (treating them as one coherent moving object, like the thick rhombus) or segment them into separate entities.
- **Minimal Assumptions, Maximum Results:** The Bayesian model operates on a surprisingly minimal set of ecological constraints: it assumes local physical measurements are inherently ambiguous/noisy, and that things in our environment generally tend to move slowly and smoothly.
- **Remarkable Biological Agreement:** When we calculate the mathematically optimal interpretation under these exact constraints, the results land in remarkable agreement with actual human psychophysical data.
- **Demystifying Visual Illusions:** Rather than proving our brains are faulty, complex visual illusions (like the thin rhombus or varying plaid behaviors) are actually the side-effect of a single, highly optimal computational strategy doing its job under noisy conditions.
    

## Quiz

### Question 1: The Plaid Contrast Challenge

![[Pasted image 20260621121616.png]]

 _Scenario:_ Both of the plaid patterns shown in the image are physically moving at the exact same velocity. Plaid **a** is high-contrast (black and white), while Plaid b is low-contrast (light gray and dark gray).
 
**Question:** Which plaid will appear to move faster to a human observer?

#### Answer Breakdown

**Plaid 'a' (High-Contrast) will appear to move faster.**

- **The Mathematical Explanation:** Because Plaid **b** has very low contrast, the image gradients ($I_x, I_y$) are weak, resulting in a low signal-to-noise ratio and high sensory noise ($\sigma^2_n \to \infty$).
- In our Bayesian regularization formula $\vec{v} = \left( M + \frac{\sigma^2_n}{\sigma^2_p} I \right)^{-1} b$, a larger noise value $\sigma^2_n$ strengthens the pull of the slow-motion prior. This weights the posterior heavily toward zero velocity, causing the human brain to underestimate the speed of the low-contrast plaid.
- Because Plaid **a** has crisp, reliable contrast, its noise is minimal, allowing the brain to perceive its true, faster speed.

### Question 2: The Ultrasound Estimation

 **Scenario:** We measure a fetus's head size using an ultrasound and get a value of **$10.0\text{ cm}$**. We know that ultrasound measurements are noisy. From prior clinical data, we know that a fetus at this specific gestational stage has a mean head size of **$15\text{ cm}$** with a standard deviation of $3$.
 **Question:** Will the final Bayesian estimate be _below_, _above_, or _equal to_ $10.0\text{ cm}$?

#### Answer Breakdown

**The Bayesian estimate will be ABOVE $10.0\text{ cm}$.**

- **The Mathematical Explanation:** Let's look at the variables given:
    - Sensor Measurement ($Y$) = $10.0\text{ cm}$
    - Prior Mean ($\mu$) = $15\text{ cm}$
- Recall our Gaussian Bayesian posterior mean formula:$$\mu_{x|y} = \frac{wY + w_p\mu}{w + w_p}$$
- Because the ultrasound instrument is explicitly stated to be noisy, its precision weight ($w$) is finite. Therefore, the prior weight ($w_p$) acts as a statistical magnet. It pulls the posterior mean away from the raw sensor reading ($10.0\text{ cm}$) and shifts it upward toward the historical average of $15\text{ cm}$.
- As a result, the calculated optimal estimate will sit somewhere between $10.0\text{ cm}$ and $15\text{ cm}$ (e.g., around $11$ or $12\text{ cm}$ depending on the exact sensor variance), which is strictly **above $10.0\text{ cm}$**.

Congratulations on wrapping up the first major pillar of the course! You've officially conquered Motion Processing. Now, it's time to transition into our second core pillar: **Color Processing**.

Because color is an incredibly massive topic, we aren't going to jump straight into the full rainbow. Instead, we are going to strip things down to the basics and start with "black and white" vision—which includes all the varying shades of gray. And just like we did with motion, we will begin our journey with **psychophysics**.

## Lecture 5: Achromatic Vision & Brightness Perception

### Simultaneous Contrast

Let's kick things off with a classic visual puzzle. Look at the two inner gray squares below:
![[Pasted image 20260621123007.png]]
Even though the two inner squares have the exact same physical pixel intensity (they reflect the same amount of light), they look completely different to your eyes.

- The gray square on the **dark background** looks significantly lighter.
- The gray square on the **light background** looks significantly darker.

> 🔬 **Interactive Check:** If your brain is trying to tell you this is a trick, go prove it to yourself using the [Simultaneous Contrast Simulation](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment4.html).

This phenomenon is formally known as the **Simultaneous Contrast Effect**. It tells us something fundamental about computational vision: **Human brightness perception is not pixel-perfect.** Your brain doesn't act like a light meter measuring absolute luminance; instead, it computes brightness _relatively_, evaluating an object based on its surrounding context.

### Biological Explanation: Lateral Inhibition

To explain why this happens, scientists looked at the hardware of the early visual system. They discovered a neural mechanism called **Lateral Inhibition**.

Cells in the retina and early visual cortex have a specific "center-surround" architecture. A neuron's firing rate is determined by a tug-of-war: light hitting the exact center of its receptive field _excites_ the cell, while light hitting the surrounding area _inhibits_ (suppresses) it.

This hardware setup perfectly explains the simultaneous contrast effect:
- The inner square on the white background receives a massive amount of surrounding light, causing heavy neural inhibition. The brain gets a suppressed signal, making the square look **darker**.
- The inner square on the black background receives almost no surrounding light, causing minimal inhibition. The neuron fires freely, making the square look **lighter**.

This low-level wiring also explains classic illusions like the **Hermann Grid** (where illusory gray dots appear at the white intersections of a grid) and various color-bleeding phenomena.

![[Pasted image 20260621123626.png]] _Diagrams demonstrating the Hermann Grid_

### The Boundary of Biology: The Gestalt Critique

If Lateral Inhibition were the final, absolute truth of brightness perception, it should predict everything perfectly. But consider this famous counter-experiment involving a uniform gray ring placed over a split black-and-white background:

 ![[Pasted image 20260621124004.png]]

According to lateral inhibition, the left half of the ring (on the white background) should look dark, and the right half (on the black background) should look light. But it doesn't! We perceive the entire ring as a single, uniform shade of gray. The brain overrides the local center-surround signals.

This is exactly where **Gestalt Psychology** comes into play.

_Gestalt_ is a German word that translates roughly to "form" or "unified whole." In vision science, Gestalt theory refers to the brain's automatic, top-down tendency to organize raw visual inputs into distinct objects, surfaces, and scenes rather than disconnected pixels.

Because your brain instantly groups the ring into a single, continuous object, it enforces a rule of global consistency. It concludes, _"This is one solid gray ring sitting on a split background,"_ completely overriding the local lateral inhibition signals. When you split the ring down the middle with a line, breaking that object unity, the simultaneous contrast illusion instantly snaps back into place!



## Summary

- **Contextual Brightness:** Our perception of brightness is highly relative and depends heavily on the surrounding background context.
- **Low-Level Hardware:** Early biological explanations relied on **lateral inhibition** (an implementation-level mechanism), which works well for simple layouts but fails for complex shapes.
- **High-Level Grouping:** **Gestalt theory** explains that our brain organizes visual scenes into unified surfaces and objects (an algorithmic-level approach), overriding local pixel-level discrepancies.


## Lecture 5 (Continued): The Computational View of Brightness

### The Physics of Light: Lambert's Law

To find the true computational explanation (Marr's first level) for why our brain processes brightness the way it does, we must look at the physics governing how light interacts with objects in a scene.

What actually determines the intensity of light that hits our retina?

![[Pasted image 20260621125212.png]]

When light interacts with a matte, non-shiny (Lambertian) surface, the intensity of light reaching our eye ($I$) is defined by three physical variables:

- $L$: The ambient illumination (the amount of light reaching the surface from a light source).
- $\rho$ (Albedo): The reflectance factor of the surface material (the intrinsic fraction of light it reflects, ranging from $0$ to $1$).
- $\theta$: The angle between the surface normal vector and the incoming light source.

Mathematically, this relationship is expressed as:

$$I = \rho L \cos\theta$$

### Why the Problem is Mathematically Ill-Posed

If our goal is to perceive the true physical property of an object—whether it is naturally white or naturally black—what we actually need to extract is the **albedo** ($\rho$).

- An albedo of $\rho \approx 0.1$ reflects only 10% of light, which we perceive as a dark surface like asphalt.
- An albedo of $\rho \approx 0.9$ reflects 90% of light, which we perceive as a bright surface like fresh snow.

**Here is why the problem is incredibly difficult:** Our eyes only have access to the final product, $I$ (the pixel intensity). We are given a single number ($I$) which is the result of multiplying three unknown numbers ($\rho$, $L$, and $\cos\theta$). Mathematically, trying to solve for three unknowns with just one equation is an **ill-posed problem** with an infinite number of solutions.

Yet, the human visual system effortlessly separates these layers, allowing us to instantly tell whether an object looks bright because it has a high albedo ($\rho$), or simply because a strong light ($L$) is shining directly on it.

### A Practical Example: Adelson's Checker Shadow Illusion

To see this computational decomposition in action, consider the famous checkerboard shadow illusion:
![[Pasted image 20260621130808.png]]

In this illusion, Square A sits out in the open, while Square B sits inside a shadow cast by a cylinder. Astonishingly, both squares output the _exact same_ light intensity to your eye ($I_A = I_B$). Your brain, however, automatically factors out the illumination context using a brilliant bit of unconscious algebra:

1. The visual system recognizes that Square B is sitting in a shadow, meaning its incoming illumination is lower than Square A's ($L_A > L_B$).
2. We know from physics that:$$I_A = \rho_A L_A \quad \text{and} \quad I_B = \rho_B L_B$$
3. Since our eyes measure $I_A = I_B$, substituting the equations yields:$$\rho_A L_A = \rho_B L_B$$
4. Because the brain already established that $L_A > L_B$, the only mathematically consistent reality is that **$\rho_A < \rho_B$**.

By solving this internal equation, your brain successfully estimates that Square B must have a significantly higher intrinsic reflectance ($\rho$) than Square A. It overrides the raw pixel values to present you with the true physical layout of the board: Square B is a light check, and Square A is a dark check!

## Summary

- **Contextual Brightness:** Our perception of brightness is not a direct readout of pixel intensity; it depends heavily on spatial context.
- **Lambert’s Law:** The light energy reaching our eye follows the physical equation $I = \rho L \cos\theta$.
- **The Computational Goal:** The core mission of the visual system is to **estimate the intrinsic reflectance ($\rho$) from the compounded input ($I$)** by making smart assumptions about illumination ($L$) and scene geometry ($\cos\theta$).

## Quiz

### Question 1: Receptive Fields vs. Gestalt

![[Pasted image 20260621131251.png]]

 **Question:** (T/F) According to the lateral inhibition explanation, A should be perceived as brighter than B.

#### Answer: TRUE

- **Explanation:** Lateral inhibition is a strictly localized, low-level mechanism.
    
    - The outer edge of Region A is bordered by a very dark background, meaning its surrounding receptive fields receive very little light and generate minimal neural inhibition. This causes the neurons tracking A to fire intensely.
    - Region B is bordered by a highly reflective, light background, causing massive surrounding inhibition that suppresses the neural signal.
    - Therefore, purely based on the hardware mechanics of lateral inhibition, A must be perceived as brighter than B (even though the Gestalt object unity of the ring tries to override this globally).

### Question 2: Surface Angles and Reflected Light

 **Question:** (T/F) Suppose the sun is directly above us. A piece of paper lying flat on the ground will reflect more light than a piece of paper that is at 45 degrees to the ground.

#### Answer: TRUE

- **Explanation:** This is a direct application of Lambert’s Law ($I = \rho L \cos\theta$).
    
    - When the sun is directly overhead, the incoming light rays are parallel to the surface normal of a flat piece of paper. The angle $\theta$ between the light source and the normal is $0^\circ$, and since $\cos(0^\circ) = 1$, the reflected light intensity is maximized ($I = \rho L$).
    - For the paper tilted at $45^\circ$, the angle becomes $\theta = 45^\circ$. Since $\cos(45^\circ) \approx 0.707$, the tilted paper physically receives and reflects roughly 30% less light than the flat paper.

## Lecture 6: The Retinex Algorithm & Intrinsic Images

### Intrinsic Images

To make the ill-posed problem of brightness perception mathematically trackable, we can group our physical terms into a slightly simplified framework called **Intrinsic Images**. Instead of dealing with three separate terms ($I = \rho L \cos\theta$), we consolidate scene geometry ($\cos\theta$) and light source strength ($L$) into a single spatial illumination field, $L(x,y)$.

This allows us to break down the image hitting our eyes, $I(x,y)$, into the product of just two core components:

$$I(x,y) = R(x,y) \cdot L(x,y)$$

Where:

- **$R(x,y)$ — The Reflectance Image:** The intrinsic material properties of the surfaces (playing the exact same role as Albedo).
- **$L(x,y)$ — The Illumination Image:** The amount of light hitting those surfaces, which captures shadows, highlights, and shading gradients.
 ![[Pasted image 20260621162601.png]]

By reducing the equation to two components, our life gets significantly easier: if we can accurately approximate just _one_ of these layers, we automatically get the other layer for free via simple division. However, without adding extra ecological constraints, it is still mathematically impossible to solve.

To bridge this gap, Edwin Land's **Retinex Algorithm** introduces two fundamental assumptions about how edges behave in the real world:

1. Every spatial gradient (change in pixel intensity) in an image is strictly caused by _either_ a change in illumination or a change in material reflectance.
2. **Sharp, abrupt jumps in intensity** are assumed to be caused by changes in **reflectance** (material boundaries). **Smooth, gradual changes in intensity** are assumed to be caused by variations in **illumination** (shadows or curved lighting).

#### Why Do These Assumptions Make Sense?

In the natural world, physical objects tend to be made of uniform materials with consistent pigments. When you transition from one object to another (e.g., from a green shirt to a leather jacket), the material properties change instantaneously at the border, creating a sharp, high-frequency jump in intensity.

On the flip side, light sources are usually distant or diffuse. As light spreads across a surface or a shadow softens along its edges, the change in ambient illumination happens gradually over space, creating smooth, low-frequency gradients.

While these assumptions can be violated in edge-case scenarios (like a crisp, hard-edged shadow from a laser pointer), they hold true in enough real-world scenes to make Retinex an incredibly powerful and helpful algorithm.

### The Simplified 1D Retinex Algorithm

To execute this separation cleanly, Retinex shifts the multiplicative relationship into an additive one by operating in log-space. Let $i = \log(I)$, $l = \log(L)$, and $r = \log(R)$:

$$i(x,y) = l(x,y) + r(x,y)$$

Next, we take the spatial gradient of our log-image, $\nabla i$:

$$\nabla i = \nabla l + \nabla r$$

We then pass the gradient magnitudes through a thresholding filter $T$:

- **If $|\nabla i| < T$:** The change is small and smooth. We attribute it entirely to illumination:$$\nabla l = \nabla i \quad \text{and} \quad \nabla r = 0$$
- **If $|\nabla i| \ge T$:** The change is a sharp jump. We attribute it entirely to reflectance:$$\nabla r = \nabla i \quad \text{and} \quad \nabla l = 0$$
Finally, we integrate the isolated gradient fields ($\nabla r$ and $\nabla l$) to reconstruct the log-maps $r$ and $l$, and then exponentiate them ($R = e^r, L = e^l$) to recover our final intrinsic images. Note that the threshold $T$ is a highly influential hyperparameter that dictates how aggressively edges are classified.

### Expanding to 2D: The Laplacian Integration

In a simple 1D image (a single row of pixels), integrating a gradient is straightforward. However, when we move to 2D image grids, a filtered gradient field ($\nabla r$) is almost never a conservative vector field, meaning standard line integration will fail and depend on the path you take.

To resolve this, we take the derivative of our gradient fields by applying the **Laplacian operator** ($\Delta$ or $\nabla^2$), which calculates the sum of second-order partial derivatives:

$$\Delta i = \frac{\partial^2 i}{\partial x^2} + \frac{\partial^2 i}{\partial y^2}$$

By applying the Laplacian to our thresholded reflectance gradients, we set up a classic **Poisson Equation**:

$$\Delta r = \text{div}(\nabla r_{\text{thresholded}})$$

To reconstruct the final 2D reflectance image, computers solve this partial differential equation using numerical methods (such as the Discrete Fourier Transform or iterative relaxation techniques like the Gauss-Seidel method). This ensures a smooth, mathematically consistent global surface reconstruction across the entire 2D image grid.

### Does Human Vision Use Retinex?

To determine if human vision utilizes a Retinex-like strategy at Marr's _Computational Level_, we have to verify whether our brains rely on the same fundamental assumption—specifically, that slow, gradual gradients represent illumination changes, not material changes.

Fascinating evidence from art history suggests we do. Masters of the High Renaissance, including Leonardo da Vinci, discovered that to paint a convincing shadow, they could not simply draw a sharp black line. They formalized that a real shadow consists of two regions:

1. **The Umbra:** The central, deeply dark core where the light source is completely blocked.
2. **The Penumbra:** The fuzzy, outer fuzzy fringe where the light source is only partially obscured, creating a smooth blur gradient from light to dark.![[Pasted image 20260621165209.png]]
 _Historical sketch by Leonardo da Vinci demonstrating the geometry of light casting an umbra and a penumbra._

If you paint a shadow _without_ a penumbra, the human brain gets confused and often interprets the sharp transition as a dark paint mark on the object (a change in reflectance) rather than a shadow (a change in illumination). The fact that our brains rely so heavily on the smooth gradient of a penumbra to recognize a shadow confirms that human vision uses the exact same computational assumptions as Retinex!

## Summary

- **The Retinex core assumption:** Sharp changes in image intensity are caused by material reflectance, while smooth gradients are caused by illumination.
- **Input/Output behavior:** The algorithm takes a single raw image as input ($I$) and produces two processed intrinsic images as output ($R$ and $L$).
- **The Penumbra Connection:** The absolute requirement of a penumbra for humans to perceive realistic shadows strongly suggests that our visual system employs these identical edge-classification strategies.

## Quiz
### Question 1: The Truck Shadow
![[Pasted image 20260621170121.png]]
**Question:** (T/F) The point marked "A" (Image 1) is in the penumbra.

##### Answer: FALSE

- **Explanation:** While the shadow cast onto the road definitely features a blurry penumbra along its outer perimeter, the arrow for "A" points directly into the deep, pitch-black central core of the shadow under the truck. Because this core region is completely shielded from the sun, it is the **umbra**.

### Question 2: Cindy Crawford's Cheek Contour

**Question:** (T/F) The gradient at the location marked "A" (Image 2) would be labeled as a reflectance change by the Retinex algorithm.

#### Answer: FALSE

- **Explanation:** Point "A" points to the smooth, gradual shading gradient along the curve of the cheekbone. Because the cheek curves smoothly away from the light source, it creates a very low-frequency change in pixel intensity. Since the gradient magnitude is small ($|\nabla i| < T$), the Retinex algorithm will classify it as an **illumination/shading change**, not a change in material reflectance

## Lecture 7: White Balance & Color Constancy

### The White Balance Problem

In the last lecture, we learned how the Retinex algorithm translates an image $I(x,y)$ into its intrinsic reflectance $R(x,y)$ and illumination $L(x,y)$. However, because the final step of Retinex requires taking an integration over gradient fields, **the resulting estimates are only true up to a scaling constant.** This ambiguity sets the stage for our next major computational hurdle: the **White Balance Problem**.

To understand this problem with a concrete numerical example, imagine a simple image $I$ consisting of just four pixels:

$$I = (100, 125, 125, 150)$$

Because of the scaling ambiguity, this single physical image can be decomposed and interpreted by a computer in multiple ways depending on what we assume the maximum ambient light level is:

- **Interpretation A (Assuming $L = 255$):**$$I = 255 \times \left(\frac{100}{255}, \frac{125}{255}, \frac{125}{255}, \frac{150}{255}\right)$$
- **Interpretation B (Assuming $L = 150$):**$$I = 150 \times \left(\frac{100}{150}, \frac{125}{150}, \frac{125}{150}, \frac{150}{150}\right)$$

Look at what happens to the middle pixels ($125$) under these different interpretations. In Interpretation A, $\frac{125}{255} \approx 0.49$, meaning the brain perceives those pixels as a neutral **gray**. In Interpretation B, $\frac{125}{150} \approx 0.83$, meaning the exact same pixels are perceived as a bright **white**.

Mathematically, our goal is to find an algorithm that takes a raw image $I(x,y)$ and correctly extracts the true material reflectance $\rho(x,y)$ and the global illumination constant $L$. For an image with $N$ pixels, we have $N$ inputs, but we are trying to solve for $N + 1$ unknowns ($N$ reflectance values plus $1$ global illumination value). Because this is mathematically underdetermined, any algorithm we propose will be an **approximation heuristic**, not a definitive truth.

### Heuristic 1: The Gray World Algorithm

The **Gray World Algorithm** operates on a core statistical assumption about the world: if you average all the colors and material reflectances in a typical natural scene, they will average out to a neutral, middle gray ($\rho_{\text{avg}} = 0.5$).

Using this assumption, we can state that the average pixel intensity of our image must equal half of the total illumination:

$$\frac{1}{N} \sum_{x,y} I(x,y) = 0.5 \cdot L$$

From here, we can easily isolate and solve for the unknown illumination constant $L$:

$$L = \frac{2}{N} \sum_{x,y} I(x,y)$$

Once we have approximated $L$, recovering the true material reflectance for every individual pixel is simple:

$$\rho(x,y) = \frac{I(x,y)}{L}$$

### Heuristic 2: The Brightest = White Algorithm

Alternatively, we can use a completely different heuristic: the **Brightest = White Algorithm**. This strategy assumes that the highest intensity pixel in any given image represents a perfectly reflective, pure white surface ($\rho_{\text{max}} = 1.0$).

Using this rule, the maximum intensity pixel directly reveals our illumination boundary:

$$\max_{x,y} I(x,y) = 1.0 \cdot L$$

Therefore, the global illumination is simply $L = \max_{x,y} I(x,y)$, and the reflectance image is calculated by dividing everything by that peak value:

$$\rho(x,y) = \frac{I(x,y)}{\max_{x,y} I(x,y)}$$

> 🧠 **Why "Darkest = Black" Fails:** You might wonder why we don't build an inverse algorithm assuming the darkest pixel is pure black ($\rho = 0$). Mathematically, this fails completely because it would mean setting $0 \cdot L = \min(I)$. Multiplying $L$ by zero completely destroys our ability to isolate the illumination constant. This makes perfect ecological sense: a true black surface absorbs all incoming light, meaning it reflects absolutely no color data back to your eye regardless of how bright the ambient light source is.

### What Algorithm Do Humans Actually Use?

To figure out which strategy our biological vision aligns with, we can look at a case where the two algorithms fundamentally disagree. Consider a completely uniform, flat image where every single pixel has the exact same intensity value of 100:

$$I = (100, 100, 100, 100)$$

- **The Gray World Algorithm** looks at this, computes the average as 100, and concludes that the scene is a **middle gray** ($\rho = 0.5$) under an illumination of $L = 200$.
- **The Brightest = White Algorithm** looks at this, finds the maximum value is 100, and concludes that the scene is a highly reflective **pure white** surface ($\rho = 1.0$) under an illumination of $L = 100$.

When human subjects are placed in a completely uniform testing environment with no external context, **they overwhelmingly perceive the surface as white.** Psychophysical experiments like this confirm that the human visual system relies on a strategy much closer to the **Brightest = White** algorithm to lock in its color constancy and white balance.


### The Simplified RGB Color Model

Up until this point, we have restricted our discussion strictly to grayscale intensities. Now, it's time to add color to our universe.

To keep the math and concepts manageable, we are going to start with a simplified model: the **RGB Color Space**. While this three-channel approach mimics how digital cameras and computers process color rather than reflecting full, continuous physical light spectra, it serves as an excellent foundational approximation.

In this simplified color world, the physical rules of Lambert's Law remain exactly the same, but instead of our eyes capturing a single broad intensity value, we capture three distinct, parallel projections of light at every pixel location:

$$\begin{aligned} I_r(x,y) &= \rho_r L_r \cos\theta \\ I_g(x,y) &= \rho_g L_g \cos\theta \\ I_b(x,y) &= \rho_b L_b \cos\theta \end{aligned}$$

Where:

- $I_r, I_g, I_b$ represent the intensity values captured in the Red, Green, and Blue channels.
- $\rho_r, \rho_g, \rho_b$ are the material's intrinsic reflectance properties (albedos) for each respective color.
- $L_r, L_g, L_b$ represent the color components of the ambient illumination source.

By mixing these three channels, we construct our color perceptions. For example:

- $(0.8, 0.8, 0.8)$ results in a neutral, bright **white/gray**.
- $(0.8, 0.2, 0.2)$ results in a dominant **red**.
- $(0.8, 0.8, 0.2)$ combines red and green light to create **yellow**.

### The Color Constancy Problem

With three channels, our computational problem becomes significantly more complex. We are no longer trying to solve for a single global illumination constant; we now have to figure out the color of the ambient light source itself (e.g., is the scene lit by blue skylight, yellow candlelight, or white fluorescent bulbs?).

For an image containing $N$ pixels, we receive $3N$ inputs (an R, G, and B value for every pixel). However, we are forced to solve for $3N + 3$ unknowns ($3N$ separate material reflectances plus $3$ independent channel illumination constants).

This underdetermined mathematical puzzle is called the **Color Constancy Problem**.

To solve this in computer vision, a simple baseline approach is to treat each color channel as its own independent grayscale image, executing our previous **Gray-World** or **Brightest=White** algorithms separately across the R, G, and B planes.

While these independent channel approximations frequently break down in complex environments, human vision is prone to the exact same computational failures.

![[Pasted image 20260621195736.png]]
_An image demonstrating how a strong colored ambient light source can completely distort our ability to perceive the true underlying material colors._

If you wash a scene in a strong, monochromatic yellow light, your brain struggle to subtract the illumination accurately, distorting your perception of the true underlying material pigments.

> 🔬 **Interactive Check:** To test the limits of your own biological color constancy under varying illumination wavelengths, try out the [Color Constancy Interactive Experiment](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment5.html).

## Summary

- **White Balance:** Given the scalar equation $I(x,y) = \rho(x,y)L$, the goal is to isolate and estimate the material reflectance $\rho(x,y)$ and the illumination constant $L$.
- **Color Constancy:** This represents the exact same computational challenge, expanded across three independent color channels (Red, Green, and Blue).
- **Algorithmic Approximations:** Both the Gray-World and "Brightest=White" heuristics can be generalized and applied channel-by-channel to address both white balance and color constancy problems.
- **Human Performance:** While human visual processing is vastly more sophisticated than these simple computer vision shortcuts, our biological systems rely on variants of the "Brightest=White" constraint to anchor our perceptions.

## Quiz


### Question 1: Mathematical "Brightest=White" Application

**Question:** (T/F) Assume an image vector $I = (200, 300, 300, 300, 400)$. Under the "Brightest=White" algorithm, the reflectance of the first pixel is $0.5$ and it would be perceived as "gray".

#### Answer: TRUE

- **Step-by-Step Calculation:**
    1. The "Brightest=White" algorithm defines the global illumination constant $L$ as the maximum pixel intensity in the array:$$L = \max(I) = 400$$
    2. To compute the reflectance ($\rho$) of the first pixel, we divide its raw intensity value by our newly calculated $L$:$$\rho_1 = \frac{I_1}{L} = \frac{200}{400} = 0.5$$
    3. Because an intrinsic reflectance of $0.5$ lands perfectly halfway between black ($0.0$) and white ($1.0$), the brain interprets this material surface as a neutral **gray**.

### Question 2: RGB Color Mixing Theory

**Question:** (T/F) A shirt whose albedo is $\rho_R = 0.2, \rho_g = 0.8, \rho_b = 0.8$ would be called "yellow".

#### Answer: FALSE

- **Explanation:** In the RGB additive color mixing model:
    
    - **Yellow** is created by mixing high amounts of Red and Green light while suppressing Blue ($\rho_R = \text{high}, \rho_g = \text{high}, \rho_b = \text{low}$).
    - In this specific question, the shirt absorbs red light ($\rho_R = 0.2$) and reflects high amounts of Green and Blue light ($\rho_g = 0.8, \rho_b = 0.8$). Combining green and blue light produces the color **Cyan** (or a bright teal/aqua), not yellow!

## Lecture 8: Color Physics & Human Color Perception

### The Infinite-Dimensional Reality of Light

In our simplified RGB world, we treated color as a neat combination of three discrete channels. However, real physics does not abide by this simplicity. As Sir Isaac Newton famously demonstrated by splitting white light with a prism, sunlight contains a continuous, infinite spectrum of wavelengths.

![[Pasted image 20260621203333.png]]

In the real world, a light source cannot be captured by just three numbers. Instead, it is described by a continuous function, $I(\lambda)$, which represents the relative physical energy emitted at each individual wavelength ($\lambda$). While the electromagnetic spectrum is vast, the human visual system is biologically limited to a narrow window of **visible light** spanning from roughly **400 to 700 nanometers**.

Because of this, the physical law we used earlier (Lambert's Law) must be applied to every single wavelength independently:

![[Pasted image 20260621203808.png]]

$$I(\lambda) = \rho(\lambda) L(\lambda) \cos\theta$$

This physical reality means that color, at its input level, is **infinite-dimensional**.

### The Mystery of Metamers

If physical light is infinite-dimensional, how does the human brain compress this infinite stream of data into a manageable perception? To understand this abstraction, we look at a core phenomenon called **metamerism**.

> 💡 **Definition:** Two light sources with completely different physical energy spectra, $I_1(\lambda)$ and $I_2(\lambda)$, are called **metamers** (denoted $I_1 \equiv I_2$) if they are perceived as completely identical by a human observer.

To map out this phenomenon, scientists design **color-matching experiments**. An observer looks at a split screen: one side displays a target test light, and the other side contains a mixture of primary lights. The observer adjusts the knobs of the primary lights until the two sides match perfectly.

> 🔬 **Interactive Check:** To experience color-matching yourself and see how primary channels blend to mimic monochromatic lights, try the [Color Matching Interactive Experiment](https://alikashoa-cse.github.io/Human-vision-a-computational-approach-76921-Course-summary/Experiments/Experiment6.html).

### Grassmann’s Laws of Linear Color Space

Through extensive color-matching trials, Hermann Grassmann formalized the empirical rules governing how humans match colors. For any light spectra, the following algebraic properties consistently hold true:

- **Symmetry:** $V \equiv U \iff U \equiv V$
- **Transitivity:** $U \equiv V \quad \text{and} \quad V \equiv W \implies U \equiv W$
- **Proportionality (Scaling):** $U \equiv V \iff tU \equiv tV$
- **Additivity:** $U \equiv V \quad \text{and} \quad X \equiv Y \implies U + X \equiv V + Y$

Because these biological observations perfectly mirror the axioms of linear vector spaces, they provide explicit permission to model human color perception using **linear algebra**.

### Color Perception as a Vector-Matrix Product

We can mathematically formalize the compression of color in our brain as a linear dimensionality reduction from an infinite-dimensional space (approximated computationally as a highly sampled vector, e.g., $\mathbb{R}^{400}$) down to a low-dimensional internal vector space $\mathbb{R}^K$:

$$y = S I$$

Where:

- $I \in \mathbb{R}^{400}$ is the physical energy vector of the light spectrum sampled across wavelengths.
- $y \in \mathbb{R}^K$ is the compressed, low-dimensional color vector represented in our brain.
- $S \in \mathbb{R}^{K \times 400}$ is the linear sensor mapping matrix of our visual system.

Because the number of internal tracking dimensions is far smaller than the physical inputs ($K \ll 400$), the matrix $S$ possesses a massive null space. This mathematical reality guarantees that infinitely many different light vectors $I$ will map to the exact same internal coordinate $y$—proving why metamers exist.

### Deriving the Biological Parameters ($K$ and $S$)

Can we discover the exact value of $K$ (the number of dimensions) and the shape of the sensor matrix $S$ purely from behavioral color-matching experiments? **Yes, we can.**

#### 1. Finding the Dimensionality ($K$)

During human color-matching experiments, researchers discovered that if they provided observers with **exactly three independent primary lights**, the observers could successfully match any target light spectrum. If they were given only two, many colors were impossible to replicate. This empirical behavioral breakthrough proved that **$K = 3$** for human vision.

#### 2. The Color-Matching Matrix ($C$)

Let $P \in \mathbb{R}^{400 \times 3}$ be a matrix whose three columns represent the spectral footprints of our chosen primary lights. The combination weights selected by the user can be written as a vector $\alpha = (\alpha_1, \alpha_2, \alpha_3)^T$. Since the final mixture light $P\alpha$ is a metamer to the target light $I$, their internal brain representations must match:

$$S(P\alpha) = SI \implies (SP)\alpha = SI$$

Assuming the chosen primary lights are independent, the $3 \times 3$ matrix $(SP)$ is invertible, allowing us to isolate the knob adjustments ($\alpha$):

$$\alpha = (SP)^{-1}SI$$

We can encapsulate this entire empirical operation into a single, observable **Color-Matching Matrix ($C$)**:

$$\alpha = CI, \quad \text{where } C = (SP)^{-1}S$$

We can reconstruct $C$ column-by-column in a lab simply by testing standard monochromatic impulse lights ($e_i$).

#### 3. Recovering the Brain's Sensor Matrix ($S$)

Notice the elegant relationship discovered above: $C = (SP)^{-1}S$. Because $(SP)^{-1}$ is merely a square $3 \times 3$ blending matrix, this proves that **the empirical color-matching matrix $C$ is mathematically identical to the brain's internal sensor sensitivity matrix $S$, up to an arbitrary $3 \times 3$ linear transformation.**

### Grounding the Math in Biological Reality

When biology catches up to the computational theory, the alignment is flawless. Looking at the hardware level (the retina), humans possess two categories of photoreceptors: **rods** (highly sensitive to light levels, used for night vision) and **cones** (used for color vision).

![[Pasted image 20260621211525.png]]

The cones are the color-sensitive receptors. When biologists physically measured the light absorption spectra of human cones, they found exactly **three distinct types of cones**, categorized by their wavelength tuning:

- **S Cones:** Tuned to short wavelengths (Blue)
- **M Cones:** Tuned to medium wavelengths (Green)
- **L Cones:** Tuned to long wavelengths (Red)

The spectral sensitivity curves of these three physical cone types map perfectly to the linear rows of the sensor matrix $S$ we predicted using behavioral algebra! Furthermore, conditions like colorblindness occur when an individual is genetically missing one of these three cone channels, compressing their matrix mapping from $3 \times 3$ down to a rank-2 system.

## Summary

- **Dimensional Disconnect:** Physical color is continuous and infinite-dimensional, but the widespread existence of metamers proves our internal perceptual color space is highly compressed.
- **Linear Systems:** Grassmann's Laws reveal that human color matching satisfies the mathematical conditions of linearity, allowing us to model vision as a matrix-vector dimensionality reduction: $y = SI$.
- **System Recovery:** By running behavioral color-matching experiments, we can completely reconstruct the human color dimensionality ($K=3$) and isolate the sensor matrix $S$ up to an invertible $3 \times 3$ transformation matrix.

## Quiz

### Question 1: Metamers and Scaling

**Question:** (T/F) There exist two lights $I_1, I_2$ such that $I_1$ and $I_2$ are metamers, but $2I_1$ and $2I_2$ are not metamers.

#### Answer: FALSE

- **Explanation:** This statement directly violates **Grassmann’s Law of Proportionality**. By definition, if two lights are perceived as identical ($I_1 \equiv I_2$), their internal brain coordinates are equal ($S I_1 = S I_2$). Due to the fundamental linearity of matrix-vector multiplication, scaling both inputs by any scalar constant $t$ yields:
    
    $$S(2I_1) = 2(SI_1) = 2(SI_2) = S(2I_2)$$
    
    Because their transformed brain coordinates remain perfectly equal, $2I_1$ and $2I_2$ are guaranteed to remain metamers.
    

### Question 2: Canine Color Dimensionality

**Question:** (T/F) For dogs, it is possible to find a match to any light source using only two primary lights.

#### Answer: TRUE

- **Explanation:** Unlike trichromatic humans ($K=3$), dogs are biologically **dichromatic**. Their retinas contain only two distinct types of color cones (equivalent to our S and L cones). Because a dog's internal color space is reduced to $K=2$, their sensor matrix $S_{\text{dog}}$ maps to a 2D coordinate space. Therefore, a dog only requires a system of two independent primary lights ($2 \times 2$ invertible matrix) to behavioral-match any incoming spectral light configuration perfectly.


## Lecture 9: Structure from Motion (SFM)

### Transitioning to "Form" and 3D Reconstruction

Congratulations on hitting the next major milestone in the course! Two out of our three core pillars—Motion and Color—are now completely behind us. We are officially entering our final module: **Shape, Stereo, and Form Processing**.

The primary question we will be asking ourselves throughout this module is: _How do we recover 3D structure from flat, 2D visual data?_

Formally, we want to take a set of 2D coordinates tracked on a screen:

$$\begin{pmatrix} x_i \\ y_i \end{pmatrix}$$

And reconstruct their true, original 3D positions in the world:

$$\begin{pmatrix} X_i \\ Y_i \\ Z_i \end{pmatrix}$$

Mathematically, dropping a dimension throws away critical information. Trying to map a 2D point back to a 3D coordinate opens up an infinite line of possibilities, making the inverse mapping completely ill-posed.

To tackle this challenge, let's look at the forward problem first: how 3D world points project onto a 2D camera sensor. For now, we will work under the framework of an **Orthographic Projection**.

![[Pasted image 20260621214713.png]]

An orthographic projection simply drops the depth coordinate ($Z$) entirely:

$$\text{proj}\begin{pmatrix} X \\ Y \\ Z \end{pmatrix} = \begin{pmatrix} X \\ Y \end{pmatrix}$$

Using basic geometry, it is easy to see that **parallel lines remain perfectly parallel** under an orthographic projection. While real-world physics behaves like a _perspective projection_ (where objects shrink as they move further away along the $Z$-axis, as shown in the slide diagram), starting with an orthographic model simplifies the equations so we can build a working foundation.

### Structure from Motion: Factoring in Time

When an object moves through space over time, its coordinates are no longer static. Our input data becomes a series of tracked 2D feature coordinates across multiple video frames, $x_i(t), y_i(t)$, and our goal is to track down their changing 3D paths:

$$\begin{pmatrix} x_i(t) \\ y_i(t) \end{pmatrix} \longrightarrow \begin{pmatrix} X_i(t) \\ Y_i(t) \\ Z_i(t) \end{pmatrix}$$

Just like the static case, there are infinitely many 3D trajectories that could explain any given 2D video. To narrow down the possibilities to a single correct answer, we apply a massive ecological constraint: the **Rigid Body Assumption**.

We assume that the moving points belong to a solid, non-deformable object. This means the 3D coordinates at any time $t$ are simply a rigid rotation of their initial coordinates at time $0$, driven by a time-varying $3 \times 3$ rotation matrix $M(t)$:

$$\begin{pmatrix} X_i(t) \\ Y_i(t) \\ Z_i(t) \end{pmatrix} = M(t) \begin{pmatrix} X_i(0) \\ Y_i(0) \\ Z_i(0) \end{pmatrix}$$

If we track $P$ individual feature points over $T$ distinct video frames, we have $4T + 3P$ unknown variables (accounting for the changing camera rotations over time and the static 3D coordinates of each point). However, our video gives us $2TP$ independent coordinate equations. As long as we track a large number of points over a long enough video sequence, $2TP \ge 4T + 3P$, and the system becomes completely solvable!

### The Tomasi-Kanade Factorization Algorithm

Carlo Tomasi and Takeo Kanade (1992) developed an elegant linear algebra algorithm to solve this problem by organizing our observations into three distinct matrices:

- **$W_{2T \times P}$ (The Measurement Matrix):** This matrix holds our raw 2D data across all frames. Every column represents a tracked feature point, and every pair of rows represents a video frame. The odd rows contain the $x$-coordinates of all points in frame $t$, and the even rows contain the $y$-coordinates.
- **$M_{2T \times 3}$ (The Motion Matrix):** This matrix holds the camera's rotational state. The odd rows contain the first row of the rotation matrix $M(t)$, and the even rows contain the second row of $M(t)$.
- **$S_{3 \times P}$ (The Structure Matrix):** This matrix holds the permanent 3D shape of the object. Each column $i$ contains the static 3D coordinate $(X_i, Y_i, Z_i)^T$ of point $i$ at time zero.
    

By combining our rigid body and orthographic equations, the entire video sequence collapses into a simple matrix multiplication:

$$W = M \cdot S$$

Because the matrix $W$ is the product of a matrix with $3$ columns ($M$) and a matrix with $3$ rows ($S$), **the measurement matrix $W$ can have a rank of at most 3.** This rank constraint is highly profound—it means that no matter how many hundreds of frames or thousands of points you track, all that massive 2D data is constrained to sit inside a tight 3D subspace.

#### Solving the System Using Singular Value Decomposition (SVD)

To separate $W$ back out into its independent components ($M$ and $S$), we apply **Singular Value Decomposition (SVD)**. Any rectangular matrix $W$ of rank $K=3$ can be decomposed into:

$$W = U \Sigma V^T$$

Where $U$ is a $2T \times 3$ matrix, $\Sigma$ is a $3 \times 3$ diagonal matrix of singular values, and $V^T$ is a $3 \times P$ matrix. This gives us an immediate initial candidate for our motion and structure matrices:

$$\tilde{M} = U, \quad \tilde{S} = \Sigma V^T$$

However, this initial factorization is not unique. For any invertible $3 \times 3$ linear transformation matrix $A$, we can insert an identity step ($A A^{-1} = I$) into our equation:

$$W = \tilde{M} \tilde{S} = \tilde{M}(A A^{-1})\tilde{S} = (\tilde{M}A)(A^{-1}\tilde{S})$$

Therefore, the true physical motion matrix $M$ and the true structural shape matrix $S$ are related to our SVD candidates up to a $3 \times 3$ affine transformation:

$$M = \tilde{M}A, \quad S = A^{-1}\tilde{S}$$

By enforcing standard geometric properties (such as the constraint that the rows of a true rotation matrix $M$ must be unit vectors and mutually orthogonal), we can solve for the remaining coefficients in $A$. With $A$ in hand, the algorithm successfully reconstructs the full 3D shape matrix $S$ and the 3D path matrix $M$ using nothing but a flat 2D video!

### Do Humans Use This Strategy?

When we look at psychophysical data, evidence confirms that **the human visual system solves this problem using the exact same rigidity assumptions at Marr's Computational Level.** If you show a human a 2D animation of scattered dots moving randomly on a flat screen, their brain suddenly snaps the dots together, perceiving a clear, rigid 3D cylinder or sphere spinning in depth. Our visual system actively optimizes for a rigid 3D interpretation to make sense of 2D retinal motion.

## Summary


- **Orthographic Mapping:** Under an orthographic projection model, 3D world coordinates $(X, Y, Z)$ map directly to 2D screen coordinates $(X, Y)$ by stripping away depth.
- **The Rank-3 Constraint:** Tracking the rotation of a rigid body over time yields a combined measurement matrix $W$ that is mathematically restricted to a maximum rank of 3 under orthography.
- **SVD Reconstruction:** Singular Value Decomposition (SVD) serves as the primary algebraic engine to factor this measurement space, allowing us to successfully extract 3D structure and camera motion from 2D pixel tracks.

## Quiz

### Question 1: SVD Matrix Invariance

**Question:** (T/F) Any rectangular matrix $W_{M \times N}$ can be written as: $W = U \Sigma V$ with $U_{M \times K}, \Sigma_{K \times K}, V_{K \times N}$.

#### Answer: TRUE

- **Explanation:** This is a fundamental theorem of linear algebra. Every real rectangular matrix $W_{M \times N}$ possesses a Singular Value Decomposition. If the matrix has a rank of $K$, it can always be factored into an orthogonal matrix $U_{M \times K}$ containing the left-singular vectors, a diagonal matrix $\Sigma_{K \times K}$ containing $K$ positive singular values, and an orthogonal matrix $V_{K \times N}$ containing the right-singular vectors.

### Question 2: Orthographic Geometry of a Cube

**Question:** (T/F) In an orthographic projection of a cube, all the sides are rectangles.

#### Answer: FALSE

- **Explanation:** Let's look at the geometry of projection lines. If a cube is perfectly aligned parallel to the camera sensor, its faces project as perfect squares/rectangles. However, if the cube rotates in 3D space relative to the viewer, its square faces tilt away from the camera plane.
- Because orthographic projection projects points along parallel lines orthogonal to the image plane, tilting a square face causes its 2D projection to deform into a **parallelogram**, not a rectangle (the internal angles will no longer be $90^\circ$).
